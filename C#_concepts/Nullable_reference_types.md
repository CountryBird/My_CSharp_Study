- _Nullable_ 참조 타입은 코드로 인해 런타임이 예외를 throw 할 가능성을 최소화하는 기능 그룹입니다.    
nullable에 대해 표시해 예외를 방지하는 3가지 기능들이 있습니다.
   - 변수를 역참조하기 전에 변수가 `null`일 수 있는지 여부를 확인하는 정적 흐름 분석 기능
   - 흐름 분석에서 null-state를 확인하도록 API에 주석을 다는 특성
   - 개발자가 변수에 대해 의도하는 null-state를 명시적으로 선언하는 데 사용하는 변수 주석

- 컴파일러는 컴파일 시간에 코드의 null-state를 추적합니다.    
null-state는 두 값 중 하나가 있습니다.
    - not-null: null 값이 아닙니다.
    - maybe-null: null 값이 들어올 가능성이 있습니다.

- 변수에 null 허용 여부를 결정할 수 있습니다.
   - null 허용하지 않음: `null` 값이나 `maybe-null` 값이 할당되면 컴파일러가 경고를 표시합니다. (기본 상태는 not-null)
   - null 허용: `null` 값이나 `maybe-null` 값이 할당될 수 있습니다. (기본 상태는 maybe-null)

- 역참조를 하는 예로, `.` 연산자를 사용해 해당 멤버 중 하나에 엑세스하는 것이 있습니다.
```cs
string message = "Hello, World!";
int length = message.Length; // message 변수 역참조
```
값이 `null`인 변수를 역참조하면 런타임에서 `System.NullReferenceException`을 throw합니다.   

# Null 상태 분석
_Null 상태 분석_ 이란, null-state를 추적하는 것을 의미합니다.      
`not-null` 또는 `maybe-null`을 가질 수 있으며, 컴파일러는 다음 방법으로 변수가 **not-null**로 간주합니다.   

1. 변수에 `null`이 아닌 것으로 알려진 값이 할당
2. `null` 체크를 했으며, 이 후에 `null` 값을 할당하지 않음

컴파일러는, 확인할 수 없는 변수가 `not-null`로 식별되지 않으면 `maybe-null`로 간주합니다.   
이러한 경우 실수로 `null` 값을 역참조하는 상황을 방지하기 위해 경고를 제공합니다.    

- 변수가 `not-null`인 경우 해당 변수를 안전하게 역참조할 수 있습니다.
- 변수가 `maybe-null`인 경우 해당 변수를 역참조하기 전에 검사할 필요성이 있습니다.

```cs
string? message = null;

// 경고: 널 역참조
Console.WriteLine(message.Length);

var originalMessage = message;
message = "Hello, World!";

// null이 아닌 값으로 할당되었기 때문에 경고를 발생시키지 않음
Console.WriteLine(message.Length);

// 경고! (표시는 X)
Console.WriteLine(originalMessage.Length);
```
컴파일러는 첫 번째 `maybe-null`에 대한 역참조가 발생할 때 경고를 표시합니다.    
다만, 그 이후의 `maybe-null` 역참조에 대해서는 표시하지 않습니다.    

```cs
void FindRoot(Node node, Action<Node> processNode)
{
    for (var current = node; current != null; current = current.Parent)
    {
        processNode(current);
    }
}
```
해당 코드에서는 `current.Parent`에 접근하기 전과 `current`를 `processNode` 액션에 전달하기 전에,     
변수 `current`의 null 여부를 자체적으로 검사합니다.   
때문에 해당 코드에서는 `current` 변수의 역참조에 대해 어떤 경고도 발생시키지 않습니다.   

> `null-state` 분석의 한계로, 함수 내부에서의 null 체크는 잘 분석하지만,     
> 외부 메서드 안에서 null 여부가 보장되는지는 추적하지 못합니다.     
> [MemberNotNull] 특성을 사용해 특정 필드/속성이 null이 아님을 보장하는 방법이 있습니다.

# API 시그니처의 특성
`null-state` 분석에는 API의 의미 체계를 이해하기 위한 개발자의 힌트가 필요합니다.    
일부 API는 null 검사를 제공하며, 해당 변수의 `null-state`를 _`maybe-null`에서 `not-null`_ 로 변경합니다.

