<p>TanStack Query  -&gt; 서버 상태를 관리하기 위한 라이브러리
캐싱, 동기화, 무효화 기능을 제공</p>
<p>useQuery -&gt; 데이터를 가져오기 위해 사용되는 TanStack Query의 
대표적인 훅 / 쿼리 키와 비동기함수를 인자로 받아 데이터를 가져오고, 로딩 상태 
useMutation -&gt; 데이터를 생성, 수정, 삭제하는 등의 작업에 사용되는 훅
invalidateQueries -&gt;  특정 쿼리를 무효화하여 데이터를 다시 패칭하게 하는 함수 useMutation과 함께 사용하여 데이터가 변경된 후 관련 쿼리를 다시 가져오도록 함</p>