<h2 id="프로젝트-배경">프로젝트 배경</h2>
<p>ORDA는 GPS 기반 등산 경로 감지 및 활동 데이터 분석 플랫폼이다. 등산로 데이터를 A(수집) → B(정제) → C(보강/적재) 단계로 가공하여 PostgreSQL + PostGIS에 적재하는 파이프라인을 운영하고 있다.</p>
<p>C단계에서 내가 맡은 역할은 B단계에서 받은 노드-엣지 그래프에 DEM 고도, 경사도, 난이도, 정상(summit) 매칭 정보를 결합하고, 최종 GeoJSON을 PostGIS에 적재하는 것이다.</p>
<p>이번 글에서는 <strong>15만 건 이상의 공간 데이터</strong>를 처리하면서 만났던 성능 병목과 해결 과정을 정리한다.</p>
<hr />
<h2 id="문제-1-nearest_summit-계산이-끝나지-않는다">문제 1: nearest_summit 계산이 끝나지 않는다</h2>
<h3 id="상황">상황</h3>
<p>파이프라인을 실행하면 <code>[C단계] final_trail_dataset 생성 중...</code> 메시지가 출력된 뒤 아무런 진행 없이 멈춰버렸다. 10분, 20분이 지나도 끝나지 않았다.</p>
<h3 id="원인-분석">원인 분석</h3>
<p>문제는 <code>find_nearest_summit_id()</code> 함수였다. 이 함수는 <strong>각 edge의 중점에서 가장 가까운 정상(summit)을 찾는</strong> 역할을 한다.</p>
<pre><code class="language-python"># 기존 코드 — 브루트포스 방식
def find_nearest_summit_id(midpoint_lng, midpoint_lat, summit_list, max_distance_m):
    nearest_id = None
    nearest_dist = float(&quot;inf&quot;)

    for summit in summit_list:  # 16,371개 전체 순회
        dist = haversine_distance_m(
            midpoint_lng, midpoint_lat,
            summit[&quot;lng&quot;], summit[&quot;lat&quot;],
        )
        if dist &lt; nearest_dist:
            nearest_dist = dist
            nearest_id = summit[&quot;summit_id&quot;]
    ...</code></pre>
<p>edge 1개마다 summit <strong>16,371개를 전부 순회</strong>하면서 거리를 계산하고 있었다.</p>
<pre><code>162,693 edges × 16,371 summits = 약 26억 번 거리 계산</code></pre><p>Haversine 공식은 삼각함수 연산(sin, cos, atan2)을 포함하므로 연산 비용이 높다. 26억 번이면 수십 분에서 1시간 이상 걸리는 게 당연했다.</p>
<h3 id="해결-scipy-kdtree-도입">해결: scipy KDTree 도입</h3>
<p><strong>KDTree</strong>(K-Dimensional Tree)는 공간 검색을 위한 트리 자료구조다. 한 번 트리를 구축해두면, &quot;가장 가까운 점 찾기&quot;를 <strong>O(log n)</strong>으로 수행할 수 있다.</p>
<h4 id="좌표-변환">좌표 변환</h4>
<p>KDTree는 유클리드 거리 기반이므로, 위경도(WGS84)를 그대로 넣으면 정확도가 떨어진다. 위경도를 <strong>3D 직교좌표(x, y, z)</strong>로 변환하여 지구 표면 위의 거리를 유클리드 거리로 근사할 수 있게 했다.</p>
<pre><code class="language-python">import numpy as np
from scipy.spatial import cKDTree

def build_summit_kdtree(summit_list):
    R = 6371000.0  # 지구 반지름 (m)
    coords = []
    ids = []

    for summit in summit_list:
        lat_rad = math.radians(summit[&quot;lat&quot;])
        lng_rad = math.radians(summit[&quot;lng&quot;])
        x = R * math.cos(lat_rad) * math.cos(lng_rad)
        y = R * math.cos(lat_rad) * math.sin(lng_rad)
        z = R * math.sin(lat_rad)
        coords.append([x, y, z])
        ids.append(summit[&quot;summit_id&quot;])

    tree = cKDTree(np.array(coords))
    return tree, ids</code></pre>
<h4 id="검색-함수">검색 함수</h4>
<pre><code class="language-python">def find_nearest_summit_id_kdtree(midpoint_lng, midpoint_lat, summit_kdtree, max_distance_m):
    tree, ids = summit_kdtree

    R = 6371000.0
    lat_rad = math.radians(midpoint_lat)
    lng_rad = math.radians(midpoint_lng)
    x = R * math.cos(lat_rad) * math.cos(lng_rad)
    y = R * math.cos(lat_rad) * math.sin(lng_rad)
    z = R * math.sin(lat_rad)

    dist, idx = tree.query([x, y, z])  # O(log n)

    if dist &lt;= max_distance_m:
        return ids[idx]
    return None</code></pre>
