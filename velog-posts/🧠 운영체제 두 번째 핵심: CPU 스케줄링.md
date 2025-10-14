<h3 id="1️⃣-개념">1️⃣ 개념</h3>
<p>운영체제는 <strong>CPU를 어떤 프로세스에게, 얼마나 오랫동안 줄지 결정</strong>해야 합니다.
이 과정을 <strong>CPU 스케줄링(CPU Scheduling)</strong> 이라고 해요.</p>
<p>CPU는 한 번에 하나의 프로세스만 실행할 수 있으므로,
여러 프로세스가 경쟁할 때 누가 먼저 CPU를 쓰는지가 성능을 좌우합니다.</p>
<hr />
<h3 id="2️⃣-프로세스-상태-복습">2️⃣ 프로세스 상태 복습</h3>
<pre><code>생성(new) → 준비(ready) → 실행(running) → 대기(waiting) → 종료(terminated)</code></pre><p>CPU 스케줄러는 <strong>ready 상태</strong>의 프로세스 중 하나를 골라 <strong>running 상태</strong>로 바꿉니다.</p>
<hr />
<h3 id="3️⃣-cpu-스케줄링-기준-면접에서-자주-나옴">3️⃣ CPU 스케줄링 기준 (면접에서 자주 나옴)</h3>
<table>
<thead>
<tr>
<th>기준</th>
<th>설명</th>
</tr>
</thead>
<tbody><tr>
<td><strong>CPU 이용률</strong></td>
<td>CPU가 놀지 않고 일하는 비율 (높을수록 좋음)</td>
</tr>
<tr>
<td><strong>처리량(Throughput)</strong></td>
<td>단위 시간당 완료된 프로세스 수</td>
</tr>
<tr>
<td><strong>대기 시간(Waiting Time)</strong></td>
<td>Ready Queue에서 기다린 총 시간</td>
</tr>
<tr>
<td><strong>응답 시간(Response Time)</strong></td>
<td>요청 후 첫 응답까지 걸린 시간</td>
</tr>
<tr>
<td><strong>Turnaround Time(반환 시간)</strong></td>
<td>실행 시작부터 종료까지 걸린 전체 시간</td>
</tr>
</tbody></table>
<hr />
<h3 id="4️⃣-주요-스케줄링-알고리즘">4️⃣ 주요 스케줄링 알고리즘</h3>
<h4 id="1-fcfs-first-come-first-served">(1) FCFS (First Come, First Served)</h4>
<ul>
<li>먼저 온 프로세스가 먼저 처리됨.</li>
<li><strong>FIFO 큐</strong> 방식.</li>
<li>간단하지만 <strong>convoy effect(호위 효과)</strong> 발생 → 긴 작업이 짧은 작업을 막음.</li>
</ul>
<p>예시:</p>
<pre><code>P1: 24ms, P2: 3ms, P3: 3ms
→ 평균 대기시간 = (0 + 24 + 27) / 3 = 17ms</code></pre><hr />
<h4 id="2-sjf-shortest-job-first">(2) SJF (Shortest Job First)</h4>
<ul>
<li><strong>CPU 버스트 시간(예상 실행 시간)</strong> 이 짧은 프로세스부터 실행.</li>
<li>평균 대기 시간을 최소화할 수 있음.</li>
<li>하지만 <strong>예측이 어렵고</strong>, 긴 작업은 <strong>기아(starvation)</strong> 가능.</li>
</ul>
<hr />
<h4 id="3-srtf-shortest-remaining-time-first">(3) SRTF (Shortest Remaining Time First)</h4>
<ul>
<li>SJF의 <strong>선점형 버전</strong>.</li>
<li>새로운 프로세스가 들어왔을 때, 남은 시간이 더 짧으면 <strong>선점</strong>.</li>
<li>평균 대기시간은 짧지만, <strong>오버헤드 증가</strong>.</li>
</ul>
<hr />
<h4 id="4-rr-round-robin">(4) RR (Round Robin)</h4>
<ul>
<li><strong>시분할 시스템</strong>에서 가장 일반적.</li>
<li>모든 프로세스에 <strong>동일한 시간 할당량(Time Quantum)</strong> 을 줌.</li>
<li>Time Quantum이 너무 작으면 → <strong>Context Switching 오버헤드 증가</strong></li>
<li>너무 크면 → <strong>FCFS와 다를 게 없어짐</strong></li>
</ul>
<p>예시:</p>
<pre><code>Time Quantum = 4ms
프로세스: P1(6), P2(8), P3(7)
→ P1(4) → P2(4) → P3(4) → P1(2) → P2(4) → P3(3)</code></pre><hr />
<h4 id="5-priority-scheduling">(5) Priority Scheduling</h4>
<ul>
<li><strong>우선순위(priority)</strong> 가 높은 프로세스를 먼저 실행.</li>
<li>낮은 우선순위는 계속 밀릴 수 있어 <strong>기아 현상(starvation)</strong> 발생.</li>
<li>해결책 → <strong>Aging 기법</strong> (시간이 지날수록 우선순위 점점 높여줌)</li>
</ul>
<hr />
<h3 id="5️⃣-선점형-vs-비선점형">5️⃣ 선점형 vs 비선점형</h3>
<table>
<thead>
<tr>
<th>구분</th>
<th>설명</th>
<th>예시</th>
</tr>
</thead>
<tbody><tr>
<td><strong>선점형(Preemptive)</strong></td>
<td>실행 중에도 OS가 강제로 CPU를 뺏을 수 있음</td>
<td>SRTF, RR, Priority(선점형)</td>
</tr>
<tr>
<td><strong>비선점형(Non-Preemptive)</strong></td>
<td>프로세스가 CPU를 스스로 놓을 때까지 기다림</td>
<td>FCFS, SJF(비선점형)</td>
</tr>
</tbody></table>
<hr />
<h3 id="6️⃣-면접에서-자주-묻는-질문">6️⃣ 면접에서 자주 묻는 질문</h3>
<table>
<thead>
<tr>
<th>질문</th>
<th>답변 요약</th>
</tr>
</thead>
<tbody><tr>
<td>Q. FCFS의 단점은?</td>
<td>긴 작업이 짧은 작업을 지연시킴 (Convoy Effect)</td>
</tr>
<tr>
<td>Q. SJF의 장단점은?</td>
<td>효율적이지만 실행 시간 예측이 어려움</td>
</tr>
<tr>
<td>Q. RR 알고리즘의 핵심 변수는?</td>
<td>Time Quantum (작으면 context switching 많아짐)</td>
</tr>
<tr>
<td>Q. Priority Scheduling의 문제점은?</td>
<td>기아(starvation) 현상 — Aging으로 해결 가능</td>
</tr>
<tr>
<td>Q. 선점형과 비선점형의 차이?</td>
<td>OS가 CPU를 강제로 빼앗을 수 있느냐 여부</td>
</tr>
</tbody></table>
<hr />