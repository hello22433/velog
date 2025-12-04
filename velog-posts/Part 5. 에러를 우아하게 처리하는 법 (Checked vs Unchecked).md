<h1 id="에러를-우아하게-처리하는-법-checked-vs-unchecked">에러를 우아하게 처리하는 법 (Checked vs Unchecked)</h1>
<blockquote>
<p><strong>&quot;백엔드 개발자가 알아야 하는 최소한의 자바&quot;</strong> 시리즈의 다섯 번째 글입니다.
이번 글에서는 많은 주니어 개발자가 어려워하는 <strong>예외 처리(Exception Handling)</strong>를 다룹니다.
단순히 <code>try-catch</code>를 사용하는 수준을 넘어, <strong>왜 요즘은 Checked Exception을 거의 쓰지 않는지</strong>, 그리고 <strong>스프링에서 전역적으로 예외를 처리하는 표준 방식</strong>을 차근차근 설명합니다.</p>
</blockquote>
<hr />
<h2 id="1-예외-처리에서-가장-많이-묻는-질문">1. 예외 처리에서 가장 많이 묻는 질문</h2>
<p>“DB 조회가 안 되면 null을 리턴할까요? 아니면 예외를 던질까요?”</p>
<p>비즈니스 로직을 작성할 때 거의 모든 개발자가 고민하는 질문입니다.
특히 자바는 예외 계층이 크고 복잡하기 때문에 어려움이 더욱 커집니다.</p>
<p>결론부터 말하자면,</p>
<p><strong>“복구할 수 없는 예외라면 잡지 말고 던져라(throw)”</strong>
그리고 처리는 스프링이 제공하는 <strong>전역 예외 처리기</strong>에게 맡기는 것이 실무 표준입니다.</p>
<hr />
<h2 id="2-checked-vs-unchecked--실무는-왜-unchecked를-선호할까">2. Checked vs Unchecked — 실무는 왜 Unchecked를 선호할까?</h2>
<p>자바의 예외는 크게 두 가지입니다.</p>
<ol>
<li><p><strong>Checked Exception (<code>Exception</code> 상속)</strong>
컴파일러가 반드시 <code>try-catch</code> 또는 <code>throws</code> 처리를 강제합니다.
대표 예: <code>IOException</code>, <code>SQLException</code></p>
</li>
<li><p><strong>Unchecked Exception (<code>RuntimeException</code> 상속)</strong>
컴파일러가 강제하지 않습니다.
대표 예: <code>IllegalArgumentException</code>, <code>NullPointerException</code></p>
</li>
</ol>
<h3 id="왜-실무에서-checked-exception이-기피될까">왜 실무에서 Checked Exception이 기피될까?</h3>
<p>스프링 기반의 웹 애플리케이션에는 다음과 같은 특징이 있습니다.</p>
<ol>
<li><p>복구 불가능한 에러가 대부분이다.
예: DB 연결 끊김, SQL 오류 → 개발자가 코드로 “복구”할 방법이 없다.</p>
</li>
<li><p>Checked Exception은 불필요한 <code>throws</code> 전파를 만든다.
계층이 깊어질수록 메소드 시그니처가 더러워지고 유지보수가 어려워진다.</p>
</li>
<li><p>스프링의 트랜잭션 규칙과도 맞지 않는다.
스프링은 기본적으로 <strong>RuntimeException(Unchecked)</strong> 발생 시 롤백한다.</p>
</li>
</ol>
<pre><code class="language-java">@Transactional
public void order() {
    saveOrder();
    // RuntimeException → 자동 롤백 O
    // IOException(Checked) → 롤백 X → 실수 유발
}</code></pre>
<p>결국 실무에서는
<strong>Checked Exception을 바로 사용하지 않고, Unchecked(RuntimeException)으로 감싸서 던지는 방식</strong>이 일반적이다.</p>
<hr />
<h2 id="3-하지-말아야-할-예외-처리-패턴">3. 하지 말아야 할 예외 처리 패턴</h2>
<h3 id="1-예외를-삼키는-코드">1) 예외를 삼키는 코드</h3>
<pre><code class="language-java">try {
    execute();
} catch (Exception e) {
    // 아무 조치도 하지 않음
}</code></pre>
<p>예외를 무시하면 시스템은 “조용히 실패한 상태”로 계속 동작하여
문제 추적이 거의 불가능해진다. 가장 위험한 안티 패턴이다.</p>
<h3 id="2-eprintstacktrace-사용">2) <code>e.printStackTrace()</code> 사용</h3>
<pre><code class="language-java">catch (Exception e) {
    e.printStackTrace(); // 비추천
}</code></pre>
<p>콘솔에만 출력되며 운영 로그 관리 체계와 맞지 않는다.
항상 로깅 프레임워크(<code>log.error()</code>)를 사용해야 한다.</p>
<hr />
<h2 id="4-스프링의-전역-예외-처리-restcontrolleradvice">4. 스프링의 전역 예외 처리: @RestControllerAdvice</h2>
<p>Controller마다 <code>try-catch</code>를 넣는 방식은 비효율적이다.
스프링은 전역적으로 예외를 처리하는 <strong>Global Exception Handler</strong>를 제공한다.</p>
<h3 id="1-에러-응답-형식-정의">1) 에러 응답 형식 정의</h3>
<pre><code class="language-java">@Getter
@AllArgsConstructor
public class ErrorResponse {
    private String code;
    private String message;
}</code></pre>
<h3 id="2-전역-예외-처리기-작성">2) 전역 예외 처리기 작성</h3>
<pre><code class="language-java">@RestControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(BusinessException.class)
    public ResponseEntity&lt;ErrorResponse&gt; handleBusinessException(BusinessException e) {
        log.error(&quot;BusinessException&quot;, e);
        return ResponseEntity
                .status(e.getErrorCode().getStatus())
                .body(new ErrorResponse(e.getErrorCode().name(), e.getMessage()));
    }

    @ExceptionHandler(Exception.class)
    public ResponseEntity&lt;ErrorResponse&gt; handleException(Exception e) {
        log.error(&quot;UnhandledException&quot;, e);
        return ResponseEntity
                .status(HttpStatus.INTERNAL_SERVER_ERROR)
                .body(new ErrorResponse(&quot;SERVER_ERROR&quot;, &quot;서버 내부 오류입니다.&quot;));
    }
}</code></pre>
<p>Service 계층에서는 필요한 곳에서 <code>throw</code>만 하면 되고,
Controller는 예외 처리 코드 없이도 항상 일관된 JSON 응답을 보낼 수 있다.</p>
<hr />
<h2 id="5-커스텀-예외는-언제-만들까">5. 커스텀 예외는 언제 만들까?</h2>
<p><code>throw new RuntimeException(&quot;회원 없음&quot;)</code>도 동작하지만,
실무에서는 상황별로 의미가 명확한 커스텀 예외를 만든다.</p>
<pre><code class="language-java">public class BusinessException extends RuntimeException { ... }

