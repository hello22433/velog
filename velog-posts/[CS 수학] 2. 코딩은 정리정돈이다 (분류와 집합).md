<p>두 번째 에피소드 <strong>'분류와 집합'</strong>입니다.</p>
<p>어릴 때 장난감 정리하던 기억, 다들 있으시죠? 레고는 레고 통에, 인형은 인형 통에.
이 <strong>'정리정돈'</strong>의 습관이 프로그래밍에서는 <strong>에러를 막아주는 가장 강력한 방패</strong>가 됩니다. 컴퓨터 과학 관점에서 집합을 재해석한 글입니다.</p>
<hr />
<blockquote>
<p>&quot;사과(Apple)에서 3을 뺄 수 있나요?&quot;</p>
</blockquote>
<p>지나가던 유치원생에게 물어봐도 고개를 가로저을 겁니다. 사과는 과일이고, 3은 숫자니까요. 서로 다른 종류끼리는 계산할 수 없습니다.</p>
<p>우리는 아주 어릴 때부터 본능적으로 <strong>'분류(Classification)'</strong>를 배웁니다. 동그라미는 동그라미끼리, 빨간색은 빨간색끼리 모으죠. 수학에서는 이것을 <strong>집합(Set)</strong>이라고 부릅니다.</p>
<p>컴퓨터 과학에서 이 '집합'의 개념이 무너지면 어떤 일이 벌어질까요? 프로그램이 멈추고, 데이터가 꼬이고, 대혼란(Chaos)이 찾아옵니다.</p>
<h2 id="1-데이터-타입-컴퓨터의-장난감-정리함">1. 데이터 타입: 컴퓨터의 장난감 정리함</h2>
<p>컴퓨터 메모리는 거대한 장난감 방과 같습니다. 아무렇게나 데이터를 던져 놓으면 나중에 찾을 수도, 계산할 수도 없습니다. 그래서 프로그래밍 언어는 엄격한 <strong>'정리 규칙'</strong>을 가지고 있습니다. 이를 <strong>데이터 타입(Data Type)</strong>이라고 합니다.</p>
<p>수학의 집합과 프로그래밍의 타입을 비교해 볼까요?</p>
<ul>
<li><strong>수학:</strong> 정수의 집합 ($\mathbb{Z}$), 실수의 집합 ($\mathbb{R}$), 문자의 집합</li>
<li><strong>코딩:</strong> <code>int</code> (정수), <code>float</code> (실수), <code>String</code> (문자열)</li>
</ul>
<p>우리가 코딩을 할 때 <code>int a = 3</code>이라고 쓰는 건, <strong>&quot;a라는 상자에는 '정수 집합'에 속한 원소만 넣을 거야!&quot;</strong>라고 컴퓨터와 약속하는 것입니다.</p>
<h3 id="💥-왜-중요할까-type-error">💥 왜 중요할까? (Type Error)</h3>
<p>만약 이 약속을 어기고 문자열 &quot;Hello&quot;에 숫자 5를 더하려고 하면 어떻게 될까요?</p>
<pre><code class="language-python">print(&quot;Hello&quot; + 5)
# 결과: TypeError: can only concatenate str (not &quot;int&quot;) to str</code></pre>
<p>컴퓨터는 즉시 에러를 뱉어냅니다. &quot;문자 집합과 숫자 집합은 섞일 수 없어!&quot;라고 말이죠.
수학에서 배운 <strong>'교집합이 없는 서로소(Disjoint Set)'</strong> 개념이 여기서 시스템의 안정성을 지켜주는 가드레일 역할을 합니다.</p>
<h2 id="2-벤-다이어그램과-검색-필터">2. 벤 다이어그램과 검색 필터</h2>
<p>집합 하면 떠오르는 그림, 바로 <strong>벤 다이어그램</strong>입니다.</p>
<p><img alt="" src="https://velog.velcdn.com/images/hello22433/post/e7aa7e26-d27a-45ad-bed1-4b7c36269de4/image.png" /></p>
<p>학창 시절엔 색칠 놀이처럼 보였던 이 그림이, 개발자가 되면 <strong>데이터베이스(DB)</strong>를 다루는 핵심 도구가 됩니다. 쇼핑몰에서 상품을 검색하는 상황을 생각해 봅시다.</p>
<ul>
<li><strong>집합 A:</strong> '전자제품' 카테고리에 있는 모든 상품</li>
<li><strong>집합 B:</strong> 가격이 '100만 원 이하'인 모든 상품</li>
<li><strong>집합 C:</strong> 브랜드가 'Apple'인 모든 상품</li>
</ul>
<p>우리가 &quot;100만 원 이하인 Apple 전자제품 찾아줘&quot;라고 검색하면, 컴퓨터는 내부적으로 <strong>교집합($A \cap B \cap C$)</strong> 연산을 수행합니다.</p>
<p>SQL과 같은 데이터베이스 언어는 사실상 <strong>집합 연산 명령어 모음</strong>입니다.
(나중에 배우겠지만, <code>INNER JOIN</code>, <code>LEFT JOIN</code> 같은 무시무시한 용어들도 사실 벤 다이어그램 원 두 개 겹쳐 놓은 것에 불과합니다.)</p>
<h2 id="3-구조체와-객체-나만의-집합-만들기">3. 구조체와 객체: 나만의 집합 만들기</h2>
<p>수학자들은 이미 있는 집합(정수, 실수 등)을 연구하지만, <strong>개발자는 자신만의 집합을 창조</strong>할 수 있습니다. 이것이 프로그래밍의 매력입니다.</p>
<p>예를 들어 '학생'이라는 새로운 집합(Class)을 정의해 볼까요?</p>
<pre><code class="language-python">class Student:
    def __init__(self, name, id):
        self.name = name  # 이름 속성
        self.id = id      # 학번 속성</code></pre>
