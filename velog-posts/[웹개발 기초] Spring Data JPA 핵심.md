<h1 id="spring-data-jpa-핵심-정리">Spring Data JPA 핵심 정리</h1>
<p>Spring Data JPA는 <strong>Java Persistence API(JPA)</strong>를 기반으로 데이터베이스와 객체를 매핑하고, CRUD 및 쿼리 작업을 간편하게 수행할 수 있도록 돕는 스프링 모듈이다.
이 글에서는 엔티티 설계, 관계 매핑, 영속성 컨텍스트, Repository, 트랜잭션, 성능 최적화까지 핵심 내용을 정리한다.</p>
<hr />
<h2 id="1-엔티티entity-기초">1. 엔티티(Entity) 기초</h2>
<h3 id="11-엔티티-정의">1.1 엔티티 정의</h3>
<pre><code class="language-java">@Entity
@Table(name = &quot;users&quot;)
public class User {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(nullable = false, length = 50)
    private String name;
}</code></pre>
<ul>
<li><code>@Entity</code>: JPA 관리 객체</li>
<li><code>@Table</code>: 매핑 테이블 지정</li>
<li><code>@Id</code> + <code>@GeneratedValue</code>: 기본 키와 식별 전략</li>
<li><code>@Column</code>, <code>@Enumerated</code>, <code>@Temporal</code>: 필드 매핑 옵션</li>
</ul>
<h3 id="12-equalshashcode-설계">1.2 equals/hashCode 설계</h3>
<ul>
<li>식별자(id) 기준: 기본적으로 권장</li>
<li>비즈니스 키 기준: 고유 제약 조건이 있는 경우</li>
<li>주의: 영속성 컨텍스트에서 엔티티 상태 비교 시 문제 발생 가능</li>
</ul>
<hr />
<h2 id="2-관계-매핑">2. 관계 매핑</h2>
<h3 id="21-대표-매핑">2.1 대표 매핑</h3>
<ul>
<li><code>@ManyToOne</code> / <code>@OneToMany</code></li>
<li><code>@OneToOne</code></li>
<li><code>@ManyToMany</code></li>
</ul>
<h3 id="22-연관관계의-주인owner">2.2 연관관계의 주인(owner)</h3>
<ul>
<li>외래키를 관리하는 쪽이 주인</li>
<li>주인이 아닌 쪽은 <code>mappedBy</code> 사용</li>
<li>양방향 설계 시 순환 참조, 무한 루프 주의</li>
</ul>
<h3 id="23-cascade--orphanremoval">2.3 cascade &amp; orphanRemoval</h3>
<ul>
<li><strong>cascade</strong>: 부모 엔티티 상태 변화가 자식에 전파</li>
<li><strong>orphanRemoval</strong>: 자식 엔티티가 연관 해제되면 삭제</li>
<li>주의: 불필요하게 적용 시 의도치 않은 삭제 발생 가능</li>
</ul>
<h3 id="24-fetch-전략">2.4 Fetch 전략</h3>
<ul>
<li><strong>LAZY</strong> (지연 로딩, 기본값)</li>
<li><strong>EAGER</strong> (즉시 로딩)</li>
<li>N+1 문제 주의: 반복 조회 시 쿼리 폭발</li>
<li>해결: 페치 조인, 엔티티 그래프 사용</li>
</ul>
<hr />
<h2 id="3-영속성-컨텍스트-및-생명주기">3. 영속성 컨텍스트 및 생명주기</h2>
<h3 id="31-엔티티-상태">3.1 엔티티 상태</h3>
<table>
<thead>
<tr>
<th>상태</th>
<th>설명</th>
</tr>
</thead>
<tbody><tr>
<td>비영속(Transient)</td>
<td>DB와 무관, 새 객체</td>
</tr>
<tr>
<td>영속(Managed)</td>
<td>EntityManager가 관리, 변경 감지 가능</td>
</tr>
<tr>
<td>준영속(Detached)</td>
<td>DB와 분리, 영속성 컨텍스트에서 제거</td>
</tr>
</tbody></table>
<h3 id="32-entitymanager-핵심-메서드">3.2 EntityManager 핵심 메서드</h3>
<ul>
<li><code>persist()</code>: 신규 엔티티 저장</li>
<li><code>merge()</code>: 준영속 엔티티 병합</li>
<li><code>remove()</code>: 삭제</li>
</ul>
<h3 id="33-변경-감지dirty-checking">3.3 변경 감지(Dirty Checking)</h3>
<ul>
<li>트랜잭션 내에서 엔티티 속성 변경 → 자동 UPDATE 쿼리 반영</li>
<li><code>flush()</code>: 영속성 컨텍스트 내용을 DB에 반영</li>
</ul>
<hr />
<h2 id="4-repository">4. Repository</h2>
<h3 id="41-jparepository--crudrepository">4.1 JpaRepository / CrudRepository</h3>
<ul>
<li>CRUD, 페이징, 정렬 등 기본 제공</li>
<li>Query Method: 메서드 이름으로 자동 쿼리 생성</li>
</ul>
<pre><code class="language-java">List&lt;User&gt; findByNameAndAge(String name, int age);</code></pre>
<h3 id="42-query">4.2 @Query</h3>
<ul>
<li>JPQL 또는 Native Query 작성 가능</li>
</ul>
<pre><code class="language-java">@Query(&quot;SELECT u FROM User u WHERE u.name = :name&quot;)
List&lt;User&gt; findByName(@Param(&quot;name&quot;) String name);</code></pre>
<h3 id="43-동적-쿼리">4.3 동적 쿼리</h3>
<ul>
<li>Criteria API: 타입 안전, 동적 조건 생성</li>
<li>QueryDSL: 문법 간결, 복잡한 조건 표현 용이</li>
</ul>
<hr />
<h2 id="5-트랜잭션-경계">5. 트랜잭션 경계</h2>
<h3 id="51-transactional-사용-위치">5.1 @Transactional 사용 위치</h3>
<ul>
<li>서비스 레이어에 선언 권장</li>
<li>읽기 전용 옵션: <code>@Transactional(readOnly = true)</code></li>
</ul>
<h3 id="52-전파-속성propagation">5.2 전파 속성(Propagation)</h3>
<ul>
<li>REQUIRED, REQUIRES_NEW 등 호출 관계에서 트랜잭션 범위 제어</li>
</ul>
<h3 id="53-격리-수준isolation">5.3 격리 수준(Isolation)</h3>
<ul>
<li>READ_COMMITTED, SERIALIZABLE 등 동시성 제어</li>
</ul>
<hr />
<h2 id="6-성능-고려">6. 성능 고려</h2>
<h3 id="61-페이징-처리">6.1 페이징 처리</h3>
<ul>
<li>OFFSET/LIMIT는 대량 데이터에서 성능 저하 가능</li>
<li>커서 기반 페이징 또는 Keyset Pagination 고려</li>
</ul>
<h3 id="62-batch-처리">6.2 Batch 처리</h3>
<ul>
<li>JDBC batch insert/update 설정</li>
<li>@BatchSize, <code>hibernate.jdbc.batch_size</code> 활용</li>
</ul>
<h3 id="63-인덱스-설계--쿼리-튜닝">6.3 인덱스 설계 &amp; 쿼리 튜닝</h3>
<ul>
<li>조회 컬럼에 인덱스 추가</li>
<li>JPQL 최적화 및 N+1 문제 해결</li>
</ul>
<hr />
<h1 id="마무리">마무리</h1>
<p>Spring Data JPA는 <strong>객체 중심의 데이터 접근</strong>과 <strong>트랜잭션 관리, 성능 최적화</strong>를 함께 제공한다.
엔티티 설계, 관계 매핑, 영속성 컨텍스트 이해, Repository 활용, 트랜잭션 전략, 성능 고려까지 기본을 잘 잡으면 실무에서 안정적이고 확장 가능한 데이터 계층을 구축할 수 있다.</p>