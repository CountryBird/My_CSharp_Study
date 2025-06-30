_메서드_ 는 일련의 구문을 포함하는 코드 블럭을 의미합니다.    
프로그램은 **메서드를 호출하고**, **해당 메서드에 파라미터** 를 줌으로써 구문을 실행합니다.   

# 메서드 시그니처
메서드는 `class`, `record`, `struct` 안에서 선언 가능합니다.     

- 접근 수준을 선택할 수 있습니다. (`public`, `private` 등, 기본값은 `private`)
- 선택적 한정자를 적용할 수 있습니다. (`abstract`, `sealed` 등)
- `void`의 경우를 제외하고 특정 반환값을 가집니다.
- 이름을 가집니다.
- 메서드 파라미터는 괄호로 묶고, 쉼표로 구분합니다. 빈 괄호는 해당 메서드가 파라미터를 필요로 하지 않음을 나타냅니다.

오버로딩된 메서드의 이름은 각각 같을 수 있으며,    
파라미터의 구조에 따라 구분합니다.    

# 메서드 호출
메서드는 _인스턴스_ 또는 _정적_ 일 수 있습니다.    
_인스턴스_ 메서드는 특정 클래스 객체에 소속된 메서드를 의미하며,    
이에 따라 메서드를 호출하려면 객체를 인스턴스화할 필요가 있습니다.       
_정적_ 메서드는 인스턴스 객체에서 작동되지 않으며      
일부러 호출을 시도하려고 해도 컴파일 오류가 발생하며 작동되지 않습니다.     

메서드 호출은 필드 엑세스와 비슷한 형태입니다.      
`(객체/타입 이름).(메서드 이름)`의 형태를 띄며, 파라미터는 괄호 안에 나열되고 쉼표로 구분됩니다.    

메서드 정의는 필요한 모든 파라미터의 이름 및 타입을 지정합니다.      
호출자는 각 파라미터에 대해 인수라는 구체적인 값을 제공합니다.      
인수는 파라미터의 타입과 호환되어야 하지만, 이름까지 같을 필요는 없습니다.     

메서드를 호출할 때, 위치로 구분된 인수를 사용하는 대신 '명명된 인수'라는 개념도 사용할 수 있습니다.    
명명된 인수를 사용하는 경우 파라미터 이름 뒤에 `:`과 인수 값을 지정합니다.   
```cs
public override int Drive(int miles, int speed) =>
        (int)Math.Round((double)miles / speed, 0);

 static void Main()
    {
        int travelTime = moto.Drive(speed: 60, miles: 170);
        Console.WriteLine($"Travel time: approx. {travelTime} hours");
    }
```
명명된 인수를 사용하여, 메서드에 정의된 순서와 반대로 인수를 주었음에도     
정상적으로 인수 값이 반영되는 것을 확인할 수 있습니다.     

위치 인수와 명명된 인수를 둘 다 사용하여 메서드를 호출할 수도 있습니다.     
그러나 **명명된 인수가 올바른 위치에 있는 경우에만** 명명된 인수 뒤에 위치 인수를 사용할 수 있음에 유념해야 합니다.    

# 상속되고 재정의된 메서드
타입 객체들은 해당 타입 내에서 명시적으로 정의한 멤버 외에,      
기본 클래스에서 정의된 멤버를 상속하여 사용할 수 있습니다.    

타입 시스템의 모든 타입이 직간접적으로 `Object` 클래스를 상속하기 때문에,     
모든 타입은 `Equals(Object)`, `GetType()`, `ToString()`과 같은 멤버 메서드를 사용할 수 있습니다.   
```cs
public static void Main()
    {
        Person p1 = new() { FirstName = "John" };
        Person p2 = new() { FirstName = "John" };
        Console.WriteLine($"p1 = p2: {p1.Equals(p2)}");
    }
```

타입 객체는 `override` 키워드를 사용하여 상속한 멤버 메서드를 재정의할 수 있습니다.     
```cs
public class Person
{
    public string FirstName = default!;

    public override bool Equals(object? obj) =>
        obj is Person p2 &&
        FirstName.Equals(p2.FirstName);

    public override int GetHashCode() => FirstName.GetHashCode();
}
```

# 파라미터 전달
C#에서의 타입은 _값 타입_ 또는 _참조 타입_ 입니다.    

