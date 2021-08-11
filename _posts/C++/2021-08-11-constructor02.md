## Study C++ about copy constructor & temporary object
### 대입 연산자 이야기
연산자 Overloading-> C++에서 연산자를 함수형태로 사용가능 <br>
대입 연산자: □(l-value) = △(r-value)에서 l-value에는 locator가 r-value에는 변수,상수 등 옴 <br>
즉 왼쪽에선 Overwrite가 오른쪽에선 read가 일어남 <br>
>연산자 
```
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
  CTest& operator =(const CTest &ct) // 복사생성를 정의하는 클래스가 있으면 단순대입연산자도 무조건 재정의하기
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
실행시 프로그램이 죽는데 디버깅하면 delete m_Data에서 문제가 생김
 -> operator를 만들어서 해결
```
### 묵시적 변환 이야기(어려움!!)
```
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
각각 결과는 주석을 풀고 걸면서 확인해보길
```
>변환생성자의 무서움 확인하기
```
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
if) int에 대한 참조자를 선언하면 int를 줘야한다-> int nData; int &rData =nData; 이런 형식으로 줘야한다
TestFunc(5)를 풀면 const CTest &param = 5 라고 준건데 좌우의 자료형이 다른데 오류가 안난다
 -> 컴파일러가 알아서 맞춤 실행해보면 객체를 하나 만들어서 주는것이 보인다
 -> TestFunc(CTest(5)); 이렇게 준거랑 동일
CTest (10); 이렇게 작성해서 실행된다-> main에 넣어서 확인해보기
위 코드가 실행되는 이유는 변환생성자때문-> 막기위해 변환생성자 앞에 explicit 붙여주기
```
>허용되는 변환이야기
```
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
  operator int(void) // 형변환 추가 
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
main에서 주석된 부분이 실행되기 위해선 형변환 operator를 넣어준다
```
지금까지의 핵심은 변환 생성자 정의는 'explicit 변환생성자'로 정의하기 <br>
operator (자료형)형식을 통해 형 변환도 가능 역시 앞에 explicit붙여서 사용자코드 측에서 혼란을 줄여주자 <br>
