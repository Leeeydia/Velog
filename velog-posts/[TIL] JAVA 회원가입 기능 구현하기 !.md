<h3 id="java-crud---회원가입-기능을-구현해보자-">JAVA CRUD - 회원가입 기능을 구현해보자 !</h3>
<h4 id="🔧-이번에-직접-구현한-기능들">🔧 이번에 직접 구현한 기능들</h4>
<ul>
<li><p>회원 정보를 저장하는 리스트(ArrayList) 구성</p>
</li>
<li><p>아이디 중복을 직접 검사하는 로직 작성</p>
</li>
<li><p>비밀번호와 비밀번호 확인 검증</p>
</li>
<li><p>회원가입 시 날짜(regDate, updateDate) 직접 생성</p>
</li>
<li><p>로그인 시도 시 회원 검색</p>
</li>
<li><p>비밀번호 일치 여부 체크</p>
</li>
<li><p>로그인 성공 후 현재 로그인된 멤버 정보 저장</p>
</li>
<li><p>로그아웃 시 로그인 정보 초기화</p>
</li>
</ul>
<h3 id="🔧-트러블-슈팅--feat-java-진짜-어렵네-">🔧 트러블 슈팅  (feat. JAVA 진짜 어렵네 !)</h3>
<p><img alt="" src="https://velog.velcdn.com/images/leeeydia/post/32a4147d-3494-4dc9-8785-d70776b33f90/image.png" /></p>
<ol>
<li><p><code>회원 검색</code> 이라는 개념 자체가 조금 어려웠다.
프론트에서는 보통 DB에서 자동으로 찾아주는데, 백엔드 과정에서는
리스트를 직접 돌면서 조건을 만족하는 회원을 찾아야 한다는 점이 
낯설게 느껴졌다.</p>
<p><strong><em>머리를 백엔드화 해야겠다는 생각이 또 한번 들었달까 ...😭</em></strong></p>
<ol start="2">
<li><p>&quot;찾으면 반환, 못찾으면 <code>null</code> ?&quot; 
null 을 반환해야하는 이유가 이해가 안됐었다.
null이 발생했을 때 로그인 처리를 어떻게 해야 하는지까지 고려해야 하기 때문에 꼭 사용해야한다는 점을 배웠다.</p>
</li>
<li><p>프론트 과정에서는 절대 고민하지 않았던 부분 ! </p>
</li>
</ol>
<p><strong><em>Supabase 너가 그리워..😭</em></strong></p>
<p>프론트 과정에서 첫 DB 사용으로 <code>Supabase</code>를 배웠는데
그 당시 튜터님들도 처음 사용해보는 DB 사이트 ? 프로그램 ? 이라서
배우고 적용하는 데 엄청 고생했던 기억이 있다.</p>
<p><strong><em>그때 고생했던 기억을 끄집어보자면 !</em></strong></p>
<p><img alt="" src="https://velog.velcdn.com/images/leeeydia/post/4aada168-870e-4586-826d-381130414c63/image.png" />
<img alt="" src="https://velog.velcdn.com/images/leeeydia/post/dfabd849-f3d2-439f-944b-e3621be81de7/image.png" /></p>
</li>
</ol>
<p>예전에 카카오톡/이메일로 가입하기 기능을 만들면서, 사용자가 입력한 정보가 Supabase 테이블에 저장되도록 구현한 적이 있었다.</p>
<p>그때는 테이블에 들어가는 <code>타입값(Type)</code>도 무엇을 의미하는지 제대로 몰랐었고, 어떤 칼럼을 만들어야 하는지 감도 안 와서 결국 GPT에게 도움을 받아서 Supabase 테이블을 만들었었다.</p>
<p>하지만 지금 다시 보니 &quot; 아 이게 이런 의미였구나 !&quot; 하고 이해가 되는 기분이다.</p>
<p>특히 그때 고생했던 부분이 NULL값 처리였는데, 이번에 Java를 배우면서 
<code>null</code>이 왜 중요한지, 어떤 상황에서 문제가 되는지 어느정도 이해가 됐다.</p>
<p>프론트에서는 null이 있어도 UI에서 <code>undefiend</code> 정도로 표현되고 크게 터지지 않는데, Java에서는 null 하나 때문에 프로그램이 멈출 수 있다는 점을 새롭게 배웠다.</p>
<h4 id="💡참고하기">💡참고하기</h4>
<p> <strong>null 회원가입 기능에서 언제 등장 하는데 ? ?</strong></p>
<ul>
<li>가입하려는 아이디가 이미 있는지 검사할 때</li>
<li>로그인 시 회원 정보가 있는지 검색할 때</li>
<li>회원가입이 되지 않은 회원인 경우 null을 반환한다.</li>
<li>비밀번호 비교하는데 null이면 또 오류가 난다.</li>
</ul>