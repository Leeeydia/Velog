<h2 id="배경">배경</h2>
<p>ORDA 등산 앱 백엔드 개발 중 팀 내 API 응답 형식 통일 가이드가 생겼다. 도메인별로 작업하다 보면 응답 형식이 제각각이 될 수 있어서, 모든 API를 <code>ApiResponse&lt;DTO&gt;</code> 구조로 통일하고 지도용 API는 GeoJSON 형식으로 반환하도록 규칙을 정했다.</p>
<p>이번 작업은 기존에 구현한 hiking 도메인(GPS 수집/반환)과 summit 도메인(정상 인증)을 이 가이드에 맞게 리팩토링한 내용이다.</p>
<hr />
<h2 id="기존-문제점">기존 문제점</h2>
<p>기존 Controller는 DTO를 그대로 반환하고 있었다.</p>
<pre><code class="language-java">// ❌ 리팩토링 전
ResponseEntity&lt;HikingStartResponse&gt;
ResponseEntity&lt;HikingEndResponse&gt;
ResponseEntity&lt;List&lt;GpsTrackResponse&gt;&gt;</code></pre>
<p>프론트에서 응답을 받을 때 성공/실패 여부를 HTTP 상태 코드로만 판단해야 했고, 응답 구조가 API마다 달라 유지보수가 어려웠다.</p>
<hr />
<h2 id="해결-방법">해결 방법</h2>
<h3 id="1-모든-api를-apiresponse로-래핑">1. 모든 API를 ApiResponse로 래핑</h3>
<pre><code class="language-java">// ✅ 리팩토링 후
ResponseEntity&lt;ApiResponse&lt;HikingStartResponse&gt;&gt;
ResponseEntity&lt;ApiResponse&lt;HikingEndResponse&gt;&gt;
ResponseEntity&lt;ApiResponse&lt;GeoJsonFeatureCollectionResponse&gt;&gt;</code></pre>
<p><code>ApiResponse</code> 구조는 아래와 같다.</p>
<pre><code class="language-json">{
  &quot;success&quot;: true,
  &quot;message&quot;: &quot;등산 시작 성공&quot;,
  &quot;data&quot;: { ... }
}</code></pre>
<p>Controller에서는 아래처럼 사용한다.</p>
<pre><code class="language-java">@PostMapping(&quot;/start&quot;)
public ResponseEntity&lt;ApiResponse&lt;HikingStartResponse&gt;&gt; startHiking(
        @Valid @RequestBody HikingStartRequest request) {
    HikingStartResponse response = hikingService.startHiking(request);
    return ResponseEntity.status(HttpStatus.CREATED)
            .body(ApiResponse.success(&quot;등산 시작 성공&quot;, response));
}</code></pre>
<h3 id="2-gps-트랙-조회-api를-geojson으로-변환">2. GPS 트랙 조회 API를 GeoJSON으로 변환</h3>
<p>GPS 트랙은 지도에 바로 그려야 하는 데이터라 GeoJSON 형식이 적합하다. 팀에서 이미 <code>common/geojson/</code> 패키지에 공통 GeoJSON DTO를 만들어두어서 그대로 활용했다.</p>
<pre><code class="language-java">public GeoJsonFeatureCollectionResponse getTracks(Long sessionId) {
    List&lt;GpsTrack&gt; tracks = gpsTrackRepository.findBySessionIdOrderBySequenceNum(sessionId);

    List&lt;GeoJsonFeatureResponse&gt; features = tracks.stream()
            .map(track -&gt; {
                GeoJsonGeometryResponse geometry = GeoJsonGeometryResponse.point(
                        track.getGeom().getX(),  // longitude
                        track.getGeom().getY()   // latitude
                );
                Map&lt;String, Object&gt; properties = new HashMap&lt;&gt;();
                properties.put(&quot;trackId&quot;, track.getTrackId());
                properties.put(&quot;sequenceNum&quot;, track.getSequenceNum());
                properties.put(&quot;elevationM&quot;, track.getElevationM());
                properties.put(&quot;accuracyM&quot;, track.getAccuracyM());
                properties.put(&quot;recordedAt&quot;, track.getRecordedAt().toString());

                return GeoJsonFeatureResponse.of(geometry, properties);
            })
            .toList();

    return GeoJsonFeatureCollectionResponse.of(features);
}</code></pre>
<p>응답 결과:</p>
<pre><code class="language-json">{
  &quot;success&quot;: true,
  &quot;message&quot;: &quot;GPS 트랙 조회 성공&quot;,
  &quot;data&quot;: {
    &quot;type&quot;: &quot;FeatureCollection&quot;,
    &quot;features&quot;: [
      {
        &quot;type&quot;: &quot;Feature&quot;,
        &quot;geometry&quot;: {
          &quot;type&quot;: &quot;Point&quot;,
          &quot;coordinates&quot;: [127.301, 36.351]
        },
        &quot;properties&quot;: {
          &quot;trackId&quot;: 1,
          &quot;sequenceNum&quot;: 1,
          &quot;elevationM&quot;: 437.5,
          &quot;accuracyM&quot;: 5.0,
          &quot;recordedAt&quot;: &quot;2026-03-23T11:00:00&quot;
        }
      }
    ]
  }
}</code></pre>
<h3 id="3-좌표-순서-주의">3. 좌표 순서 주의</h3>
<p>GeoJSON 표준에 따라 좌표 순서는 <code>[longitude, latitude]</code> — 경도 먼저, 위도 나중이다.</p>
<p>JTS <code>Point</code>에서 꺼낼 때 <code>getX()</code> = longitude, <code>getY()</code> = latitude 순서로 넣어줬다.</p>
<pre><code class="language-java">GeoJsonGeometryResponse.point(
    track.getGeom().getX(),  // longitude (경도)
    track.getGeom().getY()   // latitude  (위도)
);</code></pre>
<hr />
<h2 id="포인트-정리">포인트 정리</h2>
<h3 id="공통-geojson-dto-활용">공통 GeoJSON DTO 활용</h3>
<p>처음엔 hiking 도메인 안에 <code>GeoJsonFeatureCollectionResponse</code>를 따로 만들었는데, 이미 <code>common/geojson/</code> 패키지에 팀 공통으로 만들어진 파일이 있었다. 중복 파일을 삭제하고 공통 패키지 것을 사용했다.</p>
<pre><code>common/
  geojson/
    GeoJsonFeatureCollectionResponse.java  ← 이걸 사용
    GeoJsonFeatureResponse.java
    GeoJsonGeometryResponse.java</code></pre><h3 id="팀원과의-역할-분리">팀원과의 역할 분리</h3>
