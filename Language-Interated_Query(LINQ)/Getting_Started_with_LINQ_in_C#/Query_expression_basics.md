# 쿼리란 무엇이며, 무엇을 하는 것일까?
_쿼리_ 는, 데이터 소스에서 검색/반환된 데이터가 가져야 하는 형태, 조직을 지정하는 개념입니다.    
쿼리를 통한 결과랑은 엄연히 다른 개념입니다.    

일반적으로, 데이터는 논리적으로 같은 종류의 시퀀스로 구성되기에,     
애플리케이션은 `IEnumerable<T>`, `IQueryable<T>`로 통일하여 보고 T만을 변환시켜 사용하는 형태로 인식합니다.     
예를 들어, LINQ to XML과 같은 경우 `IEnumerable<XElement>`로 봅니다.     

소스 시퀀스에서, 쿼리는 크게 3가지 중 하나를 수행할 수 있습니다.    

**- 하위 집합을 검색만 하는 형태**        
: 단순히 정렬, 검색만을 하여 개별 요소를 수정하지 않는 형태입니다. 
```cs
IEnumerable<int> highScoresQuery =
    from score in scores
    where score > 80
    orderby score descending
    select score;
```

**- 하위 집합을 검색하여 새 형식으로 변환하는 형태**     
: 이전과 같이 요소 시퀀스를 검색한 후, 데이터 소스에는 없는 새로운 형태로 변환시키는 형태입니다.    
이러한 방법을 이용해, int의 결과를 string으로 바꾸는 등의 쿼리도 작성할 수 있습니다.  
```cs
IEnumerable<string> highScoresQuery2 =
    from score in scores
    where score > 80
    orderby score descending
    select $"The score is {score}";
```

**- 원본 데이터에 대한 특정 값을 검색하는 형태**      
: 또 다른 시퀀스로 반환되는 다른 형태와 다르게, 개수, 최대/최소값과 같은 데이터에 관한 특정 값을 검색하는 형태입니다.     
```cs
IEnumerable<int> highScoresQuery3 =
    from score in scores
    where score > 80
    select score;

var scoreCount = highScoresQuery3.Count();
```

# 쿼리 식이란?
쿼리 식은 C#의 _일급 언어 요소_ 로 취급됩니다.      
이는 다른 기본적인 C# 객체와 동일하게 취급되어, 변수에 할당하거나 인자로 전달하는 등의 동작을 할 수 있다는 것을 의미합니다.      

쿼리 식은 `from`으로 시작해야 하며, `select` 또는 `group` 절로 끝나야 합니다.   
이 사이에는 `where`, `orderby`, `join`, `let` 또는 다른 `from` 절이 올 수 있습니다.     
또한 `into` 키워드를 사용하여 중간 결과를 이어서 사용할 수 있게 할 수 있습니다.    

```cs
 class Student
 {
     public int grade;
     public string? name;

     public Student(int grade, string? name)
     {
         this.grade = grade;
         this.name = name;
     }
 }
```
```cs
var students = new Student[]{
   new Student(1,"A"), new Student(1,"B"),new Student(2,"C")};

var query = from s in students
            group s by s.grade into grade
            where grade.Count() > 1
            select grade;

foreach (var gradeGroup in query) // 그룹화된 형태로 나오기 때문에 
{
    foreach (var student in gradeGroup) // 세부 내용을 보기 위해서는 한 번 더 처리가 필요
    {
        Console.WriteLine(student.name);
    }
}
```
위와 같이 이전에 그룹화한 결과에 추가적인 처리가 가능하게 할 수 있습니다.    

## 쿼리 변수
LINQ에서 쿼리 변수는 쿼리의 결과 대신 쿼리를 저장하는 개념의 변수입니다.    
정확히는, IEnumerable 타입이며 `foreach`문이나 `IEnumerator.MoveNext()`를 통해 반복할 때 요소의 시퀀스를 생성하는 형태입니다.   

쿼리의 결과가 쿼리 변수에 저장되는 개념이 아니고, 다른 반복 변수 등을 통해 반환되기 때문에,     
쿼리 변수는 반복하여 사용할 수 있습니다.   

```cs
var students = new Student[]{
   new Student(1,"A"), new Student(1,"B"),new Student(2,"C")};

var query1 = from student in students
             where student.grade == 1
             select student;

foreach (var student in query1)
{
    Console.WriteLine(student.name);
}

var query2 = students.Where(student => student.grade == 1);
foreach (var student in query2)
{
    Console.WriteLine(student.name);
}
```
데이터 소스나 쿼리의 내용이 바뀌지 않는 한, 결과는 항상 같습니다.    

### 쿼리 변수의 입력
쿼리 변수는 `IEnumerable<T>`의 형태로 명시적으로 사용하거나,     
`var`의 형태로 암시적인 형태로 사용할 수 있습니다.    

## 쿼리 식 시작
