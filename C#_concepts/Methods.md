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

## 값으로 파라미터 전달
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
메서드 정의 과정 중에 파라미터의 필수 여부를 지정할 수 있습니다.    
기본적으로 파라미터를 설정한 경우 필수로 취급하며, 기본값을 별도로 정의하는 것으로 선택적 파라미터가 사용 가능해집니다.    

파라미터로 사용되는 기본값은 다음과 같습니다. 

- 상수, 예로 리터럴 문자열이나 숫자가 있습니다.
- `default(타입)` 형태의 식입니다. 여기서 `타입`은 값 타입, 참조 타입 둘 다 될 수 있습니다.
이 때, 값 타입인 경우 해당 타입의 기본값, 참조 타입인 경우 null을 지정하는 것과 같습니다.     
- `new 타입()` 형태의 식입니다. 여기서 `타입`은 값 타입을 의미합니다.

메서드에 필수 파라미터, 선택적 파라미터가 둘 다 존재하는 경우,    
선택적 파라미터는 변수 목록의 마지막에 위치, 즉 _모든 필수 파라미터 다음_ 에 정의됩니다.   

```cs
public class Options
{
    public void ExampleMethod(int required, int optionalInt = default,
                              string? description = default)
    {
        var msg = $"{description ?? "N/A"}: {required} + {optionalInt} = {required + optionalInt}";
        Console.WriteLine(msg);
    }
}
```
선택적 파라미터를 사용함에 있어 주의할 점이 있는데,    
**만약 한 선택적 파라미터에 값을 할당하기로 했다면, 그 앞의 모든 선택적 파라미터에도 값을 할당해야 한다는 점**입니다.    

예를 들어 위의 코드를 기반으로,    
`opt.ExampleMethod(2,2,"Addition of 2 and 2")`와 `opt.ExampleMethod(2)`는 가능하지만      
`opt.ExampleMethod(2, ,"Addition of 2 and 2"`는 컴파일 오류를 일으킵니다.    

이는 컴파일러가 호출된 메서드의 파라미터에 어떤 인수가 매핑되는지 명확하게 알 수 있도록 하기 위함입니다.   

----

명명된 인수 / 위치 및 명명된 인수의 조합을 사용하여 메서드를 호출하는 경우,      
메서드 호출에서 마지막 위치 인수 이후에 오는 모든 인수를 생략할 수 있습니다.

```cs
var opt = new Options();
opt.ExampleMethod(10);
opt.ExampleMethod(10, 2);
opt.ExampleMethod(12, description: "Addition with zero:");
```
총 3번의 호출이 일어나는데,     
첫번째 호출은 선택적 인수를 둘 다 생략한 호출,    
두번째 호출은 마지막 선택적 인수만을 생략한 호출,    
세번째 호출은 명명된 인수를 사용해 중간의 선택적 인수를 생략한 호출입니다.    

이는 이전에 언급하였던 컴파일러가 파라미터 매핑에 모호함이 없기 때문이라 볼 수 있습니다.    

----

선택적 파라미터를 사용하면, _일부 파라미터를 생략한 채로 메서드를 호출할 수 있기 때문에,_      
오버로딩에 있어서 명확한 규칙이 필요합니다.   

1. **후보 선정**       
: 우선, 컴파일러는 필수/선택적 파라미터 여부에 관계 없이 모든 메서드 오버로드 후보를 찾습니다.

```cs
void DoSomething(int a);                     
void DoSomething(int a, int b = 0);          
void DoSomething(int a, string c = "Hello");
```
 위와 같은 경우, 3가지 방식들이 모두 후보로 선정됩니다.     

2. **후보 간의 적합성 판단**     
: 여러 후보가 있다면, 실제 호출할 때 명시적으로 값을 넣어준 인수를 가지고 후보들 중 가장 적합한 것을 찾습니다.
 이 때, 선택적 파라미터에 대한 적합성은 고려하지 않습니다.

```cs
void Process(int val);                     
void Process(short val, int opt = 0);      
void Process(long val);                    
```
 `Process(10)`과 같이 호출이 되었다면,   
 int->int로의 변환이 가장 **선호되는** 변환이기 때문에, 이 경우에 첫번째 방식이 선택됩니다.   

