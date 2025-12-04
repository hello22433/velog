<h1 id="동시성-이슈와의-전쟁-내-돈이-복사가-된다고">동시성 이슈와의 전쟁: 내 돈이 복사가 된다고?</h1>
<blockquote>
<p><strong>&quot;멀티 스레드(Multi-Thread) 환경에서 공유 자원(Shared Resource)을 아무런 보호 장치 없이 건드리는 것은, 눈을 감고 고속도로를 무단 횡단하는 것과 같습니다.&quot;</strong></p>
</blockquote>
<h2 id="1-악몽의-시나리오-따닥double-click">1. 악몽의 시나리오: 따닥(Double Click)</h2>
<p>여러분의 거래소에 <strong>100만 원</strong>을 가진 사용자가 있습니다. 이 사용자가 100만 원어치 비트코인 매수 버튼을 실수로(혹은 고의로) 아주 빠르게 두 번 <strong>'따닥'</strong> 눌렀습니다.</p>
<p>서버는 동시에 두 개의 요청을 받습니다.</p>
<ol>
<li><strong>Thread A:</strong> 잔액 확인 (100만 원 있음) → 통과</li>
<li><strong>Thread B:</strong> 잔액 확인 (100만 원 있음) → 통과 (???)</li>
<li><strong>Thread A:</strong> 매수 체결 → 잔액 0원 차감</li>
<li><strong>Thread B:</strong> 매수 체결 → 잔액 0원 차감</li>
</ol>
<p>결과적으로 사용자는 100만 원을 내고 200만 원어치 비트코인을 샀습니다. <strong>돈 복사 버그</strong>가 터진 것입니다. 회사는 망했습니다.</p>
<p>이것이 바로 <strong>경쟁 상태(Race Condition)</strong>입니다.</p>
<hr />
<h2 id="2-해결책-lv1-java의-synchronized">2. 해결책 Lv.1: Java의 <code>synchronized</code></h2>
<p>가장 원초적인 해결책은 자바가 제공하는 <code>synchronized</code> 키워드입니다. &quot;이 구간은 한 번에 한 놈만 들어와&quot;라고 문을 걸어 잠그는 것이죠.</p>
<pre><code class="language-java">public synchronized void decreaseBalance(Long userId, BigDecimal amount) {
    BigDecimal currentBalance = repository.getBalance(userId);

    if (currentBalance.compareTo(amount) &lt; 0) {
        throw new RuntimeException(&quot;잔액 부족&quot;);
    }

    repository.updateBalance(userId, currentBalance.subtract(amount));
}</code></pre>
<h3 id="❌-치명적인-한계">❌ 치명적인 한계</h3>
<p><code>synchronized</code>는 <strong>하나의 서버(인스턴스) 안에서만</strong> 유효합니다.
여러분의 거래소가 대박이 나서 서버를 3대로 늘리는 순간(Scale-out), 서버 1의 락은 서버 2의 요청을 막지 못합니다. 다시 돈 복사가 시작됩니다.</p>
<hr />
<h2 id="3-해결책-lv2-redis-분산-락-distributed-lock">3. 해결책 Lv.2: Redis 분산 락 (Distributed Lock)</h2>
<p>서버가 여러 대라면, 모든 서버가 공통으로 바라보는 <strong>'제3의 공간'</strong>에서 락을 관리해야 합니다. 업계 표준은 <strong>Redis</strong>입니다.</p>
<ul>
<li><strong>원리:</strong> 화장실(임계 영역)에 들어가기 전에, 카운터(Redis)에 가서 키(Key)를 받아옵니다. 키가 없으면 기다립니다.</li>
<li><strong>도구:</strong> Java에서는 <code>Redisson</code> 라이브러리를 주로 사용합니다. (Spin Lock 방식이 아닌 Pub/Sub 방식을 사용하여 레디스 부하를 줄여줍니다.)</li>
</ul>
<pre><code class="language-java">// Redisson을 활용한 분산 락 예시
public void decreaseBalanceSafe(Long userId, BigDecimal amount) {
    RLock lock = redissonClient.getLock(&quot;balance_lock:&quot; + userId);

    try {
        // 5초 동안 락 획득 시도, 획득 후 3초 뒤 자동 해제
        if (lock.tryLock(5, 3, TimeUnit.SECONDS)) {
            // 안전한 구역: 로직 실행
            decreaseBalance(userId, amount);
        } else {
            throw new RuntimeException(&quot;잠시 후 다시 시도해주세요.&quot;);
        }
    } catch (InterruptedException e) {
        // 예외 처리
    } finally {
        if (lock.isLocked() &amp;&amp; lock.isHeldByCurrentThread()) {
            lock.unlock(); // 작업 끝나면 반드시 락 반납
        }
    }
}</code></pre>
<h3 id="🤔-고민점-성능-저하">🤔 고민점: 성능 저하</h3>
<p>분산 락은 안전하지만, 매 요청마다 Redis와 통신해야 하므로 <strong>네트워크 지연(Network Latency)</strong>이 발생합니다.
초당 수십만 건을 처리해야 하는 매칭 엔진 내부에서 쓰기에는 너무 느릴 수 있습니다.</p>
<hr />
<h2 id="4-해결책-lv3-싱글-스레드-모델-single-thread-model">4. 해결책 Lv.3: 싱글 스레드 모델 (Single Thread Model)</h2>
<p>여기서 발상의 전환이 일어납니다.
<strong>&quot;락을 걸어서 느려진다면, 애초에 락이 필요 없는 구조를 만들면 되지 않을까?&quot;</strong></p>
<p>Node.js나 Redis가 빠른 이유가 무엇인가요?
바로 <strong>싱글 스레드(Event Loop)</strong>이기 때문입니다.</p>
<h3 id="엔진-아키텍처의-혁명">엔진 아키텍처의 혁명</h3>
<p>우리는 <strong>Chapter 4</strong>에서 만든 <code>OrderBook</code>을 관리하는 매칭 엔진을 <strong>단 하나의 스레드</strong>로만 동작시킵니다.</p>
<ol>
<li>모든 주문 요청을 <strong>큐(Queue)</strong>에 순서대로 쌓습니다.</li>
<li>단 하나의 <strong>Consumer Thread</strong>가 큐에서 하나씩 꺼내서 처리합니다.</li>
<li>동시에 접근하는 놈이 없으니, <code>synchronized</code>도 <code>Redis Lock</code>도 필요 없습니다.</li>
</ol>
<pre><code class="language-java">// 큐(Queue)에서 하나씩 꺼내서 처리하는 구조
public class SingleThreadMatchingEngine implements Runnable {
    private final BlockingQueue&lt;Order&gt; orderQueue = new LinkedBlockingQueue&lt;&gt;();
    private final OrderBook orderBook = new OrderBook();

