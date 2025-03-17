C#에서는 예외가 발생할 수 있는 코드를 `throw`하고, 이를 수정할 수 있는 코드를 `catch` 할 수 있습니다.   

모든 예외는 `System.Exception`에서 상속되는 클래스이며, 이를 이용해 사용자 정의 예외 또한 만들 수 있습니다.  

```cs
class MyException : Exception
{
    public MyException() { }
    public MyException(string message) { }
}
private static void myMethod()
{
    throw new MyException();
}
```
위와 같이 생성자를 응용해, 일반적인 예외처럼 메시지를 받는 형태 또한 구현 가능합니다.  
```cs
 private static void myMethod()
 {
     throw new MyException();
 }
```
```cs
 try
 {
     myMethod();
 }
 catch (MyException e) 
 { 
     Console.WriteLine(e.ToString());
 }
```
예외가 발생하면, 런타임은 현재 코드가 `try` 블록 안에 있는지 확인합니다.  
만약 try 블럭 안에 있다면, 이와 연결된 `catch` 블록을 확인합니다. 
catch 블록 안에는 일반적으로 예외 형식이 지정되어 있는데, 지정된 형식이 예외와 동일하거나 기본 클래스이면 처리 가능합니다.   

예외를 발생시키는 코드가 try 블록에 없거나, 이를 처리하는 catch문이 없는 경우  (위의 코드에서 myMethod만 있는 경우)   
호출 메서드에 try catch 문을 확인합니다. (위의 코드 중 아래)  
이러한 형태로 최상위 문까지 예외가 전파될 수 있습니다.  

## catch 블록을 찾지 못한 경우
예외가 throw되면 호출 스택을 통해 차례차례 catch 블록을 찾지만, 결국 찾지 못할 경우 다음 3가지 중 하나가 발생합니다.

### - 스레드의 시작에 도달하는 경우
Main문으로 실행되는 프로그램 특성상, 가장 일반적인 경우입니다.    
예외가 처리되지 않고 `Main()`이나 해당 스레드의 시작점까지 도달하면 해당 스레드는 종료됩니다.  

### - 종료자 내부에서 발생하는 경우
`finalizer`, 종료는 C#의 가비지 컬렉터가 객체를 수거할 때 실행되는 ~ClassName() 형태의 메서드입니다. (완전히 들어 맞는 개념은 아니지만, 단순히 생성자의 반대 개념 정도라 생각하면 됩니다.)   
만약 예외가 종료자 내부에서 발생한 경우, 해당 종료자는 즉시 종료되지만 상위 클래스가 있다면 그 클래스의 종료자를 다시 호출합니다.  

### - 정적 생성자, 정적 필드 이니셜라이저에서 발생하는 경우
위 두 경우에서 예외가 발생하는 경우 `TypeInitializationException`이 던져집니다.   
이 예외에 _InnerException 속성_ 으로써 원래 발생한 예외가 저장됩니다.   
또한 정적 개념 특성상 한 번 실행되기 때문에, 예외가 발생한다면 해당 타입은 사용할 수 없습니다.
