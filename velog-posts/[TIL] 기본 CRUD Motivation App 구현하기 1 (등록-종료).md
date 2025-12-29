<h3 id="1-main--app-분리하기">1. Main / App 분리하기</h3>
<p><code>main</code> 메소드 안에 모든 기능을 넣으면 코드가 금방 복잡해진다.
그래서 <code>Main</code>은 <strong>시작</strong>만, 실제 로직은 <code>App</code>에서 실행하도록 분리했다.</p>
<p><code>Main</code>: 프로그램 시작 지점</p>
<p><code>App</code>: 프로그램 흐름(run) 담당</p>
<pre><code>Scanner sc = new Scanner(System.in);
new App(sc).run();
</code></pre><p>App 클래스는 Main 아래에 두는 게 아니라 새 파일로 생성했고,
run()이 시작되자마자 시작 문구가 출력되게 세팅했다.</p>
<h3 id="2-scanner-활용">2. Scanner 활용</h3>
<p>명령어를 입력받기 위해 Scanner가 필요하다.
이번 복습에서 확실히 이해한 건, Scanner는 한 번 쓰고 끝나는 값이 아니라 계속 재사용되는 입력 객체라는 점이다.</p>
<p>그래서 App에서 새로 만들지 않고 Main에서 만든 Scanner를 생성자로 전달해서 사용했다.</p>
<pre><code>public App(Scanner sc){
    this.sc = sc;
}
</code></pre><h3 id="3-명령어-입력을-반복적으로-받기-종료까지">3. 명령어 입력을 반복적으로 받기 (종료까지)</h3>
<p>명령어는 한 번만 입력받는 게 아니라 <strong>종료될 때까지 계속 입력받아야</strong> 한다.
그래서 <code>while(true)</code>로 반복을 만들고, 종료 명령어가 들어오면 <code>break</code>로 빠져나가게 했다.</p>
<pre><code>while (true) {
    System.out.print(&quot;명령어 : &quot;);
    String cmd = sc.nextLine().trim();

    if (cmd.equals(&quot;exit&quot;)) {
        System.out.println(&quot;== motivation 종료 ==&quot;);
        break;
    }
}
</code></pre><h4 id="만약-입력을-하지-않는다면-">만약 입력을 하지 않는다면 ?</h4>
<p>아무것도 입력하지 않고 엔터를 치는 경우를 처리하기 위해 <code>trim()</code> 과
조건문을 사용했다</p>
<pre><code>if (cmd.length() == 0) {
    System.out.println(&quot;명령어를 입력해주세요.&quot;);
    continue;
}
</code></pre><h3 id="4-등록-add-기능--번호-증가-구현">4. 등록 (add) 기능 + 번호 증가 구현</h3>
<h5 id="트러블슈팅🔧">트러블슈팅🔧</h5>
<p>처음에는 <code>add</code> 안에서 <code>id = 1</code>을 선언했는데, add할 때 마다
1번만 등록 되어서 왜이러지 ? 하다가
아무리 생각해도 잘 떠오르지 않아서 강의 내용을 복습해보았더니
add 안에서 id값을 설정했기 때문에 add가 실행될 때마다 id가 다시 1로 초기화 되면서 번호가 계속 1로만 나왔었다.</p>
<pre><code>if (cmd.equals(&quot;add&quot;)) {
    int id = 1; 
    id++;
}
</code></pre><h5 id="해결방법🔧">해결방법🔧</h5>
<p>lastID를 반복문 바깥에서 관리하면 된다</p>
<p>번호는 명령어가 반복되어도 유지되어야 하는 값이라
<code>run()</code> 시작 부분에 lastId를 두고 누적되게 하는 방법을 선택했다.</p>
<pre><code>int lastId = 0;

while (true) {
    System.out.print(&quot;명령어 : &quot;);
    String cmd = sc.nextLine().trim();

    if (cmd.equals(&quot;exit&quot;)) {
        System.out.println(&quot;== motivation 종료 ==&quot;);
        break;
    }

    if (cmd.equals(&quot;add&quot;)) {
        lastId++;
        System.out.println(lastId + &quot;번 motivation이 등록되었습니다.&quot;);
    }
}
</code></pre><p>이런 방식을 사용하면 번호가 정상적으로 증가한다</p>
<h4 id="느낀점">느낀점</h4>
<p>이번 moti 복습을 하면서 가장 크게 느낀 점은
코드가 동작하는 것과 구조를 이해하고 작성하는 것은 완전히 다르다는 점이었다</p>
<p>처음에는 whie은 그냥 반복문이고 break는 외워서 쓰는거고 lastId는 숫자 올리는 코드 정도로만 외워서 사용했었는데</p>
<p>직접 복습하며 구현해보니 변수 선언 위치 하나만 잘못 돼도 오류가 나고
왜 값이 유지되지 않는지 이유를 알지 못하면 결국 같은 실수를 반복하게 된다는 걸 다시 깨달았다.</p>
<p>이번 복습으로 lastId를 if문이나 while 문이 아니라 반복문 바깥부분에 둬야하는 이유를 구조적으로 이해하게 되었고, 
이제는 단순히 코드를 따라 치는 것이 아니라
변수의 역할과 유지 범위를 함께 고려하며 코드를 작성해야 한다는 마음가짐을 가지게 되었다.</p>