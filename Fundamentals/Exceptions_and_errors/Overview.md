C#에서는, 프로그램 실행 중 발생하는 예기치 못한 문제와 예외 사항을 처리하는 데 도움을 주는 기능을 제공합니다.   
예외를 처리하지 않으면, 프로그램이 갑자기 종료되는 문제가 발생할 수 있기 때문에 이를 방지하기 위해 처리가 _꼭_ 필요합니다.
```cs
 double a = 10;
 double b = 0;
 double result;

 try
 {
     result = SafeDivision(a, b);
     Console.WriteLine("{0} divided by {1} = {2}", a, b, result);
 }
 catch (DivideByZeroException)
 {
     Console.WriteLine("Attempted divide by zero");
 }
 finally
 {
     Console.WriteLine("Finally is executed regardless of if an exception is thrown");
 }
```
`try` =  일반적인 코드와 유사하게 작성하지만, 예외를 일으킬 수 있는 경우 try 블럭을 감쌉니다.   

`catch` = ()안에 처리하고자 하는 예외를 설정하며, 여러 개가 설정 가능하고 가장 위쪽부터 처리하기 때문에 아래쪽이 가장 포괄적인 개념이 되게 하는 것이 바람직합니다.   
(가장 포괄적인 개념이 가장 위에 오면 그 아래는 사실상 의미가 없는 코드가 됨)   

`finally` = 예외 발생 여부와 **관계없이** 실행되는 부분으로, 주로 파일 닫기나 리소스 해제와 같이 반드시 일어나는 마무리 작업에 사용됩니다.

# C#은 throws가 없다
C#과 유사한 형태를 가진 Java 언어에서, 메서드 옆에 `throws`가 붙는 형태가 있습니다.   
이는 해당 메서드가 특정 예외를 일으킬 가능성이 있음을 명시합니다.  

## 체크 예외
체크 예외는 _컴파일 시간에 반드시 예외 처리를 해야하는 예외_ 를 의미합니다.  
컴파일러가 예외 처리를 강제하며, 직접 해결하거나 (try catch) 상위에 처리를 맡기는 (throws) 형태로 가능합니다.   
Java 언어에서 throws 해야하는 것이 이러한 체크 예외이며, 위에서 설명했듯이 예외를 일으킬 가능성이 있는 부분에 체크되기 때문에 코드가 안정적으로 작성된다는 장점이 있습니다.  

## 언체크 예외
언체크 예외는 _런타임 시간 중 발생하는 예외_ 를 의미합니다.   
컴파일러가 예외 처리를 강제하지 않습니다.   
덕분에 코드가 간결해지고 복잡하지 않아지지만, 실수로 인한 예외의 가능성이 늘어난다는 단점이 있습니다.   
**_C#은 모든 예외를 언체크 예외로 처리합니다._**

##

이러한 이유로 Java와 다르게 C#은 체크 예외 개념이 없기 때문에 `throws` 개념 또한 없습니다.   
물론 `throws`를 한 것처럼 하위에서 예외가 발생하면 자동으로 상위로 전파는 되기 때문에 원하는 위치에서 예외를 처리하기만 하면 됩니다.