```cs
    static void PrintMessageUpper(string? message)
    {
        if(!IsNull(message))
        {
            Console.WriteLine(message);
        }
    }
    //static bool IsNull(string? s) => s == null;
    static bool IsNull([NotNullWhen(false)] string? s) => s == null;
```
주석 처리된 첫번째 IsNull의 경우 문제가 없어 보이지만,   
컴파일러 입장에서 IsNull 메서드가 null 체크의 역할이라는 사실을 모르기 때문에 `message`가 여전히 `maybe-null`로 간주되고,    
`message.ToUpper` 등의 메서드에서 경고를 발생시킵니다.    

이 때, 개발자는 [NotNullWhen] 특성을 사용해 해당 메서드가       
특정 값을 반환할 때 null 값이 아님을 보장할 수 있습니다.  

# null 허용 변수 주석
`null-state` 분석은 지역 변수에 대한 강력한 분석을 제공합니다.   
하지만 멤버 변수에 대해서는, 컴파일러가 개발자에게 더 많은 정보를 요구합니다.   

컴파일러는 특정 멤버의 `null-state`를 설정하려면 더 많은 정보를 필요로 합니다.   

객체는 접근 가능한 어떤 생성자든 사용할 수 있기 때문에,    
특정 필드가 null로 설정될 가능성이 있다면, 컴파일러는 각 메서드의 시작 시점에 해당 필드를 `maybe-null`로 간주합니다.   

때문에 개발자는 변수에 nullable인지 non-nullable인지 선언하는 _주석_ 을 사용합니다.   

## null이 아닌 참조
컴파일러는 null 확인 없이 해당 변수들을 참조할 수 없게합니다.    

- 선언 시 반드시 null이 아닌 값으로 초기화해야 함
- null 넣으려 하면 컴파일러 경고
- 컴파일러는 해당 변수가 항상 유효한 참조를 갖는다고 추론

`string`, `object` 등의 일반적인 참조 타입이 이러한 형태입니다.   

## null이 가능한 참조
null이 될 수 있다는 것을 명시합니다.  

- 초기값 없이 선언하거나 null을 넣어도 경고 없음
- 값을 사용할 때는, null 체크를 반드시 해야 함
- 역참조를 하기 전에 반드시 null이 아님을 보장해야 함

`string?`, `object?` 등의 nullable 참조 타입이 이러한 형태입니다.   

---

경우에 따라 변수가 null이 아님을 알고 있지만    
컴파일러가 `null-state`를 `maybe-null`임을 판단하는 경우 별도 처리가 필요합니다.   
변수 이름 뒤에 `!`을 사용해 `null-state`를 `not-null`로 강제할 수 있습니다.   
```cs
string nullString = null;

Console.WriteLine(nullString.Length); // 경고 표시

Console.WriteLine(nullString!.Length); // 경고 없음
```
---
null 허용 _참조_ 타입과 null 허용 _값_ 타입은 유사한 의미 체계를 가집니다.   
둘 다 내용이 존재하거나 null일 수 있음을 알려주는 역할을 합니다.   

다만, null 허용 참조 타입은 같은 방식으로 구현되지만, (ex) `string`과 `string?`은 `System.String`으로 같음)     
null 허용 값 타입은 다른 방식으로 구현된다는 차이가 있습니다.      
(ex) `int`와 `int?`는 각각 `System.Int32`와 `System.Nullable<System.Int32>`로 다름)

---
---

해당 기능들은 코드가 실행되기 전에만 동작하며,   
실행 중에 null을 막아주는 기능은 아닙니다.  

즉, 호출자가 경고를 무시하고 null을 허용하지 않는 변수에 일부러 null을 넘길 수 있습니다.   
때문에 라이브러리 작성자는 매개변수가 null인지 _런타임에 확인하는 코드_ 를 반드시 넣어야 합니다.   

`?`, `!`는 **단지 코드를 읽는 사람에게 의도를 보여주는 용도**이지,    
런타임에 아무런 기능이 없다는 것을 이해해야 합니다. 

# 제네릭
제네릭 null 허용 타입 `T?`에 대해서는 조금 더 자세한 규칙이 필요합니다.   
null 허용 값 타입과 null 허용 참조 타입이 다르기 때문에 이 부분은 명확히 이해할 필요가 있습니다.     

## T?의 의미
- `T`가 참조 타입 ==> nullable 참조형 (`string` -> `string?`)
- `T`가 값 타입 ==> 같은 타입에 Nullable 포장 (`int` -> `Nuallable<int>`)
- `T`가 nullable 참조 타입 ==> 그대로 (`string?` -> `string?`)
- `T`가 nullable 값 타입 ==> 그대로 (`int?` -> `int?`)

## 반환 값과 매개변수에서의 T?
- 반환값의 `T?`는 `[MaybeNull]T`
- 매개변수의 `T?`는 `[AllowNull]T`

