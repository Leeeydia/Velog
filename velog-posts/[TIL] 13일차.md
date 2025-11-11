<blockquote>
<p>GIT</p>
</blockquote>
<p>지금까지 GIT 은 VS코드의 소스제어를 통해서 커밋하고 푸시했는데
이번 GIT 심화강의를 통해서 터미널을 이용한 GIT 사용 방법에 대해 자세히 알 수 있었다</p>
<p>그리고 팀원들의 도움 덕분에 터미널을 이용한 git push / pull 을 연습해 볼 수 있어서 감사했다</p>
<blockquote>
<p>터미널을 이용해 GIT PUSH 하는 법</p>
</blockquote>
<p>git add .  -&gt; .을 붙이는 이유는 수정된 모든 파일을 한꺼번에 push 할 수 있다.
수정된 파일 중 1.js 파일만 push 하고 싶다면
git add 1.js 라고 입력하면 된다.</p>
<p>git commit -m &quot;커밋 메시지&quot;</p>
<p>git push origin dev(main)</p>
<p>현재 우리 팀은 dev 브랜치를 사용하고 있어서 dev를 입력했다
main 사용시 main 으로 쓰면 됨</p>
<p>git branch -&gt; 현재 브랜치 설정이 어디로 되어있는지 확인 가능</p>
<blockquote>
<p>git pull 하는 법</p>
</blockquote>
<p>git pull origin dev -&gt; 개발 시작 전 팀원들이 깃헙에 올린 수정된 파일을 먼저 땡겨온 뒤 시작해야함. 안 그러면 충돌 생길 수도 있음</p>
<p>어제 git pull 하다가 몇 가지 충돌이 일어났는데
그 중 해결된 한 가지 충돌을 작성해보자면</p>
<p>화면에 초록색 빨강색으로 나타나서 뭐지 했는데</p>
<p>(빨강) HEAD-&gt;  git pull  되기 전, 내가 가지고 있던 코드로서 과거본</p>
<p>(초록) origin/master-&gt;  git pull  후, 새로 반영된 코드로서 최신본</p>
<p>둘 중에 어느 코드를 사용할지 고른 후, 필요없는 코드는 삭제하면 된다.</p>
<p>나는 HEAD 부분을 삭제하고 다시 git pull 했더니 정상적으로 돌아왔다.</p>