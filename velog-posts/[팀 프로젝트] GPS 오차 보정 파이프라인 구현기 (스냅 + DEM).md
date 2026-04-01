<h2 id="배경">배경</h2>
<p>등산 앱에서 GPS를 그대로 저장하면 두 가지 문제가 생긴다.</p>
<ol>
<li><strong>위치 오차</strong> — GPS는 10~30m 오차가 있어서 등산로 옆 허공에 찍힌다</li>
<li><strong>고도 오차</strong> — 기기 GPS 고도는 실제 지형과 차이가 나거나 null이다</li>
</ol>
<p>이걸 해결하기 위해 백엔드에서 raw GPS 좌표를 받아 보정한 뒤 저장하는 파이프라인을 구현했다.</p>
<hr />
<h2 id="공식-정책">공식 정책</h2>
<table>
<thead>
<tr>
<th>항목</th>
<th>기준</th>
</tr>
</thead>
<tbody><tr>
<td>공식 위치</td>
<td>등산로에 스냅된 좌표</td>
</tr>
<tr>
<td>공식 고도</td>
<td>스냅 좌표 기준 DEM 고도</td>
</tr>
<tr>
<td>raw GPS</td>
<td>참고용으로만 저장</td>
</tr>
<tr>
<td>geom 컬럼</td>
<td>snapped 좌표 기준 저장</td>
</tr>
</tbody></table>
<hr />
<h2 id="전체-흐름">전체 흐름</h2>
<pre><code>프론트 → raw GPS (위도, 경도, 고도)
           ↓
    1. TrailSnapService
       PostGIS ST_ClosestPoint로 반경 50m 내 가장 가까운 등산로 위 점 찾기
       → snapped 좌표 반환 (스냅 실패 시 raw 좌표 그대로)
           ↓
    2. DemService
       GeoTIFF DEM 파일에서 snapped 좌표의 실제 지형 고도 샘플링
       → canonical 고도 반환
           ↓
    3. HikingService.saveGpsTrack()
       raw + canonical 모두 DB에 저장
       geom은 snapped 좌표 기준으로 생성</code></pre><hr />
<h2 id="1-trailsnapservice--등산로-스냅">1. TrailSnapService — 등산로 스냅</h2>
<p>PostGIS의 <code>ST_ClosestPoint</code> 함수를 사용한다.
<code>ST_ClosestPoint(linestring, point)</code>는 linestring 위에서 point와 가장 가까운 점을 반환한다.</p>
<pre><code class="language-sql">SELECT
    ST_X(ST_ClosestPoint(e.geom, ST_SetSRID(ST_MakePoint(:lon, :lat), 4326))) AS snappedLon,
    ST_Y(ST_ClosestPoint(e.geom, ST_SetSRID(ST_MakePoint(:lon, :lat), 4326))) AS snappedLat,
    ST_Distance(e.geom::geography, ST_SetSRID(ST_MakePoint(:lon, :lat), 4326)::geography) AS distanceM
