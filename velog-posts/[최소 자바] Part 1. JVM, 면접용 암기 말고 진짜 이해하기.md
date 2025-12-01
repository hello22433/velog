<blockquote>
<p><strong>&quot;백엔드 개발자가 알아야 하는 최소한의 자바&quot;</strong> 시리즈의 두 번째 글입니다.</p>
<p>오늘은 자바 코드가 실행되는 무대, JVM과 메모리 구조를 &quot;실무적 관점&quot;에서 파헤쳐 봅니다.</p>
</blockquote>
<hr />
<h3 id="jvm-구조-면접-때-외우고-다-까먹으셨죠">&quot;JVM 구조, 면접 때 외우고 다 까먹으셨죠?&quot;</h3>
<p>신입 개발자 면접 단골 질문입니다.
*&quot;JVM의 메모리 구조(Runtime Data Area)에 대해 설명해 보세요.&quot;*</p>
<p>대부분 이렇게 대답합니다.
*&quot;메소드 영역, 힙, 스택, PC 레지스터, 네이티브 메소드 스택이 있고요... 힙에는 객체가 저장되고 스택에는 지역변수가...&quot;*</p>
<p>정답입니다. 하지만 백엔드 개발자라면 여기서 한 발 더 나아가야 합니다. 우리가 이 지루한 이론을 알아야 하는 진짜 이유는 <strong>&quot;내 서버가 왜 터지는지&quot;, &quot;내 코드가 왜 동시성 이슈를 일으키는지&quot;</strong> 알기 위해서입니다.</p>
<p>오늘은 교과서적인 정의 대신, <strong>코드가 메모리에 올라갈 때 벌어지는 일</strong>을 중심으로 JVM을 뜯어보겠습니다.</p>
<br />

<h2 id="1-jvm-자바라는-프로그램의-운영체제">1. JVM: 자바라는 프로그램의 운영체제</h2>
<p>우리가 작성한 <code>.java</code> 파일은 사람의 언어입니다. 컴퓨터(CPU)는 이걸 모릅니다. 그래서 <code>javac</code> 컴파일러가 이를 바이트코드(<code>.class</code>)로 바꿉니다. 이 바이트코드를 읽어서 실행하는 녀석이 바로 **JVM(Java Virtual Machine)**입니다.</p>
<p>여기서 기억할 키워드는 하나입니다. <strong>JIT 컴파일러(Just-In-Time Compiler)</strong>.</p>
<h3 id="🚀-인터프리터-vs-jit">🚀 인터프리터 vs JIT</h3>
<p>초기 자바는 바이트코드를 한 줄씩 읽어서 실행(인터프리터)했기 때문에 C언어보다 압도적으로 느렸습니다. 하지만 JIT 컴파일러가 도입되면서 상황이 바뀌었습니다.</p>
<ul>
<li><strong>반복되는 코드 감지:</strong> JVM은 코드를 실행하다가 &quot;어? 이 메소드가 엄청 자주 호출되네?&quot;(Hotspot)라고 판단하면,</li>
<li><strong>기계어로 변환:</strong> 해당 부분을 통째로 기계어(Native Code)로 컴파일해서 캐싱해둡니다.</li>
</ul>
<p><strong>[실무 Tip] Warm-up의 중요성</strong>
서버를 막 띄웠을 때는 JIT 컴파일이 덜 된 상태라 응답 속도가 느릴 수 있습니다. 대규모 트래픽을 받는 서비스라면 배포 직후 인위적으로 트래픽을 흘려보내 JIT 컴파일러를 예열(Warm-up)시키는 과정을 거치기도 합니다.</p>
<br />

