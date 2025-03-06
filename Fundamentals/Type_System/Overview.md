**C#은 다른 형끼리의 변환이 금지되어있고, 만약 변환을 하고싶다면 명시적으로 타입을 선언해 주어야 합니다.**
_==> 이러한 특성을 강 타입 언어라고 합니다._

```cs
int myInt = 0;
char myChar = 'a';
bool myBool = true;
```
각 변수가 이러하다고 가정하고,

```cs
int intandbool1 = myInt + myBool; // 에러
bool intandbool2 = myInt + myBool; // 에러

char charandbool1 = myChar + myBool; // 에러
bool charandbool2 = myChar + myBool; // 에러
```
다음과 같은 코드를 작성하면 타입 안정성을 지키기 위해 에러가 나는 것을 확인할 수 있습니다.

```cs
int intandchar1 = myInt + myChar; // 정상 실행
char intandchar2 = myInt + myChar; // 에러
```
하지만 다음과 같은 경우에는 한 쪽은 실행이 되는데,
이는 int가 32비트, char가 16비트 정수로 실행이 되기 때문이라고 합니다.
단, int가 char에 비해 범위가 크기 때문에 문제를 일으킬 가능성이 있어 다른 한 쪽은 에러를 발생시킵니다.

**핵심적으로, 타입들끼리 연산이 되었을 때 데이터에 지장이 가느냐를 기준으로 판단하는 것입니다.**

# 변수 선언에서 형식 지정
C#에서는 `var` 키워드를 사용해서 컴파일러가 타입을 추론하게 할 수 있습니다.
때문에 사용자 지정 형식 등의 복잡한 타입을 간단히 초기화 할 수 있습니다.
```cs
var myVar = 0;
myVar = 1; // 정상 실행
myVar = 'a'; // 정상 실행
myVar = true; // 에러
```
단, 타입이 자동적으로 변한다는 개념이 아닌 처음 초기화만 자동으로 된다는 관점으로 보는 것이 맞습니다.

# 값 형식
값 형식은 System.Object-> System.ValueType에서 상속됩니다.
해당 값이 직접 포함되며, 상속 불가능 합니다.

`struct`와 `enum`으로 나눌 수 있습니다.
기본 숫자 타입들은 `struct`에 속합니다.

```cs
byte b = 0xA; // 10
```
_※ 0x는 16진수를 의미합니다._

```cs
public struct MyStruct
{
    public int a, b;
    public MyStruct(int a1,int b1)
    {
        a = a1;
        b = b1;
    }
}
```
사용자가 고유하게 `struct`를 만드는 것도 가능합니다.

```cs
public enum MyEnum1
{
    A,B,C,D // 0,1,2,3
}
public enum MyEnum2
{
    A,B=100,C=50,D // 0, 100, 50, 51
}
```
`enum` 타입은 기능적인 부분보다는 사용자 편의를 위한 타입입니다. FileMode.Create, FileMode.Open과 같이 가독성 향상이 목적입니다.

따로 적어주지 않으면 암묵적으로 **이전 값에서 1씩 더해지며**, 값은 중복 가능합니다.

# 참조 형식
참조 형식은 System.Object에서 바로 상속되며, 변수 선언 시 해당 형식의 인스턴스 할당 또는 new로 생성하여야 합니다.

값의 참조값이 할당됩니다.

`interface`는 new 연산자를 통해 직접 인스턴스화 될 수 없으며, 이를 상속하는 클래스의 인스턴스가 필요합니다.

```cs
MyClass myClass = new MyClass();
IMyInterface myInterface = myClass; // 인터페이스 자료형은 앞에 I를 넣어주는 게 권장되는 관례
```

# 리터럴 값 형식 / 제네릭 형식
```cs
var myInt = 0;
var myLong = 0L; // 소문자는 숫자 1과 혼동될 경우가 있어 대문자 권장

var myFloat = 0f;
var myDouble = 0d; 
Console.WriteLine(myInt.GetType()); // System.Int32
Console.WriteLine(myLong.GetType()); // System.Int64

Console.WriteLine(myFloat.GetType()); // System.Single (float 자료형의 전체 이름)
Console.WriteLine(myDouble.GetType()); // System.Double
```
C#에서 리터럴 값을 컴파일러에서 받아오는 것이 가능한데, 이를 이용해 변수의 입력 방법을 지정할 수 있습니다.

```cs
 List<int> myIntList = new List<int>();
 List<string> myStringList = new List<string>();
```
같은 클래스를 사용해도 제네릭 형식을 이용해 지정하면 사용시 이점을 볼 수 있습니다.

# 컴파일 형식과 런타임 형식
```cs
 string myMessage1 = "Message";
 Console.WriteLine(myMessage1.GetType()); // System.String

 object myMessage2 = "Message";
 Console.WriteLine(myMessage2.GetType()); // System.String

 IEnumerable<char> myMessage3 = "Message";
 Console.WriteLine(myMessage3.GetType()); // System.String
```
**컴파일 형식과 런타임 형식을 다를 수 있다는 것을 유념해야 코드가 형식과 상호 작용하는 방식을 효과적으로 이해할 수 있습니다.**
