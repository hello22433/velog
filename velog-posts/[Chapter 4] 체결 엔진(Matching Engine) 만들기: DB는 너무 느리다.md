<p>&quot;왜 내 서버는 트래픽이 몰리면 터질까?&quot;를 고민하는 주니어 개발자들에게, <strong>&quot;DB를 버리고 메모리로 올라타라&quot;</strong>는 강력한 메시지를 던지는 챕터입니다. 면접관들이 눈을 반짝일 내용이니 힘을 줘서 작성해 보겠습니다.</p>
<hr />
<h1 id="chapter-4-체결-엔진matching-engine-만들기-db는-너무-느리다">[Chapter 4] 체결 엔진(Matching Engine) 만들기: DB는 너무 느리다</h1>
<blockquote>
<p><strong>&quot;초당 10만 건의 주문을 처리해야 한다면, 데이터베이스(DB)는 우리의 친구가 아니라 짐입니다.&quot;</strong></p>
</blockquote>
<h2 id="1-주니어-개발자의-흔한-착각-쿼리로-매칭하면-되지-않나요">1. 주니어 개발자의 흔한 착각: &quot;쿼리로 매칭하면 되지 않나요?&quot;</h2>
<p>처음 거래소 로직을 짜보라고 하면, 대부분의 개발자는 습관적으로 SQL부터 떠올립니다.</p>
<pre><code class="language-sql">-- 최악의 매칭 로직 예시
SELECT * FROM orders 
WHERE side = 'ASK' AND price &lt;= {내_매수_가격} 
ORDER BY price ASC, created_at ASC 
LIMIT 1 
FOR UPDATE; -- 락(Lock)까지 걸어버림</code></pre>
<p>이 방식은 치명적인 문제가 있습니다.</p>
<ol>
<li><strong>Disk I/O:</strong> DB는 기본적으로 디스크에 씁니다. 램(RAM)보다 수십만 배 느립니다.</li>
<li><strong>Lock Contention:</strong> <code>FOR UPDATE</code>로 행을 잠그는 순간, 다른 모든 거래는 대기 상태에 빠집니다. (병목 현상)</li>
</ol>
<p>거래소는 <strong>속도(Latency)</strong>가 생명입니다. 버튼을 눌렀는데 1초 뒤에 체결된다면 그 거래소는 망합니다. 우리는 <strong>마이크로초(µs)</strong> 단위로 싸워야 합니다.</p>
<h2 id="2-정답은-메모리in-memory다">2. 정답은 메모리(In-Memory)다</h2>
<p>해결책은 간단하면서도 과감합니다. <strong>&quot;모든 호가 데이터(OrderBook)를 힙 메모리(Heap Memory)에 올린다.&quot;</strong></p>
<p>DB는 오직 '백업'과 '로그' 용도로만 사용하고, 실제 전투(매칭)는 메모리 위에서 치릅니다. 이것이 LMAX 거래소나 토스 증권 같은 고성능 시스템들이 작동하는 방식입니다.</p>
<p>[Image of in-memory computing architecture]</p>
<h2 id="3-매칭-알고리즘-구현-the-core-logic">3. 매칭 알고리즘 구현 (The Core Logic)</h2>
<p>지난 1장에서 만든 <code>OrderBook</code>을 기억하시나요? 이제 새로운 주문이 들어왔을 때, 상대방 주문을 잡아먹는(Taking) 로직을 짭니다.</p>
<p>핵심은 <strong><code>while</code> 루프</strong>입니다. 내 주문량이 다 채워지거나, 상대방 물량이 없을 때까지 반복합니다.</p>
<pre><code class="language-java">public class MatchingEngine {

    // 1장에서 만든 호가창 관리 객체
    private final OrderBook orderBook;

    public void processOrder(Order incomingOrder) {
        if (incomingOrder.getSide() == OrderSide.BID) {
            match(incomingOrder, orderBook.getAsks(), orderBook.getBids());
        } else {
            match(incomingOrder, orderBook.getBids(), orderBook.getAsks());
        }
    }

    private void match(Order incoming, TreeMap&lt;BigDecimal, OrderBucket&gt; oppositeBook, TreeMap&lt;BigDecimal, OrderBucket&gt; myBook) {
        // 상대방 호가창을 순회 (Iterator)
        Iterator&lt;Map.Entry&lt;BigDecimal, OrderBucket&gt;&gt; iterator = oppositeBook.entrySet().iterator();

        while (incoming.getRemainingQty().compareTo(BigDecimal.ZERO) &gt; 0 &amp;&amp; iterator.hasNext()) {
            Map.Entry&lt;BigDecimal, OrderBucket&gt; entry = iterator.next();
            BigDecimal bestPrice = entry.getKey();
            OrderBucket bucket = entry.getValue();

            // 가격 조건 확인: (매수라면) 상대 가격이 내 가격보다 싸야 함
            if (!canMatch(incoming, bestPrice)) {
                break; 
            }

            // 버킷 안의 주문들과 매칭 (FIFO)
            matchWithBucket(incoming, bucket);

            // 버킷이 비었으면 호가창에서 삭제 (가격대 삭제)
            if (bucket.isEmpty()) {
                iterator.remove(); 
            }
        }

        // 매칭 후에도 잔량이 남았다면? 내 호가창(myBook)에 등록 (Maker가 됨)
        if (incoming.getRemainingQty().compareTo(BigDecimal.ZERO) &gt; 0) {
            orderBook.addOrder(incoming, myBook);
        }
    }
}</code></pre>
<h3 id="3-1-이-코드의-위력">3-1. 이 코드의 위력</h3>
<ul>
<li><strong>No DB Access:</strong> 모든 로직이 자바 객체 간의 연산으로 끝납니다.</li>
<li><strong>Zero Copy:</strong> 데이터를 복사하지 않고 참조(Reference)만 넘깁니다.</li>
<li><strong>속도:</strong> 단순히 <code>TreeMap</code>을 조회하고 <code>LinkedList</code>를 훑는 시간 복잡도만 가집니다.</li>
</ul>
<h2 id="4-백엔드-개발자의-고민-데이터가-날아가면-어떡하죠">4. 백엔드 개발자의 고민: 데이터가 날아가면 어떡하죠?</h2>
<p>&quot;메모리에서 다 한다고요? 그러다 서버 전원 꺼지면 다 날아가는 거 아닙니까?&quot;</p>
<p>맞습니다. 이것이 인메모리 시스템의 최대 약점입니다. 그래서 우리는 <strong>이중 안전장치</strong>를 둬야 합니다.</p>
<ol>
<li><strong>WAL (Write Ahead Log):</strong> 주문이 메모리에 들어오기 전에, 아주 빠르게 파일이나 DB에 &quot;나 이거 처리할 거야&quot;라고 로그부터 남깁니다.</li>
<li><strong>스냅샷 (Snapshot):</strong> 주기적으로 메모리 상태 전체를 디스크에 저장합니다.</li>
<li><strong>이벤트 소싱 (Event Sourcing):</strong> 서버가 재시작되면, 로그에 남아있는 주문들을 순서대로 다시 실행(Replay)하여 메모리 상태를 복구합니다.</li>
</ol>
<blockquote>
<p>(이 부분은 나중에 Kafka 챕터에서 더 자세히 다룰 예정이니 &quot;이런 게 있다&quot; 정도로만 언급하고 넘어갑니다.)</p>
</blockquote>
<h2 id="5-마치며-하지만-아직-불안하다">5. 마치며: 하지만 아직 불안하다</h2>
<p>이제 우리는 DB보다 수천 배 빠른 엔진을 얻었습니다. 하지만 여기서 안심하기엔 이릅니다.</p>
<p>만약 두 개의 스레드(Thread)가 동시에 <code>processOrder()</code>를 호출하면 어떻게 될까요?
동시에 같은 주문을 체결시키려다가 잔고가 마이너스가 되는 <strong>경쟁 상태(Race Condition)</strong>가 발생할 것입니다.</p>
<p>싱글 스레드로 가야 할까요? 멀티 스레드로 가되 락(Lock)을 걸어야 할까요?
다음 챕터 <strong>[동시성 이슈와의 전쟁]</strong>에서 자바가 제공하는 <code>synchronized</code>, <code>ReentrantLock</code>, 그리고 <code>Redis</code> 분산락까지 탈탈 털어보겠습니다.</p>
<hr />
<p><strong>Next Post:</strong> [Chapter 5. 동시성 이슈와의 전쟁 - 내 돈이 복사가 된다고?]</p>