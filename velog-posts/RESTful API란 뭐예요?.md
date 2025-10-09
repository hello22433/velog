<h2 id="1️⃣-api란-무엇인가">1️⃣ API란 무엇인가?</h2>
<p><strong>API</strong> = “서로 다른 프로그램이 <strong>말을 주고받는 약속</strong>”이에요.</p>
<ul>
<li>예를 들어,
스마트폰 앱에서 “오늘 날씨 보여줘” 버튼을 누르면
앱이 날씨 서버에 요청을 보내고, 서버가 날씨 정보를 돌려주잖아요.</li>
</ul>
<p>이때 앱과 서버가 <strong>말을 주고받는 방법/규칙</strong>이 바로 <strong>API</strong>예요.</p>
<ul>
<li>앱 = 요청을 보내는 쪽</li>
<li>서버 = 요청을 받아 처리하고 답하는 쪽</li>
<li>API = <strong>“말하는 규칙”</strong></li>
</ul>
<p>비유하면:</p>
<blockquote>
<p><strong>레스토랑 주문서</strong>가 API예요.
손님은 “치킨 한 마리 주세요”라고 주문서에 적고,
주방은 그 주문서를 보고 요리를 만들어서 돌려주죠.</p>
</blockquote>
<hr />
<h2 id="2️⃣-rest란-무엇인가">2️⃣ REST란 무엇인가?</h2>
<p><strong>REST</strong> = “API를 설계하는 <strong>규칙/방법</strong> 중 하나”예요.</p>
<ul>
<li>원래 뜻: <strong>Representational State Transfer</strong> (표현 상태 전이)</li>
<li>어렵게 들리지만 핵심은 <strong>“자원(Resource)을 중심으로 설계”</strong> 하는 것</li>
</ul>
<p>즉,</p>
<ol>
<li><p>서버에 있는 <strong>모든 것(자원)</strong> 을 URI(주소)로 나타내고</p>
<ul>
<li>예: <code>/users</code> → 사용자 정보</li>
<li><code>/orders/123</code> → 주문 번호 123</li>
</ul>
</li>
<li><p>HTTP 메서드(GET, POST, PUT, DELETE)를 사용해 <strong>자원을 다루는 행동</strong>을 정리</p>
</li>
</ol>
<hr />
<h2 id="3️⃣-restful-api의-특징">3️⃣ RESTful API의 특징</h2>
<table>
<thead>
<tr>
<th>특징</th>
<th>의미</th>
<th>비유</th>
</tr>
</thead>
<tbody><tr>
<td>자원(Resource) 중심</td>
<td>모든 건 “물건/데이터”로 생각</td>
<td>메뉴판에 있는 음식 하나하나</td>
</tr>
<tr>
<td>URI</td>
<td>자원을 찾는 주소</td>
<td>치킨집 주소, 메뉴판 번호</td>
</tr>
<tr>
<td>HTTP 메서드</td>
<td>행동(GET/POST/PUT/DELETE)</td>
<td>주문, 수정, 삭제, 조회</td>
</tr>
<tr>
<td>무상태성(Stateless)</td>
<td>요청할 때마다 필요한 정보 모두 담아야 함</td>
<td>매번 주문할 때 주문서에 이름·주소 쓰기</td>
</tr>
<tr>
<td>캐시 가능(Cacheable)</td>
<td>동일한 요청은 미리 저장 가능</td>
<td>이미 만들어둔 도시락 바로 제공</td>
</tr>
</tbody></table>
<hr />
<h2 id="4️⃣-http-메서드-사용법-행동-정의">4️⃣ HTTP 메서드 사용법 (행동 정의)</h2>
<table>
<thead>
<tr>
<th>메서드</th>
<th>의미</th>
<th>예시</th>
</tr>
</thead>
<tbody><tr>
<td>GET</td>
<td>읽기</td>
<td><code>/users</code> → 모든 사용자 목록 보기</td>
</tr>
<tr>
<td>POST</td>
<td>생성</td>
<td><code>/users</code> → 새 사용자 추가</td>
</tr>
<tr>
<td>PUT</td>
<td>전체 수정</td>
<td><code>/users/123</code> → 사용자 123 정보 전체 바꾸기</td>
</tr>
<tr>
<td>PATCH</td>
<td>일부 수정</td>
<td><code>/users/123</code> → 이름만 바꾸기</td>
</tr>
<tr>
<td>DELETE</td>
<td>삭제</td>
<td><code>/users/123</code> → 사용자 123 삭제</td>
</tr>
</tbody></table>
<blockquote>
<p>중요한 점:
GET은 <strong>안전하게 데이터를 읽는 것</strong>
POST/PUT/PATCH/DELETE는 서버 상태를 바꾸는 요청이에요.</p>
</blockquote>
<hr />
<h2 id="5️⃣-상태-코드status-code">5️⃣ 상태 코드(Status Code)</h2>
<p>서버가 요청을 처리한 결과를 숫자로 알려줘요.</p>
<ul>
<li>200 OK → 성공, 데이터 포함</li>
<li>201 Created → 생성 성공</li>
<li>204 No Content → 성공, 데이터 없음</li>
<li>400 Bad Request → 잘못된 요청</li>
<li>404 Not Found → 데이터 없음</li>
<li>500 Internal Server Error → 서버 문제</li>
</ul>
<p>비유:</p>
<blockquote>
<p>요리사가 손님에게 “주문 완료! 여기 치킨 나왔어요(200 OK)” 또는 “죄송, 재료가 없어요(404)”라고 알려주는 것과 같아요.</p>
</blockquote>
<hr />
<h2 id="6️⃣-restful-api-예시">6️⃣ RESTful API 예시</h2>
<h3 id="예시-주문-시스템">예시: 주문 시스템</h3>
<p><strong>1) 주문 생성</strong></p>
<pre><code>POST /orders
Content-Type: application/json

