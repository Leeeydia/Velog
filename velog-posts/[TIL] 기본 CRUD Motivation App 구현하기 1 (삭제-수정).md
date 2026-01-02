<h2 id="🗑️-명언-삭제delete--✏️-수정edit-기능-구현-정리">🗑️ 명언 삭제(Delete) / ✏️ 수정(Edit) 기능 구현 정리</h2>
<h3 id="1️⃣-삭제-기능-del">1️⃣ 삭제 기능 (del)</h3>
<h3 id="✅-구현-목표">✅ 구현 목표</h3>
<ul>
<li><p>del 3 형태의 명령어를 입력하면</p>
</li>
<li><p>ID가 3인 명언을 찾아 리스트에서 삭제</p>
</li>
</ul>
<h3 id="🔧-사용된-핵심-문법">🔧 사용된 핵심 문법</h3>
<p><strong>1. 문자열 분리 (split)</strong></p>
<pre><code>String[] cmdBits = cmd.split(&quot; &quot;);</code></pre><p>&quot;del 3&quot; → [&quot;del&quot;, &quot;3&quot;]</p>
<p>명령어 + 번호를 분리하기 위해 사용한다.</p>
<p><strong>2. 문자열 → 정수 변환 (Integer.parseInt)</strong></p>
<pre><code>int id = Integer.parseInt(cmdBits[1]);</code></pre><p>배열로 분리된 &quot;3&quot;을 숫자 3으로 변환</p>
<p>ID 비교를 위해 필수</p>
<p><strong>3. 리스트 탐색 (향상된 for문)</strong></p>
<pre><code>for (Motivation motivation : motivations) {
    if (motivation.getId() == id) {
        return motivation;
    }
}</code></pre><p>리스트 전체를 순회하며 같은 ID 찾기</p>
<p>객체 비교는 ==가 아니라 값 비교</p>
<p><strong>4. 객체 삭제 (List.remove)</strong></p>
<pre><code>motivations.remove(foundMotivation);</code></pre><p>찾은 객체 자체를 리스트에서 제거</p>
<p>인덱스로 삭제하지 않아도 됨</p>
<h3 id="2️⃣-수정-기능-edit">2️⃣ 수정 기능 (edit)</h3>
<h3 id="✅-구현-목표-1">✅ 구현 목표</h3>
<p>edit 2 입력 시</p>
<p>기존 명언 내용 보여주기</p>
<p>새로운 작가 / 명언 입력받아 수정</p>
<h3 id="🔧-사용된-핵심-문법-1">🔧 사용된 핵심 문법</h3>
<p><strong>1. 기존 객체 내용 출력</strong></p>
<pre><code>System.out.println(&quot;작가 : &quot; + foundMotivation.getBody());
System.out.println(&quot;명언 : &quot; + foundMotivation.getSource());</code></pre><p>수정 전 데이터를 사용자에게 보여줌</p>
<p><strong>2. 빈 문자열 방지 (trim() + length())</strong></p>
<pre><code>newBody = Container.getSc().nextLine().trim();
if (newBody.length() != 0) {
    break;
}</code></pre><p>공백 입력 방지</p>
<p>실제 값이 입력될 때까지 반복</p>
<p><strong>3. while(true) 입력 검증 패턴</strong></p>
<pre><code>while (true) {
    System.out.print(&quot;작가 : &quot;);
    newBody = Container.getSc().nextLine().trim();

    if (newBody.length() != 0) {
        break;
    }
}</code></pre><p>👉 콘솔 입력 유효성 검사에서 자주 쓰는 패턴</p>
<p><strong>4. 객체 값 수정 (setter)</strong></p>
<pre><code>foundMotivation.setBody(newBody);
foundMotivation.setSource(newSource);</code></pre><p>리스트에서 객체를 다시 넣지 않아도 됨</p>
<p>객체 참조이기 때문에 바로 반영됨</p>
<h3 id="🔧-트러블슈팅">🔧 트러블슈팅</h3>
<p>⚠️ equals() 사용으로 명령어 인식 오류 발생
🔍 문제 상황</p>
<p>명언 프로그램에서
삭제(del), 수정(edit), 상세(detail) 같은 명령어를 처리할 때
처음에는 문자열 비교를 equals()로 처리했다.</p>
<pre><code>if (cmd.equals(&quot;del&quot;)) {
    controller.del(cmd);
}</code></pre><p>하지만 실제로 프로그램을 실행해보니
아래와 같은 명령어들이 전혀 동작하지 않았다.</p>
<pre><code>del 1
edit 2
detail 3</code></pre><p>❌ 원인 분석</p>
<p>equals()는 문자열이 <strong>완전히 동일</strong> 할 때만 true를 반환한다.</p>
<pre><code>&quot;del&quot;.equals(&quot;del 1&quot;) // false</code></pre><p>즉,</p>
<pre><code>cmd = &quot;del 1&quot;</code></pre><p>&quot;del&quot;과 정확히 같지 않기 때문에</p>
<p>삭제 로직이 실행되지 않음</p>
<h3 id="🔧해결-방법">🔧해결 방법</h3>
<p><strong>✅ startsWith() 사용</strong></p>
<p>명령어의 공통된 시작 부분만 확인하면 되기 때문에
startsWith()를 사용해야 했다.</p>
<h4 id="🔧-수정한-코드-app-컴포넌트">🔧 수정한 코드 (App 컴포넌트)</h4>
<pre><code>if (cmd.startsWith(&quot;del&quot;)) {
    motivationController.del(cmd);
}
else if (cmd.startsWith(&quot;edit&quot;)) {
    motivationController.edit(cmd);
}
else if (cmd.startsWith(&quot;detail&quot;)) {
    motivationController.detail(cmd);
}</code></pre><h4 id="📌-startswith가-맞는-이유">📌 startsWith()가 맞는 이유</h4>
<p>&quot;del 1&quot;.startsWith(&quot;del&quot;)      // true
&quot;edit 2&quot;.startsWith(&quot;edit&quot;)    // true</p>
<p>명령어 뒤에 숫자가 붙어도 정상 인식하고</p>
<p>콘솔 명령어 처리 구조에 적합하기 때문이다</p>
<h3 id="느낀점">느낀점</h3>
<p>처음에는 단순히
“문자열 비교니까 equals 쓰면 되겠지” 라고 생각했지만,</p>
<p>입력값의 형태를 먼저 생각하지 않으면
문법은 맞아도 로직은 틀릴 수 있다는 걸 깨달았다.</p>
<p>콘솔 프로그램에서는
입력 문자열 구조를 먼저 분석한 뒤
그에 맞는 메서드를 선택하는 게 중요하다는 것을 배웠다.</p>