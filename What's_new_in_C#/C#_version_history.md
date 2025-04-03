해당 문서는 C# 1.0부터 지금까지의 버전까지의 기능을 정리한 문서이며,    
작성일 기준 안정적 C# 버전은 12입니다.   

# C# 1.0
_C# 1.0_ 은 _Java_ 와 매우 비슷한 형태였으며, Visual Studio .Net 2002와 함께 나타났습니다.   
**"단순하고 현대적인 범용 객체 지향 언어"** 를 추구했습니다.

클래스, 구조체, 인터페이스, 이벤트, 속성, 대리자, 연산자, 문장, 특성 등은 있었지만,     
비동기, 제네릭, LINQ와 같은 기능은 아직 존재하지 않았습니다.     

# C# 1.2
_C# 1.2_ 는 Visual Studio .Net 2003과 함께 나타났습니다.    
이전 버전까지는 `foreach`가 `IDisposable` (리소스 정리를 위한 인터페이스)을 구현한다면 따로 Dispose를 작성해주어야 했지만,    
이 버전부터는 자동으로 해당 메서드가 호출되게 바뀌었습니다.

# C# 2.0
_C# 2.0_ 은 Visual Studio 2005와 함께 나타났습니다.    
해당 버전부터 제네릭 타입을 지원하기 시작하여, 임의의 형식에 대한 처리가 쉬워졌습니다.   
또한 기존까지 `foreach` 순회를 위해서는 `IEnumberable`, `IEnumerator`(순회를 위한 인터페이스)를 구현한 구조가 필요했지만,      
이 버전부터는 `Iterator` 개념이 도입되어 직관적 코드 작성이 가능해졌습니다.     

이외로는 부분 형식, 무명 메서드, 널 허용 값, 공변성 개념이 생겨났으며,    
getter/setter 별도 엑세스, 메서드 그룹 변환, 정적 클래스, 대리자 유추 기능이 추가되었습니다. 

# C# 3.0
_C# 3.0_ 은 Visual Studio 2008과 함께 나타났으며, 이 때부터 하나의 프로그래밍 언어로써 자리잡게 되었습니다.     
자동 구현 속성, 무명 형식, 쿼리, 람다, 식 트리, 확장 메서드, 암시적 형식, 부분 메서드, 객체/컬렉션 이니셜라이저 개념이 생겼으며,    
이를 토대로 드디어 LINQ가 탄생했습니다.    

또한 컬렉션에 대한 SQL 스타일의 코드 작성도 이 시점부터 가능해졌습니다.
```cs
int[] arrays = { 1, 2, 3, 4};
Console.WriteLine(arrays.Average());
```

# C# 4.0
_C# 4.0_ 은 Visual Studio 2010과 함께 릴리스되었습니다.     
`임베디드 interop` 개념이 도입되어,  COM 어셈블리(다른 언어 호환을 위한 표준) 작성이 쉬워졌고,    
또한 제네릭 공변, 반공변 개념 또한 생겨서 제네릭 타입에 대해서도 확장되었습니다.    
명명된 인수 (파라미터를 넘길 때 '이름=' 식으로 명시적으로 적어주되, 순서를 반드시 지킬 필요가 없어짐),    
선택적 인수 (정의 단계에서 기본값을 넣어주어 선택적으로 파라미터 할당)도 사용 가능해졌습니다.   

`dynamic` 키워드도 이 때 도입되어, 동적 키워드도 가능해졌습니다.   

# C# 5.0
_C# 5.0_ 은 Visual Studio 2012와 함께 릴리스되었으며, 언어에 중점을 두었다고 합니다.     
호출자 정보 특성을 사용해서, 많은 양의 리플렉션 코드를 사용하지 않고 컨텍스트 정보 호출이 가능해졌습니다.
```cs
public static void Log([CallerLineNumber] int lineNumber)
{
  Console.WriteLine(lineNumber); 
}

Log() // 호출된 코드 상의 줄 번호 출력
```
또한, `async`와 `await` 키워드 또한 이 시점에 도입되었습니다.   
이로써 본격적인 비동기 프로그래밍이 가능해졌습니다.   

