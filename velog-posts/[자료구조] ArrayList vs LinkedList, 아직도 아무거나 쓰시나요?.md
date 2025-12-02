<p><strong>Part 4. 밥벌이 자료구조</strong>의 시작입니다.</p>
<p>자료구조는 코딩 테스트뿐만 아니라, <strong>&quot;이 상황에서 어떤 컬렉션을 써야 성능이 나올까?&quot;</strong>를 판단하는 실무 감각의 척도입니다.</p>
<p>가장 기본이지만 의외로 많은 개발자가 &quot;그냥 편해서&quot; <code>ArrayList</code>만 쓰는 경우가 많습니다. 왜 <code>LinkedList</code>가 존재하는지, 두 녀석의 태생적 차이를 통해 확실하게 구분해 드립니다.</p>
<hr />
<h2 id="자료구조-arraylist-vs-linkedlist-아직도-아무거나-쓰시나요">[자료구조] ArrayList vs LinkedList, 아직도 아무거나 쓰시나요?</h2>
<h3 id="list면-다-똑같은-거-아닌가요">&quot;List면 다 똑같은 거 아닌가요?&quot;</h3>
<p>자바에서 <code>List</code> 인터페이스를 구현한 클래스는 많습니다. 하지만 우리는 습관적으로 이렇게 씁니다.</p>
<pre><code class="language-java">List&lt;String&gt; list = new ArrayList&lt;&gt;();</code></pre>
<p>왜 <code>LinkedList</code>는 잘 안 쓸까요? 아니, 애초에 <code>LinkedList</code>는 언제 써야 할까요?
이 둘의 차이를 모르면, <strong>데이터가 수십만 개 쌓였을 때 프로그램 속도가 수백 배 차이 날 수 있습니다.</strong></p>
<p>오늘은 자바 컬렉션 프레임워크(JCF)의 양대 산맥, <strong>배열 기반 리스트</strong>와 <strong>연결 기반 리스트</strong>의 결정적 차이를 파헤쳐 봅니다.</p>
<hr />
<h3 id="1-arraylist-붙어-있어서-빠르다">1. ArrayList: &quot;붙어 있어서 빠르다&quot;</h3>
<p><code>ArrayList</code>는 이름 그대로 <strong>Array(배열)</strong>의 업그레이드 버전입니다.</p>
<ul>
<li><strong>구조:</strong> 메모리상에 데이터들이 <strong>연속적으로</strong> 다닥다닥 붙어 있습니다.</li>
<li><strong>특징:</strong> 빈틈없이 채워져 있어서 인덱스(Index)로 관리합니다.</li>
</ul>
<h4 id="👍-장점-조회search가-빛의-속도다">👍 장점: 조회(Search)가 빛의 속도다</h4>
<p>데이터들이 순서대로 붙어있기 때문에 <code>get(100)</code>을 호출하면 계산 한 번으로 바로 100번째 위치를 찾아갑니다.</p>
<ul>
<li><strong>시간 복잡도:</strong> <code>O(1)</code> (무조건 한 번에 찾음)</li>
</ul>
<h4 id="👎-단점-중간에-끼어들기가-힘들다">👎 단점: 중간에 끼어들기가 힘들다</h4>
<p>이미 꽉 찬 지하철 의자(배열)를 생각해보세요.
중간에 한 명이 앉으려면, 그 뒤에 앉은 모든 사람이 엉덩이를 들고 한 칸씩 옆으로 이동해야 합니다.</p>
<ul>
<li><strong>중간 삽입/삭제:</strong> 데이터를 뒤로 밀거나 당기는 연산(Shifting)이 필요해서 느립니다.</li>
<li><strong>시간 복잡도:</strong> <code>O(n)</code> (데이터 개수만큼 이동)</li>
</ul>
<hr />
<h3 id="2-linkedlist-흩어져-있어서-유연하다">2. LinkedList: &quot;흩어져 있어서 유연하다&quot;</h3>
<p><code>LinkedList</code>는 데이터들이 메모리 여기저기에 흩어져 있고, 서로를 <strong>줄(Link)</strong>로 연결하고 있습니다.</p>
<ul>
<li><strong>구조:</strong> <strong>Node(노드)</strong>라는 바구니에 '데이터'와 '다음 데이터의 주소'를 담고 있습니다.</li>
<li><strong>특징:</strong> 물리적으로 붙어있지 않고 논리적으로 연결되어 있습니다.</li>
</ul>
<h4 id="👍-장점-삽입삭제가-너무-쉽다">👍 장점: 삽입/삭제가 너무 쉽다</h4>
<p>중간에 데이터를 넣고 싶으면?
줄(참조)을 끊고 새 노드를 연결만 해주면 끝입니다. 뒤에 있는 데이터를 밀고 당길 필요가 없습니다.</p>
<ul>
<li><strong>삽입/삭제:</strong> 위치만 안다면 매우 빠릅니다. <code>O(1)</code></li>
</ul>
<h4 id="👎-단점-조회search가-느리다">👎 단점: 조회(Search)가 느리다</h4>
<p>&quot;5번째 데이터 내놔&quot;라고 하면?
<code>ArrayList</code>처럼 한 번에 못 갑니다. 첫 번째 노드부터 &quot;다음... 그다음... 그다음...&quot; 하면서 줄을 타고 5번 이동해야 합니다. (보물찾기랑 똑같습니다.)</p>
<ul>
<li><strong>조회:</strong> 데이터가 많을수록 오래 걸립니다. <code>O(n)</code></li>
</ul>
<hr />
<h3 id="3-한-눈에-보는-성능-비교-면접용">3. 한 눈에 보는 성능 비교 (면접용)</h3>
<table>
<thead>
<tr>
<th align="left">기능</th>
<th align="left">ArrayList (배열)</th>
<th align="left">LinkedList (연결)</th>
<th align="left">승자 🏆</th>
</tr>
</thead>
<tbody><tr>
<td align="left"><strong>조회 (get)</strong></td>
<td align="left">O(1)</td>
<td align="left">O(n)</td>
<td align="left"><strong>ArrayList 압승</strong></td>
</tr>
<tr>
<td align="left"><strong>순차 추가 (add)</strong></td>
<td align="left">O(1) (공간 충분 시)</td>
<td align="left">O(1)</td>
<td align="left"><strong>무승부</strong></td>
</tr>
<tr>
<td align="left"><strong>중간 삽입/삭제</strong></td>
<td align="left">O(n) (밀어내기 발생)</td>
<td align="left">O(1) (노드 연결만 변경)</td>
<td align="left"><strong>LinkedList 압승</strong></td>
</tr>
</tbody></table>
<hr />
<h3 id="4-실무에서는-뭘-써야-할까">4. 실무에서는 뭘 써야 할까?</h3>
<p><strong>&quot;99%의 상황에서 그냥 <code>ArrayList</code> 쓰세요.&quot;</strong></p>
<p>이유가 뭘까요?</p>
<ol>
<li><strong>웹 개발은 조회가 많다:</strong> DB에서 데이터를 가져와서 뿌려주는(Read) 작업이 90%입니다.</li>
<li><strong>메모리 효율:</strong> <code>LinkedList</code>는 다음 주소를 저장해야 해서 메모리를 더 많이 먹습니다.</li>
<li><strong>캐시 지역성(Locality):</strong> <code>ArrayList</code>는 데이터가 붙어있어서 CPU 캐시 효과를 잘 받지만, <code>LinkedList</code>는 흩어져 있어서 캐시 효율이 떨어집니다.</li>
</ol>
<blockquote>
<p><strong>💡 그럼 LinkedList는 언제 써요?</strong></p>
<ul>
<li>데이터의 <strong>삽입/삭제가 빈번하게 일어나는 경우</strong> (특히 앞/뒤가 아닌 <strong>중간</strong>에서).</li>
<li>알고리즘 문제 풀 때 큐(Queue)나 데크(Deque)를 구현해야 하는 경우.</li>
</ul>
</blockquote>
<hr />
<h3 id="5-실전-면접-대비-3초-요약">5. 실전 면접 대비: 3초 요약</h3>
<p><strong>Q. ArrayList와 LinkedList의 차이를 설명해주세요.</strong></p>
<blockquote>
<p><strong>A.</strong>
&quot;<strong>데이터 접근 속도</strong>와 <strong>삽입/삭제 효율</strong>의 차이입니다.
<strong>ArrayList</strong>는 인덱스를 기반으로 하여 <strong>데이터 조회(Search)</strong>가 <code>O(1)</code>로 매우 빠르지만, 중간에 데이터를 삽입하거나 삭제할 때는 데이터를 이동시켜야 하므로 <code>O(n)</code>이 걸립니다.
반면 <strong>LinkedList</strong>는 노드 간의 연결로 이루어져 있어 <strong>삽입/삭제</strong>는 참조값만 변경하면 되므로 빠르지만, 특정 데이터를 찾으려면 처음부터 순회해야 하므로 조회가 <code>O(n)</code>으로 느립니다.&quot;</p>
</blockquote>
<hr />
<h3 id="🚀-다음-글-예고">🚀 다음 글 예고</h3>
<p>&quot;배열은 꽉 차면 에러가 나는데, 자바의 Map은 어떻게 데이터를 무한정 넣을까요?&quot;</p>
<p>자바 개발자가 가장 사랑하는 자료구조이자, 면접 질문 난이도 최상위권인 <strong>'해시맵(HashMap)'</strong>.
키(Key)와 값(Value)이 저장되는 마법 같은 원리를 아주 쉽게 뜯어보겠습니다.</p>
<blockquote>
<p><strong>Next Topic:</strong> [자료구조] HashMap의 동작 원리, <code>equals()</code>와 <code>hashCode()</code>의 관계</p>
</blockquote>
<hr />