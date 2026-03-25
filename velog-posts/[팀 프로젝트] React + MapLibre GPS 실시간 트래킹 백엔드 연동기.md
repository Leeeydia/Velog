<h1 id="react--maplibre-gps-실시간-트래킹-백엔드-연동기">React + MapLibre GPS 실시간 트래킹 백엔드 연동기</h1>
<h2 id="개요">개요</h2>
<p>등산 앱 ORDA를 개발하면서 GPS 실시간 트래킹 데이터를 백엔드와 연동한 과정을 정리합니다.</p>
<p>프론트엔드는 React 19 + TypeScript + MapLibre GL, 백엔드는 Spring Boot + PostGIS를 사용했습니다.</p>
<hr />
<h2 id="목표">목표</h2>
<ul>
<li>등산 시작 시 백엔드에 세션 생성</li>
<li>GPS 포인트 수신마다 백엔드에 저장</li>
<li>등산 종료 시 세션 종료 처리</li>
<li>모바일 ngrok 환경에서 실데이터 테스트</li>
</ul>
<hr />
<h2 id="아키텍처">아키텍처</h2>
<pre><code>모바일(ngrok) → Vite 프론트 → Vite Proxy → Spring Boot → PostGIS</code></pre><p>GPS는 <code>navigator.geolocation.watchPosition</code>으로 실시간 수신하고, 포인트가 올 때마다 백엔드 API를 호출해서 저장합니다.</p>
<hr />
<h2 id="파일-구조">파일 구조</h2>
<pre><code>src/
├── features/
│   └── hiking/
│       ├── types/hiking.types.ts
│       ├── api/hikingApi.ts
│       └── hooks/useHiking.ts
├── features/gps/
│   ├── hooks/useGPS.ts
│   └── components/GpsTrackingMap.tsx
└── pages/HikingPage.tsx</code></pre><hr />
<h2 id="구현">구현</h2>
<h3 id="1-타입-정의">1. 타입 정의</h3>
<pre><code class="language-typescript">// src/features/hiking/types/hiking.types.ts

export interface HikingStartRequest {
  userId: number;
}

export interface HikingStartResponse {
  sessionId: number;
  startedAt: string;
}

export interface HikingEndResponse {
  sessionId: number;
  endedAt: string;
}

export interface GpsTrackRequest {
  latitude: number;
  longitude: number;
  elevationM: number | null;
  accuracyM: number | null;
}

export interface SummitVerifyRequest {
  sessionId: number;
  latitude: number;
  longitude: number;
}

export interface SummitVerifyResponse {
  verified: boolean;
  summitId: string;
  summitName: string;
  distanceM: number;
}</code></pre>
<h3 id="2-api-함수">2. API 함수</h3>
<pre><code class="language-typescript">// src/features/hiking/api/hikingApi.ts

import axios from &quot;axios&quot;;

interface ApiResponse&lt;T&gt; {
  success: boolean;
  message: string;
  data: T;
}

export const startHiking = async (body: HikingStartRequest) =&gt; {
  const res = await axios.post&lt;ApiResponse&lt;HikingStartResponse&gt;&gt;(
    &quot;/api/hiking/start&quot;, body
  );
  return res.data.data;
};

export const endHiking = async (sessionId: number) =&gt; {
  const res = await axios.post&lt;ApiResponse&lt;HikingEndResponse&gt;&gt;(
    `/api/hiking/${sessionId}/end`
  );
  return res.data.data;
};

export const saveGpsTrack = async (sessionId: number, body: GpsTrackRequest) =&gt; {
  await axios.post(`/api/hiking/${sessionId}/tracks`, body);
};

export const verifySummit = async (body: SummitVerifyRequest) =&gt; {
  const res = await axios.post&lt;ApiResponse&lt;SummitVerifyResponse&gt;&gt;(
    &quot;/api/summit/verify&quot;, body
  );
  return res.data.data;
};</code></pre>
<h3 id="3-usegps에-콜백-추가">3. useGPS에 콜백 추가</h3>
<p>GPS 포인트 수신마다 외부에서 동작을 주입할 수 있도록 <code>onPoint</code> 콜백 파라미터를 추가했습니다.</p>
<pre><code class="language-typescript">// src/features/gps/hooks/useGPS.ts

