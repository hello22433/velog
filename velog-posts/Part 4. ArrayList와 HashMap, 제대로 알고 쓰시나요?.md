<p><strong>Part 4. 컬렉션 프레임워크(Collections)</strong> 편입니다.</p>
<p>자바 개발자가 숨 쉬듯이 사용하는 <code>ArrayList</code>와 <code>HashMap</code>.
하지만 의외로 내부 구조를 모르고 써서 성능 저하나 버그를 만드는 경우가 많습니다. 이번 편은 <strong>&quot;기본기를 탄탄하게 다지는&quot;</strong> 내용으로 구성했습니다.</p>
<hr />
<h1 id="part-4-arraylist와-hashmap-제대로-알고-쓰시나요">Part 4. ArrayList와 HashMap, 제대로 알고 쓰시나요?</h1>
<blockquote>
<p><strong>&quot;백엔드 개발자가 알아야 하는 최소한의 자바&quot;</strong> 시리즈의 다섯 번째 글입니다.</p>
<p>오늘은 자바 프로그래밍의 9할을 차지한다고 해도 과언이 아닌, <strong>컬렉션 프레임워크(Collection Framework)</strong>를 해부해 봅니다.</p>
</blockquote>
<hr />
<h3 id="그냥-습관적으로-new-arraylist-했습니다">&quot;그냥 습관적으로 new ArrayList() 했습니다.&quot;</h3>
<p>코드 리뷰를 하다 보면 가장 많이 듣는 변명입니다.
데이터를 담아야 하면 무조건 <code>ArrayList</code>, 키-값 쌍이 필요하면 무조건 <code>HashMap</code>.</p>
<p>물론 <code>ArrayList</code>는 훌륭한 클래스입니다. 하지만 <strong>왜 쓰는지, 내부가 어떻게 생겼는지</strong> 모르면 대용량 데이터를 처리할 때 서버가 멈추거나(OOM), 원인을 알 수 없는 버그에 시달리게 됩니다.</p>
<p>오늘은 익숙함에 속아 놓치고 있던 컬렉션의 <strong>'디테일'</strong>을 챙겨봅시다.</p>
<br />

<h2 id="1-list-arraylist가-만능은-아니다">1. List: ArrayList가 만능은 아니다?</h2>
<p><code>List</code> 인터페이스의 구현체는 크게 두 가지, <code>ArrayList</code>와 <code>LinkedList</code>가 있습니다. 면접족보에서는 이렇게 가르칩니다.</p>
<ul>
<li><strong>ArrayList:</strong> 조회가 빠르고, 중간 삽입/삭제가 느리다.</li>
<li><strong>LinkedList:</strong> 조회가 느리고, 중간 삽입/삭제가 빠르다.</li>
</ul>
<h3 id="팩트-체크-실무에서는-99-arraylist를-씁니다">팩트 체크: 실무에서는 99% ArrayList를 씁니다.</h3>
<p>이론상 중간 삽입은 <code>LinkedList</code>가 빨라야 하지만, 실제로는 <strong>CPU 캐시(Cache) 효율</strong> 때문에 <code>ArrayList</code>가 더 빠른 경우가 많습니다. <code>ArrayList</code>는 메모리에 연속적으로 붙어있지만, <code>LinkedList</code>는 여기저기 흩어져 있어서 참조를 따라가느라 바쁘거든요.</p>
<p>단, <strong><code>ArrayList</code>를 쓸 때 꼭 지켜야 할 습관</strong>이 있습니다.</p>
<h3 id="🔥-성능-꿀팁-초기-용량capacity-설정하기">🔥 성능 꿀팁: 초기 용량(Capacity) 설정하기</h3>
<p><code>ArrayList</code>는 내부에 <strong>배열(Array)</strong>을 가지고 있습니다. 처음에 크기 10짜리 배열을 만들었다가, 꽉 차면 <strong>크기를 1.5배 늘린 새 배열을 만들고 기존 데이터를 복사</strong>합니다. (이사 비용 발생!)</p>
<p>데이터가 대략 1,000개 정도 들어올 걸 안다면?</p>
<pre><code class="language-java">// Bad: 기본값(10)으로 시작. 1000개 될 때까지 여러 번 이사 다녀야 함.
List&lt;String&gt; list = new ArrayList&lt;&gt;(); 

// Good: 처음부터 방을 1000개 잡아둠. 이사 비용 0.
List&lt;String&gt; list = new ArrayList&lt;&gt;(1000); </code></pre>
<p>이 작은 습관이 대량 처리 시 성능을 좌우합니다.</p>
<br />

<h2 id="2-map-hashmap의-비밀과-hashcode">2. Map: HashMap의 비밀과 hashCode</h2>
<p><code>HashMap</code>은 Key-Value 쌍을 저장합니다. 여기서 핵심 질문!
<strong>&quot;Key가 달라도 해시코드(Hash Code)가 같으면 어떻게 되나요?&quot;</strong></p>
<p>이것을 <strong>해시 충돌(Hash Collision)</strong>이라고 합니다. 자바의 HashMap은 충돌이 나면 같은 버킷(Bucket) 안에 <strong>LinkedList</strong>로 줄을 세웁니다. (Java 8부터는 데이터가 많아지면 <strong>Red-Black Tree</strong>로 변신해서 성능을 최적화합니다.)</p>
<h3 id="⚠️-치명적-실수-equals와-hashcode">⚠️ 치명적 실수: equals와 hashCode</h3>
<p>직접 만든 객체(Dto 등)를 <code>Map</code>의 Key로 쓸 때는 반드시 <code>equals()</code>와 <code>hashCode()</code>를 재정의(Override)해야 합니다.</p>
<pre><code class="language-java">// Key로 쓸 객체인데 오버라이딩 안 함
class MemberKey {
    private Long id;
    public MemberKey(Long id) { this.id = id; }
}

