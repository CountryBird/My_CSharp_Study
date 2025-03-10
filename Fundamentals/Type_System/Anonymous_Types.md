`익명 타입`은 다른 클래스처럼 별도의 틀을 짜는 과정 없이 사용할 수 있도록 하는 타입입니다.

new {...}와 같은 형식으로 정의되며 내부에는 해당 타입이 가지는 속성이 정의됩니다.

컴파일러 수준에서 자체적으로 타입을 만들어 사용하는 개념이기에 초기화 시 var를 사용합니다.

```cs
var anonymousType = new { A = 1, B = "hello" };
Console.WriteLine(anonymousType.B); // hello
Console.WriteLine(anonymousType.GetType()); // <>f__AnonymousType0`2[System.Int32,System.String]
```
위와 같이, 흔히 볼 수 있는 타입이 아닌 컴파일러가 임의로 설정한 타입으로 설정되는 것을 알 수 있습니다.

`<>f__AnonymousType`로 시작하며, `[]` 내부에 속성에 대한 타입을 확인할 수 있습니다.

### 익명 타입 특징
- 속성은 `public` 읽기 전용 속성으로 정의됩니다 --> 한 번 초기화되면 일반적인 방법으로 변경할 수 없음
- 속성으로 null, 메서드, 포인터 형식일 수 없습니다. --> 개념적으로 접근하면, 값 형식과 안전한 참조 형식(배열 등)만 익명 타입에서 사용 가능합니다.
- object에서 직접적으로 상속되며, 이를 제외한 어떠한 형식으로도 캐스팅될 수 없는 class 형식입니다.

```cs
anonymousType.A = 2; // 에러
var anonymousType = new { A = 1, B = null }; // 에러
```

### 익명 타입 수정
앞서 서술했듯이, 원칙적으로 익명 타입은 초기화 된 이후 속성을 변경할 수 없습니다.
다만, `with`를 사용하여 새로운 값에 수정된 형식으로 할당하는 형태는 가능합니다.
```cs
var anonymousType = new { A = 1, B = "hello" };
var anonymousTypeWith = anonymousType with { B = "bye" };
Console.WriteLine(anonymousType.B); // hello
Console.WriteLine(anonymousTypeWith.B); // bye
```

### 익명 타입 내부 메서드
익명 타입의 객체들은 `System.Object`를 상속하는 형태이기 때문에, 일부 내부 메서드를 사용할 수 있습니다.
```cs
var anonymousType = new { A = 1, B = "hello" };
Console.WriteLine(anonymousType.ToString()); // { A = 1 , B = "hello"}
```
`ToString()` 메서드를 사용하면, 중괄호로 둘러싸인 모든 속성의 이름을 출력합니다.

```cs
 var anonymousType = new { A = 1, B = "hello" };
 var anonymousType2 = new { A = 1, B = "hello" };
 var anonymousType3 = new { B = "hello", A = 1 };
 
 Console.WriteLine(anonymousType.Equals(anonymousType2)); // True
 Console.WriteLine(anonymousType.Equals(anonymousType3)); // False
```
`Equals()`와 같은 메서드도 지원하는데, 속성들의 순서까지 확인하기 때문에 사용에 유의해야 합니다.
