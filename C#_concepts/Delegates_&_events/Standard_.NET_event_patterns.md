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
