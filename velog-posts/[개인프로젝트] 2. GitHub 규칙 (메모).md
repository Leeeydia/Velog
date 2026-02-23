<p><strong>깃허브 규칙</strong></p>
<p>template(템플릿) 사용</p>
<p>approve(승인) 규칙 사용</p>
<p>최소 한명 코멘트나 리뷰를 받아야 merge 하기</p>
<p>브랜치 규칙</p>
<p>(dev, qa, live) 배포할 때 문제가 있을 수가 있어서, 미리 선작업하는게 좋음</p>
<p>배포가 안되는 이유는 빌드가 깨지는 상황에서 이루어지는데, 그러한 상황을 dev환경에서 머지하여 안정적으로 라이브 환경에 배포할 수 있음 이러한 경험을 할 수 있다.</p>
<p>예시: 메인 만들고 dev분기 → 깃허브 푸시 → 브랜치 2개 → 이 2개로 배포</p>
<p>공통 기능 컴포넌트: feat/calender, feat/chat, feat/search (겹칠 경우)</p>
<p>페이지 컴포넌트: page/commu</p>
<p>commit 규칙</p>
<p>커밋은 최대한 기능단위로, 함수단위로 작성. 왜냐하면 깃은 협업 툴이기도 하지만 형상관리(버전관리) 목적도 있기 때문</p>
<p>커밋규칙
작업 타입 작업내용 ✨ docs readme, 문서 수정할 때 사용 🎉 add 초기 세팅,파일 생성 및 기능 추가 ♻️ refactor 코드 리팩토링 🩹 fix 코드 수정 🔥 del 기능/파일을 삭제 🍻 test 테스트 코드를 작성 (배포할 때, 그럴때 사용하자) 💄 style css</p>
<p>pull 규칙</p>
<p>pull을 받을 때 원격 브랜치와 로컬 브랜치를 서로 같은 브랜치로 맞추어주고, 다른 브랜치로 넘어가 merge를 시킨다.</p>
<p>ex: dev(origin) -&gt; feat/main(local) (x)</p>
<p>dev(origin) -&gt; dev(local) -&gt; feat/main(local) (o)</p>