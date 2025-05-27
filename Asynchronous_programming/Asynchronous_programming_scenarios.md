코드가 네트워크 _데이터 요청_, _데이터베이스 엑세스_, _파일 시스템 읽기, 쓰기_ 를 지원하기 위해            
I/O 바인딩된 시나리오를 구현하는 경우 비동기 프로그래밍이 가장 적절한 방법일 수 있습니다.      

# 비동기 프로그래밍 모델 살펴보기
`Task`와 `Task<T>` 개체는 비동기 프로그래밍의 핵심 개념입니다.        
이 둘은 `async`, `await` 키워드를 지원하며, 이 방법을 통해 비동기 작업을 모델링할 수 있습니다.     

- **I/O 바운드된 코드**란
디스크, 네트워크, DB, 파일 시스템 등 입출력 작업이 완료되기를 기다리는 작업을 의미합니다.
주로 `Task` 형태로 제공되고 있기에, `async` 메서드 안에서 `await`만 붙여주면 사용 가능합니다.     

- **CPU 바운딩된 코드**란
숫자 계산, 이미지 처리, 압축/해제 등 CPU가 집중적으로 계산이 필요한 작업을 의미합니다.
기본적으로 동기 메서드로 작성되어 있기 때문에 `Task.Run`으로 감싸 따로 비동기 처리를 해주어야 합니다.

## 기본 개념 검토
C# 코드에서 비동기 프로그래밍을 구현하면 컴파일러가 프로그램을 상태 기계로 변환합니다.     
상태 기계란 코드의 흐름을 추적하고 관리하는 구조를 의미하는데, 이를 통해 비동기 작업이 끝났을 때 중단 지점으로 돌아갈 수 있게 됩니다.    

또한, C#의 `Task` 기반 비동기 프로그래밍은 Promise 모델에 기반합니다.    
Promise는 비동기적으로, 언젠가 값이 생긴다는 약속을 하는 개념의 모델을 의미합니다.    

- I/O 바인딩 코드와 CPU 바인딩된 코드 모두에 비동기 코드를 사용할 수 있지만 구현은 다릅니다.
- 비동기 코드는 `Task`, `Task<T>` 개체를 사용합니다.
- `async` 키워드를 통해 _메서드를 비동기 메서드_ 로 선언할 수 있으며, 이에 따라 `await` 키워드를 사용할 수 있게 됩니다.
- `await` 키워드를 사용하면, 코드는 호출되고 있는 메서드를 일시 중지하고, 호출자에게 제어권을 반환합니다.

## I/O 바인딩 예제
아래 예시 코드는 특정 버튼을 클릭하였을 때, 웹에서 데이터를 다운로드하는 코드인데,       
비동기로 처리함으로써 해당 작업을 수행하는 와중 UI가 멈추는 현상을 방지합니다.    
```cs
s_downloadButton.Clicked += async (o, e) =>
{
    // This line will yield control to the UI as the request
    // from the web service is happening.
    //
    // The UI thread is now free to perform other work.
    var stringData = await s_httpClient.GetStringAsync(URL);
    DoSomethingWithData(stringData);
};
```
`(o,e)`는 이벤트 핸들러에서 자주 사용되는 패턴인데,     
`o`는 이벤트를 발생시킨 _객체_ 를 의미하며 주로 sender라고 불립니다.      
`e`는 이벤트와 관련된 _추가정보_ 를 의미하며 주로 EventArgs나 구체적인 파생 클래스 형태입니다.    

## CPU 바인딩 예제
CPU를 사용하는 코드도 비동기로 처리하면 UI가 멈추는 현상들을 방지할 수 있습니다.    
다만 자체적으로 `Task` 타입을 지원하지 않기 때문에 `Task.Run`의 형태로 한 번 더 처리가 필요합니다.   
```cs
static DamageResult CalculateDamageDone()
{
    return new DamageResult()
    {
        // Code omitted:
        //
        // Does an expensive calculation and returns
        // the result of that calculation.
    };
}

s_calculateButton.Clicked += async (o, e) =>
{
    // This line will yield control to the UI while CalculateDamageDone()
    // performs its work. The UI thread is free to perform other work.
    var damageResult = await Task.Run(() => CalculateDamageDone());
    DisplayDamage(damageResult);
};
```

# CPU 바인딩 및 I/O 바인딩된 시나리오 인식
효과적인 비동기 프로그래밍 구현을 하기 위해서는 바인딩된 시기를 식별하는 법을 이해하여야 합니다.         
구현에 대한 선택은 코드의 성능에 큰 영향을 줄 수 있습니다.    

