<p>클린 아키텍처는 소프트웨어를 <strong>유지보수하기 쉽고, 테스트하기 쉽고, 확장 가능하게 만드는 설계 원칙</strong>을 말합니다.
로버트 C. 마틴(Robert C. Martin, Uncle Bob)이 제안한 구조입니다.</p>
<hr />
<h2 id="1️⃣-핵심-개념">1️⃣ 핵심 개념</h2>
<ol>
<li><p><strong>계층 분리</strong></p>
<ul>
<li>소프트웨어를 <strong>몇 가지 층(Layer)으로 나누고 책임을 분리</strong>하는 것</li>
<li>예: <code>Domain</code>, <code>Application</code>, <code>Interface</code>, <code>Infrastructure</code></li>
<li>계층별 책임을 명확히 해서 서로 의존성을 최소화</li>
</ul>
</li>
<li><p><strong>의존성 규칙</strong></p>
<ul>
<li><strong>안쪽 계층(inner layer)</strong>는 <strong>바깥 계층(outer layer)</strong>에 의존하지 않음</li>
<li>의존성 방향: <strong>외부 → 내부</strong></li>
<li>예: Controller → Service → Domain → Repository(인터페이스)</li>
<li>안쪽 계층은 외부 기술(예: DB, 프레임워크)에 의존하지 않음</li>
</ul>
</li>
<li><p><strong>비즈니스 로직 보호</strong></p>
<ul>
<li>핵심 비즈니스 로직은 외부 변경(프레임워크, DB, UI)에 영향받지 않음</li>
<li>즉, <strong>도메인 모델은 가장 안정적인 계층</strong></li>
</ul>
</li>
</ol>
<hr />
<h2 id="2️⃣-계층-구조기본">2️⃣ 계층 구조(기본)</h2>
<pre><code>       +-----------------+
       |   Interface     | &lt;- Controller, API, UI
       +-----------------+
               ↓
       +-----------------+
       |   Application   | &lt;- UseCase, Service
       +-----------------+
               ↓
       +-----------------+
       |     Domain      | &lt;- 핵심 비즈니스 로직, Entity
       +-----------------+
               ↓
       +-----------------+
       | Infrastructure  | &lt;- DB, 메시징, 외부 API
       +-----------------+</code></pre><ul>
<li><strong>Domain</strong>: 핵심 엔티티와 비즈니스 규칙</li>
<li><strong>Application</strong>: Use Case, 트랜잭션 관리</li>
<li><strong>Interface</strong>: 사용자 요청 처리, Controller</li>
<li><strong>Infrastructure</strong>: DB, 메시징, 외부 API 구현</li>
</ul>
<hr />
<h2 id="3️⃣-왜-클린-아키텍처가-중요한가">3️⃣ 왜 클린 아키텍처가 중요한가?</h2>
<ul>
<li><strong>유지보수 용이</strong>: 계층별 책임이 명확해서 코드 변경이 안전</li>
<li><strong>테스트 편리</strong>: 핵심 로직이 외부 시스템에 의존하지 않아 단위 테스트 가능</li>
<li><strong>확장성 향상</strong>: 새로운 DB, 메시징, UI를 추가해도 내부 로직 변경 최소화</li>
</ul>
<hr />
<h2 id="4️⃣-핵심-포인트-요약">4️⃣ 핵심 포인트 요약</h2>
<ol>
<li><strong>안쪽 계층이 바깥 계층에 의존하면 안 된다</strong></li>
<li><strong>비즈니스 로직을 중심에 두고 외부 기술에 영향받지 않게 한다</strong></li>
<li><strong>계층별 책임을 명확히 나눈다</strong></li>
<li><strong>테스트와 확장을 쉽게 만들기 위한 구조</strong></li>
</ol>
<hr />
<p>정리하면, 클린 아키텍처는 <strong>“코드가 커져도 혼란스럽지 않게 유지보수하고 확장 가능하게 만드는 설계 원칙”</strong>입니다.
면접에서는 주로 <strong>“계층 구조와 의존성 규칙을 설명해보세요”</strong>처럼 질문이 나옵니다.</p>
<hr />