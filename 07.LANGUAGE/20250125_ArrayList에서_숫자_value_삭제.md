# 0. 들어가며...

해당 글은 **`ArrayList<Integer>`**에서 원하는 숫자를 지우는 방법에 대한 글이다. `ArrayList.remove()` 함수로 지우면 되지 않겠냐 생각할 수 있지만 `.remove(int i)`를 넣으면, remove의 오버로딩된 함수 중 `ArrayList.remove(int index)`가 호출되어, **`i`를 index로 가지는 Value를 ArrayList에서 삭제한다.** 따라서 원래 의도한  **'i라는 Value 지우기'**는 실패한다.

# 1. `ArrayList.remove(int index)`

위에서 설명했듯이 remove에 **원시 자료형 int**를 넣으면 **해당 인수를 index로 가지는 Value를 ArrayList에서 삭제**한다.

# 2. `ArrayList.remove(Object o)`

우리가 실제 **`i라는 Value`**를 삭제하고 싶다면, 해당 함수가 호출되어야 한다. `ArrayList.remove(Object o)`는 **`o`라는 객체와 `동등한` 객체를 지우는 함수**이다. 이때 동등성 여부는 `Object o의 equals()`함수를 활용한다. 즉 지우고자 하는 객체가 Custom한 객체이면 제대로 된 `remove()`함수를 쓰기 위해 **`equals()` 함수를 Overriding 해야한다.**

그렇다면 우리가 `ArrayList<Integer>`에서 지우고자 하는 value인  `int i`는 어떻게 지울까? 계속해서 1️⃣의 함수가 호출될텐데 말이다. 

답은 **int i를 Wrapper Class인 Integer의 객체로 변환한다.**이다.

이렇게 하면 **`ArrayList.remove(Integer i)`**가 들어가게 되고, 인수로 WrapperClass의 객체가 들어갔음으로, 1번이 아닌 2️⃣의 remove가 호출된다. 또한 Integer class는 내부에 equals를 저장된 숫자와 equals에 인수로 들어온 값이 같은지로 판별하게 `overriding`이 되어 있다. 

따라서 int i라는 value를 `ArrayList<Integer>`에서 지우기 위해서는 **`.remove(Integer i)`**를 활용한다.