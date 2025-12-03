<h1 id="-slas-는-뭐고-뭐가-어떻게-달라-증권사-도메인을-예를-들어서-알려줄래-">? SLAs 는 뭐고 뭐가 어떻게 달라? 증권사 도메인을 예를 들어서 알려줄래 ?</h1>
<hr />
<h1 id="1-sla란-무엇인가">1. SLA란 무엇인가?</h1>
<p><strong>SLA(Service Level Agreement)</strong> = “이 시스템이 어느 수준까지 정상적으로 작동할 것을 보장하겠다”는 <strong>서비스 품질 계약 지표</strong>이다.</p>
<p>보통 다음을 포함한다:</p>
<ul>
<li><strong>가용성(Availability %)</strong> — 99.9%, 99.99% 같은 숫자</li>
<li><strong>지연 시간(Latency)</strong> — 응답이 몇 ms 안에 나와야 하는가</li>
<li><strong>처리량(Throughput)</strong> — 초당 요청 수</li>
<li><strong>오류율(Error rate)</strong> — 허용되는 실패율</li>
<li><strong>RPO/RTO</strong> — 장애 시 데이터 손실 허용 범위, 복구 시간</li>
</ul>
<blockquote>
<p>즉, &quot;이 기능은 어느 수준의 품질/속도/안정성을 가져야 하는가&quot;를 숫자로 약속하는 것.</p>
</blockquote>
<hr />
<h1 id="2-왜-api--체결매칭--원장은-서로-다른-sla를-가지는가">2. 왜 API / 체결(매칭) / 원장은 서로 다른 SLA를 가지는가?</h1>
<p>이 세 시스템의 역할과 성격이 완전히 다르기 때문에 <strong>요구되는 품질과 성능 기준이 전혀 다르다</strong>.</p>
<p>아래 비교가 가장 이해가 잘 된다.</p>
<hr />
<h1 id="3-api-매칭-엔진-원장의-sla-비교">3. API, 매칭 엔진, 원장의 SLA 비교</h1>
<h2 id="31-api-layer-입력-검증·인증·rate-limit">3.1 API Layer (입력 검증·인증·Rate-Limit)</h2>
<p><strong>역할</strong>:
사용자 요청 받기, 인증, 파라미터 검증, 기본 비즈니스 검증, Rate limit, 트래픽 보호.</p>
<p><strong>SLA 특징</strong>:</p>
<ul>
<li>지연(latency): 수십 ms 내면 충분
예: 20~50ms</li>
<li>가용성: 높지만 초고가용까지는 아님
예: 99.9%</li>
<li>장애 복구: 빠르게 롤백해도 괜찮음</li>
<li>배포: 하루에도 여러 번 배포 가능</li>
<li>데이터 일관성: 강하게 요구되지 않음 (API 서버는 stateless)</li>
</ul>
<p><strong>이유</strong>:
API는 단순히 요청을 받아 <strong>큐나 매칭 엔진으로 전달</strong>하는 게 메인이다.
절대 <strong>초저지연 영역이 아님</strong>.</p>
<hr />
<h2 id="32-체결-엔진matching-engine">3.2 체결 엔진(Matching Engine)</h2>
<p><strong>역할</strong>:
주문을 순서대로 받고, 오더북을 조작하고, 매칭을 확정한다.
(증권사/거래소의 핵심)</p>
<p><strong>SLA 특징</strong>:</p>
<ul>
<li><strong>초저지연!</strong>
수 마이크로초(us) ~ 수 밀리초(ms) 단위 요구
예: 50µs ~ 1ms</li>
<li><strong>가용성 최상급</strong>
99.99% 이상</li>
<li>장애 복구:
실시간 복구, 상태 복제, in-memory snapshot</li>
<li>배포:
함부로 배포 못함 (1~2달에 한 번, 심지어 야간)
배포 전에 수십 차례 시뮬레이션 필요</li>
<li>데이터 일관성:
오더북은 절대 틀리면 안 됨 → Strong Consistency</li>
</ul>
<p><strong>이유</strong>:
체결 엔진은 <strong>돈이 걸린 로직</strong>이며 지연이 단 5ms만 늘어도 고객이 다른 거래소로 간다.
장애가 나도 <em>절대 오더북 깨지면 안 됨.</em></p>
<hr />
<h2 id="33-원장ledger">3.3 원장(Ledger)</h2>
<p><strong>역할</strong>:
거래 내역, 체결 결과, 잔고 변동을 영속적으로 저장하는 최종 시스템.
은행의 Account Book 같은 역할.</p>
<p><strong>SLA 특징</strong>:</p>
<ul>
<li>지연: 반드시 초저지연은 아님
예: 5~20ms 정도 허용</li>
<li>가용성: 매우 높음
99.99% 또는 그 이상</li>
<li>핵심은 <strong>데이터 무결성, 원자성, 정합성</strong>
직전 1건이라도 손실되면 법적 문제 발생</li>
<li>장애 복구:
RPO = 0(단 1개의 트랜잭션도 잃으면 안 됨)
동기 복제, 트랜잭션 로그 기반 복구</li>
<li>배포 주기:
느림. 변경되면 계정/잔고 모델 전체 검증 필요
(1~2개월 단위)</li>
</ul>
<p><strong>이유</strong>:
원장은 속도보다 <strong>안정성·영속성·법적 기준</strong>이 최우선이다.
잘못 저장되면 회사 망함.</p>
<hr />
<h1 id="4-한눈에-보는-sla-비교표">4. 한눈에 보는 SLA 비교표</h1>
<table>
<thead>
<tr>
<th>기능</th>
<th>지연 시간 요구</th>
<th>가용성</th>
<th>장애 복구 방식</th>
<th>배포 주기</th>
<th>최우선 목표</th>
</tr>
</thead>
<tbody><tr>
<td><strong>API Layer</strong></td>
<td>20–50ms OK</td>
<td>99.9%</td>
<td>Blue/Green, 빠른 롤백</td>
<td>자주 배포 가능</td>
<td>트래픽 방어, 안정적 요청 처리</td>
</tr>
<tr>
<td><strong>매칭 엔진</strong></td>
<td><strong>µs ~ sub-ms</strong></td>
<td><strong>99.99% 이상</strong></td>
<td>In-memory snapshot, 복제, 실시간 failover</td>
<td>매우 제한적</td>
<td><strong>초저지연, 순서 보장, 정합성</strong></td>
</tr>
<tr>
<td><strong>원장(Ledger)</strong></td>
<td>5–20ms</td>
<td>99.99%</td>
<td>동기복제, Write-Ahead Log, RPO=0</td>
<td>느림</td>
<td><strong>원자성·무결성·영속성</strong></td>
</tr>
</tbody></table>
<hr />
<h1 id="5-결론-왜-분리해야-하는가">5. 결론: 왜 분리해야 하는가?</h1>
<p>API, 매칭 엔진, 원장은 <strong>각각 목표하는 SLA 자체가 완전히 다르기 때문</strong>이다.</p>
<ul>
<li>API는 트래픽을 잘 받고 <strong>많이 배포</strong>해야 한다.</li>
<li>매칭 엔진은 <strong>1µs 지연도 조심해야 하므로</strong> 코드가 더럽더라도 절대 안정적으로 유지해야 한다.</li>
<li>원장은 <strong>속도보다 정확성</strong>이 우선이다.</li>
</ul>
<p>그러므로 하나의 모놀리식으로 합치면:</p>
<ul>
<li>API의 자주 배포 → 매칭 엔진의 안정성을 깨뜨림</li>
<li>원장의 무거운 트랜잭션 → 매칭 엔진의 지연을 폭발시킴</li>
<li>장애 복구 전략도 서로 다름</li>
</ul>
<p>즉, <strong>“서로 다른 SLA = 서로 다른 기술적 성격 = 반드시 분리해야 하는 아키텍처”</strong>다.</p>