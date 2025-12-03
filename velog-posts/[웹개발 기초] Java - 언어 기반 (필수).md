<h1 id="java-언어-기반-핵심-개념-정리">Java 언어 기반 핵심 개념 정리</h1>
<p>자바 기반의 애플리케이션 개발을 시작하려면, 언어 문법과 핵심 라이브러리 이해가 필수적이다. 이 글에서는 실제 프로젝트에서 반드시 사용하는 개념만 선별해 정리했다.</p>
<hr />
<h2 id="1-java-문법과-핵심-라이브러리">1. Java 문법과 핵심 라이브러리</h2>
<h3 id="11-컬렉션collection">1.1 컬렉션(Collection)</h3>
<p>컬렉션은 데이터를 효율적으로 저장하고 처리하기 위한 자료구조 모음이다.</p>
<ul>
<li><strong>List</strong>: 순서 O, 중복 O (ArrayList, LinkedList)</li>
<li><strong>Set</strong>: 순서 X, 중복 X (HashSet, LinkedHashSet)</li>
<li><strong>Map</strong>: key-value 형태 (HashMap, LinkedHashMap)</li>
</ul>
<p>실무에서 가장 많이 사용하는 조합은 ArrayList와 HashMap이다.</p>
<h3 id="12-제네릭generics">1.2 제네릭(Generics)</h3>
<p>타입 안정성을 확보하기 위한 기능이다.</p>
<pre><code class="language-java">List&lt;String&gt; names = new ArrayList&lt;&gt;();</code></pre>
<p>제네릭을 사용하면 컴파일 시점에 타입 오류를 방지할 수 있다.</p>
<h3 id="13-람다lambda와-스트림stream">1.3 람다(Lambda)와 스트림(Stream)</h3>
<p>Java 8 이후 자바가 함수형 스타일을 지원하면서 데이터 처리 방식이 크게 변화했다.</p>
<ul>
<li><strong>람다</strong>: 간단한 함수 표현식</li>
<li><strong>스트림</strong>: 데이터 흐름 기반 처리. 필터링, 매핑, 집계 등을 선언형으로 작성한다.</li>
</ul>
<p>예시:</p>
<pre><code class="language-java">List&lt;String&gt; result = names.stream()
        .filter(n -&gt; n.startsWith(&quot;A&quot;))
        .toList();</code></pre>
