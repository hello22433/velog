<h1 id="-jpaaudit-에-대해서-기초부터-알려줄래">? JpaAudit 에 대해서 기초부터 알려줄래?</h1>
<hr />
<h1 id="1-jpaaudit이란-무엇인가">1. JpaAudit이란 무엇인가</h1>
<p><strong>JpaAudit(JPA Auditing)</strong> 은 엔티티가 <strong>언제 생성되고 수정되었는지</strong>, 그리고 <strong>누가 만들고 수정했는지</strong>를 자동으로 기록해주는 기능이다.</p>
<p>보통 서비스 개발에서는 거의 모든 테이블에 다음 컬럼이 공통으로 존재한다.</p>
<ul>
<li>created_at</li>
<li>updated_at</li>
<li>created_by</li>
<li>updated_by</li>
</ul>
<p>이걸 매번 서비스 로직에서 일일이 넣어줬다면, 코드가 지저분해지고 유지보수가 어렵다.
이를 JPA가 자동으로 처리하도록 하는 기능이 <strong>Auditing</strong>이다.</p>
<hr />
<h1 id="2-왜-필요한가">2. 왜 필요한가</h1>
<h3 id="1-중복-코드-제거">(1) 중복 코드 제거</h3>
<p>서비스, 컨트롤러에서 매번 <code>LocalDateTime.now()</code>를 넣을 필요가 없다.</p>
<h3 id="2-실수-방지">(2) 실수 방지</h3>
<p>업데이트할 때 updated_at을 깜빡한다든지, created_by를 잘못 넣는 실수를 줄일 수 있다.</p>
<h3 id="3-일관성-유지">(3) 일관성 유지</h3>
<p>프로젝트 전반에서 동일한 규칙으로 생성/수정 정보를 기록할 수 있다.</p>
<hr />
<h1 id="3-어떻게-동작하는가">3. 어떻게 동작하는가</h1>
<p>JPA Auditing은 크게 두 가지 요소로 구성된다.</p>
<ol>
<li><strong>Auditing을 활성화하는 설정</strong></li>
<li><strong>Auditing 전용 어노테이션이 붙은 엔티티</strong></li>
</ol>
<hr />
<h2 id="3-1-auditing-활성화">3-1. Auditing 활성화</h2>
<p>Spring Boot에서 보통 다음 설정 하나면 끝난다.</p>
<pre><code class="language-java">@EnableJpaAuditing
@SpringBootApplication
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}</code></pre>
<p>여기서 <code>@EnableJpaAuditing</code> 이 중요하다.
이걸 통해 JPA가 엔티티의 생성·수정 시점을 감지하고 값을 넣어줄 수 있다.</p>
<hr />
<h2 id="3-2-audit-정보를-담는-필드-선언">3-2. Audit 정보를 담는 필드 선언</h2>
<p>보통 추상 클래스를 하나 만들어 상속한다.</p>
<pre><code class="language-java">@MappedSuperclass
@EntityListeners(AuditingEntityListener.class)
public abstract class BaseTimeEntity {

    @CreatedDate
    private LocalDateTime createdAt;

    @LastModifiedDate
    private LocalDateTime updatedAt;
}</code></pre>
<p>여기서 중요한 어노테이션:</p>
<ul>
<li><code>@CreatedDate</code>: 엔티티가 <strong>처음 저장될 때</strong> 자동으로 값 삽입</li>
<li><code>@LastModifiedDate</code>: 엔티티가 <strong>변경될 때마다</strong> 자동으로 갱신</li>
<li><code>@MappedSuperclass</code>: 이 클래스를 상속하는 엔티티가 컬럼을 가져감</li>
<li><code>@EntityListeners(AuditingEntityListener.class)</code>: JPA가 변경 이벤트를 감지하기 위함</li>
</ul>
<hr />
<h2 id="3-3-엔티티에-적용">3-3. 엔티티에 적용</h2>
<p>예를 들면 Product 엔티티에 적용하면 다음과 같다.</p>
<pre><code class="language-java">@Entity
public class Product extends BaseTimeEntity {

    @Id @GeneratedValue
    private Long id;

