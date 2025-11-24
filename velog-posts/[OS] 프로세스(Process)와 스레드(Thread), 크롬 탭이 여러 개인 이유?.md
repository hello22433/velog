<h3 id="작업-관리자-켜보셨나요">&quot;작업 관리자 켜보셨나요?&quot;</h3>
<p>컴퓨터가 느려지면 습관적으로 Ctrl + Alt + Delete를 눌러 작업 관리자를 켭니다. 거기서 우리는 수많은 <strong>'프로세스'</strong>들이 내 메모리를 잡아먹고 있는 광경을 목격합니다.</p>
<p>그런데 개발자 채용 공고를 보면 <strong>&quot;멀티 프로세스 경험 우대&quot;</strong>가 아니라 <strong>&quot;멀티 스레드 환경 이해도 필수&quot;</strong>라고 적혀 있습니다.</p>
<p>도대체 프로세스는 뭐고 스레드는 뭘까요? 이 둘의 차이를 명확히 구분하는 것, 백엔드 개발자의 필수 소양입니다.</p>
<hr />
<h2 id="1-프로세스process-나-혼자-산다">1. 프로세스(Process): &quot;나 혼자 산다&quot;</h2>
<p>프로세스는 쉽게 말해 <strong>'실행 중인 프로그램'</strong>입니다. 여러분이 하드디스크에 저장된 IntelliJ 아이콘을 더블 클릭하는 순간, 메모리에 적재되어 <strong>프로세스</strong>가 됩니다.</p>
<h3 id="🏭-비유-독립적인-공장">🏭 비유: 독립적인 공장</h3>
<p>프로세스는 <strong>'완벽하게 독립된 공장'</strong>입니다.</p>
<ul>
<li>국가(OS)로부터 자신만의 땅(메모리)을 할당받습니다.</li>
<li><strong>Code, Data, Heap, Stack</strong>이라는 4가지 영역을 모두 독자적으로 가집니다.</li>
<li>그래서 A 프로세스가 고장 나서 터져도, B 프로세스에는 아무런 영향을 주지 않습니다.</li>
</ul>
<blockquote>
<p><strong>💡 크롬 탭이 프로세스인 이유</strong> 
크롬 브라우저는 탭 하나하나가 <strong>별도의 프로세스</strong>입니다. 그래서 특정 사이트가 응답 없음으로 멈춰도, 브라우저 전체가 꺼지지 않고 그 탭만 종료할 수 있는 것이죠. 
(단점: 메모리를 엄청나게 잡아먹습니다.)</p>
</blockquote>
<hr />
<h2 id="2-스레드thread-한-지붕-세-가족">2. 스레드(Thread): &quot;한 지붕 세 가족&quot;</h2>
<p>스레드는 <strong>'프로세스 안에서 실행되는 작업의 단위'</strong>입니다. 하나의 프로세스 안에는 반드시 하나 이상의 스레드가 존재합니다.</p>
<h3 id="👷-비유-공장-안의-일꾼들">👷 비유: 공장 안의 일꾼들</h3>
<p>스레드는 공장(프로세스) 안에서 일하는 <strong>'일꾼'</strong>입니다.</p>
<ul>
<li><strong>특징:</strong> 일꾼들은 공장의 자원을 공유합니다.</li>
<li><strong>공유하는 것:</strong> Code, Data, <strong>Heap(힙)</strong> 영역. (서로 데이터를 쉽게 주고받음)</li>
<li><strong>따로 가진 것: Stack(스택)</strong> 영역. (자신만의 작업 공간은 필요하니까요)</li>
</ul>
<p><img alt="" src="https://velog.velcdn.com/images/hello22433/post/9ef08567-0e10-4686-ba17-cb1ff4334121/image.png" /></p>
<hr />
<h2 id="3-왜-백엔드는-멀티-스레드를-쓸까">3. 왜 백엔드는 '멀티 스레드'를 쓸까?</h2>
<h3 id="안정적인-게-좋으면-그냥-프로세스를-여러-개-띄우면-안-되나요">&quot;안정적인 게 좋으면 그냥 프로세스를 여러 개 띄우면 안 되나요?&quot;</h3>
<p>웹 서버(Spring Boot 등)는 수백, 수천 명의 요청을 동시에 처리해야 합니다. 만약 <strong>사용자 1명당 프로세스 1개</strong>를 만든다면?</p>
<ol>
<li><p><strong>메모리 폭발:</strong> 프로세스는 덩치가 큽니다(Code, Data, Heap 다 따로 만듦). 서버 메모리가 남아나지 않습니다.</p>
</li>
<li><p><strong>비용 과다:</strong> 프로세스를 갈아 끼우는 비용(Context Switching)이 매우 비쌉니다.</p>
</li>
</ol>
<p>그래서 우리는 <strong>'멀티 스레드'</strong> 방식을 사용합니다. <strong>프로세스(서버)는 딱 하나만 띄워두고, 요청이 들어올 때마다 가벼운 스레드(일꾼)를 하나씩 배정</strong>해서 일을 시키는 것이죠.</p>
<blockquote>
<p><strong>⚠️ 치명적인 단점: 동시성 이슈</strong> 
스레드끼리는 <strong>Heap(데이터)</strong> 영역을 공유한다고 했죠? 만약 A 스레드가 &quot;통장 잔고&quot; 데이터를 수정하고 있는데, B 스레드가 동시에 와서 읽어가면? 돈이 복사되거나 증발하는 대참사가 일어납니다. 
이것이 바로 <strong>동시성 문제(Concurrency Issue)</strong>이며, 이를 막기 위해 Syncronized 같은 기술이 필요합니다.</p>
</blockquote>
<hr />
<h2 id="4-실전-면접-대비-3초-요약">4. 실전 면접 대비: 3초 요약</h2>
<p>면접관이 가장 좋아하는 질문 중 하나입니다.</p>
<h3 id="q-프로세스와-스레드의-차이를-설명해주세요">Q. 프로세스와 스레드의 차이를 설명해주세요.</h3>
<p><strong>A. &quot;프로세스</strong>는 운영체제로부터 자원을 할당받는 가장 작은 <strong>작업 단위</strong>이고, <strong>스레드</strong>는 프로세스 내에서 실행되는 <strong>흐름의 단위</strong>입니다. 가장 큰 차이는 <strong>메모리 공유 여부</strong>입니다. 프로세스는 메모리가 완전히 분리되어 있지만, 스레드는 <strong>Stack만 따로 가지고 Code, Data, Heap 영역은 공유</strong>합니다.&quot;</p>
<h3 id="q-크롬-탭은-프로세스인가요-스레드인가요">Q. 크롬 탭은 프로세스인가요 스레드인가요?</h3>
<p><strong>A.</strong> &quot;크롬은 안정성을 위해 탭마다 별도의 <strong>프로세스</strong>를 생성하는 멀티 프로세스 방식을 주로 사용합니다. 덕분에 하나의 탭에 문제가 생겨도 전체 브라우저가 종료되지 않습니다.&quot;</p>
<hr />
<h2 id="🚀-part-1을-마치며">🚀 Part 1을 마치며</h2>
<p>지금까지 <strong>JVM 메모리 구조, GC, 그리고 프로세스와 스레드</strong>까지. 자바 백엔드 개발자가 컴퓨터를 이해하는 데 필요한 가장 기초적인 지식을 쌓았습니다.</p>
<p>이제 여러분의 컴퓨터 내부(Local)에 대한 이야기는 끝났습니다. 다음 시간부터는 내 컴퓨터가 <strong>바깥세상과 소통하는 법, Part 2. 네트워크(Network)</strong> 편이 시작됩니다.</p>
<p>첫 번째 주제는 개발자 면접의 '바이블'과도 같은 질문입니다.</p>
<h3 id="⏭-next-topic">⏭ Next Topic:</h3>
<p>[Network] 주소창에 <a href="http://www.google.com%EC%9D%84">www.google.com을</a> 치면 일어나는 일 (면접 프리패스)</p>