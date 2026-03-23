<h2 id="개요">개요</h2>
<p>등산 앱 ORDA의 GPS 실시간 트래킹 기능을 구현하면서 겪은 과정을 정리했습니다.
브라우저 Geolocation API로 실시간 좌표를 수신하고, GeoJSON으로 변환해 지도에 표시하는 것이 핵심이었습니다.</p>
<hr />
<h2 id="기술-스택">기술 스택</h2>
<ul>
<li>React 19 + TypeScript</li>
<li>Vite 7</li>
<li>Tailwind CSS v4</li>
<li>MapLibre GL v4</li>
<li>Zustand, React Query, Axios, Dayjs</li>
</ul>
<hr />
<h2 id="폴더-구조">폴더 구조</h2>
<p>GPS 기능은 <code>features/gps</code> 폴더 안에서 관리합니다.
기능이 커지면 <code>store/</code>, <code>api/</code> 폴더를 추가하는 방식으로 확장할 예정입니다.</p>
<pre><code>src/
├── features/
│   └── gps/
│       ├── hooks/useGPS.ts
│       ├── components/GpsTrackingMap.tsx
│       └── types/gps.types.ts
├── components/map/CommonMap.tsx
└── pages/HikingPage.tsx</code></pre><hr />
<h2 id="타입-정의">타입 정의</h2>
<p>GPS 한 점의 데이터와 hook 전체 상태를 타입으로 분리했습니다.</p>
<pre><code>// src/features/gps/types/gps.types.ts

export type GpsPoint = {
  lat: number;
  lng: number;
  altitude: number | null;  // 실내/일부 기기는 null
  accuracy: number;         // 오차 반경 (미터, 낮을수록 정확)
  timestamp: number;
};

export type GpsState = {
  currentPos: GpsPoint | null;
  trail: GpsPoint[];
  isTracking: boolean;
  error: string | null;
};</code></pre><hr />
<h2 id="usegps-hook-구현">useGPS hook 구현</h2>
<p>핵심은 <code>watchPosition</code>으로 실시간 좌표를 수신하고 GeoJSON으로 변환하는 것입니다.</p>
<h3 id="⚠️-좌표-순서-주의">⚠️ 좌표 순서 주의</h3>
<p>브라우저 GPS는 위도/경도 순으로 반환하지만, GeoJSON은 반드시 경도/위도 순이어야 합니다.
이 순서가 바뀌면 지도에 엉뚱한 곳에 점이 찍힙니다.</p>
<pre><code>// GPS에서 받은 값
pos.coords.latitude   // 위도
pos.coords.longitude  // 경도

// GeoJSON에 넣을 때 반드시 순서 바꾸기
coordinates: [pos.coords.longitude, pos.coords.latitude]</code></pre><h3 id="trail-성능-최적화">trail 성능 최적화</h3>
<p>경로 배열을 <code>useState</code>로만 관리하면 좌표가 찍힐 때마다 배열 전체가 복사되어 성능에 좋지 않습니다.
내부 누적은 <code>useRef</code>로 빠르게 처리하고, 외부에 노출할 때만 <code>useState</code>로 동기화했습니다.</p>
<pre><code>const trailRef = useRef&lt;GpsPoint[]&gt;([]);  // 내부 누적용 (성능)
const [trail, setTrail] = useState&lt;GpsPoint[]&gt;([]);  // 외부 노출용

// GPS 수신 시
trailRef.current.push(point);
setTrail([...trailRef.current]);  // 외부 동기화</code></pre><hr />
<h2 id="commonmap-버그-수정">CommonMap 버그 수정</h2>
<p>기존 <code>CommonMap</code> 컴포넌트에 두 가지 문제가 있었습니다.</p>
<h3 id="문제-1--geojsondata-변경-시-지도-재생성">문제 1 — geoJsonData 변경 시 지도 재생성</h3>
<p>GPS 좌표가 업데이트될 때마다 지도가 통째로 재생성되는 문제였습니다.</p>
<pre><code>// 기존 — deps 배열에 geoJsonData 포함
useEffect(() =&gt; { ... }, [center, zoom, styleUrl, geoJsonData, onMapReady]);

// 수정 — 초기화 1회 + 데이터 업데이트 별도 effect
useEffect(() =&gt; { ... }, []);

useEffect(() =&gt; {
  const source = map.getSource(SOURCE_ID) as maplibregl.GeoJSONSource;
  source.setData(geoJsonData ?? { type: &quot;FeatureCollection&quot;, features: [] });
}, [geoJsonData]);</code></pre><h3 id="문제-2--onmapready-인라인-함수로-인한-map-재생성">문제 2 — onMapReady 인라인 함수로 인한 map 재생성</h3>
<p>부모에서 인라인으로 함수를 넘기면 매번 새 함수 참조가 생겨 map이 재생성됩니다.</p>
<pre><code>// 기존 — 인라인 함수로 인한 재생성
onMapReady={() =&gt; { ... }}

// 수정 — useRef로 콜백 안정화
const onMapReadyRef = useRef(onMapReady);
useEffect(() =&gt; { onMapReadyRef.current = onMapReady; }, [onMapReady]);</code></pre><hr />
<h2 id="maplibre-gl-버전-이슈">MapLibre GL 버전 이슈</h2>
<p>MapLibre GL v5와 OpenFreeMap <code>bright</code> 스타일이 호환되지 않아 <code>projection</code> 에러가 발생했습니다.
v4로 다운그레이드해서 해결했습니다.</p>
<pre><code>pnpm add maplibre-gl@4</code></pre><hr />
<h2 id="모바일-테스트--ngrok">모바일 테스트 — ngrok</h2>
<p>GPS는 HTTPS 환경에서만 동작합니다.
배포 전 실제 산에서 테스트하려면 ngrok으로 로컬 서버를 HTTPS로 터널링해야 합니다.</p>
<pre><code># 터미널 1
pnpm dev --host

# 터미널 2
ngrok http 5173</code></pre><p>ngrok에서 발급된 <code>https://xxxx.ngrok-free.app/hiking</code> 주소로 핸드폰에서 접속하면 됩니다.</p>
<p>vite.config.ts에 허용 호스트도 추가해야 합니다.</p>
<pre><code>server: {
  host: true,
  allowedHosts: [&quot;xxxx.ngrok-free.app&quot;],
}</code></pre><hr />
<h2 id="결과">결과</h2>
<ul>
<li>브라우저 GPS 권한 허용 후 실시간 좌표 수신 확인</li>
<li>지도에 현재 위치 빨간 점 및 이동 경로 파란 라인 표시</li>
<li>GPS 정보 패널 (위도/경도/정확도/고도/누적 포인트) 실시간 업데이트</li>
<li>ngrok으로 모바일 웹 테스트 완료</li>
<li>실외 산 테스트 예정</li>
</ul>
<hr />
<h2 id="느낀-점">느낀 점</h2>
<p>가장 헷갈렸던 부분은 GPS 좌표 순서였습니다.
브라우저는 위도/경도 순으로 주는데, GeoJSON은 경도/위도 순이라 처음에 지도에 엉뚱한 곳에 점이 찍혔습니다.
공식 문서를 꼼꼼히 읽는 습관의 중요성을 다시 한번 느꼈습니다.</p>