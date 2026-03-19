<h2 id="개요">개요</h2>
<p>ORDA 프로젝트의 백엔드 작업 중 오늘은 크게 두 가지를 진행했다.</p>
<ol>
<li><code>domain/hiking</code>에 혼재되어 있던 summit 관련 코드를 <code>domain/summit</code>으로 분리</li>
<li>정상 인증 성공 시 <code>summit_verifications</code> 테이블에 저장하는 로직 구현</li>
</ol>
<p>GPS 트랙 포인트 저장 API도 함께 완료했다.</p>
<hr />
<h2 id="작업-배경-및-이유">작업 배경 및 이유</h2>
<p>초기 구현 당시 summit 관련 엔티티, 레포지토리, 서비스, 컨트롤러가 전부 <code>domain/hiking</code> 하위에 위치해 있었다. 기능적으로는 동작했지만 도메인 책임이 명확하지 않아 유지보수 측면에서 문제가 생길 수 있었다.</p>
<p>또한 이전 PR에서 정상 인증 API를 구현할 때 인증 여부만 반환하고 DB 저장은 미구현 상태로 남겨두었는데, 이번에 함께 완성했다.</p>
<hr />
<h2 id="구현-내용">구현 내용</h2>
<h3 id="1-summit-도메인-분리">1. summit 도메인 분리</h3>
<p>기존 구조:</p>
<p>domain/hiking
├── entity/SummitPoint.java
├── repository/SummitPointRepository.java
├── repository/NearestSummitResult.java
├── dto/request/SummitVerifyRequest.java
├── dto/response/SummitVerifyResponse.java
├── service/HikingService.java  ← summit 로직 혼재
└── controller/HikingController.java  ← summit 엔드포인트 혼재</p>
<p>변경 후 구조:</p>
<p>domain/summit
├── entity/SummitPoint.java
├── entity/SummitVerification.java
├── repository/SummitPointRepository.java
├── repository/SummitVerificationRepository.java
├── repository/NearestSummitResult.java
├── dto/request/SummitVerifyRequest.java
├── dto/response/SummitVerifyResponse.java
├── service/SummitService.java
└── controller/SummitController.java</p>
<p><code>HikingService</code>와 <code>HikingController</code>에서 summit 관련 코드를 전부 제거하고 각각 <code>SummitService</code>, <code>SummitController</code>로 분리했다.</p>
<p>엔드포인트도 함께 변경했다.</p>
<ul>
<li>변경 전: <code>POST /api/hiking/summit/verify</code></li>
<li>변경 후: <code>POST /api/summit/verify</code></li>
</ul>
<hr />
<h3 id="2-summitverification-엔티티-설계">2. SummitVerification 엔티티 설계</h3>
<p>DB 스키마를 팀 공통 기준으로 사용하고 있어서 Java 엔티티를 스키마에 맞게 설계했다.</p>
<pre><code class="language-java">@Entity
@Table(
    name = &quot;summit_verifications&quot;,
    uniqueConstraints = @UniqueConstraint(columnNames = {&quot;session_id&quot;, &quot;summit_id&quot;})
)
@Getter
@NoArgsConstructor
public class SummitVerification {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name = &quot;verification_id&quot;)
    private Long id;

    @Column(name = &quot;session_id&quot;, nullable = false)
    private Long sessionId;

    @Column(name = &quot;summit_id&quot;, nullable = false)
    private String summitId;

    @Column(name = &quot;distance_to_summit_m&quot;, nullable = false)
    private Double distanceToSummitM;

    @Column(name = &quot;verification_method&quot;, nullable = false, length = 30)
    private String verificationMethod;

    @Column(name = &quot;verified_at&quot;, nullable = false)
    private LocalDateTime verifiedAt;

    @Column(name = &quot;geom&quot;, nullable = false, columnDefinition = &quot;GEOMETRY(Point, 4326)&quot;)
    private Point geom;

    @Builder
    public SummitVerification(Long sessionId, String summitId, Double distanceToSummitM, Point geom) {
        this.sessionId = sessionId;
        this.summitId = summitId;
        this.distanceToSummitM = distanceToSummitM;
        this.verificationMethod = &quot;gps&quot;;
        this.verifiedAt = LocalDateTime.now();
        this.geom = geom;
    }
}</code></pre>
<p>주요 설계 포인트:</p>
<ul>
<li><code>UNIQUE(session_id, summit_id)</code>: 동일 세션에서 같은 정상을 중복 인증하지 않도록 제약</li>
<li><code>verification_method</code>: MVP는 <code>gps</code> 고정, 추후 <code>photo_exif</code>, <code>sign_recognition</code> 확장 예정</li>
<li><code>geom</code>: 인증 시점의 사용자 GPS 좌표를 PostGIS Point 타입으로 저장</li>
</ul>
<hr />
<h3 id="3-정상-인증-로직-summitservice">3. 정상 인증 로직 (SummitService)</h3>
<pre><code class="language-java">@Transactional
public SummitVerifyResponse verifySummit(SummitVerifyRequest request) {
    NearestSummitResult nearest = summitPointRepository
            .findNearestSummit(request.getLatitude(), request.getLongitude())
            .orElseThrow(() -&gt; new IllegalArgumentException(&quot;정상 데이터가 없습니다.&quot;));

    boolean verified = nearest.getDistance_m() &lt;= nearest.getRadius_m();

    if (verified) {
        Point userPoint = geometryFactory.createPoint(
                new Coordinate(request.getLongitude(), request.getLatitude())
        );

        SummitVerification verification = SummitVerification.builder()
                .sessionId(request.getSessionId())
                .summitId(nearest.getSummit_id())
                .distanceToSummitM(nearest.getDistance_m())
                .geom(userPoint)
                .build();

        summitVerificationRepository.save(verification);
    }

    return SummitVerifyResponse.builder()
            .verified(verified)
            .summitId(nearest.getSummit_id())
            .summitName(nearest.getName())
            .distanceM(nearest.getDistance_m())
            .build();
}</code></pre>
<p>PostGIS <code>ST_Distance</code>로 사용자 위치와 가장 가까운 정상 간 거리를 계산하고, 정상별 <code>radius_m</code> 기준으로 인증 여부를 판별한다. 인증 성공 시에만 DB에 저장한다.</p>
<hr />
<h3 id="4-gps-트랙-포인트-저장-api">4. GPS 트랙 포인트 저장 API</h3>
<p>POST /api/hiking/{sessionId}/tracks
{
  &quot;latitude&quot;: 37.5665,
  &quot;longitude&quot;: 126.9780,
  &quot;elevationM&quot;: 150.0,
  &quot;accuracyM&quot;: 5.0
}
→ 201 Created</p>
<ul>
<li><code>sequence_num</code>은 서버에서 자동 계산 (세션 내 마지막 번호 + 1)</li>
<li><code>geom</code> 컬럼은 WGS84(EPSG:4326), <code>[lng, lat]</code> 순서로 저장</li>
</ul>
<hr />
<h2 id="트러블슈팅">트러블슈팅</h2>
<h3 id="summitverification-엔티티-컬럼-불일치">SummitVerification 엔티티 컬럼 불일치</h3>
<p>초기 작성 시 DB 스키마와 다른 컬럼명을 사용했다.</p>
<table>
<thead>
<tr>
<th>항목</th>
<th>DB 스키마</th>
<th>초기 코드</th>
</tr>
</thead>
<tbody><tr>
<td>PK</td>
<td><code>verification_id</code></td>
<td>미지정</td>
</tr>
<tr>
<td>거리</td>
<td><code>distance_to_summit_m</code></td>
<td><code>distance_m</code></td>
</tr>
<tr>
<td><code>verification_method</code></td>
<td><code>NOT NULL</code></td>
<td>누락</td>
</tr>
<tr>
<td><code>geom</code></td>
<td><code>NOT NULL</code></td>
<td>누락</td>
</tr>
<tr>
<td>UNIQUE 제약</td>
<td><code>(session_id, summit_id)</code></td>
<td>누락</td>
</tr>
</tbody></table>
<p>팀 공통 스키마를 기준으로 엔티티를 재설계하여 해결했다.</p>
<hr />
<h2 id="배운-점">배운 점</h2>
<p><strong>JPA Projection 인터페이스</strong>
<code>NearestSummitResult</code>는 <code>JpaRepository</code>를 상속하지 않는 단순 인터페이스인데, 처음엔 구조가 낯설었다. native query 결과를 매핑하는 Projection 인터페이스로, 쿼리 결과 컬럼명과 getter 이름이 자동으로 매핑된다. repository 폴더에 함께 두는 것이 관례다.</p>
<p><strong>DB 스키마를 먼저 확인하는 습관</strong>
엔티티를 먼저 작성하고 나중에 스키마와 맞추려 하면 불일치가 생긴다. 팀 공통 스키마가 있을 때는 스키마를 기준으로 엔티티를 설계하는 것이 맞다.</p>
<p><strong>도메인 분리는 초기에</strong>
기능이 동작한다고 해서 도메인 경계를 흐리게 두면 나중에 분리 비용이 커진다. 초기 설계 단계에서 도메인 책임을 명확히 하는 것이 중요하다는 걸 다시 한번 느꼈다.</p>
<hr />
<h2 id="테스트-결과">테스트 결과</h2>
<p>포스트맨으로 전체 API 테스트 완료 후 DB 저장 확인까지 마쳤다.</p>
<p>verification_id | session_id | summit_id | distance_to_summit_m | verification_method | verified_at
1               | 1          | S0001     | 5.07281492           | gps                 | 2026-03-18 17:23:14.359</p>