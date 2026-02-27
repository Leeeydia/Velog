<p>프로젝트를 진행하면서 가장 많이 본 숫자는 아마 <code>401</code>, <code>500</code>이 아닐까 싶다.</p>
<p>처음에는 에러가 나면 무조건 코드부터 다시 봤다.<br />프론트에서 뭔가 잘못된 건지, 백엔드가 터진 건지 감으로만 추측했다.</p>
<p>그런데 문제는 따로 있었다.</p>
<p><strong>백엔드가 실제로 무엇을 보내고 있는지 확인하지 않았다는 것.</strong></p>
<p>이번에 제대로 깨달았다.</p>
<hr />
<h3 id="문제-상황">문제 상황</h3>
<ul>
<li>프론트에서 API 요청</li>
<li>401 에러 발생</li>
<li>화면에는 데이터가 보이지 않음</li>
<li>“토큰 문제인가…?” “백엔드가 터진 건가…?”</li>
</ul>
<p>정확히 어디서 문제가 발생한 건지 판단이 되지 않았다.</p>
<hr />
<h3 id="내가-했던-실수">내가 했던 실수</h3>
<ul>
<li>코드만 계속 다시 확인함</li>
<li>프론트 문제인지 백엔드 문제인지 추측만 함</li>
<li>에러의 원인을 단정 지으려고 함</li>
<li><strong>정작 백엔드 응답(Response)은 확인하지 않음</strong></li>
</ul>
<p>사실 가장 중요한 건 따로 있었다.</p>
<hr />
<h3 id="진짜-디버깅-시작점">진짜 디버깅 시작점</h3>
<blockquote>
<p>F12 → Network → 요청 클릭 → Response 확인</p>
</blockquote>
<p>Network 탭에서 응답을 직접 확인하니까<br />“추측”이 아니라 “근거”로 원인을 좁힐 수 있었다.</p>
<hr />
<h4 id="network-탭에서-확인할-수-있는-것들">Network 탭에서 확인할 수 있는 것들</h4>
<ul>
<li><strong>Status Code</strong> (200 / 401 / 500 등)</li>
<li>백엔드에서 내려준 <strong>message</strong></li>
<li>커스텀 <strong>error code</strong></li>
<li>실제 JSON 응답 형태</li>
<li>Request Header / Payload 값</li>
</ul>
<p>이걸 보기 전까지는 그냥 감으로만 판단하고 있었던 셈이다.</p>
<hr />
<h3 id="실제로-해결했던-사례">실제로 해결했던 사례</h3>
<h4 id="1️⃣-401-에러">1️⃣ 401 에러</h4>
<p>Response를 확인해보니:</p>
<pre><code class="language-json">{
  &quot;success&quot;: false,
  &quot;code&quot;: &quot;UNAUTHORIZED&quot;,
  &quot;message&quot;: &quot;인증이 필요합니다.&quot;
}</code></pre>
<p>→ Authorization 헤더에 토큰이 실려 있지 않았다.</p>
<p>프론트 로직 문제가 아니라<br /><strong>요청 헤더 설정 문제</strong>였다.</p>
<hr />
<h4 id="2️⃣-타입-변환-에러-long-변환-실패">2️⃣ 타입 변환 에러 (Long 변환 실패)</h4>
<p>Response를 확인해보니:</p>
<pre><code>Failed to convert value of type 'java.lang.String' to required type 'java.lang.Long'</code></pre><p>→ <code>{2}</code> 같은 잘못된 값이 전달되고 있었다.</p>
<p>이건 프론트에서 PathVariable 또는 파라미터를 잘못 보내고 있던 문제였다.</p>
<hr />
<h3 id="깨달은-점">깨달은 점</h3>
<p>에러가 나면 먼저 물어보자.</p>
<ul>
<li>백엔드가 실제로 뭐라고 응답하고 있는가?</li>
<li>어떤 Status Code를 보냈는가?</li>
<li>내가 보낸 요청은 정확한가?</li>
</ul>
<p>Network 탭을 확인하는 습관을 들이면서<br />문제 해결 속도가 훨씬 빨라졌다.</p>
<hr />
<h3 id="앞으로의-디버깅-루틴">앞으로의 디버깅 루틴</h3>
<ol>
<li>에러 코드(Status Code) 확인</li>
<li>Network 탭에서 해당 요청 선택</li>
<li>Response 내용 확인</li>
<li>Request Header / Payload 값 확인</li>
<li>그 다음에 코드 분석</li>
</ol>
<p>이 순서로 바꾸니<br />막연하게 헤매는 시간이 확실히 줄어들었다.</p>