코드 구현을 위한 선택을 위해, 2가지 질문을 할 수 있습니다.
|질문|시나리오|구현|
|--|--|--|
|코드가 작업의 결과를 기다리는가?|I/O 바인드|`async`, `await` 사용|
|코드가 고비용의 계산을 실행하는가?|CPU 바인드|`Task.Run` 사용|

# 다른 예제 살펴보기
이번 섹션에서는 C#에서 작성할 수 있는 비동기 코드 시나리오를 다루어 보겠습니다.     

## 네트워크에서 데이터 추출
다음 코드는 지정된 URL에서 HTML을 다운로드하고 HTML에서 문자열 ".NET"이 발생하는 횟수를 계산합니다.       
```cs
[HttpGet, Route("DotNetCount")]
static public async Task<int> GetDotNetCount(string URL)
{
    // Suspends GetDotNetCount() to allow the caller (the web server)
    // to accept another request, rather than blocking on this one.
    var html = await s_httpClient.GetStringAsync(URL);
    return Regex.Matches(html, @"\.NET").Count;
}
```
`HttpGet` 특성은 http로 들어오는 모든 요청 중에, Http 메서드가 GET으로 설정된 것을 처리함을 의미합니다.       
`Route` 특성은 경로와 관련된 특성인데, 주어진 문자열을 바탕으로 웹 페이지 경로를 만듭니다. (예의 경우 /컨트롤러/DotNetCount)      
이 두 어노테이션들은 ASP.NET Core Web API에서 사용되며, `Microsoft.AspNetCore.Mvc`를 선언해야 사용 가능합니다.     

## 여러 작업이 완료될 때까지 대기
일부 시나리오는 여러 데이터 조각을 동시에 검색할 필요가 있습니다.        
```cs
private static async Task<User> GetUserAsync(int userId)
{
    // Code omitted:
    //
    // Given a user Id {userId}, retrieves a User object corresponding
    // to the entry in the database with {userId} as its Id.

    return await Task.FromResult(new User() { id = userId });
}
```
`Task.FromResult`는 비동기로 작성된 메서드의 결과를 즉시 반환하는 용도,        
즉 **비동기 메서드를 동기적으로 완료된 Task를 반환**하기 위한 메서드입니다.             

때문에 위의 코드는 DB에서 비동기적으로 사용자 데이터를 가져오는 역할을 _모방_ 하는 코드입니다.      
```cs
private static async Task<IEnumerable<User>> GetUsersAsync(IEnumerable<int> userIds)
{
    var getUserTasks = new List<Task<User>>();
    foreach (int userId in userIds)
    {
        getUserTasks.Add(GetUserAsync(userId));
    }

    return await Task.WhenAll(getUserTasks);
}
```
위의 코드는 이전의 코드를 여러 개의 `User`가 있다고 가정하고 처리하는 코드입니다.        
`WhenAll` 메서드를 사용해 모든 Task가 종료될 때까지 기다리며, 이에 따른 결과를 반환합니다.      

```cs
private static async Task<User[]> GetUsersAsyncByLINQ(IEnumerable<int> userIds)
{
    var getUserTasks = userIds.Select(id => GetUserAsync(id)).ToArray();
    return await Task.WhenAll(getUserTasks);
}
```
위와 같이 LINQ 형태로 같은 효과를 내는 코드를 만들 수 있습니다.         

다만 이 때 일부 주의할 점이 있는데, LINQ는 기본적으로 지연 실행 형태이기 때문에 
기존의 `GetUserAsync`가 바라는 형태인 즉시 실행이 되지 않습니다.         
때문에 ToArray와 같은 메서드를 사용해 LINQ가 즉시 실행될 수 있게 변형하는 과정이 필요합니다.      

# 비동기 프로그래밍에 대한 고려 사항 검토
온전한 비동기 프로그래밍 사용을 위해 예기치 않은 동작을 방지하는 몇 가지 세부 정보를 알아두어야 합니다.      

## async() 메서드 본문 내에서 await 사용
`async` 한정자를 사용하는 경우 메서드 본문에는 하나 이상의 `await` 식이 필요합니다.      
사용하지 않을 경우 컴파일러가 경고를 생성하고                    
이를 무시하고 컴파일할 경우 메서드의 종료를 기다리지 않기 때문에,        
상황에 따라 무시되는 경우가 발생할 수 있습니다.         
때문에 이러한 코드는 프로세스를 비효율적으로 설계되게 만듭니다.     

## 비동기 메서드 이름에 _Async_ 접미사 추가
.NET 스타일 규칙은 모든 비동기 메서드 이름에 _Async_ 접미사를 추가하는 것입니다.       
이러한 명명법을 사용하면 동기/비동기 메서드를 보다 쉽게 구분할 수 있습니다.     