## 제약 조건
C#에서 제네릭 제약조건은 `where T : ...` 구문으로 사용 가능합니다. 

- class
   - non-nullable 참조형 타입만 가능
   - T가 참조형이라는 것을 확실히 해서 null 대입, 멤버 접근 등을 안전하게 작성할 수 있게 함
   - ```cs
     class MyClass<T> where T : class {}
     ```

- class?
   - nullable 여부와 관계 없이 참조형 타입 가능
   - C# 8부터 사용 가능
   - ```cs
     class MyClass<T> where T : class?{}
     ```
- notnull
   - nullable이 아닌 타입만 허용
   - C# 8부터 사용 가능
   - ```cs
     class MyClass<T> where T : notnull{}
     ```

# Nullable 컨텍스트
Nullable 컨텍스트는     
nullable 참조 타입 주석이 처리되는 방법과 null-state 분석을 통해 생성되는 경고를 결정합니다.   
Nullable 컨텍스트에는 _문법_, _경고_ 설정의 2가지 플래그 설정이 가능합니다.    

.NET 6, C# 10 이전까지는 _문법_, _경고_ 설정은 사용하지 않도록 설정되며,  
이후 버전 부터는 두 설정 모두 활성화 됩니다.   

이전 버전이 해당 설정을 사용하지 않는 이유는, 이전 버전의 별도 처리 없이 사용을 위함인데      
예로 명시적 참조 타입은 `not-null` 형태로 해석되고 제네릭 `class`도 이와 같은 형태로 해석되는데,     
이전에는 이에 대해 경고를 표시하지 않았었습니다. (`class?`와 같은 문법이 후에 정립되면서)     
이전에 `class` 만 작성하여도 경고를 주지 않았던 코드들이 발생시킬 수도 있게 되었습니다.     

소규모 프로젝트의 경우 null 허용 참조 타입을 사용하도록 설정하고, 경고에 대해 수정하는 것이 쉽지만,     
대규모 프로젝트, 다중 프로젝트 솔루션의 경우 많은 수의 경고를 해결하는 것이 쉽지 않을 수 있습니다.    

`#nullable` 이라는 pragma를 사용하여 관련 설정이 가능한데 사용할 수 있는 옵션은 다음과 같습니다.     

- `#nullable disable`
   - nullable 관련 기능을 완전히 끔
   - 문법 (annotation): X -> ex) `string`과 `string?`의 구분 X (둘 다 null 허용하던 `string`으로 간주)
   - 경고 (warning): X -> 경고 표시 X
   - ```cs
     #nullable disable

     string maybeNull = null; // 경고 없음
     string? x = null;        // 의미는 있지만 효과 없음
     ```
 
- `#nullable enable`
   - nullable 관련 기능을 완전히 사용
   - 문법 (annotation): O -> ex) `string`과 `string?`의 구분 O
   - 경고 (warning): O -> 경고 표시 O
   - ```cs
     #nullable enable

     string notNull = null;   // 경고: null 할당 불가
     string? canBeNull = null; // 허용
     ```

- `#nullable warning`
   - 경고 관련 기능만 사용
   - 문법 (annotation): X -> ex) `string`과 `string?`의 구분 X
   - 경고 (warning): O -> 경고 표시 O
   - ```cs
     #nullable warnings

     string? test = null;     // 문법상 의미 없음 (그냥 string으로 처리됨)
     string nonNull = null;   // 경고: null 할당됨 (문법은 무시되지만 분석은 함)
     ```

- `#nullable annotation`
   - 문법 관련 기능만 사용
   - 문법 (annotation): O ->  ex) `string`과 `string?`의 구분 O
   - 경고 (warning): X -> 경고 표시 X
   - ```cs
     #nullable annotations

     string? canBeNull = null; // 경고 없음
     string notNull = null;    // 경고 없음 
     // 문법은 적용되지만 분석이나 검사는 하지 않음
     ```

이렇게 개별로 적용하는 방법도 있지만, `.csproj` 파일에 `<Nullable>` 요소를 사용하여 파일 전체에 대해 적용할 수도 있습니다.   
```xml
<Nullable>warnings</Nullable>
```

# 알려진 문제점
배열 및 구조체는 null 허용 참조뿐만 아니라 null 안전을 확인하는 정적 분석에서도 알려진 함정입니다.   
두 경우 모두 null을 허용하지 않는 참조 타입이 경고를 생성하지 않고 null로 초기화될 수 있습니다.   

## 구조체
null을 허용하지 않는 참조 타입을 포함하는 구조에서는 default를 할당해도 경고를 표시하지 않을 수 있습니다.
