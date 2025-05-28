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
