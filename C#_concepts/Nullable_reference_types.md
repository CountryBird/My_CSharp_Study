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
