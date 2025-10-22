<h2 id="🧠-1️⃣-io-시스템의-역할">🧠 1️⃣ I/O 시스템의 역할</h2>
<blockquote>
<p><strong>CPU와 주변 장치(Device) 사이의 효율적인 데이터 전송과 제어</strong></p>
</blockquote>
<ul>
<li><p>CPU는 속도가 빠른 반면, 디스크, 네트워크, 키보드 등 장치는 느림</p>
</li>
<li><p>OS는 CPU와 장치 간 <strong>속도 차이를 조정하고 효율적인 데이터 처리를 담당</strong></p>
</li>
<li><p>주요 목표:</p>
<ol>
<li><strong>CPU 효율 증가</strong></li>
<li><strong>장치 공유</strong></li>
<li><strong>데이터 안정성 보장</strong></li>
</ol>
</li>
</ul>
<hr />
<h2 id="⚙️-2️⃣-io-방식">⚙️ 2️⃣ I/O 방식</h2>
<h3 id="1-프로그램-직접-입출력programmed-io">(1) 프로그램 직접 입출력(Programmed I/O)</h3>
<ul>
<li>CPU가 <strong>직접 I/O 장치를 제어</strong></li>
<li>장점: 구조 단순</li>
<li>단점: CPU가 기다려야 함 → 비효율</li>
</ul>
<h3 id="2-인터럽트-기반-iointerrupt-driven-io">(2) 인터럽트 기반 I/O(Interrupt-Driven I/O)</h3>
<ul>
<li>I/O 완료 시 <strong>장치가 CPU에 인터럽트 발생</strong></li>
<li>CPU는 다른 작업을 수행하다가, 인터럽트 처리 후 데이터 전송</li>
<li>장점: CPU 활용 효율 ↑</li>
<li>단점: 인터럽트 처리 오버헤드 존재</li>
</ul>
<h3 id="3-dmadirect-memory-access">(3) DMA(Direct Memory Access)</h3>
<ul>
<li><strong>CPU 개입 없이 장치가 메모리 직접 접근</strong></li>
<li>CPU는 다른 작업 수행 가능</li>
<li>장점: CPU 부담 최소화, 대량 데이터 전송 효율적</li>
<li>단점: DMA 컨트롤러 필요, 구현 복잡</li>
</ul>
<hr />
<h2 id="🧩-3️⃣-io-버퍼링buffering">🧩 3️⃣ I/O 버퍼링(Buffering)</h2>
<blockquote>
<p><strong>속도 차이 보정 및 효율적 데이터 전송</strong></p>
</blockquote>
<ul>
<li><strong>단일 버퍼(Single Buffer)</strong>: CPU가 데이터를 보내고, 장치가 읽음</li>
<li><strong>이중 버퍼(Double Buffer)</strong>: 한쪽 버퍼는 CPU, 다른쪽 버퍼는 장치 사용 → 병행 처리</li>
<li><strong>순환 버퍼(Circular Buffer)</strong>: 생산자-소비자 문제 해결 가능</li>
</ul>
<hr />
<h2 id="🔄-4️⃣-캐싱caching">🔄 4️⃣ 캐싱(Caching)</h2>
<ul>
<li><strong>자주 쓰는 데이터를 빠른 메모리(RAM)에 저장</strong></li>
<li>장점: 디스크 I/O 최소화 → 성능 향상</li>
<li>단점: 캐시 일관성 문제(Coherency) 존재 → Dirty Bit, Write-back/Write-through 전략 필요</li>
</ul>
<hr />
<h2 id="⚡️-5️⃣-디바이스-스케줄링">⚡️ 5️⃣ 디바이스 스케줄링</h2>
<ul>
<li><p>여러 프로세스가 동시에 I/O 요청 시 <strong>OS가 순서 결정</strong></p>
</li>
<li><p>대표 스케줄링 알고리즘:</p>
<ol>
<li><strong>FCFS (First Come First Serve)</strong>: 단순하지만 디스크 헤드 이동 많음</li>
<li><strong>SSTF (Shortest Seek Time First)</strong>: 헤드 이동 최소화, 기아 가능</li>
<li><strong>SCAN / C-SCAN</strong>: 엘리베이터 알고리즘, 공평하게 처리</li>
</ol>
</li>
<li><p>면접 포인트: 디스크 효율 최적화, 헤드 이동 최소화 전략</p>
</li>
</ul>
<hr />
<h2 id="🧠-6️⃣-인터럽트interrupt">🧠 6️⃣ 인터럽트(Interrupt)</h2>
<ul>
<li><p>CPU가 작업 도중 장치 신호를 받고 <strong>즉시 처리</strong></p>
</li>
<li><p>종류:</p>
<ul>
<li><strong>마스크 가능 인터럽트(Maskable Interrupt)</strong>: 무시 가능</li>
<li><strong>마스크 불가능 인터럽트(Non-maskable Interrupt)</strong>: 긴급 처리</li>
</ul>
</li>
<li><p>장점: CPU 대기 시간 감소, 효율 ↑</p>
</li>
</ul>
<hr />
<h2 id="💬-7️⃣-면접-질문-정리">💬 7️⃣ 면접 질문 정리</h2>
<table>
<thead>
<tr>
<th>질문</th>
<th>답변 요약</th>
</tr>
</thead>
<tbody><tr>
<td>Q. 프로그램 I/O와 인터럽트 I/O 차이?</td>
<td>CPU가 기다리는지 여부</td>
</tr>
<tr>
<td>Q. DMA란?</td>
<td>CPU 개입 없이 장치가 메모리에 직접 접근</td>
</tr>
<tr>
<td>Q. 버퍼링과 캐싱 차이?</td>
<td>버퍼: 속도차 보정, 캐시: 속도 향상 위해 자주 쓰는 데이터 저장</td>
</tr>
<tr>
<td>Q. 디바이스 스케줄링 알고리즘 3가지?</td>
<td>FCFS, SSTF, SCAN/C-SCAN</td>
</tr>
<tr>
<td>Q. Dirty Bit란?</td>
<td>캐시 내용이 메모리와 불일치 여부 표시</td>
</tr>
</tbody></table>
<hr />
<h2 id="💡-핵심-한-줄-요약">💡 핵심 한 줄 요약</h2>
<blockquote>
<p><strong>I/O 관리의 목표는 CPU와 장치 간 속도 차이를 조정하고, 버퍼링·캐싱·DMA·스케줄링으로 효율적 데이터 전송을 보장하는 것이다.</strong></p>
</blockquote>
<hr />