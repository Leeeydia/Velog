<blockquote>
<p>ORDA(Outdoor Route Detection &amp; Analysis)는 GPS 기반 등산 경로 감지 및 활동 데이터 분석 플랫폼입니다.<br />이번 글에서는 등산 중 수집된 GPS 포인트를 저장하고 조회하는 API 구현 과정을 정리합니다.</p>
</blockquote>
<hr />
<h2 id="📌-구현-목표">📌 구현 목표</h2>
<ul>
<li><code>POST /api/hiking/{sessionId}/tracks</code> — GPS 포인트 저장</li>
<li><code>GET /api/hiking/{sessionId}/tracks</code> — GPS 포인트 조회 (경로 순서대로)</li>
<li>MapLibre GL JS로 실제 지도에 경로 렌더링 테스트</li>
</ul>
<hr />
<h2 id="🗄️-db-스키마">🗄️ DB 스키마</h2>
<pre><code class="language-sql">CREATE TABLE gps_tracks (
    track_id     BIGSERIAL PRIMARY KEY,
    session_id   BIGINT NOT NULL REFERENCES hiking_sessions(session_id),
    sequence_num INTEGER NOT NULL,
    elevation_m  DOUBLE PRECISION,
    accuracy_m   DOUBLE PRECISION,
    recorded_at  TIMESTAMP NOT NULL,
    geom         GEOMETRY(Point, 4326) NOT NULL
);</code></pre>
<p>좌표는 <code>GEOMETRY(Point, 4326)</code> 타입으로 PostGIS에 저장합니다.<br /><code>sequence_num</code>으로 포인트 순서를 보장합니다.</p>
<hr />
<h2 id="🧱-엔티티">🧱 엔티티</h2>
<pre><code class="language-java">@Entity
@Table(name = &quot;gps_tracks&quot;)
@Getter
@Builder
@NoArgsConstructor
@AllArgsConstructor
public class GpsTrack {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name = &quot;track_id&quot;)
    private Long trackId;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = &quot;session_id&quot;, nullable = false)
    private HikingRecord hikingRecord;

    @Column(name = &quot;sequence_num&quot;, nullable = false)
    private Integer sequenceNum;

    @Column(name = &quot;elevation_m&quot;)
    private Double elevationM;

    @Column(name = &quot;accuracy_m&quot;)
    private Double accuracyM;

    @Column(name = &quot;recorded_at&quot;, nullable = false)
    private LocalDateTime recordedAt;

    @Column(name = &quot;geom&quot;, nullable = false, columnDefinition = &quot;GEOMETRY(Point, 4326)&quot;)
    private Point geom;
}</code></pre>
<p>PostGIS의 <code>Point</code> 타입을 JPA에서 사용하려면 아래 의존성이 필요합니다.</p>
<pre><code class="language-groovy">// build.gradle
implementation 'org.hibernate.orm:hibernate-spatial:6.6.13.Final'
implementation 'org.locationtech.jts:jts-core:1.19.0'</code></pre>
<hr />
<h2 id="💾-gps-포인트-저장-api">💾 GPS 포인트 저장 API</h2>
<h3 id="repository">Repository</h3>
<pre><code class="language-java">public interface GpsTrackRepository extends JpaRepository&lt;GpsTrack, Long&gt; {

    @Query(&quot;SELECT COALESCE(MAX(g.sequenceNum), 0) FROM GpsTrack g WHERE g.hikingRecord.id = :sessionId&quot;)
    int findMaxSequenceNum(@Param(&quot;sessionId&quot;) Long sessionId);

    @Query(&quot;SELECT g FROM GpsTrack g WHERE g.hikingRecord.id = :sessionId ORDER BY g.sequenceNum ASC&quot;)
    List&lt;GpsTrack&gt; findBySessionIdOrderBySequenceNum(@Param(&quot;sessionId&quot;) Long sessionId);
}</code></pre>
<h3 id="request-dto">Request DTO</h3>
<pre><code class="language-java">@Getter
@NoArgsConstructor
public class GpsTrackRequest {
    private Double latitude;
    private Double longitude;
    private Double elevationM;
    private Double accuracyM;
    private LocalDateTime recordedAt;
}</code></pre>
<h3 id="service--저장">Service — 저장</h3>
<pre><code class="language-java">public void saveTrack(Long sessionId, GpsTrackRequest request) {
    HikingRecord session = hikingRecordRepository.findById(sessionId)
            .orElseThrow(() -&gt; new IllegalArgumentException(&quot;세션 없음: &quot; + sessionId));

    int nextSeq = gpsTrackRepository.findMaxSequenceNum(sessionId) + 1;

    GeometryFactory gf = new GeometryFactory(new PrecisionModel(), 4326);
    Point point = gf.createPoint(new Coordinate(request.getLongitude(), request.getLatitude()));

    GpsTrack track = GpsTrack.builder()
            .hikingRecord(session)
            .sequenceNum(nextSeq)
            .elevationM(request.getElevationM())
            .accuracyM(request.getAccuracyM())
            .recordedAt(request.getRecordedAt())
            .geom(point)
            .build();

    gpsTrackRepository.save(track);
}</code></pre>
<p><code>GeometryFactory</code>로 위도/경도를 PostGIS <code>Point</code>로 변환합니다.<br /><code>Coordinate(longitude, latitude)</code> 순서 주의 — X축이 경도입니다.</p>
<hr />
<h2 id="🔍-gps-포인트-조회-api">🔍 GPS 포인트 조회 API</h2>
<h3 id="response-dto">Response DTO</h3>
<pre><code class="language-java">@Getter
public class GpsTrackResponse {

