<p><strong>Part 2. 객체지향과 스프링</strong> 편입니다.</p>
<p>이번 편은 자바의 문법책에서 가장 지루한 '클래스와 객체' 이야기를 <strong>스프링 프레임워크가 돌아가는 원리</strong>와 연결하는 것이 핵심입니다.</p>
<blockquote>
<p><strong>&quot;백엔드 개발자가 알아야 하는 최소한의 자바&quot;</strong> 시리즈의 세 번째 글입니다.</p>
<p>오늘은 자바의 근본, 객체지향(OOP)이 스프링 프레임워크에서 어떻게 활용되는지 알아봅니다.</p>
</blockquote>
<hr />
<h3 id="붕어빵-틀-이야기는-이제-그만합시다">&quot;붕어빵 틀 이야기는 이제 그만합시다.&quot;</h3>
<p>자바를 처음 배울 때 귀에 딱지가 앉도록 듣는 비유가 있습니다.
*&quot;클래스는 붕어빵 틀이고, 객체는 붕어빵이다.&quot;*</p>
<p>틀린 말은 아니지만, 백엔드 개발자에게는 별로 도움이 안 되는 비유입니다. 우리가 실무에서 만드는 <code>MemberService</code>, <code>OrderController</code>는 붕어빵처럼 수천 개씩 찍어내지 않거든요. 보통 <strong>앱 전체에서 딱 하나만 만들어서 돌려 씁니다.</strong></p>
<p>오늘은 교과서적인 OOP 정의 대신, **&quot;스프링은 왜 이렇게 객체지향에 집착할까?&quot;**에 대한 답을 찾아보겠습니다.</p>
<br />

<h2 id="1-클래스-vs-객체-vs-스프링-빈bean">1. 클래스 vs 객체 vs 스프링 빈(Bean)</h2>
<p>실무에서 가장 많이 다루는 객체는 여러분이 직접 <code>new</code> 하지 않습니다. 스프링이 대신 해줍니다.</p>
<h3 id="🌱-스프링-빈은-싱글톤-객체다">🌱 스프링 빈은 '싱글톤 객체'다</h3>
<p>스프링 부트 애플리케이션을 띄우면, 스프링은 <code>@Controller</code>, <code>@Service</code>, <code>@Repository</code>가 붙은 클래스를 찾아서 **딱 1개의 객체(Instance)**만 생성합니다. 그리고 이를 **스프링 빈(Bean)**이라고 부릅니다.</p>
<ul>
<li><strong>왜 하나만 만들까요?</strong><ul>
<li>요청이 올 때마다 <code>new OrderService()</code>를 한다면? 1초에 1만 명이 접속하면 객체가 1만 개 생겼다가 사라집니다. (메모리 낭비, GC 과부하)</li>
<li>그래서 <strong>무상태(Stateless)</strong> 객체 하나를 만들어 놓고 여러 스레드가 공유해서 쓰는 방식을 택했습니다.</li>
</ul>
</li>
</ul>
<blockquote>
<p><strong>⚠️ 지난 편 복습:</strong>
스프링 빈은 모든 스레드가 공유합니다(Heap 영역). 따라서 <strong>빈 내부에는 절대 상태 값(멤버 변수)을 저장하면 안 됩니다.</strong> 오직 다른 빈을 참조하거나, 상수(<code>final</code>)만 가져야 합니다.</p>
</blockquote>
<p><img alt="" src="https://velog.velcdn.com/images/hello22433/post/fc2c3341-1c82-4fac-a4ac-287cd5a0f98d/image.png" /></p>
<br />

<h2 id="2-인터페이스-껍데기는-왜-만들까">2. 인터페이스: 껍데기는 왜 만들까?</h2>
<p>SI 프로젝트나 옛날 코드들을 보면 이런 구조가 많습니다.</p>
<ul>
<li><code>MemberService</code> (인터페이스)</li>
<li><code>MemberServiceImpl</code> (구현체)</li>
</ul>
<p>구현체가 하나뿐인데 굳이 인터페이스를 만드는 관행, **&quot;이거 오버 엔지니어링 아닌가요?&quot;**라는 질문이 0순위로 나옵니다.</p>
<h3 id="🔑-인터페이스의-진짜-목적-갈아끼우기-ocp">🔑 인터페이스의 진짜 목적: '갈아끼우기' (OCP)</h3>
<p>인터페이스는 **역할(Role)**과 **구현(Implementation)**을 분리하는 도구입니다.</p>
<p>상황을 가정해 봅시다. 우리 회사가 결제 모듈을 <strong>카카오페이</strong>에서 <strong>네이버페이</strong>로 바꾼다고 합니다.</p>
<p><strong>[나쁜 코드: 강한 결합]</strong></p>
<pre><code class="language-java">public class PaymentService {
    // KakaoPay를 직접 의존. 바꾸려면 코드를 뜯어고쳐야 함.
    private KakaoPay kakaoPay; 

    public void pay() {
        kakaoPay.approve();
    }
}</code></pre>
<p><strong>[좋은 코드: 느슨한 결합]</strong></p>
<pre><code class="language-java">// 1. 역할 정의 (인터페이스)
public interface PaymentModule {
    void approve();
}

