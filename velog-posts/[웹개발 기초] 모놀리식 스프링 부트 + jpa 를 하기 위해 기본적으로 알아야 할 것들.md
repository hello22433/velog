<h1 id="java--언어-기반-필수">Java / 언어 기반 (필수)</h1>
<ul>
<li><p><strong>Java 문법과 핵심 라이브러리</strong></p>
<ul>
<li>컬렉션, 제네릭, 람다/스트림, Optional, Date/Time API</li>
<li>예외 처리(checked/unchecked), 쓰레드 기초</li>
<li>실습: 컬렉션/스트림으로 간단한 데이터 처리 코드 작성</li>
</ul>
</li>
<li><p><strong>OOP &amp; 디자인 패턴</strong></p>
<ul>
<li>캡슐화, 상속, 다형성, SOLID 원칙</li>
<li>설계 패턴: 팩토리, 싱글톤, 전략(Strategy), 템플릿 메서드 등</li>
<li>실습: 비즈니스 규칙을 전략 패턴으로 분리해보기</li>
</ul>
</li>
</ul>
<h1 id="빌드-도구--프로젝트-구조">빌드 도구 &amp; 프로젝트 구조</h1>
<ul>
<li><p><strong>Maven 또는 Gradle</strong></p>
<ul>
<li>의존성 관리, 플러그인 사용법, 빌드/패키징(JAR)</li>
<li>실습: Spring Initializr로 생성한 프로젝트 빌드해 보기</li>
</ul>
</li>
<li><p><strong>프로젝트 표준 구조</strong></p>
<ul>
<li><code>src/main/java</code>, <code>resources</code>, <code>src/test</code> 관습</li>
<li>패키지 설계(Controller, Service, Repository, domain/entity, dto 등)</li>
</ul>
</li>
</ul>
<h1 id="개발-환경--도구">개발 환경 / 도구</h1>
<ul>
<li><strong>IDE (IntelliJ IDEA 권장)</strong>: 코드 네비게이션, 디버깅, 리팩토링</li>
<li><strong>버전 관리(Git)</strong>: 브랜치 전략(Feature/Develop/Main), PR/Code Review</li>
<li><strong>로컬 DB 도구</strong>: H2, MySQL, PostgreSQL 사용법</li>
<li><strong>빌드/배포 도구 기초</strong>: Docker, CI (GitHub Actions/GitLab CI 등) 이해</li>
</ul>
<h1 id="spring-boot-기본-개념">Spring Boot 기본 개념</h1>
<ul>
<li><p><strong>Spring IoC / DI</strong></p>
<ul>
<li><code>@Component</code>, <code>@Service</code>, <code>@Repository</code>, <code>@Controller</code>, <code>@Bean</code></li>
<li>의존성 주입 방식: 생성자 주입(권장), 필드 주입의 문제</li>
<li>실습: 빈 스코프와 라이프사이클 확인</li>
</ul>
</li>
<li><p><strong>Auto-configuration / Starter</strong></p>
<ul>
<li>Starter 의존성의 역할(<code>spring-boot-starter-web</code>, <code>spring-boot-starter-data-jpa</code>)</li>
</ul>
</li>
<li><p><strong>Application properties / YAML</strong></p>
<ul>
<li><code>application.properties</code> / <code>application.yml</code>, 프로파일(<code>spring.profiles.active</code>)</li>
<li>외부 설정(환경 변수) 우선순위</li>
</ul>
</li>
<li><p><strong>의존성 주입과 테스트용 설정 분리</strong></p>
</li>
</ul>
<h1 id="web--api">Web / API</h1>
<ul>
<li><p><strong>Spring MVC 기본</strong></p>
<ul>
<li><code>@RestController</code>, <code>@Controller</code>, <code>@RequestMapping</code>, <code>@GetMapping</code> 등</li>
<li>요청 파라미터 바인딩: <code>@RequestParam</code>, <code>@PathVariable</code>, <code>@RequestBody</code></li>
</ul>
</li>
<li><p><strong>HTTP와 REST 원칙</strong></p>
<ul>
<li>상태 코드, idempotency, 리소스 설계</li>
</ul>
</li>
<li><p><strong>예외 처리</strong></p>
<ul>
<li><code>@ControllerAdvice</code>, <code>@ExceptionHandler</code>로 일관된 에러 응답</li>
</ul>
</li>
<li><p><strong>검증</strong></p>
<ul>
<li><code>javax.validation</code> (<code>@Valid</code>, <code>@NotNull</code>, <code>@Size</code>) + <code>BindingResult</code></li>
</ul>
</li>
<li><p><strong>API 문서화</strong></p>
<ul>
<li>OpenAPI/Swagger (기본 개념, 스펙 노출)</li>
</ul>
</li>
</ul>
<h1 id="spring-data-jpa-핵심">Spring Data JPA 핵심</h1>
<ul>
<li><p><strong>엔티티 기초</strong></p>
<ul>
<li><code>@Entity</code>, <code>@Table</code>, <code>@Id</code>, 식별 전략(<code>@GeneratedValue</code>)</li>
<li>필드 매핑: <code>@Column</code>, <code>@Enumerated</code>, <code>@Temporal</code> 등</li>
<li><code>equals</code>/<code>hashCode</code> 설계(식별자 기준 vs 비즈니스 키)</li>
</ul>
</li>
<li><p><strong>관계 매핑</strong></p>
<ul>
<li><code>@ManyToOne</code>, <code>@OneToMany</code>, <code>@OneToOne</code>, <code>@ManyToMany</code></li>
<li>연관관계의 주인(owner) 설정, 양방향 설계의 주의점</li>
<li><code>cascade</code>, <code>orphanRemoval</code> 용도와 위험성</li>
</ul>
</li>
<li><p><strong>Fetch 전략</strong></p>
<ul>
<li>LAZY vs EAGER, N+1 문제의 원인과 해결(페치 조인, 엔티티 그래프)</li>
</ul>
</li>
<li><p><strong>영속성 컨텍스트 및 생명주기</strong></p>
<ul>
<li>영속(Managed) 상태, 준영속/비영속, Detached 상태 이해</li>
<li><code>EntityManager</code>의 역할, <code>persist</code>, <code>merge</code>, <code>remove</code></li>
</ul>
</li>
<li><p><strong>변경 감지(Dirty Checking)</strong></p>
<ul>
<li>트랜잭션 내에서 엔티티 상태 변경 시 자동 반영 원리</li>
</ul>
</li>
<li><p><strong>플러시(Flush) 시점</strong></p>
<ul>
<li>커밋, 쿼리 실행 시 플러시, 수동 <code>flush()</code>의 필요성</li>
</ul>
</li>
<li><p><strong>Repository</strong></p>
<ul>
<li><code>JpaRepository</code>, <code>CrudRepository</code> 메서드, Query method 문법</li>
<li><code>@Query</code>로 JPQL/NativeQuery 작성</li>
</ul>
</li>
<li><p><strong>JPQL, Criteria API, QueryDSL</strong></p>
<ul>
<li>JPQL 기초, 동적 쿼리 필요 시 Criteria 또는 QueryDSL 적용</li>
</ul>
</li>
<li><p><strong>트랜잭션 경계</strong></p>
<ul>
<li><code>@Transactional</code> 사용 위치(서비스 레이어에 걸기 권장)</li>
<li>전파 속성(Propagation), 격리 수준(Isolation), 읽기 전용 옵션</li>
</ul>
</li>
<li><p><strong>성능 고려</strong></p>
<ul>
<li>페이징 처리(OFFSET/LIMIT 문제), batch insert/update, JDBC batch 설정</li>
<li>인덱스 설계, 쿼리 튜닝</li>
</ul>
</li>
</ul>
<h1 id="데이터베이스-기초">데이터베이스 기초</h1>
<ul>
<li><p><strong>관계형 DB 이해</strong></p>
<ul>
<li>SQL 기본, JOIN, 트랜잭션(ACID), 인덱스 원리</li>
</ul>
</li>
<li><p><strong>스키마 설계</strong></p>
<ul>
<li>정규화 vs 실무적 비정규화 판단</li>
</ul>
</li>
<li><p><strong>마이그레이션</strong></p>
<ul>
<li>Flyway / Liquibase 개념(버전 관리 기반 스키마 변경)</li>
</ul>
</li>
<li><p><strong>테스트용 인메모리 DB (H2)</strong></p>
</li>
</ul>
<h1 id="설계-관점-도메인-중심">설계 관점 (도메인 중심)</h1>
<ul>
<li><p><strong>엔티티 vs DTO 분리</strong></p>
<ul>
<li>엔티티는 도메인 규칙 포함, 외부 API는 DTO 사용</li>
</ul>
</li>
<li><p><strong>서비스 계층 책임</strong></p>
<ul>
<li>트랜잭션 경계, 유즈케이스(Use Case) 중심 설계</li>
</ul>
</li>
<li><p><strong>도메인 모델링</strong></p>
<ul>
<li>도메인 행위(비즈니스 로직)를 엔티티/도메인 서비스에 위치시키기</li>
</ul>
</li>
<li><p><strong>계층 간 의존성</strong></p>
<ul>
<li>Controller -&gt; Service -&gt; Repository 방향 유지</li>
</ul>
</li>
<li><p><strong>계약(인터페이스) 중심 설계</strong></p>
<ul>
<li>테스트 가능성과 느슨한 결합을 위해 인터페이스 활용</li>
</ul>
</li>
</ul>
<h1 id="테스트-단위통합">테스트 (단위/통합)</h1>
<ul>
<li><p><strong>단위 테스트</strong></p>
<ul>
<li>Mockito, JUnit 사용법, 서비스 로직 단위 테스트</li>
</ul>
</li>
<li><p><strong>통합 테스트</strong></p>
<ul>
<li><code>@SpringBootTest</code>, <code>@DataJpaTest</code>로 JPA 연동 테스트</li>
<li>Testcontainers 사용해 실제 DB 환경에서 검증(선택)</li>
</ul>
</li>
<li><p><strong>테스트 데이터 관리</strong></p>
<ul>
<li>Fixture, Factory (예: Builder 패턴), DB 초기화 전략</li>
</ul>
</li>
</ul>
<h1 id="운영배포모니터링">운영/배포/모니터링</h1>
<ul>
<li><p><strong>Spring Boot Actuator</strong></p>
<ul>
<li><code>/actuator/health</code>, 메트릭, 엔드포인트 개념</li>
</ul>
</li>
<li><p><strong>로깅</strong></p>
<ul>
<li>SLF4J/Logback 설정, 로그 레벨 관리, 포맷과 구조화 로그(JSON)</li>
</ul>
</li>
<li><p><strong>모니터링</strong></p>
<ul>
<li>메트릭(프로메테우스), APM(예: Zipkin, Jaeger) 활용 개념</li>
</ul>
</li>
<li><p><strong>프로파일/환경 분리</strong></p>
<ul>
<li><code>dev</code>, <code>staging</code>, <code>prod</code> 설정 분리</li>
</ul>
</li>
<li><p><strong>배포</strong></p>
<ul>
<li>JAR 배포, Docker 이미지화, 배포 스크립트/CI 파이프라인 기초</li>
</ul>
</li>
</ul>
<h1 id="보안-기초">보안 (기초)</h1>
<ul>
<li><p><strong>Spring Security 개념</strong></p>
<ul>
<li>인증(Authentication) vs 인가(Authorization)</li>
<li>기본 설정, JWT 기반 인증, CSRF 개념</li>
</ul>
</li>
<li><p><strong>민감 정보 취급 주의</strong></p>
<ul>
<li>패스워드 해싱(BCrypt), 시크릿 관리</li>
</ul>
</li>
</ul>
<h1 id="성능--안정성-고려사항">성능 &amp; 안정성 고려사항</h1>
<ul>
<li><p><strong>N+1 문제 탐지 및 해결</strong></p>
<ul>
<li>로그로 SQL 확인, Fetch Join 사용</li>
</ul>
</li>
<li><p><strong>쿼리 최적화</strong></p>
<ul>
<li>실행 계획(EXPLAIN), 인덱스 추가</li>
</ul>
</li>
<li><p><strong>캐싱</strong></p>
<ul>
<li>1차 캐시(JPA), 2차 캐시(선택), Redis 캐시 도입 시 고려 사항</li>
</ul>
</li>
<li><p><strong>비동기 처리</strong></p>
<ul>
<li><code>@Async</code>, 메시지 큐(RabbitMQ, Kafka) 도입 판단</li>
</ul>
</li>
<li><p><strong>장애 대응</strong></p>
<ul>
<li>타임아웃, 재시도, Circuit Breaker(Resilience4j/Hystrix 개념)</li>
</ul>
</li>
</ul>
<h1 id="설계·코드-품질--유지보수">설계·코드 품질 &amp; 유지보수</h1>
<ul>
<li><p><strong>코드 컨벤션/정적분석</strong></p>
<ul>
<li>SonarQube, Checkstyle, SpotBugs</li>
</ul>
</li>
<li><p><strong>리팩터링 원칙</strong></p>
<ul>
<li>작은 단계로 안전하게 리팩터링(테스트 우선)</li>
</ul>
</li>
<li><p><strong>로그/추적 전략</strong></p>
<ul>
<li>Request ID 전달, 분산 추적(TraceId), 적절한 로그 레벨 전략</li>
</ul>
</li>
</ul>
<h1 id="실무-팁--체크리스트">실무 팁 / 체크리스트</h1>
<ul>
<li><strong>트랜잭션은 서비스 계층에</strong> 두고, 컨트롤러에서는 트랜잭션 관리하지 않기</li>
<li><strong>엔티티에 비즈니스 로직을 담되</strong>, 복잡한 로직은 도메인 서비스로 분리</li>
<li><strong>DTO를 통한 계층 분리</strong>: 엔티티를 외부로 직접 노출하지 않기</li>
<li><strong>쿼리는 먼저 로그/프로파일링으로 확인</strong>하고 N+1/전체 스캔 방지</li>
<li><strong>마이그레이션 전략 포함한 DB 버전 관리</strong> 필수</li>
<li><strong>CI 파이프라인에서 테스트와 린트/빌드가 자동 실행</strong>되도록 구성</li>
</ul>
<h1 id="권장-학습-순서-실습-중심">권장 학습 순서 (실습 중심)</h1>
<ol>
<li>Java 문법/OO 기초 확실히 하고 Git으로 프로젝트 관리하기</li>
<li>Spring Boot 기본 프로젝트 생성 (<code>spring-boot-starter-web</code> + <code>data-jpa</code>)</li>
<li>간단 CRUD API와 JPA 엔티티 만들어 로컬 DB(MySQL/H2)로 동작 확인</li>
<li>트랜잭션/영속성 컨텍스트 실험: 변경 감지, detach/merge 실습</li>
<li>페치 전략과 N+1 문제 재현 및 해결 (페치 조인)</li>
<li>DTO, Validation, 예외처리, 테스트 추가</li>
<li>Flyway로 마이그레이션 관리, Actuator로 헬스 체크 추가</li>
<li>Dockerize + 간단 CI(깃훅/Actions) 파이프라인 만들기</li>
</ol>
<h1 id="마무리--우선순위-요약">마무리 — 우선순위 요약</h1>
<ul>
<li><strong>먼저 알아야 할 것</strong>: Java 기초, Spring Boot 기본(컨트롤러/서비스/레포지토리), JPA 엔티티와 트랜잭션</li>
<li><strong>중간 단계</strong>: 관계 매핑, Fetch 전략, N+1 문제, DTO 패턴, 테스트</li>
<li><strong>고급/운영</strong>: 마이그레이션(Flyway), 프로파일/배포, 모니터링(Actuator, 메트릭), 보안(Spring Security)</li>
</ul>