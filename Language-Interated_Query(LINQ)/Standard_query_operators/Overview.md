- _표준 쿼리 연산자_ 는 LINQ 패턴을 형성하는 키워드 및 메서드를 의미합니다.        
`select`/`.Select()`, `where`/`Where()` 등이 여기에 해당합니다.     

- C#에서는 자주 사용되는 쿼리 메서드를 LINQ 쿼리 키워드로써 정의해두었습니다.    
컴파일러는 이러한 키워드를 사용한 표현식을 메서드 호출로 변환하여 사용합니다.    

- System.Linq 네임스페이스에 속한 일부 메서드들 중에는 쿼리 형식으로는 표현할 수 없는 경우가 있습니다.      

- C# 런타임이나 NuGet 패키지 등에서 매 릴리스마다 LINQ 쿼리에 사용하는 새로운 메서드를 추가하는 경우가 있습니다. `System.Linq.Enumerable` API 문서를 통해 LINQ 메서드를 확인할 수 있습니다.    

# IEnumerable<T>와 IQueryable<T>
`IEnumerable<T>`와 `IQueryable<T>`는 런타임에 쿼리가 실행되는 방법에 따라 구분할 수 있습니다.     

`IEnuerable<T>`는 메모리 내 컬렉션을 대상으로 작업 시 사용됩니다.     
때문에 이미 로드된 데이터를 기반으로 메서드가 실행됩니다.     

쿼리 객체는 단순히 조건을 짜놓은 것에 불과하고, 실제 데이터 접근은 `foreach` 같은 열거 시점에 발생합니다.     
결국 모든 작업이 C# 코드 상에서 실행되기 때문에, 대량의 데이터 처리에는 비효율적일 수 있습니다.     

`IQueryable<T>`는 원격 데이터 소스를 대상으로 작업 시 사용됩니다.   
쿼리를 작성하면, 내부적으로 식 트리로 저장됩니다.    

이 식 트리가 SQL 쿼리 등으로 변환되어 데이터 소스에서 실행됩니다.     
따라서 쿼리를 작성해 보내고, 결과만 받아오는 형식이기 때문에 대량의 데이터 처리에도 효율적인 성능을 낼 수 있습니다.   

# 표준 쿼리 연산자 예시
```cs
string sentence = "A picture is worth a thousand words.";
string[] words = sentence.Split(' ');

var query = from word in words
            group word.ToUpper() by word.Length into gr
            orderby gr
            select new { Length = gr.Key, Words = gr };

var method = words.GroupBy(w => w.Length, w => w.ToUpper())
    .Select(g => new { Length = g.Key, Words = g })
    .OrderBy(o => o.Length);
```

# 쿼리 연산자 유형
표준 쿼리 연산자는 싱글톤 값 또는 값 시퀀스를 반환하는지 여부에 따라 실행되는 타이밍이 다릅니다.    

싱글톤 값을 반환하는, 즉 결과 값을 반환하는 메서드는 선언 시 바로 실행됩니다.    
시퀀스 값을 반환하는 메서드는 선언 시 바로 실행되지 않고 열거 형 객체를 반환합니다.    

# 쿼리 연산자
LINQ 쿼리에서 첫 번쨰 단계는 데이터 원본을 지정하는 것입니다.     
`from` 절을 사용해 데이터 소스를 지정하고 이로부터 범위 변수를 사용할 수 있습니다.     

범위 변수는 데이터 소스로부터 타입을 유추해 올 수 있기 때문에, 명시적으로 지정해 줄 필요가 없습니다.   

나머지 작업에 대해 설명하자면,    
- `where`로 데이터를 필터링합니다.
- `orderby`로 데이터를 정렬하고, `descending`을 추가해 반대로 정렬할 수도 있습니다.
- `group`으로 데이터를 그룹화하고, `into` 키워드를 사용해 그룹을 재사용 할 수 있습니다.
- `join`으로 데이터들을 조인할 수 있습니다.

# LINQ를 사용한 데이터 변환
LINQ은 데이터 검색에만 국한되지 않고, 데이터 변환에도 사용됩니다.     

- 여러 입력 시퀀스를 새로운 타입의 단일 출력 시퀀스로 병합
- 데이터에서 각 요소에서 하나/여러 속성으로의 출력 시퀀스로 변환
- 다른 타입의 출력 시퀀스로 변환

```cs
public class Student
 {
     public string FirstName;
     public string LastName;
     public int[] Scores;

     public Student(string firstName, string lastName, int[] scores)
     {
         FirstName = firstName;
         LastName = lastName;
         Scores = scores;
     }
 }
```
위와 같은 클래스가 정의되어 있다고 가정해봅시다.

```cs
 Student[] students = [new Student("Kim", "Chul", new int[] { 80, 11 }),
 new Student("Lee","Young",new int[]{ 11,22,33})];

 var studentsToXML = new XElement("Root",
     from student in students
     let scores = string.Join(",", student.Scores)
     select new XElement("student",
         new XElement("First", student.FirstName),
         new XElement("Last", student.LastName),
         new XElement("Scores", scores)
     ));

 Console.WriteLine(studentsToXML);
```
다음과 같은 형식으로 `XElement`를 사용할 수 있습니다.    
`XElement(태그, 값)`의 방식으로 사용 가능합니다.   

`XElement`를 중첩하여 사용할 수 있으며,    
중첩하여 사용하는 경우 내부적으로 태그가 들어가는 효과를 낼 수 있습니다.   
따라서, 위 코드의 결과는 다음과 같습니다.   
```xml
<Root>
  <student>
    <First>Kim</First>
    <Last>Chul</Last>
    <Scores>80,11</Scores>
  </student>
  <student>
    <First>Lee</First>
    <Last>Young</Last>
    <Scores>11,22,33</Scores>
  </student>
</Root>
```

# 권장 사항
조인 연산 전의 시퀀스에 대해 `orderby` 절을 사용할 수는 있지만 권장하지 않습니다.      
일부 LINQ 공급자는 조인 후에 해당 순서를 유지하지 않을 수도 있습니다.    
