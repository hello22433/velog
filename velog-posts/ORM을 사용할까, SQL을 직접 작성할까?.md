<p><strong>– 성능과 유지보수성을 고려한 현실적인 선택</strong></p>
<p>웹 서비스나 MSA 환경에서 데이터를 다룰 때, 개발자라면 한 번쯤 고민해보는 문제입니다.</p>
<blockquote>
<p>“ORM을 쓸까, 아니면 SQL을 직접 짜야 할까?”</p>
</blockquote>
<p>이 질문에는 <strong>성능</strong>과 <strong>유지보수성</strong>이라는 두 가지 기준이 있습니다.</p>
<hr />
<h2 id="1-orm이란-무엇인가">1. ORM이란 무엇인가?</h2>
<p><strong>ORM(Object-Relational Mapping)</strong>은
“DB 테이블을 객체로 바꾸어, 객체를 조작하면 DB에 자동으로 반영되는 기술”입니다.</p>
<h3 id="예시-java--jpa">예시: Java + JPA</h3>
<pre><code class="language-java">// 주문(Order) 엔티티
@Entity
public class Order {
    @Id @GeneratedValue
    private Long id;
    private String product;
    private int quantity;

    // 생성자, getter, setter 생략
}

// 주문 생성
Order order = new Order();
order.setProduct(&quot;노트북&quot;);
order.setQuantity(2);
orderRepository.save(order); // SQL을 직접 작성하지 않아도 DB에 반영</code></pre>
<ul>
<li>위 코드만으로 DB에 INSERT SQL이 실행됩니다.</li>
<li><strong>SQL 작성 없이 객체 중심으로 코딩 가능</strong> → 유지보수 쉽고, 읽기 편함</li>
</ul>
<hr />
<h2 id="2-직접-sql-작성이란">2. 직접 SQL 작성이란?</h2>
<p>SQL을 직접 작성해서 DB를 제어하는 방식입니다.</p>
<ul>
<li>자유도가 높아 <strong>복잡한 쿼리</strong>를 최적화 가능</li>
<li>DB 특화 기능, JOIN, 집계, 배치 처리 등에 강점</li>
</ul>
<h3 id="예시-직접-sql">예시: 직접 SQL</h3>
<pre><code class="language-java">String sql = &quot;SELECT * FROM orders WHERE product = ? AND quantity &gt; ?&quot;;
List&lt;Order&gt; orders = jdbcTemplate.query(sql, new Object[]{&quot;노트북&quot;, 1}, new OrderRowMapper());</code></pre>
<ul>
<li>SQL을 직접 작성 → 원하는 쿼리를 <strong>정확하게 제어 가능</strong></li>
<li>하지만 객체와 DB 매핑 코드를 직접 작성해야 하므로, <strong>유지보수가 조금 번거로움</strong></li>
</ul>
<hr />
<h2 id="3-장단점-비교">3. 장단점 비교</h2>
<table>
<thead>
<tr>
<th>항목</th>
<th>ORM</th>
<th>직접 SQL</th>
</tr>
</thead>
<tbody><tr>
<td>장점</td>
<td>객체 중심, 유지보수 쉽고 생산성 높음</td>
<td>복잡 쿼리 최적화, 성능 튜닝 용이</td>
</tr>
<tr>
<td>단점</td>
<td>N+1 문제, 성능 저하 가능</td>
<td>코드 길어짐, 객체 매핑 직접 필요</td>
</tr>
<tr>
<td>적합한 경우</td>
<td>CRUD 위주, 서비스 로직 중심</td>
<td>JOIN, 집계, 대량 데이터 처리</td>
</tr>
</tbody></table>
<hr />
<h2 id="4-성능-관점">4. 성능 관점</h2>
<ul>
<li><p><strong>ORM의 문제점</strong></p>
<ul>
<li>자동 생성 SQL → 복잡한 조회 시 <strong>쿼리 폭발(N+1 문제)</strong> 발생</li>
<li>예: 주문 10개 조회 → 각 주문마다 추가 쿼리 발생</li>
</ul>
</li>
<li><p><strong>SQL 직접 작성 장점</strong></p>
<ul>
<li>필요한 JOIN, WHERE, INDEX 활용 최적화 가능</li>
<li>복잡 조회 시 속도 확보</li>
</ul>
</li>
</ul>
<blockquote>
<p>실무에서는 대부분 <strong>ORM로 기본 CRUD → 성능 문제 있으면 SQL 최적화</strong> 전략 사용</p>
</blockquote>
<hr />
<h2 id="5-유지보수-관점">5. 유지보수 관점</h2>
<ul>
<li><p><strong>ORM</strong></p>
<ul>
<li>객체 중심 → DB 구조 변경 시 코드 반영 쉽고, 팀 협업 효율 높음</li>
</ul>
</li>
<li><p><strong>SQL 직접 작성</strong></p>
<ul>
<li>쿼리 복잡해질수록 유지보수 어려움</li>
<li>DB 벤더마다 SQL 차이 발생 가능</li>
</ul>
</li>
</ul>
<hr />
<h2 id="6-현실적인-선택-전략">6. 현실적인 선택 전략</h2>
<ol>
<li><p><strong>기본 CRUD → ORM 사용</strong></p>
<ul>
<li>주문 생성, 회원 가입, 간단 조회 등</li>
</ul>
</li>
<li><p><strong>복잡한 조회/집계 → SQL 직접 작성</strong></p>
<ul>
<li>JOIN, GROUP BY, 대량 데이터 처리, 성능 최적화 필요 시</li>
</ul>
</li>
<li><p><strong>하이브리드 전략</strong></p>
<ul>
<li>ORM + Native Query 또는 Repository 사용</li>
<li>객체 중심 코드 + 성능 최적화 가능</li>
</ul>
</li>
</ol>
<hr />
<h2 id="7-쉽게-이해하는-비유">7. 쉽게 이해하는 비유</h2>
<ul>
<li><p>ORM = <strong>자동 조리기</strong></p>
<ul>
<li>요리사가 재료만 넣으면 기계가 요리를 완성 → 쉽고 빠름</li>
<li>하지만 복잡한 요리나 특수 레시피는 한계 존재</li>
</ul>
</li>
<li><p>SQL 직접 작성 = <strong>직접 요리</strong></p>
<ul>
<li>재료와 불 조절까지 직접 → 원하는 맛 구현 가능</li>
<li>그러나 요리법과 재료 준비가 복잡 → 유지보수 어려움</li>
</ul>
</li>
</ul>
<hr />
<h2 id="8-핵심-포인트">8. 핵심 포인트</h2>
<blockquote>
<p><strong>“CRUD는 ORM, 복잡 쿼리는 SQL”</strong>
균형 잡힌 전략이 실무에서 가장 효율적이며, 생산성과 성능을 모두 잡을 수 있습니다.</p>
</blockquote>
<hr />