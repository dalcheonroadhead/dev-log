# 0. 들어가며...

이번에 새로 팀 프로젝트를 하면서, 노션을 통해 프로젝트 관리를 하게 되었다. `JIRA`나 `MATTERMOST` 같은 협업툴을 지원해주던 **SSAFY**와 달리, 구름 부트 캠프는 그런 것이 없어, **NOTION**을 통해 직접 만들어야 했다. 그간 노션 활용력이 많이 높아졌으니, 좋은 기회라 여겼다. 오늘은 <u>1️⃣ 노션의 관계형 데이터 베이스를 활용한 스프린트 관리</u>와 <u>2️⃣ 수식을 활용한 가시성 높이기</u>를 한 번 해보려 한다.

# 1. ERD 설계

관계형 DB를 계획해서 사용하려면 역시 설계부터 해야한다. 그래서 `ERD CLOUD`를 키고, DB 설계를 시작했다. 설계 전에는 설계의  **목표**를 세우고 시작해야한다. 내가 세운 목표는 다음과 같다.

- 1️⃣ 팀 멤버 별로 자신이 해야할 일이 귀속되어있다. 
- 2️⃣ 진행 여부를 체크하면, 진척도와 계획률을 계산할 수 있다.

![image-20250201192823080](../../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/DAY_0_Notion_setting/image-20250201192823080.png)

이후 위와 같이 ERD를 설계했다. 노션은 1대1, 1대다, 다대다 전부 가능하다. 이를 이용하여, Member와 Sprint를 1대다로 연결하여, 멤버 별로 여러 개의 스프린트를 진행할 수 있도록 했고, 스프린트는 또한 여러 개의 세부 할일로 나눠지게 하였다.

# 2. DB 간의 관계 설정

**노션에서는 관계를 거는 쪽이 자식 테이블**이다. 따라서 나는 위의 그림에서 `SPRINT 테이블`에 해당하는 `SPRINT DB`에 들어가 다음 과정을 진행했다.

⑴ 관계형을 누른다.

![image-20250201000353011](../../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/DAY_0_Notion_setting/image-20250201000353011.png)

⑵ 관계를 맺을 DB를 선택한다. 여기서 선택하는 DB 테이블이 바로 **부모 테이블**이 된다.

![image-20250201000438058](../../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/DAY_0_Notion_setting/image-20250201000438058.png)

⑶ 제한은 '제한 없음', 양방향 관계는 '활성화' 시킨다.

![image-20250201000810052](../../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/DAY_0_Notion_setting/image-20250201000810052.png)

원래 DB를 짜면, 부모 테이블의 Key가 자식 테이블에 외래키로 삽입된다. 이는 노션도 똑같다. 다만, 노션에서는 **양방향 관계를 설정하면, 자식의 KEY가 부모 테이블의 Attribute로 나타나게 할 수 있다.** 사실 안해도 되지만, 나중에 부모 테이블에서 작업할 때, 관계의 가시성을 보이기 위해 활성화 시켰다.

⑷ 위의 과정을 진행하면 `SPRINT`(자식 테이블)에 외래 키로 부모 테이블의 KEY가 들어가게 된다.

![image-20250201000836892](../../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/DAY_0_Notion_setting/image-20250201000836892.png)

위 사진은 들어간 모습이고, 이 외래키의 이름을 편의에 맞게 고칠 수 있다. 그저 Member라고 두는 것은 직관적이지 않아서, 나는 **담당자**라고 고쳤다.

![image-20250201001002449](../../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/DAY_0_Notion_setting/image-20250201001002449.png)

⑸ **`To do Table`**을 만들어 연결시키는 과정도 위와 같이 하면 된다. 하나 살펴볼 것은 `To do Table`의 계획 여부 attribute일 것이다. 이를 그저 체크 boolean으로 나타낸다면, 계획을 하고 사용자가 직접 boolean을 true로 만들어줘야 하니, 휴먼 에러가 날 가능성이 많고, 귀찮다. 따라서 필자는 **노션의 수식**을 활용했다. 
![image-20250201201255526](../../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/DAY_0_Notion_setting/image-20250201201255526.png)

