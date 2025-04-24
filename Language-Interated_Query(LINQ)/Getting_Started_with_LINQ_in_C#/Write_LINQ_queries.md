C# 컴파일러는 쿼리 구문을 메서드 호출 방식으로 내부적으로 바꿔 실행합니다.     
때문에 _쿼리 방식_ 과 _메서드 호출 방식_ 은 형태만 다를 뿐, 같은 효과를 같습니다.    

쿼리 방식의 경우 직관적으로 연산을 파악할 수 있기 때문에,     
가독성이 좋으며 초보자들이 사용하기 좋다는 장점이 있습니다.    

다만, 일부 기능들은 메서드 호출 방식으로만 사용 가능한데,     
`Count`, `Max`, `Average`, `FirstOrDefault` 등이 이러한 경우입니다.   

이러한 이유 때문에, LINQ를 제대로 사용하기 위해서는      
쿼리 방식과 메서드 호출 방식 둘 다 익숙해질 필요가 있습니다.     

# 표준 쿼리 연산자 확장 메서드
`IEnumerable<T>`는 C#에서 컬렉션이나 데이터 시퀀스를 나타내는 인터페이스입니다.     
예로, 배열과 리스트가 이 인터페이스를 구현합니다.    

LINQ는 `IEnumerable<T>`에 대해 다양한 쿼리 연산을 지원하는데,     
`Where`, `Select`, `OrderBy`가 대표적인 예입니다.     
기본적으로 `IEnumerable<T>`가 해당 메서드들을 지원하지는 않지만, 
`System.Linq` 네임스페이스의  _확장 메서드_ 개념을 통해 해당 메서드를 사용할 수 있습니다.    

# 람다 식
메서드 호출 방식 LINQ에서, 람다 식을 사용할 수 있습니다.    

람다 표현식에서 파라미터의 타입은 명시적으로 지정해주지 않아도,       
`IEnumerable<T>` 타입의 정보 덕에 컴파일러가 타입을 자동으로 추론합니다.    
이로 인해 코드가 간결해지고, 타입을 일일이 명시할 필요가 없어집니다.    
```cs
int[] numbers = { 1, 2, 3, 4, 5, 6, 7, 8, 9, 10 };

IEnumerable<int> query = numbers.Where( num => num % 2 == 0);
```

# 쿼리 결합
쿼리가 `IEnumerable`의 형태로 구성되어 있기 때문에,     
한 쿼리문의 결과를 _체이닝_ 하여 또 다른 쿼리문의 입력으로써 사용할 수 있습니다.     

예로 위의 코드의 결과를 역순으로 정렬하고자 할 때,       
`OrderByDescending` 메서드를 .으로 연결해 이어나갈 수 있습니다.   
```cs
IEnumerable<int> query = numbers.Where( num => num % 2 == 0).OrderByDescending(num=>num);
```

# 예제 - 쿼리 구문
대부분의 LINQ 쿼리는 쿼리 구문을 사용하여 쿼리 식을 만듭니다.    

첫번째 예제는 `where`절을 사용해 조건을 적용하는 방법을 보여 줍니다.   
```cs
IEnumerable<int> query1 = from num in numbers
                          where num is < 3 or > 8
                          select num;
```

두번째 예제는 위의 쿼리에 역순 정렬에 대한 부분을 추가합니다.  
```cs
IEnumerable<int> query2 = from num in numbers
                          where num is < 3 or > 8
                          orderby num descending
                          select num;
```

세번째 예제는 키에 따라 결과를 그룹화하는 방법을 보여줍니다.    
```cs
string[] groupingQuery = ["carrots", "cabbage", "broccoli", "beans", "barley"];
IEnumerable<IGrouping<char, string>> queryFoodGroups =
    from item in groupingQuery
    group item by item[0];
```

쿼리의 타입은 `IEnumerable<T>`의 형태를 띄며, 이러한 모든 쿼리는 `var` 키워드를 사용해 간단히 나타낼 수 있습니다. 

# 예제 - 메서드 구문
일부 쿼리 작업은 메서드 호출만으로 표현해야 합니다.     
가장 일반적으로, `Sum`, `Max`, `Min`, `Average`와 같은 싱글톤 숫자 값을 반환하는 메서드가 있습니다.     

