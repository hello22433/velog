<h1 id="ddd와-헥사고날-아키텍처-연결-단계별-기준">DDD와 헥사고날 아키텍처 연결: 단계별 기준</h1>
<p>핵심 아이디어:</p>
<blockquote>
<p><strong>“도메인을 중심(Core)으로 두고, 외부 시스템(DB, UI, 메시징 등)은 포트/어댑터로 분리한다”</strong></p>
</blockquote>
<p>이 기준을 바탕으로 DDD 단계를 나누면 자연스럽게 헥사고날 구조가 됩니다.</p>
<hr />
<h2 id="예시-서비스-온라인-쇼핑몰">예시 서비스: 온라인 쇼핑몰</h2>
<ul>
<li><strong>사용자(User)</strong>가 상품(Product)을 <strong>주문(Order)</strong> 하고, 결제(Payment)를 진행하는 서비스</li>
</ul>
<hr />
<h2 id="1단계-domain-객체-엔티티--밸류-객체-→-core">1단계: <strong>Domain 객체 (엔티티 + 밸류 객체)</strong> → <strong>Core</strong></h2>
<p><strong>기준:</strong></p>
<ul>
<li><strong>비즈니스의 핵심 규칙과 상태</strong>를 담당하는 객체</li>
<li>DB, UI, 외부 API에 전혀 의존하지 않음</li>
</ul>
<p><strong>예시:</strong></p>
<ul>
<li><strong>엔티티(Entity)</strong>: <code>Order</code> (주문), <code>User</code> (회원)</li>
<li><strong>밸류 객체(Value Object)</strong>: <code>OrderItem</code> (주문 상품), <code>Money</code> (금액), <code>Address</code> (배송지)</li>
</ul>
<p><strong>왜 나누었나?</strong></p>
<ul>
<li>주문 생성, 상품 추가, 결제 상태 변경 등 핵심 비즈니스 로직은 <strong>Core 안에서만 처리</strong>해야 유지보수가 쉽고 테스트도 쉬워집니다.</li>
</ul>
<hr />
<h2 id="2단계-repository-인터페이스-→-port">2단계: <strong>Repository 인터페이스</strong> → <strong>Port</strong></h2>
<p><strong>기준:</strong></p>
<ul>
<li>도메인(Core)와 외부 시스템(DB, 캐시, 메시지 큐)을 연결하는 <strong>추상화 계층</strong></li>
<li>인터페이스만 정의, 실제 구현은 나중에 교체 가능</li>
</ul>
<p><strong>예시:</strong></p>
<ul>
<li><p><code>OrderRepository</code></p>
<ul>
<li><code>save(Order order)</code></li>
<li><code>findById(String orderId)</code></li>
</ul>
</li>
</ul>
<p><strong>왜 나누었나?</strong></p>
<ul>
<li>Core 도메인은 DB 기술(MySQL, MongoDB)에 의존하지 않아야 함</li>
<li>테스트나 기술 변경 시 인터페이스만 구현체로 바꾸면 됨</li>
</ul>
<hr />
<h2 id="3단계-repository-구현체-→-adapter">3단계: <strong>Repository 구현체</strong> → <strong>Adapter</strong></h2>
<p><strong>기준:</strong></p>
<ul>
<li>Repository 인터페이스를 실제로 구현한 클래스</li>
<li>DB, 파일, 외부 API 등 기술적 구현 담당</li>
</ul>
<p><strong>예시:</strong></p>
<ul>
<li><code>InMemoryOrderRepository</code> → 메모리에 저장</li>
<li>나중에 <code>MySQLOrderRepository</code>로 쉽게 교체 가능</li>
</ul>
<p><strong>왜 나누었나?</strong></p>
<ul>
<li>Core 도메인과 기술적 구현을 분리하면, DB를 바꾸더라도 Core 로직은 <strong>영향 없음</strong></li>
</ul>
<hr />
<h2 id="4단계-domain-service-→-core">4단계: <strong>Domain Service</strong> → <strong>Core</strong></h2>
<p><strong>기준:</strong></p>
<ul>
<li>엔티티와 밸류 객체가 처리할 수 없는 <strong>비즈니스 로직</strong> 담당</li>
<li>여러 엔티티/밸류 객체를 조합해서 규칙을 처리</li>
</ul>
<p><strong>예시:</strong></p>
<ul>
<li><p><code>OrderService</code></p>
<ul>
<li>주문 생성(createOrder)</li>
<li>상품 추가(addItemToOrder)</li>
<li>결제(payOrder)</li>
</ul>
</li>
</ul>
<p><strong>왜 나누었나?</strong></p>
<ul>
<li>엔티티 단일 객체만으로는 <strong>주문 생성 → 상품 추가 → 결제</strong>와 같은 복잡한 흐름을 처리하기 어려움</li>
<li>서비스 단에서 비즈니스 로직을 분리하면 Core 도메인을 깔끔하게 유지 가능</li>
</ul>
<hr />
<h2 id="5단계-ui-외부-api-메시지-큐-→-adapter">5단계: <strong>UI, 외부 API, 메시지 큐</strong> → <strong>Adapter</strong></h2>
<p><strong>기준:</strong></p>
<ul>
<li>Core와 통신하는 외부 시스템</li>
<li>도메인을 직접 건드리지 않고 포트를 통해 호출</li>
</ul>
<p><strong>예시:</strong></p>
<ul>
<li>REST API 컨트롤러 → OrderService 호출</li>
<li>결제 게이트웨이 → PaymentService 포트 호출</li>
</ul>
<p><strong>왜 나누었나?</strong></p>
<ul>
<li>Core 도메인과 외부 기술을 분리하여 <strong>유연한 확장과 테스트 용이성</strong> 확보</li>
</ul>
<hr />
<h2 id="단계별-정리-기준과-헥사고날-대응">단계별 정리: 기준과 헥사고날 대응</h2>
<table>
<thead>
<tr>
<th>단계</th>
<th>DDD 개념</th>
<th>헥사고날 대응</th>
<th>기준/목적</th>
<th>쉬운 예시</th>
</tr>
</thead>
<tbody><tr>
<td>1</td>
<td>Domain 객체</td>
<td>Core</td>
<td>핵심 비즈니스 로직, 상태 관리</td>
<td><code>Order</code>, <code>OrderItem</code>, <code>Money</code></td>
</tr>
<tr>
<td>2</td>
<td>Repository 인터페이스</td>
<td>Port</td>
<td>외부 시스템과 연결할 인터페이스</td>
<td><code>OrderRepository</code></td>
</tr>
<tr>
<td>3</td>
<td>Repository 구현체</td>
<td>Adapter</td>
<td>실제 DB/외부 기술 구현</td>
<td><code>InMemoryOrderRepository</code></td>
</tr>
<tr>
<td>4</td>
<td>Domain Service</td>
<td>Core</td>
<td>복잡한 비즈니스 로직 처리</td>
<td><code>OrderService</code>(주문 생성+상품 추가+결제)</td>
</tr>
<tr>
<td>5</td>
<td>UI / 외부 API</td>
<td>Adapter</td>
<td>Core와 외부 시스템 연결</td>
<td>REST API 컨트롤러, 결제 게이트웨이</td>
</tr>
</tbody></table>
<hr />
<h3 id="✅-핵심-포인트">✅ 핵심 포인트</h3>
<ol>
<li><strong>Core = 도메인 객체 + 도메인 서비스</strong> → 비즈니스 중심</li>
<li><strong>Port = Repository 인터페이스</strong> → 외부 기술 의존 추상화</li>
<li><strong>Adapter = 구현체 + 외부 시스템</strong> → 기술 구현 담당</li>
<li>단계 나눔 기준 = <strong>“비즈니스 로직(Core) vs 외부 기술(Adapter)” + “단일 객체 vs 서비스”</strong></li>
</ol>
<hr />
<p>즉, <strong>DDD의 단계 구분 = 헥사고날 구조를 자연스럽게 따른 것</strong>이라고 이해하면 쉽습니다.</p>
<hr />