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