    private String name;
}</code></pre>
<p>이제 Product를 저장하면:</p>
<ul>
<li>created_at → 자동 입력</li>
<li>updated_at → created_at과 동일하게 자동 입력</li>
</ul>
<p>수정하면:</p>
<ul>
<li>updated_at 만 갱신</li>
</ul>
<hr />
<h1 id="4-created_by--updated_by까지-넣으려면">4. created_by / updated_by까지 넣으려면?</h1>
<p>유저 정보까지 넣으려면 <code>@CreatedBy</code>, <code>@LastModifiedBy</code>를 사용한다.</p>
<pre><code class="language-java">@CreatedBy
private String createdBy;

@LastModifiedBy
private String updatedBy;</code></pre>
<p>하지만 <strong>스프링이 알아서 사용자 정보를 알 수는 없으므로</strong>,
반드시 AuditorAware 구현체를 만들어서 “현재 로그인한 사용자”를 알려줘야 한다.</p>
<p>예시:</p>
<pre><code class="language-java">@Component
public class AuditorAwareImpl implements AuditorAware&lt;String&gt; {
    @Override
    public Optional&lt;String&gt; getCurrentAuditor() {
        // 예: Spring Security에서 username 가져오기
        return Optional.of(&quot;hello22433&quot;);
    }
}</code></pre>
<hr />
<h1 id="5-실무에서의-일반적인-구조">5. 실무에서의 일반적인 구조</h1>
<h3 id="5-1-시간을-위한-기본-클래스">5-1. 시간을 위한 기본 클래스</h3>
<pre><code class="language-java">@MappedSuperclass
@EntityListeners(AuditingEntityListener.class)
public abstract class BaseTime {
    @CreatedDate
    private LocalDateTime createdAt;

    @LastModifiedDate
    private LocalDateTime updatedAt;
}</code></pre>
<h3 id="5-2-사용자-정보까지-포함한-클래스">5-2. 사용자 정보까지 포함한 클래스</h3>
<pre><code class="language-java">@MappedSuperclass
public abstract class BaseEntity extends BaseTime {
    @CreatedBy
    private String createdBy;

