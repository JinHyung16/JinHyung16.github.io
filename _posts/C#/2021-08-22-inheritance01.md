---
layout: tag
title: "inheritance story(01)"
categories:
- Csharp
tag:
- Csharp
---
## Study about inheritance

>inheritance & 'base' keyword

- 기반 클래스와 파생 클래스 사이에 형식 변환이 가능하다.
- is / as 키워드가 형 변환 연산자로 as는 참조형식에만 사용한다.
- 파생 클래스의 수명주기는 다음과 같다

```csharp
class Base
{
    protected int Age;
    // 1
    public Base() { Console.WriteLine("Base()"); }
    // 4
    ~Base() { Console.WriteLine("~Base()"); }

    public void BaseMethod() {}
    public Base(int age)
    {
        this.Age = age;
    }
}
class Derived : Base
{
    // 2
    public Derived() { Console.WriteLine("Derived()"); }
    // 3
    ~Derived() { Console.WriteLine("~Derived()"); }

    public void DerivedMethod()
    {
        base.BaseMethod();
    }
    public Derived(int age) : base(Age)
    {
        Console.WriteLine("(0).Derived()", this.Age);
    }
}
```

- 위에 써놓은 숫자 순서대로 기반클래스의 생성자 파생클래스의 생성자 파생클래스의 종류자 기반클래스의 종료자 순으로 호출된다

- base 키워드 사용하면 기반 클래스의 접근가능하며 파생 클래스의 생성자에서 기반 클래스의 생성자에 매개변수 전달도 가능하다.

>overriding and polymorphism

- 오버라이딩은 대상 메소드를 vritual 키워드로 선언해야하며 private가 붙은 메소드는 오버라이딩이 불가
- 재정의를 위해 재정의 하는 쪽에서 override 키워드를 붙여야한다.
- 다형성은 객체가 여러 형태를 가질수 있음을 의미한다 (Subtype polymorphism 개념이라 생각)

```csharp
class ArmorSuite
{
    public virtual void Initialize()
    {
        Console.WriteLine("Armored");
    }
}
class IronMan : ArmorSuite
{
    public override void Initialize()
    {
        base.Initialize();
        Console.WriteLine("Repulsor Rays Armed");
    }
}
class WarMachine : ArmorSuite
{
    public override void Initialize()
    {
        base.Initaialize();
        Console.WriteLine("Double-Barrel Cannons Armed");
        Console.WriteLine("Micro-Rocket Launcher");
    }
}
```

- ArmorSuite의 기능 업그레이드를 위해 클래스를 고치기 보단 상속받아 재정의하여 기능 업그레이드 해주는게 좋은데 이게 오버라이딩이다.

> overriding 봉인하기

- 메소드의 오버라이딩 봉인은 virtual 가상 메소드를 오버라이딩한 메소드가 대상이다.
- 오작동 위험이 있거나 잘못 오버라이딩함으로써 문제가 예상되는 경우 사용하며 sealed 키워드를 사용한다

```csharp
class Base
{
    public virtual void SealMe() {}
}
class Derived : Base
{
    public sealed void SealMe() {}
}
```

>메소드 숨기기

- 기반 클래스의 메소드를 감추고 파생 클래스 구현만 표시하는 기능
- 파생 클래스에서 new 한정자로 수식

```csharp
class Base
{
    public void MyMethod()
    {
        Console.WriteLine("Base.MyMethod()");
    }
}
class Derived : Base
{
    public new void MyMethd()
    {
        Console.WriteLine("Derived.MyMethod()");
    }
}
```

- 이렇게 메소드를 숨기는 기능이 오버라이드와 비슷해보이지만 CLR에게 숨기기는 하나 기반클래스의 인스턴스로 언제든 호출이 가능하여 완전한 다형성 표현의 한계가있다.