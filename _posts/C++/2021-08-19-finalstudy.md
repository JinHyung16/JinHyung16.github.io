---
layout: tag
title:  "Lambda story(01)"
category: C++
tag:
- cpp
C++: true
---

## Study about lambda function & STL/function object

> 함수호출 연산자 예제

```cpp
void pritf();
int main(void)
{
    printf(...); // call
    return 0;
}
```

- printf는 주소 그리고 (...)을 함수호출 연산자라고하며 함수호출연산자 안의 내용을 매개변수라한다.
- 함수호출연산자는 재정의가 가능하다.
- 즉 함수자체를 객체화 가능 ex)call back logic
- #include<functional> 선언을 통해 사용 주로 람다식과 같이 사용됨

> 람다식 예제

```cpp
void TestFunc(char* pszParam, std::function<int(char*, int)> param)
{
    std::cout << pszParam << std::endl;
    param("Hello", 10);
}

int main(void)
{
    std::cout << "******Begin******" << std::endl;
    ::TestFunc //std::function 내부 구현
    )
        "TestFunc()",

        //함수의 실인수로 함수 몸체를 람다식으로 기술한다
        [](char* pszParam, int nPara)->int
        {
            std::cout << pszParam <<" : " << nParam << std::endl;
            return 0;
        }

    );

    std::cout << "******End******" << std::endl;
    return 0;
}
```

- 람다식을 이용해 함수 그 자체를 다른 함수의 매개변수로 넘기고 호출하는 형태
- void TestFunc() 내부에서 단순호출이 아닌 call-back 구조를 갖을수있는데 알아서 call-back이 된다.

## another story

> 도서 추천
- STL 관련하여 꼭 공부하기
- 공동환님의 "뇌를 자극하는 C++"
- 최홍배님의 "Thinking About C++11 STL" -> 특히 게임개발쪽이면 꼭 읽기

> '이것이 C++이다' 강의를 다 수강하고 나서 느낀점

- 흐름을 보는법을 배우자
- 객체지향에서 설계의 원칙이 중요하다.
- 정규 표현식은 필수
- SW 개발시 자료(정보)/처리 + UI + 제어 체계는 따로 떨어져서 작성되어야 한다.
- 웹에선 Model / View / Control 즉 mvc 아키텍쳐처럼 나누자는 것
- 11장 객체지향 주소록은 스스로 짤수있을때까지 공부하자!! 앞서 설명한 중요한것들을 얻을수있음
