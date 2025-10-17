<h2 id="🧠-1️⃣-ipc-inter-process-communication란">🧠 1️⃣ IPC (Inter-Process Communication)란?</h2>
<blockquote>
<p><strong>서로 독립적인 프로세스들이 데이터를 주고받는 방법</strong></p>
</blockquote>
<p>프로세스는 각각 <strong>독립된 메모리 공간</strong>을 가지고 있어서
직접 변수나 메모리를 공유할 수 없습니다.</p>
<p>그래서 운영체제가 <strong>특수한 통신 메커니즘(IPC 메커니즘)</strong> 을 제공합니다.</p>
<hr />
<h2 id="⚙️-2️⃣-ipc가-필요한-이유">⚙️ 2️⃣ IPC가 필요한 이유</h2>
<table>
<thead>
<tr>
<th>이유</th>
<th>설명</th>
</tr>
</thead>
<tbody><tr>
<td>데이터 교환</td>
<td>한 프로세스의 결과를 다른 프로세스가 사용</td>
</tr>
<tr>
<td>협력 작업</td>
<td>여러 프로세스가 하나의 작업을 분담</td>
</tr>
<tr>
<td>자원 공유</td>
<td>파일, 메모리, 장치 등을 효율적으로 이용</td>
</tr>
<tr>
<td>동기화</td>
<td>실행 순서나 타이밍을 맞춤 (세마포어 등)</td>
</tr>
</tbody></table>
<hr />
<h2 id="🔌-3️⃣-ipc의-주요-방식-면접에서-자주-나오는-6가지">🔌 3️⃣ IPC의 주요 방식 (면접에서 자주 나오는 6가지)</h2>
<table>
<thead>
<tr>
<th>방식</th>
<th>설명</th>
<th>특징</th>
</tr>
</thead>
<tbody><tr>
<td><strong>① 파이프 (Pipe)</strong></td>
<td>한쪽에서 쓰고 한쪽에서 읽는 단방향 통신</td>
<td>부모-자식 간 통신에 주로 사용</td>
</tr>
<tr>
<td><strong>② FIFO (Named Pipe)</strong></td>
<td>이름이 있는 파이프. 서로 다른 프로세스 간 통신 가능</td>
<td>파일처럼 다룸 (<code>mkfifo</code>)</td>
</tr>
<tr>
<td><strong>③ 메시지 큐 (Message Queue)</strong></td>
<td>메시지를 큐 형태로 교환</td>
<td>비동기, 구조화된 메시지 교환</td>
</tr>
<tr>
<td><strong>④ 공유 메모리 (Shared Memory)</strong></td>
<td>여러 프로세스가 같은 메모리 영역을 공유</td>
<td>빠르지만 동기화 필요</td>
</tr>
<tr>
<td><strong>⑤ 세마포어 (Semaphore)</strong></td>
<td>프로세스 간 동기화·제어</td>
<td>데이터 공유를 보호</td>
</tr>
<tr>
<td><strong>⑥ 소켓 (Socket)</strong></td>
<td>네트워크 기반 프로세스 간 통신</td>
<td>로컬 or 원격 프로세스 모두 가능</td>
</tr>
</tbody></table>
<hr />
<h2 id="🧩-4️⃣-각-방식-자세히-보기">🧩 4️⃣ 각 방식 자세히 보기</h2>
<hr />
<h3 id="①-파이프-pipe"><strong>① 파이프 (Pipe)</strong></h3>
<ul>
<li>부모 프로세스가 자식 프로세스를 <code>fork()</code>로 만든 뒤,
한쪽에서 쓰고, 한쪽에서 읽습니다.</li>
<li>단방향 (Half Duplex)</li>
<li>파일 디스크립터 형태로 관리</li>
</ul>
<h4 id="예시-c-코드">예시 (C 코드)</h4>
<pre><code class="language-c">int fd[2];
pipe(fd);
if (fork() == 0) {
    close(fd[0]);
    write(fd[1], &quot;hello&quot;, 5);
} else {
    close(fd[1]);
    char buf[10];
    read(fd[0], buf, 5);
    printf(&quot;%s\n&quot;, buf); // 출력: hello
}</code></pre>
<p>📌 <strong>제한점:</strong> 부모-자식 간 통신만 가능, 양방향이 아님.</p>
<hr />
<h3 id="②-fifo-named-pipe"><strong>② FIFO (Named Pipe)</strong></h3>
<ul>
<li>이름이 있는 파이프 (<code>mkfifo()</code>로 생성)</li>
<li>완전히 독립적인 프로세스 간에도 통신 가능</li>
<li>파일처럼 접근 가능</li>
</ul>
<pre><code class="language-bash">mkfifo mypipe</code></pre>
<pre><code class="language-c">int fd = open(&quot;mypipe&quot;, O_WRONLY);
write(fd, &quot;data&quot;, 4);</code></pre>
<hr />
<h3 id="③-메시지-큐-message-queue"><strong>③ 메시지 큐 (Message Queue)</strong></h3>
<ul>
<li>운영체제가 제공하는 <strong>메시지 버퍼 큐</strong> 사용</li>
<li>구조화된 메시지를 보낼 수 있음 (id, type, data)</li>
<li>비동기 통신이 가능</li>
</ul>
<p>📌 장점:</p>
<ul>
<li>송신자/수신자가 동시에 존재할 필요 없음.</li>
<li>커널이 메시지를 관리하므로 안전함.</li>
</ul>
<hr />
<h3 id="④-공유-메모리-shared-memory"><strong>④ 공유 메모리 (Shared Memory)</strong></h3>
<blockquote>
<p><strong>가장 빠른 IPC 방식</strong></p>
</blockquote>
<ul>
<li>여러 프로세스가 <strong>같은 메모리 영역을 직접 공유</strong></li>
<li>운영체제가 그 영역을 공통 주소 공간에 매핑함</li>
<li>단, 동시에 접근 시 <strong>세마포어나 뮤텍스</strong>로 동기화 필요</li>
</ul>
<h4 id="예시">예시</h4>
<pre><code class="language-c">int shmid = shmget((key_t)1234, 1024, IPC_CREAT|0666);
char *data = (char *)shmat(shmid, NULL, 0);
strcpy(data, &quot;Shared Hello&quot;);</code></pre>
<p>📌 <strong>속도는 빠르지만</strong>, 동기화가 어렵다는 단점 존재.</p>
<hr />
<h3 id="⑤-세마포어-semaphore"><strong>⑤ 세마포어 (Semaphore)</strong></h3>
<ul>
<li>공유 메모리를 안전하게 사용하기 위한 <strong>동기화 도구</strong></li>
<li>0 또는 1을 사용하는 <strong>이진 세마포어(Binary Semaphore)</strong> 는 뮤텍스(Mutex)와 유사.</li>
<li><code>wait()</code> / <code>signal()</code> 연산으로 임계구역 보호.</li>
</ul>
<pre><code class="language-c">wait(s);   // P 연산
// Critical Section
signal(s); // V 연산</code></pre>
<hr />
<h3 id="⑥-소켓-socket"><strong>⑥ 소켓 (Socket)</strong></h3>
<blockquote>
<p>네트워크 기반 프로세스 간 통신 (로컬 or 원격)</p>
</blockquote>
<ul>
<li>서버-클라이언트 구조</li>
<li>동일한 머신의 프로세스끼리도 가능 (<code>localhost</code>)</li>
</ul>
<h4 id="예시-1">예시</h4>
<pre><code class="language-c">int sock = socket(AF_INET, SOCK_STREAM, 0);
bind(sock, ...);
listen(sock, ...);
accept(sock, ...);</code></pre>
<p>📌 Node.js의 HTTP 서버, Redis 클라이언트, Kafka, gRPC 등도 모두 <strong>소켓 기반 IPC</strong> 구조 위에 있습니다.</p>
<hr />
<h2 id="🧠-5️⃣-면접-핵심-요약">🧠 5️⃣ 면접 핵심 요약</h2>
<table>
<thead>
<tr>
<th>질문</th>
<th>답변 요약</th>
</tr>
</thead>
<tbody><tr>
<td>Q. 프로세스 간 통신이 필요한 이유는?</td>
<td>독립된 메모리 공간을 가진 프로세스 간 데이터 교환</td>
</tr>
<tr>
<td>Q. 파이프와 FIFO의 차이는?</td>
<td>파이프는 부모-자식만, FIFO는 이름을 가진 독립 프로세스 간 가능</td>
</tr>
<tr>
<td>Q. 메시지 큐의 장점은?</td>
<td>구조화된 메시지, 비동기 처리</td>
</tr>
<tr>
<td>Q. 공유 메모리의 장단점은?</td>
<td>빠르지만 동기화 필요</td>
</tr>
<tr>
<td>Q. 세마포어의 역할은?</td>
<td>공유 자원의 동시 접근을 제어</td>
</tr>
<tr>
<td>Q. 소켓 IPC는 언제 쓰나요?</td>
<td>네트워크나 다른 머신 간 통신이 필요할 때</td>
</tr>
</tbody></table>
<hr />
<h2 id="💡-핵심-한-줄-요약">💡 핵심 한 줄 요약</h2>
<blockquote>
<p>IPC는 독립된 프로세스 간 협력을 위한 통신 메커니즘이며,
공유 메모리는 빠르지만 동기화가 어렵고, 소켓은 가장 유연하다.</p>
</blockquote>
<hr />