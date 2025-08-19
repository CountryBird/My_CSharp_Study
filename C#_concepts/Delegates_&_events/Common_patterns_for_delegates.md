_대리자_ 는 구성 요소 간의 최소한의 결합을 포함하는 소프트웨어 설계를 가능하게 하는 메커니즘을 제공합니다.   
그 중 좋은 예는 `LINQ`로, LINQ의 쿼리 식 패턴은 모든 기능에 대해 대리자 기능을 사용합니다.   
```cs
var smallNumbers = numbers.Where(n => n < 10);
```
`Where` 메서드는 시퀀스의 요소가 필터를 통과하는지 결정하는 대리자를 사용합니다.     
또한, `Where` 메서드의 프로토타입은 다음과 같습니다.   
```cs
public static IEnumerable<TSource> Where<TSource> (this IEnumerable<TSource> source, Func<TSource, bool> predicate);
```
이러한 방식을 통해 대리자가 구성 요소 간의 결합을 거의 요구하지 않는 방식으로 사용할 수 있습니다.     
특정 기본 클래스에서 파생되는 클래스를 만들 필요가 없으며, 특정 인터페이스를 구현할 필요도 없습니다.      
현재 작업에 기본적 메서드의 구현을 제공만 하면 됩니다.   

# 대리자를 사용하여 사용자 고유 구성 요소 빌드
대리자를 사용하는 디자인을 사용해 _로그 메시지_ 에 대한 구성 요소를 정의하도록 하겠습니다.        

- 라이브러리 구성 요소는 여러 다른 플랫폼의 다양한 환경에서 사용할 수 있어야 합니다.      
- 시스템의 모든 구성 요소로 부터 메시지를 수락할 수 있어야 합니다.      
- 메시지들 간에는 특정 우선 순위가 존재합니다.
- 메시지에는 타임스탬프가 있어야 합니다.

또한 메시지 기록 위치에 대한 것도 고려해야 하는데,      
크게 콘솔, 파일, 데이터베이스, OS 이벤트 로그 등을 고려할 수 있습니다.      

이러한 여러 가지 선택지 중 대리자를 기반으로 디자인함으로써 유연성을 가지고,       
나중에 추가될 수 있는 저장 메커니즘을 지원할 수 있도록 합니다.    

# 첫 번째 구현
가장 기본적이고 초기적으로 생각할 수 있는 방법으로 시작해보겠습니다.       
```cs
public static class Logger
{
  public static Action<string>? WriteMessage;
  public static void LogMessage(string msg)
  {
    if (WriteMessage is not null)
    {
      WriteMessage(msg);
    }
  }
}
```
정적 클래스가 멤버로 대리자 하나를 가지고,        
이를 실행하는 형태입니다.     

```cs
public static class LoggingMethods
{
  public static void LogToConsole(string message)
  {
    Console.Error.WriteLine(message);
  }
}
```
이는 콘솔에 메시지를 쓰는 메서드에 대한 구현입니다.    

마지막으로, Logger에 연결된 대리자에 이를 연결하여 설정합니다.
```cs
Logger.WriteMessage += LoggingMethods.LogToConsole;
```

## 관행
위의 코드는 간단한 코드 예시지만, 대리자가 포함된 디자인에 대한 몇 가지 지침을 보여줍니다.        

핵심 프레임워크에 정의된 대리자 타입을 사용하면 사용자가 대리자를 더 쉽게 작업할 수 있습니다.        
새 타입을 정의할 필요가 없으며 사용하는 개발자도 특수화된 새로운 대리자 타입을 학습할 필요가 없습니다.      

사용되는 인터페이스는 최소화되고 유연합니다.     

# 출력 타입 지정
