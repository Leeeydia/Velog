<p>package org.example;
// 문제 1 자동차 설계도를 만들어주세요.
// 문제 2 객체를 담을 변수를 만들어주세요
// 문제 3 자동차 객체를 만들고 변수에 담아주세요
// 문제 4 자동차 객체마다 서로 다른 최고 속력을 가지도록 해주세요
// 문제 5 1개의 자동차가 3번 달리게 해주세요
// 문제 6 객체를 사용하지 않고 두번째 플레이어를 만들어주세요
// 문제 7 3개의 자동차가 각각 1번씩 달리게 해주세요
// 문제 8 각각의 자동차가 서로 다른 최고속력으로 달리게 해주세요
// 문제 9 번호가 다른 각각의 자동차가 서로 다른 최고속력으로 달리게 해주세요</p>
<p>// 문제 1 풀이</p>
<pre><code>class Main{
    public static void main(String[] args) {

    }

}
 class 자동차{  // Q. 항상 설계도 만들 때는 Main과 동일선상에 위치해야 하는가 ?


     }</code></pre><p>//// 문제 2 풀이</p>
<pre><code>class Main{
    public static void main(String[] args) {
        자동차 a;
    }
}

class 자동차{

}</code></pre><ul>
<li>class 자동차{} 설계도룰  class Main과 같은 위치에 만든다</li>
<li>psvm</li>
</ul>
<p>//// 문제 3 풀이</p>
<pre><code>class Main{
    public static void main(String[] args) {
    자동차 a = new 자동차();   // 기억하기 ~~~ !!!  : 클래스 객체변수 = new 클래스();
    }
}
class 자동차{

}</code></pre><p>// 문제 4 풀이</p>
<pre><code>class Main{
    public static void main(String[] args) {
    자동차 a = new 자동차();
    자동차 b = new 자동차();

    a.최고속력 = 100;
    b.최고속력 = 200;

        System.out.println(a.최고속력);
        System.out.println(b.최고속력);
    }
}
class 자동차{
    int 최고속력;
}</code></pre><p>// 문제 5 풀이</p>
<pre><code>class Main{
    public static void main(String[] args) {
    자동차 a = new 자동차();
    a.최고속력 = 100;

    a.달리다();
    a.달리다();
    a.달리다();
    }
}
class 자동차{
int 최고속력;
void 달리다(){
    System.out.println(&quot;달리다&quot;); // void () { sout }
}
}</code></pre><p>// 문제 6 객체를 사용하지 않고 만들기</p>
<pre><code>class Main{
    public static void main(String[] args) {
     플레이어 a= new 플레이어();
     a.나이=20;
     a.이름=&quot;이윤지&quot;;
     a.직업=&quot;전사&quot;;
    }

    //객체 사용 하면
    // int a_나이=20;
    // String a_이름 = &quot;이윤지&quot;;
    // String a_직업 = &quot;전사&quot;;
}
class 플레이어{
 int 나이;
 String 이름;
 String 직업;
}</code></pre><p>// 문제 7</p>
<pre><code>class Main{
    public static void main(String[] args) {
    자동차 a = new 자동차();
    자동차 b = new 자동차();
    자동차 c = new 자동차();

    a.달리다();
    b.달리다();
    c.달리다();
    }
}
class 자동차{
    void 달리다(){
        System.out.println(&quot;달리다&quot;);
    }
}</code></pre><p>// 문제 8</p>
<pre><code>class Main{
    public static void main(String[] args) {
    자동차 a = new 자동차();
    자동차 b = new 자동차();
    자동차 c = new 자동차();

   a.최고속력=100;
   b.최고속력=200;
   c.최고속력=300;

   a.달리다();
   b.달리다();
   c.달리다();

    }
}
class 자동차{
    int 최고속력;
    void 달리다(){
        System.out.println(&quot;자동차가&quot;+this.최고속력+&quot;km/h로 달립니다&quot;); // this는 생략도 가능함
    }
}</code></pre><p>//// 문제 9</p>
<pre><code>class Main{
    public static void main(String[] args) {
        자동차 a = new 자동차();
        자동차 b = new 자동차();
        자동차 c = new 자동차();

        a.최고속력=100;
        b.최고속력=200;
        c.최고속력=300;

        a.번호=1;
        b.번호=2;
        c.번호=3;

        a.달리다();
        b.달리다();
        c.달리다();

        // Q. 반복문으로 돌려서 문제를 풀 수는 없는지 ? 궁금
    }
}
class 자동차{
    int 최고속력;
    int 번호;
    void 달리다(){
        System.out.println(this.번호+&quot;번 자동차가&quot;+this.최고속력+&quot;km/h로 달립니다&quot;);

    }
}</code></pre>