위와 같이, 계획날짜가 비어있지 않은 값은 true가 되도록 설정하면, 계획 날짜를 작성하는 순간 계획 여부는 true로 바뀔 것이다.

이후의 각 테이블의 추가 Attribute를 만드는 과정과 연결은 위와 같은 과정의 반복이므로, 생략하고 넘어가겠다.

# 3. Roll up을 통해 통계 나타내기

노션에서 관계형 데이터 베이스를 활용할 수 있는 또 한 가지 기능으로 `Roll up`이 있다.  

### `Roll up`이란?

한 테이블의 Record가 자신과 연관된 부모 혹은 자식 테이블의 record의 속성을 가져와 표시하는 기능

우리는 `Roll up`을 활용하여 ERD 설계의 두 번째 목표였던 <u>(1) Sprint 별 계획률</u>과 <u>(2) Sprint 별 계획 진척도</u>, <u>(3) 멤버 별 진척도</u> 를 나타내려 한다. 

## (1) Sprint 별 계획률 나타내기

다시 Sprint DB 테이블로 돌아오자. 

![image-20250201200449581](../../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/DAY_0_Notion_setting/image-20250201200449581.png)

Attribute를 추가하고, roll up을 선택한다. 

![image-20250201200732178](../../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/DAY_0_Notion_setting/image-20250201200732178.png)

이후 위에 체크한 3가지를 원하는 대로 설정하면 된다. 나는 나타낼 관계 DB를 Sprint의 자식 테이블인 To-do로 선택하였고, 자식 테이블에서 나타낼 속성을 `계획 여부` 로 선택, 계산은 **하나의 스프린트에 연관된 모든 계획 수**로 설정하였다. 이와 똑같은 방법으로 **계획된 To-do 수**도 똑같이 체크해주면 되겠다. 

![image-20250201201520018](../../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/DAY_0_Notion_setting/image-20250201201520018.png)

이제 이 둘을 활용해 비율을 계산할 수식 attribute를 하나 만들면 된다. **Roll up 속성의 `비율로 나타내기` 기능을 따로 사용하지 않는 이유는 가시성 때문**이다. 나중에 표가 아닌 갤러리 레이아웃이나, 리스트 레이아웃을 쓸 때, 해당 기능으로 나타낸 계획률은 그저 숫자만 표기되어 있어, 무엇에 대한 숫자 표기인지 사용자가 이해하기 힘들다. 이 때문에 간단한 단어와 emoji를 쓸 수 있는 수식 속성을 하나 더 만들어 표기하는 것을 선택했다.

![image-20250201202117577](../../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/DAY_0_Notion_setting/image-20250201202117577.png)

내 수식은 간단하게 ($\frac{계획된 할 일 수}{전체 할 일 수}$)로 표기했다.

## (2) 계획 진척도 나타내기

계획 진척도도 같은 과정, 같은 방법으로 하면 된다.  설명은 생략하고 과정 스크린 샷만 올리겠다. 전체 할 일 수는 `Sprint 전체 계획 수`라는 attribute에서 세어주고 있으니, 우리는 `완료 표시된 할 일 수`만 세어줘서 다시 수식으로 나타내면 된다.

![image-20250201202647745](../../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/DAY_0_Notion_setting/image-20250201202647745.png)

![image-20250201202839116](../../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/DAY_0_Notion_setting/image-20250201202839116.png)

**노션의 `상태` 속성을 `roll up`으로 나타냈을 경우,** 모든 값을 **String** 형태로 나타낸다. 따라서 Notion에서 String을 Integer로 변환시켜주는, `toNumber()`라는 함수를 활용해 변환 시켜준 뒤에 사칙연산을 해야 한다.

