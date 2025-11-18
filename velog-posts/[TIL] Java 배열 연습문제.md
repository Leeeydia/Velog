<h3 id="배열을-이용한-java-문제풀이">배열을 이용한 Java 문제풀이</h3>
<p>// 문제 1 arr1 변수에 t f t 연결</p>
<pre><code>boolean[] arr1 = new boolean[3];
        arr1[0] = true;
        arr1[1] = false;
        arr1[2] = true;

        System.out.println(arr1[0]);
        System.out.println(arr1[1]);
        System.out.println(arr1[2]);</code></pre><p>boolean -&gt; T/F 값만 담을 수 있는 타입
arr1 변수에 
타입[] 변수명 = new 타입[] 객체를 만들고 연결
값을 넣으려면 arr 0번째 1번째 <del>~</del> 자리에  T F T 를 넣어야 순서대로 들어감
그 다음 출력하기 </p>
<pre><code>boolean[] arr1 = new boolean[3];
        arr1[0] = true;
        arr1[1] = false;
        arr1[2] = true;</code></pre><p>배열의 개수보다 담는 공간이 큰거는 됨 (new boolean[])
3개의 공간 뚫어놓고 배열은 arr1[3<del>~</del>] 하면 오류남</p>
<p>// 개별적인 객체가 추가로 만들어진다면 ? - 추가 설명 필요</p>
<pre><code>boolean[] arr1 = new noolean [4]
        System.out.println(arr1[0]);
        System.out.println(arr1[1]);
        System.out.println(arr1[2]);</code></pre><p>새로운 객체와 연결 된다 FFF </p>
<p>// 문제 2 arr2 변수에 [3.14, 7.77, 11.11] 연결</p>
<pre><code>double[] arr2 = new double[3];
        arr2[0] = 3.14;
        arr2[1] = 7.77;
        arr2[2] = 11.11;

        System.out.println(arr2[0]);
        System.out.println(arr2[1]);
        System.out.println(arr2[2]);
</code></pre><p>double[] arr2 = {3.14, 7.77, 11.11}
라고 써도 됨!</p>
<p>// 문제 3 arr3 변수에 [1~10] 연결</p>
<pre><code>  int[] arr3 = new int[10];
        for(int i =0; i&lt;arr3.length; i++){
            arr3[i] = i+1;
            System.out.println(&quot;arr3[&quot; + i +&quot;]&quot; +&quot;=&quot; + (i+1));
        }</code></pre><p>단독으로 new int[10] 은 안됨 -&gt; 활용 불가
int[] arr3 = new int [10]; -&gt; 가능</p>
<p>반복문 사용하면 간단</p>