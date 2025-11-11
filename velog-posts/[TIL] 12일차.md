<blockquote>
<p>알고리즘 오답노트</p>
</blockquote>
<pre><code>function solution(num1, num2) {
    var answer = 0;

    if(num1==num2){
        answer = 1;
    }else{
        answer= -1;
    }</code></pre><blockquote>
<p>정답</p>
</blockquote>
<pre><code>function solution(num1, num2) {
  if(num1==num2){
      return 1;
  } else{
      return -1;
  }
}</code></pre><p>answer의 return 값을 주지 않았기 때문에 생긴 문제!
return answer; 를 추가해주면 된다.
두 번째 코드도 첫 번째와 값은 동일하지만 더욱 더 간결하게 나타냄.</p>
<blockquote>
<p>걷기반 실습 오답노트</p>
</blockquote>
<p>오늘 걷기반 실습 2번문제에서 어이없는 실수를 했는데 ,,,</p>
<pre><code>let tutorNames = ['최원장', '김르탄', '윤창식', '박가현', '김병연', '내배캠'];

tutorNames.forEach(function(tutor) {
  // console.log(tutor)
  if(tutor ==='최원장' ||  tutor ==='윤창식'|| tutor === '박가현' || tutor === '김병연') ;  {
    console.log(tutor); 
  }

});
</code></pre><p>튜터님이 보시더니 분명 맞는데 ,, 왜 틀렸지 ,, 하시더니
10초만에 발견하신 그 오류</p>
<p>&quot; ; &quot;</p>
<p>if 조건문 () 옆에 ; 을 붙여버린 것 .. !!</p>
<p><strong>한 문장이 완료 했을 경우</strong>에만 사용한다고 알려주셨다. 사실 그 전까지 언제 써야하는지 몰라서 헷갈렸었는데
이번 기회에 알고 넘어가서 다행</p>