    @LastModifiedBy
    private String updatedBy;
}</code></pre>
<h3 id="5-3-엔티티에서-상속">5-3. 엔티티에서 상속</h3>
<pre><code class="language-java">@Entity
public class Order extends BaseEntity {
    ...
}</code></pre>
<hr />
<h1 id="6-주의할-점">6. 주의할 점</h1>
<h3 id="1-enablejpaauditing-은-반드시-있어야-한다">(1) <code>@EnableJpaAuditing</code> 은 반드시 있어야 한다.</h3>
<p>설정하지 않으면 어노테이션이 있어도 자동 입력이 안 된다.</p>
<h3 id="2-created_by-updated_by는-security와-연동해야-진짜-유용해진다">(2) <code>created_by</code>, <code>updated_by</code>는 Security와 연동해야 진짜 유용해진다.</h3>
<h3 id="3-createddate는-최초-1번만-입력된다-수정해도-바뀌지-않는다">(3) <code>@CreatedDate</code>는 최초 1번만 입력된다. 수정해도 바뀌지 않는다.</h3>
<p>반면 <code>@LastModifiedDate</code>는 수정할 때마다 바뀐다.</p>
<h3 id="4-db에서는-대부분-datetime-timestamp와-매핑된다">(4) DB에서는 대부분 <code>datetime</code>, <code>timestamp</code>와 매핑된다.</h3>
<hr />
<h1 id="7-요약">7. 요약</h1>
<ul>
<li>JpaAudit은 엔티티의 생성·수정 시간 및 작성자 정보 자동 기록 기능</li>
<li><code>@EnableJpaAuditing</code> + Base Entity 클래스 + 어노테이션으로 동작</li>
<li>created_at, updated_at 은 거의 모든 테이블에서 필수</li>
<li>실무에서 중복 코드 제거, 변경 추적, 실수 방지에 매우 유용</li>
</ul>
<hr />
<h1 id="-좀더-심화내용-알려줄래">? 좀더 심화내용 알려줄래?</h1>
<p>아래는 <strong>실무에서 바로 써먹을 수 있는 JPA Auditing 심화 내용</strong>이다.
기본 개념을 이미 이해한 상태라 가정하고, 실제 프로젝트에서 중요한 포인트 중심으로 정리해줄게.</p>
<hr />
<h1 id="1-createdatupdatedat의-정확한-시점과-jpa-이벤트-흐름">1. createdAt/updatedAt의 정확한 시점과 JPA 이벤트 흐름</h1>
<p>JPA Auditing은 JPA의 <strong>Entity Lifecycle Callback</strong>을 사용한다.</p>
<p>즉 다음 시점에 각각 호출된다.</p>
<table>
<thead>
<tr>
<th>어노테이션</th>
<th>동작 시점</th>
</tr>
</thead>
<tbody><tr>
<td><code>@CreatedDate</code></td>
<td><code>@PrePersist</code> 직전에 호출</td>
</tr>
<tr>
<td><code>@LastModifiedDate</code></td>
<td><code>@PreUpdate</code> 직전에 호출</td>
</tr>
<tr>
<td><code>@CreatedBy</code></td>
<td><code>@PrePersist</code></td>
</tr>
<tr>
<td><code>@LastModifiedBy</code></td>
<td><code>@PreUpdate</code></td>
</tr>
</tbody></table>
<p>따라서 중요한 특징이 생긴다.</p>
<h3 id="1-update-쿼리-없이-업데이트되지-않음">(1) update 쿼리 없이 업데이트되지 않음</h3>
<p>엔티티를 수정했더라도 변경 감지가 일어나지 않으면 update 쿼리가 안 나가므로
<code>@LastModifiedDate</code>도 갱신되지 않는다.</p>
<p>예)</p>
<pre><code class="language-java">order.setStatus(order.getStatus());</code></pre>
<p>값이 같으면 dirty checking이 발생하지 않아 updatedAt도 갱신되지 않는다.</p>
<h3 id="2-flush-시점에-반영">(2) flush 시점에 반영</h3>
<p>Auditing 값은 <strong>영속성 컨텍스트에 세팅</strong>되지만, DB 반영은 flush 시점에 일어난다.
트랜잭션 경계를 명확히 알고 있어야 한다.</p>
<hr />
<h1 id="2-enablejpaauditing-등록-위치에-따른-동작-차이">2. @EnableJpaAuditing 등록 위치에 따른 동작 차이</h1>
<p>많은 실수 중 하나가 <strong>@EnableJpaAuditing을 @Configuration 클래스에 등록하는 경우</strong>인데,
그 자체는 문제 없지만 테스트 환경에서는 다음 문제가 발생한다.</p>
<h3 id="문제-1-datajpatest-에서는-자동-스캔이-안-될-수-있음">문제 1) @DataJpaTest 에서는 자동 스캔이 안 될 수 있음</h3>
<p>@DataJpaTest는 Application 전체를 로드하지 않기 때문에
Auditing 설정을 따로 넣어야 한다.</p>
<p>해결:</p>
<pre><code class="language-java">@DataJpaTest
@Import(TestJpaConfig.class)
class RepositoryTest { }</code></pre>
<hr />
<h1 id="3-createdby--updatedby와-security-연동-심화">3. createdBy / updatedBy와 Security 연동 심화</h1>
<h3 id="가장-실무적인-방식-securitycontextholder에서-username-꺼내기">가장 실무적인 방식: SecurityContextHolder에서 username 꺼내기</h3>
<pre><code class="language-java">@Component
public class AuditorAwareImpl implements AuditorAware&lt;String&gt; {

    @Override
    public Optional&lt;String&gt; getCurrentAuditor() {
        Authentication auth = SecurityContextHolder.getContext().getAuthentication();

        if (auth == null || !auth.isAuthenticated()) {
            return Optional.empty();
        }
        return Optional.of(auth.getName());
    }
}</code></pre>
<p>하지만 실제 서비스에서는 다음 문제가 발생한다.</p>
<hr />
<h2 id="문제-a-비동기-요청에서는-securitycontext가-전달되지-않음">문제 A. 비동기 요청에서는 SecurityContext가 전달되지 않음</h2>
<p>ThreadLocal 기반이라 @Async에서는 SecurityContextHolder가 비어있다.</p>
<h3 id="해결">해결</h3>
<p>SecurityContextPropagation을 해야 한다.</p>
<pre><code class="language-java">@EnableAsync
@Configuration
public class AsyncConfig implements AsyncConfigurer {

