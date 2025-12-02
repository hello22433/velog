<p><strong>Part 3. 모던 자바(Modern Java)</strong> 편입니다.</p>
<p>자바 8 이전과 이후는 거의 다른 언어라고 봐도 무방합니다. 이번 편의 핵심은 &quot;코드를 짧게 줄이는 것&quot;이 아니라 <strong>&quot;무엇(What)을 할 것인지 명확히 보여주는 것&quot;</strong>입니다.</p>
<hr />
<h1 id="part-3-if문과-for문을-걷어내는-마법-모던-자바">Part 3. if문과 for문을 걷어내는 마법, 모던 자바</h1>
<blockquote>
<p><strong>&quot;백엔드 개발자가 알아야 하는 최소한의 자바&quot;</strong> 시리즈의 네 번째 글입니다.</p>
<p>오늘은 자바 8에서 시작되어 자바를 '틀딱 언어'에서 구원해 준 3대장, <strong>람다(Lambda), 스트림(Stream), 옵셔널(Optional)</strong>을 다룹니다.</p>
</blockquote>
<hr />
<h3 id="선배님-코드가-안-읽혀요">&quot;선배님, 코드가 안 읽혀요.&quot;</h3>
<p>신입 시절, 선배의 코드를 보고 당황했던 기억이 납니다.
<code>for</code>문도 없고 <code>if</code>문도 없는데 데이터가 막 가공되어서 튀어나옵니다.</p>
<p>*&quot;이게 자바라고요?&quot;*</p>
<p>네, 이게 바로 <strong>모던 자바(Modern Java)</strong>입니다.
과거의 자바가 <strong>&quot;어떻게(How) 할 것인가&quot;</strong>를 구구절절 명령하는 스타일이었다면, 모던 자바는 <strong>&quot;무엇(What)을 원하는가&quot;</strong>를 선언하는 스타일입니다.</p>
<p>오늘은 이 마법 같은 도구들을 실무에서 <strong>'잘'</strong> 쓰는 법을 알아봅니다.</p>
<p>[Image of Java Stream API diagram]</p>
<br />

<h2 id="1-람다lambda-함수를-변수처럼">1. 람다(Lambda): 함수를 변수처럼!</h2>
<p>람다는 복잡한 익명 클래스(Anonymous Class)를 줄인 표현식입니다. 하지만 그 본질은 <strong>행위(함수)를 데이터처럼 주고받는 것</strong>에 있습니다.</p>
<p><strong>[Before: 예전의 자바]</strong></p>
<pre><code class="language-java">// 버튼을 누르면 할 일을 정의하기 위해 객체를 통째로 만듦
button.setOnClickListener(new OnClickListener() {
    @Override
    public void onClick(View v) {
        System.out.println(&quot;Click!&quot;);
    }
});</code></pre>
<p><strong>[After: 람다]</strong></p>
<pre><code class="language-java">// &quot;그냥 이거 실행해&quot; -&gt; 행위만 전달
button.setOnClickListener(v -&gt; System.out.println(&quot;Click!&quot;));</code></pre>
<h3 id="💡-실무-포인트-functionalinterface">💡 실무 포인트: <code>@FunctionalInterface</code></h3>
<p>람다는 아무 인터페이스나 다 되는 게 아닙니다. <strong>추상 메소드가 딱 하나만 있는 인터페이스</strong>만 람다로 바꿀 수 있습니다.
여러분이 만드는 인터페이스가 람다로 쓰이길 원한다면, 꼭 <code>@FunctionalInterface</code> 어노테이션을 붙여주세요. (메소드가 2개 이상이면 컴파일 에러를 띄워줍니다.)</p>
<br />

<h2 id="2-스트림stream-데이터의-공장-라인">2. 스트림(Stream): 데이터의 공장 라인</h2>
<p>스트림은 <strong>데이터의 흐름</strong>입니다. <code>for</code>문과 <code>if</code>문이 뒤섞인 로직을 깔끔한 파이프라인으로 바꿔줍니다.</p>
<p><strong>[상황]</strong>
&quot;회원 리스트(<code>users</code>)에서 <strong>30세 이상</strong>인 사람들의 <strong>이름</strong>만 뽑아서 <strong>리스트</strong>로 주세요.&quot;</p>
<p><strong>[Before: 절차지향적 코드]</strong></p>
<pre><code class="language-java">List&lt;String&gt; names = new ArrayList&lt;&gt;();
for (User user : users) {
    if (user.getAge() &gt;= 30) {
        names.add(user.getName());
    }
}
// 로직이 복잡해질수록 depth가 깊어지고 읽기 힘들어짐</code></pre>
<p><strong>[After: 스트림]</strong></p>
<pre><code class="language-java">List&lt;String&gt; names = users.stream()
    .filter(user -&gt; user.getAge() &gt;= 30) // 1. 30세 이상 거르기
    .map(User::getName)                  // 2. 이름표로 바꾸기
    .collect(Collectors.toList());       // 3. 박스에 담기</code></pre>
