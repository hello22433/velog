<h2 id="1-애플리케이션-락의-정확한-정의부터-다시-보자">1. 애플리케이션 락의 정확한 정의부터 다시 보자</h2>
<p><strong>애플리케이션 락</strong>이란</p>
<blockquote>
<p>“애플리케이션 코드가 명시적으로 ‘들어오지 마’라고 막는 장치”</p>
</blockquote>
<p>입니다.</p>
<p>핵심 조건은 딱 3가지입니다.</p>
<ol>
<li><strong>코드가 락을 직접 획득/해제한다</strong></li>
<li><strong>락의 범위가 비즈니스 로직 단위다</strong></li>
<li><strong>트랜잭션과 무관하게 동작할 수 있다</strong></li>
</ol>
<p>예시</p>
<ul>
<li><code>synchronized</code></li>
<li><code>ReentrantLock</code></li>
<li>Redis 분산 락</li>
</ul>
<hr />
<h2 id="2-transactional의-본질">2. @Transactional의 본질</h2>
<h3 id="transactional이-실제로-하는-일">@Transactional이 실제로 하는 일</h3>
<p><code>@Transactional</code>은 락을 거는 기능이 아닙니다.
정확히는 다음을 보장합니다.</p>
<ol>
<li>하나의 메서드를 <strong>하나의 DB 트랜잭션으로 묶는다</strong></li>
<li>커밋 / 롤백을 자동 관리한다</li>
<li>격리 수준에 따라 <strong>DB가 락을 걸 수도 있다</strong></li>
</ol>
<p>여기서 중요한 문장:</p>
<blockquote>
<p><strong>락을 거는 주체는 DB이지, 애플리케이션이 아니다</strong></p>
</blockquote>
<hr />
<h2 id="3-결정적-차이-누가-막느냐">3. 결정적 차이: “누가 막느냐”</h2>
<h3 id="애플리케이션-락">애플리케이션 락</h3>
<pre><code class="language-text">애플리케이션:
&quot;지금 이 자원은 내가 쓰고 있으니
다른 스레드/서버는 들어오지 마&quot;</code></pre>
<h3 id="transactional">@Transactional</h3>
<pre><code class="language-text">애플리케이션:
&quot;이 작업을 트랜잭션으로 실행해줘&quot;