**단일 값으로 반환되기 때문에 추가 쿼리 작업에 사용할 수 없고, 항상 모든 쿼리에서 마지막으로 호출되어야 합니다.**

다음 두 예제는 각각 _평균에 대한 값_, _병합_ 을 위한 메서드입니다. 
```cs
List<int> numbers1 = [ 5, 4, 1, 3, 9, 8, 6, 7, 2, 0 ];
List<int> numbers2 = [ 15, 14, 11, 13, 19, 18, 16, 17, 12, 10 ];

double average = numbers1.Average();
IEnumerable<int> concatList = numbers1.Concat(numbers2);
```

일부 메서드들은, 파라미터로 `System.Action` 또는 `System.Func`를 요구하는데,   
이러한 경우 람다 식을 사용합니다.
```cs
IEnumerable<int> greaterThan6 = numbers1.Where(num => num > 6);
```

단일 값을 반환하는 메서드의 경우에는 즉시 실행됩니다.   
또한 메서드 구문도 `var` 키워드를 사용해 암시적 형식을 사용할 수 있습니다.   

# 예제 - 쿼리/메서드 혼합 구문
쿼리 절의 결과에서 메서드 구문을 사용하는 등의 방식으로, 두 구문을 혼합하여 사용할 수 있습니다.   
```cs
int numCount1 = (from num in numbers1
                where num is < 3 or > 7
                select num).Count();
```
해당 식은 마지막에 단일 값을 반환하기 때문에, 쿼리가 즉시 실행됩니다.    
물론, 메서드 구문만으로도 같은 역할을 하는 식을 작성할 수 있습니다.     
```cs
int numCount2 = numbers1.Count(num => num is < 3 or > 7);
```

# 런타임에 동적으로 조건자 필터 지정
경우에 따라 `where`의 조건으로써 적용해야 하는 조건자 수를 설정하기 까다로운 순간이 있을 수 있습니다.    
예로 id에 대한 조건을 걸고 싶은데 id가 너무 많거나, id가 런타임 중에 변하는 경우를 생각할 수 있습니다.   

이러한 경우 `Contains` 메서드를 사용하여 런타임 중 조건을 설정할 수 있습니다. 
```cs
class Person
{
    public int Id;
    public string Name;

    public Person(int id, string name)
    {
        Id = id;
        Name = name;
    }
}
```
```cs
Person[] people = { new Person(1, "A"), new Person(2, "B"), new Person(3, "C"), new Person(4, "D") };

int[] selectedId= [1, 4];
var query = from person in people
            where selectedId.Contains(person.Id)
            select person;

foreach(var p in query)
{
    Console.Write($"{p.Name} "); // A D
}
```
조건리스트.Contains(검사 값)의 형태, 즉 기준이 되는 집합이 앞으로 오는 형태를 띕니다.

```cs
selectedId = [2, 3, 4];
foreach (var p in query)
{
    Console.Write($"{p.Name} "); // B C D
}
```
쿼리문이 지연 실행된다는 점과 `Contains`를 사용해, 단순히 조건을 바꾸는 것만으로 다른 결과를 출력해 낼 수 있습니다.    

---

`if... else`, `switch`와 같은 제어 흐름 문을 사용하는 것으로     
조건에 따른 다른 쿼리 실행을 할 수 있습니다.
```cs
int[] numbers = { 1, 2, 3, 4, 5, 6, 7, 8, 9, 10 };

Console.WriteLine("odd or even?");
int input = int.Parse(Console.ReadLine());

IEnumerable<int> oddOrEven = input == 0?
    (from num in numbers where num % 2 == 0 select num):
    (from num in numbers where num % 2 !=0 select num);

foreach (int num in oddOrEven)
{
    Console.Write($"{num} ");
}
```

# 쿼리 식의 Null 값 처리
데이터 소스가 `null`이거나 데이터 요소가 `null`인 경우,        
`IEnumerable<T>` 컬렉션에는 값이 null인 요소가 포함될 수 있습니다.    

