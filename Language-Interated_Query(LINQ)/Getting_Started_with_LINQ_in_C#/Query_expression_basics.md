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
쿼리 식은 `from` 절로 시작해야 합니다.     
범위 변수를 통해 데이터 소스를 지정하며, 범위 변수는 시퀀스의 각 요소들을 의미합니다.  
```cs
IEnumerable<Country> countryAreaQuery =
    from country in countries
    where country.Area > 20 //sq km
    select country;

// country가 범위 변수
```
범위 변수는 쿼리가 세미콜론이나 연속 절로 종료되기 전까지 유효합니다.    

또한 쿼리 식에는, 여러 `from` 절이 포함될 수 있습니다.    

## 쿼리 식 종료
쿼리 식은 `group` 절 또는 `select` 절로 끝나야 합니다.    

### group 절
`group` 절을 사용하여 지정한 키로 구성된 그룹 시퀀스를 생성할 수 있습니다.     
`IGrouping<Key,Element>`의 형식으로 생성되며, 키와 요소는 어떤 데이터 형식이여도 사용 가능합니다.   
```cs
 var people = new Person[]
 { new Person("Alice"),new Person("Amy"),new Person("Bob"),new Person("Charlie"),
 new Person("Colin"),new Person("Daisy")};

 var groupPeople = from person in people
                   group person by person.Name[0];

 foreach (var group in groupPeople)
 {
     Console.WriteLine($"Name start with {group.Key}: ");
     foreach (var person in group)
     {
         Console.WriteLine($" {person.Name} ");
     }
 }
```
위의 코드는, Person 객체의 배열에서 가장 앞 글자를 기준으로 그룹화하는 코드이며       
그 앞 글자가 _Key_ 가 되고, 키에 따라 그룹화된 요소를 확인할 수 있습니다.     

### select 절
`select` 절을 사용하여 다른 모든 타입에 대한 시퀀스를 생성할 수 있습니다.     
간단한 select 절로는 데이터 소스와 같은 타입의 객체를 생성할 수 있고,      
추가적인 변환을 통해 아예 새로운 타입의 시퀀스를 생성할 수도 있습니다.       

위의 코드와 같은 people 배열이 있다고 가정했을 때,
```cs
var sortedPeople = from person in people
                  orderby person.Name
                  select person;
```
단순히 `select` 절을 사용하여 같은 타입의 시퀀스에 대한 간단한 연산 정도를 할 수도 있지만,   

```cs
 int Id = 1;
 var idPeople = from person in people
                select new
                {
                    Id = Id++,
                    Name = person.Name,
                };
```
원본 데이터를 바탕으로 새로운 타입의 시퀀스를 변환하여 사용할 수도 있습니다.    
물론 다음과 같은 경우에는 익명 타입을 생성하기 때문에 var 키워드가 필요합니다.    

### into를 통한 연속 사용
`select`, `group` 절에서, `into` 키워드를 사용해 임시 식별자를 만들 수 있습니다.     
select, group 사용 후 해당 데이터에 추가적인 쿼리를 작업하고자 할 때 유용하게 사용할 수 있습니다.    

예를 들어, 그룹화한 이후 해당 그룹에 대해 조건을 걸어 필터링을 하고 싶을 때와 같은 경우 into를 사용하면     
직관적인 LINQ 쿼리 작성이 가능합니다.    
```cs
 class Person
 {
     public string Gender;
     public int Age;

     public Student(string gender, int age)
     {
         this.Gender = gender;
         this.Age = age;
     }
 }
```
```cs
var person = new Person[] {
    new Person("male",19), new Person("male",25), new Person("female",43), new Person("female",13), new Person("male",55)};

var male = from person in people
            group person by person.Gender into genderPersonn
            where genderPerson.Key == "male"
            select genderPerson;
```
위의 코드는 `Gender`에 따라 그룹화한 뒤, 그룹의 Key가 _male_ 인 데이터만 한 번 더 필터링하는 코드입니다.    
다음과 같이 쿼리에 대한 사후 처리가 필요한 경우 `into`를 활용할 수 있습니다.    

## 필터링, 정렬, 조인
시작의 `from`, 끝의 `select`/`group`을 제외한 다른 절은 선택 사항입니다.     
이러한 선택 절은 쿼리에서 사용하지 않을 수 있으며, 반대로 여러 번 사용할 수도 있습니다.     

### where 절
`where` 절을 사용하여 하나 이상의 조건 식을 데이터에 적용할 수 있습니다.    
```cs
var male = from person in people
            where person.Gender == "male"
            select person;
```

### orderby 절
`orderby` 절을 사용하여 결과를 오름차순 또는 내림차순으로 정렬할 수 있습니다.   
기본값은 오름차순이며, 명시적으로 적용하는 것 또한 가능합니다. (`ascending`: 오름차순, `descending`: 내림차순)   
```cs
var ageDescending = from person in people
                    orderby person.Age descending
                    select person;
```

### join 절
`join` 절을 사용하면, 키 간의 같음을 기반으로 한 데이터 소스를 다른 데이터 소스와 연결할 수 있습니다.      

```cs
public class Student
{
    public int Id;
    public string Name;

    public Student(int id, string name)
    {
        this.Id = id;
        this.Name = name;
    }
}

public class Grade
{
    public int Id;
    public int Score;

    public Grade(int id, int score)
    {
        this.Id = id;
        this.Score = score;
    }
}
```
다음과 같이 id라는 변수를 가지는 `Student`, `Grade` 클래스가 있다고 가정합시다.      

```cs
Student[] students = { new Student(1, "Alice"), new Student(2, "Bob"), new Student(3, "Colin"), new Student(4, "Drake") };
Grade[] grades = { new Grade(1,50), new Grade(2,87), new Grade(3,7), new Grade(4,98) };

var studentGrade = from student in students
                   join grade in grades on student.Id equals grade.Id
                   select new { student.Id, student.Name, grade.Score };
```
`from A 요소 in A`       
`join B 요소 in B on A.Key equals B.Key`와 같은 형식으로 작성하며,  
같은 Key를 기준으로 하여 데이터를 연결할 수 있게 해줍니다.   
물론 Key는 이해를 위해 통일하였을 뿐, 각각 다른 이름의 요소일 수 있습니다.     

### let 절
`let` 절을 이용하여 특정 식의 결과를 새 범위 변수에 저장할 수 있습니다.     

위의 students 배열을 그대로 사용한다고 가정하고,   
```cs
var nAndN = from student in students
            let numAndName = (student.Id).ToString() + ". " + student.Name
            select numAndName;
```
해당 코드를 작성하면, Id와 이름이 하나로 합쳐진 _numAndName_ 변수를 만들어     
해당 변수에 대한 시퀀스를 작성할 수 있게 됩니다.     

## 하위 쿼리
쿼리 절에는 _하위 쿼리_ 라고 하는 쿼리 식이 포함될 수 있습니다.    
각 쿼리들은 동일한 데이터 소스를 가리키지 않아도 됩니다.     
```cs
public class Student
{
    public int Year;
    public string Name;
    public int Score;

    public Student(int year, string name, int score)
    {
        Year = year;
        Name = name;
        Score = score;
    }
}
```
```cs
Student[] students = { new Student(1, "Alice", 90), new Student(1, "Bob", 50), new Student(2, "Colin", 80), new Student(2, "Drake", 98), new Student(3, "Elvin", 66) };

var best = from student in students
           group student by student.Year into yearGroup
           select new
           {
             Year = yearGroup.Key,
             BestScore = (from y in yearGroup select y.Score).Max()
           };
```
