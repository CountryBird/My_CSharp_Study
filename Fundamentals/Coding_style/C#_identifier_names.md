`identifier`, `식별자`는 타입, 멤버, 변수, 네임스페이스에 개발자가 부여하는 이름입니다.    
언어에서 사용하는 키워드가 아니면 큰 제한은 없지만, 일종의 _관례_ 가 있습니다.     

# 명명 규칙
- 식별자는 문자 또는 밑줄로 시작해야 합니다.
- `@`를 사용하여 C# 키워드에 속하는 식별자도 선언할 수 있습니다.
```cs
int @if = 1;
Console.WriteLine(@if) ;
```
이러한 방법은 주로 다른 언어와의 호환성을 위해 사용됩니다.  

# 명명 관습
컴파일러가 따로 경고하지는 않지만, 이름의 일관성을 위해 암묵적으로 사용되는 관습이 있습니다.    
## - 인터페이스의 이름은 `I`로 시작합니다.
```cs
public interface IMyInterface { }
```

## - 특성 타입은 `Attribute`로 끝납니다.
: 특성은 메타데이터에 원하는 기록을 남기는 용도로 사용되는 개념입니다.  

`System.Attribute`를 상속받아 사용하며, `[]`로 사용합니다.
```cs
public class MyAttribute : Attribute
{

}
```
```cs
[MyAttribute]
```

## - 열거형은 플래그가 아닌 경우 단수, 플래그인 경우 복수 명사를 사용합니다.
: 플래그란 `[Flags]`로 나타내는 속성이며, 해당 속성이 있으면 열거형을 비트 연산 개념을 도입하여 사용할 수 있습니다.
```cs
 public enum filePermission
  {
      Read,
      Write,
      Execute
  }

  [Flags]
  public enum filePermissions
  {
      Read,
      Write,
      Execute
  }
```

## - 식별자에는 연속된 밑줄이 사용되면 안 됩니다.
: 연속된 밑줄은 컴파일러가 내부적으로 사용하는 생성자에 예약되어 있습니다.

## - 의미 있고 설명적인 이름이 좋습니다.
```cs
int a = 1; // (X)
int count = 1; // (O)
```

## - 간결성보다는 명확성을 우선시 하는 것이 바람직합니다.
: 가능하면 간결한 이름을 사용하는 것이 좋지만, 지나치게 축약되거나 모호한 표현은 사용하지 않는 것이 좋습니다.
```cs
string aFN = "text.txt"; // (X)
string accessFileName = "text.txt"; // (O)
```

## - PascalCase & camelCase
: PascalCase와 camelCase는 대소문자와 관련된 명명법입니다.  
단어 자체의 대소문자를 유심히 보면 규칙을 일부 추측할 수 있습니다. 자세한 건 후술하겠습니다.

- PascalCase: 클래스, 메서드 이름 / 상수 이름
- camelCase: 메서드 매개 변수 및 지역 변수 이름 / private 객체 필드(+밑줄 시작)

## - 정적 필드값은 `s_`로 시작합니다.
```cs
static int s_totalCount;
```

## - 약어, 두문자어는 널리 알려진 것이 아니면 사용하면 안 됩니다.
```cs
string mIC = "my"; // (X)
string myIDCode = "my"; // (O)
```

## - 네임스페이스는 역도메인 형식을 권장합니다.
: 역도메인 형식이란, 이름 그대로 도메인을 뒤집은 형태로 사용하는 것을 말합니다.
전세계적 충돌 방지와 조직/관리 용이성 때문에 이를 사용합니다.  

## - 어셈블리 이름은 이의 주 기능을 나타낼 수 있어야 합니다.
: `Company.Product.DataAccess.dll`와 같이 기능을 추측할 수 있는 이름이 좋습니다. 

## - 단일 문자 변수를 사용하면 안 됩니다.
: `for`문과 같은 루프에서 사용하는 임시적인 카운터 역할의 변수와 같은 경우가 아니라면, 변수의 이름은 단일 문자로 이루어지면 안 됩니다.  


