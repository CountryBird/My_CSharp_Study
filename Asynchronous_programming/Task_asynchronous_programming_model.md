비동기 프로그래밍을 사용하여 성능 병목 상태를 방지하고 애플리케이션의 응답성을 향상시킬 수 있습니다.     
그러나 비동기 애플리케이션을 사용하면 일부 코드가 복잡해지기 때문에 작성, 디버그, 유지 관리가 어려울 수 있습니다.     

C#은 .NET 런타임에서 비동기 자원을 활용하는 간소화된 접근 방식으로 비동기 프로그래밍을 지원합니다.       
컴파일러가 어려운 작업을 수행하기 때문에, 개발자는 약간의 노력으로 비동기 프로그래밍의 장점을 취할 수 있습니다.      

# 응답성 향상
비동기 처리는 웹 액세스와 같이 잠재적으로 차단되는 작업에 필수적입니다.         

웹 리소스 액세스는 경우에 따라 지연되는 경우가 있는데, 동기 프로세스에서 이러한 방식으로 작동하면         
전체 애플리케이션이 기다리는 상황이 발생하기 때문에 비동기 처리가 필요합니다.      

## 웹 접근
```cs
HttpClient client = new HttpClient();
string content = await client.GetStringAsync("https://example.com");
```
`HttpClient`는 웹 페이지나 API로부터 데이터를 가져올 때 사용하는 클래스입니다.       
`GetStringAsync`는 비동기로 동작하여, 응답이 느려도 애플리케이션이 멈추지 않습니다.       
또한 반환값으로 받아오는 `string`은 응답의 body를 의미합니다.    

## 파일 작업
```cs
using var reader = new StreamReader("person.json");
        string json = await reader.ReadToEndAsync();
        Person person = JsonSerializer.Deserialize<Person>(json);
        Console.WriteLine(person.Name);
```
`StramReader`는 파일을 읽어오는 클래스이며,     
`ReadToEndAsync()`는 JSON 데이터를 읽는 작업을 수행하는데 이 작업은 파일 크기에 따라 읽는 시간이 오래 걸릴 수 있습니다.     
때문에 이러한 작업을 비동기로 동작하게 하면, 원하는 타이밍에 실행하여 다른 작업에 대한 시간을 낭비하게 하는 일이 줄어듭니다.      

`JsonSerializer`의 `Deserialize<T>`는 Json 파일의 내용을 원하는 객체의 형태로 변환하는 메서드입니다.       
다만 객체의 변수 이름과 Json 파일의 속성 값이 같아야 합니다.        
참고로, `Serialize<T>` 메서드는 반대로 객체를 Json 형태로 바꾸는 역할을 합니다.     

# 쓰기 쉬움
`async`와 `await` 키워드는 비동기 프로그래밍의 핵심입니다.      
이 두 키워드를 사용하는 것으로 .NET 과 Windows 런타임 환경에서 동기적 메서드를 만드는 만큼 쉽게 비동기 메서드를 만들 수 있습니다.    

```cs
static void DoIndependentWork()
{
    Console.WriteLine("Working...");
}
static public async Task<int> GetUrlContentLengthAsync()
{
    using var client = new HttpClient();

    Task<string> getStringTask = 
        client.GetStringAsync("https://learn.microsoft.com/dotnet");

    DoIndependentWork();

    string contents = await getStringTask;
    return contents.Length;
}
public static void Main(string[] args)
{
    Console.WriteLine(GetUrlContentLengthAsync());
}
```
비동기 메서드는 `async` 키워드를 포함하고, `Task` 형태의 타입을 반환하며, `Async`로 끝나는 이름을 가집니다.     
이번 코드에서는 `Task<string>`을 반환하기 때문에 `await`을 수행하여 `string`을 결과로 얻게 됩니다.     

`await` 키워드는 지정한 비동기 작업이 완료될 때까지 현재 메서드의 실행을 일시 중지합니다.      
단 프로그램 전체가 멈추는 것이 아닌, **호출한 쪽으로 제어가 넘어가는** 방식입니다.      
작업이 완료되면, 그 시점에서 `await` 이후 코드가 이어서 실행됩니다.    

---

다음 특성들은 비동기 메서드를 만드는 방법을 요약합니다.    