<h3 id="14-optional">1.4 Optional</h3>
<p>NullPointerException을 줄이기 위한 컨테이너 객체다.</p>
<pre><code class="language-java">Optional&lt;String&gt; name = Optional.ofNullable(user.getName());</code></pre>
<p>null을 직접 다루지 않고 안전한 API를 사용할 수 있다.</p>
<h3 id="15-datetime-api">1.5 Date/Time API</h3>
<p>Java 8의 <code>java.time</code> 패키지는 불변 객체이며, 날짜·시간 처리에 매우 안정적이다.</p>
<ul>
<li>LocalDate</li>
<li>LocalDateTime</li>
<li>ZonedDateTime</li>
<li>Duration / Period</li>
</ul>
<hr />
<h2 id="2-예외-처리exception">2. 예외 처리(Exception)</h2>
<h3 id="21-checked-vs-unchecked">2.1 Checked vs Unchecked</h3>
<ul>
<li><strong>Checked Exception</strong>: 반드시 try-catch 또는 throws 필요 (IOException)</li>
<li><strong>Unchecked Exception</strong>: RuntimeException 계열. 선택적 처리 (NullPointerException, IllegalArgumentException)</li>
</ul>
<p>일반적으로 비즈니스 로직에서는 Unchecked Exception을 많이 사용한다.</p>
<h3 id="22-좋은-예외-처리-원칙">2.2 좋은 예외 처리 원칙</h3>
<ul>
<li>예외는 흐름 제어용이 아니라 <strong>예외 상황</strong> 처리에만 사용해야 한다.</li>
<li>의미 있는 메시지를 포함해야 한다.</li>
<li>가능한 한 상위 레이어에서 핸들링한다.</li>
</ul>
<hr />
<h2 id="3-쓰레드thread-기초">3. 쓰레드(Thread) 기초</h2>
<p>자바는 병렬 처리를 위해 Thread를 지원한다.</p>
<h3 id="31-thread-생성-방식">3.1 Thread 생성 방식</h3>
<ul>
<li>Thread 클래스 상속</li>
<li>Runnable 구현</li>
<li>ExecutorService 활용 (실무에서 가장 많이 사용)</li>
</ul>
<p>Future, CompletableFuture는 비동기 로직 구현에 매우 유용하다.</p>
<hr />
<h2 id="4-객체지향-프로그래밍oop">4. 객체지향 프로그래밍(OOP)</h2>
<h3 id="41-캡슐화encapsulation">4.1 캡슐화(Encapsulation)</h3>
<p>데이터를 숨기고 메서드를 통해서만 접근하게 하는 방식이다.
클래스 내부 상태를 보호하는 것이 핵심이다.</p>
<h3 id="42-상속inheritance">4.2 상속(Inheritance)</h3>
<p>부모 클래스의 속성과 기능을 자식 클래스가 물려받는다.
중복을 줄이는 데 유리하지만 과도한 상속은 지양해야 한다.</p>
<h3 id="43-다형성polymorphism">4.3 다형성(Polymorphism)</h3>
<p>같은 메서드 호출이지만, 실제 구현은 객체 타입에 따라 달라진다.</p>
<pre><code class="language-java">Animal a = new Dog();
a.sound();  // Dog의 sound() 실행</code></pre>
<p>인터페이스 기반 설계의 핵심 개념이다.</p>
<h3 id="44-solid-원칙-핵심-요약">4.4 SOLID 원칙 핵심 요약</h3>
<ul>
<li><strong>S</strong>: 단일 책임 원칙</li>
<li><strong>O</strong>: 개방-폐쇄 원칙</li>
<li><strong>L</strong>: 리스코프 치환 원칙</li>
<li><strong>I</strong>: 인터페이스 분리 원칙</li>
<li><strong>D</strong>: 의존성 역전 원칙</li>
</ul>
<p>모듈 간 결합도를 낮추고 유지보수성을 높이기 위한 설계 원칙이다.</p>
<hr />
<h2 id="5-디자인-패턴-기초">5. 디자인 패턴 기초</h2>
<h3 id="51-싱글톤singleton">5.1 싱글톤(Singleton)</h3>
<p>애플리케이션에서 단 하나의 인스턴스만 존재해야 할 때 사용한다.
대표적으로 Spring의 빈(Bean)이 싱글톤 범위를 기본으로 가진다.</p>
<h3 id="52-팩토리-패턴factory-method">5.2 팩토리 패턴(Factory Method)</h3>
<p>객체 생성 로직을 분리해, 클라이언트 코드가 구체 클래스를 몰라도 되도록 만든 패턴이다.</p>
<pre><code class="language-java">interface CarFactory {
    Car createCar();
}</code></pre>
<h3 id="53-전략-패턴strategy-pattern">5.3 전략 패턴(Strategy Pattern)</h3>
<p>변하는 로직을 인터페이스로 분리하고, 실행 시 전략을 바꿔 적용하는 패턴이다.</p>
<p>비즈니스 규칙이 자주 바뀌는 경우 유용하다.</p>
<pre><code class="language-java">interface DiscountStrategy {
    int discount(int price);
}</code></pre>
<p>전략 객체 교체만으로 행위 변경이 가능해진다.</p>
<h3 id="54-템플릿-메서드-패턴template-method">5.4 템플릿 메서드 패턴(Template Method)</h3>
<p>공통 흐름은 유지하고, 특정 단계만 하위 클래스에서 재정의하는 패턴이다.</p>
<p>JdbcTemplate이나 AbstractController 구조가 대표적인 예다.</p>
<hr />
<h2 id="6-실습-가이드-간단한-예시">6. 실습 가이드 (간단한 예시)</h2>
<h3 id="61-스트림으로-데이터-처리">6.1 스트림으로 데이터 처리</h3>
<pre><code class="language-java">List&lt;Integer&gt; nums = List.of(1, 2, 3, 4, 5);

int sum = nums.stream()
              .filter(n -&gt; n &gt; 2)
              .mapToInt(n -&gt; n * 2)
              .sum();</code></pre>
<h3 id="62-전략-패턴으로-할인-규칙-분리">6.2 전략 패턴으로 할인 규칙 분리</h3>
<pre><code class="language-java">public class PriceCalculator {
    private DiscountStrategy strategy;

    public PriceCalculator(DiscountStrategy strategy) {
        this.strategy = strategy;
    }

    public int calculate(int price) {
        return strategy.discount(price);
    }
}</code></pre>
<p>전략만 바꿔서 다른 할인 정책을 적용할 수 있다.</p>
<hr />
<h1 id="마무리">마무리</h1>
<p>자바 개발자가 되기 위한 가장 기초적인 내용은 문법보다도 “객체지향과 설계 감각”이다.
위 개념들을 이해하면 스프링, JPA, MSA로 확장할 때 큰 도움을 받을 수 있다.</p>