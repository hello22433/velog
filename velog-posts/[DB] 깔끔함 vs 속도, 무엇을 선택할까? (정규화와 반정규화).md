<p><strong>Part 3. 데이터베이스</strong>의 마지막이자 설계의 꽃, <strong>'정규화와 반정규화'</strong>입니다.</p>
<p>이 주제는 신입 개발자가 DB 테이블을 설계할 때 가장 많이 고민하는 지점이자, 면접관이 <strong>&quot;왜 테이블을 그렇게 쪼갰어요?&quot;</strong>라고 물었을 때 논리적으로 방어해야 하는 영역입니다.</p>
<p>단순히 &quot;정규화는 쪼개는 것, 반정규화는 합치는 것&quot;을 넘어, <strong>'성능'과 '데이터 무결성' 사이의 줄다리기</strong> 관점에서 풀어드렸습니다.</p>
<hr />
<h2 id="db-깔끔함-vs-속도-무엇을-선택할까-정규화와-반정규화">[DB] 깔끔함 vs 속도, 무엇을 선택할까? (정규화와 반정규화)</h2>
<h3 id="테이블을-쪼갤까요-합칠까요">&quot;테이블을 쪼갤까요, 합칠까요?&quot;</h3>
<p>DB 설계를 하다 보면 항상 딜레마에 빠집니다.</p>
<ul>
<li>테이블을 잘게 쪼개면(정규화) 데이터가 깔끔해지는데, 조회할 때 <code>JOIN</code>을 너무 많이 써서 느려집니다.</li>
<li>반대로 테이블을 합치면(반정규화) 조회는 빠른데, 데이터가 중복돼서 관리가 지옥 같아집니다.</li>
</ul>
<p>정답은 없습니다. 상황에 따른 <strong>선택</strong>만 있을 뿐입니다.
오늘은 백엔드 개발자가 이 선택을 할 때 기준이 되는 <strong>정규화의 목적</strong>과 <strong>반정규화의 타이밍</strong>을 알아보겠습니다.</p>
<hr />
<h3 id="1-정규화normalization-다이어트의-정석">1. 정규화(Normalization): 다이어트의 정석</h3>
<p>정규화는 쉽게 말해 <strong>&quot;중복된 데이터를 없애고 테이블을 깔끔하게 나누는 과정&quot;</strong>입니다.
뚱뚱한 테이블 하나에 모든 정보를 다 때려 넣으면, 필연적으로 <strong>이상 현상(Anomaly)</strong>이 발생하기 때문입니다.</p>
<h4 id="😱-정규화를-안-했을-때-생기는-비극-이상-현상">😱 정규화를 안 했을 때 생기는 비극 (이상 현상)</h4>
<p>하나의 테이블에 <code>(학생ID, 이름, 학과, 학과사무실위치)</code>가 다 있다고 가정해 봅시다.</p>
<ol>
<li><strong>삽입 이상 (Insertion Anomaly):</strong> '컴퓨터공학과'라는 학과를 새로 만들고 싶은데, 아직 입학생이 없어서 데이터 추가를 못 합니다. (학생ID가 PK라서)</li>
<li><strong>갱신 이상 (Update Anomaly):</strong> '컴퓨터공학과' 사무실이 1층에서 3층으로 이사했습니다. 컴공과 학생이 500명이면, 500개의 행(Row)을 모두 수정해야 합니다. 하나라도 놓치면 데이터 불일치가 일어납니다.</li>
<li><strong>삭제 이상 (Deletion Anomaly):</strong> 컴공과 학생이 딱 1명 있었는데 자퇴했습니다. 그 학생 데이터를 지웠더니 '컴퓨터공학과' 정보까지 덩달아 사라집니다.</li>
</ol>
<p>그래서 우리는 테이블을 쪼갭니다. <strong>학생 테이블</strong>과 <strong>학과 테이블</strong>로요.</p>
<blockquote>
<p><strong>💡 정규화의 핵심 목표:</strong>
데이터의 <strong>중복을 제거</strong>하고 <strong>무결성(정확성)</strong>을 유지하는 것!</p>
</blockquote>
<hr />
<h3 id="2-반정규화de-normalization-필요악의-선택">2. 반정규화(De-normalization): 필요악의 선택</h3>
<p>&quot;그럼 무조건 쪼개는 게 정답인가요?&quot;
아니요. 너무 쪼개면 <strong>조회 속도</strong>가 박살 납니다.</p>
<p>쇼핑몰 주문 내역을 보여줘야 하는데, 정규화가 너무 잘 되어 있어서 <code>주문</code> JOIN <code>회원</code> JOIN <code>상품</code> JOIN <code>배송</code> JOIN <code>결제</code>...
쿼리 한 번에 테이블 5개를 합쳐야(JOIN) 한다면 서버는 비명을 지를 겁니다.</p>
<p>이때 <strong>성능을 위해 의도적으로 중복을 허용하고 테이블을 합치는 것</strong>을 <strong>반정규화</strong>라고 합니다.</p>
<h4 id="🚀-반정규화를-고려해야-할-때">🚀 반정규화를 고려해야 할 때</h4>
<ol>
<li><strong>조인(JOIN)이 너무 많아 쿼리가 느릴 때:</strong> 자주 같이 조회되는 데이터는 그냥 한 테이블에 넣습니다.</li>
<li><strong>통계/요약 데이터가 필요할 때:</strong> 매번 <code>COUNT(*)</code>나 <code>SUM()</code>을 계산하는 게 무거우면, 아예 <code>총주문금액</code>, <code>댓글수</code> 같은 컬럼을 추가해서 숫자를 박아둡니다.</li>
</ol>
<blockquote>
<p><strong>⚠️ 반정규화의 대가 (Trade-off)</strong>
조회는 빨라지지만, 데이터 입력/수정/삭제 시 <strong>동기화 비용</strong>이 듭니다.
(예: 댓글 하나 지워지면, 게시글 테이블의 <code>댓글수</code> 컬럼도 -1 해줘야 함. 안 하면 데이터 꼬임.)</p>
</blockquote>
<hr />
<h3 id="3-실전-예시-게시판의-댓글-수">3. 실전 예시: &quot;게시판의 댓글 수&quot;</h3>
<p>가장 흔한 반정규화 사례입니다.</p>
<ul>
<li><strong>정규화 원칙:</strong> 게시글 목록을 보여줄 때마다 <code>SELECT count(*) FROM 댓글 WHERE 게시글_id = ?</code> 쿼리를 날려서 실시간으로 셉니다. (데이터 정확함, 성능 느림)</li>
<li><strong>반정규화 적용:</strong> 게시글(Post) 테이블에 <code>reply_count</code>라는 컬럼을 만듭니다. 댓글이 달릴 때마다 이 숫자를 +1 해줍니다. 목록 조회 시 그냥 이 숫자만 읽으면 됩니다. (성능 빠름, 숫자 안 맞을 위험 있음)</li>
</ul>
<hr />
<h3 id="4-실전-면접-대비-3초-요약">4. 실전 면접 대비: 3초 요약</h3>
<p>면접관이 &quot;정규화와 반정규화의 차이가 뭔가요?&quot;라고 묻는다면, <strong>목적</strong>을 중심으로 대답하세요.</p>
<p><strong>Q. 정규화와 반정규화에 대해 설명해주세요.</strong></p>
<blockquote>
<p><strong>A.</strong>
&quot;<strong>정규화</strong>는 데이터의 중복을 제거하여 <strong>무결성(Consistency)</strong>을 지키고 이상 현상을 방지하는 과정입니다. 대신 테이블이 나뉘어 조인 연산이 많아질 수 있습니다.
<strong>반정규화</strong>는 읽기 성능을 향상하기 위해 정규화를 포기하고 <strong>의도적으로 데이터 중복을 허용</strong>하는 기법입니다. 조인을 줄여 조회 속도는 빨라지지만, 데이터 정합성을 맞추는 관리 비용이 증가합니다.&quot;</p>
</blockquote>
<p><strong>Q. 본인 프로젝트에서는 어떻게 적용했나요?</strong></p>
<blockquote>
<p><strong>A.</strong>
&quot;기본적으로는 <strong>3정규형</strong>까지 적용하여 데이터 무결성을 확보했습니다. 하지만 메인 페이지에서 빈번하게 조회되는 '상품 좋아요 수' 같은 집계 데이터는 매번 <code>count</code> 쿼리를 날리는 부하를 줄이기 위해, 상품 테이블에 컬럼을 추가하는 <strong>반정규화</strong>를 적용하여 조회 성능을 최적화했습니다.&quot;</p>
</blockquote>
<hr />
<h3 id="🚀-part-3를-마치며">🚀 Part 3를 마치며</h3>
<p>데이터베이스는 결국 <strong>&quot;데이터를 안전하게 저장하느냐(정규화)&quot;</strong> vs <strong>&quot;빠르게 꺼내오느냐(반정규화)&quot;</strong>의 싸움입니다. 무조건적인 정답은 없으니 상황에 맞춰 유연하게 설계하는 것이 실력입니다.</p>
<p>자, 이제 컴퓨터 내부(OS), 통신(Network), 저장소(DB)까지 섭렵했습니다.
마지막 <strong>Part 4</strong>는 코딩 테스트와 실무 효율성을 책임지는 <strong>자료구조(Data Structure)</strong>입니다.
그 첫 번째 주인공, 자바 개발자가 가장 사랑하는 <strong>'ArrayList와 LinkedList'</strong>로 돌아오겠습니다.</p>
<blockquote>
<p><strong>Next Topic:</strong> [자료구조] ArrayList vs LinkedList, 아직도 아무거나 쓰시나요?</p>
</blockquote>