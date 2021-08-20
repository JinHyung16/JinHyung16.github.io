---
layout: tag
title: "Constructor Story(03)"
categories:
- Cpp
tag:
- Cpp
---
## Study C++ about copy constructor & temporary object

## temporary object story

> 예제로 생각해보기 (이해가능하면 하기)

```cpp
class
{
public:
    int TestFunc()
    {
        return nData;
    }
private:
    int nData;

}

int main(void)
{
    int nResult = TestFunc();
    return 0;
}
```

- main부분을 보면 nResult = TestFunc()의 의미는 nData를 nResult에 전달하겠다는 의미다
- Q) nData를 반환하면 TestFunc()은 사라질거고 그러면 nData도 사라질텐데 nResult에 nData를 전달하는 중간접점은 어떤식으로 동작할까?
- A) 어셈블리를 뜯어보면 EAX 레지스터가 항상 개입한다 (항상은 아님)
- EAX안에는 임시결과(?)라는게 들어있다
- 따라서 TestFunc이 사라져도 Cpu안 EAX에 임시결과에 저장해서 전달해주는거

> temporary object 쉽게 설명 예제

```cpp
int main(void)
{
    int a;
    a = 3 + 4;
    return 0;
}
```

- a = 3 + 4는 7이 a에 들어가는게 아니라 정확히는 3 + 4의 임시결과 7이 a에 대입되는거다 그렇다면 임시결과는 도대체 어디에 저장되는걸까?
- a의 타입은 int 3, 4도 int다 즉 모두 동일형식이라는 전제로 생각하자
- a = 3 + 4 에는 inteager instance가 몇 개 있을까?
- 코드 수준에선 3개 이를 갖고 연산을 수행하면 4개다 임시결과 7 있지말자
- 즉 내가 생각한거보다 더 많은게 사용되는데 이는 메모리랑 연관된다

> temporary object 깊게

```cpp
class CMData
{
    // 어떤 내용
}

int main(void)
{
    // 어떤 내용
    CMyData a = new CMyData();
    a = b + c; // 1번
    CMyData d = b + c; // 2번
}
````

- 대충 이런 형식으로 작성되있는 예제가 있을때 a가 CMyData 클래스의 인스턴스라고 치면 CMyData의 인스턴스의 개수는 몇 개일까?
- 임시객체까지 포함해 4개가 된다
- 따라서 위 질문은 "1번 구문에서 CMyData클래스의 생성자 함수가 몇 번 호출됩니까?"로 바뀌며 4번이다
- 만약 2번의 경우를 물으면 선언과 동시에 초기화가 진행되어 3번이다
- 이게 왜 중요하고 메모리랑 무슨 연관일까?

> 메모리로 보는 직관적인 이해

```cpp
class MyImage
{
    // 영상 데이터 로딩하는 코드
}

int main(void)
{
    MyImage i = new MyImage();
    i = a + b; // 1번
    return 0;
}
```

- 1번에서 i는 1.2MB a는 3MB, b는 2MB 정도라 치면 현재 6.2MB에 순간적으로 메모리가 피크칠땐 임시객체 5MB까지 추가로 더해진다.