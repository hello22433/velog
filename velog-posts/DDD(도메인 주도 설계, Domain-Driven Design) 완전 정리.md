<h1 id="ddd도메인-주도-설계-domain-driven-design-완전-정리">DDD(도메인 주도 설계, Domain-Driven Design) 완전 정리</h1>
<hr />
<h2 id="1-ddd란-무엇인가">1. DDD란 무엇인가?</h2>
<p><strong>DDD(Domain-Driven Design, 도메인 주도 설계)</strong>는 2003년 Eric Evans가 제안한 설계 방법론입니다.
핵심 아이디어는:</p>
<blockquote>
<p><strong>“소프트웨어를 비즈니스 도메인(현실 세계의 문제 영역)에 맞추어 설계하라”</strong></p>
</blockquote>
<p>즉, <strong>코드 구조가 실제 비즈니스 개념과 최대한 일치하도록 만들고</strong>,
팀 전체가 공통 언어(Ubiquitous Language)를 사용하도록 하는 접근 방식입니다.</p>
<hr />
<h3 id="1-1-왜-ddd가-필요한가">1-1. 왜 DDD가 필요한가?</h3>
<ol>
<li><p><strong>복잡한 비즈니스 로직을 명확히 표현</strong></p>
<ul>
<li>현실 세계의 규칙과 요구사항이 복잡할수록, 단순 CRUD 코드만으로는 관리가 어렵습니다.</li>
</ul>
</li>
<li><p><strong>개발자와 도메인 전문가 간 의사소통 향상</strong></p>
<ul>
<li>“Order”, “Payment”, “Shipment” 같은 용어를 코드와 문서에서 일관되게 사용.</li>
</ul>
</li>
<li><p><strong>유지보수와 확장성 개선</strong></p>
<ul>
<li>도메인 중심 구조 덕분에 새로운 요구사항이 들어와도 핵심 로직(Core)은 최소한만 수정.</li>
</ul>
</li>
</ol>
<hr />
<h2 id="2-ddd-핵심-구성-요소">2. DDD 핵심 구성 요소</h2>
<p>DDD를 이해하려면 <strong>도메인과 관련된 개념들</strong>을 알아야 합니다.</p>
<table>
<thead>
<tr>
<th>개념</th>
<th>설명</th>
<th>예시(온라인 쇼핑몰)</th>
</tr>
</thead>
<tbody><tr>
<td><strong>도메인(Domain)</strong></td>
<td>소프트웨어가 해결하려는 문제 영역, 핵심 비즈니스</td>
<td>주문, 결제, 상품, 회원</td>
</tr>
<tr>
<td><strong>엔티티(Entity)</strong></td>
<td>식별 가능한 객체, 상태를 가지며 ID로 구분</td>
<td>주문(Order), 회원(User)</td>
</tr>
<tr>
<td><strong>밸류 객체(Value Object)</strong></td>
<td>값으로만 식별, 불변(immutable) 객체</td>
<td>주소(Address), 금액(Money)</td>
</tr>
<tr>
<td><strong>애그리거트(Aggregate)</strong></td>
<td>관련 객체들을 묶어 일관성 있게 관리</td>
<td>주문(Order) + 주문 상품(OrderItem)</td>
</tr>
<tr>
<td><strong>리포지토리(Repository)</strong></td>
<td>엔티티 저장/조회 담당</td>
<td>OrderRepository, UserRepository</td>
</tr>
<tr>
<td><strong>도메인 서비스(Domain Service)</strong></td>
<td>엔티티와 밸류 객체가 담당하지 않는 도메인 로직 수행</td>
<td>결제 처리 서비스(PaymentService)</td>
</tr>
<tr>
<td><strong>유비쿼터스 언어(Ubiquitous Language)</strong></td>
<td>개발자와 도메인 전문가가 공유하는 공통 언어</td>
<td>“회원 가입”, “주문 결제”, “배송 준비”</td>
</tr>
</tbody></table>
<hr />
<h3 id="2-1-엔티티-vs-밸류-객체">2-1. 엔티티 vs 밸류 객체</h3>
<ul>
<li><p><strong>엔티티(Entity)</strong>: 식별자가 있고, 생명주기가 있음</p>
<ul>
<li>예: 주문(Order), 회원(User)</li>
</ul>
</li>
<li><p><strong>밸류 객체(Value Object)</strong>: 값만으로 구분되고 불변</p>
<ul>
<li>예: 주소(Address), 금액(Money)</li>
</ul>
</li>
</ul>
<blockquote>
<p>“회원 ID”는 엔티티, “배송지 주소”는 밸류 객체</p>
</blockquote>
<hr />
<h3 id="2-2-애그리거트aggregate">2-2. 애그리거트(Aggregate)</h3>
<ul>
<li>연관된 엔티티와 밸류 객체를 하나의 단위로 묶는 개념</li>
<li>외부에서는 <strong>애그리거트 루트(Aggregate Root)</strong>를 통해서만 접근</li>
<li>예: 주문(Order) 애그리거트는 <strong>주문(Order) + 주문 상품(OrderItem)</strong> 묶음</li>
</ul>
<hr />
<h3 id="2-3-리포지토리repository">2-3. 리포지토리(Repository)</h3>
<ul>
<li>데이터베이스를 직접 다루지 않고, <strong>도메인 객체를 저장/조회</strong>하는 역할</li>
<li>Core 로직은 DB나 외부 인프라에 의존하지 않음</li>
<li>예: OrderRepository → <code>save(Order)</code> / <code>findById(OrderId)</code></li>
</ul>
<hr />
<h3 id="2-4-도메인-서비스domain-service">2-4. 도메인 서비스(Domain Service)</h3>
<ul>
<li>엔티티나 밸류 객체가 처리하기 어려운 <strong>도메인 로직</strong> 담당</li>
<li>예: 할인 정책, 결제 처리, 포인트 계산 등</li>
</ul>
<hr />
<h2 id="3-ddd의-핵심-원칙">3. DDD의 핵심 원칙</h2>
<ol>
<li><p><strong>비즈니스 도메인 중심 설계</strong></p>
<ul>
<li>코드 구조를 비즈니스 용어와 맞춤</li>
</ul>
</li>
<li><p><strong>유비쿼터스 언어 사용</strong></p>
<ul>
<li>모든 팀원이 같은 용어를 사용</li>
</ul>
</li>
<li><p><strong>애그리거트 루트로 일관성 유지</strong></p>
<ul>
<li>도메인 상태를 한 곳에서 관리</li>
</ul>
</li>
<li><p><strong>인프라(데이터베이스, 외부 시스템)와 분리</strong></p>
<ul>
<li>Core 로직은 외부 기술에 의존하지 않음</li>
</ul>
</li>
</ol>
<hr />
<h2 id="4-ddd-장점">4. DDD 장점</h2>
<ol>
<li><strong>복잡한 도메인 로직을 명확히 구조화</strong></li>
<li><strong>유지보수 용이</strong></li>
<li><strong>팀 간 의사소통 개선</strong></li>
<li><strong>확장성과 테스트 용이성</strong></li>
</ol>
<hr />
<h2 id="5-ddd-단점--현실적인-한계">5. DDD 단점 / 현실적인 한계</h2>
<ol>
<li><strong>초기 학습 비용이 높음</strong></li>
<li><strong>작은 서비스에는 과도한 설계</strong></li>
<li><strong>구현 복잡도 증가</strong></li>
</ol>
<blockquote>
<p>결론: <strong>DDD는 “필수”가 아니라, 필요할 때 적용하는 설계 방법론입니다.</strong></p>
</blockquote>
<hr />
<h2 id="6-현실적인-적용-기준">6. 현실적인 적용 기준</h2>
<table>
<thead>
<tr>
<th>상황</th>
<th>적용 여부</th>
</tr>
</thead>
<tbody><tr>
<td>복잡한 비즈니스 로직, 여러 도메인</td>
<td>적극 추천</td>
</tr>
<tr>
<td>팀 규모가 크고 장기 유지보수 필요</td>
<td>추천</td>
</tr>
<tr>
<td>단순 CRUD, MVP, 스타트업 프로토타입</td>
<td>불필요</td>
</tr>
<tr>
<td>단일 개발자, 작은 프로젝트</td>
<td>과도할 수 있음</td>
</tr>
</tbody></table>
<hr />
<h2 id="7-요약">7. 요약</h2>
<ul>
<li><p><strong>DDD = 도메인 중심 설계 + 유비쿼터스 언어 + 명확한 경계</strong></p>
</li>
<li><p><strong>장점</strong>: 복잡한 도메인 명확화, 유지보수 용이, 팀 소통 개선</p>
</li>
<li><p><strong>단점</strong>: 초기 학습 곡선 높음, 작은 프로젝트에는 과도</p>
</li>
<li><p><strong>현실적 조언</strong>:</p>
<ul>
<li>큰 서비스 → 적용 고려</li>
<li>작은 서비스 → 단순 설계 후 점진적 적용 가능</li>
</ul>
</li>
</ul>
<hr />