# C# 6.0
_C# 6.0_ 은 Visual Studio 2015와 함께 릴리즈되었습니다.     
이 버전에서는 생산성을 높이는 것을 목표로 해서 기능들이 추가되었습니다.     
using을 정적에 적용하는 것이 가능해졌고, 예외에 필터를 적용하는 것이 가능해졌습니다.    
속성 설정 시 초기값 설정 기능과, 식 기반으로 간결히 설정하는 것 또한 가능해졌습니다.    
문자열 보간과 `nameof` 연산자를 통해 문자열 관련 기능도 추가되었습니다.  
```cs
List<int> myList = new List<int>();
Console.WriteLine(nameof(myList)); // myList
Console.WriteLine(nameof(List<int>)); // List
```

정리하면, 6.0 버전에서는 코드의 보일러플레이트(반복되는 작업 표준)를 줄이고, 더 간결히 하도록하는 기능이 다수 추가되었다 볼 수 있습니다.

# C# 7.0
_C# 7.0_ 은 Visual Studio 2017과 함께 릴리즈되었습니다.      
`out` 키워드를 통해 선언과 동시에 초기화 할 수 있게 되었으며, 튜플 분해를 통해 여러 값에 대한 처리도 간편해졌습니다.   
패턴 매칭과 무시 항목 개념이 이 버전부터 시작되었으며,    
로컬 함수(함수 내에서의 함수)와 참조 변수/반환이 가능해졌습니다.   

또한, 이 버전부터 본격적으로 .NET Core과의 통합이 강화되었기 때문에, 제대로 .NET Core를 사용하기 위해서는 7.0 버전 이상이 필요합니다. 

6.0이 간결함, 가독성을 중점으로 발전했다면,    
7.0은 효율성, 유연성을 중점으로 발전했다고 볼 수 있습니다.    

# C# 7.1
C#은 7.1 버전으로 '포인트 릴리즈' (변경사항마다 릴리즈하는 것이 아닌 일종의 '버전' 개념)를 제공하기 시작했습니다.    
이 때부터 Main 메서드에도 `async` 적용이 가능해졌고, `default` 리터럴이 생겨 값 유추가 가능한 경우는 기본값을 할당되게도 할 수 있게 되었습니다.    
```cs
public static void MyMethod(int i = defualt)
{
  Console.WriteLine(i); // 0
}
```
유추된 튜플 요소의 이름이라 해서, 따로 명시적으로 요소 이름을 적어주지 않아도      
할당된 변수로 컴파일러가 요소를 만드는 기능도 생겼고,   
제네릭 매개변수에 대한 패턴 일치 기능도 생겼습니다.     
추가적으로, 이 버전부터 언어 버전 선택 구성요소가 추가되었습니다.    

# C# 7.2
_C# 7.2_ 부터는 다음과 같은 기능을 추가했습니다.   
- `stackalloc` 배열의 이니셜라이저
: 원래 배열은 Heap 영역에 할당되는데, 해당 키워드를 사용하면 Stack에 할당되게 할 수 있습니다.
- `fixed` 문
: 가비지 컬렉터의 메모리 효율적 관리를 위한 객체 이동을 막아, 포인터와 같은 경우 더 안정적인 사용이 가능해집니다.
- `ref` 지역 변수 재할당
- `in` 한정자
: 파라미터를 참조 값으로 전달 가능해지지만, 호출된 메서드에 의해 변경되지는 않게 할 수 있습니다.    
- `readonly struct` 형식 선언
: 구조체 변경이 불가능해지고, `in` 매개변수로 전달되어야 함을 나타냅니다.
- `ref readonly` 한정자
: 메서드가 참조 형태로 반환되지만, 수정은 불가능하게 할 수 있습니다.
- `ref struct` 구조체
: 참조 형식으로 된 구조체가 가능해지며, 스택에 할당되는 것을 강제합니다.     
(원래 구조체도 스택에 들어가는 게 원칙이긴 한데, 가끔 힙에 들어가는 경우가 있다고 합니다.)
- 비후행 명명된 인수    
: 해당 버전 이전에는, 위치 기반 인수를 한 번 사용하면 명명된 인자를 사용할 수 없었습니다.
하지만 이 시점부터 순서에 대한 부분만 유의한다면, 둘을 혼용하여 사용할 수 있게 되었습니다.
```cs
static public void MyMethod(int myInt1,int myInt2, string myString1, string myString2){}
```
```cs
MyMethod(myInt1: 1, 2, myString: "hello", "bye"); // 정상 실행
MyMethod(1, myString2: "bye", myString: "hello", myInt2: 2); // 정상 실행
MyMethod(1, myString2: "bye", myString: "hello", 2); // 에러
```
위치 기반 인수 판단이 꼬일 것 같은 방식은 사용하지 않는 편이 좋습니다.     
- 숫자 리터럴의 선행 밑줄
: 숫자 타입에 밑줄 `_`을 추가할 수 있게 되었습니다. 이는 지나치게 긴 숫자에 대해 가독성을 높입니다.
```cs
int longInt1 = 1000000; // 가독성 안 좋음
int longInt2 = 1_000_000;
```
- `private protected` 가능
- 조건부 `ref` 식 가능

