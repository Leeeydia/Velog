<blockquote>
<p>모달창 구현</p>
</blockquote>
<p>-&gt; 일반 팝업처럼 새로운 윈도우를 여는 것이 아닌 현재 윈도우의 레이어를 쌓아서 보여주는 형식의 팝업을 의미</p>
<p><img alt="" src="https://velog.velcdn.com/images/leeeydia/post/789fe05b-eb2b-4935-bc1d-fef0b3f3908f/image.png" /></p>
<blockquote>
<p>HTML</p>
</blockquote>
<pre><code> &lt;div id=&quot;noResultModal&quot; class=&quot;modal&quot;&gt;
        &lt;div class=&quot;modal-content&quot;&gt;
          &lt;span class=&quot;close&quot; onclick=&quot;closeModal()&quot;&gt;&amp;times;&lt;/span&gt;
          &lt;p&gt;검색한 결과가 나오지 않습니다.&lt;/p&gt;
        &lt;/div&gt;
      &lt;/div&gt;</code></pre><blockquote>
<p>JS</p>
</blockquote>
<pre><code>function openModal() {
  document.getElementById('noResultModal').style.display = 'block';
}

function closeModal() {
  document.getElementById('noResultModal').style.display = 'none';
}

document.getElementById('search-form').addEventListener('submit', search_movie);

window.onclick = function (event) {
  const modal = document.getElementById('noResultModal');
  if (event.target == modal) {
    modal.style.display = 'none';
  }
};
</code></pre><p>얼른 복습하고,,, 수정해야겠다,,,</p>