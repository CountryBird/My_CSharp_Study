# 언어 지침
다음 언어 지침들은 최신 C# 버전과 모던한 프로그래밍 스타일을 사용하여 코드의 가독성과 유지보수성을 높이는 것을 목표로 합니다.   

- 새로운 C# 버전이 나오면 최신 기능을 사용해 볼 것   
: `record`, `pattern matching` 같은 최신 문법을 적극 활용할 것을 권장합니다.

- 예외는 구체적으로 정할 것     
: 단순히 `Exception`으로 예외를 설정하기 보다, 상황에 맞게 명확하고 구체적으로 정하는 것이 좋습니다.
또한, 이를 이용하여 오류 메시지도 의미있게 설정해야 합니다.

- 컬렉션을 조작할 때는 LINQ 문법을 사용하는 것을 권장    
: 리스트나 배열과 같은 컬렉션을 조작할 때는 LINQ 문법을 사용하는 것이 C#을 제대로 활용하는 방법입니다.       
LINQ에 대해서는 추후에 다룰 예정입니다.

- I/O 작업에는 `async`와 `await`를 사용     
: I/O 작업을 비동기ㅈ거으로 수행하면 UI가 멈추는 것을 방지할 수 있습니다.

- 교착 상태를 주의하고, 필요한 경우 `ConfigureAwait` 사용      
: 기본적으로 await는 현재 컨텍스트를 유지하려 합니다. (이를 출장 후 회사 복귀에 비유하자면,)      
하지만 `ConfigureAwait(false)` 설정하면, 컨텍스트 결합을 유지하려 하지 않습니다. (출장 후 회사에 복귀할 필요가 없어지는 것)

- 런타임 타입보다는 언어 타입 사용
: System.String보다는 string, System.Int32보다는 int를 사용하는 것이 좋습니다.

- 부호 없는 타입보다는 int 사용
: 대부분의 라이브러리에서 정수 연산은 `int`를 기준으로 설계되어 있기 때문에,
특별한 경우가 아니라면 `unsigned`(양수만 표현)을 사용하지 않는 것이 좋습니다.

- var는 사용자가 형식을 유추할 수 있는 경우에만 사용     
: `var`를 너무 많이 사용하면 가독성을 해치기 때문에, 타입이 명확한 경우에만 사용해야 합니다.

- 명확성과 단순성을 염두하고 코드 작성

- 지나치게 복잡한 코드 논리 회피

# 문자열 데이터
- ""앞에 `$`를 사용하고 `{}`를 통해 변수를 묶으면, 변수만으로 해당 값을 보일 수 있습니다.     
이는 긴 값을 변수 하나로 대체할 수 있기 때문에, 가독성과 효율성을 높입니다.
```cs
string h = "hello,";
string w = "world!";

Console.WriteLine($"{h}{w}"); // hello, world!
```

- 많은 양의 텍스트를 사용할 때 문자열을 루프에 추가하려면 `StringBuilder`를 사용합니다.    
: 기본적으로 `string`은 _불변 객체_ 이기 때문에, 수정에 원 객체를 버리고 새로 가져오는 과정을 거칩니다.     
이러한 과정은 반복되는 string 추가 작업에서 효율을 떨어뜨립니다. 이러한 비효율을 해결하고자 `StringBuilder`를 사용합니다.
```cs
var alphabet = "abcdefghijklmnopqrstuvwxyz";
var stringBuilder = new StringBuilder();

for (int i = 0; i < 10; i++)
{
    stringBuilder.Append(alphabet);
    stringBuilder.Append("\n");
}
Console.WriteLine(stringBuilder);
```

- `"""`를 사용하는 것이 `\`(escape 방식)이나 `@`(Verbatim 방식)보다 좋습니다.
: 기존 escape 방식은 이스케이프 문자를 사용해서 특수 문자들을 문자 그대로 표현할 수 있게하는 방식이고, (ex) "C:\\Users\\")     
Verbatim 방식은 문자열 앞에 `@`를 붙여 문자 그대로 표현할 수 있게하는 방식입니다.    
escape 방식은 매 문자마다 \를 적어주어야 한다는 번거로움이 있고, Verbatim은 이보다는 좋지만 큰따옴표에 대한 부분은 여전히 문제가 있습니다.      
: 이를 위해 C# 11부터는 raw string literals라는 개념을 도입했는데, 2개의 """ 사이에 원 문자열을 그대로 적는 방법입니다.
이 방법을 사용하여, 멀티라인, 특수문자를 신경 쓰지 않고 문자열을 사용할 수 있게 되었습니다.
```cs
var message = """    
This is a long message that spans across multiple lines.
It uses raw string literals. This means we can 
also include characters like \n and \t without escaping them.
""";
```

- _위치 문자열 보간_  대신 _식 기반 문자열 보간_ 을 사용하는 것이 좋습니다.
: 위치 문자열 보간 방식은 값 삽입 순서에 따른 오류 발생, 가독성 등의 이유로 식 기반 문자열 보간 방식보다 좋지 않습니다.
```cs
string h = "Hello, ";
string w = "World!";