<h2 id="2-메모리-구조-runtime-data-area--스레드의-전쟁터">2. 메모리 구조 (Runtime Data Area) : 스레드의 전쟁터</h2>
<p>백엔드 개발자에게 가장 중요한 건 **&quot;어떤 데이터가 공유되고, 어떤 데이터가 공유되지 않는가&quot;**입니다. 이것만 구분해도 동시성 이슈의 80%는 예방할 수 있습니다.</p>
<h3 id="🏠-method-area-메소드-영역--설계도-보관소">🏠 Method Area (메소드 영역) = 설계도 보관소</h3>
<ul>
<li><strong>저장 데이터:</strong> 클래스 정보, <code>static</code> 변수.</li>
<li><strong>특징:</strong> 프로그램 시작부터 종료까지 메모리에 남습니다. 모든 스레드가 공유합니다.</li>
<li><strong>주의점:</strong> 여기에 데이터를 함부로 담으면(Global State), 모든 사용자가 그 값을 공유하게 됩니다. <strong><code>static</code> 리스트에 데이터를 계속 담다가 <code>OutOfMemoryError</code>가 터지는 경우가 흔합니다.</strong></li>
</ul>
<h3 id="🏟-heap-힙-영역--객체들의-공터">🏟 Heap (힙 영역) = 객체들의 공터</h3>
<ul>
<li><strong>저장 데이터:</strong> <code>new</code>로 생성된 모든 객체와 배열 (<code>new Member()</code>, <code>new ArrayList()</code>).</li>
<li><strong>특징:</strong> <strong>모든 스레드가 공유합니다.</strong> 이게 핵심입니다.</li>
<li><strong>위험성:</strong> A 스레드가 힙에 있는 객체의 값을 바꾸고 있는데, B 스레드가 와서 그 값을 읽으면? -&gt; <strong>동시성 문제 발생!</strong></li>
</ul>
<h3 id="📚-stack-스택-영역--스레드만의-작업실">📚 Stack (스택 영역) = 스레드만의 작업실</h3>
<ul>
<li><strong>저장 데이터:</strong> 메소드 호출 시 생성되는 지역변수, 매개변수.</li>
<li><strong>특징:</strong> <strong>각 스레드마다 별도로 생성됩니다.</strong> 다른 스레드는 절대 침범할 수 없습니다.</li>
<li><strong>안전성:</strong> 메소드 안에서 선언한 <code>int count = 0;</code> 같은 변수는 동시성 문제에서 100% 안전합니다.</li>
</ul>
<p><img alt="" src="https://velog.velcdn.com/images/hello22433/post/c091b3d2-93ea-4283-b24b-33ae72eac8cd/image.png" /></p>
<br />

<h2 id="3-그림으로-보는-코드의-흐름">3. 그림으로 보는 코드의 흐름</h2>
<p>코드를 보며 메모리 움직임을 상상해 봅시다.</p>
<pre><code class="language-java">public class OrderService {
    // 1. Method Area (static 변수)
    private static final double DISCOUNT_RATE = 0.1;

    public void order(User user) {
        // 2. Stack (지역변수 price)
        int price = 10000;

        // 3. Heap (객체 생성)
        Order order = new Order(user, price);

        save(order);
    }
}</code></pre>
<ol>
<li><strong><code>DISCOUNT_RATE</code></strong>: 프로그램 시작 시 <strong>Method Area</strong>에 딱 하나 생성됩니다.</li>
<li><strong><code>order()</code> 호출</strong>: 요청이 들어와서 스레드가 하나 생성되면, 이 스레드를 위한 <strong>Stack</strong> 공간이 생깁니다.</li>
<li><strong><code>int price</code></strong>: <strong>Stack</strong>에 저장됩니다. 요청이 100개 들어와서 스레드가 100개 생겨도, <code>price</code> 변수는 각자의 스레드 스택에 100개 따로 생깁니다. (서로 간섭 불가)</li>
<li><strong><code>new Order(...)</code></strong>: 실제 주문 객체 데이터는 <strong>Heap</strong>에 저장됩니다. 스택에는 그 힙 영역의 주소값(참조값)만 저장됩니다.</li>
</ol>
<p><strong>🤔 잠깐, 그럼 Heap에 있는 객체는 위험하다면서요?</strong>
맞습니다. 그래서 스프링 빈(Spring Bean)처럼 힙에 딱 하나만 떠 있는(싱글톤) 객체의 **필드(멤버 변수)**를 상태 값으로 쓰면 안 됩니다.</p>
<pre><code class="language-java">// ❌ 최악의 코드 예시
@Service
public class BadService {
    private int tempResult; // (위험) 힙에 있는 객체의 필드. 모든 스레드가 공유함.

    public void logic() {
        tempResult++; // A사용자와 B사용자의 요청이 섞여버림
    }
}</code></pre>
<br />