# C# 7.3
_C# 7.3_ 에서는 크게 2가지 방향으로 발전했는데, _안전 코드의 성능 향상_ 과 _기존 기능 개선_ 입니다.    
## 안전 코드 성능 향상
안전한 코드란, CLR이 메모리 접근을 관리하게 되어, 메모리 관련 버그가 발생하지 않는 코드를 말합니다.    
때문에 안전하다는 장점이 있지만, 포인터 기능을 사용할 수 없고 비안전 코드에 비해 성능이 일부 떨어지는 단점이 있었습니다.     

이러한 단점을 해결하고자 여러 시도가 있었습니다.      
fixed된 필드에 고정 없이 접근이 가능해졌고, 더 다양한 타입에 fixed가 적용 가능해졌습니다.    
ref 로컬 변수에 재할당이 가능해졌고, stackalloc 배열에서 초기화가 가능해졌습니다.    

## 기존 기능 개선
기존에 있던 기능들도 일부 개선되었습니다.    

Equals로만 가능하던 튜플 비교 연산이 `==`,`!=`로 연산이 가능해졌습니다.   
`out` 변수에 대해 사용할 수 있는 표현이 확장되었습니다.      
기존까지는 자동 구현되는 속성에 대해서는 속성 추가가 불가능했지만,      
`[field:NonSeriaized]`를 활용해 자동 처리 과정에서 제외되게 해줘 수정이 가능해졌습니다.
`in`이나 `params`를 사용하는 메서드가 오버로딩될 때, 기준이 명확해졌습니다.     

# C# 8.0
_C# 8.0_ 에서 .NET Core C#을 대상으로 하는 본격적인 릴리즈가 처음으로 시작되었습니다.    

- 구조체에서 읽기 전용 멤버 선언
: 구조체에서 특정 멤버 메서드를 `readonly`로 설정할 수 있습니다.
해당 멤버는 읽기 관련 기능은 할 수 있지만, 쓰기 관련 기능은 수행할 수 없습니다.
```cs
struct X
{
  public int x;
  public readonly int GetX() => x; // 정상 실행
  public readonly void EditX(int i) x = i; // 에러
}
```
- 기본 인터페이스 메서드
: 인터페이스에서도 메서드를 작성할 수 있게 되었습니다.
이는 기존에 인터페이스 메서드 추가시 상속하는 모든 클래스에 대해서도 재정의가 필요했던 번거로움을 해결했습니다.

- 패턴 매칭 기능 개선
: Switch, 속성, 튜플, 위치 패턴에 대해 패턴 매칭 기능이 개선되었습니다.

- using 선언문
: using을 더 간결히 작성할 수 있게 되었습니다.
`{}` 없이 `IDisposable` 객체의 생명 주기 관리가 가능해졌습니다.
```cs
using var stream = new FileStream("text.txt", FileMode.Open);
```

- 정적 지역 함수
: 지역 함수를 `static`으로 선언할 수 있게 되었습니다.
정적으로 선언되어 있기 때문에, 오직 파라미터만을 사용하는 함수를 만들 수 있습니다.
```cs
public void MyMethod()
{
  int myInt = 1;

  static void staticMethod(int param)
  {
    Console.WriteLine(param); // 정상 실행
    Console.WriteLine(myInt); // 에러
  }
}
```