    @Override
    public Executor getAsyncExecutor() {
        return new DelegatingSecurityContextExecutor(Executors.newFixedThreadPool(8));
    }
}</code></pre>
<hr />
<h2 id="문제-b-jwt-인증에서는-username을-항상-꺼낼-수-있는게-아님">문제 B. JWT 인증에서는 username을 항상 꺼낼 수 있는게 아님</h2>
<p>이 경우 Request Filter에서 직접 SecurityContext에 넣어줘야 한다.</p>
<hr />
<h2 id="문제-c-batch--scheduler-같은-환경에서는-createdby가-null이-될-수-있음">문제 C. Batch / Scheduler 같은 환경에서는 createdBy가 null이 될 수 있음</h2>
<p>보통 시스템 계정(system)으로 설정한다.</p>
<pre><code class="language-java">return Optional.of(&quot;SYSTEM&quot;);</code></pre>
<hr />
<h1 id="4-soft-delete삭제-상태-컬럼와-auditing-결합하기">4. Soft Delete(삭제 상태 컬럼)와 Auditing 결합하기</h1>
<p>대부분의 실제 서비스는 delete 쿼리를 사용하지 않고,
<code>deleted_at</code>, <code>deleted_by</code>, <code>is_deleted</code> 같은 컬럼을 사용한다.</p>
<p>예)</p>
<pre><code class="language-java">@Entity
public class Post extends BaseEntity {

    private boolean deleted = false;

    private LocalDateTime deletedAt;

    private String deletedBy;

    public void softDelete(String username) {
        this.deleted = true;
        this.deletedAt = LocalDateTime.now();
        this.deletedBy = username;
    }
}</code></pre>
<h3 id="auditing으로-삭제-시간과-삭제자를-자동화하고-싶다면">Auditing으로 삭제 시간과 삭제자를 자동화하고 싶다면?</h3>
<p>JPA Auditing만으로는 <strong>delete 이벤트를 감지하는 기능이 없다</strong>.
<code>@PreRemove</code>는 엔티티가 삭제되기 직전에만 호출된다.
Soft Delete는 remove를 호출하지 않으므로 동작하지 않는다.</p>
<h3 id="해결-방법">해결 방법</h3>
<ol>
<li>직접 Soft Delete 메서드 제공</li>
<li>@SQLDelete + @Where 적용하는 Hibernate 기능 활용</li>
<li>Querydsl BooleanBuilder로 필터링</li>
</ol>
<hr />
<h1 id="5-localdatetime-말고-offsetdatetimeinstant-쓰는-이유">5. LocalDateTime 말고 OffsetDateTime/Instant 쓰는 이유</h1>
<p>실무에서는 LocalDateTime은 권장하지 않는다.</p>
<h3 id="이유">이유</h3>
<p>타임존 정보가 없기 때문이다.</p>
<p>문제 예시)
서버는 UTC, DB는 Asia/Seoul, 애플리케이션은 로컬 LocalDateTime을 사용한다면
병합 시 혼란을 일으킬 수 있다.</p>
<h3 id="추천-방식">추천 방식</h3>
<ul>
<li>AWS, 클라우드 환경: <strong>Instant</strong> 또는 <strong>OffsetDateTime(UTC)</strong></li>
<li>여러 국가 서비스: <strong>OffsetDateTime</strong> 필수</li>
</ul>
<p>Auditing 적용 예)</p>
<pre><code class="language-java">@CreatedDate
private Instant createdAt;

