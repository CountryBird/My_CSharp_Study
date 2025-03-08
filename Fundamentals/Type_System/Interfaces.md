`interface`는 class나 struct가 구현해야 하는 기능에 대한 정의를 포함하는, 일종의 **설계도** 역할을 합니다.

- 인터페이스는 직접 인스턴스화될 수는 없으며, 해당 인터페이스를 구현하는 클래스, 구조체에 의해 구현됩니다.
- 인터페이스의 이름은 **I**로 시작하는 규칙이 있습니다.
- 인터페이스의 멤버는 기본적으로 `public`입니다.
- 클래스와 다르게 다중 상속이 가능합니다.
 
```cs
interface IMyInterface
{
    public void method1(); // C# 8.0 이전
    void method2() // C# 이후부터는 인터페이스에서도 구현 가능
    {
        Console.WriteLine("Hello");
    }
}
```
C# 8.0 이전에는 method1과 같이 멤버에 대한 명시만 가능했고, 이를 상속하는 class, struct는 반드시 해당 멤버를 구현하였어야 했습니다.

하지만 이후부터는 인터페이스도 멤버에 대한 기본 구현을 정의할 수 있게 되었으며, 구현이 된 멤버에 대해서는 상속하는 이들이 반드시 구현할 필요는 없게 되었습니다.

```cs
interface MyInterface2:IMyInterface
{
  // 인터페이스끼리도 상속이 가능하며, 이 경우 멤버를 반드시 구현할 필요는 없습니다.
}
class MyClass : IMyInterface
{
    public void method1()
    {
    }
    // method2는 구현할 필요 없음
}
```
단, method2와 같이 인터페이스 내에 구현된 멤버는 객체 타입을 인터페이스로 지정하거나, 클래스에서 재정의하지 않으면 사용할 수 없습니다.
```cs
MyClass myClass = new MyClass();
IMyInterface myInterface = new MyClass();
myClass.method2(); // 에러
myInterface.method2(); // 정상 실행
```
## 기본 인터페이스 메서드의 의미
위와 같은 이유로, 클래스가 자신이 상속하고 있는 인터페이스에 대해 별도 처리가 필요하다면, 기본 인터페이스의 의미가 없어 보일 수 있습니다.

### 공통 기능 제공으로 중복 코드 작성 방지
기본 인터페이스 메서드의 개념을 들었을 때, 가장 먼저 떠올릴 수 있는 의미입니다. 로그 출력과 같은 공통된 작업에 대한 중복 코드 작성을 줄일 수 있습니다.

하지만 기본 인터페이스 메서드의 목적이 단순히 _편리성_ 만은 아닙니다.

### 기존 인터페이스와의 호환성 유지
```cs
 interface IMyInterface
 {
     public void method1();
 }
 class MyClass : IMyInterface // 이렇게 IMyInterface를 상속하는 클래스가 다수 있다고 가정
 {
     public void method1()
     {

     }
 }
```
이러한 경우에 만약 `IMyInterface`에 `method2`라는 새로운 메서드를 만든다고 가정해봅시다.

인터페이스의 변화에 따라 이를 상속하는 모든 클래스에도 이에 대한 구현이 필요하고, 이는 효율적이지 않습니다.

하지만 기본 인터페이스 메서드를 사용하면 인터페이스 내부에 정의하는 것 하나로 새로운 메서드 추가가 가능해집니다.