- Disposable Ref 구조체
: `struct`를 `IDisposable`로 만들면서도, `ref` 키워드로 힙에는 할당되지 않게 할 수 있습니다.

- 널 허용 참조 타입
: C# 8.0 이전에는, 일부 참조 타입에 한해서 널 자체는 허용이 되었지만, 런타임 중에만 감지가 가능해 개발자가 직접 코드에서 널 체크가 필요했습니다.       
이후부터는 널 허용 참조 타입을 선언함으로써 컴파일러가 이를 체크하며 _NullReferenceException_ 을 예방할 수 있게 되었습니다.
```cs
string? name = null;
Console.WriteLine(name.Length); //  경고: null 가능 참조에 대한 역참조입니다.
```

- 비동기 스트림
: `await foreach`를 사용해 비동기적으로 데이터를 스트리밍할 수 있게 되었습니다.

- 새로운 인덱스 연산자
: `^`와 `..`의 도입으로 배열을 좀 더 쉽고 간편하게 사용할 수 있습니다.
`^`는 역으로 세는 인덱스(1이 시작), `..`는 범위를 의미합니다.    
```cs
int[] arr = {1, 2, 3, 4, 5};

Console.WriteLine(arr[^1]); // 5
Console.WriteLine(arr[^2]); // 4

foreach(int a in arr[0..3]) Console.WriteLine(a);
// 1
// 2
// 3
```

- 널 병합 할당
: `??=` 를 사용하여 변수가 null일 때만 할당되는 식을 만들 수 있습니다.
```cs
string? nullString = null;
string notNullString = "not null";

nullString ??= "null?";
Console.WriteLine(nullString); // null?

notNullString ??= "null?";
Console.WriteLine(notNullString); // not null
```
- unmanaged 생성 타입
: 우선 `unmanaged`란, 가비지 컬렉터의 관리 대상이 아닌 타입을 의미합니다.
기본 자료형, 구조체, 포인터 등이 이에 속합니다.
또한 `constructed type`. 생성 타입이란, 제네릭 타입이 특정 타입으로 채워졌을 때의 타입을 의미합니다.   

따라서 `unmanaged constructed type`은 제네릭 타입이 구체화될 때, unmanaged로 제약이 적용되는 개념을 의미합니다.
```cs
struct Container<T> where T : unmanaged  // T는 반드시 unmanaged 타입이어야 함
{
    public T Value;
}
```

- 중첩 식의 `stackalloc` 도입
: 기존까지 `stackalloc`은 해당 변수를 스택에 저장되도록 하는 명령어인데,      
단독 식 형태로 사용되었어야 했고 중첩된 형식으로 사용될 수 없었지만, 해당 버전부터는 사용 가능하게 되었습니다.
```cs
Span<int> numbers = stackalloc int[2]; // C# 8.0 이전
Span<int> GetNumbers() => stackalloc int[3] { 1, 2, 3 }; // C# 8.0 이후 가능
```
`Span`은 배열을 가리키는 뷰의 개념이라고 생각하면 되며, 스택에서 사용하기 위한 배열 개념이라 생각하면 됩니다. 

- 문자열 보간 개선
: 변수를 문자열에 포함할 수 있는 기능인 `$` 기호와, 이스케이프 문자(\)를 반복해서 적을 필요가 없게 해주는 `@` 기호를 같이 사용하려고 할 때,
C# 8.0 버전 이전이라면 반드시 `$@` 순으로 위치했어야 합니다. 하지만 이러한 점이 해당 버전부터 개선되어 순서가 상관 없게 되었습니다.
```cs
string username = "User";
string path1 = @$"C:\Users\{username}";
string path2 = $@"C:\Users\{username}"; // 어느 순서로 적어도 무방함
```

**위의 기능들은 .NET Core 3.0에 추가된 새로운 CLR과 라이브러리를 필요로 하기 때문에,      
상기한 기능들을 온전히 사용하려면 .NET Core 3.0 이상이 필요합니다.**

# C# 9
_C# 9_ 는 .NET 5와 함께 릴리스되었으며, 다음과 같은 기능이 추가 되었습니다.    

