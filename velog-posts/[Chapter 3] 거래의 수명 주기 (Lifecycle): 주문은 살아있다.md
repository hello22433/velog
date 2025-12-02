<p><strong>&quot;주문은 단순한 데이터가 아니라, 상태를 가진 객체(Stateful Object)다&quot;</strong>라는 관점을 심어주는 챕터입니다.</p>
<p>많은 주니어 개발자들이 주문 로직을 짤 때 수많은 <code>if-else</code>문의 늪에 빠지곤 합니다. 이를 깔끔하게 해결하는 <strong>상태 전이(State Transition)</strong>의 개념과 객체지향적인 구현 방법을 다룹니다.</p>
<hr />
<h1 id="chapter-3-거래의-수명-주기-lifecycle-주문은-살아있다">[Chapter 3] 거래의 수명 주기 (Lifecycle): 주문은 살아있다</h1>
<blockquote>
<p><strong>&quot;주문 확인(Insert)이 끝이 아닙니다. 주문은 접수되는 순간부터 끊임없이 자신의 상태를 바꿉니다.&quot;</strong></p>
</blockquote>
<h2 id="1-주문의-일생-the-life-of-an-order">1. 주문의 일생 (The Life of an Order)</h2>
<p>우리가 배달 앱에서 치킨을 시킬 때 <code>주문 접수</code> -&gt; <code>조리 중</code> -&gt; <code>배달 중</code> -&gt; <code>배달 완료</code>의 과정을 거치듯, 주식이나 코인 주문도 명확한 생명 주기를 가집니다.</p>
<p>개발자는 이 흐름을 명확하게 정의하지 않으면, <strong>&quot;이미 취소된 주문이 체결되는&quot;</strong> 끔찍한 버그를 마주하게 됩니다.</p>
<h3 id="상태-정의-enum">상태 정의 (Enum)</h3>
<p>가장 먼저 할 일은 주문이 가질 수 있는 모든 상태를 열거형(Enum)으로 정의하는 것입니다.</p>
<pre><code class="language-java">public enum OrderStatus {
    PENDING,          // 1. 접수 대기 (아직 호가창 진입 전, 검증 단계)
    OPEN,             // 2. 미체결 (호가창에 등록됨, 매칭 대기)
    PARTIALLY_FILLED, // 3. 부분 체결 (일부만 사짐/팔림)
    FILLED,           // 4. 완전 체결 (모두 처리됨 - 종료 상태)
    CANCELLED         // 5. 주문 취소 (사용자 요청 - 종료 상태)
}</code></pre>
<p>여기서 중요한 점은 <code>PARTIALLY_FILLED</code>(부분 체결)입니다. 10개를 주문했는데 3개만 체결되고 7개가 남은 상황입니다. 이 상태를 누락하면 잔고 계산이 꼬이게 됩니다.</p>
<h2 id="2-나쁜-코드-vs-좋은-코드">2. 나쁜 코드 vs 좋은 코드</h2>
<p>주문의 상태를 변경할 때 최악의 코드는, 서비스 레이어(Service Layer) 여기저기서 <code>setter</code>를 남발하며 상태를 바꾸는 것입니다.</p>
<h3 id="💀-나쁜-코드-절차지향적이고-위험함">💀 나쁜 코드: 절차지향적이고 위험함</h3>
<pre><code class="language-java">// OrderService.java
public void matchOrder(Order order, BigDecimal quantity) {
    // 외부에서 주문 객체의 속을 다 까뒤집고 있음
    if (order.getStatus() == OrderStatus.OPEN) {
        order.setMatchedQuantity(order.getMatchedQuantity().add(quantity));
        if (order.getMatchedQuantity().compareTo(order.getTotalQuantity()) == 0) {
            order.setStatus(OrderStatus.FILLED); // 여기서 실수로 빼먹으면?
        } else {
            order.setStatus(OrderStatus.PARTIALLY_FILLED);
        }
    }
}</code></pre>
<p>이 방식은 코드가 흩어져 있어, 상태 변경 규칙이 바뀌면 모든 서비스 코드를 뒤져서 고쳐야 합니다.</p>
<h3 id="✨-좋은-코드-도메인-주도-설계-ddd--상태-캡슐화">✨ 좋은 코드: 도메인 주도 설계 (DDD) &amp; 상태 캡슐화</h3>
<p>주문의 상태를 판단하고 변경하는 로직은 <strong>주문 객체 자신</strong>이 가지고 있어야 합니다. 이것이 객체지향입니다.</p>
<pre><code class="language-java">// Order.java (Domain Entity)
public class Order {
    private OrderStatus status;
    private BigDecimal totalQuantity;
    private BigDecimal remainingQuantity;

