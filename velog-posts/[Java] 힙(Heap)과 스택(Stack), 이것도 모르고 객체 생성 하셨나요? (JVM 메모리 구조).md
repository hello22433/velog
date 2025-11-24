<h2 id="코드-한-줄-짤-때마다-메모리-지도가-그려지시나요">&quot;코드 한 줄 짤 때마다 메모리 지도가 그려지시나요?&quot;</h2>
<p>우리가 자바에서 무심코 작성하는 </p>
<pre><code class="language-java">int a = 10;
User user = new User();</code></pre>
<p>겉보기엔 비슷해 보이지만, 컴퓨터 내부에서는 완전히 다른 세상에서 살고 있는 녀석들입니다.</p>
<p>이 둘이 <strong>메모리의 '어디'에 저장되는지</strong> 모르면, 나중에 이런 에러를 만났을 때 멍하니 모니터만 바라보게 됩니다.</p>
<ul>
<li>StackOverflowError (재귀함수 돌리다 터짐)</li>
<li>OutOfMemoryError (서버가 뻗어버림)</li>
<li>NullPointerException (가장 흔한 그 녀석)</li>
</ul>
<p>오늘은 백엔드 개발자가 꼭 알아야 할 JVM의 3대장, Method, Stack, Heap 영역을 아주 쉽게 털어보겠습니다.</p>
<hr />
<h2 id="1-jvm-메모리-3분할-요약-the-big-picture">1. JVM 메모리 3분할 요약 (The Big Picture)</h2>
<p>JVM(자바 가상 머신)의 메모리 구조는 복잡하지만, 실무에서는 딱 <strong>3가지</strong>만 기억하면 됩니다.</p>
<ol>
<li><p><strong>Method Area (메서드 영역):</strong> 붕어빵 틀 (설계도)</p>
<ul>
<li>클래스(class) 정보, static 변수가 저장되는 곳. 프로그램 시작부터 끝까지 살아있습니다.</li>
</ul>
</li>
<li><p><strong>Stack Area (스택 영역):</strong> 잠깐 쓰는 작업실</p>
<ul>
<li>메서드가 호출될 때마다 생기는 임시 공간. 변수들이 잠깐 머물다 사라집니다.</li>
</ul>
</li>
<li><p><strong>Heap Area (힙 영역):</strong> 거대한 창고</p>
<ul>
<li>new 키워드로 생성된 <strong>객체(Object)</strong>들이 사는 곳. 우리가 관리해야 할 핵심 구역입니다.</li>
</ul>
</li>
</ol>
<hr />
<h2 id="2-stack-vs-heap-여기가-핵심입니다">2. Stack vs Heap: 여기가 핵심입니다</h2>
<p>면접관이 &quot;스택과 힙의 차이가 뭐죠?&quot;라고 묻는다면, 아래 내용을 머릿속에 그리고 대답하면 됩니다.</p>
<p>📍 <strong>Stack (스택)</strong></p>
<ul>
<li><p><strong>특징: LIFO(Last In First Out)</strong> 구조입니다. 메서드가 호출되면 프레임이 쌓이고, 끝나면 바로 사라집니다.</p>
</li>
<li><p><strong>누가 사나요?</strong></p>
<ul>
<li><strong>기본형 변수(Primitive Type):</strong> int, double, boolean 등 실제 값(10, 3.14, true)이 여기에 바로 저장됩니다.</li>
<li><strong>참조형 변수(Reference Type)의 '주소값':</strong> 객체를 가리키는 리모컨만 저장됩니다.</li>
</ul>
</li>
</ul>
<p>📍 <strong>Heap (힙)</strong></p>
<ul>
<li><p><strong>특징:</strong> 자유롭게 데이터를 저장하는 거대한 공간입니다. Garbage Collector(GC) 라는 청소부가 주기적으로 안 쓰는 데이터를 지워줍니다.</p>
</li>
<li><p><strong>누가 사나요?</strong></p>
<ul>
<li><strong>참조형(Reference Type) 데이터:</strong> new 명령어로 만든 모든 객체, 배열(Array), String 등이 여기에 저장됩니다.</li>
</ul>
</li>
</ul>
<p><img alt="" src="https://velog.velcdn.com/images/hello22433/post/c483064c-6f50-449c-a8c6-74a358c7694f/image.png" /></p>
<hr />
<h2 id="3-코드로-보는-메모리-이동">3. 코드로 보는 메모리 이동</h2>
<p>백문이 불여일견, 코드를 보면서 메모리에 어떻게 쌓이는지 추적해 봅시다.</p>
<pre><code class="language-java">public class Main {
    public static void main(String[] args) {
        int age = 25;                 // 1. Stack에 저장
        String name = &quot;BugStar&quot;;      // 2. Stack엔 주소, Heap엔 &quot;BugStar&quot; 객체
        User user = new User();       // 3. Stack엔 user(리모컨), Heap엔 User 객체
    }
}</code></pre>
<p><strong>이 코드가 실행될 때 일어나는 일:</strong></p>
<ol>
<li><p><strong>int age = 25;</strong></p>
<ul>
<li><strong>Stack</strong>에 age라는 상자가 생기고, 그 안에 25라는 값이 <strong>직접</strong> 들어갑니다.</li>
</ul>
</li>
<li><p><strong>User user = new User();</strong></p>
<ul>
<li><strong>Heap</strong>에 거대한 User 객체가 생성됩니다. (예: 주소번지 100번지)</li>
<li><strong>Stack</strong>에는 user라는 변수가 생기는데, 여기엔 객체 자체가 아니라 <strong>주소값(100번지)</strong>이 들어갑니다.</li>
</ul>
</li>
</ol>
<p>💡 핵심 포인트: 스택에 있는 user 변수는 힙에 있는 실제 객체를 가리키는 <strong>'리모컨'</strong>일 뿐입니다!</p>
<hr />
<h2 id="4-실전-면접-대비-3초-요약">4. 실전 면접 대비: 3초 요약</h2>
<p>기술 면접에서 자주 나오는 질문에 대한 모범 답안입니다.</p>
<h3 id="q-자바의-메모리-구조-중-stack과-heap의-차이를-설명해주세요">Q. 자바의 메모리 구조 중 Stack과 Heap의 차이를 설명해주세요.</h3>
<p><strong>A. &quot;Stack</strong>은 메서드 호출 시 지역 변수나 매개변수가 저장되는 임시 공간으로, 메서드가 종료되면 데이터가 즉시 소멸됩니다. 반면, <strong>Heap</strong>은 new 연산자로 생성된 객체가 저장되는 공간이며, 개발자가 직접 지우지 않고 <strong>Garbage Collector(GC)</strong>에 의해 관리됩니다.&quot;</p>
<h3 id="q-그럼-stackoverflowerror는-언제-발생하나요">Q. 그럼 StackOverflowError는 언제 발생하나요?</h3>
<p><strong>A.</strong> &quot;주로 재귀 함수가 종료 조건 없이 무한 호출되어, Stack 메모리 영역이 꽉 찼을 때 발생합니다.&quot;</p>
<hr />
<h2 id="🚀-다음-글-예고">🚀 다음 글 예고</h2>
<h3 id="q-heap-영역에-객체가-계속-쌓이기만-하면-메모리가-터지지-않을까요">Q. &quot;Heap 영역에 객체가 계속 쌓이기만 하면 메모리가 터지지 않을까요?&quot;</h3>
<p><strong>A.</strong> 맞습니다. 그래서 자바에는 아주 부지런한 청소부, <strong>GC(Garbage Collector)</strong>가 존재합니다. 다음 시간에는 개발자가 몰라도 알아서 메모리를 정리해 주는 <strong>GC의 동작 원리</strong>에 대해 아주 쉽게 알아보겠습니다.</p>
<h3 id="⏭-next-topic">⏭ Next Topic:</h3>
<p>[Java] 개발자가 메모리 관리를 안 해도 되는 이유 (GC의 원리)</p>