    private final Long trackId;
    private final Integer sequenceNum;
    private final Double latitude;
    private final Double longitude;
    private final Double elevationM;
    private final Double accuracyM;
    private final LocalDateTime recordedAt;

    public GpsTrackResponse(GpsTrack track) {
        this.trackId = track.getTrackId();
        this.sequenceNum = track.getSequenceNum();
        this.latitude = track.getGeom().getY();   // Y = 위도
        this.longitude = track.getGeom().getX();  // X = 경도
        this.elevationM = track.getElevationM();
        this.accuracyM = track.getAccuracyM();
        this.recordedAt = track.getRecordedAt();
    }
}</code></pre>
<p>PostGIS <code>Point</code>에서 좌표를 꺼낼 때 <code>getY()</code> = 위도, <code>getX()</code> = 경도입니다.</p>
<h3 id="service--조회">Service — 조회</h3>
<pre><code class="language-java">public List&lt;GpsTrackResponse&gt; getTracks(Long sessionId) {
    return gpsTrackRepository.findBySessionIdOrderBySequenceNum(sessionId)
            .stream()
            .map(GpsTrackResponse::new)
            .toList();
}</code></pre>
<h3 id="controller">Controller</h3>
<pre><code class="language-java">// 저장
@PostMapping(&quot;/{sessionId}/tracks&quot;)
public ResponseEntity&lt;Void&gt; saveTrack(
        @PathVariable Long sessionId,
        @RequestBody GpsTrackRequest request) {
    hikingService.saveTrack(sessionId, request);
    return ResponseEntity.status(HttpStatus.CREATED).build();
}

// 조회
@GetMapping(&quot;/{sessionId}/tracks&quot;)
public ResponseEntity&lt;List&lt;GpsTrackResponse&gt;&gt; getTracks(@PathVariable Long sessionId) {
    return ResponseEntity.ok(hikingService.getTracks(sessionId));
}</code></pre>
<hr />
<h2 id="🌐-cors-설정">🌐 CORS 설정</h2>
<p>HTML 파일이나 프론트 개발 서버에서 <code>localhost:8080</code> API를 호출하려면 CORS 설정이 필요합니다.</p>
<pre><code class="language-java">@Configuration
public class CorsConfig {

