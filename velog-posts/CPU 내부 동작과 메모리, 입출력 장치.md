<h2 id="1️⃣-cpu-내부-구조">1️⃣ CPU 내부 구조</h2>
<p>CPU는 크게 <strong>연산장치(ALU)</strong>, <strong>제어장치(CU)</strong>, <strong>레지스터</strong>로 나뉘어 있어.</p>
<h3 id="1-alu-arithmetic-logic-unit">1. ALU (Arithmetic Logic Unit)</h3>
<ul>
<li>역할: <strong>산술 연산 + 논리 연산</strong></li>
<li>예시: 덧셈, 뺄셈, 곱셈, 나눗셈, AND/OR 비교</li>
<li>실생활 예시: 계산기에서 실제 계산을 하는 부분</li>
</ul>
<h3 id="2-cu-control-unit">2. CU (Control Unit)</h3>
<ul>
<li>역할: <strong>명령어 해석과 실행 순서 관리</strong></li>
<li>CPU에게 “어떤 연산을 언제 할지” 알려줌</li>
<li>예시: 요리사가 레시피 보고 단계별로 재료를 처리하는 것과 같음</li>
</ul>
<h3 id="3-레지스터register">3. 레지스터(Register)</h3>
<ul>
<li>역할: <strong>CPU 안의 초고속 임시 저장소</strong></li>
<li>주로 연산 중간값, 주소, 명령어 저장</li>
<li>예시: 요리사 손에 들고 있는 재료, 바로 사용 가능</li>
</ul>
<p><strong>그림 느낌</strong></p>
<pre><code>         [CU] → 명령 해석
           ↓
[레지스터] ↔ [ALU] → 계산 수행
           ↓
         [메모리] ↔ CPU</code></pre><hr />
<h2 id="2️⃣-메모리-구조">2️⃣ 메모리 구조</h2>
<p>컴퓨터 메모리는 <strong>계층 구조</strong>를 가지고 있어.
속도와 용량, 비용이 다르기 때문에 <strong>적절한 위치에 데이터를 저장</strong>하는 게 핵심.</p>
<table>
<thead>
<tr>
<th>종류</th>
<th>속도</th>
<th>용량</th>
<th>용도</th>
<th>예시</th>
</tr>
</thead>
<tbody><tr>
<td>레지스터</td>
<td>초고속</td>
<td>몇 개</td>
<td>CPU 내부 임시값</td>
<td>연산 중간값</td>
</tr>
<tr>
<td>캐시</td>
<td>매우 빠름</td>
<td>KB~MB</td>
<td>CPU 근처, 자주 쓰는 데이터</td>
<td>자주 호출되는 함수</td>
</tr>
<tr>
<td>RAM</td>
<td>빠름</td>
<td>GB 단위</td>
<td>프로그램, 데이터 임시 저장</td>
<td>실행 중인 앱</td>
</tr>
<tr>
<td>하드디스크/SSD</td>
<td>느림</td>
<td>TB 단위</td>
<td>영구 저장</td>
<td>영화, 사진, DB</td>
</tr>
</tbody></table>
<p><strong>예시 비유</strong></p>
<ul>
<li>레지스터 → 손에 들고 있는 재료</li>
<li>캐시 → 작업대 위에 올려놓은 재료</li>
<li>RAM → 부엌 찬장</li>
<li>하드디스크 → 창고</li>
</ul>
<hr />
<h2 id="3️⃣-입출력-장치">3️⃣ 입출력 장치</h2>
<ul>
<li><p><strong>입력 장치</strong>: CPU로 데이터 전달</p>
<ul>
<li>키보드, 마우스, 스캐너</li>
<li>예시: 주문서 작성 → 요리사(CPU)에게 전달</li>
</ul>
</li>
<li><p><strong>출력 장치</strong>: CPU 처리 결과 전달</p>
<ul>
<li>모니터, 프린터, 스피커</li>
<li>예시: 완성된 요리 → 손님에게 전달</li>
</ul>
</li>
<li><p><strong>입출력 방식</strong></p>
<ol>
<li><strong>직접 I/O</strong> → CPU가 직접 제어</li>
<li><strong>메모리 맵 I/O</strong> → 메모리 주소처럼 입출력 장치를 제어</li>
</ol>
</li>
</ul>
<p><strong>그림 느낌</strong></p>
<pre><code>[입력] 키보드 → [CPU] → [출력] 모니터</code></pre><hr />
<h2 id="4️⃣-cpu-동작-예시-실제-느낌">4️⃣ CPU 동작 예시 (실제 느낌)</h2>
<ul>
<li>명령: <code>a = 2 + 3</code></li>
</ul>
<pre><code>1. CU가 명령어 읽음 → &quot;덧셈 수행&quot;
2. 레지스터에 a=2, b=3 저장
3. ALU가 2+3 계산 → 5
4. 결과 레지스터에 임시 저장
5. RAM에 a=5 저장
6. 화면에 5 출력</code></pre><ul>
<li>한 줄 명령도 실제로는 <strong>여러 단계</strong>가 거쳐서 처리됨 → 정처기에서 이런 흐름 문제 자주 나옴</li>
</ul>
<hr />
<h3 id="🔹-학습-팁">🔹 학습 팁</h3>
<ol>
<li>CPU 구조 → ALU, CU, 레지스터 역할 구분</li>
<li>메모리 구조 → 속도와 용량, 용도 이해</li>
<li>입출력 장치 → CPU와 데이터 흐름 이해</li>
<li>필기시험 대비 → “어떤 장치가 무엇 담당?” 문제 위주로 학습</li>
</ol>
<hr />