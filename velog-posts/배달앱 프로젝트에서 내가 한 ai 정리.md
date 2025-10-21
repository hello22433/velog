<h1 id="🦁-ai-전체흐름">🦁 ai 전체흐름</h1>
<p><img alt="" src="https://velog.velcdn.com/images/hello22433/post/ef0ea82b-517e-4bcd-9c4e-061ef7a36e6e/image.png" /></p>
<h1 id="spring-ai-란">Spring AI 란?</h1>
<h3 id="-spring-ai는-resttemplate혹은-webclient을-이용한-llm-호출을-훨씬-단순화·표준화한-추상화-계층">: Spring AI는 <code>RestTemplate</code>(혹은 WebClient)을 이용한 LLM 호출을 훨씬 단순화·표준화한 추상화 계층</h3>
<h3 id="1️⃣-직접-호출-resttemplate">1️⃣ 직접 호출, RestTemplate</h3>
<p>현재 코드는 <strong>HTTP 요청을 수동으로 구성</strong>하는 구조입니다.</p>
<pre><code class="language-java">HttpHeaders headers = new HttpHeaders();
headers.setContentType(MediaType.APPLICATION_JSON);
headers.set(&quot;Authorization&quot;, &quot;Bearer &quot; + apiKey);

Map&lt;String, Object&gt; payload = Map.of(
    &quot;model&quot;, &quot;gemini-2.0-flash&quot;,
    &quot;messages&quot;, List.of(Map.of(&quot;role&quot;, &quot;user&quot;, &quot;content&quot;, prompt))
);

HttpEntity&lt;Map&lt;String, Object&gt;&gt; entity = new HttpEntity&lt;&gt;(payload, headers);

ResponseEntity&lt;Map&gt; response = restTemplate.postForEntity(
    &quot;https://generativelanguage.googleapis.com/v1beta/openai/chat/completions&quot;,
    entity,
    Map.class
);</code></pre>
<p>이 구조의 문제는 👇</p>
<ul>
<li>요청 헤더/바디를 매번 직접 구성해야 함</li>
<li>모델 교체(Gemini → GPT → Claude 등) 시 엔드포인트 변경 필요</li>
<li>응답 JSON 구조가 모델마다 달라서 매번 파싱 로직을 수정해야 함</li>
</ul>
<h3 id="2️⃣-spring-ai-방식">2️⃣ Spring AI 방식</h3>
<p>Spring AI는 이런 “공통 패턴”을 <strong>자동화·통합</strong>한 추상화 계층이에요.</p>
<p>(즉, RestTemplate을 내부적으로 <strong>대신 써주는 라이브러리</strong>입니다.)</p>
<pre><code class="language-java">@Service
@RequiredArgsConstructor
public class AIRecommendationService {

    private final ChatClient chatClient;

    public String generateRecommendation(String prompt) {
        return chatClient.prompt()
                .user(prompt)
                .call()
                .content();
    }
}</code></pre>
<p>끝.</p>
<p>헤더 설정, URL 지정, 모델 이름, 응답 파싱 전부 필요 없습니다.</p>
<h3 id="3️⃣-내부적으로는-여전히-http-호출을-사용">3️⃣ 내부적으로는 여전히 HTTP 호출을 사용</h3>
<p>즉, Spring AI가 하는 일은 아래를 자동으로 해주는 것뿐이다.</p>
<table>
<thead>
<tr>
<th>RestTemplate에서 직접 하던 일</th>
<th>Spring AI가 대신 하는 일</th>
</tr>
</thead>
<tbody><tr>
<td>HTTP 헤더 구성</td>
<td>모델별 기본 헤더 자동 구성</td>
</tr>
<tr>
<td>엔드포인트 선택</td>
<td>모델 타입(OpenAI, Gemini 등)에 따라 자동 결정</td>
</tr>
<tr>
<td>JSON 직렬화/역직렬화</td>
<td>내부 ObjectMapper로 자동 처리</td>
</tr>
<tr>
<td>응답 파싱</td>
<td><code>.content()</code> 한 줄로 텍스트 추출</td>
</tr>
<tr>
<td>예외 처리</td>
<td>표준 예외(<code>AiException</code>)로 통합</td>
</tr>
</tbody></table>
<p>그래서 “<strong>RestTemplate + Gson + 예외처리 + 엔드포인트 관리</strong>”를 전부 없애버릴 수 있는 것!</p>
<hr />
<h2 id="유저별-가게-추천-ml기반knn--주문과-유저-테이블을-활용하여-학습-후-유저와-가게의-유사도-도출">유저별 가게 추천 (ML기반[kNN] , 주문과 유저 테이블을 활용하여 학습 후, 유저와 가게의 유사도 도출)</h2>
<ul>
<li>학습 (실제로는 <a href="http://main.py">main.py</a>로 서버 작동 시키면 주기적으로 자동 학습)</li>
</ul>
<p><img alt="" src="https://velog.velcdn.com/images/hello22433/post/b521e833-63c2-4339-82e1-3d1b174c5b64/image.gif" /></p>
<ul>
<li><p>학습데이터 활용하여, 유저-가게 유사도 계산하여 가게 추천</p>
<p>  <img alt="" src="https://velog.velcdn.com/images/hello22433/post/80eca315-4032-42b4-a40d-6829e1a7dbc5/image.gif" /></p>
</li>
</ul>
<hr />
<h2 id="리뷰분석-ml기반페이크점수bykobert--룰기반키워드추출-감정점수">리뷰분석 (ML기반[페이크점수ByKoBert] / 룰기반[키워드추출, 감정점수])</h2>
<p><img alt="" src="https://velog.velcdn.com/images/hello22433/post/059ef7bd-708c-4888-8adc-db132ec9a092/image.gif" /></p>
<hr />
<h2 id="가게요약--by-리뷰분석">가게요약  By 리뷰분석</h2>
<p><img alt="" src="https://velog.velcdn.com/images/hello22433/post/a87e4396-8e74-42fd-93c6-5a69bd2268cf/image.gif" /></p>
<hr />
<h2 id="검색-시-검색query-분석-후-유사한-임베딩의-가게메뉴-추천-spring-ai-google-ai-studio-api-by-리뷰분석-or-가게요약">검색 시, 검색query 분석 후 유사한 임베딩의 가게(메뉴) 추천 (spring ai, google ai studio api) By 리뷰분석 or 가게요약</h2>
<p><img alt="" src="https://velog.velcdn.com/images/hello22433/post/a082e00f-9679-4a10-a7b2-190d530062a3/image.gif" /></p>