<p><strong>금융 도메인의 '가장 기초적이면서도 치명적인' 실수</strong>를 다루는 2장입니다.</p>
<hr />
<h1 id="chapter-2-돈은-실수float가-아니다-정밀도-문제">[Chapter 2] 돈은 실수(Float)가 아니다: 정밀도 문제</h1>
<blockquote>
<p><strong>&quot;0.1 + 0.2 = 0.3이 아닙니다. 적어도 컴퓨터의 세계에서는요.&quot;</strong></p>
</blockquote>
<h2 id="1-100억짜리-버그의-시작">1. 100억짜리 버그의 시작</h2>
<p>만약 여러분이 만든 거래소에서 사용자가 <strong>0.1 BTC</strong>를 매수하고, 곧바로 <strong>0.2 BTC</strong>를 추가 매수했다고 가정해 봅시다.
사용자의 지갑에는 총 <strong>0.3 BTC</strong>가 있어야 합니다.</p>
<p>하지만 여러분이 변수 타입을 <code>double</code>로 선언했다면, 데이터베이스에는 <strong>0.30000000000000004 BTC</strong>가 기록될 수도 있습니다.</p>
<p>&quot;에이, 그 정도 미세한 차이는 괜찮지 않나요?&quot;</p>
<p>아니요, 괜찮지 않습니다.</p>
<ol>
<li><strong>정합성 불일치:</strong> 장부상 입금 내역의 합과 현재 잔고가 0.0000000000000004만큼 차이가 납니다. 회계 감사를 통과할 수 없습니다.</li>
<li><strong>출금 불가:</strong> 사용자가 &quot;전액 출금(0.3 BTC)&quot;을 요청했는데, 시스템은 &quot;잔액 부족(실제로는 0.3...04인데 0.3만 있다고 판단하거나 그 반대)&quot; 에러를 뱉을 수 있습니다.</li>
</ol>
<h2 id="2-왜-컴퓨터는-계산을-못할까-ieee-754">2. 왜 컴퓨터는 계산을 못할까? (IEEE 754)</h2>
<p>우리가 쓰는 10진수(Decimal)와 컴퓨터가 쓰는 2진수(Binary) 사이에는 건널 수 없는 강이 있습니다.</p>
<p>컴퓨터는 숫자를 0과 1로 저장합니다.</p>
<ul>
<li>10진수 0.5는 2진수로 <code>0.1</code> (깔끔합니다)</li>
<li>10진수 0.1는 2진수로 <code>0.0001100110011...</code> (무한 소수입니다)</li>
</ul>
<p>메모리는 유한하기 때문에 컴퓨터는 이 무한 소수를 적당한 선에서 잘라내서(반올림) 저장합니다. 이것이 바로 <strong>부동소수점(Floating Point) 오차</strong>입니다.</p>
<pre><code class="language-java">public class MoneyTest {
    public static void main(String[] args) {
        double a = 1.0;
        double b = 0.9;

        // 예상: 0.1
        // 실제: 0.09999999999999998
        System.out.println(a - b); 
    }
}</code></pre>
<p>금융 시스템에서 <code>double</code>이나 <code>float</code>을 쓰는 것은, <strong>구멍 난 지갑</strong>을 쓰는 것과 같습니다.</p>
<h2 id="3-해결책-1-bigdecimal-the-standard">3. 해결책 1: BigDecimal (The Standard)</h2>
<p>자바 백엔드 개발자라면 돈과 관련된 모든 필드는 무조건 <code>BigDecimal</code>을 써야 합니다. <code>BigDecimal</code>은 숫자를 이진수 부동소수점이 아니라, <strong>문자열(String)처럼 취급하여 10진수 계산</strong>을 수행합니다.</p>
<h3 id="3-1-주의할-점-생성자constructor">3-1. 주의할 점: 생성자(Constructor)</h3>
<p>여기서도 함정이 있습니다. <code>BigDecimal</code>을 만들 때 <code>double</code>을 넘기면 안 됩니다.</p>
<pre><code class="language-java">// 나쁜 예 (여전히 오차 발생)
BigDecimal bad = new BigDecimal(0.1); 
// 출력: 0.1000000000000000055511151231257827021181583404541015625