Console.WriteLine("{0}{1}", h, w); // 위치 문자열 보간
Console.WriteLine($"{h}{w}"); // 식 기반 문자열 보
```

# 생성자 및 초기화
- 레코드 형식에서 기본 생성자 매개 변수에는 파스칼 케이스를, 클래스/구조체 형식의 기본 생성자 매개 뱐수에는 카멜 케이스를 사용합니다.
```cs
public record MyRecord(int MyInt, string MyString);
public class MyClass(int myInt, string myString);
```

- `required`가 적용된 속성을 사용해서, 생성자의 역할을 대신할 수 있습니다.
```cs
 public class MyClass
 {
     public required int myInt { get; set; } 
     public required string myString { get; set; }
 }
 private static void Main(string[] args)
 {
     MyClass myClass = new MyClass { myInt = 1, myString= "string"};
 }
```

# 배열 및 컬렉션
- 가능하면 칼렉션 초기화에 컬렉션 표현식을 사용하는 것이 간결하고 가독성이 좋습니다.
일일이 하나하나 할당하거나 Add할 필요가 없어집니다.
```cs
int[] array = { 1, 2, 3, 4 };
List<int> list = new List<int> { 1, 2, 3, 4 };
Dictionary<string, int> dictionary = new Dictionary<string, int>
{
    {"One",1 },
    {"Two",2 }
};
```

# 대리자
대리자는 메서드를 변수처럼 할당하여 사용하는 방식입니다. 

메서드에서 변수와 같이 _할당 개념_ 이 가능해지기 때문에,   
파라미터로 넘기고, 특정 상황에 맞춰 바꿔 끼울 수 있다는 이점이 생깁니다.

대리자 개념을 사용하는 것은 크게 2가지 방향이 있는데, `delegate` 키워드 / `Func<>, Action<>` 입니다.

## delegate
`delegate` 키워드를 사용해, 특정 함수에 대한 대리자를 만들 수 있습니다.     
대리자 개념에서, _클래스를 선언하는 느낌_ 과 비슷합니다. 
```cs
public delegate void MyDelegate();
public static void MyMethod()
{
    Console.WriteLine("MyMethod");
}
```
```cs
MyDelegate myDelegate1 = MyMethod;
myDelegate1(); // MyMethod

MyDelegate myDelegate2 = new MyDelegate(MyMethod);
myDelegate2(); // MyMethod
```

## Func<> / Action<>
Func<>는 반환값이 있는(void 이외 메서드) 대리자를, Action<>은 반환값이 없는(void 메서드) 대리자를 의미합니다.    
<> 안에는 파라미터의 타입이 순차적으로 들어가며, Func의 경우 마지막은 반환 타입을 의미합니다.    
대리자 개념에서,  이미 정의되어 있는 클래스에 _실제 인스턴스를 할당하는 느낌_ 과 비슷합니다.  
```cs
 public static void MyVoidMethod()
 {
 }
 public static void MyVoidMethodWithParameter(string s)
 {

 }
 public static int MyIntMethod()
 {
     return 1;
 }
 public static int MyIntMethodWithParameter(string s)
 {
     return 2;
 }
```
```cs
 Action myAction1 = MyVoidMethod;
 Action<string> myAction2 = MyVoidMethodWithParameter;
 Func<int> myFunc1 = MyIntMethod;
 Func<string,int> myFinc2 = MyIntMethodWithParameter;