FROM trail_edges e
WHERE ST_DWithin(
    e.geom::geography,
    ST_SetSRID(ST_MakePoint(:lon, :lat), 4326)::geography,
    50  -- 반경 50m
)
ORDER BY distanceM
LIMIT 1</code></pre>
<ul>
<li><code>ST_DWithin</code> 으로 반경 50m 내 등산로만 탐색 → spatial index 활용</li>
<li>반경 내 등산로가 없으면 스냅 실패로 처리하고 raw 좌표를 그대로 사용</li>
</ul>
<hr />
<h2 id="2-demservice--dem-고도-샘플링">2. DemService — DEM 고도 샘플링</h2>
<p>NASADEM GeoTIFF 파일을 서버 시작 시 한 번만 메모리에 로드한다.
매 요청마다 파일을 열지 않으므로 성능에 영향이 없다.</p>
<pre><code class="language-java">@PostConstruct
public void init() {
    TIFFImage tiffImage = TiffReader.readTiff(demFile);
    FileDirectory directory = tiffImage.getFileDirectory();
    rasters = directory.readRasters();
    // GeoTIFF 메타데이터에서 원점 좌표와 픽셀 해상도 읽기
    originLon = tiepoint.get(3);
    originLat = tiepoint.get(4);
    pixelWidth = pixelScale.get(0);
    pixelHeight = pixelScale.get(1);
}</code></pre>
<p>고도 샘플링 시 지리 좌표를 픽셀 좌표로 변환해서 값을 읽는다.</p>
<pre><code class="language-java">int pixelX = (int) Math.round((lon - originLon) / pixelWidth);
int pixelY = (int) Math.round((originLat - lat) / pixelHeight);
Number value = rasters.getPixel(pixelX, pixelY)[0];</code></pre>
<p>DEM 실패 시 fallback 처리:</p>
<table>
<thead>
<tr>
<th>상황</th>
<th>elevation_source</th>
</tr>
</thead>
<tbody><tr>
<td>DEM 샘플링 성공</td>
<td><code>dem</code></td>
</tr>
<tr>
<td>DEM 실패 + raw 고도 있음</td>
<td><code>gps_fallback</code></td>
</tr>
<tr>
<td>DEM 실패 + raw 고도 없음</td>
<td><code>none</code></td>
</tr>
</tbody></table>
<hr />
<h2 id="3-gps_tracks-테이블-구조">3. gps_tracks 테이블 구조</h2>
<p>raw와 canonical을 분리해서 저장한다.</p>
<pre><code class="language-sql">raw_latitude          DOUBLE PRECISION NOT NULL  -- 기기 GPS 원본
raw_longitude         DOUBLE PRECISION NOT NULL
raw_elevation_m       DOUBLE PRECISION           -- 기기 GPS 고도 (참고용)

snapped_latitude      DOUBLE PRECISION           -- 등산로 스냅 좌표
snapped_longitude     DOUBLE PRECISION
canonical_elevation_m DOUBLE PRECISION           -- DEM 기준 공식 고도
elevation_source      VARCHAR(20)                -- dem / gps_fallback / none

geom                  GEOMETRY(Point, 4326)      -- snapped 좌표 기준</code></pre>
<hr />
<h2 id="실제-테스트-결과">실제 테스트 결과</h2>
<p>좌표 <code>36.3504, 127.3845</code> (대전 근처) 기준으로 테스트했다.</p>
<table>
<thead>
<tr>
<th>항목</th>
<th>값</th>
</tr>
</thead>
<tbody><tr>
<td>raw 좌표</td>
<td>36.3504, 127.3845</td>
</tr>
<tr>
<td>snapped 좌표</td>
<td>36.35007..., 127.38450...</td>
</tr>
<tr>
<td>raw 고도 (GPS)</td>
<td>100m</td>
</tr>
<tr>
<td>canonical 고도 (DEM)</td>
<td>91m</td>
</tr>
<tr>
<td>외부 사이트 실제 고도</td>
<td>83m</td>
</tr>
<tr>
<td>elevation_source</td>
<td>dem</td>
</tr>
</tbody></table>
<p>GPS 고도(100m)보다 DEM 고도(91m)가 실제 지형(83m)에 더 가깝다.
NASADEM은 30m 해상도라 약 8m 오차는 정상 범위다.</p>
<hr />
<h2 id="geom을-snapped-기준으로-저장하는-이유">geom을 snapped 기준으로 저장하는 이유</h2>
<p><code>geom</code> 컬럼은 지도 표시, 거리 계산, 리플레이에 사용된다.
raw 좌표로 저장하면 지도에 등산로 밖 허공에 찍히고 거리 계산도 오차가 생긴다.
canonical 정책과 실제 동작이 일치하도록 snapped 좌표 기준으로 저장한다.</p>
<pre><code class="language-java">// canonical 변환 먼저 수행
CanonicalGpsPoint canonical = gpsTrackProcessor.process(
        request.getLatitude(), request.getLongitude(), request.getElevationM()
);

// geom은 snapped 좌표 기준으로 생성
Point geom = geometryFactory.createPoint(
        new Coordinate(canonical.getSnappedLongitude(), canonical.getSnappedLatitude())
);</code></pre>
<hr />
<h2 id="개발-환경-설정">개발 환경 설정</h2>
<p>DEM 파일은 용량이 커서 git에 포함하지 않는다.
<code>application.yml</code>에서 경로를 비워두면 <code>elevation_source = gps_fallback</code>으로 동작한다.</p>
<pre><code class="language-yaml"># 개발 환경 (DEM 미사용)
dem:
  file-path: &quot;&quot;

# DEM 사용 시
dem:
  file-path: /path/to/korea_dem.tif</code></pre>