<p>이 주제는 신입 개발자들이 API를 설계할 때 가장 많이 실수하는 부분이자, 면접관이 &quot;기본기가 되어있는지&quot; 확인하기 딱 좋은 질문입니다. </p>
<p>단순히 &quot;데이터를 보낼 때 쓴다&quot;를 넘어서 <strong>'멱등성(Idempotency)'</strong>이라는 키워드까지 챙겨가실 수 있도록 구성했습니다.</p>
<hr />
<h2 id="network-get과-post의-차이-아직도-모르고-api-만드시나요-rest-api">[Network] GET과 POST의 차이, 아직도 모르고 API 만드시나요? (REST API)</h2>
<h3 id="그냥-동작만-하면-되는-거-아닌가요">&quot;그냥 동작만 하면 되는 거 아닌가요?&quot;</h3>
<p>처음 API를 만들 때 흔히 하는 실수가 있습니다.
데이터를 조회하든, 수정하든, 삭제하든 상관없이 <strong>모조리 <code>POST</code> 메서드로 통일</strong>해서 보내버리는 것이죠.</p>
<p>물론 기능은 돌아갑니다. 하지만 사수가 코드를 보면 등짝 스매싱을 맞을 겁니다.
<strong>HTTP 메서드는 서버와 클라이언트 간의 '약속'이자 '명찰'이기 때문입니다.</strong></p>
<p>오늘은 백엔드 개발자라면 숨 쉬듯 구분해야 할 <strong>GET과 POST의 차이</strong>, 그리고 면접 필살기인 <strong>멱등성(Idempotency)</strong> 개념까지 확실하게 정리해 드립니다.</p>
<hr />
<h3 id="1-직관적인-비교-엽서-vs-편지봉투">1. 직관적인 비교: 엽서 vs 편지봉투</h3>
<p>가장 쉬운 비유로 시작해볼까요?</p>
<h4 id="📨-get-투명한-엽서">📨 GET: 투명한 엽서</h4>
<ul>
<li><strong>용도:</strong> 서버에서 데이터를 <strong>가져올 때 (Read/Retrieve)</strong> 씁니다.</li>
<li><strong>특징:</strong> 데이터가 URL 뒤에 다 보입니다. (<code>?id=1&amp;page=2</code>)</li>
<li><strong>비유:</strong> 내용을 누구나 볼 수 있는 <strong>엽서</strong>입니다. 비밀번호 같은 걸 여기 적으면 큰일 나겠죠?</li>
</ul>
<h4 id="📦-post-밀봉된-편지봉투">📦 POST: 밀봉된 편지봉투</h4>
<ul>
<li><strong>용도:</strong> 서버에 데이터를 <strong>보내거나, 상태를 바꿀 때 (Create/Update)</strong> 씁니다.</li>
<li><strong>특징:</strong> 데이터가 <strong>Body(본문)</strong> 안에 숨겨져서 갑니다.</li>
<li><strong>비유:</strong> 내용을 봉투 안에 넣어서 보내는 <strong>편지</strong>입니다. 겉으로는 안 보이지만, 뜯어보면(패킷을 까보면) 다 보입니다.</li>
</ul>
<p><img alt="" src="https://velog.velcdn.com/images/hello22433/post/0f06237b-f99e-465d-b32b-d542c6bb213b/image.png" /></p>
<hr />
<h3 id="2-기술적인-차이점-면접용">2. 기술적인 차이점 (면접용)</h3>
<p>단순히 &quot;보인다/안 보인다&quot;의 차이만 있을까요? 진짜 차이점은 여기에 있습니다.</p>
<h4 id="①-데이터-전송-위치-header-vs-body">① 데이터 전송 위치 (Header vs Body)</h4>
<ul>
<li><strong>GET:</strong> URL의 끝인 <strong>Query String</strong>에 데이터를 담아 보냅니다. 그래서 전송할 수 있는 데이터 양에 제한이 있습니다.</li>
<li><strong>POST:</strong> HTTP 패킷의 <strong>Body</strong>에 데이터를 담아 보냅니다. 대용량 데이터를 보내기에 적합합니다.</li>
</ul>
<h4 id="②-캐싱-caching-가능-여부">② 캐싱 (Caching) 가능 여부</h4>
<ul>
<li><strong>GET:</strong> <strong>캐싱이 가능합니다.</strong> 우리가 웹페이지를 즐겨찾기(Bookmark) 할 수 있는 이유입니다. 한 번 조회한 데이터는 브라우저가 기억했다가 빠르게 보여줍니다.</li>
<li><strong>POST:</strong> 기본적으로 <strong>캐싱되지 않습니다.</strong> 주문 버튼을 누를 때마다 새로운 주문이 생성되어야 하니까요.</li>
</ul>
<h4 id="③-멱등성-idempotency---⭐-핵심-키워드">③ 멱등성 (Idempotency) - ⭐ 핵심 키워드!</h4>
<p>면접관이 눈을 반짝이며 듣는 포인트입니다.</p>
<blockquote>
<p><strong>멱등하다(Idempotent):</strong> 연산을 한 번 하든 100번 하든 <strong>결과가 똑같다</strong>는 뜻입니다.</p>
</blockquote>
<ul>
<li><strong>GET은 멱등합니다.</strong><ul>
<li>게시글을 100번 조회한다고 해서 게시글 내용이 변하거나 서버 데이터가 망가지지 않습니다.</li>
</ul>
</li>
<li><strong>POST는 멱등하지 않습니다.</strong><ul>
<li>'결제하기' 버튼을 100번 누르면? <strong>100번 결제됩니다.</strong> 서버의 상태가 매번 바뀝니다.</li>
</ul>
</li>
</ul>
<hr />
<h3 id="3-오해와-진실-post는-보안상-안전하다">3. 오해와 진실: &quot;POST는 보안상 안전하다?&quot;</h3>
<p><strong>❌ 땡! 틀렸습니다.</strong></p>
<p>많은 분들이 &quot;GET은 URL에 보이니까 위험하고, POST는 Body에 숨기니까 안전하다&quot;고 생각합니다.
하지만 개발자 도구(F12)를 켜거나 패킷 분석 툴(Wireshark)을 쓰면 <strong>POST로 보낸 데이터도 적나라하게 다 보입니다.</strong></p>
<blockquote>
<p><strong>💡 진짜 보안은?</strong>
HTTP 메서드가 아니라 <strong>HTTPS(암호화 통신)</strong>를 써야 안전해집니다. POST라고 안심하고 비밀번호를 평문으로 보내면 해킹 당합니다.</p>
</blockquote>
<hr />
<h3 id="4-rest-api의-나머지-친구들-put-patch-delete">4. REST API의 나머지 친구들 (PUT, PATCH, DELETE)</h3>
<p>&quot;그럼 수정이랑 삭제는요?&quot;
과거에는 GET/POST만 썼지만, 요즘은 <strong>RESTful API</strong> 원칙에 따라 목적에 맞는 메서드를 씁니다.</p>
<ul>
<li><strong>PUT:</strong> 리소스를 <strong>통째로 갈아끼울 때</strong> (전체 수정)</li>
<li><strong>PATCH:</strong> 리소스의 <strong>일부분만 고칠 때</strong> (부분 수정)</li>
<li><strong>DELETE:</strong> 리소스를 <strong>삭제할 때</strong></li>
</ul>
<hr />
<h3 id="5-실전-면접-대비-3초-요약">5. 실전 면접 대비: 3초 요약</h3>
<p><strong>Q. GET과 POST의 차이를 설명해주세요.</strong></p>
<blockquote>
<p><strong>A.</strong>
&quot;<strong>사용 목적</strong>과 <strong>멱등성</strong>의 차이가 있습니다.
<strong>GET</strong>은 서버의 데이터를 <strong>조회</strong>할 때 사용하며, 여러 번 요청해도 결과가 같은 <strong>멱등성</strong>을 가집니다. 또한 URL에 데이터가 노출되므로 캐싱이 가능합니다.
반면 <strong>POST</strong>는 서버의 데이터를 <strong>생성하거나 변경</strong>할 때 사용하며, 요청할 때마다 새로운 리소스가 생성될 수 있어 <strong>멱등하지 않습니다.</strong> 데이터는 Body에 담겨 전송됩니다.&quot;</p>
</blockquote>
<hr />
<h3 id="🚀-다음-글-예고">🚀 다음 글 예고</h3>
<p>&quot;열심히 API를 만들어서 보냈는데, 서버가 <code>404</code>라고 화를 냅니다.&quot;</p>
<p>개발자가 평생 봐야 할 숫자들. <code>200</code>, <code>404</code>, <code>500</code>...
이 숫자들만 잘 알아도 에러 잡는 속도가 2배 빨라집니다. 다음 시간에는 <strong>HTTP 상태 코드(Status Code)</strong>의 모든 것을 파헤쳐 보겠습니다.</p>
<blockquote>
<p><strong>Next Topic:</strong> [Network] 200 OK와 404 Not Found, 숫자로 대화하는 법 (HTTP 상태 코드)</p>
</blockquote>
<hr />
<p>이번 주제로 <strong>API 설계의 기초</strong>를 다졌습니다. 다음 주제인 <strong>'상태 코드(Status Code)'</strong>까지 알면, 클라이언트(프론트)와 서버(백엔드)가 어떻게 소통하는지 완벽하게 이해하게 됩니다.</p>