- 레코드
: 기존 클래스에서 _불변_ 과 _값 기반 비교_ 의 개념이 추가된  `record` 타입이 도입되었습니다.

- init
: 속성에서 `get`과 `set`에 이어 `init`을 지정할 수 있게 되었습니다.
`set`과 유사한 역할을 하지만, 객체 생성 이후에는 값을 변경할 수 없다는 차이가 있습니다.
```cs
 public class SetClass
 {
     public string Name { get; set; }
 }
 public class InitClass
 {
     public string Name { get; init; }
 }
```
위와 같이 값 설정을 위해 set과 init을 사용하는 클래스가 있다고 가정합시다.
```cs
 var setClass = new SetClass { Name = "setClass"};
 var initClass = new InitClass() { Name = "initClass"};

 setClass.Name = "new Name"; // 정상 실행
 initClass.Name = "new Name"; // 에러
```
init은 객체 생성 시점에만 값을 변경할 수 있습니다.   

- 최상위 문       
: 기본적으로 C#에서는 `static void Main(string args[])`와 같은 형태의, 일종의 프로그램의 시작점 역할을 하는 함수가 필요 했습니다.
하지만 C# 9 이후부터는 _최상위 문_ 개념이 생겨 자체적으로 지원을 하게 되었습니다. Python 코드의 형태와 유사해졌다고 생각하면 됩니다.
물론, 이전 스타일의 코드 작성도 여전히 가능합니다.
```cs
using System;

class Program
{
    static void Main()
    {
        Console.WriteLine("Hello, World!");
    }
}
```
```cs
using System;

Console.WriteLine("Hello, World!");
```
위의 두 코드는 같은 동작을 합니다.     

- 패턴 매칭
: C# 9 버전부터 패턴 매칭에 대한 기능이 확장되었습니다.

- 타입 패턴이 추가되어, 특정 객체가 해당 타입인지 확인할 수 있습니다.
```cs
int myInt = 1;

if(myInt is int)
{
  Console.WriteLine("int"); // int
}

if(myInt is int newInt)
{
  Console.WriteLine(newInt); // 1
}
```
위와 같이 `is` 키워드와 조합하여 간단하게 타입 검사 식을 만들 수 있습니다.  

- 자연어에 가까운 논리 연산자를 사용할 수 있게 되었습니다.  
```cs
int myInt = 5;

if(myInt is not 10)
{
  Console.WriteLine("not 10"); // not 10
}

if(myInt is > 0 and < 10)
{
  Console.WriteLine("between 0 ~ 10"); // between 0 ~ 10
}

if(myInt is 5 or 10)
{
  Console.WriteLine("5 or 10"); // 5 or 10
}
```
보다 자연어에 가까운 방식으로 식을 설정하여, 가독성을 높일 수 있습니다.   

- 모듈 이니셜라이저 개념을 이용해, 어셈블리 로드 시기에 메서드를 호출할 수 있습니다.
```cs
[ModuleInitializer]
public static void Init()
{
  Console.WriteLine("Init");
}

public static void Main(string[] args)
{
  Console.WriteLine("Main");
}

// Init
// Main
```
다음과 같이 따로 실행하지 않아도 자동으로 처음으로 실행되는 메서드를 설정할 수 있습니다.      
다만, 이러한 특성 때문에 해당 메서드에는 _반환값 없음, 파라미터 없음, void, static_ 이어야 한다는 제한 사항이 있습니다.    

- 한 클래스를 여러 부분에서 정의할 수 있게 해주는 `partial class`에 이어, `partial method` 개념도 사용할 수 있게 되었습니다.    
```cs
partial class MyPartialClass
{
  partial void myPartialMethod(); // 선언만
}
partial class MyPartialClass
{
  partial void myPartialMethod() // 구현까지
  {
    Console.WriteLine("Hello"); 
  }
}
```
다음과 같이, 메서드도 여러 부분에서 정의할 수 있게 되었습니다.    
다만 이러한 방법을 사용할 때 주의해야 할 사항이 있습니다. (편의를 위해 선언을 한 위의 partial 클래스를 u, 구현을 한 아래의 partial 클래스를 d라고 가정하겠습니다.)     
|경우|결과|
|-|-|
|u와 d 모두 있는 경우|정상 동작|
|u만 있는 경우|정상 동작|
|d만 있는 경우|컴파일 오류 (partial method는 선언이 반드시 필요)|
|u와 d 모두 없는 경우|메서드 자체가 없음| 

