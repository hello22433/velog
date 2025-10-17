<p>스프링 프레임워크를 제대로 이해하려면, <strong>IoC(Inversion of Control)</strong>와 <strong>DI(Dependency Injection)</strong> 개념은 필수입니다.
많은 면접 질문은 단순 문법보다 <strong>“왜 IoC/DI가 필요한가?”</strong>, <strong>“스프링은 어떻게 구현했는가?”</strong>를 묻습니다.</p>
<hr />
<h2 id="1️⃣-iocinversion-of-control란">1️⃣ IoC(Inversion of Control)란?</h2>
<h3 id="개념">개념</h3>
<ul>
<li><strong>제어의 역전</strong>이라는 뜻</li>
<li>기존의 객체 지향 방식에서는 객체가 필요한 의존 객체를 직접 생성합니다.</li>
<li>IoC에서는 <strong>객체가 자신이 사용할 의존 객체를 직접 만들지 않고, 외부에서 제공받는 구조</strong>를 말합니다.</li>
</ul>
<h3 id="비교-예시">비교 예시</h3>
<p><strong>전통적 방식</strong></p>
<pre><code class="language-java">public class OrderService {
    private PaymentService paymentService;

    public OrderService() {
        this.paymentService = new PaymentService(); // 직접 생성
    }
}</code></pre>
<ul>
<li>문제: 결합도가 높고, 테스트하기 어려움.</li>
</ul>
<p><strong>IoC 적용 방식 (스프링)</strong></p>
<pre><code class="language-java">@Service
public class OrderService {
    private final PaymentService paymentService;

    public OrderService(PaymentService paymentService) {
        this.paymentService = paymentService; // 외부에서 주입
    }
}</code></pre>
<ul>
<li>결합도 낮음</li>
<li>테스트 시 모킹(mocking) 가능</li>
<li>객체 관리 책임이 스프링 컨테이너로 넘어감</li>
</ul>
<hr />
<h2 id="2️⃣-didependency-injection란">2️⃣ DI(Dependency Injection)란?</h2>
<h3 id="개념-1">개념</h3>
<ul>
<li><strong>의존성 주입</strong></li>
<li>필요한 객체를 외부에서 주입받는 방법</li>
<li>IoC 개념을 실제 구현한 방식 중 하나</li>
</ul>
<h3 id="di-방식-3가지">DI 방식 3가지</h3>
<table>
<thead>
<tr>
<th>방식</th>
<th>설명</th>
<th>장단점</th>
</tr>
</thead>
<tbody><tr>
<td>생성자 주입</td>
<td>생성자에서 필요한 의존 객체를 주입</td>
<td>✅ 불변성 보장, 테스트 용이<br />❌ 순환 의존성 주의</td>
</tr>
<tr>
<td>세터 주입</td>
<td>set메서드로 의존 객체 주입</td>
<td>✅ 선택적 의존성 가능<br />❌ 객체 불변성 깨짐</td>
</tr>
<tr>
<td>필드 주입</td>
<td><code>@Autowired</code>로 직접 주입</td>
<td>✅ 코드 간결<br />❌ 테스트 어려움, 불변성 깨짐</td>
</tr>
</tbody></table>
<p><strong>추천</strong></p>
<ul>
<li>생성자 주입을 기본으로 사용</li>
</ul>
<hr />
<h3 id="면접에서-자주-나오는-질문">면접에서 자주 나오는 질문</h3>
<ol>
<li><p><strong>“IoC와 DI 차이가 뭐예요?”</strong></p>
<ul>
<li>IoC: 객체 생성·관리 주체를 개발자가 아니라 컨테이너가 맡음 → 개념</li>
<li>DI: 필요한 의존 객체를 외부에서 주입 → IoC 구현 방식</li>
</ul>
</li>
<li><p><strong>“왜 생성자 주입이 세터/필드 주입보다 좋은가?”</strong></p>
<ul>
<li>불변 객체 보장 → 런타임 안정성</li>
<li>순환 의존 발견 용이</li>
<li>테스트 용이 (mock 주입)</li>
</ul>
</li>
<li><p><strong>“스프링은 DI를 어떻게 구현하나요?”</strong></p>
<ul>
<li><strong>스프링 컨테이너(ApplicationContext)</strong>가 관리하는 Bean을 주입</li>
<li>런타임 시 리플렉션과 프록시를 활용해 생성자 또는 세터에 의존 객체 연결</li>
</ul>
</li>
</ol>
<hr />
<h2 id="3️⃣-실무-예시">3️⃣ 실무 예시</h2>
<pre><code class="language-java">@Service
public class OrderService {
    private final PaymentService paymentService;
    private final DeliveryService deliveryService;

    public OrderService(PaymentService paymentService, DeliveryService deliveryService) {
        this.paymentService = paymentService;
        this.deliveryService = deliveryService;
    }

    public void createOrder(Order order) {
        paymentService.pay(order);
        deliveryService.deliver(order);
    }
}</code></pre>
<ul>
<li><strong>테스트 시 모킹</strong></li>
</ul>
<pre><code class="language-java">OrderService orderService = new OrderService(mock(PaymentService.class), mock(DeliveryService.class));</code></pre>
<ul>
<li>실제 객체 대신 mock 객체를 주입하여 단위 테스트 가능</li>
</ul>
<hr />
<h2 id="4️⃣-요약">4️⃣ 요약</h2>
<ul>
<li><strong>IoC</strong>: 제어권을 스프링 컨테이너로 넘기는 것</li>
<li><strong>DI</strong>: 의존 객체를 외부에서 주입하는 것</li>
<li>생성자 주입을 기본으로 사용 → 테스트 용이, 결합도 낮음</li>
<li>면접에서는 “왜 IoC/DI를 쓰는가?” → “결합도 감소, 테스트 용이, 유지보수 편리” 정도로 답변하면 충분</li>
</ul>
<hr />