    @Override
    public void run() {
        while (true) {
            try {
                // 여기서 줄 서서 하나씩 나옴 (순서 보장)
                Order order = orderQueue.take(); 
                orderBook.match(order);
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
        }
    }
}</code></pre>
<p>이것이 바로 세계 최고의 속도를 자랑하는 <strong>LMAX 거래소</strong>가 고안한 <strong>Disruptor</strong> 패턴의 핵심 철학입니다.
CPU 캐시 효율을 극대화하고 컨텍스트 스위칭 비용을 없앱니다.</p>
<hr />
<h2 id="5-마치며-정답은-없다-선택만-있을-뿐">5. 마치며: 정답은 없다, 선택만 있을 뿐</h2>
<ul>
<li><strong>일반적인 입출금/결제 서비스:</strong> 안정성이 중요하므로 <strong>Redis 분산 락</strong>을 추천합니다.</li>
<li><strong>초고속 체결 엔진:</strong> 속도가 생명이므로 <strong>싱글 스레드 큐(Single Thread Queue)</strong> 방식을 추천합니다.</li>
</ul>
<p>우리는 이제 안전하고 빠른 엔진을 만들었습니다.
그런데 이 엔진이 혼자 돌아서는 의미가 없습니다.
체결된 결과를 사용자에게, DB에게, 시세 서버에게 알려줘야 합니다.</p>
<p>다음 챕터에서는 <strong>[비동기 아키텍처]</strong>의 핵심인 Kafka를 통해, 엔진의 굉음을 세상에 알리는 법을 다뤄보겠습니다.</p>
<hr />
<p><strong>Next Post:</strong>
👉 <em>Chapter 6. Kafka와 이벤트 기반 아키텍처 - 체결 사실을 전파하라</em></p>