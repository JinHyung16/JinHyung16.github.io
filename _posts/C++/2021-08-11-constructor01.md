## Study C++ about copy constructor & temporary object
[생성자/소멸자] 라는 함수는 Caller가 호출해서 생성되는것이 아닌 언어 문법에의해 Call되는 시점이 '알아서' '자동'으로 호출된다 <br>
호출 When? 생성자-> 인스턴스 선언 or new 사용 / 소멸자-> delete or scope 밖으로 벗어났다 등... <br>
>복사에 관한 C++ style code
```복사 이야기
우리는 생성에 대해 아래 두 가지처럼 표현할수있다
int a(10); // a는 생성되는 것(변수, 인스턴스) 10은 생성되는것에 대한 초기값(상수) <- 나는 '생성'
int b(a); // 이때 a는 초기값인데 변수(인스턴스)로 이 문장의 의미는 a는 원본 b는 사본이라는 뜻 <- 나는 '복사생성'
```
### 생성자의 종류 및 특징
  0. 기본생성자
  1. 다중 정의된 생성자(매개변수가 있음)
    1-1. 변환생성자(매개변수가 1개인 특징)-> 형 변환연산자와 엮임
  2. 복사생성자
  3. 이동생성자 (복사생성자에서 + r-value 참조를받는 생성자) <br>
위 종류들의 핵심적인 이론은 '이름이 없는 임시객체'가 문제가 됨-> 코드에서 드러나지 않음을 주의<br>
### 복사생성자 이야기
Deep copy vs Shallow copy의 차이를 알자
>코드 예제
```
int main(void)
{
  int nData = 5;
  int *pA = &nData;
  int *pB = &nData;
  return 0;
}

nData라는 '대상체 1개'를 포인터 pA와 pB 2개가 가르키는 상황으로 포인터는 대상체에 대한 [접근,참조]의 수단일뿐 그 대상체 자체가 아니다 <br>
따라서 수단이 늘었다고해서 대상체가 늘어난 것이 아님 <br>
if) 대상체를 여러개로 만드는 경우는 'deep copy' / [접근,참조]를 여러개로 만드는 경우는 'shallow copy'

int main(void)
{
  int *pA = new int(5);
  int *pB = NULL;
  
  pB = pA; // shallow copy
  pB = new int(*pA); // deep copy
  
  delete pA; // 만약 shallow copy만 썻는데 pA를 날리면 pB는 삭제된 데이터를 가르켜 Dangling Pointer가 됨
  delete pB;
  return 0;
}
pB = new  int(*pA)가 왜 deep copy일까?
*pA는 5를 인스턴스화 했는데 *pB가 *pA를 인스턴스화 하면서 포인터가 가르키는 대상체가 늘어났으므로 deep copy
그런데 pB = pA는 그저 *pB가 *pA가 가르키는 대상체를 가르키는 것으로 shallow copy
```
>복사가 언제일어나는지 살펴보는 예제
``` 예제
class CTest
{
public:
  CTest() { std::cout << "CTest()" << std::endl; }
  CTest(const CTest &ct) { std::cout << "CTest(const CTest &)" << std::endl; }
  ~CTest() { std::cout <<"~CTest()" << std::endl; }
  
  int m_Data = 0;
};

int main(void)
{
  CTest a; <- default 생성자가 호출
  CTest b(a); <- 복사생성자가 호출
  
  std::cout << a.m_Data << std::endl; // 0 출력
  std::cout << b.m_Data << std::endl; // 0 출력
  return 0;
} <- 소멸자 호출
```
>m_Data의 또 다른 출력
```
class CTest
{
public:
  CTest() { std::cout << "CTest()" << std::endl; }
  CTest(const CTest &ct) { std::cout << "CTest(const CTest &)" << std::endl; }
  ~CTest() { std::cout <<"~CTest()" << std::endl; }
  
  int m_Data = 0;
};

int main(void)
{
  CTest a; <- default 생성자가 호출
  a.m_Data = 300;
  CTest b(a); <- 복사생성자가 호출
  
  std::cout << a.m_Data << std::endl; // 300 출력
  std::cout << b.m_Data << std::endl; // 0 출력-> 복사생성자 부분을 주석처리후 실행시 300 출력
  return 0;
} <- 소멸자 호출
복사생성자를 주석처리하지 않고 출력하면 왜 b.m_Data는 값이 다를까? 
 -> 복사생성자가 호출되는 지점을보면 간단하다 a.m_Data를 바꾼후 b의 복사생성자가 호출되었기 때문이다
```
>m_Data의 또 다른 출력
```
class CTest
{
public:
  CTest() { std::cout << "CTest()" << std::endl; }
  CTest(const CTest &ct) 
  { 
    std::cout << "CTest(const CTest &)" << std::endl;
    this->m_Data = ct.m_Data; // 멤버값을 단순 대입으로 복사
  }
  ~CTest() { std::cout <<"~CTest()" << std::endl; }
  
  int m_Data = 0;
};

int main(void)
{
  CTest a; <- default 생성자가 호출
  a.m_Data = 300;
  CTest b(a); <- 복사생성자가 호출
  
  std::cout << a.m_Data << std::endl; // 300 출력
  std::cout << b.m_Data << std::endl; // 300 출력
  return 0;
} <- 소멸자 호출
복사생성자 부분에서 this를 이용해 단순 대입복사를 통해 같은 값이 출력됨을 확인가능
그렇다면 만약 m_Data가 포인터면 어떻게 될까?
```
>m_Data가 포인터인 경우
```
class CTest
{
public:
  CTest() 
  {
    std::cout << "CTest()" << std::endl;
    m_Data = new int(5);
  }
  /* CTest(const CTest &ct) 
  { 
    std::cout << "CTest(const CTest &)" << std::endl;
    this->m_Data = new int(*ct.m_Data); // 이렇게 변화시켜주자
  } */
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
private:
  int *m_Data = nullptr;
};

int main(void)
{
  CTest a;
  CTest b(a);  
  std::cout << a.GetData() << std::endl;
  std::cout << b.GetData() << std::endl;
  return 0;
}
실행시 프로그램이 죽는다 Why? 디버깅모드로 보면 delete m_Data에서 죽었다고 표시된다
아마 this->m_Data = ct.m_Data 연산이 일어나서 이미 delte 한 데이터를 또 지우려했기에 죽은거다
복사 생성자부분을 주석처리했는데 위 같은 연산이 어떻게 일어나지? 
 -> 컴파일러가 디폴트생성자를 생성할때 포인터에 대해 멤버값 단순 대입 즉 shallow copy를 했음을 알수있음
따라서 주석처리한 복사생성자에서 코드를 변경시켜 주석 해제후 실행하면 잘 실행됨 -> deep copy를 통해 해결
```
