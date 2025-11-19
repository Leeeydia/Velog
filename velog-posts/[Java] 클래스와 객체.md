<h3 id="객체-지향-프로그래밍">객체 지향 프로그래밍</h3>
<h3 id="클래스-class">클래스 (class)</h3>
<ul>
<li><strong>클래스란?</strong></li>
<li><blockquote>
<p>객체를 생성하는데 사용되며, 객체를 만들기 위한 설계도 라고 할 수 있다. </p>
</blockquote>
</li>
<li><blockquote>
<p>객체가 가지는 속성(필드)와 동작(메서드)으로 이루어져 있으며 이들을 적절히 구성하여 객체의 동작을 정의한다.</p>
</blockquote>
</li>
<li><blockquote>
<p>java를 실행 시 클래스는 JVM 메모리의 클래스 영역에 로드된다.</p>
</blockquote>
</li>
</ul>
<ul>
<li><strong>클래스 구성요소</strong></li>
</ul>
<table>
<thead>
<tr>
<th>구성요소</th>
<th>역할</th>
</tr>
</thead>
<tbody><tr>
<td>필드</td>
<td>객체의 속성을 나타내는 변수</td>
</tr>
<tr>
<td>생성자</td>
<td>객체를 초기화하고 생성하는 특별한 메소드</td>
</tr>
<tr>
<td>메소드</td>
<td>객체의 동작을 정의하는 기능</td>
</tr>
</tbody></table>
<ul>
<li><strong>클래스 선언</strong></li>
<li><blockquote>
<p>사용하고자 하는 객체를 구상했다면, 그 객체의 대표 이름을 하나 결정하고
이 것을 클래스 이름으로 한다.</p>
</blockquote>
</li>
</ul>
<pre><code>class 클래스명{}</code></pre><ul>
<li><strong>클래스 작성 규칙</strong></li>
</ul>
<table>
<thead>
<tr>
<th>번호</th>
<th>작성규칙</th>
<th>예</th>
</tr>
</thead>
<tbody><tr>
<td>1</td>
<td>하나 이상의 문자로 이루어져야 한다.</td>
<td>Car, SportsCar</td>
</tr>
<tr>
<td>2</td>
<td>첫 번째 글자는 숫자가 올 수 없다.</td>
<td>3Car(x)</td>
</tr>
<tr>
<td>3</td>
<td>'$', '_' 이외의 특수 문자는 사용할 수 없다.</td>
<td>$Car, _Car, @Car(x), #Car(x)</td>
</tr>
<tr>
<td>4</td>
<td>자바 키워드(예약어)는 사용할 수 없다.</td>
<td>$Car, _Car, @Car(x), #Car(x)</td>
</tr>
</tbody></table>
<p>tip) 일반적으로 클래스 이름 첫 글자는 대문자로 작성한다</p>
<ul>
<li><strong>예제</strong></li>
</ul>
<pre><code>public class Cat {

    // 멤버 변수(인스턴스 변수)
    String name;
    int age;
    String color;

    // 생성자(Constructor)
    public Cat(String name, int age, String color) {
        this.name = name;
        this.age = age;
        this.color = color;
    }

    // 메서드(Method)
    public void meow() {
        System.out.println(&quot;냐옹&quot;);
    }

    // Getter 메서드
    public String getName() {
        return name;
    }

    public int getAge() {
        return age;
    }

    public String getColor() {
        return color;
    }

    // Setter 메서드
    public void setName(String name) {
        this.name = name;
    }

    public void setAge(int age) {
        this.age = age;
    }

    public void setColor(String color) {
        this.color = color;
    }
}
</code></pre><h3 id="객체">객체</h3>
<ul>
<li>클래스에 선언된 모양 그대로 생성된 실체</li>
</ul>
<p>Java 에서는 기본 원시타입이 아닌 타입의 모든 데이터를 객체(참조변수)로 인식한다</p>
<pre><code>public class Main{
public static void main(String[] args){
Iphone11; // 객체 (구현해야할 대상, 아직 구현은 안됨)
}
}</code></pre><h3 id="인스턴스">인스턴스</h3>
<ul>
<li>클래스를 담은 일종의 클래스 변수</li>
<li>설계도를 바탕으로 구현된 실체</li>
</ul>
<pre><code>public class Main{
public static void main(String[] args){
Iphone11 = new Phone();
}
}
class Phone{
}</code></pre><h4 id="클래스-vs-객체">클래스 vs 객체</h4>
<ul>
<li>클래스는 설계도, 객체는 설계도로 구현한 대상을 의미한다</li>
</ul>
<h4 id="객체-vs-인스턴스">객체 vs 인스턴스</h4>
<ul>
<li>클래스의 타입으로 선언되었을 때 객체라고 부르고, 그 객체가 메모리에 할당되어 실제 사용될 대 인스턴스라고 부른다.</li>
</ul>