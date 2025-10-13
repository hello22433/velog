<h1 id="💡-1-프로세스-동기화process-synchronization">💡 1. 프로세스 동기화(Process Synchronization)</h1>
<h2 id="🧠-왜-필요한가">🧠 왜 필요한가?</h2>
<p>현대 운영체제는 여러 프로세스/스레드가 동시에 실행돼.
→ 그런데 <strong>공유 자원(shared resource)</strong> 을 동시에 접근하면 문제가 생김.</p>
<p>예를 들어 보자 👇</p>
<pre><code class="language-c">int counter = 0;

void increment() {
    counter = counter + 1;
}</code></pre>
<ul>
<li>두 개의 스레드가 <code>increment()</code>를 동시에 실행한다고 하자.</li>
<li>CPU는 <code>counter</code>를 읽고, +1 하고, 다시 쓰는 세 단계를 거침.</li>
<li>동시에 접근하면 <strong>counter = 2</strong> 가 아니라 <strong>1</strong>이 될 수도 있다.</li>
</ul>
<p>이걸 <strong>경쟁 상태(Race Condition)</strong> 라고 해.</p>
<hr />
<h2 id="🧩-경쟁-상태-race-condition">🧩 경쟁 상태 (Race Condition)</h2>
<blockquote>
<p>여러 프로세스나 스레드가 <strong>동시에 공유 자원에 접근</strong>할 때,
실행 순서에 따라 결과가 달라지는 현상.</p>
</blockquote>
<h3 id="💥-예시-상황">💥 예시 상황</h3>
<ol>
<li>프로세스 A가 <code>counter</code>를 읽음 (0)</li>
<li>프로세스 B가 <code>counter</code>를 읽음 (0)</li>
<li>A가 <code>counter = 1</code> 저장</li>
<li>B가 <code>counter = 1</code> 저장 (A의 결과 덮어씀)</li>
</ol>
<p>결과: <code>counter = 1</code> ❌ (원래는 2가 되어야 함)</p>
<hr />
<h1 id="⚙️-2-임계-구역critical-section">⚙️ 2. 임계 구역(Critical Section)</h1>
<blockquote>
<p>여러 프로세스/스레드가 <strong>공유 자원을 접근하는 코드 영역</strong></p>
</blockquote>
<p><strong>규칙:</strong>
한 순간에 <strong>오직 하나의 프로세스만</strong> 임계 구역에 들어올 수 있어야 함.</p>
<p>이를 보장하기 위한 조건 3가지 👇</p>
<table>
<thead>
<tr>
<th>조건</th>
<th>설명</th>
</tr>
</thead>
<tbody><tr>
<td><strong>상호 배제(Mutual Exclusion)</strong></td>
<td>한 번에 하나만 접근 가능</td>
</tr>
<tr>
<td><strong>진행(Progress)</strong></td>
<td>임계 구역이 비어 있으면 들어갈 수 있어야 함</td>
</tr>
<tr>
<td><strong>유한 대기(Bounded Waiting)</strong></td>
<td>언젠가는 접근 기회를 가져야 함 (기아 방지)</td>
</tr>
</tbody></table>
<hr />
<h1 id="🔐-3-동기화-기법-synchronization-mechanisms">🔐 3. 동기화 기법 (Synchronization Mechanisms)</h1>
<p>이제 임계 구역 문제를 해결하는 대표적인 도구를 보자.</p>
<hr />
<h2 id="🧱-1-뮤텍스mutex">🧱 (1) 뮤텍스(Mutex)</h2>
<blockquote>
<p>Mutual Exclusion의 줄임말.
임계 구역을 보호하기 위한 <strong>잠금(lock)</strong> 기반 동기화 도구.</p>
</blockquote>
<ul>
<li>잠금(Lock)을 획득해야 임계 구역 진입 가능.</li>
<li>다른 스레드는 잠금이 해제될 때까지 대기.</li>
</ul>
<pre><code class="language-c">pthread_mutex_t lock;

void increment() {
    pthread_mutex_lock(&amp;lock);   // 잠금
    counter = counter + 1;
    pthread_mutex_unlock(&amp;lock); // 해제
}</code></pre>
<h3 id="✅-특징">✅ 특징</h3>
<ul>
<li>한 번에 <strong>1개의 스레드만 접근</strong></li>
<li>“lock / unlock” 명확히 호출해야 함</li>
<li>POSIX 스레드(pthread) 등에서 많이 사용</li>
</ul>
<hr />
<h2 id="⚖️-2-세마포어semaphore">⚖️ (2) 세마포어(Semaphore)</h2>
<blockquote>
<p>공유 자원 접근 개수를 제어하는 변수 (일종의 카운터)</p>
</blockquote>
<p>세마포어는 크게 두 종류로 나뉜다 👇</p>
<table>
<thead>
<tr>
<th>종류</th>
<th>설명</th>
</tr>
</thead>
<tbody><tr>
<td><strong>이진 세마포어 (Binary Semaphore)</strong></td>
<td>0 또는 1만 가짐 → 뮤텍스와 유사</td>
</tr>
<tr>
<td><strong>카운팅 세마포어 (Counting Semaphore)</strong></td>
<td>여러 개의 프로세스 접근 허용 가능</td>
</tr>
</tbody></table>
<hr />
<h3 id="🧩-세마포어-기본-동작">🧩 세마포어 기본 동작</h3>
<ul>
<li><code>wait()</code> : 자원 획득 (카운트 감소)</li>
<li><code>signal()</code> : 자원 반환 (카운트 증가)</li>
</ul>
<pre><code class="language-c">Semaphore S = 1;  // 1개의 자원

