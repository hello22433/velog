<p><strong>Phase 2 [논리와 연산의 기초]</strong>의 문을 여는 첫 번째 글입니다.</p>
<p>이번 주제는 컴퓨터 구조(Architecture)의 핵심이면서, 전공자들도 처음 배울 때 가장 헷갈려 하는 <strong>'보수(Complement)'</strong>와 <strong>'오버플로우(Overflow)'</strong>입니다. 하지만 어렵지 않게, '시계'와 '주행 거리계'에 비유해서 아주 쉽게 풀어보겠습니다.</p>
<hr />
<blockquote>
<p>&quot;컴퓨터는 계산기(Calculator)니까 덧셈, 뺄셈, 곱셈, 나눗셈 다 잘하겠지?&quot;</p>
</blockquote>
<p>반은 맞고 반은 틀렸습니다. 놀랍게도 <strong>컴퓨터(CPU)의 회로에는 '빼기(-)' 기능이 없습니다.</strong> (정확히는 뺄셈 전용 회로를 따로 만들지 않습니다.)</p>
<p>컴퓨터는 오직 <strong>'더하기(+)'</strong>밖에 모르는 바보입니다.
그렇다면 $5 - 3$은 어떻게 계산할까요? 컴퓨터는 이 식을 이렇게 바꿉니다.</p>
<p>$$5 + (-3)$$</p>
<p>&quot;3을 빼는 대신, -3을 더하면 되잖아?&quot;라는 논리죠.
그런데 문제가 있습니다. 0과 1밖에 모르는 컴퓨터에게 <strong>마이너스(-)</strong> 기호는 어떻게 가르쳐야 할까요? 여기서 천재적인 수학적 트릭, <strong>보수(Complement)</strong>가 등장합니다.</p>
<h2 id="1-시계를-거꾸로-돌리지-마세요-보수의-원리">1. 시계를 거꾸로 돌리지 마세요 (보수의 원리)</h2>
<p>지금 시계가 <strong>10시</strong>를 가리키고 있습니다. <strong>7시</strong>로 맞추고 싶다면 어떻게 해야 할까요?</p>
<ol>
<li><strong>뺄셈:</strong> 바늘을 뒤로 <strong>3칸</strong> 돌린다. ($10 - 3 = 7$)</li>
<li><strong>덧셈:</strong> 바늘을 앞으로 <strong>9칸</strong> 돌린다. ($10 + 9 = 19 \rightarrow$ 12를 넘었으니 7시!)</li>
</ol>
<p>결과는 똑같이 7시입니다. 즉, 시계 세상(12진법)에서는 <strong>'-3'과 '+9'가 똑같은 역할</strong>을 합니다. 여기서 9를 3에 대한 <strong>'보수(보충해 주는 수)'</strong>라고 부릅니다.</p>
<p>컴퓨터도 이 원리를 이용합니다. 뺄셈을 하는 대신, <strong>한 바퀴를 핑~ 돌아서 뒤로 간 효과</strong>를 내는 숫자를 더해버리는 것이죠.</p>
<h2 id="2-2의-보수-컴퓨터가-음수를-표현하는-법">2. 2의 보수: 컴퓨터가 음수를 표현하는 법</h2>
<p>컴퓨터는 2진수를 쓰니까 <strong>'2의 보수'</strong>를 사용합니다. 만드는 법은 아주 간단합니다.</p>
<blockquote>
<p><strong>규칙:</strong> &quot;뒤집고 1을 더해라!&quot;</p>
</blockquote>
<p>예를 들어, 10진수 <strong>3</strong>을 2진수(4비트 기준)로 표현하면 <code>0011</code>입니다. 이걸 <strong>-3</strong>으로 만들어볼까요?</p>
<ol>
<li><strong>뒤집기 (NOT 연산):</strong> 0은 1로, 1은 0으로 바꿉니다.<ul>
<li><code>0011</code> $\rightarrow$ <code>1100</code> (이걸 '1의 보수'라고 합니다)</li>
</ul>
</li>
<li><strong>1 더하기:</strong><ul>
<li><code>1100</code> + <code>1</code> = <strong><code>1101</code></strong></li>
</ul>
</li>
</ol>
<p>자, 이제 <code>1101</code>이 진짜 <strong>-3</strong>인지 확인해 봅시다. 원래 숫자 3(<code>0011</code>)과 더했을 때 0이 나오면 됩니다.</p>
<ul>
<li><code>0011</code> (3)</li>
<li><code>+ 1101</code> (-3)</li>
<li><code>-------</code></li>
<li><code>10000</code> $\rightarrow$ 맨 앞의 1은 자릿수를 넘쳐서 버려짐(비트 수 제한) $\rightarrow$ 결과는 <strong><code>0000</code> (0)</strong></li>
</ul>
<p>와우! 덧셈만 했는데 0이 되었습니다. 컴퓨터는 이렇게 '보수'를 이용해 뺄셈 회로 없이 덧셈 회로만으로 모든 계산을 처리합니다.</p>
<h2 id="3-오버플로우overflow-숫자가-폭발했다">3. 오버플로우(Overflow): 숫자가 폭발했다!</h2>
<p>하지만 이 방식에는 치명적인 부작용이 있습니다. 바로 <strong>오버플로우(Overflow, 넘침)</strong> 현상입니다.</p>
<p>자동차 주행 거리계를 상상해 보세요. <code>9999km</code>에서 1km를 더 달리면 어떻게 되나요? <code>10000</code>이 되는 게 아니라 다시 <code>0000</code>으로 돌아갑니다. 숫자가 표현 범위를 넘어서면 <strong>가장 작은 수로 리셋</strong>되는 것이죠.</p>
<p>컴퓨터의 데이터 타입도 그릇의 크기가 정해져 있습니다.
예를 들어 <code>byte</code> 타입(8비트)은 -128 ~ +127까지의 숫자만 담을 수 있습니다.</p>
<p>만약 <strong>127</strong>이 담긴 변수에 <strong>1</strong>을 더하면 어떻게 될까요?</p>
<ul>
<li>상식: 128</li>
<li>컴퓨터: <strong>-128</strong></li>
</ul>
<!-- end list -->

