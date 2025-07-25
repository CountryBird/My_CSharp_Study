작성되어지는 대부분의 프로그램은 컬렉션을 순환하거나 처리해야 할 필요가 있습니다.     

또한 해당 클래스의 요소에 대한 반복 작업을 수행하는 _이터레이터_ 메서드를 만들 수 있습니다. 
_이터레이터_ 는 컨테이너나 리스트와 같은 객체를 트래버스 하는 객체입니다.  

- 컬렉션의 각 항목에 대해 작업을 수행합니다.
- 사용자 지정 컬렉션을 열거합니다.
- LINQ 또는 기타 라이브러리 확장
- 이터레이터 메서드를 통해 데이터가 효율적으로 흐르는 데이터 파이프라인 만들기

# foreach를 사용하여 반복
컬렉션을 단순 열거하는 것은 간단합니다.    
`foreach`를 통해 컬렉션을 열거하고 각 요소에 대해 작업을 수행할 수 있습니다.   
```cs
foreach(var item in collection)
{
  Console.WriteLine(item?.ToString());
}
```
컬렉션에 대해 모든 내용을 반복하려면 `foreach`문만 있으면 됩니다.     
물론 `IEnumerable<T>` 이나 `IEnumerator<T>`에 의존하여 실행되는 형태이기 때문에 해당 컬렉션은      
이 두 인터페이스에 대해 의존 관계가 있어야 합니다.   

시퀀스가 비동기적으로 생성되는 경우, `await foreach`문을 사용하여 
해당 시퀀스를 비동기적으로 사용할 수 있습니다. 
```cs
await foreach(var item in asyncSequence)
{
  Console.WriteLine(item?.ToString());
}
```

# 이터레이터 메서드를 사용하여 소스 열거
열거를 위한 데이터 시퀀스를 생성할 수 있습니다.   
이러한 메서드는 _이터레이터 메서드_ 라고 부릅니다.   

`yield return`의 형태를 사용하는데,   
값을 하나씩 외부로 내보내고, 다음 호출이 오면 그 다음에 이어서 실행해주는 역할을 합니다.   

데이터가 크거나, 무한 시퀀스를 다루는 경우       
전체 컬렉션을 메모리에 올리지 않기 때문에 성능적인 이득을 볼 수 있습니다.   
```cs
public IEnumerable<int> GetSetsOfNumbers()
{
    int index = 0;
    while (index < 10)
        yield return index++;

    yield return 50;

    index = 100;
    while (index < 110)
        yield return index++;
}
```

사용 경우에 따라, 이터레이터 메서드에 비동기 개념을 접목해 사용할 수 있습니다.   
이러한 경우 반환 타입을 `IEnumerable<T>`에서 `IAsyncEnumerable<T>`로 변환할 필요가 있습니다.
```cs
public async IAsyncEnumerable<int> GetSetsOfNumbersAsync()
{
    int index = 0;
    while (index < 10)
        yield return index++;

    await Task.Delay(500);

    yield return 50;

    await Task.Delay(500);

    index = 100;
    while (index < 110)
        yield return index++;
}
```
---
이터레이터 메서드를 사용할 때 주의할 점이 하나 있는데,   
동일한 메서드에서 `return`과 `yield return`을 동시에 사용할 수 없습니다.   
```cs
public IEnumerable<int> GetSingleDigitNumbers()
{
    int index = 0;
    while (index < 10)
        yield return index++;

    yield return 50;

    // 컴파일 에러 발생
    var items = new int[] {100, 101, 102, 103, 104, 105, 106, 107, 108, 109 };
    return items;
}
```

# foreach 심층 분석
`foreach`문은 `IEnumerable<T>`와 `IEnumerator<T>` 인터페이스를 통해   
내부적으로 컬렉션의 모든 요소를 반복하는 관용구로 확장시킵니다.    
또한 이 방법을 통해 개발자가 리소스를 관리하지 않아 발생하는 오류도 최소화합니다.    

예를 들어 아래의 코드는,
```cs
IEnumerator<int> enumerator = collection.GetEnumerator();
while (enumerator.MoveNext())
{
    var item = enumerator.Current;
    Console.WriteLine(item.ToString());
}
```

```cs
{
    var enumerator = collection.GetEnumerator();
    try
    {
        while (enumerator.MoveNext())
        {
            var item = enumerator.Current;
            Console.WriteLine(item.ToString());
        }
    }
    finally
    {
        // dispose of enumerator.
    }
}
```
아래의 코드로 확장되어 자체적인 Dispose를 실행하고 이와 관련된 오류도 방지합니다.  

Dispose에 관한 코드 확장은, 컬렉션의 타입에 따라 일부 차이가 생기는데,     
기본 컬렉션에 대해서는 
```cs
finally
{
   (enumerator as IDisposable)?.Dispose();
}
```
`enumerator`가 `IDisposable`을 구현했는지 여부를 런타임에서 확인합니다. 

비동기 컬렉션에 대해서는
```cs
finally
{
    if (enumerator is IAsyncDisposable asyncDisposable)
        await asyncDisposable.DisposeAsync();
}
```
`enumerator`가 `IAsyncDisposable`을 구현했는지 여부를 런타임에서 확인합니다. 

만약 `enumerator`가 `sealed` 타입이고    
`IDisposable` 또는 `IAsyncDisposable`로의 암시적 변환이 불가능한 경우는
```cs
finally
{
}
```
`sealed` 되어 있고, 변환도 불가능한 것을 컴파일러가 알기 때문에 아무런 코드도 추가하지 않습니다.

값 타입이고 암시적인 변환이 가능한 경우는
```c
finally
{
   ((IDisposable)enumerator).Dispose();
}
```
박싱 없이 정적 타입 변환으로 `Dispose()`를 호출해 변환합니다.   

위와 같은 형식들로 변환된다는 것을 알 수 있습니다.
