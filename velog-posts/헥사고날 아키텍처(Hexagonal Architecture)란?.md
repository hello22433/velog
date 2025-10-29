<h1 id="헥사고날-아키텍처hexagonal-architecture-쉽게-이해하기">헥사고날 아키텍처(Hexagonal Architecture) 쉽게 이해하기</h1>
<hr />
<h2 id="1-헥사고날-아키텍처란">1. 헥사고날 아키텍처란?</h2>
<p><strong>핵심 아이디어:</strong></p>
<blockquote>
<p>“핵심 비즈니스 로직은 외부 환경에 영향을 받지 않는다.”</p>
</blockquote>
<p>즉, 앱 안에서 중요한 <strong>업무 규칙(Core)</strong>만 중심에 두고,
외부 환경(DB, 웹, API 등)과의 연결은 <strong>포트(Port)와 어댑터(Adapter)</strong>를 통해서만 하도록 구조를 잡는 방법입니다.</p>
<hr />
<h2 id="2-아주-쉬운-비유">2. 아주 쉬운 비유</h2>
<h3 id="예시-우체국-소포-처리-시스템">예시: <strong>우체국 소포 처리 시스템</strong></h3>
<ul>
<li><strong>Core(핵심 로직)</strong>: 소포를 <strong>분류하고 처리하는 규칙</strong></li>
<li><strong>Port(인터페이스)</strong>: 소포를 주고받는 <strong>접점</strong></li>
<li><strong>Adapter(구현체)</strong>: 실제 <strong>택배기사, 드론, 우편함</strong> 등 전달 수단</li>
</ul>
<pre><code>Core: 소포 처리 규칙
Port: &quot;소포를 보내고 받는 방법&quot;이라는 인터페이스
Adapter: 택배기사, 드론, 우편함 실제 구현</code></pre><ul>
<li>규칙(Core)은 바뀌지 않음</li>
<li>택배기사 → 드론 → 우편함 등 전달 방법을 바꾸어도 규칙은 그대로</li>
</ul>
<hr />
<h2 id="3-헥사고날-아키텍처-특징">3. 헥사고날 아키텍처 특징</h2>
<table>
<thead>
<tr>
<th>항목</th>
<th>특징</th>
</tr>
</thead>
<tbody><tr>
<td>Core</td>
<td>핵심 업무 로직, 외부 의존 없음</td>
</tr>
<tr>
<td>Port</td>
<td>Core ↔ 외부 연결 인터페이스</td>
</tr>
<tr>
<td>Adapter</td>
<td>실제 외부 구현체(DB, 웹, 메시지 큐 등)</td>
</tr>
<tr>
<td>장점</td>
<td>기술 변경에 유연, 테스트 쉬움, 확장성 좋음</td>
</tr>
<tr>
<td>단점</td>
<td>구조 복잡, 작은 프로젝트에는 과도</td>
</tr>
</tbody></table>
<hr />
<h2 id="4-java-예시-소포-배송-서비스">4. Java 예시: 소포 배송 서비스</h2>
<h3 id="4-1-domain-객체">4-1. Domain 객체</h3>
<pre><code class="language-java">// core/Package.java
package core;

public class Package {
    private String id;
    private String destination;

    public Package(String id, String destination) {
        this.id = id;
        this.destination = destination;
    }

    public String getId() { return id; }
    public String getDestination() { return destination; }

    @Override
    public String toString() {
        return &quot;Package{id='&quot; + id + &quot;', destination='&quot; + destination + &quot;'}&quot;;
    }
}</code></pre>
<hr />
<h3 id="4-2-port-인터페이스-정의">4-2. Port (인터페이스 정의)</h3>
<pre><code class="language-java">// core/DeliveryService.java
package core;

public interface DeliveryService {
    void deliver(Package pkg);
}</code></pre>
<hr />
<h3 id="4-3-core-logic-핵심-업무">4-3. Core Logic (핵심 업무)</h3>
<pre><code class="language-java">// core/PackageProcessor.java
package core;

public class PackageProcessor {
    private final DeliveryService deliveryService; // Port

    public PackageProcessor(DeliveryService deliveryService) {
        this.deliveryService = deliveryService;
    }

    public void process(Package pkg) {
        // 핵심 규칙: 목적지 확인 후 배송
        System.out.println(&quot;Processing package to &quot; + pkg.getDestination());
        deliveryService.deliver(pkg); // 외부와 연결
    }
}</code></pre>
<hr />
<h3 id="4-4-adapter-외부-구현체">4-4. Adapter (외부 구현체)</h3>
<pre><code class="language-java">// adapters/DroneDelivery.java
package adapters;

import core.Package;
import core.DeliveryService;

public class DroneDelivery implements DeliveryService {
    @Override
    public void deliver(Package pkg) {
        System.out.println(&quot;Delivering package via drone to &quot; + pkg.getDestination());
    }
}</code></pre>
<pre><code class="language-java">// adapters/CourierDelivery.java
package adapters;

import core.Package;
import core.DeliveryService;

public class CourierDelivery implements DeliveryService {
    @Override
    public void deliver(Package pkg) {
        System.out.println(&quot;Delivering package via courier to &quot; + pkg.getDestination());
    }
}</code></pre>
<hr />
<h3 id="4-5-실제-연결-및-실행">4-5. 실제 연결 및 실행</h3>
<pre><code class="language-java">// App.java
import core.Package;
import core.PackageProcessor;
import adapters.DroneDelivery;
import adapters.CourierDelivery;

public class App {
    public static void main(String[] args) {
        Package pkg1 = new Package(&quot;P001&quot;, &quot;Seoul&quot;);
        Package pkg2 = new Package(&quot;P002&quot;, &quot;Busan&quot;);

        // Drone으로 배송
        PackageProcessor droneProcessor = new PackageProcessor(new DroneDelivery());
        droneProcessor.process(pkg1);

        // Courier로 배송
        PackageProcessor courierProcessor = new PackageProcessor(new CourierDelivery());
        courierProcessor.process(pkg2);
    }
}</code></pre>
<p><strong>결과 예시:</strong></p>
<pre><code>Processing package to Seoul
Delivering package via drone to Seoul
Processing package to Busan
Delivering package via courier to Busan</code></pre><hr />
<h3 id="✅-이해-포인트">✅ 이해 포인트</h3>
<ol>
<li><strong>PackageProcessor(Core)</strong>는 배송 방법을 전혀 모름</li>
<li><strong>DeliveryService(Port)</strong>로 외부와 연결</li>
<li><strong>DroneDelivery, CourierDelivery(Adapter)</strong>만 바꾸면 배송 수단 변경 가능</li>
<li>규칙(Core)은 변하지 않으므로 테스트와 유지보수가 쉬움</li>
</ol>
<hr />
<h2 id="5-요약">5. 요약</h2>
<ul>
<li>헥사고날 아키텍처 = <strong>핵심 규칙(Core) + 인터페이스(Port) + 실제 구현(Adapter)</strong></li>
<li>장점: 기술 변경 유연, 테스트 용이, 확장성 좋음</li>
<li>단점: 구조 복잡, 작은 프로젝트에는 과도</li>
<li>쉬운 비유: <strong>핵심 소포 처리 규칙(Core) + 전달 방법(Port) + 택배기사/드론/우편함(Adapter)</strong></li>
</ul>
<blockquote>
<p>핵심: “핵심 로직은 외부 환경에 절대 의존하지 않는다”</p>
</blockquote>
<hr />