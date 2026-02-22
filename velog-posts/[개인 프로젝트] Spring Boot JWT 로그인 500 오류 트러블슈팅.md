<h2 id="🔐-1차-트러블--jwt-인증-문제">🔐 1차 트러블 — JWT 인증 문제</h2>
<h3 id="📌-문제">📌 문제</h3>
<p>로그인은 성공해서 토큰은 발급됐지만,<br />이후 모든 보호 API 요청이 실패했다.</p>
<ul>
<li>일기 작성 불가</li>
<li>AI 답변 요청 불가</li>
<li>인증 필요한 기능 전부 차단</li>
</ul>
<p>Emotion Diary 구조상 로그인 인증이 안 되면<br />글 작성도, AI 답변도 받을 수 없었다.</p>
<hr />
<h3 id="🔎-원인">🔎 원인</h3>
<p>JWT 인증 과정에서 문제가 발생했다.<br />Authorization 헤더 형식이 올바르지 않아 인증이 통과되지 않았다.</p>
<hr />
<h3 id="🛠-해결">🛠 해결</h3>
<p>코드를 수정한 것이 아니라,<br />Postman 요청 시 Authorization 헤더에 <code>Bearer</code>를 붙여서 전송하도록 수정했다.</p>
<pre><code>Authorization: Bearer {accessToken}</code></pre><p>이후 정상적으로 인증이 통과되었고<br />일기 작성 및 AI 답변 요청 기능이 모두 동작했다.</p>