# 파스칼 케이스
첫 단어의 첫 글자를 대문자로 표기하는 규칙입니다.   
주로 `class`, `interface`, `struct`, `delegate` 타입에 이름을 지정할 때 사용합니다.
```cs
public class MyClass { }
public struct MyStruct { }
```
`interface`의 이름을 지정할 때는, 이름 제일 앞에 `I`를 붙이고 파스칼 케이스를 사용합니다.  
```cs
public interface IMyInterface { }
```
`public`으로 지정된 필드, 속성, 이벤트와 같은 멤버에도 파스칼 케이스를 사용합니다.
```cs
public class MyClass 
{
    public int MyInt;
    public void MyMethod()
    {

    }
}
```
위치와 관련된 `record`에는 public 속성을 파스칼 케이스를 사용합니다.
```cs
public record Address
(
    string Street,
    string City,
    string Province
);
```

# 카멜 케이스
파스칼 케이스와 비슷하지만, 첫 단어의 첫 문자는 소문자로 표기하는 규칙입니다.    
주로 `private`,`internal` 필드의 이름을 사용하고, `_`를 접두사로 붙입니다.  
```cs
public class MyClass
{
    private int _myInt;
}
```
`private`, `internal`에 `static`을 적용할 때는 `s_`를 접두사로 사용하고,   
`[ThreadStatic]`을 사용할 때는 `t_`를 사용합니다.   
(이 특성을 적용하면, 스레드마다 해당 변수에 대한 독립적인 필드를 가집니다. => 스레드에 따라 같은 변수도 다르게 판단할 수 있다는 의미)
```cs
public class MyClass
{
    private static int s_myInt;

    [ThreadStatic]
    private static int t_myInt;
}
```
매개변수를 작성할 때도 카멜 케이스를 적용합니다.
```cs
 public void MyMethod(int myInt, string myString)
 {

 }
```

# 타입 매개변수 명명 지침
타입 매개변수는 제네릭 클래스나 제네릭 메서드에서 사용되는 자리 표시자를 의미합니다.  
이러한 타입 매개변수를 사용할 때도 몇 가지 규칙이 있습니다.

## - 제네릭 타입 매개변수를 설명적인 이름으로 지을 것
: 단일 문자 `T`로 사용해도 충분히 설명이 되는 경우가 아니면, 타입 매개변수의 이름을 설명적으로 적는 것이 좋습니다.
```cs
 public class Converter<T1,T2> { }; // (X)
 public class Converter<TInput,TOutput> { }; // (O)
```

## - 타입 매개변수가 하나인 경우에는, `T`를 고려해 볼 것
: 만약 타입 매개변수가 하나만 있는 경우에는, 관례적으로 `T`로 사용합니다.
```cs
static public void Speaker<T>(T t)
{
    Console.WriteLine(t);
}
```

## - T를 타입 파라미터 이름의 접두사로 사용할 것
: 일반적인 클래스의 사용 타입과 구분을 쉽게하기 위해서, 타입 파라미터 이름은 시작할 때 `T`로 사용하면 좋습니다. 

## - 타입 파라미터 이름을 제약 조건과 관련 있도록 고려해 볼 것
: 제네릭 타입 매개변수는 `where`을 사용해 조건을 걸 수 있는데, _특정 클래스나 인터페이스를 구현하는 타입_ 만 받도록 제한할 수도 있습니다.   
이러한 경우, 타입 파라미터의 이름을 제약 조건과 관련이 있는 방향으로 정할 수 있습니다.
```cs
 public class User
 {
     int name;
 }
```
위와 같이 _사용자_ 를 의미하는 클래스가 있다고 가정합시다.
```cs
public class UserRepositoryl<T> where T : User
{
    // (X)
}
public class UserRepository<TUser> where TUser: User
{
    // (O)
}
```
단순히 `T`로만 정하는 것 보다는, `TUser`와 같이 관계 있는 형태로 명명해주면 코드만 보아도 직관적으로 어떤 타입을 다루는지 알아보기가 쉽습니다.
