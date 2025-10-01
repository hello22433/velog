<h2 id="jpa란-뭘까">JPA란 뭘까?</h2>
<p>자바로 프로그램을 만들다 보면, 데이터를 데이터베이스(MySQL, Oracle 같은 곳)에 저장해야 할 때가 많습니다.
그런데 문제는 자바는 <strong>객체(Object)</strong> 를 다루고, 데이터베이스는 <strong>테이블(Table)</strong> 을 다룬다는 거예요.
둘의 세계가 다르기 때문에 매번 SQL을 직접 써서 데이터를 넣고 꺼내야 했습니다.</p>
<p>여기서 JPA가 등장합니다.
JPA는 <strong>“자바 객체와 데이터베이스 테이블을 자동으로 연결해주는 번역기”</strong> 라고 생각하면 돼요.</p>
<hr />
<h2 id="왜-jpa를-쓰는-걸까">왜 JPA를 쓰는 걸까?</h2>
<ol>
<li><p><strong>SQL을 직접 안 써도 된다</strong>
예전에는 회원을 저장하려면 이렇게 해야 했습니다:</p>
<pre><code class="language-sql">INSERT INTO user (id, name) VALUES (1, '철수');</code></pre>
<p>JPA를 쓰면 그냥 이렇게만 하면 돼요:</p>
<pre><code class="language-java">userRepository.save(user);</code></pre>
</li>
<li><p><strong>개발 속도가 빨라진다</strong>
매번 SQL을 손으로 안 쓰니까 훨씬 빠르고, 버그도 줄어듭니다.</p>
</li>
<li><p><strong>DB를 바꿔도 유연하다</strong>
MySQL 쓰다가 PostgreSQL로 바꿔도, 코드 대부분은 그대로 쓸 수 있습니다.</p>
</li>
</ol>
<hr />
<h2 id="jpa에서-자주-보는-개념">JPA에서 자주 보는 개념</h2>
<ul>
<li><p><strong>Entity (엔티티)</strong>
→ 데이터베이스 테이블과 연결되는 자바 클래스
예: <code>User</code>, <code>Store</code>, <code>Review</code></p>
</li>
<li><p><strong>EntityManager (엔티티 매니저)</strong>
→ 엔티티를 저장하고, 꺼내고, 지우는 일을 대신해주는 관리자</p>
</li>
<li><p><strong>Annotation (어노테이션)</strong>
→ <code>@Entity</code>, <code>@Id</code> 같은 표시를 클래스나 필드에 붙여서 “이건 DB랑 연결된 거야”라고 알려줍니다.</p>
</li>
</ul>
<hr />
<h2 id="간단한-예시">간단한 예시</h2>
<p>예를 들어 가게(Store) 정보를 저장한다고 해봅시다.</p>
<pre><code class="language-java">@Entity  // 이 클래스는 DB 테이블과 연결된 엔티티야!
public class Store {
    @Id  // 이 필드가 테이블의 기본 키야
    @GeneratedValue  // 자동으로 번호를 만들어줘
    private Long id;

    private String name;
    private String category;
}</code></pre>
<p>그리고 실제로 사용할 때는:</p>
<pre><code class="language-java">Store store = new Store();
store.setName(&quot;김밥천국&quot;);
store.setCategory(&quot;한식&quot;);

// 저장하기
entityManager.persist(store);

// 조회하기
Store found = entityManager.find(Store.class, 1L);</code></pre>
<p>→ SQL을 쓰지 않았는데도, 자동으로 <code>INSERT</code>와 <code>SELECT</code> 쿼리가 실행됩니다.</p>
<hr />
<h2 id="정리">정리</h2>
<ul>
<li>JPA는 자바 객체와 데이터베이스를 자동으로 연결해주는 도구</li>
<li>SQL을 직접 안 쓰고 객체 중심으로 개발 가능</li>
<li>엔티티(Entity), 엔티티 매니저(EntityManager), 어노테이션이 핵심 키워드</li>
</ul>
<p>👉 쉽게 말하면, <strong>“자바 개발자가 데이터베이스랑 대화할 때 통역사 역할을 하는 것”</strong>이 JPA입니다.</p>
<h1 id="jpa가-없을-때와-함께-쓰는-sql의-장점">JPA가 없을 때와 함께 쓰는 SQL의 장점</h1>
<h2 id="jpa가-없으면-어떻게-될까">JPA가 없으면 어떻게 될까?</h2>
<p>예를 들어 <code>User</code>라는 객체를 저장한다고 해보겠습니다.</p>
<h3 id="jpa-없이-jdbc-직접-사용">JPA 없이 (JDBC 직접 사용)</h3>
<pre><code class="language-java">// User 객체
User user = new User(1L, &quot;철수&quot;);