DB:
&quot;알겠어, 필요하면 내가 알아서 락 걸게&quot;</code></pre>
<p>즉,</p>
<ul>
<li>애플리케이션 락 → <strong>명시적 제어</strong></li>
<li>트랜잭션 → <strong>위임</strong></li>
</ul>
<hr />
<h2 id="4-초등학생-비유">4. 초등학생 비유</h2>
<h3 id="애플리케이션-락-1">애플리케이션 락</h3>
<p>문 앞에 <strong>자물쇠</strong>를 직접 채운다.</p>
<ul>
<li>열쇠는 내가 들고 있음</li>
<li>내가 열기 전까지 아무도 못 들어옴</li>
</ul>
<h3 id="transactional-1">@Transactional</h3>
<p>관리실에 말한다.</p>
<ul>
<li>“이 방에서 작업할게요”</li>
<li>관리실(DB)이 상황 봐서 문을 잠글 수도 있음</li>
</ul>
<p>👉 <strong>잠글지 말지는 관리실 마음</strong></p>
<hr />
<h2 id="5-실제-코드-비교">5. 실제 코드 비교</h2>
<h3 id="애플리케이션-락-2">애플리케이션 락</h3>
<pre><code class="language-java">lock.lock();
try {
    doBusinessLogic();
} finally {
    lock.unlock();
}</code></pre>
<ul>
<li>락 획득/해제 위치가 코드에 명확</li>
<li>범위가 비즈니스 로직</li>
</ul>
<hr />
<h3 id="transactional-2">@Transactional</h3>
<pre><code class="language-java">@Transactional
public void pay(Long userId) {
    updateBalance(userId);
}</code></pre>
<ul>
<li>락 코드 없음</li>
<li>“트랜잭션 경계”만 선언</li>
<li>실제 락은 DB 내부에서 자동 발생</li>
</ul>
<hr />
<h2 id="6-가장-중요한-반례-이걸-보면-끝">6. 가장 중요한 반례 (이걸 보면 끝)</h2>
<h3 id="transactional만-쓰면-동시성-제어-되지-않나요">“@Transactional만 쓰면 동시성 제어 되지 않나요?”</h3>
<p>❌ 아니다.</p>
<h4 id="상황">상황</h4>
<ul>
<li>서버 2대</li>
<li>같은 사용자 잔액 차감 API 동시 호출</li>
</ul>
<pre><code class="language-java">@Transactional
public void withdraw(Long userId, int amount) {
    User u = userRepository.findById(userId);
    u.withdraw(amount);
}</code></pre>
<h3 id="결과">결과</h3>
<ul>
<li>두 서버 모두 트랜잭션 시작</li>
<li>둘 다 같은 잔액을 읽음</li>
<li>둘 다 차감 성공</li>
<li><strong>잔액 마이너스</strong></li>
</ul>
<blockquote>
<p>트랜잭션은 <strong>원자성</strong>을 보장하지
<strong>상호 배제</strong>를 보장하지 않는다</p>
</blockquote>
<hr />
<h2 id="7-그럼-db-락-아닌가요">7. “그럼 DB 락 아닌가요?”</h2>
<p>맞습니다.</p>
<p><code>@Transactional</code>은 <strong>DB 락을 유발할 수는 있지만</strong>
그 자체가 락은 아닙니다.</p>
<table>
<thead>
<tr>
<th>구분</th>
<th>@Transactional</th>
</tr>
</thead>
<tbody><tr>
<td>락의 주체</td>
<td>DB</td>
</tr>
<tr>
<td>락의 위치</td>
<td>DB 엔진</td>
</tr>
<tr>
<td>락 제어</td>
<td>불가</td>
</tr>
<tr>
<td>범위</td>
<td>SQL / Row</td>
</tr>
<tr>
<td>애플리케이션 락?</td>
<td>❌</td>
</tr>
</tbody></table>
<hr />
<h2 id="8-언제-transactional이-락처럼-보일까">8. 언제 @Transactional이 락처럼 보일까?</h2>
<p>다음 상황에서는 <strong>착각</strong>하기 쉽습니다.</p>
<pre><code class="language-sql">SELECT ... FOR UPDATE</code></pre>
<pre><code class="language-java">@Transactional
public void update() {
    repository.findForUpdate();
}</code></pre>
<p>이때의 락은?</p>
<ul>
<li><code>@Transactional</code> ❌</li>
<li><code>FOR UPDATE</code> + DB ❌</li>
<li>애플리케이션 락 ❌</li>
</ul>
<p>👉 <strong>DB 락</strong>입니다.</p>
<hr />
<h2 id="9-실무-결론-아주-중요">9. 실무 결론 (아주 중요)</h2>
<table>
<thead>
<tr>
<th>목적</th>
<th>써야 할 것</th>
</tr>
</thead>
<tbody><tr>
<td>데이터 일관성</td>
<td><code>@Transactional</code></td>
</tr>
<tr>
<td>스레드 충돌 방지</td>
<td>싱크 락</td>
</tr>
<tr>
<td>서버 간 충돌 방지</td>
<td>분산 락</td>
</tr>
<tr>
<td>금액/수량 보호</td>
<td>락 + 트랜잭션</td>
</tr>
</tbody></table>
<blockquote>
<p><strong>트랜잭션은 보호막이 아니라 계약서다.</strong>
<strong>싸움은 막지 않는다.</strong></p>
</blockquote>
<hr />
<h2 id="10-한-문장-요약">10. 한 문장 요약</h2>
<blockquote>
<p><strong><code>@Transactional</code>은 락이 아니라 “트랜잭션 경계 선언자”이며,
락을 거는 주체가 DB이기 때문에 애플리케이션 락이 아니다.</strong></p>
</blockquote>
<hr />
<h2 id="2-1-트랜잭션이-보장하는-것과-보장하지-않는-것">2-1. 트랜잭션이 “보장하는 것”과 “보장하지 않는 것”</h2>
<h3 id="트랜잭션이-보장하는-것-acid">트랜잭션이 보장하는 것 (ACID)</h3>
<ul>
<li>Atomicity: 중간 실패 시 롤백</li>
<li>Consistency: 제약 조건 유지</li>
<li>Isolation: <strong>논리적 격리</strong></li>
<li>Durability: 커밋 후 영속성</li>
</ul>
<h3 id="트랜잭션이-보장하지-않는-것-치명적">트랜잭션이 보장하지 않는 것 (치명적)</h3>
<ul>
<li><strong>요청 간 상호 배제</strong></li>
<li><strong>비즈니스 규칙 보호</strong></li>
<li><strong>중복 실행 방지</strong></li>
<li><strong>순서 보장</strong></li>
</ul>
<p>👉 핀테크 사고는 전부 여기서 터집니다.</p>
<hr />
<h2 id="2-핀테크에서-동시성은-예외가-아니라-일상">2. 핀테크에서 동시성은 “예외”가 아니라 “일상”</h2>
<p>핀테크 트래픽 특징</p>
<ul>
<li>동일 계좌로 <strong>동시 요청</strong></li>
<li>모바일 재시도 (네트워크 흔들림)</li>
<li>사용자 + 배치 + 정산 작업이 겹침</li>
<li>멀티 서버 + 오토스케일링</li>
</ul>
<p>즉,</p>
<blockquote>
<p>“같은 데이터를 동시에 건드리는 상황”이 기본값</p>
</blockquote>
<hr />
<h2 id="3-대표적인-사고-시나리오-실제에서-가장-흔함">3. 대표적인 사고 시나리오 (실제에서 가장 흔함)</h2>
<h3 id="상황-계좌-잔액-10000원">상황: 계좌 잔액 10,000원</h3>
<p>두 요청이 동시에 들어옴.</p>
<pre><code class="language-java">@Transactional
public void withdraw(Long accountId, int amount) {
    Account a = find(accountId);   // 잔액: 10,000
    if (a.balance &lt; amount) throw;
    a.balance -= amount;
}</code></pre>
<h3 id="실행-타임라인">실행 타임라인</h3>
<pre><code>T1: 트랜잭션 A 시작 → 잔액 10,000 조회
T2: 트랜잭션 B 시작 → 잔액 10,000 조회
T3: A → 7,000으로 업데이트
T4: B → 7,000으로 업데이트</code></pre><h3 id="결과-1">결과</h3>
<ul>
<li>총 6,000원이 빠져야 하는데</li>
<li><strong>3,000원만 빠짐</strong></li>
<li>장부와 실제 금액 불일치</li>
</ul>
<p>👉 <strong>트랜잭션은 아무것도 막지 않았다</strong></p>
<hr />
<h2 id="4-격리-수준-높이면-되지-않나요의-함정">4. “격리 수준 높이면 되지 않나요?”의 함정</h2>
<h3 id="serializable-쓰면">SERIALIZABLE 쓰면?</h3>
<p>이론상 안전
실무에선 거의 불가능</p>
<p>이유:</p>
<ul>
<li>트랜잭션 대기 폭증</li>
<li>데드락 빈발</li>
<li>TPS 급락</li>
<li>실시간 결제 시스템 마비</li>
</ul>
<p>그래서 대부분 핀테크는</p>
<ul>
<li>READ COMMITTED</li>
<li>REPEATABLE READ</li>
</ul>
<p>을 씁니다.</p>
<p>👉 즉, <strong>충돌 가능성을 남긴 채 운영</strong></p>
<hr />
<h2 id="5-트랜잭션은-데이터-정합성-핀테크는-돈의-정합성">5. 트랜잭션은 “데이터 정합성”, 핀테크는 “돈의 정합성”</h2>
<p>이 차이가 핵심입니다.</p>
<h3 id="일반-서비스">일반 서비스</h3>
<ul>
<li>게시글 좋아요 수 틀려도 복구 가능</li>
</ul>
<h3 id="핀테크">핀테크</h3>
<ul>
<li><strong>1원이라도 틀리면 금융 사고</strong></li>
<li>법적 책임</li>
<li>금융감독원 보고</li>
</ul>
<blockquote>
<p>데이터 일관성과
<strong>자산 일관성은 완전히 다른 문제</strong></p>
</blockquote>
<hr />
<h2 id="6-실제-핀테크-사고의-공통-패턴">6. 실제 핀테크 사고의 공통 패턴</h2>
<p>거의 모든 장애 보고서에 이 문장이 있습니다.</p>
<blockquote>
<p>“트랜잭션 처리가 되어 있어 동시성 이슈를 고려하지 못했습니다.”</p>
</blockquote>
<p>즉,</p>
<ul>
<li>개발자는 안전하다고 생각</li>
<li>시스템은 전혀 안전하지 않음</li>
</ul>
<hr />
<h2 id="7-그래서-핀테크는-무엇을-추가하는가">7. 그래서 핀테크는 무엇을 추가하는가</h2>
<h3 id="필수-조합">필수 조합</h3>
<ol>
<li><strong>애플리케이션 락 (싱글 자원 선점)</strong></li>
<li><strong>DB 트랜잭션 (원자성)</strong></li>
<li><strong>멱등성 키 (중복 실행 차단)</strong></li>
<li><strong>이벤트 순서 보장</strong></li>
</ol>
<p>이 중 <strong>하나라도 없으면 사고 확률 급상승</strong></p>
<hr />
<h2 id="8-실제-패턴-개념">8. 실제 패턴 (개념)</h2>
<pre><code class="language-text">[요청]
   ↓
