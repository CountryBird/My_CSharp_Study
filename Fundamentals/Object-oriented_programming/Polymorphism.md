`Polymorphism`, _다형성_ 은 _캡슐화_, _상속_ 에 이어 개체 지향 프로그래밍의 특징으로 일컬어집니다.  
"여러 형태"를 의미하는 그리스 단어가 어원이며, 다형성이 적용되면 객체의 선언된 형식이 런타임 형식과 같지 않을 수 있습니다.

다형성은, 특정 객체 그룹이 동일한 방식으로 작업을 수행할 수 있습니다.  
기본 클래스 메서드를 호출하는 것으로, 모든 파생 클래스에 대해 적절한(재정의된) 메서드를 호출할 수 있습니다.
```cs
public class Shape
{
    public virtual void Draw()
    {
        Console.WriteLine("Just Draw");
    }
}

public class Triangle : Shape
{
    public override void Draw()
    {
        Console.WriteLine("Draw Triangle");
    }
}
public class Circle: Shape
{
    public override void Draw()
    {
        Console.WriteLine("Draw Circle");
    }
}
```
기본 클래스인 Shape과, 이를 상속하는 Triangle 클래스와 Circle 클래스가 있다고 가정해 봅시다.
```cs
 List<Shape> shapes = new List<Shape> { new Triangle(), new Circle() };
 foreach (var shape in shapes)
 {
     shape.Draw();
     Console.WriteLine(shape.GetType());
 }
// Draw Triangle
// Program + Triangle
// Draw Circle
// Program + Circle
```
Shape에 대한 List를 작성하였기 때문에, 컴파일 시간에는 List의 구성이 Shape인 것을 알 수 있습니다.  
하지만, 보이는 것과 같이 런타임 시간 중에는 새로 할당된 파생 클래스로 타입이 바뀌며, 실행되는 메서드와 GetType을 통해 이를 확인할 수 있습니다.

# 가상 멤버
파생 클래스가 가상 멤버를 재정의하면 새로운 동작을 정의할 수 있습니다.  
추상 멤버와의 차이로는 반드시 재정의할 필요는 없다는 것이며, 따로 재정의하지 않을 경우 기본 메서드의 형태로 실행 가능합니다.
```cs
public class MyVirtualClass
{
    public virtual void method()
    {
        Console.WriteLine("virtual class");
    }
    public virtual void method2()
    {
        Console.WriteLine("virtual class");
    }
}
public class MyOverrideChild : MyVirtualClass
{
    public override void method()
    {
        Console.WriteLine("override child");
    }
}
```
```cs
MyVirtualClass myVirtualClass1 = new MyVirtualClass();
myVirtualClass1.method(); // virtual class
myVirtualClass1.method2(); // virtual class

MyVirtualClass myVirtualClass2 = new MyOverrideChild();
myVirtualClass2.method(); // override child
myVirtualClass2.method2(); // virtual class
```
다형성으로 인해 런타임 중에 `myVirtualClass2`는 MyOverrideChild로 변형되었음을 확인할 수 있습니다.  
또한, 파생 클래스는 별도 재정의 없이 기본 클래스의 가상 메서드를 사용하는 것도 확인할 수 있습니다.

# 기본 클래스 멤버 숨기기
파생 클래스가 _재정의를 하지 않고도_ 기본 클래스와 동일한 이름을 가지는 멤버를 가지려면, `new` 키워드를 사용할 수 있습니다.
```cs
public class MyClass
{
    public void method()
    {
        Console.WriteLine("my class");
    }
}
public class MyNewChild : MyClass
{
    public new void method()
    {
        Console.WriteLine("new child");
    }
}
```
new 키워드를 사용하면 기본 클래스의 메서드와 같은 이름을 사용할 수 있으며, 메서드는 기본형이든 `virtual`이든 관계 없습니다.   
(기본 클래스와 아예 별개의 메서드를 생성하는 개념이기에 `abstract` 메서드에 대해서는 재정의를 하지 않은 것으로 판단하여 에러)  
```cs
MyClass myClass1 = new MyClass();
myClass1.method(); // my class

MyClass myClass2 = new MyNewChild();
myClass2.method(); // my class

MyNewChild myNewChild = new MyNewChild();
myNewChild.method(); // new child
```
`override` 하는 것과 `new`를 사용하는 것의 가장 큰 차이는 다형성으로 인해 변환된 인스턴스에서 볼 수 있습니다.  
개념 그대로 **숨기는** 개념이기 때문에, 파생 클래스에서 실행할 경우 새로 정의된 형태지만, 기본 클래스를 통해서 실행할 경우 기본 클래스의 형태를 보입니다.

# 파생 클래스에서 기본 클래스 멤버 사용
재정의된 멤버는 _덮어쓰여진_ 개념이기 때문에 파생 클래스에서는 기본 클래스의 멤버를 사용할 수 없습니다.  
하지만 부모를 의미하는 `base` 키워드를 사용하면 접근할 수 있습니다.
```cs
public class MyClass
{
    public virtual void method()
    {
        Console.WriteLine("my class");
    }
}
public class MyChild : MyClass
{
    public override void method()
    {
        base.method();
        Console.WriteLine("my child");
    }

}
```
```cs
MyClass myClass = new MyChild();
myClass.method(); // my class
                  // my child
```