## 값으로 파라미 전달
값 타입이 메서드에 전달되는 경우 객체 자체가 아니라 _객체의 복사본_ 이 메서드에 전달됩니다.    
따라서 메서드를 통한 객체의 변경 내용은 원래 객체에 영향을 주지 않습니다.
```cs
public static void Main()
    {
        var value = 20;
        Console.WriteLine("In Main, value = {0}", value);
        ModifyValue(value);
        Console.WriteLine("Back in Main, value = {0}", value);
    }

    static void ModifyValue(int i)
    {
        i = 30;
        Console.WriteLine("In ModifyValue, parameter value = {0}", i);
        return;
    }
//      In Main, value = 20
//      In ModifyValue, parameter value = 30
//      Back in Main, value = 20
```

참조 타입의 객체가 메서드에 전달되는 경우 _객체에 대한 참조 값_ 이 전달됩니다.    
따라서 메서드를 통한 객체의 변경은 참조 주소를 찾아가서 변경을 하는 형태가 되기 때문에, 원래 객체에 영향을 줍니다.   
```cs
public static void Main()
    {
        var rt = new SampleRefType { value = 44 };
        ModifyObject(rt);
        Console.WriteLine(rt.value);
    }

    static void ModifyObject(SampleRefType obj) => obj.value = 33;
```

## 참조로 파라미터 전달
메서드의 인수 값을 변경하고 이후 반환하는 용도로 사용하기 위해, 참조로 파라미터를 전달합니다.   
참조로 파라미터를 전달하려면 `ref` 또는 `out` 키워드를 사용합니다.     
(`in` 키워드 또한 참조로 값을 전달하지만 읽기 전용으로 값을 넘기기 때문에 현재 목적에서는 사용하지 않습니다.)    

```cs
public static void Main()
    {
        var value = 20;
        Console.WriteLine("In Main, value = {0}", value);
        ModifyValue(ref value);
        Console.WriteLine("Back in Main, value = {0}", value);
    }

    private static void ModifyValue(ref int i)
    {
        i = 30;
        Console.WriteLine("In ModifyValue, parameter value = {0}", i);
        return;
    }
```

## 컬렉션 파라미터
경우에 따라, 메서드의 인수 개수를 정확히 맞추는 것은 어려울 수 있습니다.     
`params` 키워드를 사용하여 파라미터가 컬렉션임을 나타내면, 가변 개수의 인수를 사용하여 메서드를 호출할 수 있습니다.    
해당 키워드로 지정된 파라미터는 _컬렉션 타입_ 이어야 하며, 파라미터 목록 중 마지막에 위치해야 합니다.     

`params` 파라미터에 대해 다음과 같은 방식으로 메서드를 호출할 수 있습니다.
- 원하는 개수의 요소를 포함하는 적절한 타입의 컬렉션을 전달합니다.
- 적절한 타입의 개별 인수를 쉼표로 구분해 메서드에 전달합니다.
- null을 넘겨줍니다.
- 인수를 넣지 않습니다.

**그냥 컬렉션 객체를 넘기는 것과의 차이는, 호출 과정 중 컬렉션 객체를 만들지 않아도 된다는 점이 있습니다.**       

다음과 같이 정의된 `GetVowels` 메서드에 대해서,
```cs
static string GetVowels(params IEnumerable<string>? input)
{
        if (input == null || !input.Any())
        {
            return string.Empty;
        }

        char[] vowels = ['A', 'E', 'I', 'O', 'U'];
        return string.Concat(
            input.SelectMany(
                word => word.Where(letter => vowels.Contains(char.ToUpper(letter)))));
}
```

개발자는 다음과 같은 4가지 방식으로 파라미터를 넣을 수 있습니다.
```cs
string fromArray = GetVowels(["apple", "banana", "pear"]);
Console.WriteLine($"Vowels from collection expression: '{fromArray}'");

string fromMultipleArguments = GetVowels("apple", "banana", "pear");
Console.WriteLine($"Vowels from multiple arguments: '{fromMultipleArguments}'");

string fromNull = GetVowels(null);
Console.WriteLine($"Vowels from null: '{fromNull}'");

string fromNoValue = GetVowels();
Console.WriteLine($"Vowels from no value: '{fromNoValue}'");
```

# 선택적 파라미터 및 인수
