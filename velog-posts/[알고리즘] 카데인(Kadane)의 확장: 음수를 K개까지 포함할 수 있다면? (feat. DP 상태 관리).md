<blockquote>
<p><strong>주제:</strong> DP(Dynamic Programming)의 상태 확장과 업데이트 순서</p>
</blockquote>
<p>기술 면접이나 코딩 테스트에서 &quot;최대 연속 부분합(Maximum Subarray Sum)&quot; 문제는 단골 손님입니다. 보통 <strong>카데인 알고리즘(Kadane's Algorithm)</strong>으로 $O(N)$에 풀죠.</p>
<p>하지만 면접관이 이런 제약을 추가한다면 어떻게 될까요?</p>
<blockquote>
<p><strong>&quot;음수가 최대 K개까지만 포함될 수 있다고 가정해 봅시다. 그때의 최대 연속합은 어떻게 구하나요?&quot;</strong></p>
</blockquote>
<p>이 질문은 단순한 알고리즘 암기가 아니라, <strong>DP의 상태(State) 정의</strong>와 <strong>메모리 갱신 순서</strong>를 제대로 이해하고 있는지 묻는 좋은 질문입니다.</p>
<p>오늘은 기본 카데인 알고리즘이 어떻게 확장되는지, 직관적인 흐름으로 정리해 보겠습니다.</p>
<hr />
<h2 id="1-기본-카데인-알고리즘-kadanes-algorithm">1. 기본: 카데인 알고리즘 (Kadane's Algorithm)</h2>
<p>가장 기본적인 문제부터 짚고 넘어갑시다.
카데인의 핵심 아이디어는 <strong>&quot;지금까지의 합을 가져갈 것인가, 버리고 새로 시작할 것인가?&quot;</strong>입니다.</p>
<h3 id="핵심-로직">핵심 로직</h3>
<ul>
<li><code>이전까지의 합 + 현재 값</code> vs <code>현재 값</code> 중 더 큰 것을 선택합니다.</li>
<li>이전 구간의 합이 음수가 되어 내 발목을 잡는다면, 과감히 버리고 현재 숫자부터 새로 시작하는 것이 이득이기 때문입니다.</li>
</ul>
<p>이를 수식으로 표현하면 다음과 같습니다.</p>
<p>$$dp[i] = \max(dp[i-1] + num[i], num[i])$$</p>
<p>여기서 중요한 점은 <strong>관리해야 할 상태가 '현재 위치에서 끝나는 최대합' 딱 하나</strong>라는 점입니다. 그래서 1차원 배열(혹은 변수 하나)로 해결되며 시간 복잡도는 $O(N)$입니다.</p>
<hr />
<h2 id="2-문제의-확장-음수는-k개까지만">2. 문제의 확장: &quot;음수는 K개까지만!&quot;</h2>
<p>이제 제약 조건이 생겼습니다. 무작정 큰 수를 더하는 게 아니라, <strong>구간 내에 음수가 K개를 초과하면 안 됩니다.</strong></p>
<p>기존 카데인 방식은 &quot;합이 커지냐 작아지냐&quot;만 봤지, &quot;안에 음수가 몇 개 있냐&quot;는 관심이 없었습니다. 따라서 기존의 <code>dp</code> 변수 하나로는 이 문제를 풀 수 없습니다.</p>
<h3 id="해결책-상태state를-확장하자">해결책: 상태(State)를 확장하자</h3>
<p>상태가 하나에서 여러 개로 늘어납니다.</p>
<ul>
<li><strong>기존:</strong> $dp[i]$ = 현재 위치까지의 최대 연속합</li>
<li><strong>확장:</strong> $dp[k][i]$ = <strong>&quot;음수를 정확히 $k$개 포함한 상태에서, $i$번째에서 끝나는 최대 연속합&quot;</strong></li>
</ul>
<p>즉, 음수 개수별로 카데인 알고리즘을 여러 개 동시에 돌린다고 생각하면 됩니다.</p>
<hr />
<h2 id="3-핵심-로직-경우의-수-나누기">3. 핵심 로직: 경우의 수 나누기</h2>
<p>값을 하나씩 읽을 때마다(<code>num</code>), 이 숫자가 <strong>양수</strong>인지 <strong>음수</strong>인지에 따라 행동이 달라집니다.</p>
<h3 id="1-num이-음수일-때">1) <code>num</code>이 음수일 때</h3>
<p>음수를 만났다면, 우리는 선택해야 합니다.
&quot;이 음수를 포함해서 연속합을 이어갈 것인가?&quot;</p>
<ul>
<li>이 음수를 포함하려면, 이전 상태는 반드시 <strong>음수가 $k-1$개인 상태</strong>여야 합니다.</li>
<li>그래야 현재 음수를 더해서 <strong>음수가 $k$개인 상태</strong>가 되니까요.</li>
<li>$$dp[k] = dp[k-1] + num$$</li>
</ul>
<h3 id="2-num이-양수일-때">2) <code>num</code>이 양수일 때</h3>
<p>양수는 음수 개수에 영향을 주지 않습니다.</p>
<ul>
<li>그냥 현재 상태 그대로 값을 더해주면 됩니다.</li>
<li>$$dp[k] = dp[k] + num$$</li>
</ul>
<hr />
<h2 id="4-가장-중요한-포인트-업데이트-순서-dirty-read-방지">4. 가장 중요한 포인트: 업데이트 순서 (Dirty Read 방지)</h2>
<p>이 알고리즘 구현의 하이라이트입니다. 2차원 배열을 다 쓰면 메모리 낭비가 심하므로, 보통 1차원 배열 <code>dp[K+1]</code>만 사용하여 공간 복잡도를 줄입니다.</p>
<p>이때 <strong>업데이트 순서</strong>가 틀리면 답이 완전히 달라집니다.</p>
<h3 id="⚠️-음수일-때는-반드시-역순k-→-1으로-갱신해야-한다">⚠️ 음수일 때는 반드시 '역순(K → 1)'으로 갱신해야 한다</h3>
<p>만약 <code>num</code>이 음수인데 <code>k</code>를 0부터 $K$로(순방향) 갱신하면 어떻게 될까요?</p>
<ol>
<li><code>dp[0]</code>을 보고 <code>dp[1]</code>을 갱신합니다. (음수 1개 사용)</li>
<li>그런데 바로 다음 루프(<code>k=2</code>)에서 <strong>방금 갱신된 <code>dp[1]</code>을 보고</strong> <code>dp[2]</code>를 갱신해 버립니다.</li>
<li>결과적으로 <strong>음수 하나(<code>num</code>)를 한 번의 턴에서 중복해서 카운트</strong>하는 오류(Dirty Read)가 발생합니다.</li>
</ol>
<p>따라서 음수가 들어왔을 때는 <strong>큰 $k$부터 작은 $k$로(뒤에서 앞으로)</strong> 업데이트해야, 아직 갱신되지 않은 '이전 단계의 상태'를 안전하게 참조할 수 있습니다.</p>
<blockquote>
<p><strong>요약:</strong></p>
<ul>
<li><code>num &lt; 0</code>: $k$를 $K$에서 1로 <strong>역순</strong> 업데이트 (데이터 오염 방지)</li>
<li><code>num &gt;= 0</code>: 순서 상관없음 (보통 순방향)</li>
</ul>
</blockquote>
<hr />
<h2 id="5-java-코드-구현">5. Java 코드 구현</h2>
<pre><code class="language-java">import java.util.Arrays;