[분산 락 획득]  ← 핵심
   ↓
[트랜잭션 시작]
   ↓
[잔액 검증 + 변경]
   ↓
[커밋]
   ↓
[락 해제]</code></pre>
<p>트랜잭션은 <strong>중간 실패</strong>를 막고
락은 <strong>동시 접근</strong>을 막습니다.</p>
<hr />
<h2 id="9-db-락으로-충분하지-않나요가-틀린-이유">9. “DB 락으로 충분하지 않나요?”가 틀린 이유</h2>
<ul>
<li>DB 락은 <strong>쿼리 단위</strong></li>
<li>비즈니스 단위 보호 불가</li>
<li>외부 API, 캐시, 메시지 큐 보호 못함</li>
</ul>
<p>핀테크 로직은 보통</p>
<pre><code class="language-text">검증 → 외부 호출 → 계산 → 저장</code></pre>
<p>👉 DB 밖이 훨씬 김</p>
<hr />
<h2 id="10-한-문장으로-요약-이건-외워야-함">10. 한 문장으로 요약 (이건 외워야 함)</h2>
<blockquote>
<p><strong>핀테크에서 트랜잭션은 필수지만,
트랜잭션만 쓰면 반드시 사고가 난다.</strong></p>
</blockquote>
<hr />
<h2 id="3-1-실무의-대원칙-이거-3개는-고정">3-1. 실무의 대원칙 (이거 3개는 고정)</h2>
<h3 id="원칙-1">원칙 1</h3>
<p><strong>락은 최소 단위로, 짧게</strong></p>
<ul>
<li>사용자 ID / 계좌 ID / 주문 ID</li>
<li>절대 전역 락 금지</li>
</ul>
<hr />
<h3 id="원칙-2">원칙 2</h3>
<p><strong>락은 비즈니스 임계 구간에만</strong></p>
<ul>
<li>잔액 검증</li>
<li>수량 차감</li>
<li>상태 전이</li>
</ul>
<hr />
<h3 id="원칙-3">원칙 3</h3>
<p><strong>트랜잭션과 락은 역할이 다르다</strong></p>
<table>
<thead>
<tr>
<th>역할</th>
<th>담당</th>
</tr>
</thead>
<tbody><tr>
<td>동시 접근 차단</td>
<td>애플리케이션 락</td>
</tr>
<tr>
<td>실패 시 복구</td>
<td>DB 트랜잭션</td>
</tr>
</tbody></table>
<hr />
<h2 id="2-실무-표준-구조-99가-이-형태">2. 실무 표준 구조 (99%가 이 형태)</h2>
<pre><code>[요청 수신]
   ↓
