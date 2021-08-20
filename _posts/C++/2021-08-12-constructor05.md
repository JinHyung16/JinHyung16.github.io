---
layout: tag
title:  "Constructor Story(05)"
category: C++
tag:
- cpp
C++: true
---
## Study C++ about copy constructor & temporary object

*Constructor Story(01) 강의와 연관*

## Deep Copy Story

- 생성자의 호출 시점, 조건에 대해 이전 내용들을 보면서 꼭 기억하자

> 복사 생성자의 또 다른 이야기

```cpp
class CTest
{
public:
  CTest() { std::cout << "CTest()" << std::endl; } // default
  CTest(const CTest &ct) // copy
  { 
    std::cout << "CTest(const CTest &)" << std::endl; 
  }
  ~CTest() { std::cout <<"~CTest()" << std::endl; } // destructor

  void TestFunc1(CTest param) { //무언가 실행 }
  // void TestFunc(CTest &param) { // 무언가 실행 }
  CTest TestFunc2() { //무언가 실행 } 
};

int main(void)
{
  CTest a; // default 생성자가 호출
  CTest b(a); // 복사생성자가 호출
  TestFunc(a); // 복사생성자를 호출하는 다른 경우1
  CTest c = TestFunc2(); // 복사생성자 호출 다른 경우2
  a = TestFunc2() + b; // 문제1
  CTest d = TestFunc2() + b; // 문제2
  return 0;
} <- 소멸자 호출
```

- "Constructor Story(01)" 에서 사용한 예제를 들고왔다.
- CTest b(a); 가 복사생성자를 호출하는 시점인데 이 코드외 복사생성자를 호출하느 경우는 또 언제일까? 예제를 통해 확인
- TestFunc1() 함수는 좋은 코드일까? 비 효율적이다 왜냐 인스턴스를 불 필요하게 2개 호출하고 있다. 따라서 & 붙이자
- TestFunc2() 는 반환형식이 class로 임시객체를 생성하는 요소다
- TestFunc2()가 반환하는 객체1개 인스턴스 b 1개 합쳐나온 임시객체 T1이 생기는데 이를 원본으로 a가 복사생성하는 문제1과 단순 대입하는 문제2 등이 생김
- 최적화 과정에서의 논리발달로 이어지는 단계임
- 임시객체 T1은 r-value 문제2처럼 단순대입했다면 이전처럼 순간적인 메모리가 확 올라갈것이다. 이 코드하나로 전체 성능이 저하되는 문제
- 이를 해결하기 위해 이동생성자가 나오게 된다

## Move constructor Story

- deep copy를 하기 위해선 인스턴스 a가 내부적으로 *p를 갖고있을때 *p가 가르키는 대상의 용량이 10MB고, 인스턴스 b를 만들면 b에대한 내부적인 *p가 가르키는 대상도 10MB가 생겨 메모리가 순간적으로 20MB까지 올라간다.
- 메모리를 획기적으로 줄이는 방법으로 b 내부 *p가 가르키는 걸 a 내부 *p가 가르키는 대상으로 바꿔주면 된다 (shallow copy가 아닌가?)
- 언제 shallow copy를 하냐 a내부 *p가 가르키는 대상이 소멸하기전에 b 내부 *p가 해당 대상을 가로채기하고 a내부 *p = nullptr로 바꿔준다.
- 이것이 이동생성자에서 move semantics
- CTest(const CTest && ct) 형태