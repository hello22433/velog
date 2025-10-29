<h2 id="1-개념">1. 개념</h2>
<ul>
<li><p><strong>이벤트(Event)</strong>: 시스템에서 발생한 “사건”이나 “상태 변화”</p>
<ul>
<li>예: 주문 생성, 결제 완료, 상품 재고 부족</li>
</ul>
</li>
<li><p><strong>Event-Driven Architecture</strong>는 <strong>이벤트를 중심으로 시스템 구성</strong></p>
<ul>
<li>이벤트가 발생하면 <strong>다른 서비스가 이를 구독(subscribe)</strong>하고 반응</li>
<li>서비스 간 직접 호출이 아닌 <strong>이벤트 버스/메시지 큐</strong>로 통신</li>
</ul>
</li>
</ul>
<p>즉, <strong>“누가 무엇을 했는지 이벤트를 알려주면, 관심 있는 서비스가 알아서 처리”</strong>하는 구조입니다.</p>
<hr />
<h2 id="2-구조-예시">2. 구조 예시</h2>
<pre><code>[OrderService] → 주문 생성 이벤트 → [Kafka / RabbitMQ]
                                       │
                         ┌─────────────┴─────────────┐
                         │                           │
                 [InventoryService]           [NotificationService]
              (재고 차감 처리)               (주문 확인 알림)</code></pre><ul>
<li><p>주문 생성 이벤트(OrderCreated)를 발생시키면</p>
<ul>
<li>재고 서비스가 재고 차감</li>
<li>알림 서비스가 사용자에게 주문 확인 메시지 전송</li>
</ul>
</li>
<li><p>OrderService는 <strong>이벤트만 발행</strong>하고, 다른 서비스와 직접 통신하지 않음</p>
</li>
</ul>
<hr />
<h2 id="3-eda의-장점">3. EDA의 장점</h2>
<ol>
<li><p><strong>서비스 간 결합도 낮음(Low Coupling)</strong></p>
<ul>
<li>이벤트만 정의하면, 서비스가 서로 몰라도 됨</li>
</ul>
</li>
<li><p><strong>확장성(Scalability)</strong></p>
<ul>
<li>새 기능 추가 시 기존 서비스 수정 없이 이벤트 구독만 추가</li>
</ul>
</li>
<li><p><strong>비동기 처리</strong></p>
<ul>
<li>주문 처리, 결제, 알림 등 병렬로 처리 가능 → 빠른 응답</li>
</ul>
</li>
</ol>
<hr />
<h2 id="4-eda의-단점--단점이-될-수-있는-경우">4. EDA의 단점 / 단점이 될 수 있는 경우</h2>
<ol>
<li><p><strong>복잡성 증가</strong></p>
<ul>
<li>이벤트 흐름 추적 어려움 → 디버깅과 운영 난이도 상승</li>
</ul>
</li>
<li><p><strong>데이터 일관성 관리 어려움</strong></p>
<ul>
<li>이벤트 기반은 <strong>eventual consistency</strong> → 즉시 일관성 보장 어려움</li>
</ul>
</li>
<li><p><strong>불필요한 경우</strong></p>
<ul>
<li>단순 CRUD 시스템, 작은 서비스</li>
<li>읽기/쓰기 부하가 낮고, 서비스 간 통신이 적은 경우</li>
</ul>
</li>
</ol>
<hr />
<h2 id="5-언제-효과적인가">5. 언제 효과적인가?</h2>
<ul>
<li><p><strong>마이크로서비스 환경</strong>에서 서비스 간 독립성과 비동기 처리가 중요한 경우</p>
</li>
<li><p><strong>이벤트 기반 통합</strong>이 필요한 경우</p>
</li>
<li><p>예시:</p>
<ul>
<li>쇼핑몰: 주문 → 재고 차감 → 배송 준비 → 알림</li>
<li>금융 시스템: 거래 발생 → 회계 처리 → 알림 → 통계</li>
</ul>
</li>
</ul>
<hr />
<h2 id="6-요약">6. 요약</h2>
<table>
<thead>
<tr>
<th>항목</th>
<th>내용</th>
</tr>
</thead>
<tbody><tr>
<td>개념</td>
<td>이벤트를 중심으로 서비스가 반응하는 아키텍처</td>
</tr>
<tr>
<td>장점</td>
<td>서비스 결합도 낮음, 확장성 높음, 비동기 처리 가능</td>
</tr>
<tr>
<td>단점</td>
<td>복잡성 증가, 데이터 일관성 문제, 작은 서비스에는 과도</td>
</tr>
<tr>
<td>유용한 상황</td>
<td>마이크로서비스, 비동기 처리, 이벤트 기반 통합 필요</td>
</tr>
<tr>
<td>불필요한 상황</td>
<td>단순 CRUD, 서비스 규모 작음, 실시간 동기 처리 필요</td>
</tr>
</tbody></table>
<hr />
<p>💡 <strong>핵심:</strong></p>
<blockquote>
<p>EDA는 <strong>무조건 좋은 게 아니라</strong>, “비동기 이벤트 기반 처리와 확장성”이 필요한 경우에만 효과적입니다.
작은 서비스나 단순 CRUD에는 오히려 복잡성과 운영 비용을 증가시키는 선택이 될 수 있습니다.</p>
</blockquote>
<hr />