[멱등성 체크]
   ↓
[자원 단위 락 획득]   ← 핵심
   ↓
[DB 트랜잭션 시작]
   ↓
[검증 + 상태 변경]
   ↓
[커밋]
   ↓
[락 해제]</code></pre><p>이 구조가 깨지는 순간 사고 확률이 폭증합니다.</p>
<hr />
<h2 id="3-왜-멱등성-→-락-순서인가">3. 왜 “멱등성 → 락” 순서인가</h2>
<h3 id="실무-이유">실무 이유</h3>
<ul>
<li>재시도 요청은 매우 흔함</li>
<li>락을 먼저 잡으면 <strong>불필요한 경합 증가</strong></li>
<li>멱등 키로 이미 처리된 요청은 즉시 종료</li>
</ul>
<hr />
<h2 id="4-락-단위는-이렇게-잡는다">4. 락 단위는 이렇게 잡는다</h2>
<h3 id="나쁜-예">나쁜 예</h3>
<ul>
<li>전역 락</li>
<li>테이블 락</li>
<li>서비스 락</li>
</ul>
<h3 id="좋은-예">좋은 예</h3>
<pre><code>lockKey = &quot;account:{accountId}&quot;
lockKey = &quot;order:{orderId}&quot;</code></pre><blockquote>
<p>“동시에 처리되면 안 되는 최소 단위”</p>
</blockquote>
<hr />
<h2 id="5-분산-락-실무-기준-redis-기준">5. 분산 락 실무 기준 (Redis 기준)</h2>
<h3 id="기본-옵션">기본 옵션</h3>
<ul>
<li>TTL 필수 (락 유실 방지)</li>
<li>짧은 만료 시간 (수백 ms ~ 수 초)</li>
<li>재시도는 짧게, 실패 허용</li>
</ul>
<pre><code class="language-text">SET lockKey value NX PX 3000</code></pre>
<hr />
<h2 id="6-트랜잭션은-반드시-락-안에서">6. 트랜잭션은 반드시 “락 안에서”</h2>
<p>❌ 잘못된 구조</p>
<pre><code>@Transactional
public void pay() {
    lock();
    ...
}</code></pre><ul>
<li>프록시 경계 꼬임</li>
<li>커밋 타이밍 불명확</li>
</ul>
<h3 id="올바른-구조">올바른 구조</h3>
<pre><code>lock
  → @Transactional 로직
