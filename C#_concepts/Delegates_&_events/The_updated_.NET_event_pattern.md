이전까지의 문서에서는 가장 일반적인 이벤트 패턴에 대해 설명했습니다.       
.NET Core에는 해당 패턴보다 쉬운 방식이 있는데, `EventHandler<T EventArgs>`에서 파생 클래스와 관련된 문제가 사라집니다.       

이는 유연성을 향상하면서 이전 버전과 호환된다는 장점이 있습니다.      

유연성부터 다뤄보자면,      
`EventArgs`를 상속받으면 내부적으로 `MemberwiseClone()`이라는 얕은 복사 기능을 활용할 수 있는데         
이 기능은 특정 이벤트 데이터 클래스에서는 크게 필요하지 않고, 이에 따라 굳이 `EventArgs`를 상속받아야 하는          
실질적인 이유가 없다는 것을 알 수 있습니다.       
.NET Core에서는 이런 제약이 사라졌기 때문에 더 자유롭게 이벤트 데이터를 설계할 수 있습니다.      

```cs
public class FileFoundArgs : EventArgs
{
    public string FileName { get; set; }
}
// 기존 방식

public class FileFoundArgs
{
    public string FileName { get; set; }
}
// .NET Core 방식
```

구조체 `SearchDirectoryArgs`를 설계함에 있어 고려해야 할 사항이 있습니다.     
```cs
internal struct SearchDirectoryArgs
{
    internal string CurrentSearchDirectory { get; }
    internal int TotalDirs { get; }
    internal int CompletedDirs { get; }

    internal SearchDirectoryArgs(string dir, int totalDirs, int completedDirs) : this()
    {
        CurrentSearchDirectory = dir;
        TotalDirs = totalDirs;
        CompletedDirs = completedDirs;
    }
}
```
매개변수가 없는 생성자를 호출함으로써, C# 규칙에 의해 '할당 전에 접근함' 문제를 방지할 수 있습니다.      

또한, `FileFoundArgs`는 구조체로 변환하면 안되는데,          
이벤트 취소 기능은 이벤트 argument를 참조로 전달해야 하는데, 이 때 구조체로 설계되면 복사본이 만들어집니다.          
따라서, 구독자가 값을 바꿔도 원래 파일 검색 클래스에서 변경을 볼 수 없게 됩니다.       

`EventHandler<TEventArgs>`에서 `TEventArgs : EventArgs` 제약을 제거했지만,         
기존 코드는 여전히 EventArgs를 상속받고 있으며 구독자들도 예전 패턴 그대로 동작합니다.        
따라서 .NET Core의 해당 변화는 **완전히 하위 호환적**인 것을 알 수 있습니다.      

# 비동기 구독자를 사용하는 이벤트
비동기 메서드는 void 반환 형식을 가질 수 있지만 권장되지 않습니다.          
이벤트 구독자 코드가 비동기 메서드를 호출하는 경우 `async void` 형태로 호출을 해야합니다.        
하지만 이 때, Task를 반환하지 않기 때문에 예외를 잡을 수도, await 할 수도 없습니다.    
```cs
worker.StartWorking += async (sender, eventArgs) =>
{
    try
    {
        await DoWorkAsync();
    }
    catch (Exception e)
    {
        Console.WriteLine($"Async task failure: {e.ToString()}");
    }
};
```
그래서 주로 사용하는 방식은, `async void`를 사용한 뒤, 이를 try-catch로 감싸는 패턴을 사용하는 것입니다.      
await 하려는 부분은 반드시 try/catch로 감싸야합니다.       
