_쿼리_ 는 데이터 소스에서 데이터를 검색하는 식입니다.     
데이터 소스는 SQL, XML 등의 각자의 고유한 쿼리 형식이 있기 때문에 개발자의 입장에서는 이를 위한 학습이 필요합니다.    
LINQ는 이러한 과정을 간소화하는 역할을 합니다.     

C# 객체를 사용할 수 있으며,     
같은 방법으로 하여금 XML, SQL 등의 데이터를 사용할 수 있도록 지원합니다.

# 쿼리 작업의 3가지 과정
모든 LINQ 쿼리 작업는 3가지 과정을 거칩니다.       

1. 데이터 소스 가져오기
2. 쿼리 만들기
3. 쿼리 실행하기

```cs
// 1. 데이터 소스 가져오기
int[] numbers = [1, 2, 3, 4, 5, 6];

// 2. 쿼리 생성하기
var numQuery = from num in numbers
               where (num%2) == 0
               select num;

// 3. 쿼리 실행하기
foreach (var num in numQuery)
{
    Console.Write("{0} ", num);
}
```

![image](https://github.com/user-attachments/assets/9ea73bf4-daba-4f8e-84d6-690a4a375fe1)       
**중요한 포인트는, 쿼리 선언과 쿼리 실행이 동시에 일어나지 않는다는 것입니다.**

# 데이터 소스
LINQ로 사용하는 데이터 소스는 `IEnumerable<T>`를 지원하는 형태입니다.      
쿼리가 `foreach`문에서 실행되고, `foreach`문은 `IEnumerable` 또는 `IEnumerable<T>`이 필요합니다.      
`IEnmerable<T>` 또는 `IQueryable<T>` 같은 인터페이스를 지원하는 형식을 _쿼리 가능 형식_ 이라고 합니다.      

쿼리 가능 형식은 LINQ 데이터 소스로 사용하기 위해 별도의 처리가 필요 없습니다.     
예를 들어 LINQ to XML과 같은 경우 XElement 형식을 사용해 로드할 수 있습니다.
```cs
XElement booksXml = XElement.Load("sample.xml");

var books = from book in booksXml.Elements("Book")
            where (string)book.Element("Author") == "George Orwell"
            select new
            {
                Title = book.Element("Title")?.Value,
                Author = book.Element("Author")?.Value
            };

foreach (var book in books)
{
    Console.WriteLine($"Title: {book.Title}, Author: {book.Author}");
}
```

`EntityFramework`를 사용하여 C# 클래스와 데이터베이스 스키마 간에 개체 관계형 매핑을 만듭니다.     
객체에 대한 쿼리를 작성하면 런타임에 EntityFramework가 데이터베이스와의 통신을 별도 처리하는 방식입니다.    

# 쿼리
쿼리는 데이터 소스 또는 소스에서 검색할 정보를 지정합니다.       
선택적으로 쿼리는 정렬, 그룹화에 대한 방법도 지정할 수 있습니다.      

쿼리 식에는 크게 `from`, `where`, `select`와 같은 형태의 3가지 절로 나눌 수 있습니다.    
(SQL과 반대되는 형태를 띕니다.)      
- `from`: 데이터를 가져올 출처를 지정할 수 있습니다.
- `where`: 조건을 통해서 필터를 적용할 수 있습니다.
- `select`: 어떤 데이터를 선택해 반환할지 지정할 수 있습니다.

# 실행 방식에 따른 표준 쿼리 연산자 분류
LINQ to Objects 구현은 즉시 실행, 지연 실행으로 2가지 형식이 있습니다.     
지연 실행은 또 _스트리밍_, _비스트리밍_ 의 범주로 구분할 수 있습니다.    

## 즉시 실행
즉시 실행은 **데이터 소스를 읽고 작업이 한 번 수행됨**을 의미합니다.    

주로 스칼라 결과를 반환하는 연산자는 _즉시 실행_ 되는 형태입니다.    
또한 이러한 연산은 결과를 반환하기 위해 `foreach`를 사용할 필요가 있기 때문에,     
암묵적으로 `foreach`문이 실행됩니다.    
```cs
int[] numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9];

var evenNum = from num in numbers
              where num % 2 == 0
              select num;

 int evenCount = evenNum.Count();
```

`ToList`, `ToArray`를 사용하면,      
스칼라 반환 연산자가 아니더라도, 모든 쿼리가 즉시 실행되는 형태로 바꿀 수도 있습니다.    
쿼리 식 바로 뒤에 배치함으로써 강제로 실행할 수 있습니다.    
```cs
int[] numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9];

List<int> oddNum = (from num in numbers
              where num % 2 != 0
              select num).ToList();
```

## 지연 실행
지연 실행은 **코드의 쿼리가 선언되는 시점에서 작업이 수행되지 않음**을 의미합니다.    
예로, `foreach`문에 쿼리 변수가 열거되는 경우를 말할 수 있습니다.    

`IEnumerable<T>`, `IOrderedEnumerable<TElement>`가 반환 형식인 경우 대부분 지연 방식으로 실행됩니다.    

지연 실행은 쿼리 결과가 반복될 때마다 데이터 소스에서 업데이트된 데이터를 가져오는 형태이기 때문에,      
쿼리 재사용이 가능합니다.    

### 스트리밍
요소를 생성하기 전에 모든 데이터를 읽지 않습니다.     
데이터를 하나씩 읽고, 처리, 반환(yield)하는 형태입니다.    
결과를 만들기 위해 필요한 만큼만 읽는 개념이라 생각하면 됩니다.     

`Where`, `Select` 등이 이에 속합니다.  
```cs
var result = numbers.Where(n => n % 2 == 0).Select(n => n * 10);
```
### 비스트리밍
요소를 생성하기 위해 모든 데이터를 읽어야 합니다.     
정렬과 같이 모든 데이터가 필요한 연산은 이러한 형태를 띕니다.    

`OrderBy` 등이 이에 속합니다.    
```cs
var result = numbers.OrderBy(n => n);
```

# LINQ to Objects
"LINQ to Object"는 IEnuerable 또는 IEnumerable\<T\>을 상속하는 컬렉션을 LINQ 쿼리로 사용하는 방법입니다.     
메모리 컬렉션에 LINQ를 사용하는 형태를 의미하며,     
흔히 _LINQ_ 하면 생각나는 형태가 _LINQ to Object_ 를 의미합니다.    

`foreach`를 통한 루프에 비해서 LINQ는 다음과 같은 장점을 가집니다.     
- 코드보다는 쿼리와 가까운 형태이기 때문에 보다 간결하고 쉽게 읽을 수 있습니다.
- 최소한의 코드로도 강력한 필터링, 순서 지정, 그룹화 기능을 사용할 수 있습니다.
- 다른 데이터 소스에 대해 같은 방식을 사용하려고 할 때, 수정이 거의 필요 없습니다.

# 쿼리 결과를 메모리에 저장
쿼리는 기본적으로 데이터를 검색하고 구성하는 방법에 대한 _명령_ 의 개념이기 때문에,      
`foreach`나 특정 메서드를 통하기 전까지는 지연 실행됩니다.     

`foreach`를 통해 결과를 반복하여 액세스하거나,     
`ToList`,`ToArray`,`ToDictionary`,`ToLookup` 등으로 통해 결과를 반환합니다.    

```cs
int[] numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9];

IEnumerable<int> oddNums = from nums in numbers
                          where nums % 2 != 0
                          select nums;

var oddNumsList = oddNums.ToList();
for (int i = 0; i < oddNumsList.Count; i++)
{
   Console.Write($"{oddNumsList[i]} ");
}
```
