<p>Postman 테스트 순서</p>
<ol>
<li>세션 먼저 생성 (sessionId 확보)
POST 
<a href="http://localhost:8080/api/hiking/start">http://localhost:8080/api/hiking/start</a></li>
</ol>
<p>Content-Type: application/json</p>
<p>{
    &quot;userId&quot;: 1
}
→ 응답에서 sessionId 값 확인해줘.</p>
<ol start="2">
<li>세션 조회
GET <a href="http://localhost:8080/api/hiking/%7BsessionId%7D">http://localhost:8080/api/hiking/{sessionId}</a>
{sessionId} 자리에 1번에서 받은 값 넣으면 돼.
예: GET <a href="http://localhost:8080/api/hiking/1">http://localhost:8080/api/hiking/1</a></li>
</ol>
<p>결과 말해줘.</p>