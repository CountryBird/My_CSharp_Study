작업이 완료되기를 기다리지 않으려는 경우 비동기 콘솔 애플리케이션을 취소할 수 있습니다.    
`CancellationTokenSource`를 각 작업에 연결하여 많은 작업을 취소할 수 있습니다.   

# 필드 추가
```cs
static readonly CancellationTokenSource s_cts = new CancellationTokenSource();;

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
`s_client`는 콘텐츠 버퍼링 최대 바이트에 대한 설정을 한 HttpClient입니다.    
`s_urllist`는 프로그램에서 처리하고자 하는 url의 목록입니다.     

# 애플리케이션 진입점 업데이트
```cs
public static async Task Main(string[] args)
{
    Console.WriteLine("Application started.");
    Console.WriteLine("Press the ENTER key to cancel...\n");

    Task cancelTask = Task.Run(() =>
    {
        while (Console.ReadKey().Key != ConsoleKey.Enter)
        {
            Console.WriteLine("Press the ENTER to cancel...");
        }

        Console.WriteLine("\nEnter key pressed: cancelling downloads.\n");
        s_cts.Cancel();
    });

    Task sumPageSizesTask = SumPageSizeAsync();

    Task finishedTask = await Task.WhenAny(new[] { cancelTask, sumPageSizesTask });
    if(finishedTask == cancelTask)
    {
        try
        {
            await sumPageSizesTask;
            Console.WriteLine("Download task completed before cancel request was processed");
        }
        catch(TaskCanceledException)
        {
            Console.WriteLine("Download task has been cancelled");
        }
    }
    Console.WriteLine("Application ending,");
}
```
업데이트된 `Main` 문은 비동기 진입점을 허용하는 형태입니다.    

`cancelTask`는 위의 코드에서 설정한 s_cts를 사용하는 작업으로,    
_Enter_ 키를 누르면 `Cancel`메서드를 통해 취소 토큰을 트리거합니다.     
**명심해야 할 점은 해당 메서드는 `Cancel` 메서드에 의해 취소되는 게 아니라, 자신의 작업을 끝내고 정상 종료하는 형태입니다.**

`sumPageSizesTask`는 이후 기술할 메서드에 의한 작업으로,      
url을 기반으로 하는 일반적인 작업입니다.    

이후 `WhenAny`를 통해 먼저 종료되는 작업을 가져오고,    
이에 따라 다른 출력문을 보이는 형태입니다.    

# 비동기 페이지 크기 합계 메서드
```cs
static async Task SumPageSizesAsync()
{
    var stopwatch = Stopwatch.StartNew();

    int total = 0;
    foreach (string url in s_urlList)
    {
        int contentLength = await ProcessUrlAsync(url, s_client, s_cts.Token);
        total += contentLength;
    }

    stopwatch.Stop();

    Console.WriteLine($"\nTotal bytes returned:  {total:#,#}");
    Console.WriteLine($"Elapsed time:          {stopwatch.Elapsed}\n");
}
```
해당 코드는 `ProcessUrlAsync`에 작업을 맡기고     
작업 시간을 측정하여 결과 값과 작업 시간을 보이는 메서드입니다.    

# 프로세스 메서드
```cs
static async Task<int> ProcessUrlAsync(string url, HttpClient client, CancellationToken token)
{
    HttpResponseMessage response = await client.GetAsync(url, token);
    byte[] content = await response.Content.ReadAsByteArrayAsync(token);
    Console.WriteLine($"{url,-60} {content.Length,10:#,#}");

    return content.Length;
}
```
해당 메서드는 `CancellationToken.Token`을 추가 파라미터로 받아 비동기 작업을 수행하는데,     
이 때 token의 값에 따라 `TaskCanceledException`을 발생시키는지 여부를 결정합니다.    

만약 취소가 되지 않는다면 url을 통해 int 형태의 값을 반환하고,     
취소가 된다면 예외를 발생시켜 이를 `Main`문까지 올립니다.    