// 2. 클라이언트 코드
@Service
@RequiredArgsConstructor
public class PaymentService {
    // 3. 구체적인 건 모름. 그냥 '결제 기능'이 있는 애면 됨.
    private final PaymentModule paymentModule; 

    public void pay() {
        paymentModule.approve();
    }
}</code></pre>
<p>이제 카카오페이에서 네이버페이로 바꿀 때, <code>PaymentService</code> 코드는 <strong>단 한 줄도 수정할 필요가 없습니다.</strong> 그저 주입해 주는 설정(Configuration)만 바꾸면 됩니다.</p>
<p>이것이 객체지향의 꽃, **OCP (Open-Closed Principle, 개방-폐쇄 원칙)**이자 **전략 패턴(Strategy Pattern)**입니다.</p>
<br />

<h2 id="3-setter-좀-그만-쓰세요-캡슐화">3. Setter 좀 그만 쓰세요 (캡슐화)</h2>
<p>주니어 개발자의 코드에서 가장 많이 보이는 안티 패턴(Anti-Pattern)은 <strong>무지성 <code>@Data</code>와 <code>Setter</code> 남발</strong>입니다.</p>
<pre><code class="language-java">@Entity
@Data // ❌ 모든 필드에 Getter/Setter가 다 열림
public class Member {
    private Long id;
    private String name;
    private String status;
}

// 서비스 코드
member.setName(&quot;김철수&quot;);
member.setStatus(&quot;STOP&quot;); // ❌ 도대체 어디서 누가 바꿨는지 추적 불가</code></pre>
<h3 id="🔒-캡슐화-객체를-객체답게">🔒 캡슐화: 객체를 객체답게</h3>
<p>객체지향의 핵심은 데이터를 보호하는 것입니다. <code>Setter</code>를 열어두는 건 집 현관문을 떼어버리는 것과 같습니다. 대신 <strong>목적이 명확한 메소드</strong>를 만드세요.</p>
<pre><code class="language-java">@Getter // 조회는 열어두되
// @Setter (X) 데이터 변경은 닫음
public class Member {
    private String name;
    private String status;

    // ✅ 변경 의도가 명확한 메소드 제공
    public void rename(String newName) {
        if (newName == null) throw new IllegalArgumentException();
        this.name = newName;
    }

    public void ban() {
        this.status = &quot;STOP&quot;;
    }
}</code></pre>
<p>이렇게 하면 <code>member.ban()</code> 코드만 봐도 **&quot;아, 회원을 정지시키는구나&quot;**라고 알 수 있습니다. 이것이 진정한 객체지향적 설계입니다.</p>
<br />

<h2 id="4-상속inheritance보다는-조합composition">4. 상속(Inheritance)보다는 조합(Composition)</h2>
<p>자바 전공 서적에서는 <code>extends</code> 키워드를 이용한 상속을 엄청 강조합니다. 하지만 <strong>최신 백엔드 트렌드에서 상속은 신중하게 써야 합니다.</strong></p>
<h3 id="🚫-상속의-문제점">🚫 상속의 문제점</h3>
<ol>
<li>부모 클래스를 수정하면 자식 클래스들이 줄줄이 깨질 수 있습니다.</li>
<li>부모의 불필요한 기능까지 다 물려받습니다.</li>
<li>자바는 다중 상속이 안 됩니다.</li>
</ol>
<h3 id="✅-조합composition을-쓰자">✅ 조합(Composition)을 쓰자</h3>
<p>상속 대신 **필드로 다른 객체를 가지고 있는 방식(조합)**을 선호합니다. 스프링의 DI(의존성 주입)가 바로 이 '조합'을 극대화한 기술입니다.</p>
<p>*&quot;Controller가 Service를 상속받나요?&quot;* 아니죠.
*&quot;Controller가 Service를 필드로 가지고(조합) 있죠.&quot;*</p>
<p>기능이 필요하면 물려받지 말고, <strong>그 기능을 가진 객체를 주입받아 사용하세요.</strong></p>
<br />

<h2 id="5-마치며-스프링을-잘-하려면">5. 마치며: 스프링을 잘 하려면</h2>
<p>스프링 프레임워크는 **&quot;개발자가 객체지향 원칙을 잘 지키도록 강제하는 도구&quot;**에 가깝습니다.</p>
<ol>
<li><strong>싱글톤(Bean):</strong> 메모리 효율을 위해 객체를 하나만 쓴다. (상태 관리 주의)</li>
<li><strong>DI(의존성 주입):</strong> 인터페이스를 통해 결합도를 낮춘다. (유지보수성 UP)</li>
<li><strong>캡슐화:</strong> DTO나 Entity에는 Setter를 막고 비즈니스 메소드를 쓴다.</li>
</ol>
<p>이 원칙들만 지켜도 여러분의 코드는 훨씬 더 '스프링스럽고', 유지보수하기 좋은 코드가 될 것입니다.</p>
<p>다음 시간에는 자바 코드를 획기적으로 줄여주는 모던 자바의 핵심, **[Part 3] 람다(Lambda), 스트림(Stream), 그리고 옵셔널(Optional)**로 찾아오겠습니다.</p>
<blockquote>
<p><strong>Next:</strong> [Part 3] if문과 for문을 걷어내는 마법, 모던 자바</p>
</blockquote>