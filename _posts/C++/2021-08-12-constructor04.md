---
layout: tag
title:  "Constructor Story(04)"
category: C++
tag:
- cpp
C++: true
---

## Study C++ about copy constructor & temporary object

*constructor(03)강의 temporary object 꼭 보기* 

## 변수 상수 Story

- 자료에는 변수 / 상수 (Or Instance) 가 있다
- 무슨 소리냐 팬의 재료를 모아놨다고 그게 팬은 아니다 적절하게 팬의 형태를 이루면서 그 기능이 실현되어야 팬이라고 할 수 있는 것과 비슷

> a = 3 + 4 를 통한 예시

- Constructor Story(3)에서 사용한 예시를 들고와서 설명해보자
- l-value 에는 변수가 r-value에는 변수, 상수가 온다 즉 모든 l-value는 r-value가 될 수 있다
- 그렇다면 임시결과 7은 변수일까 상수일가? 상수다!!
- Instance 7의 life cycle은 어떨까?
- 3 + 4를 할때 생기고 a에 대입하고 a가 7이되면 사라진다
- 7이 상수면 r-value 라는거다 매우 중요한 개념!

> a = b + 3 + c + 4 로 확장한 예시

- 계산순서는 1.b+3 / 2. (b+3)+c / 3. ((b+3)+c)+4 이렇게 되며 각 순서 과정에서 생긴 임시객체를 T1 T2 T3라 해보자
- 그러면 각 임시객체들의 life cycle은 어떨까?
- T1은 T2가 생기고 소멸 T2는 T3가 생기고 소멸 T3는 a가 T3가 되면 소멸되면 메모리 효율이 좋다만 만약 이전 임시객체가 필요하다면?
- 예를들어 임시객체 T2가 다음 코드에서 필요하면 a = (b+3)+c; 이렇게 끊고 z = a + 4; 이런식으로 나눠서 작성한다.
- 이런 overhead가 발생해 생긴게 r-value 참조다
- 즉 int &로 참조하면 r-value 참조는 int && 이렇게 참조

> r-value 참조 쉽게 설명

- 위 확장 예시에서 z=(b+3)+c; 이렇게 하여 임시 객체 T2를 z에 담아 썼는데 차라리 T2에 z를 넣어 임시객체 하나를 줄이면 어떨까?
- 무슨소리냐 T2에 (b+3)+c 담아서 z에 대입보단 그냥 z에 담아버리는게 객체 입장에선 더 효율적이다 이래서 r-value 참조가 나온거다.

> r-value 예제

``` r-value &&
void testFunc(int& value) 
{
    std::cout << "testFunc(int&)" << endl;
}

void testFunc(int&& value) // r-value ref
{
    std::cout << "testFunc(int&&)" << endl;
}

int main()
{
    testFunc(3 + 4);
    return 0;
}
```

- 실행시 "testFunc(int&&)" 출력

> 여담

- 이 부분을 공부하면서 내가 C++을 배우고 코드작성시 현재를 생각하면서 코드를 작성했다는걸 느꼈다. 단순 class를 코드 분할이 아닌 객체로 사용하는 방법에 대해 깊게 생각하게 됐다.
- 특히 상속을 하면서 시간의 흐름에 대해 코드를 생각할 필요성이 생김
