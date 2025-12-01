<blockquote>
<p>&quot;세상에는 10종류의 사람이 있다. 이진수를 이해하는 사람과 이해하지 못하는 사람.&quot;
(There are 10 types of people in the world: Those who understand binary, and those who don't.)</p>
</blockquote>
<p>개발자들 사이에서 유명한 농담입니다. 여기서 '10'은 십(Ten)이 아니라 이진수 '일영(Two)'을 뜻하죠.</p>
<p>우리는 살면서 **10진수(Decimal)**에 너무나 익숙해져 있습니다. 0부터 9까지의 숫자를 쓰고, 9 다음에는 자릿수를 올려 10이 됩니다. 그런데 왜 하필 10일까요? 답은 아주 단순합니다. <strong>우리의 손가락이 10개이기 때문입니다.</strong></p>
<p>만약 인류의 손가락이 8개였다면, 우리는 자연스럽게 8진법을 썼을 겁니다.</p>
<p>그렇다면 <strong>컴퓨터</strong>를 볼까요? 컴퓨터에게는 손가락이 없습니다. 대신 <strong>전기</strong>가 흐르는 회로가 있죠. 이 차이가 모든 것의 시작입니다.</p>
<h2 id="1-컴퓨터의-손가락은-1개다-스위치-이론">1. 컴퓨터의 손가락은 1개다? (스위치 이론)</h2>
<p>컴퓨터의 뇌인 CPU를 아주 단순하게 쪼개보면 수많은 **스위치(Transistor)**들로 이루어져 있습니다.</p>
<p>전기로 숫자를 표현하려면 어떻게 해야 할까요?
전압을 조절해서 0V는 0, 1V는 1, ... 9V는 9로 표현하면 10진수를 쓸 수 있지 않을까요?</p>
<p>이론상은 가능하지만, 현실은 어렵습니다. 전기는 불안정해서 3V를 보내도 가끔 2.9V가 되거나 3.1V가 될 수 있습니다. 2와 3 사이를 구분하다가 에러가 날 확률이 매우 높죠.</p>
<p>그래서 공학자들은 가장 안전하고 확실한 방법을 택했습니다.</p>
<ul>
<li><strong>전기가 흐른다 (ON) → 1</strong></li>
<li><strong>전기가 안 흐른다 (OFF) → 0</strong></li>
</ul>
<p>이분법적인 확실함. 이것이 컴퓨터가 **2진법(Binary)**을 사용하는 근본적인 이유입니다. 흑백논리 같지만, 기계에게는 이것만큼 명확한 언어가 없습니다.</p>
<p><img alt="" src="https://velog.velcdn.com/images/hello22433/post/e6114832-2ae7-47a5-95a2-8567e246ef4b/image.png" /></p>
<h2 id="2-0과-1로-숫자를-세는-법">2. 0과 1로 숫자를 세는 법</h2>
<p>우리가 쓰는 10진수는 자릿수가 하나 올라갈 때마다 <strong>10배</strong>씩 커집니다. ($1, 10, 100, 1000...$)
반면 2진수는 자릿수가 올라갈 때마다 <strong>2배</strong>씩 커집니다. ($1, 2, 4, 8, 16...$)</p>
<p>숫자가 어떻게 늘어나는지 볼까요?</p>
<table>
<thead>
<tr>
<th align="center">10진수 (우리의 언어)</th>
<th align="center">2진수 (컴퓨터의 언어)</th>
<th align="left">설명</th>
</tr>
</thead>
<tbody><tr>
<td align="center">0</td>
<td align="center"><strong>0</strong></td>
<td align="left">전구 꺼짐</td>
</tr>
<tr>
<td align="center">1</td>
<td align="center"><strong>1</strong></td>
<td align="left">전구 켜짐</td>
</tr>
<tr>
<td align="center">2</td>
<td align="center"><strong>10</strong></td>
<td align="left">자릿수 올림 ($1\times2 + 0\times1$)</td>
</tr>
<tr>
<td align="center">3</td>
<td align="center"><strong>11</strong></td>
<td align="left">($1\times2 + 1\times1$)</td>
</tr>
<tr>
<td align="center">4</td>
<td align="center"><strong>100</strong></td>
<td align="left">($1\times4 + 0\times2 + 0\times1$)</td>
</tr>
<tr>
<td align="center">...</td>
<td align="center">...</td>
<td align="left">...</td>
</tr>
<tr>
<td align="center">255</td>
<td align="center"><strong>11111111</strong></td>
<td align="left">8비트가 꽉 찬 상태</td>
</tr>
</tbody></table>
<h2 id="3-개발자의-상식-비트bit와-바이트byte">3. 개발자의 상식: 비트(Bit)와 바이트(Byte)</h2>
<p>여기서 중요한 용어가 나옵니다.</p>
<ul>
<li><strong>비트 (Bit):</strong> 0 또는 1을 담을 수 있는 가장 작은 정보 단위 (Binary Digit의 약자). 전구 1개.</li>
<li><strong>바이트 (Byte):</strong> 비트 8개를 묶은 것.</li>
</ul>
<p><strong>&quot;왜 하필 8개를 묶었나요?&quot;</strong>
초창기 컴퓨터 과학자들이 영문 알파벳 대소문자와 숫자, 특수문자를 다 표현하려면 최소한 128~256가지의 경우의 수가 필요하다고 계산했기 때문입니다. ($2^8 = 256$)</p>
<p>그래서 1바이트는 <strong>글자 한 자</strong>를 저장하는 기준이 되었습니다.</p>
<h2 id="4-파이썬으로-확인해보기">4. 파이썬으로 확인해보기</h2>
<p>백문이 불여일견, 코드로 확인해 봅시다. 파이썬에는 10진수를 2진수로 바꿔주는 <code>bin()</code> 함수가 있습니다.</p>
<pre><code class="language-python"># 10진수를 2진수로 변환
print(bin(2))   # 출력: 0b10
print(bin(3))   # 출력: 0b11
print(bin(10))  # 출력: 0b1010
print(bin(255)) # 출력: 0b11111111

# 앞에 붙은 '0b'는 이 숫자가 2진수(binary)라는 뜻입니다.</code></pre>
<p>반대로 2진수를 10진수로 계산하는 것도 간단합니다.</p>
<pre><code class="language-python"># 2진수 1010을 10진수로 변환
print(int('1010', 2)) # 출력: 10</code></pre>
<h2 id="5-보너스-16진수hexadecimal는-왜-쓸까">5. 보너스: 16진수(Hexadecimal)는 왜 쓸까?</h2>
<p>개발을 하다 보면 <code>0xFF</code>, <code>#A3F122</code> 같은 이상한 숫자와 문자의 조합을 봅니다. 바로 <strong>16진수</strong>입니다.</p>
<p>컴퓨터는 0과 1을 좋아하지만, 숫자가 조금만 커져도 길이가 너무 길어집니다.</p>
<ul>
<li>10진수 <code>255</code> $\rightarrow$ 2진수 <code>11111111</code> (너무 길어서 읽기 힘들다!)</li>
</ul>
<p>그래서 2진수 4개를 묶어서 한 글자로 표현하는 16진법을 사용합니다.
0~9까지 쓰고, 10부터는 A, B, C, D, E, F를 빌려와서 씁니다. (F = 15)</p>
<ul>
<li>2진수 <code>1111 1111</code> $\rightarrow$ 16진수 <code>FF</code></li>
</ul>
<p>주로 **색상 코드(RGB)**나 <strong>메모리 주소</strong>를 표현할 때, 인간이 보기 편하게 줄여 쓰기 위해 사용합니다.</p>
<hr />
<h2 id="🔑-오늘의-핵심-요약">🔑 오늘의 핵심 요약</h2>
<ol>
<li>우리는 손가락이 10개라 <strong>10진수</strong>를 쓰고, 컴퓨터는 스위치(ON/OFF)가 편해서 <strong>2진수</strong>를 쓴다.</li>
<li>**비트(Bit)**는 전구 1개, **바이트(Byte)**는 전구 8개 묶음이다.</li>
<li><strong>16진수</strong>는 2진수가 너무 길어서 보기 좋게 4자리씩 끊어서 줄인 것이다.</li>
</ol>
<p>수학이라기엔 너무 기초적이었나요? 하지만 이 0과 1이 모여서 우리가 보는 화려한 그래픽과 인공지능을 만듭니다.</p>
<p>다음 시간에는 <strong>이 0과 1들을 어떻게 정리 정돈할까?</strong>
<strong>'분류와 집합'</strong>을 통해 데이터 타입과 폴더 구조의 원리를 알아보겠습니다.</p>