---
layout: tag
title:  "Inheritance story(03)"
category: C++
tag:
- cpp
C++: true
---
## Study about inheritance (심화편 시작)

## virtual function story

> 가상함수를 사용하는 이유 (코드 예제)

```cpp
class MyString
{
public:
    int SetString(const char* c)
    {
        // ...
        onSetString(m_Data); // 1번
    }
    virtual void onSetString(const char* c)
    {
        // 아무 내용 없음
    }
private:
    char* m_Data;
};

class MyStringEx : public MyString
{
public:
    // 위에 내용과 동일
    void onSetString(const char* c)
    {
        // 재정의 해버림
    }
};

int  main(void)
{
    MyStringEx a;
    a.onSetString("2021년");
}
```

> 1번 설명

- MyString 클래스는 2018년에 작성됐고 MyStringEx 클래스는 2021년에 작성됏다고 가정하자
- 1번의 경우 2018년 당시에는 바보같아 보이나 사실 이는 미래를 보는 코드다
- 2021년 현재 내가 onSetString() 을 재정의해서 사용시 virtual을 안 붙여도 상위 클래스에서 virtual을 사용했기에 자동으로 가상함수다
- 가상함수는 실형식이 중요하다 따라서 main에서 a.onSetString은 MyStringEx 클래스 내 onSetString이 호출된다
- 즉 21년 내가 재정의한 onSetString은 내가 call하는게 아닌 called by framework가 된다.
- 무슨 소리냐 2018년에 만들어놓은 코드의 흐름에 21년 내가 재정의한 코드가 껴 들어가는거다.
- 따라서 이를 통해 가상함수가 "언제? 왜?" 호출되는지 알아야한다

> onSetString(const char* c) 확장 설명

```cpp
onSetString(const char* c)
{
    // 1. 2021년 작성한 코드 (1)
    // 2. 2018년 MyString::onSetString(const char *c)
    // 3. 2021년 작성한 코드 (2)
}
```

- 코딩의 순서가 1->2->3 || 1->2 || 2->3 이렇게 나뉠 수 있는데 잘 선택해서 사용하자 여기서 2번 코드가 어디 들어가는지가 매우 중요하다
- 왜냐 2번을 통해 과거에서 메모리 할당을 다 했으니 2번 전에 현재 코드를 사용하면 메모리 할당전에 실행이고 3번은 메모리 할당 후 실행된다
- 파생클래스에서 기본클래스의 메서드를 재정의 한다는것은 두가지 의미가 있다.
    1. 기존의 것을 무력화시켜 내 코드로 대체한다
    2. 기존 코드와 더불어 공존할거다
- 이렇게 재정의는 확장의 개념이 들어감을 기억 따라서 코드의 흐름을 보는법을 배우자

> final 키워드

- 파생클래스에서 재정의되면 안되는 메서드 앞에 final에 붙이면 재정의를 막아준다