물론 코드에서 명시적으로 호출되지 않으며, 이러한 형태로 적용되지 않는 메서드들도 일부 있기 때문에        
크게 중요한 부분은 아닙니다.         

## 이벤트 핸들러에서만 `async void`를 반환
보통 비동기 메서드는 반환 타입이 없는 경우 `Task`,  반환 타입이 존재하는 경우 `Task<T>`를 사용합니다.        
다만 이벤트 핸들러의 경우 `async void`의 형식을 사용하며, 이에 따라 일부 제약 사항이 있습니다.         
- async void 메서드에서 던진 예외는 그 메서드 외부에서 잡을 수 없습니다.
- async void 메서드는 테스트가 어렵습니다.
- async void 메서드는 호출자가 비동기 메서드를 원하지 않는 경우, 부작용을 일으킬 수 있습니다.

굳이 이벤트 핸들러가 이러한 형태로 사용되는 이유는        
이벤트 핸들러가 동기적인 형태로 `Task`의 개념보다 먼저 나온 개념이었기 때문에,           
먼저 `void`의 형태로 굳어졌고 비동기 형태로 처리하기 위해 `async`를 붙이는 것이 최선이었다고 생각할 수 있습니다.      

## LINQ에서 비동기 람다에 주의
LINQ 식에서 비동기 람다를 구현할 때는 주의할 사항이 있습니다.        
LINQ는 기본적으로 지연 실행되는 형태입니다. 때문에 선언과 다른 타이밍에 코드가 실행되고,       
이러한 시나리오에서 차단을 수행하면 교착 상태를 발생시킬 수 있으며, 복잡한 오류 처리를 필요로 할 수도 있습니다.      

## 비블로킹 방식으로 Task 양보
프로그램에 Task의 결과가 필요한 경우 `await`를 사용하여 비차단 방식으로 코드를 작성하여야 합니다.       
즉, **현재 스레드를 멈추지 않고 비동기로 기다리는 방법**을 사용하라는 의미입니다.        

차단 방식으로 코드를 작성하면, 데드락이나 컨텍스트 스레드의 차단 등의 문제가 발생할 수 있습니다.        

다음은 Task 시나리오에 따른 차단 형태의 코드입니다.
|Task 시나리오|현재 코드|await 대체|
|--|--|--|
|백그라운드 작업 결과 가져오기|`Task.Wait()` 또는 `Task.Result`|`await`|
|아무 Task나 완료될 때까지 대기|`Task.WaitAny()`|`await Task.WhenAny()`|
|모든 Task가 완료될 때까지 대기|`Task.WaitAll()`|`await Task.WhenAll()`|
|일정 시간 대기|`Thread.Sleep()`|`await Task.Delay()`|

## ValueTask 타입 사용 고려
`Task` 타입은 힙이 할당되는 참조 타입이기 때문에,      
비동기 메서드가 `Task` 개체를 반환하면 특정 경로에서 병목 현상이 발생할 수 있습니다.     

`ValueTask` 타입을 사용하면,     
같은 효과를 내면서도 반환이 값 타입처럼 스택 또는 간단한 형태를 유지하기 때문에 효율적으로 처리가 가능할 수 있습니다.      

단, `ValueTask`는 `Task`에 비해 복잡도, 호환성 등의 성능이 좋지 않고       
한 번만 `await` 할 수 있다는 단점도 있습니다. 

## ConfigureAwait(false)의 설정 시기 이해
기본적으로 `await`는 **동기화 컨텍스트를 캡처**합니다.         
동기화 컨텍스트를 캡처한다는 것은,       
_다른 스레드에서 다른 작업을 끝내고 원래 스레드로 돌아온다는 것_ 을 의미합니다.

예를 들어, UI를 관리하는 스레드 코드 중     
잠깐 `await Task.Delay`를 하면 비동기 코드가 백그라운드 스레드에서 실행이 되고,      
이 후 UI 스레드로 돌아오기 때문에 스레드 관련 예외가 없이 안전하게 UI 요소를 업데이트할 수 있습니다.     

만약 이러한 처리가 필요가 없는 코드의 경우 `ConfigureAwait(false)`를 하여 동기화 컨텍스트를 캡처하지 않고,       
성능 최적화를 노려볼 수 있습니다.       

## 상태 의존성이 적은 코드 작성
_전역 객체의 상태_ 또는 _특정 메서드의 실행_ 에 따라 달라지는 코드를 작성하지 않아야 하며,      
메서드의 반환 값에만 의존하는 코드를 작성하는 것이 좋습니다.       