<h2 id="4-garbage-collection-gc--조물주의-청소부">4. Garbage Collection (GC) : 조물주의 청소부</h2>
<p>C나 C++에서는 <code>malloc</code>으로 메모리를 잡고 <code>free</code>로 해제해야 했습니다. 자바는 개발자가 메모리 해제를 하지 않습니다. **Garbage Collector(GC)**가 있으니까요.</p>
<h3 id="gc의-대원칙-reachability-도달-가능성">GC의 대원칙: Reachability (도달 가능성)</h3>
<p>GC는 단순합니다. **&quot;Root Set(스택의 변수 등)에서 참조가 끊긴 객체는 쓰레기다&quot;**라고 판단하고 지웁니다.</p>
<pre><code class="language-java">public void test() {
    Member m = new Member(&quot;userA&quot;); // (1) 힙에 객체 생성, 스택의 m이 참조
    m = null; // (2) 참조 끊김. userA 객체는 이제 GC 대상(Unreachable)
}</code></pre>
<h3 id="stop-the-world-stw">Stop-the-world (STW)</h3>
<p>GC가 청소를 하려면 잠깐 세상(애플리케이션)을 멈춰야 합니다. 이걸 <strong>Stop-the-world</strong>라고 합니다.</p>
<ul>
<li>GC가 돌면 모든 스레드가 멈춥니다. (서버가 순간 멈칫!)</li>
<li>GC 튜닝이란 이 멈추는 시간을 줄이는 것입니다. (하지만 요즘은 하드웨어가 좋아서, 대용량 트래픽이 아니면 기본 설정으로도 충분합니다.)</li>
</ul>
<h3 id="young-gen-vs-old-gen">Young Gen vs Old Gen</h3>
<p>대부분의 객체는 금방 쓰레기가 됩니다(메소드가 끝나면 사라지니까요).</p>
<ul>
<li><strong>Young Generation:</strong> 금방 사라질 객체들이 모이는 곳. 여기서 일어나는 GC(Minor GC)는 아주 빠릅니다.</li>
<li><strong>Old Generation:</strong> Young에서 오래 살아남은 객체가 이동하는 곳. 여기가 꽉 차서 일어나는 GC(Major GC)는 시간이 오래 걸리고 STW도 깁니다.</li>
</ul>
<p><strong>[실무 Tip] 메모리 누수(Memory Leak) 패턴</strong>
자바에도 메모리 누수가 있습니다. GC가 &quot;이건 쓰는 객체야&quot;라고 착각하게 만들면 됩니다.</p>
<ol>
<li><strong>Static Collection:</strong> <code>static List&lt;User&gt; users = new ArrayList&lt;&gt;();</code> 여기에 계속 데이터를 넣고 비우지 않으면? 앱이 꺼질 때까지 메모리를 잡아먹습니다.</li>
<li><strong>Unclosed Resource:</strong> DB 커넥션이나 IO 스트림을 <code>close()</code> 하지 않으면 메모리를 계속 점유할 수 있습니다. (그래서 <code>try-with-resources</code>를 써야 합니다.)</li>
</ol>
<br />

<h2 id="5-마치며-이것만은-꼭-기억하자">5. 마치며: 이것만은 꼭 기억하자</h2>
<p>복잡한 튜닝 기술보다 중요한 건 기본 원리입니다.</p>
<ol>
<li><strong>스택은 안전하고 힙은 불안하다.</strong><ul>
<li>지역변수(Stack)는 맘껏 써도 됩니다.</li>
<li>멤버변수(Heap)를 쓸 때는 &quot;여러 스레드가 동시에 접근하면?&quot;을 항상 고민하세요.</li>
</ul>
</li>
<li><strong>Static은 신중하게 쓰자.</strong><ul>
<li>편하다고 막 쓰면 메모리 낭비와 동시성 이슈의 주범이 됩니다.</li>
</ul>
</li>
<li><strong>GC를 믿되 방관하지 말자.</strong><ul>
<li>사용하지 않는 객체 참조는 끊어주는 습관, 리소스 반환(<code>close</code>)을 철저히 하는 습관이 서버를 살립니다.</li>
</ul>
</li>
</ol>
<p>다음 시간에는 <strong>[Part 2] 객체지향(OOP)과 스프링의 연결고리</strong>에 대해 알아봅니다. 붕어빵 틀 비유를 넘어, 왜 우리는 인터페이스를 쓰고 왜 빈을 싱글톤으로 관리하는지 파헤쳐 보겠습니다.</p>
<blockquote>
<p><strong>Next:</strong> [Part 2] 스프링이 사랑한 객체지향, 그리고 디자인 패턴</p>
</blockquote>