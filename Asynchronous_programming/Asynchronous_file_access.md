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
`WriteAllTextAsync`를 통해 특정 파일에 텍스트를 비동기적으로 작성할 수 있습니다.   

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

## 텍스트 읽기
다음 예제는 파일에 텍스트를 읽는 예를 보입니다.   

### 간단한 예
```cs
static public async Task SimpleReadAsync()
{
    string filePath = "simple.txt";
    string text = await File.ReadAllTextAsync(filePath);

    Console.WriteLine(text);
}
public static async Task Main(string[] args)
{
    await SimpleReadAsync();
}
```
`ReadAllTextAsync`를 통해 파일에 비동기적으로 접근할 수 있습니다.   

### 한정된 제어 예
```cs
static public async Task ProcessReadAsync()
{
    try
    {
        string filePath = "simple.txt";
        if (File.Exists(filePath))
        {
            string text = await ReadTextAsync(filePath);
            Console.WriteLine(text);
        }
        else
        {
            Console.WriteLine($"file not found: {filePath}");
        }
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.Message);
    }
}
static public async Task<string> ReadTextAsync(string filePath)
{
    using var sourceStream = 
        new FileStream(
            filePath,
            FileMode.Open, FileAccess.Read,FileShare.Read,
            bufferSize:4096, useAsync: true);

    var sb = new StringBuilder();

    byte[] buffer = new byte[0x1000];
    int numRead;
    while((numRead = await sourceStream.ReadAsync(buffer, 0, buffer.Length)) != 0)
    {
        string text = Encoding.UTF8.GetString(buffer,0,numRead);
        sb.Append(text);
    }
    return sb.ToString();
}
public static async Task Main(string[] args)
{
    await ProcessReadAsync();
}
```
해당 코드는 `FielStream`과 `ReadAsync`를 통해 세부 설정을 통한 비동기 읽기를 실행하는 코드입니다.   
buffer를 통해 파일의 내용을 청크 단위로 읽어오고, UTF8 방식을 통해 바이트를 인코딩(유니코드 문자열 변환)합니다.    

## 병렬 비동기 I/O
다음 예제에서는 10개의 텍스트 파일을 작성하여 병렬 처리를 하는 예를 보여 줍니다.  

### 간단한 예
```cs
public async Task SimpleParallelWriteAsync()
{
    string folder = Directory.CreateDirectory("tempfolder").Name;
    IList<Task> writeTaskList = new List<Task>();

    for (int index = 11; index <= 20; ++ index)
    {
        string fileName = $"file-{index:00}.txt";
        string filePath = $"{folder}/{fileName}";
        string text = $"In file {index}{Environment.NewLine}";

        writeTaskList.Add(File.WriteAllTextAsync(filePath, text));
    }

    await Task.WhenAll(writeTaskList);
}

static async Task Main(string[] args)
{
    await SimpleParallelWriteAsync();
}
```
위의 코드는 `tempfolder`라는 디렉토리를 생성한 뒤,       
`for` 문을 순회하며 새로운 txt 10개의 파일을 생성합니다.  
`WhenAll`을 통해 작업을 수행하기 때문에 병렬 처리가 가능해집니다.   

### 한정된 제어 예
```cs
public async Task ProcessMultipleWritesAsync()
{
    IList<FileStream> sourceStreams = new List<FileStream>();

    try
    {
        string folder = Directory.CreateDirectory("tempfolder").Name;
        IList<Task> writeTaskList = new List<Task>();

        for (int index = 1; index <= 10; ++ index)
        {
            string fileName = $"file-{index:00}.txt";
            string filePath = $"{folder}/{fileName}";

            string text = $"In file {index}{Environment.NewLine}";
            byte[] encodedText = Encoding.Unicode.GetBytes(text);

            var sourceStream =
                new FileStream(
                    filePath,
                    FileMode.Create, FileAccess.Write, FileShare.None,
                    bufferSize: 4096, useAsync: true);

            Task writeTask = sourceStream.WriteAsync(encodedText, 0, encodedText.Length);
            sourceStreams.Add(sourceStream);

            writeTaskList.Add(writeTask);
        }

        await Task.WhenAll(writeTaskList);
    }
    finally
    {
        foreach (FileStream sourceStream in sourceStreams)
        {
            sourceStream.Close();
        }
    }
}

static async Task Main(string[] args)
{
    await ProcessMultipleWriteAsync();
}
```
위의 예제는 기존의 방식들과 유사하게, 
`FileStream`을 통해 세부적인 설정을 한 후 비동기 처리를 하며       
`WhenAll`을 통해 병렬적으로 작업을 처리합니다.   

또한, 모든 작업이 완료되는 시점인 `finally` 블럭에서     
`FileStream`을 닫음으로써 작업이 완료되기 전 해당 객체가 삭제되는 것을 방지할 수 있습니다.    
