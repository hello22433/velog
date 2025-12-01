<p><strong>Part 3. 데이터베이스</strong>의 핵심, <strong>트랜잭션(Transaction)</strong> 편입니다.</p>
<p>금융, 커머스, 예약 시스템 등 '돈'이나 '중요한 데이터'를 다루는 백엔드 개발자라면 이 개념은 선택이 아니라 <strong>필수 생존 지식</strong>입니다. 면접관이 &quot;ACID가 뭔가요?&quot;라고 물었을 때, 사전적 정의만 읊지 않고 <strong>계좌 이체 예시</strong>를 들어 설명하면 합격 확률이 비약적으로 올라갑니다.</p>
<hr />
<h2 id="db-내-돈이-증발하지-않게-지켜주는-4가지-약속-트랜잭션과-acid">[DB] 내 돈이 증발하지 않게 지켜주는 4가지 약속 (트랜잭션과 ACID)</h2>
<h3 id="분명-내-통장에서는-돈이-나갔는데">&quot;분명 내 통장에서는 돈이 나갔는데...&quot;</h3>
<p>친구에게 10만 원을 송금했습니다.
내 통장에서는 -10만 원이 찍혔는데, 하필 그 순간 은행 서버가 정전으로 꺼졌습니다.
친구 통장에는 +0원. 내 10만 원은 디지털 세상에서 증발해 버렸습니다.</p>
<p>상상만 해도 끔찍한 이 상황, 데이터베이스(DB)는 어떻게 막을까요?
바로 **트랜잭션(Transaction)**이라는 강력한 보호막 덕분입니다.</p>
<p>오늘은 백엔드 개발자가 반드시 알아야 할 **&quot;모 아니면 도(All or Nothing)&quot;**의 법칙에 대해 알아봅니다.</p>
<hr />
<h3 id="1-트랜잭션transaction이란">1. 트랜잭션(Transaction)이란?</h3>
<p>트랜잭션은 **'쪼갤 수 없는 업무 처리의 최소 단위'**를 뜻합니다.
쉽게 말해 **&quot;이 작업들은 무조건 한 덩어리로 처리해야 해!&quot;**라고 묶어두는 것입니다.</p>
<h4 id="💸-대표적인-예시-계좌-이체">💸 대표적인 예시: 계좌 이체</h4>
<p>A가 B에게 100만 원을 이체하는 과정은 사실 두 단계입니다.</p>
<ol>
<li>A의 잔액에서 100만 원을 뺀다. (UPDATE)</li>
<li>B의 잔액에 100만 원을 더한다. (UPDATE)</li>
</ol>
<p>트랜잭션은 이 1번과 2번을 <strong>하나의 박스</strong>로 묶습니다.
만약 1번은 성공했는데 2번에서 에러가 났다?
<strong>DB는 1번까지도 없던 일로 취소(Rollback)시켜 버립니다.</strong></p>
<blockquote>
<p><strong>💡 핵심 용어</strong></p>
<ul>
<li><strong>Commit (커밋):</strong> 모든 작업이 성공해서 DB에 영구적으로 저장하는 것. &quot;저장 버튼&quot;</li>
<li><strong>Rollback (롤백):</strong> 작업 중 하나라도 실패해서 시작 전으로 되돌리는 것. &quot;실행 취소(Ctrl+Z)&quot;</li>
</ul>
</blockquote>
<hr />
<h3 id="2-db가-지키는-4가지-약속-acid">2. DB가 지키는 4가지 약속: ACID</h3>
<p>트랜잭션이 안전하게 수행된다는 것을 보장하기 위해 DB는 4가지 성질을 가집니다. 앞글자를 따서 <strong>ACID</strong>라고 부릅니다. 면접 필수 질문이니 꼭 기억하세요!</p>
<h4 id="①-atomicity-원자성">① Atomicity (원자성)</h4>
<ul>
<li><strong>&quot;All or Nothing (모 아니면 도)&quot;</strong></li>
<li>트랜잭션 내의 작업은 모두 성공하거나, 아예 모두 실패해야 합니다. &quot;반만 성공&quot;이라는 건 없습니다.</li>
</ul>
<h4 id="②-consistency-일관성">② Consistency (일관성)</h4>
<ul>
<li><strong>&quot;법칙은 지켜져야 한다&quot;</strong></li>
<li>트랜잭션이 끝난 후에도 DB의 제약 조건(무결성)은 유지되어야 합니다.</li>
<li>예: &quot;잔액은 마이너스가 될 수 없다&quot;는 제약이 있다면, 이체를 마친 후에도 잔액은 0원 이상이어야 합니다.</li>
</ul>
<h4 id="③-isolation-격리성">③ Isolation (격리성)</h4>
<ul>
<li><strong>&quot;끼어들기 금지&quot;</strong></li>
<li>A가 B에게 송금하는 동안, C가 끼어들어서 A의 잔액을 조회하거나 변경하면 안 됩니다.</li>
<li>트랜잭션끼리는 서로 간섭하지 않고 마치 혼자 실행되는 것처럼 격리되어야 합니다.</li>
</ul>
<h4 id="④-durability-지속성">④ Durability (지속성)</h4>
<ul>
<li><strong>&quot;기록은 영원하다&quot;</strong></li>
<li>일단 <code>Commit</code>이 완료된 트랜잭션은, 그 직후에 서버 플러그가 뽑혀도 데이터가 날아가면 안 됩니다. (하드디스크나 로그에 안전하게 기록됨)</li>
</ul>
<p><img alt="" src="https://velog.velcdn.com/images/hello22433/post/e02e6157-0840-4a1f-87a9-3b8f56308c7f/image.png" /></p>
<hr />
<h3 id="3-실무-코드에서는-어떻게-쓸까-spring">3. 실무 코드에서는 어떻게 쓸까? (Spring)</h3>
<p>우리가 SQL로 매번 <code>BEGIN TRANSACTION</code>, <code>COMMIT</code>을 칠 필요는 없습니다.
스프링(Spring) 프레임워크를 쓰는 자바 개발자라면, 마법의 주문(어노테이션) 하나면 끝납니다.</p>
<pre><code class="language-java">@Service
public class AccountService {