## (3) 멤버 별 진척도 나타내기

이번에 나타내려고 하는 것은, **멤버 별 자신이 진행 중인 전체 Sprint 할 일에 대하여 진척도가 어느 정도 인가** 이다. 이를 나타내려면, `Sprint table`의 `roll up` 속성들을 가져오면 쉽겠구나 할 수 있지만, **자식 테이블이 손자 테이블에서 가져온 롤업 속성들은 부모 테이블에서 가져오지 못한다.** 따라서 우리는 **수식을 활용하여 이를 우회 하겠다.**

### ⓐ 수식으로 `전체 계획 수`와 `완료된 할 일 수`를 똑같이 가지고 있는 속성을 2개 더 만든다.

이는 그저 **부모 테이블인 Member에서 나타내기 위한 용도**이다.

![image-20250201204015074](../../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/DAY_0_Notion_setting/image-20250201204015074.png)

![image-20250201204219648](../../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/DAY_0_Notion_setting/image-20250201204219648.png)

### ⓑ 멤버 테이블에서 각 멤버 별 `전체 할 일 개수`, `완료된 할 일 개수`를 합쳐서 나타내기

멤버 별로 Sprint가 여러 개일 것이므로, 그저 숫자만 들고 온다면, `5, 3, 2, ...` 과 같이 나타날 것이다. 우리는 그것들의 합계를 나타내고 싶다. 여기서 반가운 것은 Notion에는 합계를 내주는 기능도 있다! (대단한 노션...)

![image-20250201205245977](../../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/DAY_0_Notion_setting/image-20250201205245977.png)

![image-20250201205404647](../../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/DAY_0_Notion_setting/image-20250201205404647.png)

### ⓒ 멤버별 진척도 수식으로 나타내기

![image-20250201205730716](../../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/DAY_0_Notion_setting/image-20250201205730716.png)

# 4. 노션 CRM 가시성 높게 꾸미기 

## (1) 개인별 SPRINT 설정 및 To-do-List 계획 세우기

![image-20250202013451396](../../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/DAY_0_Notion_setting/image-20250202013451396.png)

![image-20250202013411365](../../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/DAY_0_Notion_setting/image-20250202013411365.png)

위는 개인별 프로필을 눌렀을 때, 나오는 화면이다. 담당자의 `SPRINT 목록`, `날짜 설정이 안된 TO-DO LIST`, `TO-DO 계획 일정`이 각각 적혀있다. 위의 그림처럼 날짜 설정이 필요한 `TO-DO`를 누르고 **캘린더에 DRAG & DROP**하면 계획 날짜가 설정된다.

## (2) 멤버 별 SPRINT 목록 및 CAN-BAN BOARD

![image-20250202013733294](../../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/DAY_0_Notion_setting/image-20250202013733294.png)

멤버별로 계획을 얼마나 했고, 얼마나 달성 했는지를 보여주는 보드 표이다. 계획률은 모든 계획이 캘린더에 기록 되었을 때, 100%로 찍힌다.

![image-20250202013657685](../../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/DAY_0_Notion_setting/image-20250202013657685.png)

`보드 레이아웃`을 활용하여 **칸반보드**를 만들었다.

## (3) 멤버별 기술 로그 만들기

![image-20250202171807387](../../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/DAY_2_Notion_setting/image-20250202171807387.png)

위와 같이 TagLog 용 테이블을 하나 더 만들어서 멤버 테이블과 연관시켜줬다. 이를 통해 각 멤버별로 작성한 기술로그, 회고, 데일리스크럼이 각 멤버에게 귀속 되도록 했다.

![image-20250202171907477](../../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/DAY_2_Notion_setting/image-20250202171907477.png)

![image-20250202172016145](../../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/DAY_2_Notion_setting/image-20250202172016145.png)

다음 그림처럼, 로그 별로 양식을 만들어, 유형 별로 통일성이 있도록 조치했다.

