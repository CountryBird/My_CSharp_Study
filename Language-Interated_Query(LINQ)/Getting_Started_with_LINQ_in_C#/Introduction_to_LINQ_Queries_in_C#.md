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
