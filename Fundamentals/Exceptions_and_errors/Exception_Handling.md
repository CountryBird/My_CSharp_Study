`try` 블록은 예외의 영향을 받을 수 있는 코드를 분할하는 역할을 합니다.   
이에 연결된 `catch`는 결과 예외를 처리하는 데 사용됩니다.   
마지막으로 `finally`에는 예외 throw 여부와 관계없이 실행되는 코드가 포함됩니다.   

다음과 같은 형식으로 사용할 수 있습니다.
```cs
 try
 {
  // 예외 발생 가능성 코드
 }
 catch (Exception ex)
 {
  // 예외 처리 코드
 }
```
```cs
 try
 {
  // 예외 발생 가능성 코드
 }
 finally
 {
  // 반드시 실행할 코드
 }
```
```cs
 try
 {
  // 예외 발생 가능성 코드
 }
 catch
 {
  // 예외 처리 코드
 }
 finally
 {
  // 반드시 실행할 코드
 }
```

# catch 블록
`catch` 블록에서는 처리할 예외의 형식을 지정할 수 있으며, 이를 _예외 필터_ 라고 부릅니다.
모든 예외가 `Exception`에서 상속되기 때문에 물론 Exception도 블록에서 사용될 수 있지만, 일반적으로 선호되는 방법은 아닙니다.   
(어떤 예외를 처리해야 하는지 모르거나, catch 안에서 다시 throw할 생각이 아니면)    
```cs
try
{
    
}
catch (Exception ex)
{
    throw new DivideByZeroException();
}
```
---
예외 필터에 `when`을 이해 부울 식을 추가할 수도 있습니다.    
이를 이용해 같은 예외에 대한 경우도 상황에 따라 다르게 처리하는 것이 가능합니다.
```cs
 int myInt = -1;
 try
 {
     throw new Exception();
 }
 catch (Exception ex) when (myInt > 0)
 {
     Console.WriteLine("myInt is +");
 }
 catch (Exception ex)
 {
     Console.WriteLine("myInt isn't +");
 }
```
항상 false인 예외 필터를 만들어, 예외를 처리하기보다 단순 검사 용도로 사용하는 것도 일종의 방법입니다.
