# 0. 개요

오늘은 <u>1️⃣ PRD(요구사항 정의서)</u>와 <u>2️⃣  API 명세서</u>를 팀 노션에 부착하여 노션을 업그레이드 했다. 그간 팀 프로젝트를 하면서 느꼈던 불편했던 점들을 생각해보고, 그것들을 개선하는 형식으로 PRD와 API 명세서를 구현했다. 내가 불편함을 느꼈던 사항은 다음과 같다.

# 1. 불편 했던 점

- **1️⃣ 요구사항 정의서 DB와 API 명세서 DB가 따로 논다.** 
  둘이 연관관계가 없으니, API 명세서가 요구사항 정의서의 요구사항을 잘 이행하고 있는지 확인이 어려웠다. 따라서 요구사항 준수 여부를 확인하기 위해 (1) 두개의 표를 띄우고, (2) 연관 있는 것을 사람이 직접 찾고, (3) API가 요구사항을 벗어났는지 아닌지 확인하는 식으로 진행 했다. 이러니 초반에는 구현 속도가 더뎠고, 후반에는 요구사항을 내팽겨친 채로 구현을 진행했다. 
- 2️⃣ API 혹은 요구사항 담당자 선택이 어렵고 실수가 많다. 노션의 경우 `담당자` 태그를 활용하면 팀 스페이스에 초대된 모든 멤버 혹은 게스트를 *언급* 할 수 있다. 근데 이게 **30명 이상의 사람이 같은 팀 스페이스를 활용하거나, 회사의 경우** 팀과 관계 없는 엉뚱한 사람을 언급하는 일이 많아지게 된다. 이는 잘못된 알림을 보내서 회사 구성원을 피곤하게 만들고, 사용하는 당사자도 담당 태그를 누를 때마다 예민해져야 한다.

# 2. 개선 방향

따라서 필자는 다음과 같이 노션 ERD를 바꾸어 위의 **2가지 불편사항**을 개선했다.

![image-20250202230954952](../../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/DAY_3_PRD&API 명세서 세팅/image-20250202230954952.png)

- 1️⃣ 요구사항이 부모 테이블, API 명세서가 자식 테이블이 되어 서로 연관관계로 묶는다. 또한 요구사항이 자식이 되어 멤버 테이블과 연관지어, **멤버 별로 자신의 요구사항과 구현해야할 API 목록을 볼 수 있게 만들었다.**
- 2️⃣ 1번의 연관 관계가 생기면서 자연스럽게 두 번째 불편사항이 해결되었다. 더 이상 **노션 접속 계정 언급이 아닌 멤버 테이블의 record 언급으로 바뀌었기 때문이다.**

이제 이들을 구현한 방법을 소개하겠다. 다만 **관계형 DB 설정 방법**은 간소히 보고 넘어가겠다. 왜냐하면 이미 지난 포스팅에서 다뤘기 때문이다.

https://dalcheonroadhead.tistory.com/611

# 3. 요구사항 정의서 구현

위의 ERD를 보면, PRD 테이블의 외래키가 2개인 것을 알 수 있다. ERD 명세처럼 **멤버 테이블을 중복하여 부모 테이블로 가져야 한다.** 이는 프론트 담당과 백엔드 담당이 나뉠 경우를 대비하기 위함이다. 뭐 별다른 건 없고, 관계형 속성 설정을 Member가 부모가 되도록 2번 반복하면 된다.

![image-20250202231527242](../../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/DAY_3_PRD&API 명세서 세팅/image-20250202231527242.png)

## (1) 종속 record 설정

노션의 기능 중 하나로 **record 별로 상위, 하위 종속성 설정이 가능하다.** 종속성은 셀병합이 안되는 노션 DB 테이블에서 같은 속성을 가진 항목을 묶기 위해 사용되어진다.

설정 방법은 사용자 지정에 들어가서

![image-20250202231731690](../../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/DAY_3_PRD&API 명세서 세팅/image-20250202231731690.png)

![image-20250202231753370](../../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/DAY_3_PRD&API 명세서 세팅/image-20250202231753370.png)

하위 항목을 활성화 하면 된다. 이러고 DB의 record를 만들면,

![image-20250202231822653](../../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/DAY_3_PRD&API 명세서 세팅/image-20250202231822653.png)

위와 같이, **대분류, 중분류,소분류로 세세하게 나누어 표현할 수 있다.**

# 4. API 명세서 구현

api 명세서는 부모테이블로 `PRD TABLE`을 갖도록 해주면 된다. 방법은 위에 다 설명했고, attribute만 만들어주면 되는 것이라서 과정 설명은 생략하겠다.

![image-20250202232007826](../../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/DAY_3_PRD&API 명세서 세팅/image-20250202232007826.png)

## (1) API 페이지 템플릿

이제 알아볼 것은 DB의 record 즉 API 하나하나의 페이지 양식을 알아보려 한다. API 마다 **`Request 시 보낼 Head, body 항목`**과 **`Response 시 받는 양식`**이 다를 수 있다. 따라서 이를 **모든 멤버가 알아보기 쉽게** 템플릿을 작성하는 것이 유용하다.

필자는 다음과 같이

![image-20250202233015106](../../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/DAY_3_PRD&API 명세서 세팅/image-20250202233015106.png)

`key - value 자료형 명세` 와 `실제 데이터가 들어온 예시`로 head, body를 각각 작성하여 보내줬다.

# 5. 소결

이번 붙캠을 계기로 그 동안 불편했던 PM 설정을 직접 해보며 **`관계형 데이터 베이스` 활용 능력**과 **`노션` 활용 능력**을 키울 수 있었다. 노션은 메모앱인데도 관계형 데이터 베이스를 구현해놔서, 개발자들이 DB 지식을 실습하는데 활용하기 정말 적합한 것 같다.
  하지만 여기서 노션 세팅을 끝마치는 것은 아니고, 팀원들의 피드백을 받고 더 최적화 하려 한다. 아 그리고 위와 같이 요구사항 정의서의 항목을 구현하기 위해 현직자 PM 분이 작성한 ['요구사항 정의서 작성법과 양식: IT 기획자 20년의 노하우를 전해드립니다.'](https://www.elancer.co.kr/blog/detail/283)를 참고 했다.

