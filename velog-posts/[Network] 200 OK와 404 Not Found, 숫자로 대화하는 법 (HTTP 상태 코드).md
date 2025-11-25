<p><strong>Part 2. 네트워크</strong>의 피날레, <strong>HTTP 상태 코드</strong>입니다.</p>
<p>이 주제는 백엔드 개발자가 <strong>프론트엔드 개발자와 싸우지 않고 평화롭게 협업하기 위해</strong> 가장 중요한 약속입니다. &quot;왜 에러가 났는지&quot;를 숫자로 정확하게 알려줘야 서로 남 탓을 안 하거든요.</p>
<p>특히 <strong>400번대 에러(너 잘못)</strong>와 <strong>500번대 에러(내 잘못)</strong>의 차이를 명확히 아는 것이 '칼퇴'의 지름길입니다.</p>
<hr />
<h2 id="network-200-ok와-404-not-found-숫자로-대화하는-법-http-상태-코드">[Network] 200 OK와 404 Not Found, 숫자로 대화하는 법 (HTTP 상태 코드)</h2>
<h3 id="서버가-말을-안-듣나요-숫자를-보세요">&quot;서버가 말을 안 듣나요? 숫자를 보세요.&quot;</h3>
<p>API를 연동하다 보면 프론트엔드 개발자에게 이런 연락이 옵니다.
&quot;API 호출했는데 안 돼요. 에러 나요.&quot;</p>
<p>이때 초보 개발자는 &quot;어... 제 로컬에서는 잘 되는데요?&quot;라고 답합니다. (최악의 답변입니다.)
센스 있는 개발자는 이렇게 되묻습니다.
<strong>&quot;혹시 상태 코드(Status Code) 몇 번 떴어요?&quot;</strong></p>
<p>HTTP 상태 코드는 서버가 클라이언트에게 보내는 <strong>'채점표'</strong>이자 <strong>'진단서'</strong>입니다. 이 숫자만 봐도 에러의 범인이 프론트인지 백엔드인지 1초 만에 알 수 있습니다.</p>
<p>오늘은 수많은 코드 중, <strong>실무에서 딱 쓰는 5가지 핵심 그룹</strong>만 정리해 드립니다.</p>
<hr />
<h3 id="1-백의-자리-숫자의-비밀-100500">1. 백의 자리 숫자의 비밀 (100~500)</h3>
<p>상태 코드는 3자리 숫자지만, <strong>첫 번째 숫자(백의 자리)</strong>만 봐도 상황 파악이 끝납니다.</p>
<ul>
<li><strong>2xx (Success):</strong> &quot;성공! 잘 처리됐어.&quot; (초록불 🟢)</li>
<li><strong>3xx (Redirection):</strong> &quot;이사 갔어. 저쪽 주소로 다시 가봐.&quot; (노란불 🟡)</li>
<li><strong>4xx (Client Error):</strong> &quot;네가(클라이언트) 요청을 이상하게 보냈어.&quot; (사용자 과실 🔴)</li>
<li><strong>5xx (Server Error):</strong> &quot;미안, 나(서버)한테 문제가 생겼어.&quot; (서버 과실 🔥)</li>
</ul>
<blockquote>
<p><strong>💡 꿀팁:</strong>
로그를 봤는데 <strong>400번대</strong>가 뜬다? -&gt; <strong>프론트엔드</strong> 요청 데이터 확인.
로그를 봤는데 <strong>500번대</strong>가 뜬다? -&gt; <strong>백엔드</strong> 코드(NullPointer 등) 확인.</p>
</blockquote>
<hr />
<h3 id="2-이것만-알면-끝-필수-상태-코드-best-6">2. 이것만 알면 끝! 필수 상태 코드 Best 6</h3>
<p>면접과 실무에서 가장 자주 마주칠 녀석들입니다.</p>
<h4 id="✅-성공-success">✅ 성공 (Success)</h4>
<ul>
<li><strong>200 OK:</strong> 가장 일반적인 성공. 조회(GET), 수정, 삭제 등 대부분에 씁니다.</li>
<li><strong>201 Created:</strong> 무언가 <strong>'생성'</strong>되었을 때 씁니다. 회원가입이나 게시글 작성(POST) 성공 시 200보다 201을 주는 것이 더 RESTful 합니다.</li>
</ul>
<h4 id="❌-클라이언트-잘못-client-error">❌ 클라이언트 잘못 (Client Error)</h4>
<ul>
<li><strong>400 Bad Request:</strong> &quot;개떡같이 말해서 못 알아듣겠어.&quot;<ul>
<li>필수 파라미터가 누락됐거나, 숫자를 보내야 하는데 문자를 보냈을 때.</li>
</ul>
</li>
<li><strong>401 Unauthorized:</strong> &quot;너 누구야? (신분증 없음)&quot;<ul>
<li>로그인이 안 된 사용자가 접근했을 때. (비유: 입구 컷)</li>
</ul>
</li>
<li><strong>403 Forbidden:</strong> &quot;누군지는 알겠는데, 권한이 없어.&quot;<ul>
<li>로그인은 했는데, 관리자 페이지에 접근하려고 할 때. (비유: 출입증은 있는데 구역 위반)</li>
</ul>
</li>
<li><strong>404 Not Found:</strong> &quot;그런 거 없는데요?&quot;<ul>
<li>URL 주소를 오타 냈거나, 삭제된 게시글을 조회했을 때.</li>
</ul>
</li>
</ul>
<h4 id="🔥-서버-잘못-server-error">🔥 서버 잘못 (Server Error)</h4>
<ul>
<li><strong>500 Internal Server Error:</strong> &quot;으악! 서버 터짐.&quot;<ul>
<li>백엔드 개발자가 가장 무서워하는 코드. 코드에 버그가 있거나(<code>NullPointerException</code>), DB 연결이 끊겼을 때 발생합니다.</li>
</ul>
</li>
</ul>
<hr />
<h3 id="3-실무에서의-에러-핸들링-tip">3. 실무에서의 에러 핸들링 (Tip)</h3>
<p>초보 개발자들이 가장 많이 하는 실수가 <strong>모든 에러를 그냥 500으로 던져버리는 것</strong>입니다.</p>
<p>예를 들어, 사용자가 '이미 있는 아이디'로 가입하려고 했습니다.
이건 서버가 터진 게 아니라 <strong>사용자의 입력 오류</strong>죠?
이때는 <code>500</code>이 아니라 <code>409 Conflict</code>(충돌)나 <code>400 Bad Request</code>를 내려줘야 프론트엔드에서 &quot;이미 존재하는 아이디입니다&quot;라고 예쁜 안내창을 띄울 수 있습니다.</p>
<p><strong>&quot;적절한 상태 코드를 내려주는 것, 그것이 백엔드 개발자의 센스이자 배려입니다.&quot;</strong></p>
<hr />
<h3 id="4-실전-면접-대비-3초-요약">4. 실전 면접 대비: 3초 요약</h3>
<p><strong>Q. 401(Unauthorized)과 403(Forbidden)의 차이는 무엇인가요?</strong></p>
<blockquote>
<p><strong>A.</strong>
&quot;<strong>인증(Authentication)</strong>과 <strong>인가(Authorization)</strong>의 차이입니다.
<strong>401</strong>은 로그인이 되지 않아 사용자가 <strong>누구인지 모를 때</strong> 발생하고,
<strong>403</strong>은 누구인지 식별은 되었으나 해당 리소스에 접근할 <strong>권한이 없을 때</strong> 발생합니다.&quot;</p>
</blockquote>
<p><strong>Q. 200과 201의 차이는 언제 구분해서 쓰나요?</strong></p>
<blockquote>
<p><strong>A.</strong>
&quot;둘 다 성공을 의미하지만, <strong>201 Created</strong>는 <code>POST</code> 요청으로 인해 서버에 <strong>새로운 리소스가 생성</strong>되었음을 명확히 알릴 때 사용합니다. 클라이언트에게 '생성 성공'을 더 구체적으로 알려주기 위함입니다.&quot;</p>
</blockquote>
<hr />
<h3 id="🚀-part-2를-마치며">🚀 Part 2를 마치며</h3>
<p>이제 여러분은 <strong>네트워크 통신 과정(주소창) → 요청 방법(GET/POST) → 결과 확인(상태 코드)</strong>까지, 웹 통신의 A to Z를 훑었습니다. 이 정도면 프론트엔드 개발자와 소통하는 데 전혀 문제가 없습니다.</p>
<p>다음 시간부터는 백엔드 개발자의 영원한 숙제, <strong>데이터베이스(Database)</strong> 파트로 넘어갑니다.
&quot;데이터가 100만 개인데, 검색이 왜 3초나 걸리죠?&quot;
이 질문에 0.1초 만에 답하게 만들어 줄 <strong>'인덱스(Index)'</strong>의 마법부터 시작해 봅시다.</p>
<blockquote>
<p><strong>Next Topic:</strong> [DB] 책 뒤에 있는 '찾아보기'가 쿼리 속도를 100배 올린다 (인덱스 기초)</p>
</blockquote>