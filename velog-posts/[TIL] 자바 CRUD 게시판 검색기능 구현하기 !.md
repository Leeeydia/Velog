<h3 id="java-crud---검색-기능을-구현해보자-">JAVA CRUD - 검색 기능을 구현해보자 !</h3>
<p>오늘은 java 콘솔 기반 CRUD 프로젝트에서 검색 기능을 구현했다.</p>
<h4 id="핵심-코드">&lt;핵심 코드&gt;</h4>
<p><strong>✔ 제목에 <code>keyword</code> 가 포함된 Article만 필터링하는 기능</strong></p>
<pre><code class="language-java">System.out.print(&quot;검색어를 입력하세요 : &quot;);
String keyword = sc.nextLine().trim();

List&lt;Article&gt; searchedArticles = articles.stream()
        .filter(a -&gt; a.getTitle().contains(keyword)) 
        .toList();

if (searchedArticles.isEmpty()) {
    System.out.println(&quot;해당 제목을 가진 글이 없습니다&quot;);
    continue;
}

System.out.println(&quot;== 검색 결과 ==&quot;);
for (Article a : searchedArticles) {
    System.out.printf(&quot;%d / %s / %s / %s\n&quot;,
            a.getId(),
            a.getTitle(),
            a.getBody(),
            a.getRegDate());
}
</code></pre>
<blockquote>
<p>사용한 Java 문법 정리</p>
</blockquote>
<p><strong>✔ 1. <code>stream()</code></strong>
-리스트(ArrayList)를 스트림으로 바꿔서 데이터를 순차적으로 처리할 수 있게 해주는 기능.</p>
<p><strong>✔ 2. <code>filter()</code></strong>
-조건에 맞는 데이터만 걸러내는 메소드</p>
<pre><code>.filter(a -&gt; a.getTitle().contains(keyword))
</code></pre><p><strong>✔ 3. <code>.toList()</code></strong>
-필터링 된 결과를 다시 List 형태로 반환.</p>
<p><strong>✔ 4. <code>isEmpty()</code></strong>
-검색 결과가 없을 때 안내 메시지 출력.</p>
<blockquote>
<p>JS CRUD와 비교하면서 느낀 점</p>
</blockquote>
<p><strong>1. JS는 배열 탐색이 더 자유롭고 간단했다</strong></p>
<p>예를 들자면 !</p>
<pre><code>articles.filter(a =&gt; a.title.includes(keyword))
</code></pre><p>이런식으로 자바보다는 비교적 간단한 식이 완성됐다.
처음에 CRUD를 배우면서 stream() 이라는 개념이 익숙하지 않아서 어렵게 느껴졌다.</p>
<p><img alt="" src="https://velog.velcdn.com/images/leeeydia/post/eb613072-516d-439a-b2f3-1f1872677296/image.png" /></p>
<p>그림처럼 stream을 호스를 연결해 물을 흐르게 하는 것이라고 이해하면 
쉽게 이해할 수 있다.</p>
<p><strong>2. 이전에 만들었던 JS ToDoList 프로젝트와 비교해보기</strong>
<strong>JS ToDoList</strong></p>
<ul>
<li>DOM 이벤트 기반</li>
<li>input value로 검색</li>
<li>웹 화면에서 바로 반영</li>
</ul>
<p><strong>JS ArticleController</strong></p>
<ul>
<li>Scanner로 직접 콘솔에서 입력받아야 함</li>
<li>흐름 제어는 continue/break로 해결</li>
<li>사용자가 정확히 명령어를 입력해야만 수행</li>
</ul>
<blockquote>
<p>오늘 배운 점</p>
</blockquote>
<p><strong>✔ 검색 기능 자체는 JS 경험 덕분에 오히려 쉽게 이해되었다.</strong></p>
<p>오늘 선생님이 주신 과제는 
<strong>1) 테스트 데이터 입력하기, 2) 검색 기능 구현하기</strong>였다.
처음에는 1번 테스트 데이터 입력하는 부분이 너무 막막해서,
“예전에 배웠던 목데이터처럼 만드는 건가?” 하는 고민부터 들었고
어떻게 시작해야 할지 감도 잘 오지 않았다.</p>
<p>그런데 오히려 더 어려울 거라 하셨던 2번 검색 기능은
문제를 보는 순간 바로 JS에서 쓰던 <code>filter()</code> 메서드와
java에서 새롭게 배운 <code>stream()</code> 이 떠올랐다.</p>
<p>JavaScript는 거의 다 잊어버린 줄 알았는데,
비슷한 개념이 Java에서 나오니까
오히려 자연스럽게 코드가 떠오르고
구현하는 동안 시간이 훅 지나갈 정도로 집중할 수 있었다.</p>
<p>이번 과제를 하면서
CRUD가 결국 모든 프로젝트의 기초가 된다는 걸 다시 느꼈다.
나중에 더 큰 프로젝트를 할 때 제대로 기반을 쌓아두려면
지금 이 CRUD 개념을 확실히 익혀야겠다고 다짐했다.</p>
<p><strong>✔ Java는 구조가 중요해서 처음엔 길어보여도 체계적이다.</strong></p>
<ul>
<li>검색, 수정, 삭제같은 기능들이 클래스와 메서드 단위로 명확하게 구조화 되어있어서 유지보수에 좋다는 점을 다시 한 번 느꼈다.</li>
</ul>