unlock</code></pre><blockquote>
<p>락은 트랜잭션 “바깥”,
보호 대상은 “안쪽”</p>
</blockquote>
<hr />
<h2 id="7-실제-예시-개념-코드">7. 실제 예시 (개념 코드)</h2>
<pre><code class="language-java">public void pay(PayCommand cmd) {

    if (idempotent(cmd)) return;

    Lock lock = lockManager.lock(&quot;account:&quot; + cmd.accountId);

    try {
        payTx(cmd);
    } finally {
        lock.unlock();
    }
}

@Transactional
public void payTx(PayCommand cmd) {
    Account a = findForUpdate(cmd.accountId);
    validate(a, cmd);
    a.withdraw(cmd.amount);
    saveHistory(cmd);
}</code></pre>
<hr />
<h2 id="8-db-락은-보조-수단">8. DB 락은 “보조 수단”</h2>
<ul>
<li><code>SELECT FOR UPDATE</code></li>
<li>Optimistic Lock (version)</li>
</ul>
<blockquote>
<p>애플리케이션 락을 <strong>대체하지 않는다</strong>
실패 시 안전장치 역할</p>
</blockquote>
<hr />
<h2 id="9-성능은-어떻게-감당하나">9. 성능은 어떻게 감당하나?</h2>
<h3 id="핵심은-락을-피하는-설계">핵심은 “락을 피하는 설계”</h3>
<ul>
<li>사전 검증은 락 밖에서</li>
<li>이벤트 기반 비동기 처리</li>
<li>잔액 분리 (가용 / 보류)</li>
<li>CQRS</li>
</ul>
<p>락은 <strong>마지막 최후의 보루</strong></p>
<hr />
<h2 id="10-실무에서-자주-보는-실수-top-5">10. 실무에서 자주 보는 실수 TOP 5</h2>
<ol>
<li><code>@Transactional</code>만 믿음</li>
<li>락 TTL 없음</li>
<li>락 범위가 너무 큼</li>
<li>외부 API 호출을 락 안에서 수행</li>
<li>장애 시 락 해제 안 됨</li>
</ol>
<hr />
<h2 id="11-실무자-한-문장-요약">11. 실무자 한 문장 요약</h2>
<blockquote>
<p><strong>“락은 쓰되, 쓰고 있다는 사실을 항상 불안해해야 한다.”</strong></p>
</blockquote>
<p>그래서 락은</p>
<ul>
<li>적게</li>
<li>짧게</li>
<li>명확하게</li>
</ul>
<hr />
<h2 id="4-1-먼저-결론-한-줄">4-1. 먼저 결론 한 줄</h2>
<blockquote>
<p><strong>DB 락은 “데이터 무결성의 최후 방어선”이고
애플리케이션 락은 “비즈니스 규칙을 지키는 1차 방어선”이다.</strong></p>
</blockquote>
<p>그래서 관계는 <strong>대체 관계가 아니라 계층 관계</strong>입니다.</p>
<hr />
<h2 id="2-두-락의-보호-대상이-다르다">2. 두 락의 보호 대상이 다르다</h2>
<h3 id="애플리케이션-락-3">애플리케이션 락</h3>
<ul>
<li>보호 대상: <strong>비즈니스 행위</strong></li>
<li>범위: 여러 단계 (검증 → 계산 → 외부 호출 → 저장)</li>
<li>시간: 짧을 수도 길 수도 있음</li>
<li>위치: 애플리케이션 코드</li>
</ul>
<h3 id="db-락">DB 락</h3>
<ul>
<li>보호 대상: <strong>DB 레코드</strong></li>
<li>범위: SQL 실행 순간</li>
<li>시간: 트랜잭션 동안</li>
<li>위치: DB 엔진 내부</li>
</ul>
<p>👉 보호 대상이 아예 다릅니다.</p>
<hr />
<h2 id="3-select-for-update의-정확한-역할">3. SELECT FOR UPDATE의 정확한 역할</h2>
<h3 id="무엇을-보장하는가">무엇을 보장하는가</h3>
<ul>
<li>같은 Row를 <strong>동시에 수정하지 못하게 막음</strong></li>
<li>커밋 전까지 다른 트랜잭션 대기</li>
</ul>
<h3 id="무엇을-보장하지-않는가">무엇을 보장하지 않는가</h3>
<ul>
<li>트랜잭션 시작 전 로직</li>
<li>트랜잭션 밖 로직</li>
<li>외부 API 호출</li>
<li>중복 요청</li>
<li>요청 순서</li>
</ul>
<p>즉,</p>
<blockquote>
<p><strong>이미 DB에 도달한 이후만 보호</strong></p>
</blockquote>
<hr />
<h2 id="4-optimistic-lock의-정확한-역할">4. Optimistic Lock의 정확한 역할</h2>
<h3 id="작동-방식">작동 방식</h3>
<ul>
<li><code>version</code> 컬럼 비교</li>
<li>충돌 시 <strong>예외 발생</strong></li>
</ul>
<h3 id="본질">본질</h3>
<ul>
<li>락 ❌</li>
<li>충돌 감지기 ⭕️</li>
</ul>
<h3 id="보장하는-것">보장하는 것</h3>
<ul>
<li><strong>“겹쳤다”는 사실을 알려줌</strong></li>
</ul>
<h3 id="보장하지-않는-것">보장하지 않는 것</h3>
<ul>
<li>충돌 방지</li>
<li>순서 보장</li>
<li>비즈니스 원자성</li>
</ul>
<p>그래서 Optimistic Lock은 항상</p>
<blockquote>
<p><strong>“재시도 전략”이 전제</strong></p>
</blockquote>
<hr />
<h2 id="5-왜-db-락만으로-핀테크가-위험한가-결정적-이유">5. 왜 DB 락만으로 핀테크가 위험한가 (결정적 이유)</h2>
<h3 id="실제-비즈니스-로직-구조">실제 비즈니스 로직 구조</h3>
<pre><code>1. 요청 검증
2. 잔액 조회
3. 한도 체크
4. 외부 결제 승인
5. 상태 변경
6. 히스토리 저장</code></pre><p>DB 락은 보통</p>
<pre><code>5, 6번</code></pre><p>만 보호합니다.</p>
<p>👉 <strong>1~4번은 완전 무방비</strong></p>
<hr />
<h2 id="6-타임라인으로-보면-차이가-더-명확">6. 타임라인으로 보면 차이가 더 명확</h2>
<h3 id="애플리케이션-락-있음">애플리케이션 락 있음</h3>
<pre><code>[락 획득]
  검증
  외부 호출
  계산
  트랜잭션
    UPDATE
  COMMIT