3. **두 후보가 동등한 적합성을 가짐**        
: 만약 두 개 이상의 후보가 동일한 우선 순위를 가진다면,     
 우선권은 **호출에서 인수가 생략된 선택적 파라미터가 없는** 후보에게 있습니다.

 이는 선택적 파라미터가 아예 없는 메서드가 우선순위를 가지고,    
 선택적 파라미터의 경우에도 명시적으로 인수를 제공하여 호출한 메서드가 우선권을 갖는다는 의미입니다.    

```cs
void Calculate(int x, int y);                     
void Calculate(int x, int y, int z = 0);          
```
 위와 같은 경우에서 `Calculate(10,20)`과 같이 호출이 되었다면,    
 선택적 파라미터가 없는 첫번째 방식이 선택됩니다.    

```cs
void Calculate(int x, int y, int z = 0);
void Calculate(int x, int y, short z = 0); 
```
 위와 같은 경우에서 `Calculate(10,20)`과 같이 호출이 되었다면,    
 컴파일러는 상황을 모호하다 판단해 컴파일 오류를 발생시킵니다.     

 또한 `Calculate(10,20,10)`과 같이 호출이 되었다면,     
 두 번째 규칙과 유사하게 판단하여 (int->int 가 가장 선호되는 방식) 첫번째 방식이 선택됩니다.    

# 반환 값
메서드는 호출자에 값을 반환할 수 있습니다.  

- 반환 타입이 `void`가 아니면 메서드는  `return` 키워드를 사용하여 값을 반환할 수 있습니다.     

- `return` 키워드와 그 뒤에 반환 타입과 일치하는 객체로 메서드 호출자에 해당 값을 반환합니다. 

- `void`가 아닌 메서드의 경우 반환 값을 반드시 반환할 필요가 있습니다.      

- `return` 키워드는 메서드 실행을 중지합니다.       
반환 타입이 `void`이면 값이 없는 `return` 문을 사용하여 메서드 실행을 중지할 수 있습니다.      
`return` 키워드가 없으면 메서드는 코드 블록 끝에 도달할 때 실행을 중지합니다.

- 메서드는 `return`을 사용해 블럭 형식의 본문 내용을 작성하는 방법이 있고,    
식 형식의 본문 내용을 작성하는 방법도 있습니다.   
```cs
public int AddTwoNumbers(int number1, int number2)
{
        return number1 + number2;
}

public int AddTwoNumbersExperssion(int number1, int number2) => number1 + number2;
``` 

- 메서드는 반환된 값을 변수에 할당할 수 있으며, 메서드 호출 자체를 또 다른 메서드의 인자로 사용할 수 있습니다.

- 메서드에서 둘 이상의 값을 반환하는 경우도 있습니다.
_튜플_ 또는 _튜플 리터럴_ 을 사용하여 여러 값을 반환합니다.
튜플은 **튜플 요소의 타입만을 정의**하는 것이고, 튜플 리터럴은 **튜플 요소의 이름을 지정하는 형식**이라 생각하면 됩니다.

```cs
public (string, string, string, int) GetPersonalInfo1(string id)
{
    PersonInfo per = PersonInfo.RetrieveInfoById(id);
    return (per.FirstName, per.MiddleName, per.LastName, per.Age);
}
// 튜플으로 반환 (string, string, string, int)

public (string FName, string MName, string LName, int Age) GetPersonalInfo2(string id)
{
    PersonInfo per = PersonInfo.RetrieveInfoById(id);
    return (per.FirstName, per.MiddleName, per.LastName, per.Age);
}
// 튜플 리터럴로 반환 (string FName, string MName, string LName, int Age)
```
위와 같이 반환 타입에 대해 정의할 때 차이가 나며,

```cs
var person = GetPersonalInfo1("111111111");
Console.WriteLine($"{person.Item1} {person.Item3}: age = {person.Item4}");

var person = GetPersonalInfo("111111111");
Console.WriteLine($"{person.FName} {person.LName}: age = {person.Age}");
```
사용 시 튜플은 `Item`이라는 명칭에 차례대로 숫자가 붙고,     
튜플 리터럴은 작성한 명칭으로 선언된다는 것을 확인할 수 있습니다.   