이러한 경우 해당 데이터에 접근하면 `NullReferenceException`이 발생할 수 있기 때문에 별도의 처리가 필요합니다.
```cs
class MyClass
{
  public MyClass(int myInt)
  {
    this.myInt = myInt;
  }
}
```
```cs
MyClass[] myClasses = [new MyClass(1), new MyClass(2), null, new MyClass(3)];

IEnumerable<MyClass> query = from myClass in myClasses
                             where myClass != null // 없으면 사용 시 오류!
                             select myClass;
```

---

join 절에서 비교 키 중 하나만 _null 허용 값_ 인 경우, 다른 키를 null 허용 값으로 캐스팅해야 합니다.   
```cs
var query =
    from o in db.Orders
    join e in db.Employees
        on o.EmployeeID equals (int?)e.EmployeeID
    select new { o.OrderID, e.FirstName };
```
`o`의 `EmployeeID`는 `int?`이며, `e`의 `EmployeeID`는 `int` 입니다.     
`equals`와 `is`에서는 타입이 정확히 같아야 비교할 수 있기 때문에, 캐스팅 과정이 필요합니다.    

또한 LINQ에서는 is와 같은 문법 사용을 피하고 equals를 사용하는 것이 좋습니다.    
LINQ는 쿼리 프로바이더가 데이터 쿼리로 변환하는 것이지, 실제 C#으로 이루어진 코드는 아니기 때문에      
최신 C# 문법의 경우 제대로 해석되지 않을 가능성이 있습니다.    

# 쿼리 식의 예외 처리
`IEnumerble`이나 `IQueryable`의 형태라면, 메서드의 반환값도 데이터 소스로 사용될 수 있습니다.   
때문에, 예외를 일으키거나 데이터 소스를 수정하는 등의 위험 요소도 존재합니다.    

- 예외 처리는 쿼리 밖에서 하는 것이 안전합니다.      
LINQ 쿼리는 지연 실행되기 때문에 쿼리 내부에서 예외가 발생하면 처리하기 어렵고 예측도 어렵습니다.      
- 위험 요소를 지닌 코드를 쿼리에 넣으면      
LINQ의 원래 목적을 잃게 됩니다. (순수 함수형 스타일, 안전하고 읽기 쉬운 코드)


#### - 예외 발생 가능성이 있는 _데이터 소스_ 자체 예외 처리

```cs
IEnumerable<int> GetData() => throw new InvalidOperationException();
// 예외 발생 코드

// 잘못된 방식 - 예외가 쿼리 내부에서 발생함
var query = from i in GetData()  
            select i * i;
```

```cs
// 올바르게 수정된 방식 - 예외를 미리 처리
IEnumerable<int>? dataSource = null;
try
{
    dataSource = GetData();
}
catch (InvalidOperationException)
{
    Console.WriteLine("Invalid operation");
}

if (dataSource is not null)
{
    var query = from i in dataSource
                select i * i; 

    foreach (var i in query)
    {
        Console.WriteLine(i.ToString());
    }
}
```
데이터 소스를 가져오는 과정에서 예외가 발생할 가능성이 있다면,     
쿼리 정의 전에 예외를 처리해주는 것이 바람직합니다.    

#### - 예외 발생 가능성이 있는 _메서드를 포함_ 한 쿼리 예외 처리
쿼리 내의 메서드에서 예외가 발생할 가능성이 있다면,     
쿼리 정의 시점이 아닌 실행 시점에 예외를 처리해주는 것이 바람직합니다.    

```cs
string SomeMethodThatMightThrow(string s) =>
    s[4] == 'C' ?
        throw new InvalidOperationException() :
        $"""C:\newFolder\{s}""";
// 4번째 문자가 C인 경우 예외 발생
// 이외의 경우 정상 실행

string[] files = ["fileA.txt", "fileB.txt", "fileC.txt"];

var exceptionDemoQuery = from file in files
                         let n = SomeMethodThatMightThrow(file)
                         select n;
// 쿼리 정의 시점에는 처리 X

try
{
    foreach (var item in exceptionDemoQuery)
    {
        Console.WriteLine($"Processing {item}");
    }
}
catch (InvalidOperationException e)
{
    Console.WriteLine(e.Message);
}
// foreach나 .ToList(), .Count()와 같이 실행되는 순간에 처리
```
