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

# 분해 확장 메서드
위와 같이 사용자가 직접 정의한 클래스, 인터페이스, 구조체는 내부적으로 `Deconstruct`를 구현해 사용할 수 있습니다.   
하지만, 라이브러리에 이미 정의되어 있는 클래스, 인터페이스를 분해하고자 하는 경우가 있을 수 있습니다.

이러한 경우 수정이 불가능하기 때문에 기존 방법을 사용할 수 없습니다.    
```cs
public class MyClass
{
    public int myInt = 10;
    public string myString = "hello";
    public bool myBool = true;
}
```
이러한 라이브러리의 클래스가 있다고 가정합시다. (편의를 위해 임의로 작성한 클래스이며, 이대로 내부 코드 수정이 불가능하다고 가정합니다.)  
```cs
 public static void Deconstruct(this MyClass myClass, out bool myBool, out int myInt)
 {
     myBool = myClass.myBool;
     myInt = myClass.myInt;
 }
```
최상위 문 위치에 다음과 같은 형태의 `Deconstruct`를 사용하면, MyClass의 객체를 분해해서 임의의 분해 메서드를 만들 수 있습니다.    
`this` 키워드는 해당 클래스에 대한 확장 메서드를 작성한다의 의미 정도로 이해하면 되며, 이에 따라 분해 시에 제외됩니다. 

# 시스템 형식의 확장 메서드
`KeyValuePair`나 `Dictionary`와 같은 일부 시스템 형식은 편의를 위해 자체적으로 `Deconstruct` 메서드를 제공합니다.