<p>GPS 트랙 저장/조회는 내가 담당하고, 고도 데이터 시각화는 다른 팀원이 담당한다. <code>GET /api/hiking/{sessionId}/tracks</code>가 GeoJSON을 반환하도록 만들어두면 팀원이 <code>result.data</code>를 Mapbox 등 지도 라이브러리에 바로 꽂을 수 있어 연동이 편해진다.</p>
<pre><code class="language-javascript">const result = await fetch(`/api/hiking/${sessionId}/tracks`);
const { data } = await result.json();

map.addSource('gps-track', {
  type: 'geojson',
  data: data
});</code></pre>
<hr />
<h2 id="최종-api-응답-구조">최종 API 응답 구조</h2>
<table>
<thead>
<tr>
<th>API</th>
<th>반환 타입</th>
</tr>
</thead>
<tbody><tr>
<td><code>POST /api/hiking/start</code></td>
<td><code>ApiResponse&lt;HikingStartResponse&gt;</code></td>
</tr>
<tr>
<td><code>POST /api/hiking/{id}/end</code></td>
<td><code>ApiResponse&lt;HikingEndResponse&gt;</code></td>
</tr>
<tr>
<td><code>GET /api/hiking/{id}</code></td>
<td><code>ApiResponse&lt;HikingSessionResponse&gt;</code></td>
</tr>
<tr>
<td><code>POST /api/hiking/{id}/tracks</code></td>
<td><code>ApiResponse&lt;Void&gt;</code></td>
</tr>
<tr>
<td><code>GET /api/hiking/{id}/tracks</code></td>
<td><code>ApiResponse&lt;GeoJsonFeatureCollectionResponse&gt;</code></td>
</tr>
<tr>
<td><code>POST /api/summit/verify</code></td>
<td><code>ApiResponse&lt;SummitVerifyResponse&gt;</code></td>
</tr>
</tbody></table>