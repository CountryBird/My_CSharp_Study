`Generic`은 C#에서 제공하는 기능으로, 코드의 재사용성과 타입 안정성을 위한 기능입니다.

클래스나 메서드가 특정 데이터 형식에 국한되지 않고, 재사용이 가능해집니다.

C#의 자료구조 클래스(System.Collections.Generic)과 같이, .NET의 클래스들은 이러한 개념을 사용하고 있으며, 
사용자가 따로 만들어서 사용할 수도 있습니다.

```cs
public class MyGenricClass<T>
{
    public void method(T obj)
    {
        Console.WriteLine(obj);
    }
}
private static void Main(string[] args)
{
    MyGenricClass<int> myIntGenricClass = new MyGenricClass<int>();
    myIntGenricClass.method(1); // 1

    MyGenricClass<string> myStringGenricClass = new MyGenricClass<string>();
    myStringGenricClass.method("hello"); // hello
}
```
이와 같이 <T>를 사용하여 타입 매개변수를 일반화합니다. (T는 컴파일 중 타입이 배정되었을 때, 타입을 지정해주기 위한 임의 파라미터일 뿐, 반드시 T라는 이름을 가질 필요는 없습니다.)

```cs
public class MyPairGenericClass<A, B>
{
    public void method(A a,B b)
    {
        Console.WriteLine(a);
        Console.WriteLine(b);
    }
}
private static void Main(string[] args)
{
    MyPairGenericClass<int, string> myPairGenericClass = new MyPairGenericClass<int, string>();
    myPairGenericClass.method(1, "hello"); // 1
                                           // hello
}
```
지정해 주는 형식에 따라 서로 다른 타입에 대한 연산을 더 간단하고 안정적으로 수행할 수 있습니다.