wait(S);
    // 임계 구역
signal(S);</code></pre>
<p><strong>Pseudo 코드</strong></p>
<pre><code class="language-c">wait(S):
    while (S &lt;= 0); // busy wait
    S--;

signal(S):
    S++;</code></pre>
<h3 id="✅-세마포어의-장점">✅ 세마포어의 장점</h3>
<ul>
<li>여러 프로세스/스레드 제어 가능</li>
<li>생산자-소비자 문제 등 다양한 패턴 해결 가능</li>
</ul>
<hr />
<h2 id="⚙️-3-모니터monitor">⚙️ (3) 모니터(Monitor)</h2>
<blockquote>
<p>고급 언어 수준에서 제공하는 동기화 도구.
세마포어나 뮤텍스보다 추상화된 개념.</p>
</blockquote>
<h3 id="📦-특징">📦 특징</h3>
<ul>
<li>모니터는 <strong>공유 자원 + 동기화 메커니즘을 함께 묶은 객체</strong></li>
<li>내부에서 자동으로 <strong>lock / unlock</strong> 관리</li>
<li>Java의 <code>synchronized</code>, Python의 <code>with Lock</code> 등이 모니터 기반</li>
</ul>
<pre><code class="language-java">class Counter {
    private int count = 0;

    public synchronized void increment() {
        count++;
    }
}</code></pre>
<h3 id="✅-모니터의-장점">✅ 모니터의 장점</h3>
<ul>
<li>코드가 깔끔하고 안전함</li>
<li>실수로 unlock을 안 하는 문제 없음</li>
<li>고수준 언어에서 일반적으로 사용</li>
</ul>
<hr />
<h1 id="🧩-4-고전적인-동기화-문제">🧩 4. 고전적인 동기화 문제</h1>
<p>면접에서 자주 나오는 “대표 동기화 문제들”이 있어.
이건 <strong>개념을 얼마나 잘 이해했는지</strong> 보는 질문들이야.</p>
<hr />
<h2 id="🧺-1-생산자소비자-문제-producerconsumer-problem">🧺 (1) 생산자–소비자 문제 (Producer–Consumer Problem)</h2>
<h3 id="🧠-상황">🧠 상황</h3>
<ul>
<li><strong>생산자</strong>: 버퍼에 데이터 넣음</li>
<li><strong>소비자</strong>: 버퍼에서 데이터 꺼냄
→ 동시에 접근하면 충돌 발생 가능</li>
</ul>
<h3 id="💡-해결-세마포어-이용">💡 해결 (세마포어 이용)</h3>
<ul>
<li><code>mutex</code>: 임계 구역 보호</li>
<li><code>empty</code>: 남은 공간</li>
<li><code>full</code>: 채워진 공간</li>
</ul>
<pre><code class="language-c">Semaphore mutex = 1;
Semaphore empty = N;  // 버퍼 크기
Semaphore full = 0;

Producer() {
    while (true) {
        wait(empty);
        wait(mutex);
        // 버퍼에 데이터 넣기
        signal(mutex);
        signal(full);
    }
}

Consumer() {
    while (true) {
        wait(full);
        wait(mutex);
        // 버퍼에서 데이터 꺼내기
        signal(mutex);
        signal(empty);
    }
}</code></pre>
<hr />
<h2 id="🍴-2-식사하는-철학자-문제-dining-philosophers-problem">🍴 (2) 식사하는 철학자 문제 (Dining Philosophers Problem)</h2>
<h3 id="🧠-상황-1">🧠 상황</h3>
<p>5명의 철학자가 원탁에 앉아 있고,
포크 5개가 있음. (철학자마다 양쪽에 하나씩)</p>
<p>모두 동시에 왼쪽 포크를 집으면? → 데드락 발생!</p>
<h3 id="💡-해결-방법">💡 해결 방법</h3>
<ul>
<li>한 번에 <strong>최대 4명까지만 식사 가능하게 제한</strong></li>
<li>혹은 <strong>짝수/홀수 철학자 순서 바꾸기</strong></li>
</ul>
<p>이 문제는 <strong>동시성 제어, 데드락, 세마포어</strong> 개념을 종합적으로 묻는 문제야.</p>
<hr />
<h1 id="🚦-5-면접-예시-질문">🚦 5. 면접 예시 질문</h1>
<table>
<thead>
<tr>
<th>질문</th>
<th>답변 요약</th>
</tr>
</thead>
<tbody><tr>
<td>동기화란 무엇인가요?</td>
<td>여러 프로세스/스레드가 공유 자원을 동시에 접근할 때 충돌을 막는 기술</td>
</tr>
<tr>
<td>임계 구역이란?</td>
<td>공유 자원을 접근하는 코드 영역</td>
</tr>
<tr>
<td>Race Condition이란?</td>
<td>실행 순서에 따라 결과가 달라지는 상황</td>
</tr>
<tr>
<td>세마포어와 뮤텍스 차이는?</td>
<td>세마포어는 자원 개수 제어, 뮤텍스는 단일 잠금</td>
</tr>
<tr>
<td>모니터란?</td>
<td>고수준 언어에서 제공하는 자동 동기화 구조</td>
</tr>
<tr>
<td>생산자–소비자 문제를 해결하는 방법은?</td>
<td>세마포어 3개(<code>mutex</code>, <code>empty</code>, <code>full</code>) 이용</td>
</tr>
</tbody></table>
<hr />