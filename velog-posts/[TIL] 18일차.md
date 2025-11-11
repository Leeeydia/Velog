<p>일주일간의 팀 프로젝트 완성 ........
우리 팀이 구현한 대표적인 기능을 소개하자면</p>
<ol>
<li>캐러셀</li>
<li>Filter를 이용하여 장르별 영화 분류</li>
<li>댓글/좋아요 기능</li>
<li>검색기능 및 모달창 구현</li>
</ol>
<p>이 중에서 나는 4번과 메뉴페이지, API 정리를 맡아서 진행했다
주제 자체가 개인과제의 연장선이라는 느낌이 들어서 크게 어렵지 않겠다
라는 생각이 들었지만
그건 큰 오산이었다...... ㅋ</p>
<blockquote>
<p>검색 기능</p>
</blockquote>
<p>첫 번째로 TMDB에서 fetch / .then형태로 이루어진 api를 호출하고 json 형태로 반환시켰다. </p>
<p>Filter기능을 이용해 영화 제목에 포함된 결과를 필터링 한 후
검색 결과가 있으면 표시할 컨테이너를 가져와 기존 걸 제거한 뒤 새 결과를 표시하고.
검색 결과가 없으면 모달창을 표시하게 구현했다</p>
<blockquote>
<p>모달 구현</p>
</blockquote>
<p>모달창을 보이게 할때는 display 속성을 block으로 설정해서 모달창을 보이게 하고,
모달창을 숨길 때는 display 속성을 none 으로 설정한 뒤 숨겼다.</p>
<p>이벤트 핸들러를 사용해서 모달창 밖을 클릭하거나 x버튼 누르면 모달창 닫히게하는 추가적인 부분도 구현했다.</p>
<p>movie.length로 검색 결과가 있는지 확인하고, 검색 결과가 없을 경우
openModal(block 설정) 함수를 호출해 모달창이 열리도록 하였다</p>
<blockquote>
<p>검색 기능 문제점 및 해결방안</p>
</blockquote>
<ol>
<li>각각의 메뉴페이지 ( popular, Top Rated, Now Playing) API 내에서만 검색을 해야하는데
Movie 전체의 API가 가져와지는 현상이 발생...</li>
</ol>
<p>-&gt; 링크만 변경 했더니 바로 해결됐다</p>
<blockquote>
<p>모달창 문제점</p>
</blockquote>
<ol>
<li>CSS를 적용하는 과정에서
백그라운드 이미지의 투명도를 설정하지 않아서 
모달창이지만 전체화면마냥 아주 크게 설정되어서 뒷배경이 다 가려지는
오류가 나타났다.</li>
</ol>
<p>-&gt; rgba 속성을 0으로 설정해서 투명도를 수정했다</p>
<blockquote>
<p>추가 기능 구현</p>
</blockquote>
<p>기존의 TMDB API를 그대로 가져오면 1페이지 = 20개의 영화만 가져오게 되는데, 튜터님께서 10페이지는 가져와야지 UX적인 측면에서 만족스러울거다 라는 피드백을 주셔서 기존 API에 for 반복문을 돌려서 
총 10페이지 = 200개의 영화를 가져왔다</p>
<blockquote>
<p>잘한 점</p>
</blockquote>
<ol>
<li><p>일단 최선을 다해보자!
팀원분들이 제안해주신 요구사항을 받았을 때 
아 저 이런거 못해요! 라고 바로 표현하기보다는 일단 한번 해보자 ! 라는 생각으로 프로젝트 과정에 임했던 것 같다 ..
결과적으로는 챗지피티와 팀원분들의 도움을 많이 받았긴 하지만,
코드를 해석하고 공부해가며 내 것으로 만드는 계기가 되었음 한다.</p>
</li>
<li><p>회의 내용 정리하기 / 우선순위 정하여 해결하기
회의에 좀 더 집중하고, 빠트리는 점 없이 프로젝트를 진행할 수 있었다.
사실 git 관련해서 모르는점이 많았는데
회의 때 팀원분이 알려주신 점을 메모해놓고 계속 사용해보니까 
제대로 습득할 수 있었다. </p>
</li>
</ol>
<p>또한 우선순위를 정해서 일을 해결하니 체계적으로 과제를 진행할 수 있었다.</p>
<blockquote>
<p>개선해야 할 점</p>
</blockquote>
<ol>
<li><p>확실한 사전조사가 필요하다 !  
API 코드 정리 관련 업무를 맡았는데
팀원들이 요청한 것만 전달해 주는 것이 아니라 팀원들이 맡은 엄무를 파악하고 그에 맞는 API를 전달해줘야겠다는 생각이 들었다. 
이번 프로젝트를 진행하면서 비슷한 영화 추천 구현할 때 
팀장님께서 여러 API를 사용하고, setattribute 를 사용하여 요소의 속성값을 정해가며까지 추천 영화를 뽑아왔는데
알고보니 TMDB에서 추천 영화 API가 있었던 것... 나의 사전조사가 부족했었다. 다음에는 이런 일 없도록 해야지..</p>
</li>
<li><p>JS 공부가 더 필요하다
아직까지는 이 문법을 어떻게 사용하는지는 알아도
어떻게 적용해야할지 감이 잡히질 않는다. 
ex) 200페이지를 가져오려면 for 반복문을 통해서 가져오면 되겠구나
근데 어떻게 코드를 작성해야하지 ? ?
라는 ... 문제점이 발생
아무리 구글링을 해봐도 내 코드를 작성하기엔 아직까진 어려운 것 같다
한동안은 코드를 자주 따라서 써보고 익숙해져야할듯 ... 어렵다!</p>
</li>
</ol>
<blockquote>
<p>결과물</p>
</blockquote>
<p>GitHub - <a href="https://github.com/JongHoJang/movie-team-project">https://github.com/JongHoJang/movie-team-project</a>
배포링크 - <a href="https://movie-team-project.vercel.app/">https://movie-team-project.vercel.app/</a></p>
<p><img alt="" src="https://velog.velcdn.com/images/leeeydia/post/3c7dad66-93fc-40ec-ba8f-0948c04c10f8/image.png" /></p>
<p>-&gt; 메인페이지</p>
<p><img alt="" src="https://velog.velcdn.com/images/leeeydia/post/6e5ea978-13b9-4b95-b012-ca405c5e2870/image.png" /></p>
<p>-&gt; 메뉴</p>
<p><img alt="" src="https://velog.velcdn.com/images/leeeydia/post/3e01dcec-5a12-40dc-8eb6-5866fab75b54/image.png" /></p>
<p>-&gt; 상세페이지</p>
<p><img alt="" src="https://velog.velcdn.com/images/leeeydia/post/da15a239-1f6e-4d33-b006-9a793e6549f3/image.png" />
-&gt; 리뷰</p>
<p><img alt="" src="https://velog.velcdn.com/images/leeeydia/post/0a93ac64-7866-4e0a-ab7c-e70f33c1c786/image.png" /></p>
<p><img alt="" src="https://velog.velcdn.com/images/leeeydia/post/8761914b-dca5-4b14-a6c7-037561429c9e/image.png" /></p>
<p>-&gt; 2조 비전공자들 !!</p>