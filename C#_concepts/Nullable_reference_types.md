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
