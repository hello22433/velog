<p><strong>[Step 3. 중등 단계]</strong> 본문입니다.</p>
<p>이제부터는 겉으로 보이는 화면을 넘어, <strong>코드 안쪽(Back-end)</strong>의 이야기를 다룹니다. 프로그래밍 입문자가 가장 먼저 배우지만, 영어 뜻을 몰라 무작정 외우게 되는 <code>String</code>, <code>Boolean</code> 같은 단어들의 <strong>'어원과 진짜 의미'</strong>를 풀어냈습니다.</p>
<hr />
<p>유치원(하드웨어), 초등(명령어) 단계를 지나 이제 <strong>중등 단계</strong>에 오신 것을 환영합니다.</p>
<p>이제부터는 조금 추상적인 이야기를 해보려 합니다.
프로그래밍을 배우다 보면 <code>int</code>, <code>String</code>, <code>bool</code> 같은 외계어들이 등장합니다. 이것들은 컴퓨터가 데이터를 다루는 방식, 즉 <strong>자료형(Data Type)</strong>의 이름입니다.</p>
<p>이 영어 단어들의 유래만 알아도 코딩이 훨씬 쉬워집니다.</p>
<br />

<h2 id="1-글자와-그들의-연결-text">1. 글자와 그들의 연결 (Text)</h2>
<p>컴퓨터에게 &quot;가&quot;는 글자이고, &quot;가나다&quot;는 글자의 묶음입니다. 이 둘을 부르는 이름이 다릅니다.</p>
<h3 id="🔣-character-캐릭터">🔣 Character (캐릭터)</h3>
<ul>
<li><strong>줄임말:</strong> <code>char</code> (주로 '차' 또는 '캐러'라고 읽음)</li>
<li><strong>의미:</strong> 성격, 등장인물, <strong>(글자) 문자</strong></li>
<li><strong>설명:</strong> 소설 속 등장인물 한 명 한 명을 캐릭터라고 하죠? 컴퓨터에서는 <strong>글자 하나'</strong>를 캐릭터라고 부릅니다.<ul>
<li><code>'A'</code>, <code>'B'</code>, <code>'1'</code> (따옴표로 감싼 하나)</li>
</ul>
</li>
</ul>
<h3 id="🧵-string-스트링">🧵 String (스트링)</h3>
<ul>
<li><strong>의미:</strong> 끈, 줄</li>
<li><strong>전산적 의미:</strong> <strong>문자열 (글자 덩어리)</strong></li>
<li><strong>설명:</strong> 왜 글자 덩어리를 '끈(String)'이라고 부를까요?<ul>
<li>진주 목걸이를 상상해보세요. 진주 알 하나가 <code>Character</code>라면, 이 알들을 <strong>실(String)로 꿰어놓은 것</strong>이 바로 단어와 문장이기 때문입니다.</li>
<li>개발자가 *&quot;이거 스트링으로 처리해&quot;* 라고 하면 *&quot;이거 문자로 취급해&quot;* 라는 뜻입니다.</li>
</ul>
</li>
</ul>
<br />

