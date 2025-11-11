<p>JS 이용한 영화 검색창 구현</p>
<pre><code>function search_movie(event) {
  event.preventDefault();
  const query = document.querySelector('.search-box').value.toLowerCase();

  if (query) {
    fetch(`${BASE_URL}popular?language=en-US&amp;page=1`, options)
      .then((response) =&gt; response.json())
      .then((data) =&gt; {
        const movies = data.results.filter((movie) =&gt; movie.title.toLowerCase().includes(query));
        const movieContainer = document.getElementById('popMovieContainer');
        movieContainer.innerHTML = '';

        if (movies.length &gt; 0) {
          movies.forEach((movie) =&gt; {
            const card = createMovieCard(movie);
            movieContainer.appendChild(card);
          });
        } else {
          openModal();
        }
      })
      .catch((error) =&gt; console.error('Error:', error));
  }
}</code></pre><ul>
<li><p>영화 API를 fetch 함수를 이용해서 가져온다</p>
</li>
<li><p>가져온 데이터 중 영화 제목이 검색어를 포함하는 영화만 필터링한다
if(query) 통해서</p>
</li>
<li><p>필터링해서 걸린 영화가 있을 경우 영화 카드르 생성한 뒤 
화면에 뿌려준다</p>
</li>
</ul>