<h1 id="spring-web--api-기본-개념-정리">Spring Web / API 기본 개념 정리</h1>
<p>Spring Boot를 기반으로 한 백엔드 개발에서 Web 계층과 REST API 설계는 필수 영역이다.
이 글에서는 Spring MVC, REST 설계, 예외 처리, 검증, API 문서화까지 기본 개념을 정리한다.</p>
<hr />
<h2 id="1-spring-mvc-기본">1. Spring MVC 기본</h2>
<p>Spring MVC는 요청을 처리하고 응답을 반환하는 웹 프레임워크다.
핵심 개념은 <strong>컨트롤러 → 서비스 → 레포지토리</strong> 구조로 역할을 나누는 것이다.</p>
<h3 id="11-컨트롤러-애노테이션">1.1 컨트롤러 애노테이션</h3>
<table>
<thead>
<tr>
<th>애노테이션</th>
<th>역할</th>
</tr>
</thead>
<tbody><tr>
<td><code>@Controller</code></td>
<td>뷰를 반환하는 전통적인 MVC 컨트롤러</td>
</tr>
<tr>
<td><code>@RestController</code></td>
<td>JSON/XML 같은 객체 데이터를 반환하는 API 컨트롤러 (<code>@Controller + @ResponseBody</code>)</td>
</tr>
<tr>
<td><code>@RequestMapping</code></td>
<td>클래스/메서드 레벨 공통 URL 매핑</td>
</tr>
<tr>
<td><code>@GetMapping</code>, <code>@PostMapping</code>, <code>@PutMapping</code>, <code>@DeleteMapping</code></td>
<td>HTTP 메서드 별 URL 매핑</td>
</tr>
</tbody></table>
<p>예제:</p>
<pre><code class="language-java">@RestController
@RequestMapping(&quot;/users&quot;)
public class UserController {

    @GetMapping(&quot;/{id}&quot;)
    public UserResponse getUser(@PathVariable Long id) {
        return userService.getUser(id);
    }
}</code></pre>
<hr />
<h2 id="2-요청-파라미터-바인딩">2. 요청 파라미터 바인딩</h2>
<p>Spring MVC는 다양한 방식으로 HTTP 요청 데이터를 매핑할 수 있다.</p>
<table>
<thead>
<tr>
<th>애노테이션</th>
<th>설명</th>
</tr>
</thead>
<tbody><tr>
<td><code>@RequestParam</code></td>
<td>쿼리 파라미터 바인딩</td>
</tr>
<tr>
<td><code>@PathVariable</code></td>
<td>URL 경로 변수 바인딩</td>
</tr>
<tr>
<td><code>@RequestBody</code></td>
<td>JSON/XML 요청 바디를 객체로 매핑</td>
</tr>
</tbody></table>
<p>예:</p>
<pre><code class="language-java">@GetMapping
public List&lt;UserResponse&gt; search(@RequestParam String name) { ... }

@PostMapping
public UserResponse create(@RequestBody @Valid UserRequest request) { ... }</code></pre>
<hr />
<h2 id="3-http와-rest-원칙">3. HTTP와 REST 원칙</h2>
<p>REST API 설계 시 지켜야 할 기본 원칙:</p>
<ol>
<li><p><strong>HTTP 메서드 활용</strong></p>
<ul>
<li>GET: 조회, 안전하고 멱등(Idempotent)</li>
<li>POST: 생성</li>
<li>PUT/PATCH: 수정</li>
<li>DELETE: 삭제</li>
</ul>
</li>
<li><p><strong>상태 코드 활용</strong></p>
<ul>
<li>200 OK: 정상 처리</li>
<li>201 Created: 생성 완료</li>
<li>400 Bad Request: 클라이언트 오류</li>
<li>404 Not Found: 리소스 없음</li>
<li>500 Internal Server Error: 서버 오류</li>
</ul>
</li>
<li><p><strong>리소스 기반 설계</strong></p>
<ul>
<li>URL은 명사 중심으로 설계: <code>/users</code>, <code>/orders/{id}</code></li>
</ul>
</li>
<li><p><strong>Idempotency</strong></p>
<ul>
<li>GET, PUT, DELETE는 멱등성을 유지해야 한다.</li>
</ul>
</li>
</ol>
<hr />
<h2 id="4-예외-처리">4. 예외 처리</h2>
<p>API에서 일관된 에러 응답을 제공하기 위해 <code>@ControllerAdvice</code>와 <code>@ExceptionHandler</code>를 활용한다.</p>
<pre><code class="language-java">@ControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(ResourceNotFoundException.class)
    public ResponseEntity&lt;ErrorResponse&gt; handleNotFound(ResourceNotFoundException ex) {
        return ResponseEntity.status(HttpStatus.NOT_FOUND)
                             .body(new ErrorResponse(&quot;NOT_FOUND&quot;, ex.getMessage()));
    }
}</code></pre>
<ul>
<li>모든 컨트롤러에서 발생하는 예외를 중앙 집중식으로 처리 가능</li>
<li>클라이언트에게 일관된 형식의 에러 응답 제공</li>
</ul>
<hr />
<h2 id="5-검증validation">5. 검증(Validation)</h2>
<p>Spring MVC는 <strong>javax.validation</strong> 기반으로 요청 데이터 검증을 지원한다.</p>
<pre><code class="language-java">public class UserRequest {

    @NotNull
    private String name;

    @Size(min = 6, max = 20)
    private String password;
}</code></pre>
<p>컨트롤러에서 검증 적용:</p>
<pre><code class="language-java">@PostMapping
public UserResponse create(@RequestBody @Valid UserRequest request,
                           BindingResult bindingResult) {
    if (bindingResult.hasErrors()) {
        throw new ValidationException(bindingResult);
    }
    return userService.create(request);
}</code></pre>
<ul>
<li><code>@Valid</code>: 객체 검증 활성화</li>
<li><code>BindingResult</code>: 검증 결과 확인</li>
</ul>
<hr />
<h2 id="6-api-문서화">6. API 문서화</h2>
<p>REST API를 개발할 때 문서화는 필수다.
Spring Boot에서는 OpenAPI/Swagger를 활용하면 자동으로 API 스펙을 노출할 수 있다.</p>
<h3 id="61-기본-개념">6.1 기본 개념</h3>
<ul>
<li>OpenAPI: API 구조와 요청/응답 정의를 위한 표준</li>
<li>Swagger UI: 브라우저에서 API 테스트 및 문서 확인 가능</li>
</ul>
<h3 id="62-spring-boot-적용-예시">6.2 Spring Boot 적용 예시</h3>
<pre><code class="language-java">@SpringBootApplication
@EnableOpenApi
public class Application { }</code></pre>
<p>의존성:</p>
<pre><code class="language-gradle">implementation 'org.springdoc:springdoc-openapi-ui:2.1.0'</code></pre>
<p>브라우저 접속:</p>
<pre><code>http://localhost:8080/swagger-ui.html</code></pre><ul>
<li>엔드포인트와 요청/응답 스펙 확인 가능</li>
<li>테스트용 요청 전송 가능</li>
</ul>
<hr />
<h1 id="마무리">마무리</h1>
<p>Spring Web과 REST API의 핵심은 <strong>컨트롤러 계층의 역할 분리, 요청 데이터 매핑, REST 원칙 준수, 일관된 예외 처리, 검증, 문서화</strong>다.
이 구조를 제대로 이해하면, 실제 서비스 개발에서 안정적이고 확장 가능한 API를 설계할 수 있다.</p>