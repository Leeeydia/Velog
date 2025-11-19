<h4 id="매개변수-parameter">매개변수 (Parameter)</h4>
<p>이전에 자바스크립트에서 함수의 매개변수/지역변수를 배웠기 때문에 자바에서 배우는 개념들도 어느정도 익숙했다.
but ... 매개변수 / 지역변수에 대해 배우며
JS와 Java의 개념이 비슷한듯 ~ 다른 듯~ 헷갈리기 시작해서 이 둘의 차이점도 벨로그에 정리해보면 좋을 것 같다는 생각이 들었다.</p>
<ul>
<li>매개변수에서의 JS와 Java 공통점은 뭘까 ?</li>
</ul>
<ol>
<li><p>둘 다 &quot;매개변수 = parameter&quot;</p>
</li>
<li><p>호출할 때 넘기는 값 = &quot;인자(argument)&quot;</p>
</li>
<li><p>함수/메서드 안에서 지역 변수처럼 쓰임</p>
</li>
</ol>
<p>두 언어 모두 '함수에 값을 전달하기 위해 사용되는 변수'
라는 점에서는 동일하다.</p>
<p>하지만 자바는 '정적'타입 언어라서 매개변수에 <strong>타입을 반드시 명시</strong> 해야하고, 매개변수의 <strong>개수도 정확히 맞춰야 한다</strong> 는 점에서 자바스크립트보다 좀 더 엄격한 부분이 있다.
tip) 자바스크립트는 동적타입 언어이다.</p>
<p>예제 코드를 보자면</p>
<pre><code class="language-js">
function add(a,b)
{retrun a+b}</code></pre>
<ul>
<li>a,b에 숫자/문자/배열 뭐든 들어올 수 있다.</li>
<li>타입 지정이 없다</li>
</ul>
<pre><code class="language-java">int add(int a, int b)
{ return a+b;
}</code></pre>
<ul>
<li>a,b는 <strong>반드시 int</strong></li>
<li>다른 타입이 나오면 컴파일 에러가 뜬다.</li>
</ul>
<p>자바는 같은 이름의 메서드를 매개변수 타입/개수로 구분하는 <strong>오버로딩(Overloading)</strong> 을 지원해,
함수 설계 방식이 JS 보다 더 체계적이고 확장성이 있다는 점을 새롭게 알게 되었다.</p>
<p>정리 !
개념은 같지만 언어의 타입에 따라 사용하는 방식이 다르다.</p>
<h5 id="sout-vs-return-">sout vs return ?</h5>
<p>sout (System.out.println(x)) 와 return은 같은 개념인가?</p>
<ul>
<li>결론부터 말 하자면 아니다 X</li>
<li>자바를 배울 때 return 과 sout를 같은 개념으로 착각하고 있었는데
오늘 TIL을 작성하다보니 두 개념은 다르다는 것을 인지했다 .. 헉</li>
</ul>
<p>return -&gt; &quot;메서드의 결과를 돌려주는 것&quot;
sout -&gt; 단순히 &quot;콘솔에 출력하는 것&quot;일 뿐이다.</p>
<p>JS에서 return 과 console.log가 역할이 다른 것처럼
자바에서도 return은 값을 외부로 보내고, 출력은 sout이 담당한다 
그래서 메소드의 결과를 활용하려면 반드시 return이 필요하다는 점을 새롭게 알게되었다.</p>
<h4 id="지역변수">지역변수</h4>