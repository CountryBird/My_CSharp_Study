_변환 메서드_ 는 입력 개체의 타입을 변경합니다.   

변환 메서드 중 일부는 `As` 또는 `To`로 시작하는 형식을 가지고 있습니다. 

`As`로 시작하는 메서드는 정적 타입만을 변환하지, 컬렉션을 열거하지는 않습니다. 
때문에 지연 실행되는 형식이 됩니다. (중간에 원본 컬렉션이 변화되는 경우 결과 컬렉션도 변화)

`To`로 시작하는 메서드는 컬렉션을 즉시 열거하여, 새로운 컬렉션에 복사합니다.       
때문에 즉시 실행되는 형식이 됩니다.   

```cs
List<int> original = new List<int> { 1, 2, 3, 4 };

var asMethod = original.AsEnumerable();
var toMethod = original.ToList();

original.Add(5);

foreach (var a in asMethod) Console.Write($"{a} ");
// 1 2 3 4 5

Console.WriteLine();

foreach (var t in toMethod) Console.Write($"{t} ");
// 1 2 3 4
```

# AsEnumerable
`AsEnumerable` 메서드는 입력된 타입을 `IEnumerable<T>`로 변환합니다.       
해당 메서드를 통해 배열, List와 같은 타입을 명시적으로 `IEnumerable` 타입으로 변환할 수 있습니다.     
메서드 방식에서만 지원하며, 쿼리 방식에서는 사용할 수 없습니다.   
```cs
List<int> numbers = new List<int> { 1, 2, 3, 4 };

IEnumerable<int> asEnumerable = numbers.AsEnumerable();
```

# AsQueryable
`AsQueryable` 메서드는 입력된 타입을 `IQueryable<T>`로 변환합니다.       
해당 메서드를 통해 배열, List와 같은 타입을 명시적으로 `IQueryable` 타입으로 변환할 수 있습니다.   
메서드 방식에서만 지원하며, 쿼리 방식에서는 사용할 수 없습니다.
```cs
List<int> numbers = new List<int> { 1, 2, 3, 4 };

IQueryable<int> asQueryable = numbers.AsQueryable();
```

# Cast
`Cast` 메서드는 비제너릭 컬렉션인 타입을 지정한 제네릭 타입의 `IEnumerable` 컬렉션으로 변환합니다.   
메서드 방식에서 `Cast<T>()`와 같은 방식으로 사용하며,    
쿼리 방식에서는 _변수에 명시적으로 타입을 적어_ 이와 같은 기능을 수행할 수 있습니다.    
```cs
ArrayList numbers = new ArrayList { 1, 2, 3, 4 };

IEnumerable<int> queryStyle = from int num in numbers select num;
IEnumerable<int> methodStyle = numbers.Cast<int>();
```
만약 입력 컬렉션이 지정한 타입으로 이루어지지 않은 경우 예외가 발생할 수 있습니다.   

# OfType
`OfType` 메서드는 `Cast`와 비슷한 역할을 하지만,       
입력 컬렉션이 지정한 타입으로 이루어지지 않았을 경우 예외를 발생시키지 않고, 무시한다는 차이가 있습니다.      
메서드 방식에서만 지원하며, 쿼리 방식에서는 사용할 수 없습니다.  
```cs
ArrayList numbers = new ArrayList { 1, "Two", 3, "Four", 5 };

IEnumerable<int> cast = numbers.Cast<int>();
IEnumerable<int> ofType = numbers.OfType<int>();
// 지연 실행 방식이라 해당 단계에서 예외 발생 X

foreach(int c in cast)
{
  Console.Write($"{c} "); // 예외 발생 ("Two", "Four")
}

foreach(int o in ofType)
{
  Console.Write($"{o} "); // 정상 실행
  // 1 3 5
}
````

# ToList
`ToList` 메서드는 지연 실행된 쿼리의 결과를 즉시 평가하고,        
리스트 형식으로 변환할 때 사용하는 메서드입니다.       
메서드 방식에서만 지원하며, 쿼리 방식에서는 사용할 수 없습니다.   
```cs
IEnumerable<int> number = Enumerable.Range(1, 5);
List<int> numberList = number.ToList();
```

# ToArray 
`ToArray` 메서드는 지연 실행된 쿼리의 결과를 즉시 평가하고,     
배열 형식으로 변환할 때 사용하는 메서드입니다.      
메서드 방식에서만 지원하며, 쿼리 방식에서는 사용할 수 없습니다.    
```cs
IEnumerable<int> number = Enumerable.Range(1, 5);
int[] numberArr = number.ToArray();
```

# ToDictionary 
`ToDictionary` 메서드는 지연 실행된 쿼리의 결과를 즉시 평가하고,
딕셔너리 형식으로 변환할 때 사용하는 메서드입니다.       
메서드 방식에서만 지원하며, 쿼리 방식에서는 사용할 수 없습니다.         

`ToDictionary` 메서드는 (Key, Element)와 같은 형식을 통해    
각 요소에서 키와 값이 무엇인지 지정해야 합니다.      

Dictionary의 특성 상, 입력에 키로 사용하고자 하는 값에 중복이 있으면 사용이 불가능합니다.     
```cs
var people = new[]
{
  new { Id = 1, Name = "Alice" },
  new { Id = 2, Name = "Bob" }
};
Dictionary<int, string> dict = people.ToDictionary(p => p.Id, p => p.Name);
```

# ToLookup
`ToLookup` 메서드는 `ToDictionary`와 비슷한 역할을 하지만,    
입력에 키로 사용하고자 하는 값에 중복이 있으면 예외를 발생시키지 않고, 값을 그룹으로 저장한다는 차이가 있습니다.     
메서드 방식에서만 지원하며, 쿼리 방식에서는 사용할 수 없습니다.     
```cs
var people = new[]
{
  new { Id = 1, Name = "Alice" },
  new { Id = 2, Name = "Bob" }
};

Dictionary<int, string> dict = people.ToDictionary(p => p.Id, p => p.Name); // 예외 발생
ILookup<int, string> lookup = people.ToLookup(p => p.Id, p => p.Name); // 정상 실행

foreach (var l in lookup)
{
  Console.WriteLine(l.Key);
  foreach(var name in l)
  {
    Console.Write($"{name} ");  
  }
  Console.WriteLine();
}

// 1
// Alice Alice?
// 2
// Bob
```