// JDBC 코드 (SQL 직접 작성)
String sql = &quot;INSERT INTO user (id, name) VALUES (?, ?)&quot;;
PreparedStatement pstmt = connection.prepareStatement(sql);
pstmt.setLong(1, user.getId());
pstmt.setString(2, user.getName());
pstmt.executeUpdate();</code></pre>
<p>조회할 때도 마찬가지입니다.</p>
<pre><code class="language-java">String sql = &quot;SELECT id, name FROM user WHERE id = ?&quot;;
PreparedStatement pstmt = connection.prepareStatement(sql);
pstmt.setLong(1, 1);
ResultSet rs = pstmt.executeQuery();

if (rs.next()) {
    Long id = rs.getLong(&quot;id&quot;);
    String name = rs.getString(&quot;name&quot;);
    User user = new User(id, name);
}</code></pre>
<p>👉 매번 SQL을 직접 쓰고, 객체로 변환하는 코드를 손수 작성해야 합니다.
👉 필드가 많아지면 코드 양이 기하급수적으로 늘어나죠.</p>
<hr />
<h2 id="jpa를-쓰면">JPA를 쓰면?</h2>
<p>같은 동작을 JPA로 하면 훨씬 간단해집니다.</p>
<pre><code class="language-java">// User 엔티티
@Entity
public class User {
    @Id
    private Long id;
    private String name;
}

// 사용 코드
User user = new User(1L, &quot;철수&quot;);
entityManager.persist(user); // 저장

User found = entityManager.find(User.class, 1L); // 조회</code></pre>
<p>👉 SQL을 직접 작성하지 않아도, JPA가 자동으로 <code>INSERT</code>, <code>SELECT</code>를 실행해줍니다.
👉 개발자는 객체(User)만 신경 쓰면 됩니다.</p>
<hr />
<h2 id="그래도-sql이-필요한-순간이-있다">그래도 SQL이 필요한 순간이 있다</h2>
<p>그렇다면 SQL은 완전히 안 써도 될까요? 그렇지 않습니다.
JPA가 대부분 자동으로 처리해주지만, <strong>복잡한 쿼리</strong>가 필요할 때는 여전히 SQL 또는 JPQL을 직접 써야 합니다.</p>
<p>예시: “특정 가게에서 별점이 4점 이상인 리뷰를 최신순으로 10개 가져오기”</p>
<h3 id="jpa의-jpql">JPA의 JPQL</h3>
<pre><code class="language-java">List&lt;Review&gt; reviews = entityManager.createQuery(
    &quot;SELECT r FROM Review r WHERE r.storeId = :storeId AND r.rating &gt;= 4 ORDER BY r.createdAt DESC&quot;,
    Review.class
)
.setParameter(&quot;storeId&quot;, 1L)
.setMaxResults(10)
.getResultList();</code></pre>
<h3 id="sql-직접-작성-native-query">SQL 직접 작성 (Native Query)</h3>
<pre><code class="language-java">@Query(value = &quot;SELECT * FROM review WHERE store_id = :storeId AND rating &gt;= 4 ORDER BY created_at DESC LIMIT 10&quot;, nativeQuery = true)
List&lt;Review&gt; findTopReviews(@Param(&quot;storeId&quot;) Long storeId);</code></pre>
<p>👉 JPA가 제공하는 <strong>JPQL</strong>은 객체 중심 쿼리라 직관적이고,
👉 <strong>Native SQL</strong>은 데이터베이스 고유 기능(예: <code>LIMIT</code>, <code>JSON 함수</code>)을 활용할 때 유용합니다.</p>
<hr />
<h2 id="정리-1">정리</h2>
<ul>
<li>JPA가 없으면 SQL을 직접 작성해야 하고, 코드가 장황해집니다.</li>
<li>JPA는 객체 중심 개발을 가능하게 해서 생산성과 유지보수성을 높여줍니다.</li>
<li>하지만 JPA를 쓰더라도 <strong>복잡한 조회나 성능 최적화</strong>에서는 여전히 SQL이 필요합니다.</li>
</ul>
<p>👉 즉, <strong>JPA는 SQL을 완전히 대체하는 게 아니라, 대부분을 자동화해주고 필요한 경우 SQL을 병행할 수 있게 해주는 도구</strong>입니다.</p>