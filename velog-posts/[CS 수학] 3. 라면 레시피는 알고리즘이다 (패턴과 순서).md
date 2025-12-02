<p>이번 주제는 개발자의 밥줄(?)이자 면접 단골 소재인 <strong>'알고리즘'</strong>입니다. 하지만 어렵지 않습니다. 유치원 때 배웠던 '규칙 찾기'와 '순서대로 줄 세우기'가 알고리즘의 본질이니까요.</p>
<hr />
<blockquote>
<p>&quot;라면을 가장 맛있게 끓이는 방법은 무엇일까요?&quot;</p>
</blockquote>
<p>봉지 뒷면에 적힌 <strong>조리법(Recipe)</strong>대로 끓이는 것입니다.</p>
<ol>
<li>물 550ml를 끓인다.</li>
<li>면과 스프를 넣는다.</li>
<li>4분 30초간 더 끓인다.</li>
</ol>
<p>이 조리법을 컴퓨터 과학 용어로 바꾸면 무엇일까요? 바로 <strong>알고리즘(Algorithm)</strong>입니다.</p>
<p>많은 초보자가 '알고리즘'이라는 단어를 들으면 복잡한 수식을 떠올리며 겁을 먹습니다. 하지만 알고리즘의 진짜 정의는 <strong>'문제를 해결하기 위한 정해진 절차'</strong>입니다.</p>
<p>우리는 유치원 때 이미 알고리즘의 기초 훈련을 마쳤습니다. 바로 <strong>'순서'</strong>와 <strong>'패턴'</strong>입니다.</p>
<h2 id="1-순서sequence-컴퓨터는-융통성이-없다">1. 순서(Sequence): 컴퓨터는 융통성이 없다</h2>
<p>&quot;양말을 신고 신발을 신으세요&quot;와 &quot;신발을 신고 양말을 신으세요&quot;.
결과는 천지 차이입니다. 후자는 슈퍼맨이 아닌 이상 불가능하죠.</p>
<p>수학에서 $2 \times 3 + 1$과 $2 \times (3 + 1)$의 결과가 다르듯, 프로그래밍에서도 <strong>명령어의 순서</strong>는 생명입니다.</p>
<p>컴퓨터는 기본적으로 위에서 아래로, 한 줄씩 코드를 읽어 내려갑니다. 이를 <strong>절차적 프로그래밍(Procedural Programming)</strong>이라고 합니다.</p>
<pre><code class="language-python"># 순서의 중요성
print(&quot;물 끓이기&quot;)
print(&quot;라면 넣기&quot;)
print(&quot;먹기&quot;) 
# 결과: 맛있는 라면

# 순서가 꼬인다면?
print(&quot;먹기&quot;)
print(&quot;라면 넣기&quot;) # 에러! (아직 안 끓였는데?)</code></pre>
<p>우리가 코드를 짤 때 가장 먼저 하는 일은, 머릿속에 있는 해결 방법을 <strong>아주 잘게 쪼개서 순서대로 나열하는 것</strong>입니다.</p>
<h2 id="2-패턴pattern-반복되는-귀찮음-해결하기">2. 패턴(Pattern): 반복되는 귀찮음 해결하기</h2>
<p>유치원 학습지 단골 문제, 기억나시나요?</p>
<blockquote>
<p>▲ ● ▲ ● ▲ ● ... 다음 빈칸에 들어갈 모양은?</p>
</blockquote>
<p>정답은 ▲입니다. 우리는 본능적으로 <strong>반복되는 규칙(Pattern)</strong>을 찾아냅니다.
컴퓨터 과학에서 이 '규칙 찾기'는 <strong>반복문(Loop)</strong>의 핵심이 됩니다.</p>
<p>만약 여러분이 &quot;사랑해&quot;라는 말을 100번 써야 한다면 어떨까요?
손으로 쓰면 100번의 노동이 필요하지만, 패턴을 아는 개발자는 단 두 줄로 끝냅니다.</p>
<pre><code class="language-python"># 100번 반복하는 패턴
for i in range(100):
    print(&quot;사랑해&quot;)</code></pre>