<h2 id="2-숫자의-세계-numbers">2. 숫자의 세계 (Numbers)</h2>
<p>수학 시간에는 실수, 정수, 자연수를 배웠죠? 영어로는 딱 두 가지만 기억하면 됩니다.</p>
<h3 id="🔢-integer-인티저">🔢 Integer (인티저)</h3>
<ul>
<li><strong>줄임말:</strong> <code>int</code> (인트)</li>
<li><strong>어원:</strong> 라틴어로 '손대지 않은', '완전한'</li>
<li><strong>전산적 의미:</strong> <strong>정수 (소수점 없는 숫자)</strong></li>
<li><strong>설명:</strong> 쪼개지지 않은 온전한 숫자입니다.<ul>
<li><code>1</code>, <code>100</code>, <code>-5</code> (O)</li>
<li><code>3.14</code> (X) -&gt; 이건 쪼개진 숫자죠?</li>
</ul>
</li>
</ul>
<h3 id="⛵-float-플로트--double-더블">⛵ Float (플로트) / Double (더블)</h3>
<ul>
<li><strong>Float 의미:</strong> (물 위에) 둥둥 뜨다</li>
<li><strong>전산적 의미:</strong> <strong>실수 (소수점 있는 숫자)</strong></li>
<li><strong>설명:</strong> 갑자기 왜 '뜨다'일까요?<ul>
<li><code>3.14</code>라는 숫자를 볼 때 소수점(.)이 고정되어 있지 않고, 계산에 따라 둥둥 떠다니며 움직인다고 해서 <strong>'부동(Floating) 소수점'</strong>이라고 부릅니다.</li>
<li>그냥 <strong>&quot;소수점 있는 숫자는 Float&quot;</strong>라고 기억하면 됩니다.</li>
</ul>
</li>
</ul>
<br />

<h2 id="3-참과-거짓-그리고-무-logic--void">3. 참과 거짓, 그리고 무 (Logic &amp; Void)</h2>
<p>컴퓨터는 아주 단순해서 '그렇다/아니다'로 세상을 봅니다.</p>
<h3 id="⚖️-boolean-불리언">⚖️ Boolean (불리언)</h3>
<ul>
<li><strong>줄임말:</strong> <code>bool</code> (불)</li>
<li><strong>의미:</strong> 논리 자료형</li>
<li><strong>설명:</strong> 영단어 뜻이 있는 게 아니라, 조지 불(George Boole)이라는 수학자 할아버지의 이름을 땄습니다. 이분은 세상을 <strong>True(참)</strong>와 <strong>False(거짓)</strong>, 딱 두 가지로만 계산하는 체계를 만들었거든요.<ul>
<li>개발자가 *&quot;이 변수 불리언이야?&quot;* 라고 묻는다면, *&quot;이거 Yes 아니면 No야?&quot;* 라는 뜻입니다.</li>
</ul>
</li>
</ul>
<h3 id="🕳️-null-널-vs-void-보이드">🕳️ Null (널) vs Void (보이드)</h3>
<p>둘 다 '없음'을 뜻하지만 뉘앙스가 다릅니다.</p>
<ul>
<li><strong>Null:</strong> <strong>(알맹이가) 비어있음.</strong><ul>
<li>휴지걸이에 휴지심조차 없고 텅 비어있는 상태입니다. &quot;값이 존재하지 않는다&quot;는 것을 명시할 때 씁니다.</li>
</ul>
</li>
<li><strong>Void:</strong> <strong>(공간이) 공허함 / 되돌려줄 게 없음.</strong><ul>
<li>우주 공간(The Void)처럼 아무것도 없는 상태입니다. 주로 함수가 일을 다 하고 나서 &quot;난 결과물로 줄 게 없어(반환할 값이 없어)&quot;라고 할 때 씁니다.</li>
</ul>
</li>
</ul>
<br />

<h2 id="🍎-요약-summary">🍎 요약 (Summary)</h2>
<p>오늘은 코딩의 기초 재료인 <strong>데이터의 종류</strong>를 영어로 살펴봤습니다.</p>
<ol>
<li>글자가 실처럼 꿰어진 것이 <strong>String</strong>(문자열)이다.</li>
<li>소수점이 둥둥 떠다니는 숫자는 <strong>Float</strong>(실수)다.</li>
<li>참/거짓을 따지는 것은 수학자 이름을 따서 <strong>Boolean</strong>이라고 한다.</li>
</ol>
<p>이제 재료(데이터)를 알았으니, 요리법(문법)을 배울 차례입니다.
다음 시간, <strong>[Step 4. 고등 단계: 코딩의 문법과 논리]</strong>에서는 영어 문법 시간인지 코딩 시간인지 헷갈리는 <code>If</code>, <code>While</code>, <code>Loop</code> 등을 다룹니다.</p>