    @Bean
    public CorsFilter corsFilter() {
        CorsConfiguration config = new CorsConfiguration();
        config.addAllowedOriginPattern(&quot;*&quot;);
        config.addAllowedMethod(&quot;*&quot;);
        config.addAllowedHeader(&quot;*&quot;);

        UrlBasedCorsConfigurationSource source = new UrlBasedCorsConfigurationSource();
        source.registerCorsConfiguration(&quot;/api/**&quot;, config);

        return new CorsFilter(source);
    }
}</code></pre>
<hr />
<h2 id="🗺️-maplibre-gl-js로-경로-렌더링-테스트">🗺️ MapLibre GL JS로 경로 렌더링 테스트</h2>
<p>백엔드 구현 후 실제 지도에 경로가 그려지는지 확인하기 위해 간단한 HTML 테스트 페이지를 만들었습니다.</p>
<pre><code class="language-html">&lt;!DOCTYPE html&gt;
&lt;html lang=&quot;ko&quot;&gt;
&lt;head&gt;
  &lt;meta charset=&quot;UTF-8&quot;&gt;
  &lt;title&gt;ORDA GPS 경로 테스트&lt;/title&gt;
  &lt;script src=&quot;https://unpkg.com/maplibre-gl@3.6.2/dist/maplibre-gl.js&quot;&gt;&lt;/script&gt;
  &lt;link href=&quot;https://unpkg.com/maplibre-gl@3.6.2/dist/maplibre-gl.css&quot; rel=&quot;stylesheet&quot;&gt;
  &lt;style&gt;
    body { margin: 0; }
    #map { width: 100vw; height: 100vh; }
  &lt;/style&gt;
&lt;/head&gt;
&lt;body&gt;
&lt;div id=&quot;map&quot;&gt;&lt;/div&gt;
&lt;script&gt;
  const SESSION_ID = 1;

  const map = new maplibregl.Map({
    container: 'map',
    style: 'https://tiles.openfreemap.org/styles/liberty',
    center: [126.978, 37.5665],
    zoom: 14
  });

  map.on('load', async () =&gt; {
    const res = await fetch(`http://localhost:8080/api/hiking/${SESSION_ID}/tracks`);
    const tracks = await res.json();

    const coordinates = tracks.map(t =&gt; [t.longitude, t.latitude]);

    map.addSource('route', {
      type: 'geojson',
      data: { type: 'Feature', geometry: { type: 'LineString', coordinates } }
    });

    map.addLayer({
      id: 'route-line',
      type: 'line',
      source: 'route',
      paint: { 'line-color': '#ff6b35', 'line-width': 4 }
    });

    new maplibregl.Marker({ color: '#00c851' })
      .setLngLat(coordinates[0])
      .setPopup(new maplibregl.Popup().setText('시작'))
      .addTo(map);

    new maplibregl.Marker({ color: '#ff4444' })
      .setLngLat(coordinates[coordinates.length - 1])
      .setPopup(new maplibregl.Popup().setText('종료'))
      .addTo(map);

    const bounds = coordinates.reduce(
      (b, c) =&gt; b.extend(c),
      new maplibregl.LngLatBounds(coordinates[0], coordinates[0])
    );
    map.fitBounds(bounds, { padding: 60 });
  });
&lt;/script&gt;
&lt;/body&gt;
&lt;/html&gt;</code></pre>
<p>MapLibre는 Mapbox와 달리 <strong>토큰 없이 무료로 사용 가능</strong>합니다.<br /><code>openfreemap.org</code> 스타일을 사용하면 한국 지도 타일도 정상 렌더링됩니다.</p>
<hr />
<h2 id="✅-테스트-결과">✅ 테스트 결과</h2>
<table>
<thead>
<tr>
<th>항목</th>
<th>결과</th>
</tr>
</thead>
<tbody><tr>
<td>GPS 포인트 저장</td>
<td>✅ 201 Created</td>
</tr>
<tr>
<td>GPS 포인트 조회</td>
<td>✅ 200 OK, sequence_num 순 정렬</td>
</tr>
<tr>
<td>PostGIS 좌표 변환</td>
<td>✅ Point(4326) 정상 저장</td>
</tr>
<tr>
<td>CORS</td>
<td>✅ 브라우저에서 API 호출 성공</td>
</tr>
<tr>
<td>MapLibre 경로 렌더링</td>
<td>✅ 지도에 경로 선 및 마커 표시</td>
</tr>
</tbody></table>
<hr />
<h2 id="💡-배운-점">💡 배운 점</h2>
<p><strong>1. PostGIS Coordinate 순서</strong><br /><code>new Coordinate(longitude, latitude)</code> — X가 경도, Y가 위도. 반대로 넣으면 좌표가 뒤집힌다.</p>
<p><strong>2. hibernate-spatial 의존성</strong><br />JPA에서 PostGIS <code>Point</code> 타입을 쓰려면 <code>hibernate-spatial</code>과 <code>jts-core</code>를 반드시 추가해야 한다. 팀 프로젝트라면 팀원에게 사전 공유 필수.</p>
<p><strong>3. MapLibre는 토큰 불필요</strong><br />Mapbox 대신 MapLibre + openfreemap 조합으로 토큰 없이 지도 테스트 가능. 프론트 개발 전 백엔드 검증용으로 유용하다.</p>