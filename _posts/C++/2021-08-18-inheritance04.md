---
layout: tag
title: "Inheritance story(04)"
categories:
- Cpp
tag:
- Cpp
---
## Study about inheritance (심화편 시작)

## pure virtual function story

- 선언은 있으나 정의는 없음 (C에서는 link 에러가 났음)
- 이런 흐름이 있다라고 설계만 해놓고 아직 구현을 안했을때 사용

>순수가상함수 예제

```cpp
class MyInterface
{
    virtual int GetData() const = 0;

};
class MyData : public MyInterface
{
    virtual int GetData() const
    {
        // 재정의
    }
}
```

- 순수가상함수들은 파생클래스에서 꼭 재정의를 해줘야한다
- 예를들면 MyCase는 휴대폰 케이스제작 MyInterface는 핸드폰 규격이 담겨있는 클래스라면 MyCase에선 핸드폰 크기만 알면될뿐 그 속의 RAM등은 알 필요 없기에 이런경우 사용

## static_cast story

>여러 _cast 종류

1. const_cast<>: const 제거기 / 권장은 안함
2. static_cast<>: 상/하향 형변환 제공 (Compiletime에) / 잘 사용하자
3. dynamic_cast<>: 상/하향 형변환 제공(Runtime에) / 절대쓰지말자
4. reinterpret_cast<>: / 쓰지말자

- 상속에서 형 변환시 static_cast를 잘 사용하자

## 다중 상속 story

> 인터페이스 USB 예제

```cpp
class USB {};
class PCI {};
class Device {};
```

- 이렇게 인터페이스에서 Device가 여러개의 클래스의 순수가상함수를 받아 써야할때만 사용하자
- 다중상속이 좋은경우가 적으니 잘 사용하되 대부분 피하자

>여기까지 공부하면서 느낀점

- 파생클래스를 가져다 쓸땐 항상 기반 즉 Framework에 대해 구조를 꼭 파악하자 (문서화를 잘하고 잘 보자는 의미)