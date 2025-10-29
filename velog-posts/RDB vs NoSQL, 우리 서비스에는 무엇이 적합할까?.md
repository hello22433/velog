<p>웹 서비스를 설계할 때 <strong>데이터베이스 선택</strong>은 가장 중요한 결정 중 하나입니다.
“RDB를 써야 할까, NoSQL을 써야 할까?”라는 고민을 많이 하죠.
이번 글에서는 각각 장단점과 실제 적용 사례를 비교하면서 쉽게 풀어보겠습니다.</p>
<hr />
<h2 id="1-rdbrelational-database-관계형-db">1. RDB(Relational Database, 관계형 DB)</h2>
<h3 id="특징">특징</h3>
<ul>
<li>데이터가 <strong>표(table) 형태</strong>로 저장됨</li>
<li><strong>스키마(schema)</strong>가 엄격하게 정의됨 → 데이터 구조가 고정</li>
<li><strong>SQL</strong>을 사용해 데이터를 조회, 삽입, 수정, 삭제</li>
<li>데이터 <strong>정합성(CACD)</strong> 보장 → 트랜잭션 처리 강력</li>
</ul>
<h3 id="장점">장점</h3>
<ol>
<li><p><strong>데이터 정합성 보장</strong></p>
<ul>
<li>은행, 결제, 주문 등 중요한 데이터 관리에 강함</li>
</ul>
</li>
<li><p><strong>복잡한 쿼리 가능</strong></p>
<ul>
<li>JOIN, GROUP BY 등 다양한 분석과 조회 가능</li>
</ul>
</li>
<li><p><strong>성숙한 생태계</strong></p>
<ul>
<li>MySQL, PostgreSQL, Oracle 등 안정적이고 문서화 잘됨</li>
</ul>
</li>
</ol>
<h3 id="단점">단점</h3>
<ol>
<li><p><strong>수평 확장 어려움</strong></p>
<ul>
<li>서버를 늘리는 수평 스케일링(sharding)이 복잡</li>
</ul>
</li>
<li><p><strong>스키마 변경이 어렵다</strong></p>
<ul>
<li>컬럼 추가/변경 시 서비스 중단 위험</li>
</ul>
</li>
</ol>
<h3 id="대표-사례">대표 사례</h3>
<ul>
<li><strong>은행, 카드사</strong>: 트랜잭션 정합성이 필수</li>
<li><strong>온라인 쇼핑몰 주문/결제 DB</strong>: 주문 데이터 정확성 보장 필요</li>
</ul>
<hr />
<h2 id="2-nosqlnot-only-sql">2. NoSQL(Not Only SQL)</h2>
<h3 id="특징-1">특징</h3>
<ul>
<li><strong>유연한 데이터 구조</strong> → JSON, key-value, 문서, 그래프 등</li>
<li>스키마가 없거나 느슨함 → 필요할 때 필드 추가 가능</li>
<li>수평 확장(sharding, replication) 쉽고 분산 처리 용이</li>
</ul>
<h3 id="장점-1">장점</h3>
<ol>
<li><p><strong>확장성이 뛰어남</strong></p>
<ul>
<li>트래픽 급증에도 쉽게 서버를 늘릴 수 있음</li>
</ul>
</li>
<li><p><strong>유연한 데이터 구조</strong></p>
<ul>
<li>다양한 형태의 데이터를 저장 가능 → 로그, 이벤트, 세션</li>
</ul>
</li>
<li><p><strong>빠른 읽기/쓰기</strong></p>
<ul>
<li>단순 조회/저장 시 매우 빠름</li>
</ul>
</li>
</ol>
<h3 id="단점-1">단점</h3>
<ol>
<li><p><strong>정합성 약함</strong></p>
<ul>
<li>CAP 이론 상 일부 데이터가 일시적으로 일관성 없을 수 있음</li>
</ul>
</li>
<li><p><strong>복잡한 쿼리 어려움</strong></p>
<ul>
<li>JOIN, 다중 조건 조회 등은 구현 난이도 ↑</li>
</ul>
</li>
</ol>
<h3 id="대표-사례-1">대표 사례</h3>
<ul>
<li><strong>SNS, 댓글 서비스</strong>: 게시물, 좋아요, 팔로우 등 빠른 쓰기와 확장성 필요</li>
<li><strong>로그, 세션 관리, 캐시</strong>: Redis, MongoDB, Cassandra 등</li>
</ul>
<hr />
<h2 id="3-선택-기준-비교">3. 선택 기준 비교</h2>
<table>
<thead>
<tr>
<th>기준</th>
<th>RDB</th>
<th>NoSQL</th>
</tr>
</thead>
<tbody><tr>
<td>데이터 정합성</td>
<td>매우 중요 (트랜잭션)</td>
<td>일부만 필요, eventual consistency 허용</td>
</tr>
<tr>
<td>데이터 구조</td>
<td>고정, 스키마 엄격</td>
<td>유연, 자유로운 구조</td>
</tr>
<tr>
<td>확장성</td>
<td>수직 확장(서버 업)</td>
<td>수평 확장(서버 추가) 용이</td>
</tr>
<tr>
<td>쿼리 복잡도</td>
<td>복잡한 쿼리 가능</td>
<td>단순 조회/쓰기 위주</td>
</tr>
<tr>
<td>대표 사례</td>
<td>은행, 주문/결제</td>
<td>SNS, 로그, 실시간 서비스</td>
</tr>
</tbody></table>
<hr />
<h2 id="4-우리-서비스에는-무엇이-적합할까">4. 우리 서비스에는 무엇이 적합할까?</h2>
<ul>
<li><p><strong>주문/결제, 회원 정보 관리</strong> → <strong>RDB</strong> 추천</p>
<ul>
<li>데이터 정합성이 필수적이고 트랜잭션이 많음</li>
</ul>
</li>
<li><p><strong>게시글, 댓글, 로그, 세션</strong> → <strong>NoSQL</strong> 추천</p>
<ul>
<li>트래픽이 많고, 확장성·속도가 더 중요</li>
</ul>
</li>
</ul>
<blockquote>
<p>결론: 대부분의 서비스는 <strong>RDB + NoSQL 혼합 구조</strong>를 사용합니다.
핵심 데이터는 RDB, 비정형 데이터나 캐싱, 로그는 NoSQL을 사용하는 방식이 일반적입니다.</p>
</blockquote>
<hr />