public class KadaneWithKNegatives {
    public static int solve(int[] nums, int K) {
        // dp[k]: 음수를 k개 포함하며 현재 위치에서 끝나는 최대 연속합
        // 초기값은 아주 작은 수로 설정 (해당 상태가 아직 없음을 의미)
        long[] dp = new long[K + 1];
        Arrays.fill(dp, Integer.MIN_VALUE);

        long globalMax = Long.MIN_VALUE;

        for (int num : nums) {
            // Case 1: 음수가 들어옴 -&gt; 음수 개수가 늘어남 (상태 전이 발생)
            if (num &lt; 0) {
                // Dirty Read 방지를 위해 역순(K -&gt; 1) 업데이트
                for (int k = K; k &gt;= 1; k--) {
                    // 이전 상태(k-1)가 유효하다면 전이
                    if (dp[k - 1] != Integer.MIN_VALUE) {
                        // k=1인 경우: 이 음수로 '새로 시작'하는 경우도 고려해야 함
                        if (k == 1) dp[k] = Math.max(dp[k - 1] + num, num);
                        else dp[k] = dp[k - 1] + num;
                    } 
                    // 이전 상태가 없어도 k=1이면 이 음수 하나로 시작 가능
                    else if (k == 1) {
                        dp[k] = num;
                    }
                }
                // 음수가 들어왔으므로 '음수 0개(dp[0])'인 연속 구간은 끊김
                dp[0] = Integer.MIN_VALUE; 
            }

            // Case 2: 양수가 들어옴 -&gt; 현재 상태 유지하며 값만 증가
            else {
                // dp[0]: 일반 카데인 로직 (이어붙이기 vs 새로 시작)
                if (dp[0] == Integer.MIN_VALUE) dp[0] = num;
                else dp[0] = Math.max(dp[0] + num, num);

                // dp[1] ~ dp[K]: 기존 상태에 양수 더하기
                for (int k = 1; k &lt;= K; k++) {
                    if (dp[k] != Integer.MIN_VALUE) {
                        dp[k] += num;
                    }
                }
            }

            // 매 턴마다 전체 최대값 갱신
            for (int k = 0; k &lt;= K; k++) {
                if (dp[k] != Integer.MIN_VALUE) {
                    globalMax = Math.max(globalMax, dp[k]);
                }
            }
        }

        return (int) globalMax;
    }
}</code></pre>
<hr />
<h2 id="6-직관적-예시-tracing">6. 직관적 예시 (Tracing)</h2>
<p>배열이 <code>[5, -2, 3]</code>이고 <strong>$K=1$ (음수 최대 1개)</strong>인 상황을 봅시다.</p>
<p><strong>1. <code>num = 5</code> (양수)</strong></p>
<ul>
<li><code>dp[0]</code>(음수 0개): 5 (새로 시작)</li>
<li><code>dp[1]</code>(음수 1개): -INF (아직 불가능)</li>
<li><strong>현재 상태:</strong> <code>[5, -INF]</code></li>
</ul>
<p><strong>2. <code>num = -2</code> (음수)</strong></p>
<ul>
<li>음수이므로 <strong>역순 업데이트</strong> ($k=1 \rightarrow 0$)</li>
<li><code>dp[1]</code>: <code>dp[0]</code>(5) + (-2) = <strong>3</strong> (전이 성공)</li>
<li><code>dp[0]</code>: 음수가 들어왔으니 끊김 $\rightarrow$ <strong>-INF</strong></li>
<li><strong>현재 상태:</strong> <code>[-INF, 3]</code> (최대합 3)</li>
</ul>
<p><strong>3. <code>num = 3</code> (양수)</strong></p>
<ul>
<li><code>dp[0]</code>: 3 (새로 시작)</li>
<li><code>dp[1]</code>: 기존 <code>dp[1]</code>(3) + 3 = <strong>6</strong></li>
<li><strong>현재 상태:</strong> <code>[3, 6]</code></li>
</ul>
<p><strong>최종 결과:</strong> <code>6</code> (구간: <code>5, -2, 3</code>)
음수 <code>-2</code>를 하나 포함했지만, 앞뒤의 양수(<code>5</code>, <code>3</code>) 덕분에 더 큰 합을 만들었습니다.</p>
<hr />
<h2 id="7-마무리-요약">7. 마무리 요약</h2>
<p>이 알고리즘 확장의 핵심은 두 가지입니다.</p>
<ol>
<li><strong>상태의 분리:</strong> 하나의 <code>dp</code> 대신, 음수 개수($k$)별로 <code>dp</code>를 관리한다.</li>
<li><strong>업데이트 순서:</strong> 상태가 전이($k-1 \rightarrow k$)될 때는 <strong>역순</strong>으로 처리하여 꼬임을 방지한다.</li>
</ol>
<p>단순히 코드를 외우는 것보다, <strong>&quot;상태를 어떻게 정의하고, 어떻게 전이시킬 것인가?&quot;</strong>라는 DP의 본질을 이해하는 데 아주 좋은 예제입니다.</p>
<hr />