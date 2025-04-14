<h1 align='center'>📋 목차 📋 </h1>

<div>
  <ol style="text-align: left; display: inline-block;">
    <li><b>[SPOT]</b> 소일거리 매칭 서비스  
      <br />└ 당근 알바, 해주세요에서 소일거리 특화 
      <br />└ <i>구인구직, 알림 담당</i>
      <br />└ <i>OS, WAS, DB 튜닝 담당</i>
    </li>
    <br />
    <li><b>[울반]</b> 학급관리 서비스  
      <br />└ 네이버 밴드를 학급 전용으로 재개발  
      <br />└ <i>퀴즈게임, 회원 관리 담당</i>
    </li>
    <br />
    <li><b>[WALK WALK]</b> 어르신 운동 서비스
      <br />└ '런데이' 앱을 어르신 용으로 특화
      <br />└ <i>타자가 느린 어르신을 위한 말로하는 TTS, STT 채팅방 담당</i>    
    </li>
  </ol>
</div>





# 1️⃣ SPOT 

> **`개요`** : 누구나 하기 싫은 소일거리 (빨래, 음식물 쓰레기 치우기) 등 대신 해줄 사람을 찾고, 일거리를 맡기는 서비스

### 담당

- 사용자의 위치 기반 근처 일거리 찾기
- NO-SHOW 한 회원에 대한 자동 일거리 배정 취소
- 일 매칭 생애주기 구현 (일 등록, 해결 신청, 일 시작, 해결, 해결 확정)
- 서비스 내의 모든 이벤트 알림
- 대규모 사용자 부하 대비 WAS, DB, 서버 OS 튜닝  

## (1) 사용자 위치 기반 근처 일거리 찾기

### A. 첫 구현

  <span align="center" style="float: left"> <img src = "https://raw.githubusercontent.com/dalcheonroadhead/img-cloud/main/2025-03/image-20250328014642212.png" width = 100%/> 
</span>


- 프론트에서 현 사용자의 `위도`, `경도`를 전송하면, RDMS에서 거리 계산 식인 `Haver Sine` 공식을 활용해 직접 계산

**💭 고민사항**

- 페이지네이션과 복잡한 쿼리 로직이 동시에 필요한 현 서비스에서 문자열을 sql 쿼리로 매번 변환하는 JPQL의 경우 성능 저하가 우려됨. RDB에 쿼리를 보내는 여러 방법들의 성능을 비교해보고, 성능 개선을 고려

**🌱 개선사항**: **응답시간 60% 단축, TPS: 30% 상승**

- 부하 테스트 실행: 
  (`대상 데이터`: 회원 = 5000명, 일거리 = 5000개, 
  `Thread 수` : 500, `ramp-up`: 300, `지속`: 6분 지속, `페이지 머무르는 시간`: 2초)    

<p align ='center' style="display: flex; break-inside: avoid"> 
<img src="https://raw.githubusercontent.com/dalcheonroadhead/img-cloud/main/2025-03/image-20250328011632812.png" width = 50%/>
<img src = "https://raw.githubusercontent.com/dalcheonroadhead/img-cloud/main/2025-03/image-20250328011553536.png" width = 50%/>
</p>
<img src = "https://raw.githubusercontent.com/dalcheonroadhead/img-cloud/main/2025-03/image-20250328011835923.png" width=50%/>

- 확인 결과, JPQL은 부하가 지속될수록 응답 시간이 1초를 넘어가며, 속도가 제일 안 좋았고, native-query는 1초 안으로 응답 시간이 들어왔으나, TPS가 제일 높고, 평균 응답시간이 400ms 이하인 **`QueryDSL`**을 활용하기로 결정, 응답 속도를 2배 이상 개선

### B. 고도화 1단계

**💭 고민사항**

- 현 서비스는 `RDB에서 일 찾기 fullscan - O(N)` ➜ `찾은 일과 사용자 간의 거리계산 O(N)` ➜ `DTO MAPPING O(N)`이라는 **`O(3N)`**의 시간 복잡도를 극복하지 못함.