<p>이제 컴퓨터에게 <code>Student</code>는 <code>int</code>나 <code>String</code>처럼 하나의 당당한 집합(Type)이 됩니다.
<strong>객체 지향 프로그래밍(OOP)</strong>은 이렇게 우리가 정의한 집합들끼리의 관계(포함 관계, 상속 등)를 설계하는 거대한 수학적 건축물입니다.</p>
<h2 id="4-파이썬으로-집합-다루기">4. 파이썬으로 집합 다루기</h2>
<p>파이썬에는 수학의 집합을 그대로 옮겨놓은 <code>set</code>이라는 자료형이 있습니다. 아주 재미있는 특징이 하나 있는데, <strong>&quot;중복을 허용하지 않는다&quot;</strong>는 점입니다.</p>
<pre><code class="language-python"># 장바구니에 사과를 3번 담아도, 종류는 '사과' 하나뿐입니다.
basket = {'apple', 'banana', 'apple', 'apple'}

print(basket)
# 출력: {'banana', 'apple'} -&gt; 중복이 자동으로 사라짐!</code></pre>
<p>이 성질을 이용하면 복잡한 데이터에서 중복을 제거하는 코드를 단 한 줄로 짤 수 있습니다.</p>
<pre><code class="language-python"># 중복이 많은 리스트
numbers = [1, 2, 2, 3, 3, 3, 4]

# 집합으로 변환하면 중복 삭제 끝!
unique_numbers = set(numbers) # {1, 2, 3, 4}</code></pre>
<hr />
<h2 id="🔑-오늘의-핵심-요약">🔑 오늘의 핵심 요약</h2>
<ol>
<li>프로그래밍의 <strong>데이터 타입(Type)</strong>은 수학의 <strong>집합(Set)</strong> 개념을 컴퓨터에 적용한 것이다.</li>
<li>타입이 다르면 연산할 수 없다. (사과 + 3 = 에러)</li>
<li>쇼핑몰 검색 필터나 데이터베이스는 <strong>교집합, 합집합</strong> 원리로 돌아간다.</li>
<li>파이썬의 <code>set</code>을 쓰면 데이터 <strong>중복 제거</strong>를 아주 쉽게 할 수 있다.</li>
</ol>
<p>이제 우리는 데이터를 0과 1로 표현하고(1편), 종류별로 정리함에 담는 법(2편)까지 알게 되었습니다.</p>
<p>그렇다면 이제 이 재료들을 가지고 <strong>요리(일 처리)</strong>를 해야겠죠?
다음 시간에는 라면 끓이는 순서로 배우는 코딩의 핵심, <strong>'패턴과 순서(알고리즘의 기초)'</strong>에 대해 이야기해 보겠습니다.</p>