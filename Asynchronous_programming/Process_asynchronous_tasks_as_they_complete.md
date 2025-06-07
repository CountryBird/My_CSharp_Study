`Task.WhenAny`를 사용하면 동시에 여러 작업을 시작하고 완료 시 하나씩 처리하는 방식을 사용할 수 있습니다.    

해당 문서는 예제 코드로 설명을 진행합니다.    

# 필드 추가
```cs
static readonly HttpClient s_client = new HttpClient
{
    MaxResponseContentBufferSize = 1_000_000
};

static readonly IEnumerable<string> s_urlList = new string[]
{
   "https://learn.microsoft.com",
  "https://learn.microsoft.com/aspnet/core",
  "https://learn.microsoft.com/azure/devops",
  "https://learn.microsoft.com/dotnet",
  "https://learn.microsoft.com/dynamics365",
  "https://learn.microsoft.com/enterprise-mobility-security",
  "https://learn.microsoft.com/gaming",
  "https://learn.microsoft.com/graph",
  "https://learn.microsoft.com/microsoft-365",
  "https://learn.microsoft.com/office",
  "https://learn.microsoft.com/powershell",
  "https://learn.microsoft.com/sql",
  "https://learn.microsoft.com/surface",
  "https://learn.microsoft.com/system-center",
  "https://learn.microsoft.com/visualstudio",
  "https://learn.microsoft.com/windows"
};
```
`HttpClient`는 HTTP 요청을 보내고 응답을 받는 기능을 가진 객체이며,     
`s_silent`는 콘텐츠 버퍼링 최대 바이트에 대한 설정을 한 HttpClient입니다.    
(참고로, MaxResponseContentBufferSize 속성은 HttpClient가 버퍼링 방식에서 스트리밍 방식으로 사용되면서 이제는 잘 사용되지 않습니다.)  
`s_urlList`는 프로그램에서 처리하고자 하는 url의 목록입니다.   

# 애플리케이션 진입점 업데이트
```cs
static Task Main() => SumPageSizesAsync();
```
위와 같이 Main문을 작성하면, Async main으로 간주되어 비동기 실행됩니다.  

# 비동기 페이지 크기 합계 메서드
Main문에서 실행하고자 하는 `SumPageSizeAsync` 메서드를 작성합니다.    
```cs
static async Task SumPageSizesAsync()
{
    var stopwatch = Stopwatch.StartNew(); // 소요 시간 측정 시작
    
    IEnumerable<Task<int>> downloadTasksQuery =
        from url in s_urlList
        select ProcessUrlAsync(url,s_client); // 각 url 주소에 따라 다운로드 작업 실행

    List<Task<int>> downloadTasks = downloadTasksQuery.ToList(); // 즉시 실행을 위해

    int total = 0;
    while (downloadTasks.Any())
    {
        Task<int> finishedTask = await Task.WhenAny(downloadTasks); 
        // 제일 먼저 끝나는 작업을 가져옴 (await 안하면 Task 껍데기만 가져오는 꼴)

        downloadTasks.Remove(finishedTask);
        // 해당 작업 삭제

        total += await finishedTask;
        // 결과 값 갱신 (Task 객체에서 가져 옴)
        // 만약 동기적으로 작성하면 데드락 문제 발생 가능성
    }

    stopwatch.Stop(); // 소요 시간 측정 정지

    Console.WriteLine($"\nTotal bytes returned: {total:#,#}");
    Console.WriteLine($"Elapsed time:           {stopwatch.Elapsed}\n");
}
```

# 프로세스 메서드 추가
`SumPageSizesAsync`에서 실행하는 `ProcessUrlAsync` 메서드를 작성합니다.    
```cs
 static async Task<int> ProcessUrlAsync(string url, HttpClient client)
 {
     byte[] content = await client.GetByteArrayAsync(url);
     Console.WriteLine($"{url,-60} {content.Length,10:#,#}");

     return content.Length;
 }
```
`GetButeArrayAsync`는 해당 URL에서 바이트 값을 가져오는 메서드입니다.    

`{url,-60}`의 의미는 전체 너비 60칸을 할당하고 왼쪽 정렬하여 url 문자열을 보이는 것을,   
`{content.Length,10:#,#}`의 의미는 전체 너비 10칸을 할당하고 오른쪽 정렬한 후, 1000단위로 ,를 표시하며 content.Length를 보이는 것을 의미합니다.  

---

> 예제에서 볼 수 있듯이, `WhenAny`는     
> 하나하나 적용시킬 작업이 있을 때 사용하기 때문에      
> 적은 수의 작업이 필요한 문제 해결에 적합합니다.   