    @Transactional  // &lt;- 이 어노테이션 하나면 끝!
    public void transferMoney(Long senderId, Long receiverId, Long amount) {

        // 1. 보내는 사람 잔액 차감
        sender.minusAmount(amount);

        // (만약 여기서 에러가 발생하면? -&gt; 알아서 1번까지 롤백됨)
        if (errorCondition) throw new RuntimeException(&quot;이체 실패!&quot;);

        // 2. 받는 사람 잔액 증가
        receiver.plusAmount(amount);
    }
}</code></pre>
<p>저 <code>@Transactional</code>을 붙이면, 메서드 내의 코드가 실행되다가 <strong>예외(Exception)가 터지는 순간, 스프링이 알아서 모든 DB 작업을 롤백</strong>시켜 줍니다.</p>
<hr />
<h3 id="4-실전-면접-대비-3초-요약">4. 실전 면접 대비: 3초 요약</h3>
<p><strong>Q. 트랜잭션(Transaction)이 무엇인가요?</strong></p>
<blockquote>
<p><strong>A.</strong>
&quot;데이터베이스의 상태를 변화시키는 하나의 논리적인 작업 단위입니다. 작업 중 하나라도 실패하면 전체를 취소하는 **원자성(Atomicity)**을 보장하여 데이터의 무결성을 지키는 것이 목적입니다.&quot;</p>
</blockquote>
<p><strong>Q. ACID 중 '원자성'과 '격리성'을 설명해주세요.</strong></p>
<blockquote>
<p><strong>A.</strong>
&quot;<strong>원자성</strong>은 트랜잭션 내의 연산들이 모두 성공하거나 모두 실패해야 한다는 'All or Nothing' 원칙입니다.
<strong>격리성</strong>은 동시에 실행되는 트랜잭션들이 서로의 연산에 영향을 주지 않도록 독립적으로 수행되어야 함을 의미합니다.&quot;</p>
</blockquote>
<hr />
<h3 id="🚀-다음-글-예고">🚀 다음 글 예고</h3>
<p>&quot;테이블을 쪼개는 게 좋을까요, 합치는 게 좋을까요?&quot;</p>
<p>데이터베이스 설계의 영원한 난제.
깔끔하게 정리해서 중복을 없애자니 조인(Join)이 너무 많아 느려지고,
합쳐서 속도를 높이자니 데이터 관리가 엉망이 됩니다.</p>
<p>다음 시간에는 DB 설계의 줄다리기, <strong>정규화(Normalization)와 반정규화</strong>에 대해 알아보겠습니다.</p>
<blockquote>
<p><strong>Next Topic:</strong> [DB] 깔끔함 vs 속도, 무엇을 선택할까? (정규화와 반정규화)</p>
</blockquote>