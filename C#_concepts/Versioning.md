# 언어 버전
C# 컴파일러는 .NET SDK의 일부인데,        
이에 따라 컴파일러는 프로젝트에 대해 .NET 버전에 맞는 C# 언어 버전을 선택합니다.        
SDK 버전이 선택한 프레임워크보다 큰 경우 컴파일러에서 더 큰 언어 버전을 사용할 수 있습니다.             
또한 원한다면, 프로젝트에서 _LangVersion_ 요소를 설정하여 기본값을 변경할 수 있습니다.  

LangVersion 요소를 latest로 설정하는 것은 권장되지 않는데,       
latest는 이름 그대로 설치된 컴파일러가 최신 버전을 사용하다는 의미를 가집니다.       
이는 머신마다 변경될 수 있어 빌드를 불안정하게 만들 가능성이 있습니다.    

# 라이브러리 작성
공용 .NET 라이브러리를 만든 개발자라면 새로운 업데이트를 출시해야 하는 상황을 겪습니다.             
이에 따른 새 릴리즈를 만들 때 다음과 같은 몇 가지 사항을 고려해야 합니다.    

## 의미론적 버전 관리
특정 중요 시점 이벤트를 나타내기 위해 라이브러리의 버전에 적용되는 명명 규칙으로,             
라이브러리에 제공하는 버전 정보를 통해 개발자는 프로젝트 간의 호환성을 나타낼 수 있습니다.          

가장 기본적이고 통상적으로 사용되는 방법은 `MAJOR.MINOR.PATCH` 형식입니다.     
- `MAJOR`: 호환되지 않는 API 변경 사항이 있을 때 번호가 증가합니다.
- `MINOR`: 이전 버전과의 호환성은 유지하면서, 기능이 추가될 때 번호가 증가합니다.
- `PATCH`: 하위 호환성 버그 등을 수정했을 때 번호가 증가합니다.

## MAJOR 버전 번호 
MAJOR 버전 번호를 갱신할 수 있는 경우는 주로 다음과 같습니다.      
사용자는 이전 버전의 프로그램을 사용하지 못하게 되며, 이에 따라 업데이트가 필요합니다.        

- 공용 메서드 또는 속성 제거
```cs
// Version 1.0.0
public class Calculator
{
    public int Add(int a, int b) => a + b;
    public int Subtract(int a, int b) => a - b; // This method exists
}

// Version 2.0.0 - MAJOR increment required
public class Calculator
{
    public int Add(int a, int b) => a + b;
    // Subtract method removed - breaking change!
}
```

- 메서드 서명(파라미터) 변경
```cs
// Version 1.0.0
public void SaveFile(string filename) { }

// Version 2.0.0 - MAJOR increment required
public void SaveFile(string filename, bool overwrite) { } // Added required parameter
```

- 기존과 메서드 흐름이 변경되는 경우
```cs
// Version 1.0.0 - returns null when file not found
public string ReadFile(string path) => File.Exists(path) ? File.ReadAllText(path) : null;

// Version 2.0.0 - MAJOR increment required
public string ReadFile(string path) => File.ReadAllText(path); // Now throws exception when file not found
```

## MINOR 버전 번호
MINOR 버전 번호를 갱신할 수 있는 경우는 주로 다음과 같습니다.      
기존 코드를 수정하지 않고 새 기능을 추가하기 때문에, 사용자가 반드시 업데이트할 필요는 없습니다.     

- 새 공용 메서드 또는 속성 추가
```cs
// Version 1.0.0
public class Calculator
{
    public int Add(int a, int b) => a + b;
}

// Version 1.1.0 - MINOR increment
public class Calculator
{
    public int Add(int a, int b) => a + b;
    public int Multiply(int a, int b) => a * b; // New method added
}
```

- 새 오버로드 추가
```cs
// Version 1.0.0
public void Log(string message) { }

// Version 1.1.0 - MINOR increment
public void Log(string message) { } // Original method unchanged
public void Log(string message, LogLevel level) { } // New overload added
```

- 기존 메서드에 선택적 파라미터 추가
```cs
// Version 1.0.0
public void SaveFile(string filename) { }

// Version 1.1.0 - MINOR increment
public void SaveFile(string filename, bool overwrite = false) { } // Optional parameter
```

## PATCH 버전 번호
PATCH 버전 번호를 갱신할 수 있는 경우는 다음과 같습니다.      
기존의 기능을 추가, 변경 없이 문제를 해결합니다.    

- 기존 메서드의 구현에서 버그 수정
```cs
// Version 1.0.0 - has a bug
public int Divide(int a, int b)
{
    return a / b; // Bug: doesn't handle division by zero
}

// Version 1.0.1 - PATCH increment
public int Divide(int a, int b)
{
    if (b == 0) throw new ArgumentException("Cannot divide by zero");
    return a / b; // Bug fixed, behavior improved but API unchanged
}
```
- API를 변경하지 않는 성능 향상
```cs
// Version 1.0.0
public List<int> SortNumbers(List<int> numbers)
{
    return numbers.OrderBy(x => x).ToList(); // Slower implementation
}

// Version 1.0.1 - PATCH increment
public List<int> SortNumbers(List<int> numbers)
{
    var result = new List<int>(numbers);
    result.Sort(); // Faster implementation, same API
    return result;
}
```

## 이전 버전과의 호환성
라이브러리의 새 버전을 릴리즈할 때 신경써야 할 점 중 하나는 _이전 버전과의 호환성_ 입니다.          
라이브러리 이전 버전과의 호환성을 유지하고자 할 경우 다음 사항을 고려해야 합니다.      