const start = useCallback((onPoint?: (point: GpsPoint) =&gt; void) =&gt; {
  watchIdRef.current = navigator.geolocation.watchPosition(
    (pos) =&gt; {
      const point: GpsPoint = { ... };
      updateGeoJson(trailRef.current, point);
      onPoint?.(point); // 콜백 호출
    },
    ...
  );
}, [updateGeoJson]);</code></pre>
<h3 id="4-usehiking-hook">4. useHiking hook</h3>
<p>GPS와 API를 한 곳에서 조율합니다.</p>
<pre><code class="language-typescript">// src/features/hiking/hooks/useHiking.ts

export function useHiking() {
  const gps = useGPS();
  const [sessionId, setSessionId] = useState&lt;number | null&gt;(null);
  const [isLoading, setIsLoading] = useState(false);
  const [error, setError] = useState&lt;string | null&gt;(null);

  const start = async () =&gt; {
    try {
      setIsLoading(true);
      const res = await startHiking({ userId: 1 }); // TODO: auth 연동 후 교체
      const newSessionId = res.sessionId;
      setSessionId(newSessionId);

      gps.start((point) =&gt; {
        // GPS 포인트 수신마다 백엔드에 저장
        saveGpsTrack(newSessionId, {
          latitude: point.lat,
          longitude: point.lng,
          elevationM: point.altitude ?? null,
          accuracyM: point.accuracy,
        }).catch((e) =&gt; console.error(&quot;GPS 저장 실패:&quot;, e));
      });
    } catch {
      setError(&quot;등산 시작에 실패했습니다.&quot;);
    } finally {
      setIsLoading(false);
    }
  };

  const stop = async () =&gt; {
    if (!sessionId) return;
    try {
      setIsLoading(true);
      await endHiking(sessionId);
      gps.stop();
      setSessionId(null);
    } catch {
      setError(&quot;등산 종료에 실패했습니다.&quot;);
    } finally {
      setIsLoading(false);
    }
  };

  return {
    geoJson: gps.geoJson,
    currentPos: gps.currentPos,
    trail: gps.trail,
    isTracking: gps.isTracking,
    sessionId,
    isLoading,
    error: error ?? gps.error,
    start,
    stop,
  };
}</code></pre>
<h3 id="5-gps-저장-주기-결정-5초">5. GPS 저장 주기 결정 (5초)</h3>
<p>처음에는 GPS 포인트가 수신될 때마다 저장했는데, 1초에 한 번씩 저장되면서 3분 테스트에 190개 포인트가 쌓였습니다. 1시간 등산 기준으로 환산하면 약 3600개입니다.</p>
<h4 id="저장-주기-고민-과정">저장 주기 고민 과정</h4>
<p>처음에는 거리 기반 필터링(20m 이상 이동 시 저장)을 고려했습니다. 실제 테스트 데이터에서 GPS 정확도를 확인했는데 평균 accuracy가 18m였고, GPS 오차 범위 이상으로 설정하는 게 맞다고 판단했습니다.</p>
<pre><code class="language-sql">SELECT AVG(accuracy_m), MIN(accuracy_m), MAX(accuracy_m)
FROM gps_tracks
WHERE session_id = 15;
-- 평균: 18m, 최소: 10.8m, 최대: 120m</code></pre>
<p>그런데 거리 기반 필터링은 <strong>원본 데이터를 버리는 방식</strong>이라 3D 리플레이나 고도 변화 시각화에 쓸 데이터가 줄어드는 문제가 있었습니다. 튀는 포인트를 나중에 보정하려면 오히려 데이터가 많아야 합니다.</p>
<h4 id="최종-결정-5초-시간-기반">최종 결정: 5초 시간 기반</h4>
<table>
<thead>
<tr>
<th>저장 주기</th>
<th>1시간 포인트 수</th>
<th>3D 리플레이 품질</th>
</tr>
</thead>
<tbody><tr>
<td>1초</td>
<td>3,600개</td>
<td>매우 부드러움</td>
</tr>
<tr>
<td>5초</td>
<td>720개</td>
<td>충분히 부드러움 ✅</td>
</tr>
<tr>
<td>10초</td>
<td>360개</td>
<td>뚝뚝 끊길 수 있음</td>
</tr>
</tbody></table>
<p>3D 리플레이 품질과 DB 부담 사이에서 <strong>5초</strong>가 가장 균형잡힌 선택이었습니다. 1시간 기준 720개 포인트면 리플레이가 충분히 부드럽고, 원본 데이터도 충분히 보존됩니다.</p>
<pre><code class="language-typescript">const SAVE_INTERVAL_MS = 5000; // 5초마다 저장

