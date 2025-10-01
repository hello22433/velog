<p>JPA와 Hibernate를 사용하면 기본적으로 SQL을 자동으로 생성해줍니다. 하지만 실제 현업에서는 <strong>SQL을 직접 작성해야 하는 경우</strong>가 많습니다. 이번 글에서는 <strong>JPA + Hibernate 환경에서 SQL을 쓰는 방법과 실제로 가져다 쓰는 방식</strong>까지 정리해봅니다.</p>
<hr />
<h2 id="1-왜-sql을-직접-쓸까">1. 왜 SQL을 직접 쓸까?</h2>
<p>Hibernate가 자동으로 SQL을 만들어주지만, 모든 상황에서 최적의 쿼리를 보장하지는 않습니다. 다음 같은 경우에는 SQL을 직접 작성하는 게 낫습니다:</p>
<ol>
<li><p><strong>복잡한 집계/통계 쿼리</strong></p>
<ul>
<li>리뷰 개수, 평균 평점, 키워드 집계 등</li>
</ul>
</li>
<li><p><strong>성능 최적화</strong></p>
<ul>
<li>필요 이상으로 많은 데이터를 가져오는 <code>N+1 문제</code> 회피</li>
</ul>
</li>
<li><p><strong>DB 고유 기능 활용</strong></p>
<ul>
<li>MySQL의 <code>JSON_EXTRACT</code>, PostgreSQL의 <code>ARRAY_AGG</code> 같은 함수</li>
</ul>
</li>
</ol>
<hr />
<h2 id="2-sql을-직접-쓰는-방법">2. SQL을 직접 쓰는 방법</h2>
<p>Hibernate 위에서 SQL을 쓸 때는 크게 세 가지 방법이 있습니다.</p>
<hr />
<h3 id="1-jpql-java-persistence-query-language">(1) JPQL (Java Persistence Query Language)</h3>
<ul>
<li>SQL과 비슷하지만, 테이블이 아니라 <strong>엔티티 객체</strong>를 대상으로 쿼리.</li>
<li>DB 독립성이 보장됨.</li>
</ul>
<p><strong>작성 예시:</strong></p>
<pre><code class="language-java">String jpql = &quot;SELECT r FROM Review r WHERE r.store.id = :storeId&quot;;
List&lt;Review&gt; reviews = em.createQuery(jpql, Review.class)
                         .setParameter(&quot;storeId&quot;, 1L)
                         .getResultList();</code></pre>
<p>→ SQL로 번역되면:</p>
<pre><code class="language-sql">SELECT * FROM review WHERE store_id = 1;</code></pre>
<p><strong>가져다 쓰는 예시 (Service 계층):</strong></p>
<pre><code class="language-java">@Service
public class ReviewService {
    @PersistenceContext
    private EntityManager em;

    public List&lt;Review&gt; getReviewsByStore(Long storeId) {
        String jpql = &quot;SELECT r FROM Review r WHERE r.store.id = :storeId&quot;;
        return em.createQuery(jpql, Review.class)
                 .setParameter(&quot;storeId&quot;, storeId)
                 .getResultList();
    }
}</code></pre>
<hr />
<h3 id="2-native-query-순수-sql">(2) Native Query (순수 SQL)</h3>
<ul>
<li>JPQL보다 더 복잡한 쿼리를 직접 작성 가능.</li>
<li>특정 DB 기능 활용 가능.</li>
</ul>
<p><strong>작성 예시:</strong></p>
<pre><code class="language-java">String sql = &quot;SELECT store_id, COUNT(*) AS review_count &quot; +
             &quot;FROM review GROUP BY store_id&quot;;
List&lt;Object[]&gt; result = em.createNativeQuery(sql).getResultList();</code></pre>
<p><strong>가져다 쓰는 예시 (Service 계층):</strong></p>
<pre><code class="language-java">@Service
public class StatsService {
    @PersistenceContext
    private EntityManager em;

    public Map&lt;Long, Long&gt; getReviewCountPerStore() {
        String sql = &quot;SELECT store_id, COUNT(*) AS review_count FROM review GROUP BY store_id&quot;;
        List&lt;Object[]&gt; result = em.createNativeQuery(sql).getResultList();

        Map&lt;Long, Long&gt; stats = new HashMap&lt;&gt;();
        for (Object[] row : result) {
            Long storeId = ((Number) row[0]).longValue();
            Long count = ((Number) row[1]).longValue();
            stats.put(storeId, count);
        }
        return stats;
    }
}</code></pre>
<hr />
<h3 id="3-spring-data-jpa에서-query-활용">(3) Spring Data JPA에서 @Query 활용</h3>
<p>Spring Data JPA를 쓰면 <code>@Query</code> 어노테이션으로 JPQL이나 SQL을 쉽게 사용할 수 있습니다.</p>
<h4 id="jpql-사용">JPQL 사용:</h4>
<pre><code class="language-java">public interface ReviewRepository extends JpaRepository&lt;Review, Long&gt; {
    @Query(&quot;SELECT r FROM Review r WHERE r.store.id = :storeId&quot;)
    List&lt;Review&gt; findByStoreId(@Param(&quot;storeId&quot;) Long storeId);
}</code></pre>
<p><strong>가져다 쓰는 예시 (Service 계층):</strong></p>
<pre><code class="language-java">@Service
public class ReviewService {
    private final ReviewRepository reviewRepository;

    public ReviewService(ReviewRepository reviewRepository) {
        this.reviewRepository = reviewRepository;
    }

    public List&lt;Review&gt; getReviews(Long storeId) {
        return reviewRepository.findByStoreId(storeId);
    }
}</code></pre>
<h4 id="native-query-사용">Native Query 사용:</h4>
<pre><code class="language-java">public interface ReviewRepository extends JpaRepository&lt;Review, Long&gt; {
    @Query(value = &quot;SELECT * FROM review WHERE store_id = :storeId&quot;, nativeQuery = true)
    List&lt;Review&gt; findByStoreIdNative(@Param(&quot;storeId&quot;) Long storeId);
}</code></pre>
<hr />
<h2 id="3-언제-어떤-방식을-써야-할까">3. 언제 어떤 방식을 써야 할까?</h2>
<ul>
<li><strong>JPQL</strong>: 엔티티 중심 조회, DB 독립적인 쿼리를 원할 때.</li>
<li><strong>Native Query</strong>: 성능 최적화, 집계/통계, DB 특화 기능이 필요할 때.</li>
<li><strong>Spring Data JPA + @Query</strong>: 깔끔하게 Repository 계층에 SQL을 넣고 싶을 때.</li>
</ul>
<hr />
<h2 id="4-정리">4. 정리</h2>
<ul>
<li>Hibernate는 SQL을 자동으로 생성하지만, 현업에서는 SQL 직접 작성이 불가피하다.</li>
<li>JPQL, Native Query, @Query 방식으로 SQL을 쓸 수 있다.</li>
<li><strong>Service 계층</strong>에서 Repository/EntityManager를 불러와 실제로 사용하는 방식까지 알아두면 현업에 바로 적용 가능하다.</li>
</ul>
<p>👉 결론: <strong>JPA + Hibernate 환경에서는 &quot;자동 + 직접 SQL&quot;을 상황에 맞게 섞어 쓰는 것</strong>이 가장 현실적인 접근법이다.</p>