Map&lt;MemberKey, String&gt; map = new HashMap&lt;&gt;();
map.put(new MemberKey(1L), &quot;홍길동&quot;);

// 결과는? null이 나옵니다!
// new로 만든 객체는 메모리 주소가 다르니까요.
String name = map.get(new MemberKey(1L)); </code></pre>
<p><strong>lombok의 <code>@EqualsAndHashCode</code></strong>를 쓰거나, IDE의 자동 생성 기능을 이용해 반드시 두 메소드를 짝으로 맞춰주세요.</p>
<br />

<h2 id="3-modern-java의-컬렉션-불변immutable의-미학">3. Modern Java의 컬렉션: 불변(Immutable)의 미학</h2>
<p>Java 9부터는 컬렉션을 만드는 더 우아한 방법이 생겼습니다.</p>
<p><strong>[Before: 투박한 초기화]</strong></p>
<pre><code class="language-java">List&lt;String&gt; list = new ArrayList&lt;&gt;();
list.add(&quot;Apple&quot;);
list.add(&quot;Banana&quot;);
// 누군가 list.add(&quot;Cherry&quot;)를 해도 막을 수 없음 (가변)</code></pre>
<p><strong>[After: 깔끔하고 안전하게]</strong></p>
<pre><code class="language-java">// 불변 리스트 생성 (Java 9+)
List&lt;String&gt; list = List.of(&quot;Apple&quot;, &quot;Banana&quot;);

// 수정 시도 시 UnsupportedOperationException 발생!
// list.add(&quot;Cherry&quot;); // 펑!</code></pre>
<p><code>List.of</code>, <code>Map.of</code>, <code>Set.of</code>로 만든 컬렉션은 <strong>불변(Immutable)</strong>입니다.
데이터가 변하지 않는다는 보장이 있으면, 멀티 스레드 환경에서도 안전하고 코드를 읽기도 훨씬 편해집니다. 조회용 데이터라면 적극적으로 활용하세요.</p>
<br />

<h2 id="4-일급-컬렉션-first-class-collection">4. 일급 컬렉션 (First Class Collection)</h2>
<p>객체지향 생활 체조(Object Calisthenics)에서 강조하는 개념입니다. 쉽게 말해 <strong>&quot;List 하나만 필드로 가지는 클래스&quot;</strong>를 만드는 것입니다.</p>
<p><strong>[상황]</strong> 로또 번호 6개를 관리해야 합니다.</p>
<p><strong>[Bad: 모든 검증 로직이 서비스에 흩어짐]</strong></p>
<pre><code class="language-java">// Service Layer
List&lt;Integer&gt; lottoNumbers = new ArrayList&lt;&gt;();
if (lottoNumbers.size() &gt;= 6) throw new Exception(); // 검증 1
if (hasDuplicate(lottoNumbers)) throw new Exception(); // 검증 2
lottoNumbers.add(45);</code></pre>
<p><strong>[Good: 일급 컬렉션 사용]</strong></p>
<pre><code class="language-java">public class LottoTicket {
    private final List&lt;Integer&gt; numbers;

    public LottoTicket(List&lt;Integer&gt; numbers) {
        validateSize(numbers);
        validateDuplicate(numbers);
        this.numbers = numbers;
    }
    // 검증 로직은 다 여기(도메인)로 숨김!
}

// Service Layer
LottoTicket ticket = new LottoTicket(numbers); // 깔끔</code></pre>
<p>비즈니스 로직을 컬렉션 객체 안으로 밀어 넣음으로써(캡슐화), 서비스 코드가 매우 깔끔해지고 유지보수가 쉬워집니다.</p>
<br />

<h2 id="5-마치며-기본기가-성능을-만든다">5. 마치며: 기본기가 성능을 만든다</h2>
<p>화려한 라이브러리보다 중요한 것은 기본 자료구조에 대한 이해입니다.</p>
<ol>
<li><strong>ArrayList:</strong> 크기를 알면 <code>initialCapacity</code>를 지정하자.</li>
<li><strong>HashMap:</strong> Key로 쓸 객체는 <code>equals</code> &amp; <code>hashCode</code> 필수.</li>
<li><strong>Modern Java:</strong> <code>List.of()</code>로 불변성을 챙기자.</li>
<li><strong>설계:</strong> 리스트를 그냥 쓰지 말고 '일급 컬렉션'으로 감싸보자.</li>
</ol>
<p>이 네 가지만 기억해도 여러분의 코드는 훨씬 더 견고해질 것입니다.</p>
<p>다음 시간에는 백엔드 개발자의 숙명, <strong>&quot;예외 처리(Exception Handling)&quot;</strong>에 대해 다룹니다. <code>try-catch</code>로 도배된 코드를 구원하는 법, 기대해 주세요.</p>
<blockquote>
<p><strong>Next:</strong> [Part 5] 에러를 우아하게 처리하는 법 (Checked vs Unchecked)</p>
</blockquote>