// 좋은 예 (문자열로 넘겨야 함)
BigDecimal good = new BigDecimal(&quot;0.1&quot;);
// 출력: 0.1</code></pre>
<p><strong>반드시 문자열(String) 형태로 생성해야 우리가 의도한 정확한 값이 들어갑니다.</strong></p>
<h3 id="3-2-나눗셈과-반올림-rounding">3-2. 나눗셈과 반올림 (Rounding)</h3>
<p>암호화폐는 소수점 단위가 매우 깁니다. (비트코인은 소수점 8자리까지 사용). 따라서 나눗셈을 할 때 **'어디서 자를 것인가(Scale)'**와 **'어떻게 처리할 것인가(RoundingMode)'**를 명시하지 않으면 에러가 납니다.</p>
<pre><code class="language-java">BigDecimal a = new BigDecimal(&quot;10&quot;);
BigDecimal b = new BigDecimal(&quot;3&quot;);

// 에러 발생! (무한 소수 ArithmeticException)
// a.divide(b); 

// 올바른 예: 소수점 8자리까지 표시, 9자리에서 반올림(HALF_UP)
BigDecimal result = a.divide(b, 8, RoundingMode.HALF_UP);
// 결과: 3.33333333</code></pre>
<h2 id="4-해결책-2-정수-연산-the-performance">4. 해결책 2: 정수 연산 (The Performance)</h2>
<p>사실 <code>BigDecimal</code>은 무겁습니다. 객체를 계속 생성해야 하고, 연산 속도가 <code>double</code>보다 느립니다. 초당 수십만 건을 처리하는 고성능 트레이딩 엔진(HFT)에서는 <strong>모든 단위를 정수(long)로 변환</strong>해서 처리하기도 합니다.</p>
<ul>
<li><strong>비트코인(BTC):</strong> 최소 단위인 **사토시(Satoshi)**를 기준으로 저장합니다.<ul>
<li>1 BTC = 100,000,000 Satoshi</li>
<li>0.00000001 BTC = 1 Satoshi</li>
</ul>
</li>
</ul>
<p>이렇게 하면 소수점 계산을 할 필요 없이 정수 덧셈/뺄셈만 하면 되므로 속도가 빠르고 오차가 없습니다. 단, 프론트엔드에 보여줄 때 다시 1억으로 나눠줘야 하는 번거로움이 있습니다.</p>
<blockquote>
<p><strong>저의 선택:</strong> 일반적인 거래소 백엔드 서비스(입출금, 자산 조회 등)에서는 생산성과 안정성을 위해 **<code>BigDecimal</code>**을 사용하고, 핵심 체결 엔진(Matching Engine) 내부에서만 성능을 위해 **<code>long</code>**을 사용하는 하이브리드 방식을 추천합니다.</p>
</blockquote>
<h2 id="5-데이터베이스-설계-mysql">5. 데이터베이스 설계 (MySQL)</h2>
<p>DB에 저장할 때도 <code>FLOAT</code>이나 <code>DOUBLE</code> 타입은 금지입니다.</p>
<ul>
<li><strong>MySQL:</strong> <code>DECIMAL(P, S)</code> 타입을 사용해야 합니다.<ul>
<li>예: <code>DECIMAL(20, 8)</code> -&gt; 전체 20자리 중 소수점 8자리까지 저장.</li>
<li>이렇게 해야 DB 수준에서도 정밀도가 보장되며, 자바의 <code>BigDecimal</code>과 완벽하게 매핑됩니다.</li>
</ul>
</li>
</ul>
<h2 id="6-마치며">6. 마치며</h2>
<p>돈을 다루는 개발자에게 <strong>'정확도'는 타협할 수 없는 가치</strong>입니다.
이제 우리는 오차 없는 숫자를 담을 준비가 되었습니다.</p>
<p>다음 챕터에서는 이 정확한 숫자들이 모여서 거래가 성사되는 과정, 즉 **[거래의 수명 주기(Lifecycle)]**를 상태 패턴(State Pattern)과 함께 다뤄보겠습니다.</p>
<hr />
<p><strong>Next Post:</strong> [Chapter 3. 거래의 수명 주기 (Lifecycle) - 주문은 살아있다]</p>
<hr />