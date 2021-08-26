---
layout: tag
title: "serialization story(01)"
categories:
- Csharp
tag:
- Csharp
---
## Study about serialization

- 복합 데이터 형식 쓰기와 읽기는 그 형식이 가진 필드 값의 저장순서 정의 후, 이 순서대로 저장/읽기 코드 작성 이를 위해 직렬화 도입
- 객체상태를 메모리나 영구 저장 가능한 0과 1의 순서로 바꾸는 것이다.

```csharp
[Serializable]
class NameCard
{
    public string Name;
    public string Phone;
    public int Age;
}

static void Main()
{
    // 직렬화
    Stream ws = new FileStream("a.dat", FileMode.Create);
    BinaryFormatter serializer = new BinaryFormatter();
    NameCard nc = new NameCard();
    nc.Name = "Hugh";
    nc.Phone = "1234";
    nc.Age = 23;

    serializer.Serialize(ws, nc);
    ws.Close();

    // 역 직렬화
    Stream rs = new FileStream("a.dat", FileMode.Open);
    BinaryFormatter deserializer = new BinaryFormatter();
    NameCard nc2;
    nc2 = (NameCard)deserializer.Deserialize(rs);
    rs.Close();

    Console.WriteLine($"Name: , {nc2.Name}");
    Console.WriteLine($"Phone: , {nc2/Phone}");
    Console.WriteLine($"Age: , {nc2.Age}");
}
```

- [Serializable] 애트리뷰트 작성
- Stream 클래스와 BinaryFormatter 이용
- [NonSerialized] 애트리뷰트는 직렬화 혹은 역직렬화에 포함 안됨