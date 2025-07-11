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
