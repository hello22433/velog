<h2 id="1-개념-트랜잭션">1. 개념: 트랜잭션</h2>
<ul>
<li><p><strong>트랜잭션(Transaction)</strong>: 데이터베이스에서 “하나의 작업 단위”</p>
</li>
<li><p><strong>ACID</strong> 특성:</p>
<ol>
<li><strong>Atomic(원자성)</strong>: 모두 수행되거나 모두 취소</li>
<li><strong>Consistency(일관성)</strong>: 트랜잭션 수행 후 데이터 일관성 유지</li>
<li><strong>Isolation(독립성)</strong>: 동시에 실행되는 트랜잭션 간 간섭 방지</li>
<li><strong>Durability(지속성)</strong>: 완료된 트랜잭션은 영구 저장</li>
</ol>
</li>
</ul>
<hr />
<h2 id="2-단일-db-환경">2. 단일 DB 환경</h2>
<ul>
<li>모든 서비스가 <strong>같은 DB를 공유</strong></li>
<li>트랜잭션은 <strong>전통적인 ACID 트랜잭션</strong>으로 관리 가능</li>
<li>장점: 데이터 일관성 보장 쉽고 구현 단순</li>
<li>단점: 서비스 간 결합 높음, 확장성 제한</li>
</ul>
<p><strong>예시:</strong></p>
<ul>
<li>OrderService + PaymentService + InventoryService → 같은 DB</li>
<li>주문 생성, 결제, 재고 차감 모두 하나의 트랜잭션으로 처리</li>
</ul>
<pre><code>begin transaction
  create order
  process payment
  decrease inventory
commit transaction</code></pre><ul>
<li>실패 시 전체 롤백 → 일관성 유지</li>
</ul>
<hr />
<h2 id="3-msa-환경-분산-트랜잭션-어려움">3. MSA 환경: 분산 트랜잭션 어려움</h2>
<ul>
<li>MSA에서는 <strong>각 서비스가 자기 DB</strong>를 갖기 때문에</li>
<li><strong>한 DB 트랜잭션으로 전체 서비스 처리 불가</strong></li>
</ul>
<h3 id="문제점">문제점</h3>
<ul>
<li><p>XA 트랜잭션 등 분산 트랜잭션은 존재하지만,</p>
<ul>
<li>구현 복잡</li>
<li>성능 저하</li>
<li>장애 발생 시 복구 어려움</li>
</ul>
</li>
</ul>
<hr />
<h2 id="4-해결책-saga-패턴">4. 해결책: SAGA 패턴</h2>
<ul>
<li><strong>SAGA</strong>: 분산 트랜잭션을 <strong>작은 로컬 트랜잭션들의 연속</strong>으로 처리</li>
<li>핵심: <strong>각 단계마다 성공/실패를 이벤트로 처리</strong>, 실패 시 <strong>보상 트랜잭션(compensating transaction)</strong> 수행</li>
</ul>
<h3 id="예시-주문-처리-saga">예시: 주문 처리 SAGA</h3>
<ol>
<li><strong>OrderService</strong>: 주문 생성 → 성공</li>
<li><strong>PaymentService</strong>: 결제 처리 → 성공</li>
<li><strong>InventoryService</strong>: 재고 차감 → 실패</li>
</ol>
<ul>
<li><p>재고 차감 실패 → <strong>보상 트랜잭션 실행</strong></p>
<ul>
<li>PaymentService 결제 취소</li>
<li>OrderService 주문 취소</li>
</ul>
</li>
</ul>
<pre><code>OrderService: create order
PaymentService: process payment
InventoryService: decrease inventory → 실패
PaymentService: rollback payment
OrderService: cancel order</code></pre><ul>
<li>모든 단계가 결국 일관성 유지</li>
</ul>
<hr />
<h2 id="5-장점-vs-단점">5. 장점 vs 단점</h2>
<table>
<thead>
<tr>
<th>항목</th>
<th>장점</th>
<th>단점</th>
</tr>
</thead>
<tbody><tr>
<td>단일 DB 트랜잭션</td>
<td>구현 간단, ACID 보장</td>
<td>확장성 제한, 서비스 결합</td>
</tr>
<tr>
<td>SAGA 패턴</td>
<td>MSA 환경 최적, 서비스 독립성, 분산 일관성</td>
<td>구현 복잡, 보상 트랜잭션 관리 필요, 최종 일관성(Immediate consistency 아님)</td>
</tr>
</tbody></table>
<hr />
<h2 id="6-요약">6. 요약</h2>
<ul>
<li>MSA에서는 <strong>분산 트랜잭션은 거의 사용하지 않고</strong>, SAGA 패턴을 사용해 <strong>단계별 로컬 트랜잭션 + 이벤트 기반 보상</strong>으로 일관성을 유지</li>
<li>단일 DB 환경에서는 <strong>전통적 ACID 트랜잭션</strong>으로 충분</li>
<li>핵심: <strong>“즉시 일관성보다 단계적 일관성(eventual consistency)을 받아들이고, 보상 트랜잭션으로 데이터 안정성 확보”</strong></li>
</ul>
<hr />
<p>💡 <strong>핵심 포인트</strong></p>
<blockquote>
<p>MSA에서는 서비스 간 데이터 독립성 때문에 <strong>분산 트랜잭션을 직접 사용하기보다 SAGA 패턴으로 관리</strong>하는 것이 실무에서 표준 방식</p>
</blockquote>
<hr />