```
Action의 경우, 파라미터와 반환값이 둘 다 없는 메서드를 대리할 수도 있습니다.   
이러한 경우 <>는 생략됩니다. 

# 예외 처리
대부분의 경우 예외 처리에서는 try-catch 문을 사용합니다.    
하지만 예외적으로, `finally` 코드에서 `Dispose` 메서드만 사용하는 경우 `using`을 사용하여 이를 대체할 수 있습니다.   
(`Dispose` 메서드는 자원 해제를 하는 메서드입니다. 파일, 데이터 베이스 연결, 네트워크 리소스 등 여러 분야에서 사용합니다.)    

`using`은 코드 블록이 끝나면 자동으로 `Dispose()`를 호출합니다. 이는 예외가 발생해도 해당되기 때문에 finally를 통한 Dispose를 대체할 수 있습니다. 

# && 및 || 연산자
C#에서 `&&`, `||`에 대해 기본적으로 **단축 평가**를 적용합니다.   
단축 평가란, 논리 연산자에서 조건을 평가할 때 필요 없는 조건은 평가하지 않는 방법입니다.    
AND(&&)의 경우 앞선 조건이 false인 경우, 전체 조건이 false가 되기 때문에 이 후 조건을 평가하지 않고,    
OR(||)의 경우 앞선 조건이 true인 경우, 전체 조건이 true가 되기 때문에 이 후 조건을 평가하지 않는 식입니다.

하지만 `&`, `|`을 사용하면, 단축 평가를 하지 않기 때문에, 조건에 대해 특수한 처리가 필요할 때 사용할 수 있습니다.
```cs
int dividend = 10;
int divisor = 0;

if(dividend > 0 || dividend/divisor > 0) { } // 정상 실행

if (dividend > 0 | dividend / divisor > 0) { } // 에러
```
2개의 if문은 같은 조건을 사용하고, `||`와 `|` 차이만이 있습니다.  
단축 평가에 따라 `DivideByZeroException`을 일으키냐 아니냐를 확인할 수 있습니다.   

# new 연산자
C# 9에서 추가된 타입 추론 기능을 사용하여, 간결화된 형식의 초기화가 가능합니다. 
```cs
MyClass myClass1 = new MyClass();
MyClass myClass2 = new();
```
추론이라는 개념 특성상, 인터페이스나 업캐스팅과 같이는 사용할 수 없습니다.

객체 이니셜라이저를 사용해 객체 만들기를 더 간소화 할 수 있습니다.
```cs
 var myClass3 = new MyClass() { MyInt = 1, MyString = "hello" };

 var myClass4 = new MyClass();
 myClass4.MyInt = 1;
 myClass4.MyString = "hello";
```

# 이벤트 처리
이벤트 처리를 정의하려는 경우, 후에 제거를 못해도 무방한 메서드라면 람다 식을 사용하는 것을 고려할 수 있습니다.  

# LINQ 쿼리
LINQ는 C#에서 데이터 쿼리를 SQL 객체 지향적으로 작성할 수 있도록 도와주는 기능입니다.   

- 쿼리 변수에는 의미 있는 이름을 사용합니다.
```cs
public class Student
{
    public int Age;
    public string Name;
}
```
```cs
 Student[] students = 
    { new Student { Age = 19, Name = "A" }, new Student { Age = 26, Name = "B" }, new Student { Age = 21, Name = "C" };

var studentOver20 = from student in students
                    where student.Age > 20
                    select student; 
```
위와 같이 쿼리를 통한 변수를 작성할 때는 조건 등을 쉽게 알아볼 수 있는 이름으로 하는 것이 좋습니다.

- 별칭을 사용하여 익명 형식의 속성 이름의 대/소문자를 올바르게 표시해야 합니다.

- 결과 속성의 이름이 모호하면 속성 이름을 바꿉니다.
```cs
 public class Purchase
 {
     public string BuyerName;
     public string ProductName;
 }
```
쿼리의 대상이 되는 데이터에 모호한 이름이 있는 경우, 이를 명확히 해야 혼동을 피할 수 있습니다.  

- 쿼리 변수의 선언에서 암시적 형식 `var`를 주로 사용합니다.

- 위의 예제처럼 주로 `from`으로 시작하여 쿼리 절을 정렬합니다.

- `where` 절을 다른 쿼리 절 앞에 사용하여 뒤의 쿼리 절이 더 좁아진 데이터 집합에서 작동하게 합니다.

- 컬렉션 내부의 컬렉션에 접근하고자 할 때, join 절 대신 여러 개의 `from` 절을 사용하는 방법도 있습니다.
```cs
var scoreQuery = from student in students
                 from score in student.Scores
                 where score > 90
                 select new { Last = student.LastName, score };
```

# 암시적 형식 지역 변수