{
  &quot;userId&quot;: &quot;u-123&quot;,
  &quot;items&quot;: [{&quot;productId&quot;:&quot;p-1&quot;, &quot;qty&quot;:2}]
}</code></pre><ul>
<li>POST → 새 주문 생성</li>
</ul>
<p><strong>응답</strong></p>
<pre><code>201 Created
Location: /orders/o-987

{
  &quot;orderId&quot;: &quot;o-987&quot;,
  &quot;status&quot;: &quot;PENDING&quot;
}</code></pre><p><strong>2) 주문 조회</strong></p>
<pre><code>GET /orders/o-987</code></pre><ul>
<li>GET → 주문 내용 보기</li>
</ul>
<p><strong>응답</strong></p>
<pre><code>200 OK
{
  &quot;orderId&quot;: &quot;o-987&quot;,
  &quot;items&quot;: [{&quot;productId&quot;:&quot;p-1&quot;,&quot;qty&quot;:2}],
  &quot;status&quot;: &quot;PENDING&quot;
}</code></pre><hr />
<h2 id="7️⃣-정리-restful-api를-쉽게-말하면">7️⃣ 정리: RESTful API를 쉽게 말하면</h2>
<blockquote>
<p>RESTful API = <strong>“서버와 프로그램이 데이터를 주고받는 약속 + 자원 중심 규칙”</strong></p>
</blockquote>
<ul>
<li>서버가 가진 <strong>모든 것</strong> → 자원(Resource)</li>
<li>주소(URI) → 자원을 찾는 위치</li>
<li>HTTP 메서드 → 자원을 다루는 행동</li>
<li>상태 코드 → 요청 결과 알림</li>
</ul>
<p>비유하면:</p>
<ul>
<li>API = <strong>주문서</strong></li>
<li>REST = <strong>메뉴판과 주문 규칙</strong></li>
</ul>
<hr />