- 메서드에는 `async` 키워드가 포함됩니다.
- 권장되는 규칙으로, Async라는 접미사로 메서드 이름을 짓습니다.
- 반환 타임은 다음들 중 하나입니다.
  - 반환값이 있는 경우, `Task<반환값 타입>` 형태로 작성합니다.
  - 반환값이 없는 경우, `Task` 형태로 작성합니다.
  - 비동기 이벤트 핸들러의 경우 `void`로 작성합니다.
- `async` 키워드를 사용한 메서드는 일반적으로 하나 이상의 `await`의 식을 포함합니다.

# 비동기 메서드에서 발생하는 동작
비동기 프로그래밍에서 이해해야 할 가장 중요한 점은 _제어의 흐름_ 인데,      
다음 다이어그램은 이러한 프로세스를 보여줍니다.     
![image](https://github.com/user-attachments/assets/a2cadc4c-e602-4afe-bbcc-19818f15cb58)           
1. (호출 메서드 입장)       
비동기 메서드 `GetUrlContentLengthAsync`를 호출하고 대기합니다.
   
2. (GetUrlContentLengthAsync 입장)      
`HttpClient`를 생성하고 `GetStringAsync`를 비동기 호출하여       
웹 사이트의 콘텐츠를 문자열로 다운로드합니다.

3. (GetStringAsync 입장)       
자신을 호출한 함수에게 제어권을 넘겨줍니다.              
`Task<string>`에 저장하였음으로, `string`을 가져올 **작업**을 하고 있다는 것을 나타냅니다.

4. (GetUrlContentLengthAsync 입장)      
아직 `await`을 만나지 않았기 때문에,        
`GetStringAsync`의 실행에 영향을 받지 않는 작업을 할 수 있습니다.

5. (DoIndependentWork 입장)       
`DoIndependentWork`는 해당 작업을 수행하고 호출자에게 반환하는 동기 메서드입니다.

6. (GetUrlContentLengthAsync 입장)       
`GetStringAsync`와 연관 없는 코드는 모두 실행하였기 때문에,
`GetUrlContentLengthAsync`에서도 할 수 있는 작업이 없습니다.         
때문에 자신을 호출했던 메서드에게 다시 제어권을 넘깁니다. 이 때 반환 타입은 `Task<int>`이기 때문에        
`int`를 가져올 것이라 약속하는 셈입니다.

7. (GetUrlContentLengthAsync 입장)     
`GetStringAsync`의 작업이 완료되고, 문자열 결과를 가져옵니다.      
정확히는, `Task<string>`의 결과를 가져오지만 `await`이 작업이 완료되면 실제 결과값을 꺼내오는 역할도 하기 때문에
`contents` 변수에는 `string`을 가져옵니다.

8. (GetUrlContentLengthAsync 입장)       
문자열 결과가 있기 때문에, 문자열의 길이를 계산할 수 있습니다.
성공적으로 `GetUrlContentLengthAsync`의 작업도 완료되고, 호출 메서드로 다시 돌아갑니다.

# API 비동기 메서드
`async`와 `await`를 사용하기 위해 비동기로 작동할 수 있는 메서드를 찾는 방법이 있습니다.   

.NET Framework 4.5 이상 또는 .NET Core에서 비동기 메서드들이 많이 추가되었는데,     
이러한 메서드들은 보통 이름에 Async라느 접미사가 붙습니다.    

또한 `Task`, `Task<T>`의 형태로 반환형이 설정되어 있습니다.   

# 스레드
비동기 메서드는 비차단 작업이기 때문에, `await`에서 대기 중인 작업을 실행되는 동안 현재 스레드를 차단하지 않습니다.      
대신 메서드의 남은 부분을 이어서 처리하도록 제어권을 호출한 곳으로 반환하는 형식으로 진행됩니다.     

많은 사람이 오해하는 부분이 있는데, _비동기 메서드는 멀티스레딩_ 과 다릅니다.      
`async` 메서드 자체는 별도 스레드로 동작하도록 하는 개념이 없고, 하나의 스레드에서만 돌아가도록 되어 있습니다.    

때문에, CPU를 사용하지 않는 I/O 작업 등의 경우       
비동기로 코드가 작성되어 있다고 하여도 하나의 스레드만 사용하는 경우가 있을 수 있습니다.     

하지만 UI를 보이는 중에 CPU를 사용하거나 연산이 무거운 다른 작업을 할 때는      
멀티스레드 개념을 사용해 병렬적으로 작업을 하는 것이 효율적입니다.    
이 때 `Task.Run`을 사용해 **다른 스레드를 사용해 작업을 진행할 것이다**를 설정할 수 있습니다.         

# async 및 await
`async`를 사용하여 메서드를 지정하면 다음 2가지 기능을 사용할 수 있습니다.    
- `await` 키워드를 사용해 일시 중단 지점을 지정할 수 있습니다.
`await`은 대기 중인 비동기 프로세스가 완료될 때까지 해당 지점을 지나갈 수 없도록 지시하는 역할을 합니다.
(또한, 앞서 말한대로 Task안의 결과 값을 꺼내주는 역할도 합니다)
`await`의 일시 중단은 메서드의 종료를 의미하는 것은 아니기 때문에    
만약 예외 처리를 하는 경우라면 기다리는 중에는 `finally` 블록이 실행되지 않습니다.     

- `async`를 사용해 명명한 메서드 자체도 다른 메서드에 의해 대기되는 방식으로 호출될 수 있습니다.

비동기 메서드는 일반적으로 하나 이상의 `await` 연산자를 포함하지만,    
`await` 식이 없다고 해서 컴파일 오류가 발생하지는 않습니다.     
다만 이러한 경우에는, 그냥 동기 방식으로 메서드를 사용하는 것과 다른 점이 없습니다.     

# 반환 타입 및 매개 변수
비동기 메서드는 일반적으로 `Task` 또는 `Task<T>`를 반환합니다.     

메서드에 타입 `T`를 지정하는 return 문이 포함된 경우,    
반환 타입은 `Task<T>`의 형태로 지정됩니다.    

메서드에 return문이 없거나 반환하지 않는 경우,      
반환 타입은 `Task`의 형태로 지정됩니다.     

이 이외에도 `GetAwaiter`를 포함한 방식의 메서드가 있는 경우, 해당 방식에 맞춰서 반환 타입을 설정할 수 있습니다.    
(`await`은 대상이 `GetAwaiter`를 가지고 있는 경우에 자체적으로 실행해 사용는 형태인데,      
`Task`와 `Task<T>`도 `GetAwaiter`를 자체적으로 가지고 있기 때문에 `await`을 사용해 비동기 처리가 가능한 것입니다.)     

```cs
static async Task<int> GetTaskOfTResultAsync()
{
    int hours = 0;
    await Task.Delay(hours);

    return hours;
}
static async Task GetTaskAsync()
{
    await Task.Delay(0);
}
public static async void Main(string[] args)
{
    Task<int> returnedTaskTResult = GetTaskOfTResultAsync();
    int intResult = await returnedTaskTResult;
    // Task<T> 형태

    Task returnedTask = GetTaskAsync();
    await returnedTask;
    // Task 형태
}
```

반환된 각 작업은 진행 중인 작업을 의미하며,    
`Task`는 비동기 프로세스의 상태에 대한 정보를 캡슐화하고, 문제가 있을 경우 예외를 캡슐화합니다.     

비동기 메서드는 특이한 경우 `void`를 반환 타입을 가질 수도 있는데 (ex) 이벤트 핸들러),     
이러한 경우 대기할 수 없고 예외를 `catch`할 수 없습니다.    

비동기 메서드는 참조 타입을 반환하거나, 매개 변수로 사용할 수 없지만,    
이러한 변수가 있는 메서드를 호출하는 것은 가능합니다.   

# 명명 규칙
규칙에 따라, 일반적으로 `await` 가능한 타입을 반환하는 (ex) `Task`, `Task<T>`, `ValueTask`, `ValueTask<T>`)       
메서드에는 _**Async**_ 라는 접미사가 붙습니다.       

`await` 할 수 없는 타입을 반환하는 (ex) 비동기 작업 후 제어권 즉시 반환, 이벤트 핸들러, 오래된 비동기 패턴)
메서드는 _**Async**_라는 접미사를 사용하지 않습니다.    
대신 _**Begin, Start**_와 같은 접두사가 붙어 이 메서드가 결과를 반환하거나 throw하지 않음을 시사할 수 있습니다.     

다만, 이벤트 핸들러의 `OnButtonClick` 과 같이 명명 규칙에는 맞지 않지만 굳어진 경우           
이미 정해진 기존의 관례를 지키는 것이 맞습니다.   
