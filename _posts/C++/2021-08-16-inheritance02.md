---
layout: tag
title:  "Inheritance story(02)"
category: C++
tag:
- cpp
C++: true
---
## Study about inheritance (심화편 시작)

## virtual function story

- "상속 + 재정의" 가 나오면 강한 특징을 발현
- Data/GUI/Ctrol(제어) 는 항상 구분
- Frame work: [기반코드] / [확장코드] 
- 기반과 확장사이 상호작용 가능하게 하는게 재정의
- 기반코드에서 확장코드를 Call 하는 방법이 가상함수다
- '미래'를 호출한다! 라는 개념도 탑재

> method 종류

1. 일반
2. 가상 
3. 순수 가상: 가상함수와의 차이는 only 선언 (정의가 없음)

- 선언은 Complie만 가능 정의는 Link만 가능

> 형식 이야기

1. 접근형식: Pointer* / Reference& 가 있음
2. 실제형식(실형식): 아래 코드예제 통해서 설명

```cpp
class A {}; // 기본 클래스
class B : public A {}; // 파생 클래스
int main(void)
{
    A* pData = new B; // pointer
    B data;
    A &rData = data; // reference
}
```

- A* 는 접근형식 B는 실형식 즉 파생형식을 기본형식으로 pointing 하는걸 추상자료형 이라고 부른다
- main에서 작성한 두 가지 모두 가능

> 형식 이야기 확장 (매우 중요)

- 이전시간 method 종류에 따른 접근 이야기를 했었다
- 아래 예제를 통해 다시한번 확인

```cpp
class A
{
    int TestFunc(void) 
    { 
        std::cout << "A::TestFunc()" << std::endl;
    }
    virtual int Test(void)
    {
        std::cout << "virtual A::TestFunc()" << std::endl;
    }
};
class B : public A
{
    int TestFunc(void) // 재정의
    { 
        std::cout << "B::TestFunc()" << std::endl;
    }
    virtual int Test(void)
    {
        std::cout << "virtual B::TestFunc()" << std::endl;
    }
};
int main(void)
{
    A* pData = new B;
    pData->TestFunc(); // 1번
    pData->Test(); // 2번    
}
```

- 1번은 A와 B클래스 내부 TestFunc() 중 무엇을 호출할까?
- 이전시간 일반메서드는 접근형식을 가상함수는 실형식을 따른다.
- 따라서 1번은 class A 내부의 TestFunc()를 호출하게 된다.
- 2번은 가상함수로 class B 내부의 Test()를 호출하게 된다.

> 접근형식 실형식 가상함수 ver

```cpp
class A 
{ 
    A(){}
    ~A(){}
    // virtual ~A(){}
};
class B : public A
{
    B(){}
    ~B(){}
};
int main(void)
{
    A *pData = new B;
    delete pData; // 1번
}
```

- 1번 호출시 A와 B 내부 소멸자중 어느게 불릴까?
- pData의 접근형식이 참조형식으로 A클래스의 소멸자가 호출된다.
- 실형식 클래스 B의 소멸자는 호출 안됨
- 만약 B 생성자에서 메모리 동적할당시 메모리 릭 현상이 생길 수 있음
- virtual 사용하면 일단 B클래스 소멸자 호출하고 A클래스 소멸자 부른다

> 생성자 소멸자 호출순서 실행순서 (알고있자)

- 생성자의 경우 호출순서와 실행순서가 서로 다름
- 소멸자의 경우 호출순서와 실행순사가 일치