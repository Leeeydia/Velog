<p><code>#</code>a -&gt; id가 a인 것
<code>.</code>a -&gt; class가 a인것</p>
<p>문제풀이</p>
<pre><code class="language-html">
&lt;nav&gt;
  &lt;section&gt;
  &lt;div&gt;&lt;a href=&quot;#&quot;&gt;아이템1&lt;/a&gt;&lt;/div&gt;
  &lt;div&gt;&lt;a href=&quot;#&quot;&gt;아이템1&lt;/a&gt;&lt;/div&gt;
  &lt;div&gt;&lt;a href=&quot;#&quot;&gt;아이템1&lt;/a&gt;&lt;/div&gt;
  &lt;div&gt;&lt;a href=&quot;#&quot;&gt;아이템1&lt;/a&gt;&lt;/div&gt;
&lt;/section&gt;
&lt;/nav&gt;

</code></pre>
<pre><code class="language-css">
nav{
  text-align : center;

}

nav &gt; section {
  background-color: #afafaf;
  display: inline-block;
  padding: 0 20px;
  border-radius: 10px;
  cursor: pointer;

}

nav &gt;section &gt; div{
  display:inline-block
}
 div&gt; a {
   padding: 20px;
   display:inline-block;
   font-weight: bold;
   letter-spacing: -1px;   
   text-decoration:none;
   color:black;


}

nav &gt; section&gt; div:hover{
  background-color: black;
}</code></pre>
<p>풀이</p>
<p>section -&gt; 메뉴 박스
nav -&gt; </p>
<pre><code>// 무조건 필수로 깔고 갔어야함
a{
color:inherit;
text-decoration: none;}

body{

}





</code></pre><pre><code>section&gt;div :hover{
  background-color: black;
color:white;
}

이거랑


section&gt;div:hover{
  background-color: black;
color:white;
}

차이점 뭐지 왜 띄어쓰기 하나로 효과가 바뀌지 ? ? </code></pre><blockquote>
<p>노멀라이즈 </p>
</blockquote>