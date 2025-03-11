`Inheritance`, _상속_ 은 _캡슐화_, _다형성_ 과 함께 객체 지향에서 중요하게 생각되는 개념 중 하나입니다.

상속을 사용하면 다른 클래스에 정의된 동작을 재사용하거나, 수정하는 새로운 클래스를 만들 수 있습니다.

흔히 상속의 대상이 되는 클래스를 **기본 클래스, 부모 클래스**라고 하며,  
상속을 하는 클래스를 **파생 클래스, 자식 클래스**라고 합니다.

상속은 원칙적으로 기본 클래스를 하나만 가질 수 있으며, 여러 클래스를 한 번에 상속하는 것은 불가능합니다.  
(단, 상속을 반복함으로써 내용을 이어갈 수는 있습니다.)

# 가상 메서드와 추상 메서드
기본 클래스에서 사용할 수 있는, 상속을 위한 키워드로는 `virtual`과 `abstract`가 있습니다.

기본 클래스가 메서드를 virtual로 선언하는 경우, 파생 클래스가 이를 `override`함으로 재정의할 수 있으며,  
기본 클래스가 메서드를 abstract로 선언하는 경우, 파생 클래스는 기본 클래스에서 상속되는 모든 메서드를 재정의해야 합니다. 

```cs
 public class MyVirtualClass
 {
     public virtual void virtualMethod()
     {
         Console.WriteLine("virtual");
     }
 }
public class MyVirtualChild : MyVirtualClass
{
    // virtual은 상속 안해도 무방
    public override void virtualMethod()
    {
        Console.WriteLine("virtual child");
    }
}
```
virtual로 정의된 메서드는 파생 클래스에서 반드시 재정의할 필요는 없지만, 재정의하려는 경우 override 키워드가 필요합니다.
```cs
MyVirtualClass myVirtualClass = new MyVirtualClass();
myVirtualClass.virtualMethod(); // virtual

MyVirtualChild myVirtualChild = new MyVirtualChild();
myVirtualChild.virtualMethod(); // virtual child
```
virtual 메서드를 가지는 클래스는 자체적으로도 생성이 가능하며, virtual 메서드는 재정의하여 기능을 바꿀 수 있습니다.

---

```cs
 public abstract class MyAbstractClass // abstract class만 abstract method를 가질 수 있음
 { 
     public abstract void abstractMethod(); // abstract method는 본문 작성 불가
 }
 public class MyAbstractChild : MyAbstractClass
 {
     // abstract는 상속 안하면 에러 발생
     public override void abstractMethod()
     {
         Console.WriteLine("abstract child");
     }
 }
```
abstract 메서드를 가지는 클래스 또한 abstract 형태이어야 하며, abstract로 정의된 메서드는 파생 클래스에서 반드시 재정의 하여야 합니다.
```cs
MyAbstractClass myAbstractClass = new MyAbstractClass(); // 추상 클래스는 생성 불가

MyAbstractChild myAbstractChild = new MyAbstractChild();
myAbstractChild.abstractMethod(); // abstract child
```
추상 클래스는 자체적으로는 생성될 수 없으며, 상속의 형태로만 생성이 가능합니다.  

이름따라, `virtual` 키워드는 어느 정도 실체를 가지는 홀로그램의 형태(자체적으로 형태를 가지지만 구체화될 수 있음).
`abstract`는 추상적인, 실체가 없는 형태(반드시 구체화되어야 사용 가능)라고 생각하면 될 것 같습니다.

# 추상 기본 클래스
: 인터페이스와 비슷하게, 특정 클래스가 직접 인스턴스화되는 것을 방지하려면 `abstract` 키워드를 사용하면 됩니다.  
추상 클래스가 반드시 추상 멤버만을 가져야 하는 것은 아니지만, 상속에 대한 제약은 그대로이기에 해당 멤버도 상속을 통한 인스턴스화가 필요합니다.

# 인터페이스
: 인터페이스는 멤버 집합을 정의하는 참조 형식으로, 추상 클래스와 비슷하게 이를 구현하는 모든 클래스/구조체는 해당 멤버를 구현해야 합니다.  
인터페이스와 추상 클래스의 가장 큰 차이는, 인터페이스는 다중 상속 (한 번에 여러 개 상속)이 가능하다는 점입니다.  

# 멤버 숨기기
: `new` 키워드를 사용해 파생 클래스가 기본 클래스의 멤버를 숨길 수 있습니다.  
new 키워드 사용이 필수는 아니지만, 컴파일러가 경고를 생성할 수는 있습니다.
```cs
public class MyClass
{
    public void method()
    {
        Console.WriteLine("myclass");
    }
}
public class MyChild : MyClass
{
    public new void method() 
    {
        Console.WriteLine("my child");
    }
}
```
```cs
MyChild myChild = new MyChild();
myChild.method(); // my child
```
멤버 숨기기에 대한 자세한 내용은 추후 다룰 예정입니다.
