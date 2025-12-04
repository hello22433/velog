<p>아래는 <strong>Layered Architecture(계층형 아키텍처)</strong>를 중심 개념으로 두고,
Spring Boot의 Controller–Service–Repository 구조가 어떻게 논리적으로 대응되는지 명확하게 설명한 글입니다.</p>
<hr />
<h1 id=""><img alt="" src="https://velog.velcdn.com/images/hello22433/post/1f3d0a8e-9abb-4314-80e2-99916c9c5441/image.png" /></h1>
<p>Layered Architecture(계층형 아키텍처)는 소프트웨어를 기능적 책임에 따라 <strong>수평적으로 분리</strong>하는 구조를 말한다.
일반적으로 다음 네 개의 계층으로 구성된다.</p>
<ol>
<li><strong>Presentation Layer</strong></li>
<li><strong>Application Layer</strong></li>
<li><strong>Domain Layer</strong></li>
<li><strong>Infrastructure Layer</strong></li>
</ol>
<p>각 계층은 명확한 책임을 가지며, 의존성은 <strong>상위 계층 → 하위 계층</strong>으로만 향해야 한다.
즉, Presentation은 Application에만 의존할 수 있고, Application은 Domain에만 의존할 수 있으며, Domain은 어떠한 상위 계층에도 의존하지 않아야 한다.</p>
<p>Spring Boot의 Controller–Service–Repository 구조는 이러한 계층형 아키텍처의 개념을 실무적으로 구현한 방식이다.
아래에서는 두 구조를 책임과 의존성 관점에서 분석한다.</p>
<hr />
<h2 id="1-각-계층의-책임과-spring-구성-요소의-역할-비교">1. 각 계층의 책임과 Spring 구성 요소의 역할 비교</h2>
<h3 id="11-presentation-layer-→-controller--dto">1.1 Presentation Layer → Controller / DTO</h3>
<ul>
<li><p><strong>핵심 책임:</strong> 외부(HTTP, UI 등)와 시스템 내부 사이의 인터페이스 역할</p>
</li>
<li><p><strong>특징:</strong> 요청 파싱, 응답 생성, 인증·인가 처리</p>
</li>
<li><p><strong>Spring 대응 요소:</strong></p>
<ul>
<li><code>@RestController</code></li>
<li>요청 DTO, 응답 DTO</li>
</ul>
</li>
<li><p><strong>의존성:</strong> Service(Application Layer)에게만 의존</p>
</li>
</ul>
<blockquote>
<p>Controller는 외부와의 접점을 담당하므로 Presentation Layer의 역할과 완전히 동일하다.</p>
</blockquote>
<hr />
<h3 id="12-application-layer-→-service">1.2 Application Layer → Service</h3>
<ul>
<li><p><strong>핵심 책임:</strong> 유스케이스 조율, 트랜잭션 경계 관리, 비즈니스 흐름 제어</p>
</li>
<li><p><strong>특징:</strong></p>
<ul>
<li>“무엇을 할지”에 대한 절차적 흐름 담당</li>
<li>도메인 규칙 그 자체는 포함하지 않음</li>
</ul>
</li>
<li><p><strong>Spring 대응 요소:</strong></p>
<ul>
<li><code>@Service</code> 클래스</li>
<li>트랜잭션 경계 (<code>@Transactional</code>)</li>
</ul>
</li>
<li><p><strong>의존성:</strong> Domain Layer(entity, domain logic)과 Infrastructure Layer(repository)에 의존 가능</p>
</li>
</ul>
<blockquote>
<p>Service가 유스케이스 단위로 흐름을 조율하는 특징은 Application Layer의 역할과 동일하다.</p>
</blockquote>
<hr />
<h3 id="13-domain-layer-→-entity--domain-logic">1.3 Domain Layer → Entity / Domain Logic</h3>
<ul>
<li><p><strong>핵심 책임:</strong> 시스템이 해결하고자 하는 핵심 규칙 및 모델 정의</p>
</li>
<li><p><strong>특징:</strong></p>
<ul>
<li>불변 규칙, 비즈니스 개념, 상태 변화 로직</li>
<li>외부 기술에 의존하지 않아야 함</li>
</ul>
</li>
<li><p><strong>Spring 대응 요소:</strong></p>
<ul>
<li><code>@Entity</code></li>
<li>엔티티 내부의 비즈니스 규칙(예: 재고 감소, 상태 전환 등)</li>
</ul>
</li>
</ul>
<blockquote>
<p>도메인은 기술이 아니라 비즈니스 개념을 중심으로 설계되며, 엔티티가 이 역할을 담당한다.</p>
</blockquote>
<hr />
<h3 id="14-infrastructure-layer-→-repository--외부-시스템-어댑터">1.4 Infrastructure Layer → Repository / 외부 시스템 어댑터</h3>
<ul>
<li><p><strong>핵심 책임:</strong> 실제 기술 구현(DB, Redis, 외부 API, 이벤트 브로커 등)</p>
</li>
<li><p><strong>특징:</strong></p>
<ul>
<li>Application/Domain이 사용하는 실제 기술적 리소스를 제공</li>
<li>데이터 저장·조회 구현</li>
</ul>
</li>
<li><p><strong>Spring 대응 요소:</strong></p>
<ul>
<li>Spring Data JPA <code>Repository</code> 인터페이스</li>
<li>외부 API 어댑터, Redis, Kafka 등과의 연동 코드</li>
</ul>
</li>
</ul>
<blockquote>
<p>Repository는 데이터 접근의 기술적 구현이므로 Infrastructure 계층에 해당한다.</p>
</blockquote>
<hr />
<h2 id="2-layered-architecture의-의존성-규칙과-spring-구조의-일치성">2. Layered Architecture의 의존성 규칙과 Spring 구조의 일치성</h2>
<p>Layered Architecture는 다음과 같은 핵심 의존성 규칙을 가진다.</p>
<h3 id="규칙-1-상위-계층은-하위-계층에만-의존한다">규칙 1. 상위 계층은 하위 계층에만 의존한다.</h3>
<ul>
<li>Presentation → Application</li>
<li>Application → Domain + Infrastructure</li>
<li>Domain → (타 계층에 의존 없음)</li>
</ul>
<h3 id="spring에서의-적용">Spring에서의 적용:</h3>
<ul>
<li>Controller는 Service만 호출한다.</li>
<li>Service는 Entity와 Repository를 사용한다.</li>
<li>Entity는 Repository나 Controller를 절대 호출하지 않는다.</li>
</ul>
<p>이는 계층형 아키텍처의 <strong>의존성 방향 원칙과 정확히 일치</strong>한다.</p>
<hr />
<h2 id="3-왜-spring-boot는-계층-이름을-그대로-사용하지-않는가">3. 왜 Spring Boot는 계층 이름을 그대로 사용하지 않는가?</h2>
<p>Layered Architecture의 개념을 물리적 폴더 이름(Presentation, Application 등)으로 만드는 것은 가능하다.
그러나 Spring 생태계에서는 Controller/Service/Repository라는 <strong>Type 기반의 명명법이 사실상 표준</strong>으로 자리 잡았다.
그 이유는 다음과 같다.</p>
<h3 id="31-repository-구현을-스프링이-자동-생성하기-때문">3.1 Repository 구현을 스프링이 자동 생성하기 때문</h3>
<p>Infrastructure 계층의 코드를 개발자가 직접 작성할 일이 적다.
따라서 Infrastructure라는 폴더를 구성해도 현실적으로 비어 있는 경우가 많기 때문이다.</p>
<h3 id="32-개발자-간-직관성과-가독성이-높다">3.2 개발자 간 직관성과 가독성이 높다</h3>
<p>“상품 등록 API는 어디에 있는가?”라는 질문은 controller 폴더를 찾는 것만으로 해결되며,
이는 협업에서 불필요한 인지 부하를 줄인다.</p>
<h3 id="33-물리적인-폴더-이름은-중요하지-않다">3.3 물리적인 폴더 이름은 중요하지 않다</h3>
<p>Layered Architecture의 핵심은 <strong>의존성 구조와 책임 분리</strong>이며,
폴더 이름이 Presentation인지 Controller인지 여부와는 무관하다.
현재 Spring 표준 구조 또한 계층형 아키텍처의 개념적 규칙을 완벽히 충족한다.</p>
<hr />
<h2 id="4-물리-계층폴더-구조까지-layered-architecture로-가지-않는-이유">4. 물리 계층(폴더 구조)까지 Layered Architecture로 가지 않는 이유</h2>
<p>Layered Architecture의 개념을 더 엄격히 적용하는 경우는 다음과 같은 상황이다.</p>
<ul>
<li>여러 기술 스택(DB, MQ, 외부 API 등) 변화가 빈번하며 기술 독립성이 중요할 때</li>
<li>시스템 규모가 커져 Service와 Domain이 지나치게 복잡해질 때</li>
<li>헥사고날 아키텍처나 클린 아키텍처 도입이 요구될 때</li>
</ul>
<p>이러한 상황에서는 Presentation, Application, Domain, Infrastructure를 물리적으로 나누는 것이 유지보수 효율을 높인다.</p>
<p>그러나 중·소규모 서비스 또는 일반적인 REST API 개발에서는
현재의 Spring 관습적 구조가 <strong>가장 단순하면서도 계층형 아키텍처의 원칙을 충실히 지킨 구현 방식</strong>이다.</p>
<hr />
<h2 id="결론">결론</h2>
<ol>
<li><strong>Spring Boot의 Controller–Service–Repository–Entity 구조는 Layered Architecture의 책임 분리 원칙과 의존성 규칙을 정확히 따른다.</strong></li>
<li>명칭의 차이는 관습적·실용적 차이이며, 논리적으로는 Presentation, Application, Domain, Infrastructure와 1:1 매핑된다.</li>
<li>계층형 아키텍처의 핵심은 폴더 이름이 아니라 <strong>책임과 의존성 방향</strong>이며,
Spring 구조는 이 원칙을 실무적으로 최적화한 형태다.</li>
<li>대규모 시스템에서만 논리 계층을 물리 계층으로 확장하는 것이 의미가 있으며,
일반적인 Spring 서비스에서는 현재 구조가 가장 적절한 Layered Architecture 구현이다.</li>
</ol>