<div style="display: flex; align-items: center; gap: 20px;">
    <li>
		<p>
                  현재 서비스 로직은 반경의 원을 그려서 그 안의 일거리만 계산하는 게 아니라
      <strong>모든 일거리와 사용자간의 거리를 계산해 그 중 반경 내의 사용자를 선정하고 있음.</strong><br>
      ➜ 브루트 포스, 개선의 여지를 찾고자 함.
        </p>
    </li>
  <img src="https://raw.githubusercontent.com/dalcheonroadhead/img-cloud/main/2025-03/image-20250328012341200.png" alt="설명 이미지" width="300">
</div>

**🌱 개선사항**: **쿼리 속도 12배 개선,  RPT 100배 개선, TPS 10배 개선**

- 부하테스트 실행: 
  (`대상 데이터`: 회원 = 10만명, 일거리 = 10만 개 (판교 내에서만 위치 분포, 
  `Thread 수` : 500, `ramp-up`: 300, `지속`: 6분 지속, `페이지 머무르는 시간`: 2초)

**a. 첫 구현 최적화 서비스에 대한 부하 진행**

<img src ='https://raw.githubusercontent.com/dalcheonroadhead/img-cloud/main/2025-03/image-20250328021543165.png' width=50%/>

데이터가 10만개 기준이 되었을 떄, 5분 이후부터, Error율이 30%이상으로 치솟았던 JPQL과 달리 에러율은 없었지만. 응답시간이  **4초 대로 사용자가 기다릴 수 없을 만큼** 높아졌음. 최적화가 필요 

**b. B-tree 인덱스를 활용한 필터링 시도, 옵티마이저 힌트를 통한 강제 인덱싱 적용**

<div style="display: flex; align-items: center; gap: 20px;">
    <p>
        B-tree 인덱스를 활용하기 위해 위도, 경도와 Between문을 활용해 넓이 3KM 짜리 격자를 생성함. 
위도, 경도를 복합 인덱스로 설정하여 1차 필터링을 시도
    </p>
  <img src="https://raw.githubusercontent.com/dalcheonroadhead/img-cloud/main/2025-03/image-20250328021805917.png" alt="설명 이미지" width="300">
</div>



  <img src="https://raw.githubusercontent.com/dalcheonroadhead/img-cloud/main/2025-03/image-20250328022358268.png" width="60%"/>

```sql
Limit: 100 row(s) (cost=10612 rows=100)(actual time=314..314 rows=100 loops=1)
   Sort: distance, limit input to 100 row(s) per chunk (cost=10612 rows=97706) (actual time=149..149 rows=0 loops=1) 
       Filter: (Haver-sine d 연산) (actual time=149..149 rows=0 loops=1) 
            Table scan on job (cost=10612 rows=97706) (actual time=0.0353..141 rows=100000 loops=1)
```

- 인덱싱을 활용해 O(M^2)이 아닌 O(logN + M) (N ➜ 데이터 접근, M ➜ 데이터 리드)를 바랬으나, 인덱싱을 활용하지 않고 `FULL-SCAN`을 탐.  

<p align ='center' style="display: flex; break-inside: avoid"> 
<img src="https://raw.githubusercontent.com/dalcheonroadhead/img-cloud/main/2025-03/image-20250328023929214.png" width = 50%/>
<img src = "https://raw.githubusercontent.com/dalcheonroadhead/img-cloud/main/2025-03/image-20250328023331266.png" width = 50%/>
</p>

- 강제 IDX를 적용한 결과, COST는 높지만 오히려 실행시간은 **10배** 떨어짐을 확인. 
- 옵티마이저가 실행계획에서 랜덤 I/O 접근에 대한 코스트를 과하게 잡고 있다고 판단하고 인덱스를 강제 적용함.

**c. 기존 3번 계산 로직을 서브쿼리로 계산 재활용으로 변경**

<p align ='center' style="display: flex; break-inside: avoid"> 
<img src="https://raw.githubusercontent.com/dalcheonroadhead/img-cloud/main/2025-03/image-20250328023732304.png" width = 50%/>
<img src = "https://raw.githubusercontent.com/dalcheonroadhead/img-cloud/main/2025-03/image-20250328023751395.png" width = 50%/>
</p>

**d. 복합 인덱스를 클러스터링 인덱스로 재구현**

- 보조 인덱스로 인덱싱을 할 경우, 보조 인덱스를 읽고, 원본 인덱스를 다시 읽어야 하기에 총 `O(logN + M) * O(M)`의 시간 복잡도가 듦. (N = 데이터 접근, M = 데이터 리드)
- 원본 인덱스로 가는 과정을 줄이기 위해, 보조 인덱스의 value를 PK가 아닌, 필요한 데이터로 집어넣는 보조 인덱스 작업을 통해, 원본으로 가는 과정을 줄여 최적화

**e. JPA -> JDBC 변환**

- 기존 JPA와 queryDSL로는 힌트를 적용한 쿼리문 작성이 불가했음. 
- 또한 이번 서비스는 GET 요청이기 때문에 트랜잭션 관리와 더티 체킹 감지라는 JPA의 이점이 필요하지 않다고 판단.
- 따라서 기존 JpaQueryFactory에서 SQLQueryFactory로 변경하고, 10만 이하 권장 힌트 적용, 이상일 때는 강제 힌트 적용하도록 플래그를 설정해서 쿼리를 개선  

**결과: **

<p align ='center' style="display: flex; break-inside: avoid"> 
<img src="https://raw.githubusercontent.com/dalcheonroadhead/img-cloud/main/2025-03/image-20250328025221447.png" width = 50%/>
<img src = "https://raw.githubusercontent.com/dalcheonroadhead/img-cloud/main/2025-03/image-20250328025139102.png" width = 50%/>
</p>

최종적으로 **0.12초가 걸리던 계산을 0.01초대로** 개선 및 계산문을 쿼리에 넣으며, 불필요한 서버 매핑 로직을 제거함.
**RPT: 100배 개선 및 TPS : 10배 개선**

### C. 고도화 2단계

**💭 고민사항**

- 현직자 멘토링 결과, 옵티마이저에 힌트를 주는 행위는 옵티마이저에 변형을 일으킬 수 있기 때문에, 실서비스에서 자주 사용하지 않는다고 함. 또한 서비스 로직을 쿼리에 넣는 행위는 단일 책임 원칙에도 위배됨.
- 따라서 쿼리문은 **최대한 명령어 안에서 해결하며, 성능은 저번 쿼리 튜닝과 동급 혹은 그 이상으로 나오는 것을 목표로 잡음**
- 위에 있는 모든 과정은 결국 RDB에서 데이터를 가져와야함으로 **`O(N)`**이라는 태생적 한계를 극복하지 못함
- 더불어, 부하 및 스파이크 테스트 시 Connection Pool 부족으로 **`JDBC Connection ERROR`**가 날 경우 이에 대한 대비책이 없음

 **🌱 개선사항**: 

**a. mySQL 공간 객체 활용**

- B-tree 인덱스가 힌트 없이 인덱스를 타는 기점을 조사한 결과 10만개의 데이터 중 3000개부터 타는 것을 발견함. 

- 전체 데이터의 10%도 안되는 상황이지만, 현재 위치 데이터가 판교에 편중되어 있어, 데이터의 편향된 분포에 따라 옵티마이저의 B-tree 활용 여부가 균등하지 않다고 판단함.

- 더 나은 쿼리를 위해, 서버와 DB에 GPS 지평 좌표계 기반 POINT 객체를 도입함. 더불어, 이와 관련된 MYSQL 내장함수를 활용

- <p align ='center' style="display: flex; break-inside: avoid"> 
  <img src="https://raw.githubusercontent.com/dalcheonroadhead/img-cloud/main/2025-03/image-20250328025139102.png" width = 50%/>
  <img src = "https://raw.githubusercontent.com/dalcheonroadhead/img-cloud/main/2025-03/image-20250328030055839.png" width = 50%/>
  </p>
  성능 테스트 결과, 공간 객체 활용이 1차 고도화의 튜닝된 쿼리의 90% 정도의 성능을 보이고 있음을 확인
  협업과 옵티마이저 변형이 안 가는 선에서 최적의 쿼리 튜닝이라 판단 후 이를 적용함.

**b. GEO-HASH 활용**

- `O(N)`의 시간과 에러 대응책이 따로 없다는 RDB의 태생적 한계를 극복하기 위해 REDIS의 GEO-HASH를 활용
- GEO-HASH는 위도/경도를 비트 단위로 압축한 HashTable + SKIP LIST 자료구조 기반이라 범위 탐색, 삽입, 삭제 연산에서 **`O(logN)`**의 시간복잡도를 보여 성능 개선에 최적이라 판단
- GEO-HASH를 먼저 사용함으로 JDBC Connction Error와 무관하고, 만약 Redis 다운 시, 기존 쿼리문을 백업으로 사용할 수 있도록 조치하여 에러에도 유연함

## (2) NO-SHOW 예약 철회

### A. 첫 구현: 동기 스케줄링

- `클라이언트로부터 예약 철회 요청` ➜ `매칭 테이블 상태 변경` ➜ `예약 철회 동기 스케줄링 처리` 로직으로 구현

**💭 고민사항**

- SpringMVC는 HTTP 요청을 위한 `DispatcherServlet`과 스케줄러를 위한 `SchedulingExecutor`가 따로 존재하지만, `SchedulingExecutor`에 요청 부하가 심해질수록 **타 API의 RPT도 같이 올라감을 발견**

<img src = 'https://raw.githubusercontent.com/dalcheonroadhead/img-cloud/main/2025-03/image-20250328033137725.png' width = 50%/>

- 동기적인 직렬적 처리 방식을 피해야한다 판단

 **🌱 개선사항**: **타 API의 평균 RPT 정상화**

- Spring 내장 비동기 스케줄러를 이용 

<p align ='center' style="display: flex; break-inside: avoid"> 
<img src="https://raw.githubusercontent.com/dalcheonroadhead/img-cloud/main/2025-03/image-20250328032623061.png" width = 50%/>
<img src = "https://raw.githubusercontent.com/dalcheonroadhead/img-cloud/main/2025-03/image-20250328032639786.png" width = 50%/>
</p>

- 백그라운 5개 개방, TimeStamp 기반 우선순위 큐인 `ThreadPoolSchedulingTaskExecutor`와 `CompletableFuture` 객체를 활용해 서비스 로직 구현
- 노쇼한 사용자의 경우 10분 타이머가 걸린 `CompletableFuture`객체에 저장 및 우선순위 큐에 삽입.
- 10분 동안 노쇼한 사용자로부터 아무런 반응이 없다면, 우선순위 큐에서 꺼내져 백그라운드에서 실행 및 노쇼 처리, 결제 철회, 구직 상태 변경이 이루어짐
- 10분 안에 반응이 오면, 미리 `matching_id`를 저장한 ConcurrentHashMap에서 id를 꺼내 우선순위큐에서 찾아내서 삭제 (HashMap은 취소할 아이디 추적용으로 만듦)

**결과**

: 타 API 서비스와 해당 노쇼 철회 기능을 함께 돌렸을 때 API 서비스의 응답 시간이 정상으로 회복됨.

<img src = "https://raw.githubusercontent.com/dalcheonroadhead/img-cloud/main/2025-03/회복된 모습.png" width = 50%/>

### B. 1차 고도화

**💭 고민사항**

- 다음과 같이 예약 철회를 포함한 시나리오 테스트를 진행
  목표:  `RPS` = 5000, `오류율` = 0%, `RPT`= 2초 이하, 30초당 300RPS씩 늘리며 부하 진행

<p align ='center' style="display: flex; break-inside: avoid"> 
<img src="https://raw.githubusercontent.com/dalcheonroadhead/img-cloud/main/2025-03/image-20250328034230632.png" width = 30%/>
<img src = "https://raw.githubusercontent.com/dalcheonroadhead/img-cloud/main/2025-03/image-20250328040246197.png" width = 70%/>
</p>



- 현재 로직에서 예약 철회 기능을 포함해 POST 요청이 6개이고, 모두 비동기 쓰레드 혹은 비동기 스케줄러 로직이 있어 **백그라운드 쓰레드를 사용하는 상황**
- 시나리오를 시작하자마자 **`RejectedExecutionException`**에러가 남.  
- 이미 쓰레드 수는 하드웨어의 코어 수인 4개 대비 최대치인 200이라 Worker 스레드 수를 늘리지 않는 선에서 성능 개선이 필요했음.

 **🌱 개선사항**:  부하 내구도 4배 증가 (300RPS ➜ 1200RPS)

![image-20250328040021266](https://raw.githubusercontent.com/dalcheonroadhead/img-cloud/main/2025-03/image-20250328040021266.png)

- 우선순위 큐가 가득차서 진입 실패한 쓰레드에 대해서 retry 로직을 활용해 3번 재시도 하도록 구현
- 지수 백오프를 활용해, 재시도 간격이 점점 벌어지도록 하여, 후진입 요청들과 다시 부하를 일으키는 경우를 피하려고 노력

<p align ='center' style="display: flex; break-inside: avoid"> <img src="https://raw.githubusercontent.com/dalcheonroadhead/img-cloud/main/2025-03/image-20250328040439270.png" width = 50%/><img src = "https://raw.githubusercontent.com/dalcheonroadhead/img-cloud/main/2025-03/RPT.png" width = 50%/></p>

- 1분 41초 즉 1200 RPS까진 버텼으나, 목표치에 턱없이 부족함. 

### C. 2차 고도화 지연 변이 추가

**💭 고민사항**

- retry-로직을 추가한 로직의 RPT를 보면,  300RPS가 추가되는 시점으로 부터 20초 후에, RPT가 크게 급증함.
- 이 현상을 보며 retry 로직이 **부하를 분산하기 보단, 처리를 뒤로 미루는 미봉책**이라 판단
- 여전히 후진입 요청과 맞물려 **부하를 더 과열시키는 현상**을 극복하지 못했음.
- retry를 하면서도 부하 분산을 할 수 있는 방법을 탐색

**🌱 개선사항**:  부하 내구도 4배 증가 (1200RPS ➜ 3600RPS)

- [AWS의 재시도 및 지터를 사용한 백오프 전략](https://aws.amazon.com/ko/builders-library/timeouts-retries-and-backoff-with-jitter/)을 읽고, 기존 지수 백오프를 통한 재시도에 Jitter 난수를 통한 동시성 부하 로직을 구현
- Retry 라이브러리에서 재공하는 백오프 정책과 내용 클래스를 모두 재정의 하여, 1000ms 이하의 임의의 난수값이 지수시간마다 더해지도록 하여 재시도 요청들 또한 재시도 시간을 분산

<p align ='center' style="display: flex; break-inside: avoid"> 
<img src="https://raw.githubusercontent.com/dalcheonroadhead/img-cloud/main/2025-03/RPT%20per%20code.png" width = 50%/>
<img src = "https://raw.githubusercontent.com/dalcheonroadhead/img-cloud/main/2025-03/지연변이_RPT.png" width = 50%/>
</p>

- 4000RPS에 가까워질수록 부하를 못 견디긴 했으나 지난 구현보다 내구도를 3배 개선 및 응답시간도 3600RPS 전까지 800ms 밑으로 안정성을 높임
- 재시도 로직으로 할 수 있는 최적화는 끝났다고 판단, 이후 OS 및 WAS 튜닝을 통해, Active Thread 개수를 늘려, 서버의 기능을 개선할 예정

# 2️⃣ 울반

> **`개요`** : 네이버 밴드보다 학급관리에 특화된 서비스, 핸드폰 태깅을 통한 포인트 획득, 퀴즈 게임 등 추가

## 담당

- 회원 관리 담당 (로그인, 회원가입, 로그아웃, 자동 로그인, ATK 발급)

- 실시간 퀴즈 게임 담당 (선생님이 출제, 반 아이들이 정답 제출)

  

## (1) 회원 관리 

### A. 첫 구현

Oauth2를 활용한 소셜 로그인만 구현.

**💭 고민사항**

- 모바일과 웹 양쪽에서 회원가입 및 로그인이 가능해야 했지만, 모바일은 자체 카카오 SDK를 활용한 소셜 로그인이 이루어져서, 모바일 클라이언트로부터 오는 유저의 소셜 로그인 여부와 회원 정보는 서버 Oauth2로 인증이 불가함.
- 모바일에서 자체적으로 이루어진 소셜로그인 여부를 서버에서 판단하는 로직이 필요하다 판단

**🌱 개선사항**: 모바일 소셜 로그인 여부 100% 확인 가능한 로직 구현

- 카카오는 OIDC를 지원하나, 구현 방법에 대한 가이드가 없어서, [OpenID Foundation 기술 블로그](https://openid.net/foundation/how-connect-works/)를 통해 OIDC 학습 후 OIDC  절차 도입

<img src = "https://raw.githubusercontent.com/dalcheonroadhead/img-cloud/main/2025-03/image-20250328092548624.png" width=50%/>

- 네이티브 앱 유저의 경우, 자체 SDK로 카카오 ATK를 발급 시, ID_token도 같이 발급 받도록 협의
- 네이티브 앱 유저로부터 로그인 완료 및 카카오 인증서버에서 발급한 회원정보와 ID_token을 보내주면, 
  서버가 카카오 인증서버로부터 검증용 공개키 재료(두가지 무작위 소수)를 받음
- 재료 기반으로 RSA 공개키를 생성 및 유저의 회원정보와 ID_token 검증 

**결과**

: 모바일 유저의 소셜 로그인 여부와 인증된 정보 여부 100% 확인 가능

### B. 1단계 고도화: OIDC 키 캐싱

**💭 고민사항**

- 카카오 인증 서버로부터 공개키 재료를 매 인증이 필요할 때마다 받으니, 카카오에서 재료 발급 차단해버림. 
- 문의 결과, API 요청이 너무 자주 이루어지면, API 차단이 이루어지고, 서버 자체 캐싱을 해야한다는 조언을 얻음

**🌱 개선사항**

- REDIS Cashing을 활용하여, OIDC  RSA 키 재료 캐싱
- 만약 캐싱한 재료로 만든 키가, 회원 정보 인증에 실패할 경우만 다시 재발급 하여, 인증 인가 재시도
- 두 번째 시도에서도 인증이 실패한 유저는 인증이 실패한 것으로 간주

###  C. 2단계 고도화: SecutiryFilter 최적화

**💭 고민사항**

- Security Filter를 학습하며 기존 9개의 필터 중  JWT 기반 API 서버에서는 사용되지 않는 필터 6개를 발견 

![image-20250328103706592](https://raw.githubusercontent.com/dalcheonroadhead/img-cloud/main/2025-03/image-20250328103706592.png)

- 분명 사용되지 않으나, 로그를 찍어보니, 모든 Filter를 전부 거치고 있었음. 13개의 필터를 모두 거치는 것은 모든 API 요청에 성능 저하를 일으킬 것이라 판단
- 로그아웃과 자동 로그인, 토큰 재발급등을 위해 매번 RDB에서 회원 정보를 가져와서 인증객체를 만든 후 해당 서비스 로직을 만드는 것은 JWT 토큰 인증만 이루어지면 서비스 로직을 진행해도 되는 상황에서 비효율적이라 판단함. 

**🌱 개선사항**

- 사용되지 않는 필터 6개 비활성화
- RDB에서 회원 정보로 인증 객체를 형성한 후에 이루어지던 로그아웃, 자동 로그인, 토큰 재발급 기능을 `Redis` 에 JWT ATK와 RTK를 저장하여, 서블릿  컨테이너 레벨에서 전부  처리되도록 구현

![image-20250328094332824](https://raw.githubusercontent.com/dalcheonroadhead/img-cloud/main/2025-03/image-20250328094332824.png)

- 필터만 거치는 요청들도 각 요청이 서로 불필요한 필터는 거치지 않도록 순서 최적화 

<p align ='center' style="display: flex; break-inside: avoid"> 
<img src="https://raw.githubusercontent.com/dalcheonroadhead/img-cloud/main/2025-03/image-20250328095446952.png" width = 50%/>
<img src = "https://raw.githubusercontent.com/dalcheonroadhead/img-cloud/main/2025-03/image-20250328095542256.png" width = 50%/>
</p>

필터 레벨로 뺐던 로그아웃, 자동 로그인, 토큰 재발급에 대해서는 평균적인 RPT가 400ms에서 183ms로 2배 이상 개선되었지만, 타 API의 평균 응답시간은 개선이 이루어지지 않았음.



## (2) 퀴즈게임

### 담당

- STOMP Pub / Sub 를 활용해, 사용자로부터 실시간 답변 전송 받고 답변 처리
- 실시간 점수 랭킹 구현
- 오답 모음 생성 후 표시 

### A. 첫 번째 구현

![image-20250328110843926](https://raw.githubusercontent.com/dalcheonroadhead/img-cloud/main/2025-03/image-20250328110843926.png)

- gamer로부터 온 답변을 질문 및 답지와 대조하여 답변 계산, 정답 답변에 한하여, 먼저 온 순서대로 더 10등씩 잘라서 배점을 더 줌.
- 이후 Spring 기본 자료 구조인, ArrayList에 랭킹과 오답을 각각 담은 뒤 랭킹은 높은 배점 순으로 정렬 하여 다시 구독자들에게 전송

**💭 고민사항**

- Socket의 경우, Header가 없으므로, 기존 HTTP 요청보다 전송 및 답변 속도가 2~3배 이상 빨라야 하지만, 현재 로직에서는 `RDB에서 답지와 질문 가져오기` ➜ `답변 계산 및 점수 채점` ➜ `랭킹 정렬, 오답 모음집 만들기` 라는 서비스 로직을 거쳐야 해서 평균 응답 시간이 2300ms 가 나왔음.
-  실시간성이 중요한 서비스 특성상 답변이 1초 이내로 줄어들어야 한다 판단

**🌱 개선사항**: 평균 RPT  개선 (2300ms ➜ 400ms)

- `부하 테스트 진행`
  : `Thread 수`= 100개, `ramp-up`=100,  `300RPS`, `10분 지속`
- 첫 구현: `평균RPT` = 2300ms, `max RPT` = 4500ms
- 이후, Redis의 **ZSET**과 Caching을 활용하여 개선

![image-20250328131928600](https://raw.githubusercontent.com/dalcheonroadhead/img-cloud/main/2025-03/image-20250328131928600.png)

- 선생님으로부터 퀴즈게임 시작 요청이 오면, 미리 모든 질문과 답지를 redis에 적재
- 답변 계산 후 정답자, 오답자 리스트 정리 ➜ 오답자=`Redis SADD SET` 사용 (O(N))
- 정답자 점수 기반 리스트업: SKIP LIST 기반 SORTED SET인  **`ZSET`**으로 정리 (O(liogN) + O(log N + M))
  (N = 유저 수, M = Reda 후 반환)
- 정답자, 오답자 리스트 정리에서는 속도 차이가 크지 않았으나, 정답자 리스트의 경우 적재와 동시에, 정렬이 이루어졌기 때문에, 속도 개선이 됨.
- 동일 테스트 진행 시 : 400ms로 개신

### B. 2단계 고도화: Kafka 추가

![image-20250328135246647](https://raw.githubusercontent.com/dalcheonroadhead/img-cloud/main/2025-03/image-20250328135246647.png)

**💭 고민사항**

- 답변을 빨리한 순으로 가점을 주어서, 랭킹이 높아지는 모델인데, 서버에서 답변 오는 순서를 제대로 포착하지 못해, 같은 테스트에서도 랭킹이 비일관적인 문제
- socket PUB로 답변이 오는 순서를 보장하는 것이 중요 했음. 

**🌱 개선사항**: Kafka 활용 메시지 순서 100% 보장 

- 직렬성 보장을 위해 Kafka Partition을 활용 하나의 파티션은 RR(RoundRobin)을 써서, TimeStamp가 빠른 순으로, 정렬을 보장. 
- **회고**
  - 해당 방식 이후 동일 부하 테스트에서 `MAX RPS`가 1800ms까지 나오며 다시 성능이 좋아지지 않음.
  - SSAFY 컨설턴트와 여러 면접관들에게 모놀리식에서 이러한 Kafka의 사용은 앞뒤가 맞지 않는다. 라는 의견을 많이 듣고,  다시 학습함
  - 이후 Redis를 학습하여 Redis 하나만으로 STOMP protocol과 ZSET을 활용한 랭킹, REDIS STREAMING을 활용한, PUB, SUB온 메시지의 순서 보장도 가능함을 알게됨. 
  - 꼭 미들웨어를 많이 활용하는 것이 고도화가 아님을 깨달았고, 리팩토링할 시간적 여유가 생기면 REDIS로 재구현할 예정 



# 3️⃣ WALK WALK

>  **`개요`** : 어르신 특화 런데이 앱, 가족 친구들과 운동하면서 소통 (STT, TTS 채팅방), 운동이 끝나면 용돈 지급

### 담당

- 운동 중에 채팅을 치기 어려운 어르신들이 음성을 남기면, 음성이 채팅으로 변환, 채팅을 선호하는 X및 MZ 세대가 채팅을 남기면, STT로 부모님께 음성으로 전달. (채팅방에 메시지 내역은 그대로 쌓임)

### A. 첫 구현

- 채팅은 STOMP, 음성 파일은 HTTP 요청으로 전송

-  `AWS TRANSCRIBE`라는 TTS, STT 양방향 서비스에 동기적으로  파일 변환을 요청하여 음성  ⬅➡ 채팅 변환

**💭 고민사항**

- 최종 응답시간에 평균 20초가 걸리며, 도저히 서비스를 할 수 없는 구조임.
- 개선책이 필요

### B. 1차 고도화

- 프론트팀과 협의 후 프론트에서 채팅 음성 파일을 BLOB 파일로 변환 후 BASE 64로 변환한 뒤, 7500자 단위로 쪼개서 전달 하도록 설계 
- 7500자 단위의 BASE 64 STRING을 마지막 단위까지 받으면, 서버에서 다시 음성 파일로 변환

![image-20250328145945661](https://raw.githubusercontent.com/dalcheonroadhead/img-cloud/main/2025-03/image-20250328145945661.png)

**🌱 개선사항**: 평균 응답 시간 17,583초로 개선

- HTTP 요청을 하지 않고, STOMP만을 활용하여, 음성 파일을 받는 로직으로, PUB,SUB 단계에서의 속도를 줄임.

**💭 고민사항**

- 하지만 음성파일 크기가 1분 이상 길어지면, Base 64 chunk가 4 덩어리가 넘어감. 이때부터, STOMP를 쓰는 시간적 이득이 HTTP랑 같아지는 현상 발견
- 동기적인 기다림을 해결하지 못함. ➜ 다른 서비스에 부담

### C. 2차 고도화

![image-20250328150326166](https://raw.githubusercontent.com/dalcheonroadhead/img-cloud/main/2025-03/image-20250328150326166.png)

- AWS Lambda를 활용하여, AWS TRANSCRIBE에서 TTS 변환이 끝나면, 해당 JSON 파일을 S3에 지정한 ID+TimeSTAMP 조합으로 저장되도록 조치
- 음성 파일의 TEXT 변환 정확도를 얼마나 낮췄을 때 서비스에 이상이 없는지 확인  65%까지 정확도를 낮추고, 속도 상승
- 음성 파일 요청이 끝난 후, Spring  내의 AWS Lambda 라이브러리 + 비동기 스케줄러 Comparatable Future를 활용하여 원하는 특정 아이디와 TimeSTAMP의 파일이 있는지 5초마다 Pulling으로 확인 

**🌱 개선사항**: 평균 응답 시간 7초로 개선

- 음성 정확도를 줄임으로써, 변환 속도를 개선함.
- 변환 파일이 S3에 적재되며, 파일 손실을 100% 예방할 수 있었음. 
- AWS Lambda를 활용하여, 모든 일의 변환 상태 (진행 중, 완료, 실패, 에러)를 확인함으로써, 변환 실패 시 재시도 로직 구현 및 클라이언트에게 변환 상태를 SOCKET으로 알릴 수 있었음.
- 먼저 음성 파일 자체를 전해주고, 변환 상태를 알려서, 클라이언트에게 신뢰성을 줌.

