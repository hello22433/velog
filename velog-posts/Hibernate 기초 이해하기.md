<p>|실제 개발에서 JPA만 쓰지는 않습니다. 왜냐하면 JPA는 <strong>인터페이스(규칙)</strong>일 뿐이고, 그 규칙을 <strong>실제로 구현한 라이브러리</strong>가 필요하기 때문입니다. 그중 가장 많이 쓰이는 구현체가 바로 <strong>Hibernate</strong>입니다.</p>
<hr />
<h2 id="jpa와-hibernate의-관계">JPA와 Hibernate의 관계</h2>
<ul>
<li><p><strong>JPA (Java Persistence API)</strong>
자바 진영에서 만든 표준(스펙, 규칙).
→ “이렇게 엔티티를 정의하고, 이렇게 저장·조회할 수 있어야 한다”라는 인터페이스.</p>
</li>
<li><p><strong>Hibernate</strong>
JPA 규칙을 실제로 구현한 라이브러리.
→ 우리가 JPA 코드를 쓰면, Hibernate가 그 규칙에 맞게 SQL을 실행하고 결과를 돌려줌.</p>
</li>
</ul>
<p>즉, JPA = 설계도, Hibernate = 설계도를 실제로 만든 건물.</p>
<hr />
<h2 id="hibernate-없이-jpa만-쓰면">Hibernate 없이 JPA만 쓰면?</h2>
<p>JPA 자체는 그냥 규칙이기 때문에 Hibernate 같은 구현체가 없으면 동작하지 않습니다.
예를 들어 우리가 JPA 코드로 아래와 같이 작성하면:</p>
<pre><code class="language-java">Store store = entityManager.find(Store.class, 1L);</code></pre>
<p>이 한 줄 뒤에서는 Hibernate가 실제로 다음과 같은 SQL을 날려줍니다:</p>
<pre><code class="language-sql">SELECT * FROM store WHERE id = 1;</code></pre>
<p>만약 Hibernate가 없다면, 이 SQL을 직접 만들어 실행할 구현체가 없어서 JPA는 그저 껍데기에 불과합니다.</p>
<hr />
<h2 id="hibernate가-해주는-일">Hibernate가 해주는 일</h2>
<ol>
<li><p><strong>SQL 자동 생성</strong></p>
<ul>
<li>우리가 엔티티(Entity)를 정의하면, Hibernate가 알아서 적절한 SQL을 만들어 실행합니다.</li>
</ul>
</li>
<li><p><strong>객체 ↔ 테이블 매핑</strong></p>
<ul>
<li>DB의 테이블과 자바 객체 사이를 자동으로 연결해줍니다.</li>
<li>예: <code>Store</code> 클래스 ↔ <code>store</code> 테이블</li>
</ul>
</li>
<li><p><strong>캐싱</strong></p>
<ul>
<li>같은 데이터를 여러 번 조회할 때, 불필요하게 DB를 계속 조회하지 않고 메모리에 캐싱해서 성능을 높여줍니다.</li>
</ul>
</li>
<li><p><strong>지연 로딩 (Lazy Loading)</strong></p>
<ul>
<li>필요한 시점에만 연관된 데이터를 불러옵니다.</li>
<li>예: 가게만 먼저 가져오고, 리뷰는 실제 사용할 때 DB에서 불러오기.</li>
</ul>
</li>
</ol>
<hr />
<h2 id="hibernate를-쓰면-좋은-점">Hibernate를 쓰면 좋은 점</h2>
<ul>
<li><strong>생산성</strong>: SQL을 일일이 작성하지 않아도 됨.</li>
<li><strong>이식성</strong>: DB를 바꾸더라도(예: MySQL → PostgreSQL) 코드 수정이 최소화됨.</li>
<li><strong>풍부한 기능</strong>: 캐싱, 페이징 처리, 지연 로딩 등 편리한 기능 제공.</li>
</ul>
<hr />
<h2 id="그래도-sql이-필요한-순간">그래도 SQL이 필요한 순간</h2>
<p>Hibernate도 만능은 아닙니다. JPA와 마찬가지로, 복잡한 통계/집계 쿼리나 DB 특화 기능은 SQL을 직접 작성하는 게 더 낫습니다.
Hibernate는 이런 경우를 위해 <code>@Query</code> 같은 방식으로 SQL이나 JPQL을 직접 쓸 수 있게 지원합니다.</p>
<hr />
<h2 id="정리">정리</h2>
<ul>
<li>JPA는 <strong>표준 인터페이스</strong>, Hibernate는 그 표준을 구현한 <strong>실제 엔진</strong>.</li>
<li>Hibernate는 SQL 자동 생성, 객체-테이블 매핑, 캐싱, 지연 로딩 같은 기능을 제공해 개발을 단순화.</li>
<li>하지만 복잡한 조회나 성능 최적화에는 여전히 SQL이 필요.</li>
</ul>
<p>👉 쉽게 말해, JPA는 규칙서이고 Hibernate는 그 규칙에 맞춰 돌아가는 대표 선수입니다.</p>