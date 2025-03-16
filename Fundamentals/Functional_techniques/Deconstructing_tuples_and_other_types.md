`튜플`은 메서드 호출에서 여러 값에 대한 처리를 간단하게 합니다.  
`()`의 형태로 구성되며, Item1, Item2와 같이 자동으로 생성되는 속성을 통해 접근할 수 있습니다.  
```cs
static (string, int) method()
{
    return ("hello", 1);
}
private static void Main(string[] args)
{
    var m  = method();
    Console.WriteLine(m); // (hello, 1)
    Console.WriteLine(m.Item1); // hello
}
```

# 튜플
C#에서는 튜플 분해를 기본적으로 지원합니다.  
var 키워드를 사용해 한 번에 처리하는 방법과, 각 변수에 직접 처리하는 방법이 있습니다.
```cs
static (string, int) method()
{
    return ("hello", 1);
}
private static void Main(string[] args)
{
    var (myString1, myInt1)  = method();
    (string  myString2, int myInt2) = method();

    Console.Write(myString1); Console.WriteLine(myInt1); // hello1
    Console.Write(myString2); Console.WriteLine(myInt2); // hello1
}
```
물론 이미 선언된 변수에 할당하는 것도 가능하고, 변수 선언과 할당을 동시에 할 수 있습니다.  

# 무시 항목이 있는 튜플 요소
항상 튜플의 모든 요소를 사용하는 경우만 있지는 않기 때문에, C#에서는 튜플에 대해서도 무시 항목을 지정할 수 있습니다.  
```cs
var (myString1,_)  = method();
(string  myString2, int _) = method();
```

# 사용자 정의 형식
클래스, 구조체, 인터페이스는 `Deconstruct` 메서드를 구현하여 분해를 가능하게 구현할 수 있습니다.  
```cs
public class MyClass
{
    int myInt = 1;
    string myString = "hello";

    public void Deconstruct(out int outInt,out string outString)
    {
        outInt = myInt;
        outString = myString;
    }
}

```
파라미터에 out 키워드를 적어 주어야 합니다.
```cs
var myClass = new MyClass();
var (outInt, outString) = myClass; // 메서드 자동 호출
Console.WriteLine(outInt); // 1
Console.WriteLine(outString); // hello
```
Deconstruct 기능을 하는 메서드 자체는 아무 이름을 사용해도 구현이 가능하지만,  
메서드를 자동 호출되게 하려면 반드시 Deconstruct라는 이름을 사용해야 합니다.
