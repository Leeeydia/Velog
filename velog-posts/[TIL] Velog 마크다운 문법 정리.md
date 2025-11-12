<h1 id="📘-마크다운markdown-문법-정리">📘 마크다운(Markdown) 문법 정리</h1>
<blockquote>
<ul>
<li>읽기 쉽고 쓰기 쉬운 일반 텍스트 형식으로 서식이 있는 문서를 작성하기 위한 경량 마크업 언어입니다.</li>
<li>Velog, GitHub, Notion 등에서 자주 사용됩니다.</li>
</ul>
</blockquote>
<hr />
<h2 id="🏷️-제목heading">🏷️ 제목(Heading)</h2>
<p><code>#</code> 의 개수로 제목의 크기를 조절 할 수 있습니다.</p>
<pre><code class="language-markdown"># 제목 1
## 제목 2
### 제목 3
#### 제목 4
##### 제목 5
###### 제목 6</code></pre>
<h3 id="👉결과">👉결과</h3>
<h1 id="h1">h1</h1>
<h2 id="h2">h2</h2>
<h3 id="h3">h3</h3>
<h4 id="h4">h4</h4>
<h5 id="h5">h5</h5>
<h6 id="h6">h6</h6>
<hr />
<h2 id="✏️-글자-스타일">✏️ 글자 스타일</h2>
<pre><code class="language-markdown">
**굵게**
*기울임*
~~취소선~~
**_굵고 기울임_**
</code></pre>
<h3 id="👉결과-1">👉결과</h3>
<p><strong>굵게</strong>
<em>기울임</em>
<del>취소선</del>
<strong><em>굵고 기울임</em></strong></p>
<hr />
<h2 id="✍인용문">✍인용문</h2>
<pre><code class="language-markdown">&gt; 인용문 작성
-aaa

&gt;인용문 작성
&gt;&gt; (&gt;)의 갯수에 따라
&gt;&gt;&gt; 중첩문이 가능하다</code></pre>
<h3 id="👉결과-2">👉결과</h3>
<blockquote>
<p>인용문 작성
-aaa</p>
</blockquote>
<blockquote>
<p>인용문 작성</p>
<blockquote>
<p>(&gt;)의 갯수에 따라</p>
<blockquote>
<p>중첩문이 가능하다</p>
</blockquote>
</blockquote>
</blockquote>
<hr />
<h2 id="📋리스트">📋리스트</h2>
<h3 id="불릿-리스트-순서가-없는-목록">불릿 리스트 (순서가 없는 목록)</h3>
<pre><code class="language-markdown">`*`, `+`, `-` 중 아무 기호나 사용이 가능하다.
스페이스바로 들여쓰기를 하면 하위 목록이 된다</code></pre>
<h3 id="👉결과-3">👉결과</h3>
<ul>
<li>리스트1  </li>
<li>리스트2  </li>
<li>리스트3<br />이어지는 리스트</li>
</ul>
<h3 id="숫자-리스트">숫자 리스트</h3>
<pre><code class="language-markdown">숫자 뒤에 마침표`.`를 붙여 사용한다.
리스트를 이어서 쓰려면 **한 칸 공백**을 추가한다</code></pre>
<h3 id="👉결과-4">👉결과</h3>
<ol>
<li>숫자리스트<br />이어지는 리스트  </li>
<li>숫자리스트</li>
</ol>
<hr />
<blockquote>
<p>(업데이트 예정) 🙂</p>
</blockquote>