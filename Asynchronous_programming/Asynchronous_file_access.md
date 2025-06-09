파일에 엑세스하느 목적으로 비동기 기능을 사용할 수 있습니다.     

다음과 같은 이유가 존재할 때 비동기를 추가하는 것을 고려할 수 있습니다.   

✔️ **UI 애플리케이션 응답성 개선**       
: 작업을 시작하는 UI 스레드가 다른 작업을 수행할 수 있게 합니다.    
✔️ **서버 기반 애플리케이션 확장성 개선**        
: 응답에 대해 처리하기 위해 종종 수천 개의 스레드를 사용할 수도 있는데, 비동기 처리를 하면     
기존 I/O 완료 스레드를 사용함으로써 그럴 필요가 없게 합니다.       
✔️ **파일 엑세스 작업의 대기 시간 고려**      
: 단순한 작업에서는 파일 엑세스 작업의 대기 시간이 작아보일 수 있지만,      
추후 확장에 따라 크게 늘어날 수 있습니다.      
✔️ **오버헤드 감소**      
: 비동기 기능을 사용하려는 경우 추가되는 오버헤드가 적습니다.    
✔️ **병렬 실행**
: 비동기 작업은 쉽게 병렬로 실행할 수 있습니다.    

# 적절한 클래스 사용
`File.WriteAllTextAsync`와 `File.ReadAllTextAsync`를 통해 비동기로 I/O를 하는 간단한 방법가 있습니다.     
내부적으로 비동기 스트림으로 처리하지만, 정형화되어 있어서 정교한 제어는 어렵습니다.    

`FileStream` 클래스를 사용하면 파일 I/O에 대한 정밀한 제어가 가능해집니다.    
객체 생성 과정에서 `useAsync: true` 또는 `FileOptions.Asynchronous`를 지정해 운영체제 수준에서 비동기 I/O를 수행할 수 있습니다.   
```cs
var stream = new FileStream("example.txt", FileMode.OpenOrCreate, FileAccess.Write, FileShare.None, 4096, useAsync: true);
```
정밀 제어를 위해서는 다음과 같은 파라미터를 모두 작성해주어야 합니다.

`StreamReader`와 `StreamWriter`에 직접적으로 파일 경로를 설정할 경우 비동기 설정이 되지 않습니다.    
하지만 `FileStream`에 먼저 설정을 하고 해당 스트림을 넘기는 것으로 비동기 설정을 할 수 있습니다.   
```cs
var fs = new FileStream("data.txt", FileMode.OpenOrCreate, FileAccess.ReadWrite, FileShare.None, 4096, useAsync: true);
var writer = new StreamWriter(fs);
await writer.WriteAsync("Hello Async!");
```

## 텍스트 쓰기
다음 예제에서는 파일에 텍스트를 작성하는 예를 보입니다.  

### 간단한 예
```cs
static public async Task SimpleWriteAsync()
{
    string filePath = "simple.txt";
    string text = $"Hello World!";

    await File.WriteAllTextAsync(filePath, text);
}
public static async Task Main(string[] args)
{
    await SimpleWriteAsync();
}
```
`WriteAllTextAsync`를 통해 특정 파일에 텍스트를 작성할 수 있습니다.   

### 한정된 제어 예
```cs
static async Task ProcessWriteAsync()
{
    string filePath = "temp.txt";
    string text = $"Hello World{Environment.NewLine}";

    await WriteTextAsync(filePath,text);
}
static async Task WriteTextAsync(string filePath, string text)
{
    byte[] encodedText = Encoding.Unicode.GetBytes(text);

    using var sourceStream = 
        new FileStream(
            filePath,
            FileMode.Create,FileAccess.Write, FileShare.None,
            bufferSize: 4096, useAsync: true);

    await sourceStream.WriteAsync(encodedText,0,encodedText.Length);
}
public static async Task Main(string[] args)
{
    await ProcessWriteAsync();
}
```
이전의 코드와 비교하여, `Stream`을 직접 설정하여 `WriteAsync`를 사용하기 때문에 
코드가 좀 더 길어졌지만, 세부 설정이 가능하다는 차이가 있습니다.  

`FileStream`에서,    
 - `FileMode.Create`: 파일이 존재하면 덮어쓰기, 없으면 새로 만드는 설정입니다.
 - `FileAcess.Write`: 쓰기 전용 접근 설정입니다.
 - `FileShare.None`: 다른 프로세스가 동시에 이 파일을 열수 없게 하는 설정입니다.
 - `useAsync: true`: 비동기 I/O를 최적화하는 설정입니다.   
