비동기 메서드의 반환 타입은 다음과 같을 수 있습니다.     
- `Task`: 작업을 수행하지만 아무 값도 반환하지 않는 비동기 메서드의 경우
- `Task<T>`: 값을 반환하는 비동기 메서드의 경우
- `void`: 이벤트 핸들러의 경우
- `IAsyncEnumerable<T>`: 비동기 스트림을 반환하는 비동기 메서드의 경우

- _GetAwaiter_ 메서드가 있는 경우, 타입에 관계 없이 사용가능

# Task 반환 타입
`return` 문이 없거나, 피연산자를 `return` 하지 않는 비동기 메서드의 반환 형식은 `Task`입니다.     
이러한 메서드가 동기적으로 실행되는 경우 `void`를 반환합니다.   

따로 반환 타입이 존재하지 않기 때문에 `await`은 호출 메서드의 일시 중단하는 역할로만 사용됩니다.    

```cs
static async Task WaitAndApologizeAsync()
{
    await Task.Delay(1000);
    Console.WriteLine("Sorry for the delay...\n");
}
public static async Task Main(string[] args)
{
   await WaitAndApologizeAsync();

    Console.WriteLine($"Today is {DateTime.Now:D}");
    Console.WriteLine($"The current time is {DateTime.Now.TimeOfDay:t}");
}
```
다음과 같은 방식으로, 메서드를 `await`의 적용과 구분하여 작성할 수도 있습니다. 
```cs
Task waitAndApologizeTask = WaitAndApologizeAsync();

 string output =
     $"Today is {DateTime.Now:D}\n" +
     $"The current time is {DateTime.Now.TimeOfDay:t}";

 await waitAndApologizeTask;
 Console.WriteLine(output);
```

# Task<T> 반환 형식
