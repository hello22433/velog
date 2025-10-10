<h3 id="1️⃣-운영체제가-하는-일">1️⃣ 운영체제가 하는 일</h3>
<p>운영체제는 <strong>하드웨어와 사용자(혹은 응용 프로그램) 사이에서 중재자 역할을 하는 소프트웨어</strong>야.</p>
<p><strong>비유:</strong>
컴퓨터를 ‘회사’라고 하면,</p>
<ul>
<li>CPU → 직원</li>
<li>메모리 → 책상</li>
<li>디스크 → 창고</li>
<li>운영체제(OS) → 관리직 (일을 배분하고 자원 낭비를 막음)</li>
</ul>
<hr />
<h3 id="2️⃣-운영체제의-주요-기능">2️⃣ 운영체제의 주요 기능</h3>
<table>
<thead>
<tr>
<th>분류</th>
<th>설명</th>
</tr>
</thead>
<tbody><tr>
<td><strong>프로세스 관리</strong></td>
<td>프로그램 실행 단위인 프로세스의 생성, 스케줄링, 종료를 관리</td>
</tr>
<tr>
<td><strong>메모리 관리</strong></td>
<td>한정된 메모리를 여러 프로세스가 효율적으로 나눠 쓰도록 관리</td>
</tr>
<tr>
<td><strong>파일 시스템 관리</strong></td>
<td>파일의 저장, 읽기/쓰기, 권한 제어 등</td>
</tr>
<tr>
<td><strong>입출력(I/O) 관리</strong></td>
<td>키보드, 마우스, 디스크, 네트워크 등 하드웨어 장치 제어</td>
</tr>
<tr>
<td><strong>보안 및 보호</strong></td>
<td>사용자 권한, 접근 제어, 프로세스 간 간섭 방지</td>
</tr>
</tbody></table>
<hr />
<h2 id="⚙️-프로세스process">⚙️ 프로세스(Process)</h2>
<h3 id="1️⃣-프로세스란">1️⃣ 프로세스란?</h3>
<blockquote>
<p>실행 중인 프로그램 (실행 상태의 프로그램)</p>
</blockquote>
<ul>
<li>프로그램이 디스크에 있을 때는 단순한 <strong>파일</strong></li>
<li>메모리에 올라와 CPU에서 실행되면 <strong>프로세스</strong></li>
</ul>
<h3 id="2️⃣-프로세스-구조-메모리-구성">2️⃣ 프로세스 구조 (메모리 구성)</h3>
<table>
<thead>
<tr>
<th>영역</th>
<th>설명</th>
</tr>
</thead>
<tbody><tr>
<td><strong>Code</strong></td>
<td>실행할 기계어 코드 (함수, 명령 등)</td>
</tr>
<tr>
<td><strong>Data</strong></td>
<td>전역변수, static 변수</td>
</tr>
<tr>
<td><strong>Heap</strong></td>
<td>동적 메모리 (<code>malloc</code>, <code>new</code> 등으로 할당)</td>
</tr>
<tr>
<td><strong>Stack</strong></td>
<td>함수 호출 시 지역 변수, 매개변수, 리턴 주소</td>
</tr>
</tbody></table>
<p><strong>면접 포인트</strong></p>
<blockquote>
<p>&quot;스택과 힙의 차이를 설명하세요.&quot;</p>
</blockquote>
<p>✅ <strong>Stack</strong></p>
<ul>
<li>함수 호출 시 자동 할당/해제</li>
<li>크기가 한정됨</li>
<li>컴파일 타임에 크기 결정</li>
</ul>
<p>✅ <strong>Heap</strong></p>
<ul>
<li><code>malloc</code> / <code>free</code>로 수동 관리</li>
<li>런타임에 크기 결정 가능</li>
<li>관리가 어렵지만 유연함</li>
</ul>
<hr />
<h2 id="🧵-스레드thread">🧵 스레드(Thread)</h2>
<h3 id="1️⃣-스레드란">1️⃣ 스레드란?</h3>
<blockquote>
<p>하나의 프로세스 내에서 실행되는 “작업의 흐름 단위”</p>
</blockquote>
<ul>
<li>프로세스는 최소 1개의 스레드를 가짐 (main thread)</li>
<li>여러 스레드가 한 프로세스 내 자원을 <strong>공유</strong></li>
</ul>
<p><strong>공유하는 자원</strong></p>
<ul>
<li>Code, Data, Heap (공유)</li>
<li>Stack (개별)</li>
</ul>
<hr />
<h3 id="2️⃣-프로세스-vs-스레드-비교">2️⃣ 프로세스 vs 스레드 비교</h3>
<table>
<thead>
<tr>
<th>구분</th>
<th>프로세스</th>
<th>스레드</th>
</tr>
</thead>
<tbody><tr>
<td>정의</td>
<td>실행 중인 프로그램</td>
<td>프로세스 내 실행 흐름</td>
</tr>
<tr>
<td>메모리</td>
<td>독립적 메모리 공간</td>
<td>스택만 독립, 나머지 공유</td>
</tr>
<tr>
<td>생성 비용</td>
<td>큼 (fork 등)</td>
<td>작음</td>
</tr>
<tr>
<td>통신</td>
<td>IPC 필요 (파이프, 소켓 등)</td>
<td>공유 메모리로 가능</td>
</tr>
<tr>
<td>안정성</td>
<td>독립적, 하나 죽어도 영향 X</td>
<td>하나 죽으면 프로세스 전체 영향</td>
</tr>
</tbody></table>
<p><strong>면접 포인트</strong></p>
<blockquote>
<p>“프로세스 대신 스레드를 쓰는 이유는?”
→ 빠르고 자원 공유가 쉬워서
“스레드의 단점은?”
→ 동기화 문제로 인한 경쟁 상태(race condition) 발생 가능</p>
</blockquote>
<hr />
<h2 id="🧠-cpu-스케줄링">🧠 CPU 스케줄링</h2>
<h3 id="1️⃣-스케줄러란">1️⃣ 스케줄러란?</h3>
<p>CPU를 어떤 프로세스에 배정할지 결정하는 운영체제의 핵심 모듈.</p>
<h3 id="2️⃣-주요-스케줄링-알고리즘">2️⃣ 주요 스케줄링 알고리즘</h3>
<table>
<thead>
<tr>
<th>알고리즘</th>
<th>설명</th>
<th>특징</th>
</tr>
</thead>
<tbody><tr>
<td><strong>FCFS (First Come First Serve)</strong></td>
<td>먼저 온 순서대로 실행</td>
<td>단점: 긴 작업이 먼저 오면 뒤에 기다리는 프로세스가 오래 대기 (Convoy effect)</td>
</tr>
<tr>
<td><strong>SJF (Shortest Job First)</strong></td>
<td>CPU 버스트 시간이 짧은 것부터</td>
<td>평균 대기 시간 최소화</td>
</tr>
<tr>
<td><strong>RR (Round Robin)</strong></td>
<td>일정 시간 단위(time quantum)로 돌아가며 실행</td>
<td>응답 시간 보장</td>
</tr>
<tr>
<td><strong>Priority Scheduling</strong></td>
<td>우선순위가 높은 프로세스부터 실행</td>
<td>starvation 문제 가능</td>
</tr>
<tr>
<td><strong>Multilevel Queue</strong></td>
<td>프로세스 종류에 따라 여러 큐로 분리</td>
<td>복합적 환경에 유리</td>
</tr>
</tbody></table>
<hr />
<h2 id="💾-메모리-관리">💾 메모리 관리</h2>
<h3 id="1️⃣-가상-메모리virtual-memory">1️⃣ 가상 메모리(Virtual Memory)</h3>
<blockquote>
<p>실제 물리 메모리(RAM)보다 큰 메모리 공간을 <strong>논리적으로 제공하는 기술</strong></p>
</blockquote>
<ul>
<li>프로세스는 “자신만의 메모리 공간”을 가지는 것처럼 보임</li>
<li>운영체제가 페이지 단위로 실제 물리 메모리와 디스크(Swap)를 매핑</li>
</ul>
<p><strong>면접 포인트</strong></p>
<blockquote>
<p>“가상 메모리를 사용하는 이유?”</p>
</blockquote>
<ul>
<li>여러 프로그램을 동시에 실행 가능</li>
<li>메모리 보호 및 격리</li>
<li>효율적인 메모리 활용</li>
</ul>
<hr />
<h2 id="💣-페이지-교체-알고리즘">💣 페이지 교체 알고리즘</h2>
<p>가상 메모리에서 새로운 페이지를 불러와야 할 때,
기존 페이지 중 하나를 내보내야 함.</p>
<table>
<thead>
<tr>
<th>알고리즘</th>
<th>설명</th>
</tr>
</thead>
<tbody><tr>
<td><strong>FIFO</strong></td>
<td>가장 먼저 들어온 페이지를 교체</td>
</tr>
<tr>
<td><strong>LRU (Least Recently Used)</strong></td>
<td>가장 오랫동안 사용되지 않은 페이지 교체</td>
</tr>
<tr>
<td><strong>LFU (Least Frequently Used)</strong></td>
<td>사용 빈도가 가장 낮은 페이지 교체</td>
</tr>
<tr>
<td><strong>Optimal</strong></td>
<td>앞으로 가장 오랫동안 사용되지 않을 페이지 교체 (이론적 최적)</td>
</tr>
</tbody></table>
<hr />
<h2 id="🔐-교착상태deadlock">🔐 교착상태(Deadlock)</h2>
<blockquote>
<p>둘 이상의 프로세스가 서로 자원을 기다리며 무한 대기하는 상태</p>
</blockquote>
<p><strong>발생 조건 (4가지 모두 만족해야 함)</strong></p>
<ol>
<li>상호 배제(Mutual Exclusion)</li>
<li>점유 대기(Hold and Wait)</li>
<li>비선점(No Preemption)</li>
<li>순환 대기(Circular Wait)</li>
</ol>
<p><strong>해결 방법</strong></p>
<ul>
<li><strong>예방</strong>: 위 조건 중 하나를 깨뜨림</li>
<li><strong>회피</strong>: 자원 할당 전 미리 검증 (은행원 알고리즘)</li>
<li><strong>탐지 및 복구</strong>: 데드락 발생 후 강제 종료</li>
</ul>
<hr />
<h2 id="🧩-면접-예시-질문">🧩 면접 예시 질문</h2>
<ol>
<li>운영체제가 하는 역할은 무엇인가요?</li>
<li>프로세스와 스레드의 차이를 설명하세요.</li>
<li>스택과 힙의 차이는 무엇인가요?</li>
<li>가상 메모리란 무엇이며, 왜 필요한가요?</li>
<li>데드락은 언제 발생하나요?</li>
<li>CPU 스케줄링의 종류와 특징을 설명하세요.</li>
<li>멀티스레드 환경에서 발생할 수 있는 문제는?</li>
</ol>
<hr />