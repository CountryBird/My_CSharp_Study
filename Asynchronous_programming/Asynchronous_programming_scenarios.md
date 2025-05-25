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

# CPU 바인딩 및 I/O 바인딩된 시나리오 인
