<h1 id="1-saga-패턴-개념">1. SAGA 패턴 개념</h1>
<p><strong>SAGA 패턴</strong>은 MSA 환경에서 분산 트랜잭션을 관리하는 방법 중 하나로, 핵심은 다음과 같습니다.</p>
<ol>
<li><p><strong>단계별 로컬 트랜잭션(Local Transaction)</strong></p>
<ul>
<li>각 서비스는 <strong>자기 DB에서 독립적으로 트랜잭션 수행</strong></li>
<li>예: OrderService, PaymentService, InventoryService 각각 자기 DB 처리</li>
</ul>
</li>
<li><p><strong>보상 트랜잭션(Compensating Transaction)</strong></p>
<ul>
<li>단계 중 <strong>문제가 발생하면 이전 단계 작업을 되돌리는 트랜잭션</strong> 수행</li>
<li>예: 결제 완료 후 재고 차감 실패 → 결제 취소, 주문 취소</li>
</ul>
</li>
<li><p><strong>이벤트 기반(Event-Driven)</strong></p>
<ul>
<li>각 단계 완료 여부를 <strong>이벤트로 발행</strong> → 다른 서비스가 구독</li>
<li>이벤트를 기반으로 다음 단계 진행 또는 보상 트랜잭션 실행</li>
</ul>
</li>
<li><p><strong>최종 일관성(Eventual Consistency)</strong></p>
<ul>
<li>단계별로 처리하므로 잠시 동안 데이터 불일치 발생 가능</li>
<li>모든 단계 완료/보상 트랜잭션 수행 후 <strong>최종적으로 데이터 일관성 확보</strong></li>
</ul>
</li>
</ol>
<hr />
<h1 id="2-단계별-예시-온라인-쇼핑몰-주문-처리">2. 단계별 예시: 온라인 쇼핑몰 주문 처리</h1>
<h3 id="서비스-구성">서비스 구성</h3>
<ul>
<li><strong>OrderService</strong>: 주문 생성</li>
<li><strong>PaymentService</strong>: 결제 처리</li>
<li><strong>InventoryService</strong>: 재고 차감</li>
</ul>
<hr />
<h3 id="단계별-시나리오">단계별 시나리오</h3>
<ol>
<li><p><strong>OrderService</strong></p>
<ul>
<li>주문 생성, Write DB에 저장</li>
<li>이벤트 발행: <code>OrderCreated</code></li>
</ul>
</li>
<li><p><strong>PaymentService</strong></p>
<ul>
<li>이벤트 구독 → 결제 처리</li>
<li>성공 → 이벤트 발행: <code>PaymentCompleted</code></li>
<li>실패 → 이벤트 발행: <code>PaymentFailed</code> → OrderService에서 주문 취소</li>
</ul>
</li>
<li><p><strong>InventoryService</strong></p>
<ul>
<li><code>PaymentCompleted</code> 이벤트 구독 → 재고 차감</li>
<li>성공 → 이벤트 발행: <code>InventoryUpdated</code></li>
<li>실패 → 이벤트 발행: <code>InventoryFailed</code> → PaymentService 결제 취소, OrderService 주문 취소</li>
</ul>
</li>
</ol>
<hr />
<h3 id="흐름-그림-없이-글로">흐름 그림 없이 글로</h3>
<pre><code>[OrderService] - create order
     │
     ▼ (OrderCreated 이벤트)
[PaymentService] - process payment
     │
     ├─&gt; 성공 → PaymentCompleted 이벤트 → InventoryService 처리
     └─&gt; 실패 → PaymentFailed 이벤트 → OrderService 보상 트랜잭션

[InventoryService] - decrease inventory
     │
     ├─&gt; 성공 → InventoryUpdated 이벤트 → 최종 완료
     └─&gt; 실패 → InventoryFailed 이벤트 → PaymentService, OrderService 보상 트랜잭션</code></pre><ul>
<li>이벤트 기반으로 <strong>각 서비스가 독립적으로 처리</strong></li>
<li>문제 발생 시 <strong>보상 트랜잭션</strong>으로 이전 상태 복구</li>
<li>처리 완료 후 최종적으로 <strong>데이터 일관성 확보(Eventual Consistency)</strong></li>
</ul>
<hr />
<h1 id="3-eventual-consistency와-saga">3. Eventual Consistency와 SAGA</h1>
<ul>
<li><p><strong>왜 즉시 일관성을 포기하는가?</strong></p>
<ul>
<li>MSA에서는 각 서비스가 독립 DB를 가짐 → 동기 트랜잭션으로 처리 불가</li>
<li>강한 일관성을 위해 동기화하면 서비스 간 결합도↑, 확장성↓, 성능↓</li>
</ul>
</li>
<li><p><strong>Eventual Consistency 의미</strong></p>
<ul>
<li>각 단계 처리 중에는 일시적으로 데이터 불일치 가능</li>
<li>모든 단계 완료/보상 후 <strong>최종적으로 모든 서비스 데이터가 일치</strong></li>
</ul>
</li>
<li><p>즉, <strong>SAGA 패턴은 Eventual Consistency를 구현하는 대표적 방법</strong>이라고 이해하면 쉬움</p>
</li>
</ul>
<hr />
<h1 id="4-장점과-단점">4. 장점과 단점</h1>
<table>
<thead>
<tr>
<th>장점</th>
<th>단점</th>
</tr>
</thead>
<tbody><tr>
<td>서비스 간 결합도 낮음</td>
<td>구현 복잡, 이벤트 설계 필요</td>
</tr>
<tr>
<td>MSA 환경에 최적</td>
<td>데이터 일시 불일치 발생 가능</td>
</tr>
<tr>
<td>장애 격리, 확장성 높음</td>
<td>보상 트랜잭션 설계 어려움</td>
</tr>
<tr>
<td>최종적으로 데이터 일관성 확보</td>
<td>테스트와 디버깅 어려움</td>
</tr>
</tbody></table>
<hr />
<h1 id="5-핵심-요약">5. 핵심 요약</h1>
<ul>
<li><strong>MSA 환경에서는 분산 트랜잭션 거의 사용하지 않음</strong></li>
<li>대신 <strong>SAGA 패턴 활용</strong> → 단계별 로컬 트랜잭션 + 이벤트 기반 보상</li>
<li>데이터는 <strong>즉시 일관성보다 단계적 일관성(Eventual Consistency)을 받아들임</strong></li>
<li>결국 <strong>모든 단계 완료 후 데이터 일관성 확보</strong></li>
</ul>
<blockquote>
<p>쉽게 말하면: “각 서비스가 자기 일 하고, 문제 생기면 이전 서비스 롤백 → 끝나면 데이터가 결국 맞춰진다”</p>
</blockquote>
<hr />