- 메서드가 배열을 파라미터로 사용하고, 해당 배열의 내부 값을 수정하는 경우,
작업 후 메서드가 배열을 반환할 필요가 없습니다.
배열의 포인터를 참조 값으로 하여 전달하고, 이 때문에 원본에 접근하는 개념이기 때문에
파라미터로 받은 배열을 수정하는 것이 원본을 수정하는 것과 같기 때문입니다.

 # 확장 메서드
 기존 형식에 메서드를 추가하는 방법에는 크게 2가지가 있습니닫.    
 - 해당 형식에 대한 소스 코드 수정      
: 원본을 수정할 때, `private` 데이터 필드를 추가하면 호환성이 깨질 수 있습니다. 
 - 파생 클래스에서 새로운 메서드 정의        
: 확장 멤버를 사용하면 형식 자체를 수정하지 않고도 새로운 메서드를 추가할 수 있습니다.   
: 구조체, 열거형의 상속, `sealed` 클래스에느 이러한 방식으로 추가할 수 없습니다.

# 비동기 메서드
비동기 기능을 사용하면, 명시적으로 콜백을 사용하거나 복잡한 코드 분할 과정 없이도 비동기 메서드를 호출할 수 있습니다.    

`async`를 추가하여 메서드를 표시하는 경우 `await`를 사용할 수 있습니다.    
비동기 메서드 내에서 `await` 식에 도달하면 대기 중인 작업이 완료되지 않은 경우, 메서드의 진행이 중단되게 할 수 있습니다.    

비동기 메서드는 보통 반환 타입이 `Task`, `Task<T>`, `IAsyncEnumberable<T>`, `void` 입니다.   
`void`의 경우 일반적인 비동기 메서드에는 사용되지 않으며, 이벤트 처리기를 정의하는 데 사용됩니다.   

```cs
class Program
{
    static Task Main() => DoSomethingAsync();

    static async Task DoSomethingAsync()
    {
        Task<int> delayTask = DelayAsync();
        int result = await delayTask;

        // 위의 두 줄은 아래 코드로 간소화해서 작성 가능합니다. 
        //int result = await DelayAsync();

        Console.WriteLine($"Result: {result}");
    }

    static async Task<int> DelayAsync()
    {
        await Task.Delay(100);
        return 5;
    }
}
```
위와 같이 반환 타입이 존재하는 경우는 `Task<T>`, 그렇지 않은 경우 `Task`로 작성합니다.   

비동기 메서드는 `in`, `ref`, `out`와 같은 참조 매개변수를 선언할 수 없지만,     
이러한 매개변수가 있는 메서드를 호출은 가능합니다.

# 식 본문 구문
메서드 정의는 표현식의 결과와 함께 즉시 반환되거나 메서드의 본문으로 단일 문이 있는 경우가 일반적입니다.      
`=>` 를 사용하여 이러한 메서드를 정의하는 구문 바로 가기 형태를 만들 수 있습니다.    

코드가 짧고 단순할 경우, 일반적인 중괄호 `{}` 방식 대신 `=>` 연산자를 사용해 간결하게 작성할 수 있습니다.
```cs
public Point Move(int dx, int dy) => new Point(x + dx, y + dy);
public void Print() => Console.WriteLine(First + " " + Last);
public static Complex operator +(Complex a, Complex b) => a.Add(b);
public string Name => First + " " + Last;
public Customer this[long id] => store.LookupCustomer(id);
```
`void`와 `async`의 경우, 표현식문만 허용됩니다. (단순히 실행되는 한 줄 짜리 문장을 의미)

# 이터레이터
이터레이터는 배열과 리스트 같은 컬렉션에 대해 반복 작업을 수행합니다.          
`yield return`문을 통해 각 요소를 한 번에 하나씩 반환합니다.    
`yield return`문에 도달하면 호출자가 시퀀스의 다음 요소를 요청할 수 있돌고 현재 위치가 기억됩니다.  

반환 타입은 `IEnumerable`, `IEnumerable<T>`, `IAsyncEnumberable<T>`, `IEnumerator`, `IEnumerator<T>`일 수 있습니다.   
