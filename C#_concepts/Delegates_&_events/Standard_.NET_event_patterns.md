.NET 이벤트는 일반적으로 몇 가지 알려진 패턴을 따릅니다.        
이러한 패턴을 표준화함으로써 개발자가 표준을 적용할 수 있습니다.      

# 이벤트 대리자 시그니처
.NET 이벤트 대리자에 대한 표준 시그니처는 다음과 같습니다.        
```cs
void EventRaised(object sender, EventArgs args);
```
- 반환 타입이 void
: 이벤트는 _무언가를 실행하라_ 는 신호일 뿐, 결과 값을 반환하지 않습니다.

- 이벤트는 sender를 포함
: 이벤트 핸들러는 첫 번째 매개변수는 이벤트를 발생시킨 sender 객체입니다.
: 타입은 항상 object로 선언되어 있지만, 실제로는 특정 클래스일 가능성이 높습니다.

- 이벤트에 추가 정보 패키지
: 이벤트 핸들러의 두 번째 매개변수는 이벤트에 대한 추가 정보를 담는 EventArgs 객체입니다.
: 예로, 마우스 이벤트라면 좌표, 키 이벤트라면 키 코드 같은 정보가 들어갑니다.

이벤트 모델을 사용하면 몇 가지 디자인 장점이 있습니다.
```cs
public class FileFoundArgs : EventArgs
{
    public string FoundFile { get; }

    public FileFoundArgs(string fileName) => FoundFile = fileName;
}
```
`EventArgs`를 상속하여, 이벤트가 발생했을 때 _어떤 파일이 발견되었는가_ 를 이벤트 구독자에게 알려주는 타입을 만듭니다.         
`FoundFile` 속성을 get으로만 설정하여 한 구독자가 데이터를 바꿔서 다른 구독자가 잘못된 값을 받는 일을 방지합니다.       

```cs
public class FileSearcher
{
    public event EventHandler<FileFoundArgs>? FileFound;

    public void Search(string directory, string searchPattern)
    {
        foreach (var file in Directory.EnumerateFiles(directory, searchPattern))
        {
            FileFound?.Invoke(this, new FileFoundArgs(file));
        }
    }
}
```
설정한 이벤트를 `Invoke`를 통해 이벤트를 발생시킬 수 있습니다.      

# 필드와 유사한 이벤트를 정의하고 발생시키기
이벤트를 클래스에 추가하는 가장 간단한 방법은 이전 예제와 같이 해당 이벤트를 public 필드로 선언하는 것입니다.      
```cs
public event EventHandler<FileFoundArgs>? FileFound;
```
해당 코드는 접근 수준이 잘못된 것처럼 보이지만, 컴파일러가 래퍼를 만들어 이벤트 개체에 안전한 방법으로 엑세스할 수 있도록 합니다.     
필드 내에서는 추가 및 제거 작업만을 할 수 있습니다.      
```cs
var fileLister = new FileSearcher();
int filesFound = 0;

EventHandler<FileFoundArgs> onFileFound = (sender, eventArgs) =>
{
    Console.WriteLine(eventArgs.FoundFile);
    filesFound++;
};

fileLister.FileFound += onFileFound;

fileLister.FileFound -= onFileFound;
```
람다 식을 사용해서 `remove`를 실행할 경우에는 처리기 해제가 제대로 작동하지 않습니다.      
또한 클래스 외부의 코드는 이벤트를 발생시키거나 다른 작업을 수행할 수 없습니다.    

# 이벤트 구독자에서 값 반환
기존 기능에 이어, _취소_ 기능을 추가해보도록 하겠습니다.      
_Found_ 이벤트를 발생시킬 때 수신기는 프로세스를 중지하며, 마지막으로 찾은 파일을 반환할 수 있어야 합니다.   

이벤트 처리기는 값을 반환하지 않으므로 또 다른 방법이 필요한데,       
`EventArgs` 객체를 사용하여 이벤트 구독자가 취소를 전달하는데 사용할 수 있습니다.    

이 시점에서 2가지 패턴을 구상할 수 있는데,      
첫 번째 패턴은 임의의 구독자 한 명이 작업을 취소하면 모든 구독자에게 해당 이벤트를 발생시키며,         
임의의 구독자 한 명이 작업을 재개하면 모든 구독자 또한 작업을 재개하는 패턴입니다.     
두 번째 패턴은 모든 구독자가 작업 취소를 원하는 경우에만 작업을 취소하는 패턴입니다.      

첫 번째 패턴에 대한 구현은 다음과 같습니다.     
```cs
public class FileFoundArgs : EventArgs
{
    public string FoundFile { get; }
    public bool CancelRequested { get; set; }

    public FileFoundArgs(string fileName) => FoundFile = fileName;
}
```

이벤트를 발생 시킨 후 플래그를 확인하여 취소가 요청되었는지 확인합니다.    
```cs
private void SearchDirectory(string directory, string searchPattern)
{
    foreach (var file in Directory.EnumerateFiles(directory, searchPattern))
    {
        var args = new FileFoundArgs(file);
        FileFound?.Invoke(this, args);
        if (args.CancelRequested)
            break;
    }
}
```

이러한 패턴의 장점은 확장성입니다.       
새 취소 프로토콜을 사용하지 않으려면 구독자 코드에 업데이트를 하지 않으면 되며, 해당 코드를 사용해도 문제가 발생하지 않습니다.    

첫 번째 파일을 찾으면 취소가 요청되도록 하는 구독자 코드는 다음과 같습니다. 
```cs
EventHandler<FileFoundArgs> onFileFound = (sender, eventArgs) =>
{
    Console.WriteLine(eventArgs.FoundFile);
    eventArgs.CancelRequested = true;
};
```

# 다른 이벤트 선언 추가
