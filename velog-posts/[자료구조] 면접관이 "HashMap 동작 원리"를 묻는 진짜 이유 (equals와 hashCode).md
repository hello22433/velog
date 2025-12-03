<p>자바 개발자 면접의 '최종 보스'이자 자료구조의 꽃, <strong>HashMap</strong> 편입니다.</p>
<p>이 주제는 단순히 자료구조를 아는지 묻는 게 아니라, 자바의 객체 지향 원리(<code>equals</code>, <code>hashCode</code>)까지 깊이 있게 파악하고 있는지 확인하는 <strong>종합 선물 세트</strong> 같은 질문입니다.</p>
<p>면접관의 눈을 번쩍 뜨이게 할 <strong>'Java 8 이후의 최적화(Red-Black Tree)'</strong> 내용까지 한 스푼 더했습니다. </p>
<hr />
<h2 id="자료구조-면접관이-hashmap-동작-원리를-묻는-진짜-이유-equals와-hashcode">[자료구조] 면접관이 &quot;HashMap 동작 원리&quot;를 묻는 진짜 이유 (equals와 hashCode)</h2>
<h3 id="key로-검색하면-value가-바로-나온다-마법일까요">&quot;Key로 검색하면 Value가 바로 나온다. 마법일까요?&quot;</h3>
<pre><code class="language-java">map.put(&quot;이름&quot;, &quot;김철수&quot;);
String name = map.get(&quot;이름&quot;); // 결과: &quot;김철수&quot;</code></pre>
<p>우리는 <code>HashMap</code>을 숨 쉬듯이 사용합니다.
데이터가 100만 개가 있어도, 키(Key)만 알면 반복문 없이 <strong>단 한 번(O(1))</strong> 만에 데이터를 찾아냅니다.</p>
<p>어떻게 이게 가능할까요? 도대체 내부에서 무슨 짓을 하고 있길래 배열처럼 인덱스도 없는데 데이터를 바로 찾는 걸까요?
오늘은 자바 개발자가 반드시 넘어야 할 산, <strong>해싱(Hashing)과 해시 충돌(Collision)</strong>의 세계를 뜯어봅니다.</p>
<hr />
<h3 id="1-해시-함수-데이터를-숫자로-바꾸는-마법">1. 해시 함수: 데이터를 숫자로 바꾸는 마법</h3>
<p>HashMap의 핵심은 <strong>&quot;Key를 배열의 인덱스(숫자)로 바꾸는 것&quot;</strong>입니다.
이 변환 작업을 해주는 녀석을 <strong>해시 함수(Hash Function)</strong>라고 합니다.</p>
<ol>
<li>사용자가 <code>map.put(&quot;Apple&quot;, 1000)</code>을 입력합니다.</li>
<li>해시 함수가 &quot;Apple&quot;이라는 글자를 특정한 공식에 넣어 숫자(해시코드)로 바꿉니다. (예: &quot;Apple&quot; → <code>35</code>)</li>
<li>내부 배열의 <code>35</code>번 칸에 <code>1000</code>이라는 값을 저장합니다.</li>
</ol>
<p>나중에 <code>get(&quot;Apple&quot;)</code>을 호출하면? 똑같이 &quot;Apple&quot;을 숫자로 바꿔서 <code>35</code>번 칸으로 바로 찾아갑니다. <strong>반복문을 돌 필요가 없죠.</strong></p>
<hr />
<h3 id="2-해시-충돌hash-collision-같은-방을-쓰라고요">2. 해시 충돌(Hash Collision): &quot;같은 방을 쓰라고요?&quot;</h3>
<p>그런데 문제가 있습니다.
만약 서로 다른 키인 &quot;Apple&quot;과 &quot;Banana&quot;가 우연히 <strong>똑같은 숫자(해시코드)</strong>로 변환된다면 어떻게 될까요?
이것을 <strong>해시 충돌(Hash Collision)</strong>이라고 합니다.</p>
<p>자바의 HashMap은 이 문제를 <strong>'체이닝(Chaining)'</strong> 기법으로 해결합니다.</p>
<h4 id="🔗-체이닝chaining이란">🔗 체이닝(Chaining)이란?</h4>
<p>충돌이 나면, 그 칸에 데이터를 덮어쓰는 게 아니라 <strong>LinkedList(연결 리스트)를 만들어 줄줄이 매달아 놓는 방식</strong>입니다.</p>
<ul>
<li><strong>충돌 발생 시:</strong> 하나의 버킷(Bucket) 안에 데이터가 주렁주렁 매달립니다.</li>
<li><strong>검색 시:</strong> 일단 해시코드로 방을 찾고, 그 안에서 LinkedList를 뒤져서 진짜 내 데이터를 찾습니다.</li>
</ul>
<blockquote>
<p><strong>💡 면접 가산점 포인트 (Java 8+)</strong>
&quot;자바 8부터는 하나의 버킷에 데이터가 너무 많이(8개 이상) 쌓이면, LinkedList 대신 <strong>Red-Black Tree(레드-블랙 트리)</strong>로 자료구조를 바꿉니다. 덕분에 충돌이 많이 나도 검색 속도가 <code>O(n)</code>에서 <code>O(log n)</code>으로 획기적으로 빨라집니다.&quot;</p>
</blockquote>
<hr />
<h3 id="3-면접-단골-질문-equals와-hashcode">3. 면접 단골 질문: equals()와 hashCode()</h3>
<p>이게 왜 HashMap이랑 관련이 있을까요?
HashMap이 데이터를 찾는 과정(메커니즘)을 보면 답이 나옵니다.</p>
<p><strong>[데이터 찾는 2단계]</strong></p>
<ol>
<li><strong><code>hashCode()</code> 실행:</strong> &quot;어느 방(Bucket)에 있는지 찾자!&quot; (대략적인 위치 탐색)</li>
<li><strong><code>equals()</code> 실행:</strong> (그 방에 들어가서) &quot;여기 있는 애들 중에 진짜 내 키랑 똑같은 애가 누구지?&quot; (정확한 값 비교)</li>
</ol>
<h4 id="⚠️-만약-둘-중-하나만-재정의override한다면">⚠️ 만약 둘 중 하나만 재정의(Override)한다면?</h4>
<ul>
<li><strong><code>hashCode()</code>만 재정의하면:</strong><ul>
<li>같은 방까지는 찾아갑니다. 근데 <code>equals()</code>가 제대로 안 되어 있어서, 눈앞에 데이터를 두고도 &quot;어? 이거 내 거 아닌데요?&quot; 하고 <code>null</code>을 반환합니다.</li>
</ul>
</li>
<li><strong><code>equals()</code>만 재정의하면:</strong><ul>
<li>논리적으로 같은 객체라도 해시코드가 달라서, 아예 엉뚱한 방(Bucket)을 뒤지다가 &quot;없는데요?&quot; 하고 <code>null</code>을 반환합니다.</li>
</ul>
</li>
</ul>
<blockquote>
<p><strong>결론:</strong> HashMap의 키(Key)로 사용하는 객체는 <strong>반드시 <code>equals()</code>와 <code>hashCode()</code>를 둘 다, 그리고 똑같은 기준(필드)으로 재정의해야 합니다.</strong> (Lombok의 <code>@EqualsAndHashCode</code>가 이걸 해주는 겁니다.)</p>
</blockquote>
<hr />
<h3 id="4-실전-면접-대비-3초-요약">4. 실전 면접 대비: 3초 요약</h3>
<p><strong>Q. HashMap의 동작 원리와 충돌 해결 방법을 설명해주세요.</strong></p>
<blockquote>
<p><strong>A.</strong>
&quot;HashMap은 Key의 <code>hashCode</code>를 계산하여 배열의 인덱스를 구하고 해당 버킷에 값을 저장합니다. 만약 해시 충돌이 발생하면 자바에서는 <strong>Chaining(체이닝)</strong> 방식을 사용하여, 해당 버킷에 <strong>LinkedList</strong>로 데이터를 연결합니다. (Java 8부터는 데이터가 많아지면 <strong>Red-Black Tree</strong>로 변환하여 성능을 최적화합니다.)&quot;</p>
</blockquote>
<p><strong>Q. 왜 equals()와 hashCode()를 같이 재정의해야 하나요?</strong></p>
<blockquote>
<p><strong>A.</strong>
&quot;HashMap 같은 해시 기반 컬렉션은 객체를 찾을 때 <strong><code>hashCode()</code>로 버킷을 찾고, 그 안에서 <code>equals()</code>로 실제 객체를 비교</strong>하기 때문입니다. 둘 중 하나라도 재정의하지 않으면 같은 객체라도 데이터를 찾지 못하거나 엉뚱한 동작을 하게 됩니다.&quot;</p>
</blockquote>
<hr />
<h3 id="🚀-part-4-그리고-시리즈를-마치며">🚀 Part 4 그리고 시리즈를 마치며</h3>
<p><strong>ArrayList, LinkedList, 그리고 HashMap.</strong>
이 3대장만 확실히 이해해도 실무 비즈니스 로직을 짜거나, 기본적인 코딩 테스트를 푸는 데에는 큰 무리가 없습니다.</p>
<p>지금까지 우리는 <strong>자바 기초(Part 1)부터 네트워크(Part 2), 데이터베이스(Part 3), 자료구조(Part 4)</strong>까지 숨 가쁘게 달려왔습니다.</p>
<p>이 시리즈가 &quot;비전공자라 불안해요&quot;라고 말하던 여러분에게,
<strong>&quot;이제 나도 기본은 되어 있어!&quot;</strong>라는 자신감이 되었기를 진심으로 바랍니다.</p>
<p>여러분의 독학은 결코 외롭지 않습니다.
지금까지, <strong>실전 압축 CS</strong>였습니다.</p>
<blockquote>
<p><strong>(완결) 독학러 여러분의 합격을 응원합니다!</strong>
<em>혹시 더 다루고 싶은 주제(Spring, 알고리즘 등)가 있다면 댓글로 남겨주세요!</em></p>
</blockquote>