gps.start((point) =&gt; {
  const now = Date.now();
  if (now - lastSavedAt.current &lt; SAVE_INTERVAL_MS) return;
  lastSavedAt.current = now;

  saveGpsTrack(newSessionId, {
    latitude: point.lat,
    longitude: point.lng,
    elevationM: point.altitude ?? null,
    accuracyM: point.accuracy,
  })
    .then(() =&gt; setSavedPointCount((prev) =&gt; prev + 1))
    .catch((e) =&gt; console.error(&quot;GPS 저장 실패:&quot;, e));
});</code></pre>
<h4 id="지도-렌더링과-저장-카운트-분리">지도 렌더링과 저장 카운트 분리</h4>
<p>지도 경로는 GPS 수신마다(1초) 업데이트해서 부드럽게 그리고, 화면에 표시하는 누적 포인트 카운트는 실제 백엔드에 저장된 수 기준으로 표시했습니다.</p>
<table>
<thead>
<tr>
<th></th>
<th>주기</th>
<th>용도</th>
</tr>
</thead>
<tbody><tr>
<td>지도 경로 업데이트</td>
<td>1초 (GPS 수신마다)</td>
<td>부드러운 경로 렌더링</td>
</tr>
<tr>
<td>백엔드 저장</td>
<td>5초</td>
<td>DB 적재</td>
</tr>
<tr>
<td>누적 포인트 카운트</td>
<td>저장 성공 시</td>
<td>UI 표시</td>
</tr>
</tbody></table>
<hr />
<h3 id="6-vite-proxy-설정">6. Vite Proxy 설정</h3>
<p>모바일(ngrok)에서도 API 요청이 백엔드로 가도록 Vite proxy를 설정합니다.</p>
<pre><code class="language-typescript">// vite.config.ts
server: {
  proxy: {
    '/api': 'http://localhost:8080'
  }
}</code></pre>
<p>이렇게 하면 <code>/api/*</code> 요청을 Vite가 가로채서 백엔드로 전달합니다.</p>
<hr />
<h2 id="모바일-테스트-ngrok">모바일 테스트 (ngrok)</h2>
<p>GPS는 HTTPS 환경에서만 동작하기 때문에 ngrok으로 HTTPS URL을 만들어 테스트했습니다.</p>
<pre><code class="language-powershell">ngrok http 5173</code></pre>
<p>모바일에서 ngrok URL + <code>/hiking</code> 으로 접속하면 GPS 권한 요청 후 트래킹이 시작됩니다.</p>
<hr />
<h2 id="결과">결과</h2>
<p>실외에서 약 3분간 걸어다니며 테스트한 결과입니다.</p>
<table>
<thead>
<tr>
<th>항목</th>
<th>결과</th>
</tr>
</thead>
<tbody><tr>
<td>총 이동 거리</td>
<td>551m</td>
</tr>
<tr>
<td>누적 상승 고도</td>
<td>61m</td>
</tr>
<tr>
<td>총 시간</td>
<td>213초</td>
</tr>
<tr>
<td>GPS 포인트 수</td>
<td>190개</td>
</tr>
</tbody></table>
<p>DB에 정상적으로 적재된 것을 확인했습니다.</p>
<pre><code class="language-sql">SELECT * FROM gps_tracks WHERE session_id = 15 ORDER BY sequence_num;
-- 190개 rows 확인</code></pre>
<hr />
<h2 id="마무리">마무리</h2>
<p>이번 작업에서 핵심은 두 가지였습니다.</p>
<p>첫째, <code>useGPS</code>의 <code>watchPosition</code> 콜백에 외부 함수를 주입하는 방식으로 GPS 수신과 API 호출을 깔끔하게 분리했습니다.</p>
<p>둘째, Vite proxy 설정 하나로 PC/모바일 환경 모두 동일하게 백엔드와 통신할 수 있었습니다.</p>
<p>다음은 JWT 인증 연동(<code>feat/auth</code>) 작업을 진행할 예정입니다.</p>