<p>코드가 영어 문장처럼 읽히지 않나요? *&quot;users를 stream해서 filter하고 map해서 collect한다.&quot;* 이것이 선언형 프로그래밍의 매력입니다.</p>
<h3 id="⚠️-스트림-주의사항-면접-단골">⚠️ 스트림 주의사항 (면접 단골)</h3>
<ol>
<li><strong>재사용 불가:</strong> 스트림은 한 번 쓰면 닫힙니다(Consumed). 또 쓰려면 <code>stream()</code>을 다시 열어야 합니다.</li>
<li><strong>디버깅의 어려움:</strong> <code>for</code>문은 브레이크 포인트를 찍기 쉽지만, 스트림은 한 줄로 이어져 있어서 디버깅이 까다롭습니다. (<code>peek()</code>을 써야 함)</li>
<li><strong>성능 이슈:</strong> 단순 반복문(<code>for-loop</code>)보다 스트림이 조금 더 느립니다. 하지만 가독성이 주는 이점이 훨씬 크기 때문에, <strong>초대량 데이터 처리가 아니라면 스트림을 쓰세요.</strong></li>
</ol>
<br />

<h2 id="3-optional-npe와의-전쟁을-끝내러-왔다">3. Optional: NPE와의 전쟁을 끝내러 왔다</h2>
<p><code>NullPointerException</code> (NPE)은 자바 개발자의 주적입니다. <code>Optional&lt;T&gt;</code>은 null일 수도 있는 객체를 감싸는 상자입니다.</p>
<p><strong>[Bad: 끔찍한 null 체크]</strong></p>
<pre><code class="language-java">public String getCity(User user) {
    if (user != null) {
        Address addr = user.getAddress();
        if (addr != null) {
            return addr.getCity();
        }
    }
    return &quot;Unknown&quot;;
}</code></pre>
<p><strong>[Good: 우아한 Optional]</strong></p>
<pre><code class="language-java">public String getCity(User user) {
    return Optional.ofNullable(user)
        .map(User::getAddress)
        .map(Address::getCity)
        .orElse(&quot;Unknown&quot;);
}</code></pre>
<h3 id="🔥-실무-꿀팁-orelse-vs-orelseget-중요">🔥 실무 꿀팁: <code>orElse</code> vs <code>orElseGet</code> (중요!)</h3>
<p>이 둘의 차이를 모르면 성능 이슈가 발생합니다.</p>
<ul>
<li><strong><code>orElse(T other)</code>:</strong> 값이 있든 없든 <strong>무조건 실행</strong>됩니다.</li>
<li><strong><code>orElseGet(Supplier other)</code>:</strong> 값이 <strong>없을 때만 실행</strong>됩니다. (Lazy)</li>
</ul>
<!-- end list -->

<pre><code class="language-java">// 1. orElse 사용 시
// user가 있어도 getDbData()를 실행해버림 -&gt; DB 조회 낭비 발생!
String name = opt.orElse(getDbData()); 

// 2. orElseGet 사용 시
// user가 없을 때만 getDbData()를 실행함 -&gt; 안전!
String name = opt.orElseGet(() -&gt; getDbData());</code></pre>
<blockquote>
<p><strong>규칙:</strong> 기본값(<code>0</code>, <code>&quot;&quot;</code>)을 줄 때는 <code>orElse</code>, 메소드를 호출하거나 객체를 생성할 때는 <code>orElseGet</code>을 쓰세요.</p>
</blockquote>
<h3 id="🚫-optional-안티-패턴">🚫 Optional 안티 패턴</h3>
<p>Optional은 <strong>'반환 타입(Return Type)'</strong>을 위해 만들어졌습니다.</p>
<ul>
<li>필드에 쓰지 마세요. (<code>private Optional&lt;User&gt; user;</code> ❌) - 직렬화 문제 발생</li>
<li>파라미터로 받지 마세요. (<code>method(Optional&lt;String&gt; str)</code> ❌) - 호출하는 쪽에서 <code>Optional.of()</code> 감싸느라 더 귀찮음</li>
<li><code>isPresent()</code> 쓰고 <code>get()</code> 할 거면 쓰지 마세요. (그냥 <code>if (obj != null)</code> 쓰는 거랑 똑같음)</li>
</ul>
<br />

<h2 id="4-마치며-도구는-도구일-뿐">4. 마치며: 도구는 도구일 뿐</h2>
<p>람다와 스트림은 코드를 '짧게' 만드는 게 목적이 아니라 <strong>'논리를 명확하게'</strong> 만드는 게 목적입니다.</p>
<p>너무 과한 스트림 사용은 오히려 독이 됩니다. 팀원들이 알아보기 힘들 정도로 복잡한 스트림 체이닝은 차라리 <code>for</code>문으로 푸는 게 낫습니다.</p>
<p><strong>&quot;가독성은 언제나 똑똑함(Cleverness)을 이깁니다.&quot;</strong></p>
<p>다음 시간에는 자바 개발자들이 가장 많이 실수하는 그곳, <strong>[Part 4] 컬렉션 프레임워크(Collections) 다시 보기</strong>로 찾아오겠습니다. 무지성 <code>ArrayList</code> 사용을 멈추는 법을 알려드립니다.</p>
<blockquote>
<p><strong>Next:</strong> [Part 4] ArrayList와 HashMap, 제대로 알고 쓰시나요?</p>
</blockquote>