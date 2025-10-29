<hr />
<h2 id="1-개념">1. 개념</h2>
<p>CQRS는 말 그대로 <strong>“명령(Command)과 조회(Query) 책임 분리”</strong>를 뜻합니다.</p>
<ul>
<li><strong>Command(명령)</strong> → 상태를 바꾸는 동작, 즉 <strong>쓰기(Write)</strong>
예: 주문 생성, 결제 처리, 회원 정보 수정</li>
<li><strong>Query(조회)</strong> → 데이터를 읽는 동작, 즉 <strong>읽기(Read)</strong>
예: 주문 내역 조회, 상품 목록 조회, 회원 정보 확인</li>
</ul>
<p>즉, <strong>읽기와 쓰기를 하나의 모델/테이블에서 처리하지 않고 분리</strong>하는 아키텍처 패턴입니다.</p>
<hr />
<h3 id="왜-분리하나">왜 분리하나?</h3>
<p>전통적인 CRUD 시스템에서는 <strong>읽기와 쓰기를 같은 테이블, 같은 구조로 처리</strong>합니다.
하지만 대규모 시스템에서는 몇 가지 문제가 생깁니다.</p>
<ol>
<li><p><strong>읽기와 쓰기 부하가 다름</strong></p>
<ul>
<li>조회 요청은 수천, 수만 건 발생할 수 있음</li>
<li>쓰기 요청은 상대적으로 적음</li>
</ul>
</li>
<li><p><strong>읽기/쓰기 모델 요구가 다름</strong></p>
<ul>
<li>조회는 복잡한 JOIN, 필터링, 캐시 최적화 필요</li>
<li>쓰기는 데이터 정합성과 트랜잭션 보장이 중요</li>
</ul>
</li>
</ol>
<p>CQRS는 이를 해결하기 위해 <strong>읽기와 쓰기를 완전히 분리</strong>합니다.</p>
<hr />
<h2 id="2-구조-예시">2. 구조 예시</h2>
<pre><code>[사용자] → Command(Write) → [Write DB] → Event → [Read DB] ← Query(Read) ← [사용자]</code></pre><ul>
<li><strong>Command(쓰기)</strong>: 주문 생성, 결제 처리 → Write DB에 기록</li>
<li><strong>Event</strong>: 상태 변경 이벤트 발행 → Read DB에 반영</li>
<li><strong>Query(읽기)</strong>: 조회는 Read DB에서 최적화된 구조로 수행</li>
</ul>
<p>즉, <strong>읽기 전용 DB</strong>와 <strong>쓰기 전용 DB</strong>를 별도로 가질 수 있고, 필요에 따라 스케일링도 독립적으로 가능</p>
<hr />
<h2 id="3-장점">3. 장점</h2>
<ol>
<li><p><strong>확장성(Scalability)</strong></p>
<ul>
<li>읽기와 쓰기를 독립적으로 확장 가능 → 트래픽이 많은 서비스에서 유리</li>
</ul>
</li>
<li><p><strong>읽기 최적화</strong></p>
<ul>
<li>Read DB는 조회 중심으로 설계 → JOIN, 캐시, 검색 최적화 가능</li>
</ul>
</li>
<li><p><strong>유지보수 용이</strong></p>
<ul>
<li>읽기/쓰기 로직이 분리되어 코드와 모델이 단순해짐</li>
</ul>
</li>
<li><p><strong>이벤트 기반 통합 용이</strong></p>
<ul>
<li>이벤트 발행을 통해 다른 서비스와 쉽게 통합 가능</li>
</ul>
</li>
</ol>
<hr />
<h2 id="4-단점">4. 단점</h2>
<ol>
<li><p><strong>복잡성 증가</strong></p>
<ul>
<li>읽기/쓰기 모델을 분리하면 시스템 구조가 복잡해짐</li>
<li>데이터 동기화, 이벤트 처리, eventual consistency 문제 발생</li>
</ul>
</li>
<li><p><strong>데이터 일관성 관리 어려움</strong></p>
<ul>
<li>Write DB와 Read DB 간 <strong>즉시 일관성(Strong Consistency)</strong> 보장 어려움</li>
<li>eventual consistency(시간 차이가 있어도 결국 일치) 방식 필요</li>
</ul>
</li>
<li><p><strong>작은 프로젝트에는 과도</strong></p>
<ul>
<li>소규모 CRUD 앱에서는 분리로 얻는 이점보다 구현 난이도가 더 큼</li>
</ul>
</li>
</ol>
<hr />
<h2 id="5-언제-유용한가">5. 언제 유용한가?</h2>
<ul>
<li><p>대규모 시스템에서 <strong>읽기/쓰기 트래픽 특성이 다를 때</strong></p>
</li>
<li><p>조회가 매우 빈번하고, 쓰기는 상대적으로 적은 경우</p>
</li>
<li><p>이벤트 기반 아키텍처와 결합해서 <strong>마이크로서비스 간 데이터 통합</strong>이 필요한 경우</p>
</li>
<li><p>예시:</p>
<ul>
<li>쇼핑몰 → 주문/결제는 쓰기, 상품 조회/검색은 읽기 많음</li>
<li>금융 서비스 → 거래 처리(쓰기) vs 계좌 조회(읽기)</li>
</ul>
</li>
</ul>
<hr />
<h3 id="✅-요약">✅ 요약</h3>
<table>
<thead>
<tr>
<th>항목</th>
<th>내용</th>
</tr>
</thead>
<tbody><tr>
<td>개념</td>
<td>읽기(Query)와 쓰기(Command)를 분리</td>
</tr>
<tr>
<td>장점</td>
<td>읽기/쓰기 독립적 확장, 읽기 최적화, 이벤트 기반 통합</td>
</tr>
<tr>
<td>단점</td>
<td>구조 복잡, 데이터 일관성 관리 필요, 작은 프로젝트에는 과도</td>
</tr>
<tr>
<td>유용한 상황</td>
<td>읽기/쓰기 트래픽 불균형, 이벤트 기반 마이크로서비스, 대규모 시스템</td>
</tr>
</tbody></table>
<hr />