<pre><code class="language-java">// Java 코드로 보는 오버플로우
byte b = 127;
b++; // 1을 더함
System.out.println(b); // 출력: -128</code></pre>
<p>가장 큰 양수에서 1을 더했더니 갑자기 가장 작은 음수가 되어버렸습니다.
(마치 <strong>문명(Civilization) 게임</strong>에서 간디가 평화주의 수치가 너무 낮아지다 못해 오버플로우가 발생해서 핵폭탄을 쏘는 호전광이 되어버렸다는 전설의 버그와 같은 원리입니다.)</p>
<h2 id="4-개발자가-알아야-할-점">4. 개발자가 알아야 할 점</h2>
<p>요즘 우리가 쓰는 파이썬(Python)은 언어 차원에서 숫자가 커지면 알아서 메모리를 늘려주기 때문에 오버플로우 걱정이 덜합니다. 하지만 <strong>Java, C, C++</strong> 등 타입을 엄격하게 따지는 언어나, 데이터베이스 설계를 할 때는 이 개념이 필수입니다.</p>
<ul>
<li>&quot;이 데이터가 21억(int의 한계)을 넘을 것 같은가?&quot; $\rightarrow$ 그럼 <code>long</code>을 써야지.</li>
<li>&quot;왜 은행 잔고가 마이너스가 됐지?&quot; $\rightarrow$ 오버플로우가 났나?</li>
</ul>
<p>수학적 원리를 모르면 &quot;컴퓨터가 미쳤나?&quot; 싶겠지만, 원리를 알면 &quot;아, 비트가 넘쳤구나&quot; 하고 해결할 수 있습니다.</p>
<hr />
<h2 id="🔑-오늘의-핵심-요약">🔑 오늘의 핵심 요약</h2>
<ol>
<li>컴퓨터는 뺄셈 회로가 없다. 대신 <strong>보수(Complement)</strong>를 더해서 뺄셈 효과를 낸다.</li>
<li>음수를 만드는 공식: <strong>비트를 반전시키고 1을 더한다 (2의 보수).</strong></li>
<li>데이터 타입의 범위를 넘어서면 숫자가 순환해버리는 <strong>오버플로우(Overflow)</strong>가 발생한다.</li>
</ol>
<p>이제 덧셈, 뺄셈의 비밀이 풀렸습니다. 그런데 덧셈, 뺄셈보다 더 강력하고 재미있는 연산이 하나 더 있습니다.
바로 <strong>'나머지 연산(%)'</strong>입니다.</p>
<p>다음 시간에는 <strong>암호학의 기초이자 요일 계산의 마법사, '나머지 연산'</strong>에 대해 알아보겠습니다.</p>