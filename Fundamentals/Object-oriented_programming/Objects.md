`클래스`와 `구조체`의 정의는 형식이 수행할 수 있는 작업을 지정하는 청사진과 비슷한 개념입니다.

객체는 이 청사진에 따라 지어진 건물/완성품과 같은 개념이며, 인스턴스라는 다른 이름으로도 불립니다.

# 클래스 인스턴스 및 구조체 인스턴스
## 클래스
클래스는 참조 형식이며, 이에 따라 한 변수가 동일한 타입의 변수에 할당된 경우 같은 객체를 참조하게 됩니다.

클래스 인스턴스는 new 연산자 + 생성자를 사용하여 생성됩니다. 별도로 지정해주지 않으면 자체적으로 파라미터를 받지 않는 생성자를 생성합니다.
```cs
public class MyClass
{
    public int value;
}

MyClass myClass1 = new MyClass();
MyClass myClass2 = myClass1; 
Console.WriteLine(myClass1.Equals(myClass2)); // True
```

## 구조체
구조체는 값 형식이며, 클래스와 동일하게 new 연산자를 사용하여 구조체 인스턴스를 만들 수 있습니다. (필수는 아님)

하지만, 클래스처럼 기본 생성자를 제공하지 않기 때문에 사용하려면 따로 지정이 필요합니다. 
```cs
public struct MyStruct
{
    public int value;
    public MyStruct()
    {
        // 구조체 생성자 별도 필요
    }
}

MyStruct myStruct;
MyStruct myStruct2 = new MyStruct();
```
# 객체 아이덴티티 및 값 동등 
## 객체 아이덴티티란?
: 모든 객체가 다른 객체와 구별된다는 속성입니다. 각 객체마다 아이덴티티를 가지고 있다는 개념이며,

일란성 쌍둥이와 비슷하게, 서로를 구별할 수 없는 경우에도 다르다고 봅니다.

## 두 객체가 같다의 정의
`"두 객체가 같다"`라고 정의 내리려고 할 때, 하나 짚고 넘어가야 할 부분이 있습니다.

바로 _동일한 객체_ 를 의미하는 것인지, _가지고 있는 값이 같은 객체_ 를 의미하는 것인지 입니다.

전자의 관점이 `System.Object`의 `Equals` 메서드입니다. 두 객체가 동일한 위치를 참조하는지 확인합니다.
- 때문에, System.Object를 상속하는 클래스의 Equals 함수는 위의 방식으로 동작합니다.
```cs
MyClass myClass1 = new MyClass();
MyClass myClass2 = new MyClass();
Console.WriteLine(myClass1.Equals(myClass2)); // False
```
후자의 관점이 `System.ValueType`의 `Equals` 메서드입니다. 두 객체의 필드 값이 같은지를 확인합니다.
- 때문에, System.ValueType을 상속하는 구조체의 Equals 함수는 위의 방식으로 동작합니다.
```cs
MyStruct myStruct1 = new MyStruct();
MyStruct myStruct2 = new MyStruct();
Console.WriteLine(myStruct1.Equals(myStruct2)); // True
```

### IEquatable<T>과 IEqualityComparer<T>
앞서 클래스가 동일 객체에 대해 Equals를 수행한다고 했지만, 다른 방식으로 비교하는 것이 아예 불가능하지는 않습니다.
IEquatable<T> 인터페이스를 상속하는 것으로(여기서 T는 해당 클래스와 비교하고자 하는 타입), 사용자가 비교 방식을 지정할 수 있습니다.
```cs
 public class MyClass:IEquatable<MyClass>
 {
     public int value;

     public bool Equals(MyClass other) 
     {
         return this.value == other.value;
     }
 }
```
Equals는 IEquatable의 메서드로, 비교 방식이 위치하는 메서드이기 때문에 반드시 구현해주어야 합니다.
```cs
 MyClass myClass1 = new MyClass();
 MyClass myClass2 = new MyClass();
 Console.WriteLine(myClass1.Equals(myClass2)); // True
```
또한, IEqualityComparer<T> 인터페이스를 상속하는 것으로(여기서 T는 비교하고자 하는 타입), 사용자가 비교 방식을 지정할 수 있습니다.
IEquatable과의 차이로는, 

이름 그대로 IEquatable을 상속하면 해당 클래스가 **_동등한지 비교 가능해지는_** 것이고,

IEqualityComparer는 해당 클래스가 **_동등한지 비교해주는_** 클래스가 된다는 것입니다.
```cs
public class MyEqualityComparer : IEqualityComparer<MyClass>
{
    public bool Equals(MyClass? x, MyClass? y)
    {
        return x.value == y.value; // 비교 방식
    }

    public int GetHashCode(MyClass obj)
    {
        throw new NotImplementedException(); // 구현은 필수지만 Equals만 사용하고자 하면 내용은 자유
    }
}
```
Equals와 GetHashCode는 IEqualityComparer의 메서드로, 반드시 구현해주어야 합니다.
```cs
MyClass myClass1 = new MyClass();
MyClass myClass2 = new MyClass();
MyEqualityComparer comparer = new MyEqualityComparer();
Console.WriteLine(comparer.Equals(myClass1, myClass2)); // True
```
