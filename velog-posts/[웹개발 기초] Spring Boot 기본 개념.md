<h1 id="spring-boot-기본-개념-정리">Spring Boot 기본 개념 정리</h1>
<p>Spring Boot는 스프링 애플리케이션을 쉽고 빠르게 개발할 수 있도록 도와주는 프레임워크이다. 스프링의 핵심 철학인 IoC/DI, 빈 관리, 설정 자동화 등을 토대로 현대적인 개발 환경을 제공한다. 이 글에서는 초보 백엔드 개발자가 반드시 이해해야 하는 Spring Boot의 기본 개념을 정리한다.</p>
<hr />
<h1 id="1-spring-ioc--di-기본-개념">1. Spring IoC / DI 기본 개념</h1>
<h2 id="11-iocinversion-of-control">1.1 IoC(Inversion of Control)</h2>
<p>IoC는 객체 생성과 의존성 관리를 개발자가 아닌 <strong>스프링 컨테이너가 담당하는 구조</strong>를 의미한다.
일반적으로 개발자가 <code>new</code>로 생성하던 객체를 스프링이 대신 생성하고 관리하며, 필요한 객체를 주입해준다.</p>
<h2 id="12-didependency-injection">1.2 DI(Dependency Injection)</h2>
<p>DI는 객체 간의 의존 관계를 외부에서 주입하는 기법이다.
스프링에서는 주로 <strong>생성자 주입</strong> 방식이 권장된다.</p>
<hr />
<h1 id="2-스프링에서-빈bean을-등록하는-방법">2. 스프링에서 빈(Bean)을 등록하는 방법</h1>
<p>스프링 IoC 컨테이너가 관리하는 객체를 <strong>Bean</strong>이라고 한다.
Spring Boot에서는 다음과 같은 애노테이션으로 빈을 등록한다.</p>
<h3 id="21-컴포넌트-계열-애노테이션">2.1 컴포넌트 계열 애노테이션</h3>
<ul>
<li><code>@Component</code> : 일반적인 빈 등록</li>
<li><code>@Service</code> : 비즈니스 로직 계층</li>
<li><code>@Repository</code> : 데이터 접근 계층 (JPA 예외 변환 기능 포함)</li>
<li><code>@Controller</code> : MVC 웹 컨트롤러</li>
</ul>
<p>이 애노테이션들은 모두 <strong>@Component 기반</strong>으로 동작한다.</p>
<h3 id="22-bean을-통한-수동-등록">2.2 @Bean을 통한 수동 등록</h3>
<p>특정 객체를 직접 생성해서 등록해야 할 때 사용한다.</p>
<pre><code class="language-java">@Configuration
public class AppConfig {

    @Bean
    public PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();
    }
}</code></pre>
<hr />
<h1 id="3-의존성-주입-방식">3. 의존성 주입 방식</h1>
<h3 id="31-생성자-주입권장">3.1 생성자 주입(권장)</h3>
<ul>
<li>불변성 보장</li>
<li>테스트 용이</li>
<li>순환 참조 조기 탐지 가능</li>
</ul>
<pre><code class="language-java">@Service
public class UserService {

    private final UserRepository repository;

    public UserService(UserRepository repository) {
        this.repository = repository;
    }
}</code></pre>
<h3 id="32-필드-주입의-문제">3.2 필드 주입의 문제</h3>
<pre><code class="language-java">@Autowired
private UserRepository repository;</code></pre>
<p>문제점:</p>
<ul>
<li>테스트에서 mock 객체를 주입하기 어렵다.</li>
<li>DI 프레임워크에 강하게 결합된다.</li>
<li>불변성이 보장되지 않는다.</li>
</ul>
<p>실무에서는 생성자 주입 외의 방식은 거의 사용하지 않는다.</p>
<hr />
<h1 id="4-빈-스코프와-라이프사이클">4. 빈 스코프와 라이프사이클</h1>
<h3 id="41-주요-빈-스코프">4.1 주요 빈 스코프</h3>
<ul>
<li><strong>singleton (기본값)</strong>: 컨테이너 내 하나만 존재</li>
<li>prototype: 요청할 때마다 새로 생성</li>
<li>request/session: 웹 요청/세션 단위</li>
</ul>
<h3 id="42-빈-라이프사이클">4.2 빈 라이프사이클</h3>
<p>스프링은 빈 생성 ↔ 의존성 주입 ↔ 초기화 ↔ 소멸의 과정을 관리한다.</p>
<p>초기화/소멸 메서드 정의 예:</p>
<pre><code class="language-java">@PostConstruct
public void init() {
    // 초기화 로직
}