* 타겟 형식의 `new` 표현식을 사용할 수 있게 되었습니다.   
객체를 생성할 때, 왼쪽 변수를 기반으로 하여 타입을 명시하지 않고 사용할 수 있게 되었습니다.
```cs
List<int> list = new();
```
- 익명 함수를 정적으로 선언할 수 있게 되었습니다.
일반적인 익명 함수는 외부의 변수를 사용하기 위해 _클로저_ 라고 하는 별도의 클래스를 내부적으로 생성하는데, 이는 성능 저하를 일으킬 수 있습니다.
하지만 정적 익명 함수를 사용하게 되면, 외부 변수에 접근할 수 없어 이러한 과정을 우려할 필요가 없습니다.
```cs
int x = 3;

Func<int,int> func = num => num * x;
Func<int,int> s_func = static (num) => num * x; // 에러
```
`static` 키워드를 붙인 익명 함수는 외부 변수를 사용할 수 없습니다.   

- 타겟 형식을 조건부 식에도 사용할 수 있습니다.
타겟 형식이 확장됨에 따라, 조건부 식에도 더 다양한 방법으로 사용할 수 있게 되었습니다.
```cs
public class Shape {}
public class Circle : Shape {}
public class Triangle : Shape {}
public class Rectangle : Shape {}
```
위와 같이 클래스가 적용이 되었다고 가정하면,
```cs
int dot = 3;

Shape shape = dot < 4 ? new Triangle() : new Rectangle(); // 캐스팅 개념
Circle circle = dot == 0 ? new() : null // 타겟 형식 new와 null 개념
```
여러 가지 방법으로 조건부 식을 사용할 수 있습니다.      

- 익명 식에서도 무시 항목을 사용할 수 있습니다.
```cs
Func<int, int, int> func = (x, _) => 0;
```

- GetEnumerator 에 대해 foreach를 사용할 수 있습니다.    
```cs
public static class MyClass
{
  public static IEnumberator<T> GetEnumerator<T>(this IEnumerator<T> enumerator) => enumerator;
}
```
```cs
IEnumerator<string> enumerator = (IEnumerator<string>)new List<string> { "A", "B", "C" };
foreach(string item in enumerator)
{
  Console.WriteLine(item);
}
```
- 로컬 함수에도 특성을 적용할 수 있습니다.

# C# 10
_C# 10_ 은 코드 간결화, 성능 향상, 유지보수성을 목표로 개선되었습니다.    

- 레코드 구조체      
: 이전까지 `class`에만 적용 가능하던 `record`의 개념을 구조체에도 적용 가능해졌습니다.
덕분에 구조체에 대해서도, 불변성/값 기반 비교를 보장할 방법이 생겼습니다.
```cs
public record struct RecordStruct(int x);
```

- 구조체 생성자 개선     
: 모든 필드에 대한 초기화가 이전까지는 필요했지만, 해당 버전부터는 매개변수 없는 생성자를 사용할 수 있게 되었습니다.
```cs
public struct Struct
{
  public int myInt;
  public Struct(){}
}
```

- 보간 문자열 핸들러    
: 문자열 보간 자체는 C# 6 버전부터 지원되었었지만, 내부적으로 `string.Format()`을 호출하는 형태였기 때문에 성능 문제를 일으킬 수 있었습니다.
하지만 C# 10 버전 이후부터는 내부적으로 적절한 핸들러를 호출하는 형태로 개선되었습니다.

- global using    
: `global using` 키워드를 사용하면, 해당 프로젝트 내에서는 반복적으로 네임스페이스를 적용할 필요가 없습니다.

- 파일 범위 네임스페이스    
: 기존까지의 `{}` 형태의 네임스페이스 선언에서 벗어나, 한 줄로 선언할 수 있게 되었습니다.
```cs
namespace MyNamespace // C# 10 이전
{
    class MyClass { }
}
```
```cs
namespace MyNamespace; // C# 10 이후

class MyClass { }
```

