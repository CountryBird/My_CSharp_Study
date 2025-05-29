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