@PreDestroy
public void close() {
    // 종료 로직
}</code></pre>
<hr />
<h1 id="5-spring-boot-auto-configuration--starter">5. Spring Boot Auto-Configuration / Starter</h1>
<p>스프링의 번거로운 설정을 자동으로 구성해주는 기능이 <strong>Auto-Configuration</strong>이다.</p>
<p>예를 들어:</p>
<ul>
<li><code>spring-boot-starter-web</code>을 추가하면 → 톰캣, MVC, Jackson 설정이 자동 적용</li>
<li><code>spring-boot-starter-data-jpa</code>를 추가하면 → Hibernate, JPA 설정이 자동 적용</li>
</ul>
<h3 id="51-starter-의존성의-역할">5.1 Starter 의존성의 역할</h3>
<p>Starter는 특정 기술 세트를 쉽게 가져오기 위한 일종의 <strong>패키지 묶음</strong>이다.</p>
<p>대표적인 Starter:</p>
<ul>
<li><code>spring-boot-starter-web</code></li>
<li><code>spring-boot-starter-data-jpa</code></li>
<li><code>spring-boot-starter-validation</code></li>
<li><code>spring-boot-starter-security</code></li>
</ul>
<p>설정 파일 없이도 서버 실행이 가능한 것은 이 자동 설정 덕분이다.</p>
<hr />
<h1 id="6-application-properties--yaml">6. Application Properties / YAML</h1>
<p>Spring Boot는 외부 설정을 파일로 관리한다.
기본 위치는 다음과 같다.</p>
<pre><code>src/main/resources/application.properties
src/main/resources/application.yml</code></pre><h3 id="61-properties-vs-yaml">6.1 properties vs YAML</h3>
<ul>
<li>단일 라인 구성은 properties가 단순</li>
<li>계층 구조는 YAML이 가독성이 좋음</li>
</ul>
<p>예:</p>
<pre><code class="language-yaml">spring:
  datasource:
    url: jdbc:mysql://localhost:3306/test
    username: root</code></pre>
<hr />
<h1 id="7-프로파일profile-설정">7. 프로파일(Profile) 설정</h1>
<p>서비스 운영 환경에 따라 서로 다른 설정을 적용해야 할 때 프로파일을 사용한다.</p>
<h3 id="71-프로파일-적용-방식">7.1 프로파일 적용 방식</h3>
<p><code>application.yml</code></p>
<pre><code class="language-yaml">spring:
  profiles:
    active: local</code></pre>
<p><code>application-local.yml</code>
<code>application-prod.yml</code></p>
<p>각 환경별로 설정을 분리할 수 있다.</p>
<hr />
<h1 id="8-외부-설정-우선순위">8. 외부 설정 우선순위</h1>
<p>Spring Boot는 다양한 외부 설정을 읽어들이며, 다음 우선순위를 따른다(위가 더 높음).</p>
<ol>
<li>명령행 인자</li>
<li>환경 변수</li>
<li><code>application-{profile}.yml</code></li>
<li><code>application.yml</code></li>
<li>기본값</li>
</ol>
<p>Docker 배포, 클라우드 환경에서 환경 변수를 자주 사용하는 이유가 이 때문이다.</p>
<hr />
<h1 id="9-의존성-주입과-테스트-설정-분리">9. 의존성 주입과 테스트 설정 분리</h1>
<p>스프링은 테스트 코드에서 별도의 설정을 적용할 수 있다.</p>
<p>예:</p>
<pre><code class="language-java">@SpringBootTest
@ActiveProfiles(&quot;test&quot;)
public class UserServiceTest {
}</code></pre>
<p>이렇게 하면 <code>application-test.yml</code>이 적용된다.</p>
<p>테스트에서는 H2 Database 등을 사용하고, 운영에서는 MySQL/PostgreSQL을 사용하는 방식이 일반적이다.</p>
<hr />
<h1 id="마무리">마무리</h1>
<p>Spring Boot의 핵심은 <strong>스프링 컨테이너가 모든 객체를 관리하며</strong>, 개발자는 비즈니스 로직에만 집중할 수 있도록 지원한다는 점이다.
DI 원칙, 빈 관리, 자동 설정, 프로파일, 외부 설정 우선순위를 이해하면 이후 Web MVC, JPA, Security 등 실무 기술을 학습하는 데 훨씬 수월하다.</p>