@LastModifiedDate
private Instant updatedAt;</code></pre>
<hr />
<h1 id="6-db-트리거-vs-jpa-auditing-비교">6. DB 트리거 vs JPA Auditing 비교</h1>
<table>
<thead>
<tr>
<th>구분</th>
<th>JPA Auditing</th>
<th>DB Trigger</th>
</tr>
</thead>
<tbody><tr>
<td>관리 위치</td>
<td>애플리케이션</td>
<td>데이터베이스</td>
</tr>
<tr>
<td>장점</td>
<td>코드로 관리, 테스트 가능</td>
<td>앱 로직과 상관없이 항상 보장</td>
</tr>
<tr>
<td>단점</td>
<td>앱 밖에서 변경된 데이터는 감지 못함</td>
<td>DB마다 설정 방식이 다름</td>
</tr>
<tr>
<td>선호도</td>
<td>MSA 환경에서는 선호</td>
<td>금융권·레거시는 트리거 선호</td>
</tr>
</tbody></table>
<h3 id="결론">결론</h3>
<p>단일 서비스 → JPA Auditing
DB가 여러 곳에서 수정될 수 있는 환경 → 트리거</p>
<hr />
<h1 id="7-immutable-엔티티에서-auditing-사용하기">7. Immutable 엔티티에서 Auditing 사용하기</h1>
<p>예를 들어 아래처럼 값 객체 스타일 엔티티는 setter가 없다.</p>
<pre><code class="language-java">@Entity
public class Post {

    @Id @GeneratedValue
    private Long id;

    private final String title;

    @CreatedDate
    private LocalDateTime createdAt;
}</code></pre>
<p>JPA Auditing은 필드에 직접 접근하므로 setter 없어도 괜찮다.
하지만 final 필드에는 값을 대입할 수 없기 때문에
Audit 필드는 final로 만들면 안 된다.</p>
<hr />
<h1 id="8-bulk-update-시-auditing-무시됨">8. Bulk update 시 Auditing 무시됨</h1>
<p>중요한 문제.</p>
<p>다음 코드는 updated_at이 전혀 갱신되지 않는다.</p>
<pre><code class="language-java">@Modifying
@Query(&quot;UPDATE Product p SET p.status = :status&quot;)
void bulkUpdateStatus(String status);</code></pre>
<p>이유
Bulk Update는 JPA가 관리하는 영속성 컨텍스트를 건너뛴다.
따라서 <code>@LastModifiedDate</code>가 동작할 수 없다.</p>
<h3 id="해결-전략">해결 전략</h3>
<ol>
<li>직접 updatedAt 값도 업데이트하도록 쿼리에 포함</li>
<li>Bulk 작업을 최소화</li>
<li>Batch Job에서는 Auditing을 포기하고 작업 시간 자체를 기록</li>
</ol>
<hr />
<h1 id="9-baseentity를-나누는-전략">9. BaseEntity를 나누는 전략</h1>
<p>실무에서는 BaseEntity를 여러 개로 분리한다.</p>
<h3 id="기본-시간만-필요한-경우">기본 시간만 필요한 경우</h3>
<p>BaseTimeEntity
(createdAt, updatedAt)</p>
<h3 id="시간--사용자-정보까지-필요한-경우">시간 + 사용자 정보까지 필요한 경우</h3>
<p>BaseEntity
(createdAt, updatedAt, createdBy, updatedBy)</p>
<h3 id="softdelete까지-필요한-경우">SoftDelete까지 필요한 경우</h3>
<p>DeletableEntity
(+ deletedAt, deletedBy, isDeleted)</p>
<hr />
<h1 id="10-정리">10. 정리</h1>
<p>Auditing은 단순히 createdAt과 updatedAt을 넣어주는 기능을 넘어서서
실제 서비스에서는 다음을 고려해야 한다.</p>
<ul>
<li>update 시점의 dirty checking 규칙</li>
<li>SecurityContext 전달 문제</li>
<li>Scheduler/Batch 환경의 Auditor 처리</li>
<li>Soft Delete와 결합 구조</li>
<li>TimeZone/Instant 사용</li>
<li>Bulk Update에서 Auditing이 무시되는 문제</li>
<li>BaseEntity 계층화 전략</li>
<li>DB 트리거와의 역할 분배</li>
</ul>
<p>모두 제대로 알고 있어야 문제 없이 실무에서 활용할 수 있다.</p>