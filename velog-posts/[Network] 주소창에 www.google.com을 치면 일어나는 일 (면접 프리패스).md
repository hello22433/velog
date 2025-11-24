<p>이 질문은 개발자 면접의 '국가대표' 같은 질문입니다. 신입부터 경력직까지, 이 질문 하나면 지원자의 <strong>네트워크 전체 이해도</strong>를 파악할 수 있기 때문입니다.</p>
<p>너무 깊게 들어가면(패킷 단위 등) 끝이 없으니, <strong>백엔드 개발자가 알아야 할 '웹 통신 흐름'</strong> 위주로 깔끔하게 정리해 드립니다.</p>
<hr />
<h2 id="network-주소창에-wwwgooglecom을-치면-일어나는-일-면접-프리패스">[Network] 주소창에 <a href="https://www.google.com">www.google.com</a>을 치면 일어나는 일 (면접 프리패스)</h2>
<h3 id="이-질문-지겹게-들으셨죠">&quot;이 질문, 지겹게 들으셨죠?&quot;</h3>
<p>하지만 면접관들은 지겹지도 않은지 계속 물어봅니다. 왜냐하면 이 질문 하나에 <strong>DNS, 프로토콜, 서버 통신, 브라우저 렌더링</strong>의 모든 과정이 담겨 있기 때문입니다.</p>
<p>달달 외운 답변은 티가 납니다. 오늘은 우리가 택배를 보내고 받는 과정에 빗대어, <strong>데이터가 내 컴퓨터에서 구글 서버까지 갔다가 돌아오는 여정</strong>을 완벽하게 이해해 봅시다.</p>
<hr />
<h3 id="1-1단계-목적지-주소-찾기-dns">1. 1단계: 목적지 주소 찾기 (DNS)</h3>
<p>우리가 친구 집을 찾아가려면 '도로명 주소'가 필요하듯, 컴퓨터도 서버를 찾으려면 <strong>IP 주소(예: 223.130.195.200)</strong>가 필요합니다. 하지만 우리는 <code>www.google.com</code>이라는 <strong>도메인 이름</strong>만 알고 있죠.</p>
<p>이 도메인 이름을 IP 주소로 변환해 주는 시스템이 바로 <strong>DNS(Domain Name System)</strong>입니다.</p>
<p><strong>[탐색 순서]</strong></p>
<ol>
<li><strong>브라우저 캐시 확인:</strong> &quot;나 여기 가본 적 있나?&quot; (가장 빠름)</li>
<li><strong>OS <code>hosts</code> 파일 확인:</strong> 내 컴퓨터에 저장된 주소록 확인.</li>
<li><strong>DNS 서버 요청:</strong> 위의 두 곳에 없으면, 통신사(ISP)의 DNS 서버에게 물어봅니다. &quot;구글 IP 주소 좀 알려줘!&quot;</li>
</ol>
<p>👉 <strong>결과:</strong> 구글 서버의 진짜 IP 주소를 획득했습니다.</p>
<hr />
<h3 id="2-2단계-길-뚫기-tcpip-연결--3-way-handshake">2. 2단계: 길 뚫기 (TCP/IP 연결 &amp; 3-way Handshake)</h3>
<p>주소(IP)를 알았으니 바로 데이터를 보낼까요? 아닙니다.
인터넷은 신뢰가 생명입니다. 상대방이 받을 준비가 됐는지 확인하는 절차가 필요합니다. 이를 <strong>3-way Handshake(3번의 악수)</strong>라고 합니다.</p>
<blockquote>
<p><strong>🤝 3-way Handshake 대화</strong></p>
<ol>
<li><strong>Client(나):</strong> &quot;똑똑, 저 데이터 보내도 될까요?&quot; (SYN)</li>
<li><strong>Server(구글):</strong> &quot;네, 들어오세요. 저도 보낼 거 있는데 괜찮나요?&quot; (SYN + ACK)</li>
<li><strong>Client(나):</strong> &quot;네, 잘 들립니다. 연결 시작합시다!&quot; (ACK)</li>
</ol>
</blockquote>
<p>이 과정이 끝나야 비로소 <strong>논리적인 연결(Session)</strong>이 성립됩니다. (참고: HTTPS라면 여기서 암호화를 위한 'SSL 핸드쉐이크' 과정이 추가됩니다.)</p>
<hr />
<h3 id="3-3단계-주문서-보내기-http-request">3. 3단계: 주문서 보내기 (HTTP Request)</h3>
<p>길이 뚫렸으니 이제 진짜 용건을 말합니다.
브라우저는 구글 서버에게 <strong>HTTP 요청(Request) 메시지</strong>를 만들어 보냅니다.</p>
<pre><code class="language-http">GET / HTTP/1.1
Host: www.google.com
User-Agent: Mozilla/5.0 ...</code></pre>
<ul>
<li><strong>GET:</strong> &quot;정보를 주세요.&quot; (주문)</li>
<li><strong>/</strong>: &quot;메인 페이지로요.&quot; (메뉴)</li>
<li>이 요청 패킷은 인터넷망의 수많은 라우터를 거쳐 구글 서버에 도착합니다.</li>
</ul>
<hr />
<h3 id="4-4단계-주문-처리-및-응답-server--http-response">4. 4단계: 주문 처리 및 응답 (Server &amp; HTTP Response)</h3>
<p>여러분이(혹은 미래의 여러분이) 만든 <strong>백엔드 서버</strong>가 활약하는 단계입니다.</p>
<ol>
<li>웹 서버(Apache, Nginx)가 요청을 받습니다.</li>
<li>WAS(Tomcat 등)가 데이터베이스(DB)에서 데이터를 꺼내거나 비즈니스 로직을 처리합니다.</li>
<li>결과물을 <strong>HTTP 응답(Response) 메시지</strong>에 담아 돌려줍니다.</li>
</ol>
<!-- end list -->

