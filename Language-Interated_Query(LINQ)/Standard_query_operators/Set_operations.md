LINQ의 _집합 작업_ 은 컬렉션 내의 동등한 요소가 있는지의 여부에 따라 결과 집합을 나타내는 작업을 의미합니다.       

# Distinct 및 DistinctBy
`Distict`는 컬렉션 내의 고유한 요소만을 포함하여 보일 수 있습니다.    
```cs
char[] letters = ['a', 'a', 'b', 'c', 'c', 'd', 'd', 'e'];

var distinctLetters = from letter in letters.Distinct()
                      select letter;

foreach (var dLetter in distinctLetters)
{
    Console.Write($"{dLetter} ");
    // a b c d e
}
```

`DistinctBy`는 `KeySelector`를 사용하는 `Distinct`입니다.    
고유 판단을 할 때, 람다 식을 사용해 판별 기준을 설정 가능합니다.    
```cs
string[] words = ["apple", "asia", "bear", "candy", "cool", "dragon", "doll", "eagle"];

var firstLetter = from letter in words.DistinctBy(fl => fl[0])
                  select letter;

foreach (var fl in firstLetter)
{
    Console.Write($"{fl} ");
    // apple bear candy dragon eagle
}
```
해당 예제에서는 단어의 첫번째 문자를 기준으로 고유 판단을 하였기 때문에,    
첫 문자 당 처음 나온 단어 하나씩만 선택된 것을 확인할 수 있습니다.    

# Except 및 ExceptBy
`Except`는 첫번째 시퀀스에는 있지만, 두번째 시퀀스에 없는 요소를 포함하여 반환합니다.    
```cs
 int[] first = { 1, 2, 3, 4, 5, 6, 7 };
 int[] second = { 1, 2, 4, 6 };

 var exceptQuery = from num in first.Except(second)
                   select num;

 foreach(int num in exceptQuery)
 {
     Console.Write($"{num} ");
     // 3 5 7
 }
```

`ExceptBy`는 `KeySelector`를 사용하는 `Except`입니다.    
없는 요소에 대한 판단 기준을 람다 식을 통해 설정할 수 있습니다.    
```cs
class Student
{
    public int Id;
    public string Name;

    public Student(int id, string name)
    {
        Id = id;
        Name = name;
    }
}
```
```cs
 Student[] students =
 {
     new Student(1,"Alice"),
     new Student(2,"Bob"),
     new Student(3,"Colin"),
     new Student(4,"Dave"),
     new Student(5,"Elvin")
 };

 int[] exceptId = { 2, 3, 4 };

 var exceptQuery = from student in students.ExceptBy(exceptId,st => st.Id)
                   select student;

 foreach(var student in exceptQuery)
 {
     Console.Write($"{student.Name} ");
     // Alice Elvin
 }
```
해당 코드에서는 exceptId 배열을 통해 제외하고자 하는 id를 설정하고      
student.Id에 대해 적용하여 Student 객체를 필터링할 수 있습니다.   

# Intersect 및 IntersectBy
`Intersect`는 입력 시퀀스 둘 다에 있는, 공통된 요소를 포함하여 반환합니다.     
```cs
int[] nums1 = { 1, 2, 3, 4, 5, 6, 7 };
int[] nums2 = { 1, 2, 4, 8, 9, 13 };

IEnumerable<int> intersectQuery = nums1.Intersect(num2);
foreach(int i in intersectQuery)
{
  Console.Write($"{i} ");
  // 1 2 4
}
```

`IntersectBy`는 `KeySelector`를 사용하는 `Intersect`입니다.    
공통 요소에 대한 판단 기준을 람다 식을 통해 설정할 수 있습니다.   
```cs
class Lottery
{
  public int Num;
  public string Name;

  public Lottery(int num, string name)
  {
    Num = num;
    Name = name;
  }
}
```
```cs
int[] winningNums = { 1, 3, 4 };
Lottery[] lotteries = { new Lottery(1, "Alice"), new Lottery(2, "Bob"), new Lottery(3, "Colin"), new Lottery(4, "Dave"), new Lottery(5, "Elvin") };

var intersectQuery = from win in lotteries.IntersectBy(winningNums, l => l.Num)
                     select win;
foreach(var win in intersectQuery)
{
  Console.Write($"{win.Name} ");
  // Alice Colin Dave
}
```
해당 코드에서는 winningNums 배열을 통해 선택하고자 하는 num을 설정하고      
lottery.Num에 적용하여 해당 배열과 Num이 겹치는 객체를 필터링할 수 있습니다.    

# Union 및 UnionBy
`Union`은 입력 시퀀스들의 합집합을 보여줍니다.  
두 시퀀스를 하나로 만들되, 중복되는 요소는 하나만 표시합니다.     

첫번째 시퀀스가 먼저 들어가며 두번째 시퀀스는 이후에 들어갑니다. 
```cs
int[] num1 = { 1, 2, 3, 4, 5 };
int[] num2 = { 3, 4, 5, 6, 7 };

var nums = num1.Union(num2);
foreach(int num in nums)
{
  Console.Write($"{num} ");
  // 1 2 3 4 5 6 7
}
```

`UnionBy`는 `KeySelector`를 사용하는 `Union`입니다.    
중복 요소에 대한 판단 기준을 람다 식을 통해 설정할 수 있습니다.   
```cs
class Person
{
  public string Name;
  public int Age;
}
```
```cs
List<Person> people1 = [ new Person { Name = "Alice", Age = 25}, new Person { Name = "Bob", Age = 23 } ];
List<Person> people2 = [ new Person { Name = "Alice", Age = 23}, new Person { Name = "Colin", Age = 23 } ];

var unionQuery = from person in people1.UnionBy(people2, p => p.Name)
                 select person;

foreach(var person in unionQuery)
{
  Console.WriteLine($"{person.Name}: {person.Age}");
  // Alice: 25
  // Bob: 23
  // Colin: 23
}
```
해당 코드에서는 Person.Name을 중복의 기준으로 설정하였기 때문에   
people2의 Alice는 결과 시퀀스에 포함되지 않습니다.     

같은 경우 `Union`을 사용하면 두 Alice 객체를 다른 객체로 보기 때문에 둘 다 포함되는 것을 확인할 수 있습니다.     