- 가상 메서드
: 새 버전에서 가상 메서드를 가상이 아닌 상태로 만들 경우, 해당 메서드를 재정의하는 프로젝트를 업데이트해야 합니다.
큰 변화가 생기므로 권장되지 않습니다.

- 메서드 시그니처(파라미터)
: 메서드 동작 업데이트 시 시그니처도 변경해야 하는 경우, 해당 메서드에 대한 코드 호출이 계속 작동하도록 오버로드를 새로 만드는 방법이 권장됩니다.

- Obsolte 특성
: 더 이상 권장되지 않는 클래스나 클래스 멤버를 지정하고, 이를 이후 버전에서 제거할 것이라 경고할 수 있습니다.
해당 라이브러리를 사용하는 개발자는 이러한 변화에 더 잘 대비할 수 있습니다.

- 선택적 메서드 인수
: 이전에는 선택 사항이었던 메서드 인수를 필수로 만들거나 기본값을 변경하는 경우,
해당 인수를 제공하지 않는 모든 코드를 업데이트해야 합니다.


## 애플리케이션 구성 파일
.NET 개발자는 대부분의 프로젝트 유형에 존재하는 _app.confing_ 파일을 접해봤을 가능성이 높습니다.          
일반적으로 라이브러리를 설계할 때 저기적으로 변경될 가능성이 있는 정보는 해당 파일에 저장하도록 해야 합니다.           
이렇게 하면 관련으로 업데이트 사항이 있을 때 라이브러리를 다시 컴파일하지 않고도 원활한 업데이트가 가능해집니다.       

# 라이브러리 소비
다른 개발자가 만든 .NET 라이브러리를 사용하는 개발자는 라이브러리의 새로운 버전이              
자신의 프로젝트와 호환되지 않을 가능성을 고려해야 합니다.          

C#과 .NET에는 이러한 문제에 대한 기능과 기술이 마련되어있습니다.           

## 어셈블리 바인딩 리디렉션
_app.config_ 파일을 사용하여 라이브러리의 버전을 업데이트 할 수 있습니다.            
`<bindingRedirect>` 를 추가하여 앱을 컴파일하지 않고도 새 라이브러리 버전을 사용할 수 있습니다.      
```cs
<dependentAssembly>
    <assemblyIdentity name="ReferencedLibrary" publicKeyToken="32ab4ba45e0a69a1" culture="en-us" />
    <bindingRedirect oldVersion="1.0.0" newVersion="1.0.1" />
</dependentAssembly>
```

## new 키워드
상속된 기본 클래스의 멤버를 숨기려면 `new` 키워드를 사용합니다.      
이 방법을 통해 파생 클래스가 기본 클래스의 업데이트에 적절하게 대처할 수 있습니다.       

```cs
public class BaseClass
{
    public void MyMethod()
    {
        Console.WriteLine("A base method");
    }
}

public class DerivedClass : BaseClass
{
    public new void MyMethod()
    {
        Console.WriteLine("A derived method");
    }
}

public static void Main()
{
    BaseClass b = new BaseClass();
    DerivedClass d = new DerivedClass();

    b.MyMethod();
    d.MyMethod();
}
```
```console
A base method
A derived method
```
기본 클래스에 존재하는 메서드를 파생 클래스에서 `new`로 선언함으로써,          
파생 클래스만의 동명의 다른 메서드를 만들 수 있습니다.       
또한, `new` 키워드를 지정하지 않으면 기본적으로 숨기도록 설정되어 있습니다.

## override 키워드
`override` 키워드를 사용하면 파생된 구현은 기본 클래스 멤버의 구현을 숨기지 않고 _확장_ 합니다.               
대신 기본 클래스 멤버에 `virtual` 키워드를 적용해야 합니다.         

```cs
public class MyBaseClass
{
    public virtual string MethodOne()
    {
        return "Method One";
    }
}

public class MyDerivedClass : MyBaseClass
{
    public override string MethodOne()
    {
        return "Derived Method One";
    }
}

public static void Main()
{
    MyBaseClass b = new MyBaseClass();
    MyDerivedClass d = new MyDerivedClass();

    Console.WriteLine($"Base Method One: {b.MethodOne()}");
    Console.WriteLine($"Derived Method One: {d.MethodOne()}");
}
```
```console
Base Method One: Method One
Derived Method One: Derived Method One
```

## new와 override의 차이
new와 override는 비슷한 역할을 하는 것 같지만, 원리와 이로 인한 결과의 차이가 있습니다.          
new 키워드는 컴파일 시 결정이 된다는 점, override는 실행 시 결정이 됩니다.                  

이로 인해, 실제 객체가 무엇이냐에 따라 다른 결과를 만들어 낼 수 있습니다.         

```cs
class Parent
{
    public void Hello() => Console.WriteLine("Parent Hello"); // new 용
    public virtual void Hi() => Console.WriteLine("Parent Hi"); // override 용
}

class Child : Parent
{
    public new void Hello() => Console.WriteLine("Child Hello");
    public override void Hi() => Console.WriteLine("Child Hi");
}

```
다음과 같이 코드가 작성되어 있다고 가정할 때,       
참조 타입이 Parent면
```cs
Parent p = new Child();
p.Hello(); // Parent Hello  ← new는 부모 타입 기준
p.Hi();    // Child Hi     ← override는 실제 객체 기준
```

참조 타입이 Child면
```cs
Child c = new Child();
c.Hello(); // Child Hello
c.Hi();    // Child Hi
```

overrride -> 항상 파생 것이 실행 / new -> 참조 타입에 따라 달라짐               
으로 이해하면 됩니다.
