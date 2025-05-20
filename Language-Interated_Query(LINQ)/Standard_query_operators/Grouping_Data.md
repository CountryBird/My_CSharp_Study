_그룹화_ 는 데이터를 그룹에 넣어 각 그룹의 요소가 공통 특성을 가지게하는 작업을 의미합니다.   
![image](https://github.com/user-attachments/assets/4e90b3da-7c70-4acc-bbfa-d5341e5fe537)

# GroupBy
`GroupBy`메서드를 사용해, 요소들 중에 공통되는 특성으로 그룹화할 수 있습니다.  
```cs
List<int> numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10];

IEnumerable<IGrouping<int, int>> methodStyle
    = numbers.GroupBy(number => number % 2);

IEnumerable<IGrouping<int, int>> queryStyle
    = from number in numbers
      group number by number % 2;
```
그룹은 `Key`를 기준으로 그룹화 되며, 해당 그룹 내의 요소들에 대해 접근하는 것도 가능합니다.
```cs
foreach(var group in methodStyle)
{
    Console.WriteLine(group.Key);
    foreach(var item in group)
    {
        Console.WriteLine(item);
    }
}
```

# 쿼리 결과 그룹화
그룹화는 LINQ의 가장 강력한 기능 중 하나이며, 다음과 같은 방법으로 데이터를 그룹화할 수 있습니다.   
- 단일 속성
- 문자열 속성의 첫 문자
- 계산된 숫자 범위
- 부울 조건자 또는 기타 식
- 복합 키

## 단일 속성에 대한 그룹화
컬렉션 내의 _단일 속성_ 을 그룹 키로 사용하여 요소를 그룹화하는 방법입니다. 
```cs
Student[] students =
{
    new Student {Grade = 1, Name = "Alice"},
    new Student {Grade = 1, Name = "Bob"},
    new Student {Grade = 2, Name = "Colin"},
    new Student {Grade = 3, Name = "Dave"}
};

var groupByGrade_Method = students.GroupBy(s => s.Grade);
var groupByGrade_Query = from student in students
                         group student by student.Grade into grade
                         select grade;

foreach(var student in groupByGrade_Method)
{
    Console.WriteLine($"Grade {student.Key}");
    foreach(var s in student)
    {
        Console.Write($" {s.Name} ");
    }
}
// Grade 1
// Alice
// Bob
//Grade 2
// Colin
//Grade 3
// Dave
```

## 값에 대한 그룹화
컬렉션 내의 속성이 아닌 _값_ 의 개념에서 그룹 키를 사용해 요소를 그룹화하는 방법입니다. 
```cs
Student[] students =
{
    new Student {Grade = 1, Name = "Alice"},
    new Student {Grade = 2, Name = "Austin"},
    new Student {Grade = 2, Name = "Blake"},
    new Student {Grade = 3, Name = "Bruce"}
};

var groupByNameStart_Method = students.GroupBy(s => s.Name[0]);
var groupByNameStart_Query = from student in students
                             group student by student.Name[0];

foreach(var student in groupByNameStart_Method)
{
    Console.WriteLine($"Name start with {student.Key}");
    foreach(var s in student)
    {
        Console.WriteLine($" {s.Name} ");
    }
}
// Name start with A
//  Alice
//  Austin
// Name start with B
//  Blake
//  Bruce
```

## 범위에 대한 그룹화
수식 메서드를 통해 일종의 _범위_ 개념을 만들어 이를 그룹 키로 사용해 그룹화하는 방법입니다.
```cs
public class Student
{
    public string Name;
    public int[] Scores;
}

static int GetPercentile(Student s)
{
    double avg = s.Scores.Average();
    return avg > 0 ? (int) avg / 10 : 0;
}
```
```cs
Student[] students = { new Student { Name = "Alice", Scores = [30, 50, 70]},
    new Student { Name = "Bob", Scores = [50, 60, 85] }, new Student { Name = "Colin", Scores = [50, 30, 70] } };

var groupByScorePercentMethod = students.GroupBy(student => GetPercentile(student));
var groupByScorePercentQuery = from student in students
                               let percent = GetPercentile(student)
                               group student by percent;

foreach(var group in groupByScorePercentMethod)
{
    Console.WriteLine($"Score around {group.Key * 10}");
    foreach(var student in group)
    {
        Console.Write($"{student.Name} ");
    }
    Console.WriteLine();
}

// Score around 50
// Alice Colin
// Score around 60
// Bob
```

## 비교에 따른 그룹화
_부울 식을 통해_ 비교를 하여 소스 요소를 그룹화하는 방법입니다. 
```cs
var over60Method = students.GroupBy(student => student.Scores.Average() > 60);
var over60Query = from student in students
                  group student by student.Scores.Average() > 60;

foreach(var group in over60Query)
{
    Console.WriteLine($"over 60? {group.Key}");
    foreach(var student in group)
    {
        Console.Write($"{student.Name} "); 
    }
    Console.WriteLine();
}
// over 60? False
// Alice Colin
// over 60? True
// Bob
```

## 무명 타입에 따른 그룹화
여러 값이 포함된 키를 그룹화하는 방법으로, _무명 타입_ 으로 값들을 캡슐화하는 방법이 있습니다. 
```cs
var groupByCompoundKeyMethod = students
     .GroupBy(student => new { Name = student.Name, Over30 = student.Scores[0] > 30 });

var groupByCompoundKeyQuery = from student in students
                              group student by new
                              {
                                  Name = student.Name,
                                  Over30 = student.Scores[0] > 30
                              };

foreach( var student in groupByCompoundKeyMethod )
{
    Console.WriteLine(student.Key.Name);
    Console.WriteLine(student.Key.Over30);
}
```

# 중첩 그룹 만들기기
