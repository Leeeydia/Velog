<h3 id="sessionid---실제-세션-id로-변경한-뒤-진행">sessionId -&gt; 실제 세션 ID로 변경한 뒤 진행</h3>
<pre><code>&lt;!DOCTYPE html&gt;
&lt;html lang=&quot;ko&quot;&gt;
&lt;head&gt;
  &lt;meta charset=&quot;UTF-8&quot;&gt;
  &lt;title&gt;ORDA GPS 경로 테스트&lt;/title&gt;
  &lt;script src=&quot;https://unpkg.com/maplibre-gl@3.6.2/dist/maplibre-gl.js&quot;&gt;&lt;/script&gt;
  &lt;link href=&quot;https://unpkg.com/maplibre-gl@3.6.2/dist/maplibre-gl.css&quot; rel=&quot;stylesheet&quot;&gt;
  &lt;style&gt;
    body { margin: 0; padding: 0; }
    #map { width: 100vw; height: 100vh; }
  &lt;/style&gt;
&lt;/head&gt;
&lt;body&gt;
&lt;div id=&quot;map&quot;&gt;&lt;/div&gt;
&lt;script&gt;
  const SESSION_ID = 1; // ← 네 세션 ID로 변경

  const map = new maplibregl.Map({
    container: 'map',
    style: 'https://demotiles.maplibre.org/style.json',
    center: [126.978, 37.5665],
    zoom: 14
  });

  map.on('load', async () =&gt; {
    const res = await fetch(`http://localhost:8080/api/hiking/${SESSION_ID}/tracks`);
    const tracks = await res.json();

    if (tracks.length === 0) {
      alert('트랙 데이터 없음');
      return;
    }

    const coordinates = tracks.map(t =&gt; [t.longitude, t.latitude]);

    // 경로 선
    map.addSource('route', {
      type: 'geojson',
      data: {
        type: 'Feature',
        geometry: {
          type: 'LineString',
          coordinates: coordinates
        }
      }
    });

    map.addLayer({
      id: 'route-line',
      type: 'line',
      source: 'route',
      paint: {
        'line-color': '#ff6b35',
        'line-width': 4
      }
    });

    // 시작점 마커
    new maplibregl.Marker({ color: '#00c851' })
      .setLngLat(coordinates[0])
      .setPopup(new maplibregl.Popup().setText('시작'))
      .addTo(map);

    // 끝점 마커
    new maplibregl.Marker({ color: '#ff4444' })
      .setLngLat(coordinates[coordinates.length - 1])
      .setPopup(new maplibregl.Popup().setText('종료'))
      .addTo(map);

    // 지도 범위 자동 조정
    const bounds = coordinates.reduce((b, c) =&gt; b.extend(c), new maplibregl.LngLatBounds(coordinates[0], coordinates[0]));
    map.fitBounds(bounds, { padding: 60 });
  });
&lt;/script&gt;
&lt;/body&gt;
&lt;/html&gt;</code></pre>