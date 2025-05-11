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

```cs
List<int> numbers = new List<int> { 1, 2, 3, 4 };

IEnumerable<int> asEnumerable = numbers.AsEnumerable();
```

# AsQueryable