<pre><code class="language-http">HTTP/1.1 200 OK
Content-Type: text/html

&lt;html&gt;
  &lt;body&gt; ... &lt;/body&gt;
&lt;/html&gt;</code></pre>
<ul>
<li><strong>200 OK:</strong> &quot;주문 정상 처리되었습니다.&quot;</li>
<li><strong>Body:</strong> 우리가 볼 HTML 코드가 들어있습니다.</li>
</ul>
<hr />
<h3 id="5-5단계-포장-뜯기-browser-rendering">5. 5단계: 포장 뜯기 (Browser Rendering)</h3>
<p>다시 내 컴퓨터(브라우저)로 돌아왔습니다.
브라우저는 서버가 보내준 HTML, CSS, JavaScript 코드를 해석(Parsing)해서 우리가 보는 예쁜 화면으로 그려줍니다.</p>
<p>이것이 바로 우리가 주소창에 엔터를 치고 <strong>0.5초</strong> 만에 일어나는 일들입니다.</p>
<p><img alt="" src="https://velog.velcdn.com/images/hello22433/post/3465f28e-1fa8-41b3-9a8d-e442f729d827/image.png" /></p>
<hr />
<h3 id="🎯-실전-면접-대비-3단계-요약">🎯 실전 면접 대비: 3단계 요약</h3>
<p>면접관이 묻는다면 너무 장황하게 설명하기보다, <strong>핵심 키워드 3개</strong>를 중심으로 흐름을 잡으세요.</p>
<blockquote>
<p><strong>Q. 주소창에 구글을 치면 일어나는 일을 설명해주세요.</strong></p>
<p><strong>A.</strong>
&quot;크게 <strong>DNS, TCP 연결, 서버 통신</strong> 3단계로 요약할 수 있습니다.</p>
<ol>
<li>먼저 <strong>DNS</strong>를 통해 도메인 이름을 <strong>IP 주소</strong>로 변환합니다.</li>
<li>해당 IP의 서버와 <strong>3-way Handshake</strong>를 통해 신뢰성 있는 <strong>TCP 연결</strong>을 수립합니다.</li>
<li>이후 클라이언트가 <strong>HTTP 요청</strong>을 보내면, 서버가 이를 처리해 <strong>HTML 등 리소스를 응답</strong>하고, 브라우저가 이를 <strong>렌더링</strong>하여 화면에 보여줍니다.&quot;</li>
</ol>
</blockquote>
<hr />
<h3 id="🚀-다음-글-예고">🚀 다음 글 예고</h3>
<p>&quot;서버와 대화하려면 규칙을 지켜야 합니다.&quot;</p>
<p>방금 과정에서 <code>GET</code>이라는 단어가 나왔죠? <code>GET</code> 말고 <code>POST</code>는 언제 쓸까요? <code>PUT</code>과 <code>PATCH</code>의 차이는요? 다음 시간에는 <strong>백엔드 개발자가 반드시 지켜야 할 API의 규칙, REST API와 HTTP 메서드</strong>에 대해 알아보겠습니다.</p>
<blockquote>
<p><strong>Next Topic:</strong> [Network] GET과 POST의 차이, 아직도 모르고 API 만드시나요? (REST API)</p>
</blockquote>