- 확장 속성 패턴     
: 만약 속성 패턴이 중첩되어 있는 경우, 이전까지는 `:{}`와 같은 형태를 반복적으로 적어주어야 했습니다.        
이는 개발자의 생산성과 사용자의 가독성을 떨어뜨렸습니다.
```cs
public class Human
{
  public string Name { get; set; }
  public Address Address { get; set; }
}

public class Address
{
  public string Country { get; set; }
  public string City { get; set; }
}
```
`Human` 클래스는 `Address`의 인스턴스를 속성 형태로 하여 가집니다. 

```cs
var h = new Human() { Name = "Kim", Address = new Address() };

if(h is { Address : { Country : "Korea" }}) {} // C# 10 이전
if(h is { Address.Country : "Korea" }) {} // C# 10 이후
```

- 람다 개선 사항     
1. 람다 식 자연 타입 유추 가능      
: 자연 타입은 컴파일러가 변수의 타입을 더 효과적으로 추론하는 것을 의미합니다.     
이전까지는 가변 타입을 선언하고 람다 식을 사용하면 오류를 일으켰지만, 이러한 부분이 해결되었습니다.  
```cs
Action<string> action = (string s) => Console.WriteLine(s);
var lambda = (string s) => Console.WriteLine(s);
```
해당 문제가 해결되기 전에는 `Func`, `Action`와 같은 방식을 사용해 명시적인 선언이 필요했습니다.  

2. 람다 식 반환 타입 명시 가능      
: 기존 람다 식은 반환 타입을 명시할 수 없었기 때문에, 반환 타입이 모호하거나 혼합된 경우 컴파일러가 추론을 못하는 경우가 있었습니다.      
하지만 해당 버전부터는 `반환 타입 (파라미터) => 표현식` 형태로 적어 반환 타입 명시가 가능해졌습니다.
```cs
var intOrString1 = (bool flag) => flag ? 10 : "ten"; // 에러
var intOrString2 = object (bool flag) => flag? 10 : "ten" // 정상 실행 
```

3. 람다 식 특성 적용 가능      
: 람다 식에도 특성이 적용 가능해졌습니다.

- 상수 문자열 보간 지원        
: `const`로 선언하는 상수 문자열에 대해서도 보간이 가능해졌습니다.
단, 해당 문자열에 들어가는 문자열 또한 상수 문자열이어야 한다는 제한이 있습니다.
(상수 문자열은 컴파일 시간 중 정해지기 때문에)
```cs
const string w = "World";
string e = "!";

string hwe = $"Hello {w} {e}"; // 정상 실행
const string chw = $"Hello {w}"; // 정상 실행
const string chwe = $"Hello {w} {e}"; // 에러
```

- 레코드의 ToString에 sealed 적용 가능
: `record`의 ToString() 메서드에 `sealed`를 적용할 수 있게 되었습니다.
때문에 ToString() 메서드가 더 이상 오버라이드되지 않음을 확신할 수 있습니다.
```cs
public record MyRecord
{
  public sealed overrride string ToString() => "MyRecord";
}
```
이로써 객체가 어떻게 출력될지도 확신할 수 있기 때문에, 객체를 이용한 로그와 같은 형식에서 큰 확실성을 가집니다.      

- #line 형식 지원     
: `#line` 지시문은 컴파일러에게 라인의 소스 코드를 지정하는 역할을 합니다.
`#line 라인번호 "파일이름"`과 같은 형식으로 작성합니다.   
```cs
#line 100 "MyCode.cs"
```
특정 소스 파일의 특정 라인에서 실행되는 것처럼 속이는 효과를 내기 때문에,      
주로 코드 생성 도구나 디버깅 시나리오에서 사용합니다.     

- CallerArgumentExpression 특성
: `CallerArgumentExpression` 특성은 메서드나 파라미터로 전달된 _표현식_ 을 문자열로 가져오는 역할을 합니다.
적용하고자 하는 파라미터에는 기본값을 필수적으로 적어주어야 합니다.   
```cs
public static void MyMethod(bool myBool, [CallerArgumentExpression("myBool")] string myString = null)
{
  Console.WriteLine(myString);
}
```
```cs
MyMethod(2 == 3); // 2 == 3
```