[락 해제]</code></pre><h3 id="db-락만-있음">DB 락만 있음</h3>
<pre><code>검증
외부 호출
트랜잭션
  SELECT FOR UPDATE  ← 여기서 처음 막음
  UPDATE
COMMIT</code></pre><p>이미 늦었습니다.</p>
<hr />
<h2 id="7-그래서-보조-수단인-이유">7. 그래서 “보조 수단”인 이유</h2>
<p>DB 락은 다음 역할만 합니다.</p>
<ol>
<li>애플리케이션 락이 실패했을 때</li>
<li>예상 못 한 경로에서 접근했을 때</li>
<li>버그가 있을 때</li>
<li>운영 중 사람 실수</li>
</ol>
<blockquote>
<p><strong>“이중 안전벨트”</strong></p>
</blockquote>
<hr />
<h2 id="8-실무에서의-정확한-관계-정리">8. 실무에서의 정확한 관계 정리</h2>
<table>
<thead>
<tr>
<th>구분</th>
<th>애플리케이션 락</th>
<th>DB 락</th>
</tr>
</thead>
<tbody><tr>
<td>역할</td>
<td>1차 방어선</td>
<td>2차 방어선</td>
</tr>
<tr>
<td>보호 범위</td>
<td>비즈니스 전체</td>
<td>DB Row</td>
</tr>
<tr>
<td>중복 요청 차단</td>
<td>⭕️</td>
<td>❌</td>
</tr>
<tr>
<td>외부 호출 보호</td>
<td>⭕️</td>
<td>❌</td>
</tr>
<tr>
<td>서버 간 동기화</td>
<td>⭕️</td>
<td>❌</td>
</tr>
<tr>
<td>성능 영향</td>
<td>설계에 따라</td>
<td>큼</td>
</tr>
<tr>
<td>장애 시 영향</td>
<td>국소적</td>
<td>광범위</td>
</tr>
</tbody></table>
<hr />
<h2 id="9-실무에서-둘을-같이-쓰는-이유">9. 실무에서 둘을 같이 쓰는 이유</h2>
<blockquote>
<p>“애플리케이션 락을 믿지만,
절대 혼자 일하게 두지는 않는다.”</p>
</blockquote>
<p>그래서 보통:</p>
<ul>
<li>애플리케이션 락: <strong>주 전략</strong></li>
<li>DB 락: <strong>보험</strong></li>
</ul>
<hr />
<h2 id="10-한-문장으로-정리">10. 한 문장으로 정리</h2>
<blockquote>
<p><strong>애플리케이션 락은 ‘사고를 예방’하고
DB 락은 ‘사고를 감지·차단’한다.</strong></p>
</blockquote>