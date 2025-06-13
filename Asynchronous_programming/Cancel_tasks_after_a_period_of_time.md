작업이 완료될 때따지 대기하지 않으려는 경우,      
일정 기간 후에 `CancellationTokenSource.CancelAfter` 메서드를 사용하여 비동기 작업을 취소할 수 있습니다.    
이 메서드는 지정된 기간 내에 작업이 완료되지 않으면, 해당 작업을 취소하는 역할을 합니다.    

_Cancel a list of tasks_ 에서 이어지는 내용이며, 일부 코드를 재사용하였다고 전제합니다.     

# 애플리케이션 진입점 업데이트
```cs
static async Task Main()
{
    Console.WriteLine("Application started.");

    try
    {
        s_cts.CancelAfter(3500);

        await SumPageSizesAsync();
    }
    catch (OperationCanceledException)
    {
        Console.WriteLine("\nTasks cancelled: timed out.\n");
    }
    finally
    {
        s_cts.Dispose();
    }

    Console.WriteLine("Application ending.");
}
```
`CancellationTokenSource` 타입인 `s_cts`를 사용합니다.   
`CancelAfter`를 사용해 일정 시간 뒤 취소를 예약할 수 있습니다. (파라미터 단위는 밀리초 입니다.)

다음 `SumPageSizeAsync`를 대기하며 작업이 완료되기를 기다립니다.    
만약 작업이 완료되기 이전에 취소 토큰이 트리거되면 `OperationCanceledException`이 throw 됩니다.    
