# 1. 문제 설명 📌

[문제 링크](https://www.acmicpc.net/problem/17976)

## 문제

`x` 중심선 위에 n개의 Thread가 존재한다. $T\_{i}$라 불리는 i번째 Thread의 길이는 $l\_{i}$와 해당 Thread의 시작 지점 위치인 $x\_{i}$로 나타내어 진다. 두 변수 모두 Integer 이다. 우리는 각각의 Thread 마다 매듭을 짓고 싶어 한다. 매듭의 위치 또한 반드시 Integer여야 한다. 매듭은 Thread 안의 어느 지점에서든 상관 없이 만들어질 수 있고, Thread의 길이가 매듭에 의해 줄어들지 않는다고 가정한다. 당신은 또한 어떠한 Thread도 또 다른 Thread에 의해 완전히 포함되어지지 않는다고 가정한다. 어떤 의미냐면, $x\_j$ <= $x\_i$ 이면서 $x\_i$ + $l\_i$<=  $x\_j + l\_j$ 인 $T\_i$와 $T\_j$는 존재하지 않는다. 

우리는 가장 가깝게 인접한 두 매듭 사이의 거리를 가능한 한 크게 만들기 위해서 각 Thread 마다의 매듭의 위치를 결정하고 싶다. 

예를 들어, 아래의 Figure 그림은 6개의 Thread에 대한 매듭의 위치를 보여주고 있다. 매듭의 위치는 포인트로 나타내어 진다. 모든 Thread는 x-중심선에 평행하다. 하지만 그들은 서로 서로 구분되어지도록 분리되어 그려져 있다.  Figure l.1 그림을 보면, 가장 가깝게 인접한 두 매듭 사이의 거리는 20이다. 하지만 만약 우리가 $T_{2}$의 매듭을 FigureI.2의 그림처럼 위치를 달리한다면, 가장 인접한 두 매듭 사이의 거리는 25가 될 것이고, 이것이 문제에서 요구하는 바이다.



n 개의 thread에 대한 정보가 주어질 때, 가장 가까운 두 매듭 사이의 거리가 최대가 되도록 계산하는 프로그램을 작성하여라

## 입력

당신의 프로그램은 표준적인 입력으로 읽어간다. 입력은 하나의 Integer n(Thread의 개수)을 포함하는 하나의 줄로 시작한다. (2 <= n <= 100,0000) 다음은 n개의 줄로 이어지며, i 번째 줄은 두개의 Integer $x\_i$와 $l\_i$가 주어진다. $x\_i$와 $l\_i$는 각각 i 번째 Thread의 시작 지점과 길이를 나타낸다. 당신은 하나의 Thread가 다른 Thread에 의해 완전히 포함되어질 수 없다고 가정한다. (무슨 뜻이냐면, $x\_j$ <= $x\_i$ 이면서 $x\_i$ + $l\_i$<=  $x\_j + l\_j$ 인 $T\_i$와 $T\_j$는 존재하지 않는다. )

## 출력

당신의 프로그램은 표준적인 출력으로 적어간다. 정확히 하나의 줄만 출력해라. 해당 줄은 가장 가까운 두 매듭 사이의 최대 거리를 뜻하는 Integer 값을 포함해야 한다.

# 2. 접근 방식 🗃️

**`KEY WORD`: `PARAMETRIC SEARCH`, `GREEDY ALGORITHM`**

- (1) 이분 탐색에 `start`, `end`, `mid`가 있을 때 `mid` 값을 활용해 매듭 사이의 임의의 거리 `distance`를 만들어 낸다.
- (2) 해당 `distance`가 매듭 사이의 최소 거리라고 가정 할 때, 모든 매듭 사이 거리가 `distance` 이상이 될 수 있는지 체크한다.
- (3 - 1) 된다면 `distance`를 좀 더 길게 해도 된다는 뜻이다. 따라서 **`Start` = `mid` + 1** 로 만들고, (1)번을 반복한다. 
- (3 - 2) 안된다면 `distance`를 좀 더 짧게 조정 해야 한다. 따라서 **`end` = `mid` - 1** 로 만들고 (1) 번을 반복한다.
- (4) 이 과정을 `start`와 `end`라는 포인터가 서로 엇갈릴 때까지 반복하면, 종국에 `start`는 답이 될 수 있는 distance의 최대값보다 한 칸 앞을 가리키게 된다. (upper bound)

## (1) < 이분 탐색을 어떻게 쓸 것인가? > Parametric Search에 대하여

해당 문제를 보면, 이분 탐색을 써야 한다는 것이 직관적으로 느껴진다. 하지만 어디에 어떻게 써야 할지 감이 안 잡힐 수 있다. 왜냐하면 문제가 **'최소값의 최대값을 구하여라.'**라서 이분탐색으로 찾아야 할 것이 두 개 처럼 느껴지기 때문이다. 이때는 이분탐색의 접근법 중 하나인 **`Parametric Search`**를 활용해볼만 하다.

### Parametric Search란?

**하나의 최적화 문제를 여러 개의 `결정 문제`로 바꾸어서 문제를 풀어가는 접근법**이다. 

<details> 
    <summary>최적화 문제와 결정 문제에 대한 개념</summary> 
    <div markdown="1">
        **최적화 문제**란 최소값, 최대값을 구하는 것과 같이, **답이 될 수 있는 범주 안에서 최적의 해를 구하는 문제**를 말한다. 
반면, **결정 문제**는 'a+b를 35로 만드는 a,b 쌍을 구하라', '모든 매듭 간의 거리가 d 이상이면 Yes 아니면 no를 출력하라' 등등 **정확한 해가 존재하거나 Yes or No로 답이 가능한 문제**를 말한다.  
    </div> 
</details>

해당 문제에서는 **` 매듭 사이 거리 최소값들 중에서 최대값 반환`**을 여러 개의 **`모든 매듭 사이 거리를 d 이상이 되게 하고 싶은데, 성립하는가? `**라는 **Yes or No** 문제로 바꾸면 된다.

![image-20250110130530922](../../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/[백준] 17976 Thread Knots/image-20250110130530922.png)

위의 그림처럼 거리가 d일 때, 모든 매듭 사이에서 그 거리 이상이 가능한지 안되는지를 boolean으로 반환하는 `F(d)`를 우리가 구해놨다고 가정하자. 그러면 우리는 f(0) ~ f(2억)을 일련의 배열로 구해서, 여기서 이분탐색을 하면 된다.

![image-20250110130714536](../../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/[백준] 17976 Thread Knots/image-20250110130714536.png)

이분 탐색이 가능한 이유는 여러 개의 결정 문제의 반환 값이 **`단조적인 성질`**을 가지기 때문이다.
   `f(x) = O` 라면, f(0) 부터 f(X-1) 까지도 O이다. 왜냐하면, 거리 3을 최소 거리로 모든 매듭 사이 거리가 가능하다면 거리가 0인 경우, 1인 경우, 2인 경우에 대해서 무조건 성립하기 때문이다. 
  반대로 `f(x) = X`라면, f(x+1) ~ f(2억) 까지는 x이다. 왜냐하면 모든 매듭 사이의 거리를 최소 4 이상으로 두는데 실패 했다면, 5로도 안되고 더 나아가 2억으로도 안된다는 것을 알 수 있기 때문이다. 

  **단조적인 성질**때문에 우리는 값을 예측할 수 있게 된다. 이제 우리는 조건을 성립하는 값 중 제일 오른쪽에 있는 값을 구하면 된다. 그 `f(d)`의 d가 바로 답이 되기 때문이다.

>  (PS) 모든 최적화 문제를 여러 개의 결정 문제로 변환할 수 있는 것은 아니다. Parametric Search를 쓰기 위해서는 위에서 살펴 보았듯이 `2 가지` 조건이 필요하다. 더 자세히 알고 싶은 분들을 위해 Parametric Search에 대한 링크를 남겨두도록 하겠다.

## (2) `f(d)`는 어떻게 구하나요? 

f(d)를 구하기 위해서 **`Greedy Algorithm`**을 사용해야 한다.

**매듭은 최대한 현 Thread의 왼쪽**에 놓는 것이 좋다. 왜냐하면 그렇게 둘수록, 다음 Thread에 매듭을 둘 때, 가용 범위가 넓어지기에 f(d)를 만족시킬 확률이 높아지기 때문이다.  

![image-20250110132323605](../../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/[백준] 17976 Thread Knots/image-20250110132323605.png)

k1,k2,k3가 각각 0, 25, 50에 위치하면서 d = 25 이상을 만족한다. 물론 k2와 k3를 오른쪽으로 옮겨서도 가능하지만, 다음과 같이 k2의 Thread가 25 ~50 사이에서만 유지될 경우 안될 것이다.

![image-20250110132550944](../../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/[백준] 17976 Thread Knots/image-20250110132550944.png)

위와 같이 최대한 왼쪽에 두었을 때, `f(d)`를 성립하는 경우가 더 많다면, 그것이 최적의 선택이다. 그리고 최적의 선택이 누적된 것이 `f(d)`의 true 값 반환 확률을 높임으로, 이것은 Greedy 알고리즘을 활용해도 된다는 것을 알 수 있다.

## (3) 그러면 `f(d)`를 0부터 2억까지 구해야 하나요? 

아까 f(0) ~ f(2억)까지 중 이분 탐색을 해야한다고 해서 이러한 궁금증이 떠오를 수 있겠다. 하지만 이는 시간초과가 날 뿐더러, 2억 개의 boolean 값을 저장해야 하므로 2GB를 필요로 하기에 메모리 초과 또한 난다. (1byte * 2억)

 **사실 전부 구할 필요가 없다.** 왜냐하면, 바로 **Parametric Search의 단조적인 성질** 때문이다. 아까도 봤듯이 f(x)의 답이 false면 그것 이상의 값들은 모두 false 였다. 따라서 그 부분은 구할 필요 없이 영역을 줄여나가면 되기 때문이다.

# 3. 코드 소개 🔎

```java
import java.io.*;
import java.util.ArrayList;
import java.util.Arrays;
import java.util.Collections;
import java.util.StringTokenizer;


public class Main {
    static ArrayList<Th> list = new ArrayList<>();
    public static void main(String[] args) throws IOException {
        // 입력 받기
        int t_cnt;
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        t_cnt = Integer.parseInt(br.readLine());
        for (int i = 0; i < t_cnt; i++) {
            StringTokenizer st = new StringTokenizer(br.readLine());
            list.add(new Th(Integer.parseInt(st.nextToken()), Integer.parseInt(st.nextToken())));
        }
        Collections.sort(list);
        System.out.println(binary_search());
    }

    public static int binary_search() {
        int start = 0;
        int end = 2000000000;

        while (start <= end) {
            int mid = start + (end - start)/2;
            if(isOk(mid)) start = mid + 1;
            else end = mid - 1;
        }
        return start - 1;
    }

    public static boolean isOk(int distance) {
        ArrayList<Integer> knots = new ArrayList<>();
        knots.add(list.get(0).s);
        for (int i = 1; i < list.size(); i++) {
           Th now_Th = list.get(i);
           int now_knot = now_Th.s;
           int prev_knot = knots.get(i-1);

           if(now_knot - prev_knot < distance){
               now_knot = prev_knot + distance;
               if(now_knot > now_Th.e){
//                   System.out.println(now_Th + " " + prev_knot + " " + now_knot);
                   return false;
               }
           }
           knots.add(now_knot);
        }
        return true;
    }
}

class Th implements Comparable<Th> {
    int s;
    int e;

    public Th (int s, int l) {
        this.s = s;
        this.e = s + l;
    }

    @Override
    public int compareTo(Th o) {
        return this.s - o.s;
    }

    @Override
    public String toString() {
        return "s=" + s + " e=" + e;
    }
}
```

# 4. 배운 것들 🎯

- `axis` : 중심선
- `denote`: 나타내다.
- `knot`: 매듭
- `assume`: 추정하다. 가정하다.
- `totally`: 완전히
- `contain`: 포함하다.
- `respectively`: 각기, 각각

>  이모지 모음: 🤔, ➜ ✨