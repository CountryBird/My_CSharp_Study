# 쿼리 식
쿼리 식은 SQL이나 XQuery와 비슷한 형태의 구문을 사용하여
`System.Collections.Generic.IEnumerable<T>` 컬렉션을 쿼리할 수 있습니다.     

컴파일 시간에 쿼리 문은 메서드 형태로 변환되어 호출됩니다.
```cs
var query = from str in stringArray
            group str by str[0] into stringGroup
            orderby stringGroup.Key
            select stringGroup;
```

# 암시적으로 형식화된 변수
`var` 한정자를 사용하여 컴파일러에게 형식에 대한 부분을 맡겨 유추하여 할당되도록 할 수 있습니다.     

`var`로 선언된 변수는 단지 시작 타입을 암시적으로 선언하는 것이기 때문에,       
여전히 강력한 타입으로 선언되어 타입을 변할 수 없습니다.  

# 객체 및 컬렉션 이니셜라이저
객체 및 컬렉션 이니셜라이저를 사용하여 객체에 대한 생성자를 직접 호출하지 않고도 객체를 초기화할 수 있습니다.      
```cs
var stud = new Student { Id = 1, Name = "Alice" };
```
기본 생성자를 호출하고 이후에 값을 넣는 개념이기 때문에      
해당 클래스에서 생성자를 별도로 선언했다면 제대로 작동하지 않을 수도 있습니다.    

이러한 객체 및 컬렉션 이니셜라이저를 새로운 객체를 LINQ에도 사용할 수 있습니다.    
```cs
var id5Stu = from s in students
         where s.Id > 5
         select new Student { Id = s.Id-5,Name=s.Name};
```

# 익명 형식
따로 객체의 타입을 지정하지 않는 형태를 사용해, 익명 형식을 LINQ 쿼리에서 사용할 수 있습니다.    
```cs
var query = from s in students
         select new { Id = s.Id,Name=s.Name};
```

# 확장형 메서드
_확장 메서드_ 는 타입 객체에 직접적으로 연결되어 사용할 수 있습니다.    
때문에 타입에 구현된 메서드인 것처럼 호출할 수 있습니다.    
다만 실질적으로는 `IEnumerable<T>`에 구현된 형태입니다.   
```cs
var query = students.OrderBy(student => student.Id);
```

# 람다 식
LINQ에서 `Where`, `Select`, `OrderBy`와 같은 메서드는 주로 람다 표현식을 사용합니다.    
이를 통해 간결하게 조건을 설정할 수 있습니다.    
```cs
int[] numbers = { 1, 2, 3, 4, 5 };
var evenNumbers = numbers.Where(n => n % 2 == 0);
```

# 데이터로서의 식
쿼리는 결과 컬렉션을 저장하는 형식이 아니라, 결과를 생성하는 단계를 저장하는 형식입니다.     
때문에 쿼리를 반환하는 메서드가 있는 경우, `OrderBy()`, `Where()`과 같은 방식을 사용해 추가적인 쿼리 처리를 할 수 있습니다.   

다만, 쿼리를 `ToList()`, `ToArray()`와 같은 방식으로 변환한다면 당연히 쿼리 처리가 불가능합니다.    
```cs
IEnumerable<string> IntToStringQuery1(int[] ints)
=> from i in ints
select i.ToString();

void IntToStringQuery2(int[] ints, out IEnumerable<string> strings)
=> strings = from i in ints
         select i.ToString();
```
`IEnumerable<T>`를 구현하는 방식을 사용하기도 하고, `out` 매개변수를 사용하여 쿼리를 외부로 보내는 방식도 사용합니다.   

```cs
int[] ints = new int[] { 1, 2, 3 };
var query1 = IntToStringQuery1(ints);

var query2 = from i in query1
             orderby i[0]
             select i;
```
특정 메서드의 반환값으로써 나온 쿼리에 추가적인 작업이 가능합니다.     