public class UserNotFoundException extends BusinessException { ... }
public class DuplicateEmailException extends BusinessException { ... }</code></pre>
<p>장점:</p>
<ul>
<li>로그 분석이 쉬워진다.</li>
<li>상황에 맞는 HTTP 상태 코드를 내려주기 쉽다.</li>
<li>예외 분류 체계를 명확히 유지할 수 있다.</li>
</ul>
<hr />
<h2 id="6-정리--좋은-예외-처리의-기준">6. 정리 — 좋은 예외 처리의 기준</h2>
<ol>
<li><strong>복구가 불가능한 예외는 잡지 말고 던진다.</strong></li>
<li><strong>Checked Exception은 최소화하고, RuntimeException 기반으로 처리한다.</strong></li>
<li><strong>전역 예외 처리(<code>@RestControllerAdvice</code>)로 응답을 일원화한다.</strong></li>
<li><strong>로깅은 반드시 Logger(<code>log.error</code>)로 기록한다.</strong></li>
<li><strong>의미 있는 커스텀 예외를 정의해 API 가독성과 유지보수성을 높인다.</strong></li>
</ol>
<p>예외 처리는 “숨기는 기술”이 아니라
<strong>문제를 명확하게 드러내고, 올바른 위치에서 처리하도록 만드는 기술</strong>이다.</p>
<hr />
<p>다음 글은 <strong>Part 6. 동시성(Concurrency)과 멀티스레딩</strong> 편입니다.
백엔드 개발에서 서버가 동시에 수백~수천 개의 요청을 처리할 때 발생하는 문제와 해결 방식을 다룹니다.</p>