    // 핵심: 외부에서는 이 메서드만 호출하면 됨 (내부 로직은 몰라도 됨)
    public void match(BigDecimal quantity) {
        if (this.status == OrderStatus.FILLED || this.status == OrderStatus.CANCELLED) {
            throw new IllegalStateException(&quot;이미 종료된 주문입니다.&quot;);
        }

        this.remainingQuantity = this.remainingQuantity.subtract(quantity);

        if (this.remainingQuantity.compareTo(BigDecimal.ZERO) == 0) {
            this.status = OrderStatus.FILLED;
        } else {
            this.status = OrderStatus.PARTIALLY_FILLED;
        }
    }

    public void cancel() {
        if (this.status == OrderStatus.FILLED) {
             throw new IllegalStateException(&quot;이미 체결된 주문은 취소할 수 없습니다.&quot;);
        }
        this.status = OrderStatus.CANCELLED;
    }
}</code></pre>
<p>이제 서비스 레이어는 아주 깔끔해집니다. <code>order.match(quantity)</code> 한 줄이면 끝납니다. 상태 관리는 주문 객체가 알아서 합니다.</p>
<h2 id="3-상태-패턴-state-pattern-적용하기">3. 상태 패턴 (State Pattern) 적용하기?</h2>
<p>만약 상태별 로직이 너무 복잡하다면(예: 상태마다 취소 수수료가 다르거나, 이벤트 발행 로직이 완전히 다른 경우), 디자인 패턴 중 하나인 <strong>State Pattern</strong>을 고려해볼 수 있습니다.</p>
<p>[Image of state design pattern class diagram]</p>
<p>하지만 일반적인 거래소 시스템에서는 <code>Enum</code>과 도메인 메서드(위의 '좋은 코드' 예시)만으로도 충분히 깔끔하게 관리할 수 있습니다. <strong>오버 엔지니어링(Over-engineering)을 경계하세요.</strong> 패턴을 위한 패턴 도입보다는, 비즈니스 복잡도에 맞는 적절한 설계를 선택하는 것이 시니어의 역량입니다.</p>
<h2 id="4-데이터베이스에서의-상태-관리">4. 데이터베이스에서의 상태 관리</h2>
<p>DB에 저장할 때도 주의할 점이 있습니다.</p>
<ol>
<li><strong>Enum은 String으로 저장하라:</strong> <code>0, 1, 2</code> 같은 숫자(Ordinal)로 저장하면, 나중에 중간에 상태가 추가되었을 때 대참사가 일어납니다. <code>@Enumerated(EnumType.STRING)</code>을 꼭 사용하세요.</li>
<li><strong>인덱스(Index):</strong> <code>WHERE status = 'OPEN'</code> 쿼리는 매칭 엔진이 가장 많이 때리는 쿼리입니다. 상태 컬럼에 인덱스를 거는 것을 잊지 마세요.</li>
</ol>
<h2 id="5-마치며-이제-엔진을-돌릴-시간">5. 마치며: 이제 엔진을 돌릴 시간</h2>
<p>우리는 지금까지 거래소를 만들기 위한 준비 운동을 마쳤습니다.</p>
<ol>
<li><strong>호가창 구조</strong>를 잡았고 (Chapter 1)</li>
<li><strong>정확한 돈</strong>을 정의했으며 (Chapter 2)</li>
<li><strong>주문의 상태</strong>를 정의했습니다 (Chapter 3)</li>
</ol>
<p>이제 드디어 이 정적인 부품들을 조립해서 움직이게 할 시간입니다.
다음 챕터부터는 이 시리즈의 하이라이트인 <strong>[Part 2. 핵심 엔진 구현]</strong>으로 들어갑니다. DB가 아니라 메모리 위에서 초고속으로 주문을 체결시키는 <strong>매칭 엔진(Matching Engine)</strong>을 직접 만들어보겠습니다.</p>
<hr />
<p><strong>Next Post:</strong> [Chapter 4. 체결 엔진(Matching Engine) 만들기 - DB는 너무 느리다]</p>
<hr />