<h4 id="사용-방식">사용 방식</h4>
<p>핵심은 <strong>KDTree를 루프 밖에서 한 번만 구축</strong>하고, 루프 안에서는 <code>query()</code>만 호출하는 것이다.</p>
<pre><code class="language-python"># 루프 진입 전 — 한 번만 구축
summit_kdtree = build_summit_kdtree(summit_list)

for edge_feature in edge_features:
    ...
    # 루프 안 — O(log n) 검색
    nearest_summit_id = find_nearest_summit_id_kdtree(
        midpoint[0], midpoint[1],
        summit_kdtree,
        SUMMIT_LINK_MAX_DISTANCE_M,
    )</code></pre>
<h3 id="결과">결과</h3>
<table>
<thead>
<tr>
<th></th>
<th>브루트포스</th>
<th>KDTree</th>
</tr>
</thead>
<tbody><tr>
<td><strong>연산 횟수</strong></td>
<td>162,693 × 16,371 ≈ 26억</td>
<td>162,693 × ~14 ≈ 230만</td>
</tr>
<tr>
<td><strong>소요 시간</strong></td>
<td>수십 분 ~ 1시간+</td>
<td><strong>수 초</strong></td>
</tr>
<tr>
<td><strong>시간 복잡도</strong></td>
<td>O(n × m)</td>
<td>O(n × log m)</td>
</tr>
</tbody></table>
<p>기존 300m 이내만 매칭하는 비즈니스 로직은 그대로 유지하면서, 검색 성능만 개선했다.</p>
<hr />
<h2 id="문제-2-db-적재가-너무-느리다">문제 2: DB 적재가 너무 느리다</h2>
<h3 id="상황-1">상황</h3>
<p>파이프라인은 빨라졌는데, <code>load_to_postgis.py</code>로 DB에 적재하는 과정이 또 끝나지 않았다. trail_nodes 156,191개 + trail_edges 162,693개 + summit_points 16,371개 = <strong>총 33만 건 이상</strong>을 넣어야 했다.</p>
<h3 id="원인">원인</h3>
<p>건당 <code>cursor.execute()</code>를 호출하는 방식이었다.</p>
<pre><code class="language-python"># 기존 코드 — 건당 INSERT
for feat in features:
    cursor.execute(sql, (
        props.get(&quot;node_id&quot;),
        props.get(&quot;node_type&quot;),
        ...
    ))</code></pre>
<p>INSERT 1건마다 Python → PostgreSQL 왕복이 발생한다. 33만 번의 DB 왕복은 네트워크 오버헤드만으로도 상당한 시간이 걸린다.</p>
<h3 id="해결-psycopg2-execute_values-배치-insert">해결: psycopg2 execute_values 배치 INSERT</h3>
<p><code>psycopg2.extras.execute_values()</code>는 여러 건의 VALUES를 하나의 INSERT 문에 묶어서 보낸다. <code>page_size=1000</code>으로 설정하면 1,000건씩 묶어서 한 번에 전송한다.</p>
<pre><code class="language-python">from psycopg2.extras import execute_values

def load_nodes(cursor, features):
    sql = &quot;&quot;&quot;
        INSERT INTO trail_nodes (
            node_id, node_type, degree,
            elevation_m, elevation_status, qa_status, geom
        ) VALUES %s
        ON CONFLICT (node_id) DO NOTHING
    &quot;&quot;&quot;

    template = &quot;(%(node_id)s, %(node_type)s, %(degree)s, ...)&quot;

    rows = []
    for feat in features:
        rows.append({
            &quot;node_id&quot;: props.get(&quot;node_id&quot;),
            &quot;node_type&quot;: props.get(&quot;node_type&quot;),
            ...
        })

    execute_values(cursor, sql, rows, template=template, page_size=1000)
    return cursor.rowcount</code></pre>
