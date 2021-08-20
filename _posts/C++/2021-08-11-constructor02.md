---
layout: tag
title: "Constructor Story(02)"
categories:
- Cpp
tag:
- Cpp
---
## Study about copy constructor & temporary object

## 대입 연산자 이야기

- operator Overloading은 C++에서 연산자를 함수형태로 사용가능
- 대입연산자 [l-value = r-value] 형태
- l-value에는 locator가 r-value에는 변수, 상수 등
- 즉 왼쪽에선 Overwrite가 오른쪽에선 read가 일어남

>operator 예제

```cpp
class CTest
{
public:
  CTest() 
  {
    std::cout << "CTest()" << std::endl;
    m_Data = new int(5);
  }
  CTest(const CTest &ct) 
  { 
    std::cout << "CTest(const CTest &)" << std::endl;
    this->m_Data = new int(*ct.m_Data);
  }
  ~CTest() 
  { 
    std::cout <<"~CTest()" << std::endl;
    delete m_Data;
  }
  void SetData(int nParam)
  {
    *m_Data = nParam;
  }
  int GetData() { return *m_Data; }
  // 복사생성를 정의하는 클래스는 단순대입연산자도 무조건 재정의
  CTest& operator =(const CTest &ct) //단순 대입연산자 재정의부분
  {
    *m_Data = ct.m_Data;
    return *this;
  }
private:
  int *m_Data = nullptr;
};

int main(void)
{
  CTest a;
  CTest b(a);
  a.SetData(5);
  // b = a; // deep copy 문제가 또 생김
  b.operator=(a);
  std::cout << a.GetData() << std::endl;
  std::cout << b.GetData() << std::endl;
  return 0;
}
```

- 단순 대입연산자 재정의 부분을 주석처리후 실행시 프로그램이 죽는데 디버깅 해보면 delet m_Data에서 문제가 생김을 알 수 있음

## Implicit conversion story(어려움!!)

> 묵시적(암시적) 형변환 예제

```cpp
class CTest
{
public:
  CTest() // Default
  {
    std::cout << "CTest()" << std::endl;
    m_Data = new int(5);
  }
  CTest(int nParam) // 변환 생성자
  {
    std::cout << "CTest(int)" << std::endl;
    m_Data = new int(nParam);
  }
  CTest(const CTest &ct) // Copy 
  { 
    std::cout << "CTest(const CTest &)" << std::endl;
    this->m_Data = new int(*ct.m_Data);
  }
  ~CTest() 
  { 
    std::cout <<"~CTest()" << std::endl;
    delete m_Data;
  }
  void SetData(int nParam)
  {
    *m_Data = nParam;
  }
  int GetData() { return *m_Data; }
  CTest& operator =(const CTest &ct)
  {
    *m_Data = ct.m_Data;
    return *this;
  }
private:
  int *m_Data = nullptr;
};
//사용자 코드
void TestFunc(CTest param)
{
  std::cout << param.GetData() << std::endl;
}
int main(void)
{
  CTest a; // default 호출 시점
  CTest c(a); // 복사생성자 호출 시점
  std::cout << a.GetData() << std::endl;
  
  CTest b(5); // 변환생성자 호출 시점
  TestFunc(b); // b를 원본으로 복사생성형태로 초기화가 되고 값이 초기화됨
  TestFunc(5); // 에러가 나야하는데 실행됨
  return 0;
}
```

- 각각 결과는 주석을 걸고 풀면서 확인하기

>Conversion constructor 무서움 확인 예제

```cpp
class CTest
{
public:
  CTest() // Default
  {
    std::cout << "CTest()" << std::endl;
    m_Data = new int(5);
  }
  /* explicit */ CTest(int nParam) // 변환 생성자
  {
    std::cout << "CTest(int)" << std::endl;
    m_Data = new int(nParam);
  }
  CTest(const CTest &ct) // Copy 
  { 
    std::cout << "CTest(const CTest &)" << std::endl;
    this->m_Data = new int(*ct.m_Data);
  }
  ~CTest() 
  { 
    std::cout <<"~CTest()" << std::endl;
    delete m_Data;
  }
  void SetData(int nParam)
  {
    *m_Data = nParam;
  }
  int GetData() const { return *m_Data; } // 여기에 const 붙였음
  CTest& operator =(const CTest &ct)
  {
    *m_Data = ct.m_Data;
    return *this;
  }
private:
  int *m_Data = nullptr;
};
//사용자 코드
void TestFunc(const CTest &param)
{
  std::cout << param.GetData() << std::endl;
}
int main(void)
{
  TestFunc(5);
  return 0;
}
```

- int에 대한 참조자 선언하면 int를 줘야한다 (아래 코드 확인)

```cpp
int nData;
int &rData = nData;
```

- 그러나 TestFunc(5)를 풀면 const CTest &param = 5라고 준건데 좌 우의 자료형이 다른데 오류가 발생하지 않는다.
- 컴파일러가 알아서 맞춤 객체를 하나 만들어서 주는것을 알 수 있다
- TestFunc(CTest(5)); 이렇게 넘겨준거와 동일
- CTest(10)은 작동이 되는데 main에 넣어 확인해보자
- 작동하는 이유는 변환생성자 때문인데 이를 막기위해선 변환생성자 앞에 explicit 붙여주자

>허용되는 변환이야기

```cpp
class CTest
{
public:
  CTest() // Default
  {
    std::cout << "CTest()" << std::endl;
    m_Data = new int(5);
  }
  explicit CTest(int nParam) // 변환 생성자
  {
    std::cout << "CTest(int)" << std::endl;
    m_Data = new int(nParam);
  }
  CTest(const CTest &ct) // Copy 
  { 
    std::cout << "CTest(const CTest &)" << std::endl;
    this->m_Data = new int(*ct.m_Data);
  }
  ~CTest() 
  { 
    std::cout <<"~CTest()" << std::endl;
    delete m_Data;
  }
  void SetData(int nParam)
  {
    *m_Data = nParam;
  }
  int GetData() const { return *m_Data; } // 여기에 const 붙였음
  CTest& operator =(const CTest &ct)
  {
    *m_Data = ct.m_Data;
    return *this;
  }
  operator int(void) // 형변환 operator 추가
  {
    return *m_Data;
  }
private:
  int *m_Data = nullptr;
};

int main(void)
{
  CTest a(5);
  std::cout << a.GetData() << std::endl;
  // std::cout << (int)a << std::endl; // 이것도 실행되면 좋을텐데...
  return 0;
}
```

- main 에서 주석된 부분이 실행되기 위해선 형변환 operator를 넣어준다

>핵심 정리

- conversion constructor 정의는 'explicit conversion constructor로 정의하자
- operator (자료형) 형식을 통해 형변환도 가능함 역시 앞에 explicit 키워드 붙여 사용자코드에서 발생할 혼란을 줄여주자
