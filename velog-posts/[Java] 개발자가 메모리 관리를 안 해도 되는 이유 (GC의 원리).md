<h3 id="c언어-개발자는-직접-치우고-자바-개발자는-청소부를-고용합니다">&quot;C언어 개발자는 직접 치우고, 자바 개발자는 청소부를 고용합니다.&quot;</h3>
<p>C나 C++ 같은 언어는 개발자가 메모리를 할당(malloc)하고, 다 쓰면 직접 해제(free)해야 합니다. 만약 까먹고 해제를 안 한다? 메모리에 쓰레기가 계속 쌓이다가 결국 <strong>메모리 누수(Memory Leak)</strong>로 프로그램이 사망합니다.</p>
<p>하지만 자바 개발자인 우리는 free() 같은 코드를 짜본 적이 없습니다. <strong>JVM(자바 가상 머신) 안에 아주 부지런한 청소부, Garbage Collector(GC)</strong>가 살고 있기 때문이죠.</p>
<p>오늘은 이 청소부가 <strong>'어떤 기준으로 쓰레기를 판단하고(Reachability)', '어떻게 청소하는지(Mark and Sweep)'</strong> 알아보겠습니다.</p>
<hr />
<h2 id="1-쓰레기의-기준-연줄이-끊기면-죽는다-reachability">1. 쓰레기의 기준: &quot;연줄이 끊기면 죽는다&quot; (Reachability)</h2>
<p>GC는 힙(Heap) 영역에 있는 객체 중 <strong>'더 이상 필요 없는 객체'</strong>를 찾아 지웁니다. 그렇다면 &quot;필요 없다&quot;는 건 어떻게 판단할까요?</p>
<p><strong>정답: &quot;스택(Stack)이나 메서드 영역(Method Area)에서 더 이상 참조하지 않는 객체&quot;</strong></p>
<ul>
<li><p><strong>Reachable:</strong> 스택의 변수가 힙의 객체를 가리키고(참조하고) 있음. → <strong>&quot;살려둠&quot;</strong></p>
</li>
<li><p><strong>Unreachable:</strong> 참조하던 변수가 사라지거나 null이 되어서, 힙에 덩그러니 남은 객체. → <strong>&quot;쓰레기(Garbage)로 간주&quot;</strong></p>
</li>
</ul>
<p>쉽게 말해, <strong>&quot;리모컨(변수) 잃어버린 TV(객체)는 버린다&quot;</strong>는 원칙입니다.</p>
<p><img alt="" src="https://velog.velcdn.com/images/hello22433/post/3306c483-9b21-4986-a929-e825c99abc7e/image.png" /></p>
<hr />
<h2 id="2-청소하는-방법-mark-and-sweep">2. 청소하는 방법: Mark and Sweep</h2>
<p>GC가 청소하는 기본 알고리즘은 <strong>'마크 앤 스윕(Mark and Sweep)'</strong>입니다. 이름 그대로 &quot;표시하고 쓸어담기&quot;입니다.</p>
<ol>
<li><p><strong>Mark (표시):</strong> 스택의 모든 변수를 뒤져서, 현재 참조되고 있는(살아있는) 객체들에 <strong>&quot;생존&quot;</strong> 마크를 찍습니다.</p>
</li>
<li><p><strong>Sweep (쓸기):</strong> 마크가 없는(연결이 끊긴) 객체들을 힙에서 싹 지워버립니다.</p>
</li>
</ol>
<hr />
<h2 id="3-효율적인-청소-구역-나누기-young--old-generation">3. 효율적인 청소 구역 나누기 (Young &amp; Old Generation)</h2>
<p>&quot;매번 힙 전체를 다 뒤지면 너무 느리지 않을까요?&quot; 맞습니다. 그래서 JVM은 힙 메모리를 효율적으로 관리하기 위해 구역을 나눴습니다.</p>
<blockquote>
<p><strong>💡 전제 조건 (Weak Generational Hypothesis)</strong> 
&quot;대부분의 객체는 생성되자마자 금방 쓰레기가 된다.&quot; (예: for문 안에서 잠깐 쓴 변수, 일회용 DTO 등)</p>
</blockquote>
<p>이 전제를 바탕으로 힙을 두 구역으로 나눕니다.</p>
<p><strong>1) Young 영역 (Eden + Survivor)</strong></p>
<ul>
<li><strong>특징:</strong> 갓 태어난 객체들이 모이는 곳입니다.</li>
<li><strong>동작:</strong> 대부분의 객체는 여기서 금방 죽습니다. 여기서 일어나는 청소를 <strong>Minor GC</strong>라고 하며, 속도가 매우 빠릅니다.</li>
</ul>
<p><strong>2) Old 영역</strong></p>
<ul>
<li><p><strong>특징:</strong> Young 영역에서 오랫동안 살아남은(여러 번의 GC를 버틴) '베테랑' 객체들이 이동(Promotion)하는 곳입니다.</p>
</li>
<li><p><strong>동작:</strong> 여기가 꽉 차면 <strong>Major GC (Full GC)</strong>가 일어납니다. 구역이 넓은 만큼 청소 시간이 오래 걸립니다.</p>
</li>
</ul>
<p><img alt="" src="https://velog.velcdn.com/images/hello22433/post/77c34db8-9e48-49ff-a72f-bc2e94dd9564/image.png" /></p>
<hr />
<h2 id="4-면접관이-노리는-키워드-stop-the-world">4. 면접관이 노리는 키워드: Stop-the-world</h2>
<p>GC는 훌륭하지만 치명적인 단점이 하나 있습니다. 바로 청소하는 동안 <strong>모든 애플리케이션을 멈춘다</strong>는 것입니다. 이를 <strong>'Stop-the-world(STW)'</strong>라고 부릅니다.</p>
<ul>
<li><p><strong>상황:</strong> &quot;잠깐 다들 동작 그만! 나 청소 좀 하고!&quot;</p>
</li>
<li><p><strong>문제:</strong> Young 영역 청소(Minor GC)는 찰나라서 못 느끼지만, <strong>Old 영역 청소(Major GC)가 터지면 서버가 몇 초간 멈출 수 있습니다.</strong></p>
</li>
<li><p><strong>튜닝:</strong> 그래서 시니어 개발자들이 하는 'GC 튜닝'이란, 결국 이 Stop-the-world 시간을 줄이는 것을 의미합니다.</p>
</li>
</ul>
<hr />
<h2 id="5-실전-면접-대비-3초-요약">5. 실전 면접 대비: 3초 요약</h2>
<h3 id="q-가비지-컬렉션gc의-동작-원리에-대해-설명해주세요">Q. 가비지 컬렉션(GC)의 동작 원리에 대해 설명해주세요.</h3>
<p><strong>A.</strong> &quot;GC는 힙 영역에서 참조되지 않는(Unreachable) 객체를 찾아 메모리를 해제하는 과정입니다. 기본적으로 <strong>Mark and Sweep</strong> 알고리즘을 사용하며, 효율성을 위해 메모리를 <strong>Young</strong>과 <strong>Old</strong> 영역으로 나누어 관리합니다.&quot;</p>
<h3 id="q-stop-the-world가-무엇인가요">Q. Stop-the-world가 무엇인가요?</h3>
<p><strong>A.</strong> &quot;GC가 실행될 때 메모리를 정리하기 위해 <strong>JVM이 애플리케이션 실행을 잠시 멈추는 현상</strong>입니다. 특히 Old 영역의 GC(Major GC)가 발생할 때 멈추는 시간이 길어질 수 있어, 이를 줄이는 것이 성능 최적화의 핵심입니다.&quot;</p>
<hr />
<h2 id="🚀-마치며">🚀 마치며</h2>
<p>우리는 GC 덕분에 Null 처리와 비즈니스 로직에만 집중할 수 있게 되었습니다. 하지만 &quot;알아서 해주니까 몰라도 돼&quot;라고 생각하면, 나중에 대용량 트래픽을 처리할 때 서버가 뚝뚝 끊기는 현상을 해결할 수 없습니다.</p>
<p>다음 시간에는 자바 백엔드 개발의 또 다른 핵심, <strong>'프로세스와 스레드'</strong>에 대해 알아보겠습니다. <strong>&quot;스프링은 요청이 들어올 때마다 스레드를 만든다&quot;</strong>는 말의 진짜 의미를 파헤쳐 봅시다.</p>
<h3 id="⏭-next-topic">⏭ Next Topic:</h3>
<p>[OS] 프로세스(Process)와 스레드(Thread), 크롬 탭이 여러 개인 이유?</p>