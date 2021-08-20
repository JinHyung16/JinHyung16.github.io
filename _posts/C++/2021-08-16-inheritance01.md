---
layout: tag
title: "Inheritance story(01)"
categories:
- Cpp
tag:
- Cpp
---
## Study about inheritance

## inheritance story

- 코드를 재사용하는 방법 중 하나 (누군가 작성한 코드를 가져다 사용)
- 규모 확장 (재사용 과정에서 일어남)
- 관계의 한 유형 (class간의 관계)
- 설계 <- 객체(요수) 규정 + 관계 정의
- 설계에선 UML 이야기 필수
- C++에선 SOLID 원칙이 있음

> 상속 예시

```cpp
// 사람 클래스
class person { };
// 의사 클래스
class doctor : public person { };
// 이때 사람클래스에서 파생된게 의사 클래스
```

- 부모는 사람클래스 자식은 의사클래스
- 부모는 기본클래스 자식은 파생클래스
- "의사는 사람의 일종" 표현 가능
- 현재가 의사클래스면 사람클래스는 과거
이렇게 시점으로도 생각하자

> 상속 이해 코드

```cpp
using namespace std;

class CTest
{
public:
    CTest() { cout << "CTest()" << endl;}
    ~CTest() { cout << "~CTest()" << endl; }  
    void TestFunc(void)
    {
        cout << "CTest::TestFunc()" << endl;
    }
};

class CTestEx : public CTest
{
public:
    CTestEx() { cout << "CTestEx()" << endl;}
    ~CTestEx() { cout << "~CTestEx()" << endl; }  

    // 재 정의
    void TestFunc(void)
    {
        cout << "CTestEx::TestFunc()" << endl;
    }
};

int main(void)
{
    // 1번
    CTestEx a_Ex;
    a_Ex.TestFunc();

    // 2번
    CTestEx b;
    CTest &c = b; // 파생형식을 기본형식으로 pointing 중
    c.TestFunc(); // CTest::TestFunc() 호출됨
    return 0;
}
```

> 1번 설명

- 1번 출력시 부모클래스의 생성자도 호출됨 (꼭 직접 확인하자)
- 즉 a_Ex가 CTestEx() call 하고 여기서 부모클래스 CTest()를 콜 한뒤 부모클래스 생성자가 return하여 CTestEx()를 실행한다
- 따라서 생성자는 [call순서] 와 [실행순서] 가 일치하지 않음
- 생성자함수는 **only [객체 자신을 초기화]** 만 하자
- **즉 파생형식 생성자에서 부모클래스 멤버 초기화는 최악의 바보짓!!**

> 2번 설명

- 파생형식 <- 기본형식 이렇게 pointing 가능
- 즉 파생형식은 기본형식으로 pointing 가능
- ex) 기본형식이 사람, 파생형식이 학생이면 학생 <- 사람 이게 가능
- 이때 사람을 추상형식 학생을 구현형식이라고 말할수있음
- c.TestFunc()는 일반 메서드를 호출함으로 접근형식을 따름
- 여기서 CTestEx b는 실형식 CTest &c는 접근형식이다

> Method 종류에 따른 접근 (매우 중요)

- 일반 : [접근형식] 을 따름
- static
- virtual : [실형식] 을 따름

> 헷갈리기 쉬운거

```cpp
class A {}; // 크기 [A크기]
class B : public A {}; // 크기 [[A크기] + [B크기]] = [전체가 B]
```

- A의 크기가 [000] 이정도면
- B의 크기는 [000(A)]+[000(B)] 이걸 합쳐서 B 라고 부른다
- 만약 B를 A* 접근시 B의 크기에서 앞에 A부분만 접근하는거고 B*로 접근하면 B의 크기 전체를 접근하는거
- 그런데 B*를 A의 접근하면 A에는 B의 부분이 없으므로 불가
- 추후 [가상함수] 까지 추가되면 헷갈리니 꼭 기억