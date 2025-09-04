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