<p>수학에서 등차수열($1, 3, 5, 7...$)의 규칙을 찾아 일반항을 세우듯, 프로그래밍에서는 <strong>반복되는 작업을 찾아내 <code>for</code>이나 <code>while</code> 문으로 묶어버립니다.</strong> 이것이 개발자의 제1덕목, <strong>효율성</strong>입니다.</p>
<h2 id="3-순서도flowchart-알고리즘의-지도">3. 순서도(Flowchart): 알고리즘의 지도</h2>
<p>복잡한 알고리즘을 짤 때는 코드를 치기 전에 그림을 먼저 그립니다. 이것을 <strong>순서도(Flowchart)</strong>라고 합니다.
지하철 노선도처럼 데이터가 어디로 흘러가는지 보여주는 지도죠.</p>
<ul>
<li><strong>직사각형:</strong> 행동 (물 끓이기)</li>
<li><strong>마름모:</strong> 판단/조건 (물이 끓었니? Yes/No)</li>
<li><strong>화살표:</strong> 흐름의 순서</li>
</ul>
<p>이 마름모(판단)가 바로 프로그래밍의 <strong>조건문(<code>if</code>)</strong>입니다.
&quot;물이 끓으면(Yes) $\rightarrow$ 면을 넣고, 안 끓으면(No) $\rightarrow$ 기다린다.&quot;</p>
<p>이 단순한 <strong>순서(Sequence), 반복(Loop), 선택(Selection)</strong> 세 가지가 조합되어 인공지능도 만들고, 로켓도 쏘아 올리는 것입니다.</p>
<h2 id="4-생각하는-연습-알고리즘적-사고">4. 생각하는 연습: 알고리즘적 사고</h2>
<p>수학을 잘한다는 건 계산이 빠르다는 게 아니라, <strong>문제를 논리적인 순서로 쪼개는 능력</strong>이 좋다는 뜻입니다.</p>
<p>오늘부터 일상생활을 알고리즘으로 바꿔보는 연습을 해보세요.
<strong>[편의점 다녀오기 알고리즘]</strong></p>
<ol>
<li>집을 나선다.</li>
<li>편의점까지 걷는다.</li>
<li><strong>(조건)</strong> 문이 열려있는가?<ul>
<li>Yes: 들어간다.</li>
<li>No: 다른 편의점을 찾는다. (반복)</li>
</ul>
</li>
<li>물건을 고른다.</li>
<li>계산하고 나온다.</li>
</ol>
<p>이 과정이 머릿속에서 자연스럽게 그려진다면, 여러분은 이미 코딩을 할 준비가 끝난 것입니다.</p>
<hr />
<h2 id="🔑-오늘의-핵심-요약">🔑 오늘의 핵심 요약</h2>
<ol>
<li><strong>알고리즘</strong>은 거창한 수식이 아니라, 문제를 해결하는 <strong>조리법(Recipe)</strong>이다.</li>
<li><strong>순서(Sequence)</strong>가 바뀌면 결과가 완전히 달라진다. (신발 vs 양말)</li>
<li><strong>패턴(Pattern)</strong>을 찾으면 반복되는 일을 컴퓨터에게 떠넘길 수 있다. (<code>for</code> 문)</li>
<li>코딩하기 전에 <strong>순서도</strong>를 그려보면 논리가 명확해진다.</li>
</ol>
<hr />
<p>🎉 <strong>축하합니다! Phase 1 [컴퓨팅 사고력의 씨앗] 과정을 모두 수료하셨습니다.</strong></p>
<p>우리는 이제 0과 1의 세계(진법)를 이해하고, 데이터를 분류(집합)하고, 일을 시키는 법(알고리즘)까지 배웠습니다. 유치원 수준의 기초 수학이 컴퓨터 과학의 뿌리가 되는 과정을 보셨나요?</p>
<p>이제 레벨업을 할 시간입니다.
다음 화부터는 <strong>Phase 2 [논리와 연산의 기초]</strong>가 시작됩니다.
초등~중등 수학으로 넘어가서, 컴퓨터가 덧셈 뺄셈을 하는 방식과 암호의 비밀(나머지 연산)을 파헤쳐 보겠습니다.</p>
<p><strong>[예고] 4. 컴퓨터의 덧셈은 뭔가 다르다? (보수와 오버플로우)</strong>
기대해 주세요!</p>