<h3 id="결과-1">결과</h3>
<table>
<thead>
<tr>
<th></th>
<th>건당 INSERT</th>
<th>배치 INSERT</th>
</tr>
</thead>
<tbody><tr>
<td><strong>DB 왕복 횟수</strong></td>
<td>~335,000회</td>
<td>~335회</td>
</tr>
<tr>
<td><strong>소요 시간</strong></td>
<td>5~15분</td>
<td><strong>1분 이내</strong></td>
</tr>
</tbody></table>
<hr />
<h2 id="문제-3-fk-제약조건-에러">문제 3: FK 제약조건 에러</h2>
<h3 id="상황-2">상황</h3>
<p><code>load_to_postgis.py</code> 실행 시 아래 에러가 발생했다.</p>
<pre><code>오류: &quot;summit_verifications&quot; 이름의 릴레이션(relation)이 없습니다</code></pre><h3 id="원인-1">원인</h3>
<p><code>summit_verifications</code> 테이블은 공통 <code>db.sql</code>(MVP 전체 스키마)에만 있고, C단계 전용 <code>c_analyze/db.sql</code>에는 없었다. 적재 스크립트가 <code>DELETE FROM summit_verifications</code>를 무조건 실행하고 있어서, C단계 DB 환경에서 에러가 났다.</p>
<p>또한 <code>summit_verifications.summit_id</code>가 <code>summit_points(summit_id)</code>를 FK로 참조하기 때문에, 공통 DB 환경에서는 <code>summit_points</code>를 삭제하기 전에 <code>summit_verifications</code>를 먼저 삭제해야 한다.</p>
<h3 id="해결">해결</h3>
<p><code>information_schema</code>에서 테이블 존재 여부를 확인하고 조건부로 삭제하도록 변경했다.</p>
<pre><code class="language-python">def table_exists(cursor, table_name):
    cursor.execute(
        &quot;SELECT EXISTS (SELECT 1 FROM information_schema.tables &quot;
        &quot;WHERE table_schema = 'public' AND table_name = %s)&quot;,
        (table_name,),
    )
    return cursor.fetchone()[0]

# main() 내
if table_exists(cursor, &quot;summit_verifications&quot;):
    cursor.execute(&quot;DELETE FROM summit_verifications&quot;)</code></pre>
<p>이렇게 하면 공통 DB(summit_verifications 있음)와 C단계 단독 DB(summit_verifications 없음) 두 환경 모두에서 안전하게 동작한다.</p>
<hr />
<h2 id="문제-4-surface-컬럼-불일치">문제 4: surface 컬럼 불일치</h2>
<h3 id="상황-3">상황</h3>
<p><code>load_to_postgis.py</code>의 INSERT 문에는 <code>surface</code> 컬럼이 있는데, <code>db.sql</code>과 실제 DB에는 <code>surface</code> 컬럼이 없었다. 코드-스키마 불일치 상태.</p>
<h3 id="원인-2">원인</h3>
<p>표준 문서에 surface가 A → B → C → DB까지 유지되어야 한다고 명시되어 있었지만, DB 스키마에 반영이 안 된 상태였다.</p>
<h3 id="해결-1">해결</h3>
<ol>
<li>로컬 DB에 컬럼 추가: <code>ALTER TABLE trail_edges ADD COLUMN surface TEXT;</code></li>
<li>공통 <code>db.sql</code>과 <code>c_analyze/db.sql</code> 모두 <code>trail_edges</code> 정의에 <code>surface TEXT</code> 추가</li>
<li><code>pipeline.py</code>의 surface 매핑 우선순위 변경: B단계 edge 우선, A단계 공공 등산로 fallback</li>
</ol>
<pre><code class="language-python"># B단계 edge에 surface가 있으면 사용, 없으면 A단계에서 조회
surface = properties.get(&quot;surface&quot;) or public_surface_map.get(trail_id)</code></pre>
<hr />
<h2 id="최종-적재-결과">최종 적재 결과</h2>
<pre><code>trail_nodes: 154,151행
trail_edges: 158,871행
summit_points: 16,371행
surface 있음: 52,349건 / 없음(null): 106,522건
geom NULL: 0개
SRID: 4326
edge → node 참조 실패: 0개</code></pre><hr />
<h2 id="배운-점">배운 점</h2>
<p><strong>대량 데이터는 알고리즘이 먼저다.</strong> 코드가 &quot;동작은 하지만 끝나지 않는&quot; 상황은 대부분 시간 복잡도 문제다. 브루트포스 O(n×m)을 KDTree O(n×log m)으로 바꾸는 것만으로 수십 분이 수 초가 됐다.</p>
<p><strong>DB 적재는 배치가 기본이다.</strong> 건당 INSERT는 소규모 테스트에서는 문제없지만, 만 건 이상부터는 반드시 배치 방식을 써야 한다. psycopg2의 <code>execute_values()</code>는 공식 문서에서도 대량 INSERT 시 권장하는 방법이다.</p>
<p><strong>스키마는 코드보다 먼저 맞춰야 한다.</strong> db.sql과 코드의 컬럼이 불일치하면 적재 시점에 에러가 난다. 특히 팀 프로젝트에서는 공통 스키마 파일을 먼저 수정하고, 코드를 그에 맞추는 순서가 안전하다.</p>
<hr />
<h2 id="기술-스택">기술 스택</h2>
<ul>
<li>Python 3, psycopg2, scipy (cKDTree), numpy, rasterio</li>
<li>PostgreSQL 18 + PostGIS</li>
<li>GeoJSON, WGS84 / EPSG:4326</li>
</ul>