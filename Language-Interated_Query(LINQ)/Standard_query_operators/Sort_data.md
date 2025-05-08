_정렬 작업_ 은 하나 이상의 특성을 기준으로 시퀀스의 요소를 정렬합니다.     
첫 번째 정렬 기준은 요소에 대해 기본 정렬을 수행합니다.    
두 번째 정렬 기준은 정렬된 그룹 내에서 또 다시 정렬을 가능하게 합니다.     

![image](https://github.com/user-attachments/assets/22b7e3e3-0c2b-4561-a028-db91851e974a)

```cs
class Person
{
  public int Id;
  public int Grade;
  public string Name;
}
```
```cs
Person[] people = { new Person { Id = 3, Grade = 2, Name = "Alice"}, new Person { Id = 1, Grade = 2, Name = "Bob" },
    new Person { Id = 2, Grade = 1, Name = "Colin"}, new Person { Id = 4, Grade = 3, Name = "Dave"} };
```
해당 문서에서 클래스와 이에 대한 객체는 위와 같이 정의되어 있다고 가정하고 진행합니다.    

# 메서드
|메서드 이름|설명|C# 쿼리 구문|
|---|---|---|
|OrderBy|값을 오름차순으로 정렬|`orderby`|
|OrderByDescending|값을 내림차순으로 정렬|`orderby ... descending`|
|ThenBy|오름차순으로 2차 정렬|`orderby ..., ...`|
|ThenBy|내림차순으로 2차 정렬|`orderby ..., ... descending`|
|Reverse|컬렉션 순서 역순|없음|

# 1차 오름차순 정렬
LINQ 쿼리에서 `orderby` 절을 사용해 특정 요소를 기준으로 오름차순 정렬할 수 있습니다. 
```cs
IEnumerable<Person> peopleQuery = from person in people
                                 orderby person.Id
                                 select person;

foreach(Person person in peopleQuery)
{
  Console.WriteLine($"{person.Id}. {person.Name}");
  // 1. Bob
  // 2. Colin
  // 3. Alice
  // 4. Dave
}
```
메서드 방식은 다음과 같이 나타낼 수 있습니다.  
```cs
IEnumerable<Person> peopleQuery = people.OrderBy(p => p.Id);
```

# 1차 내림차순 정렬
LINQ 쿼리에서 `orderby ... descending` 절을 사용해 특정 요소를 기준으로 내림차순 정렬할 수 있습니다.    
```cs
IEnumerable<Person> peopleQuery = from person in people
                                 orderby person.Id descending
                                 select person;

foreach(Person person in peopleQuery)
{
  Console.WriteLine($"{person.Id}. {person.Name}");
  // 4. Dave
  // 3. Alice
  // 2. Colin
  // 1. Bob
}
```
메서드 방식은 다음과 같이 나타낼 수 있습니다.
```cs
IEnumerable<Person> peopleQuery = people.OrderByDescending(p => p.Id);
```

# 2차 오름차순 정렬
LINQ 쿼리에서 `orderby ..., ...` 절을 사용해 특정 요소들을 기준으로 1, 2차 정렬을 할 수 있습니다.      
앞의 요소를 우선적으로 정렬한 뒤, 정렬된 그룹 안에서 다시 정렬합니다.   
```cs
IEnumerable<Person> peopleQuery = from person in people
                                 orderby person.Grade, person.Id
                                 select person;

foreach(Person person in peopleQuery)
{
  Console.WriteLine($"{person.Grade}-{person.Id}. {person.Name}");
  // 1-2. Colin
  // 2-1. Bob
  // 2-3. Alice
  // 3-4. Dave
}
```
메서드 방식은 다음과 같이 나타낼 수 있습니다.
```cs
IEnumerable<Person> peopleQuery = people.OrderBy(p => p.Grade).ThenBy(p => p.Id);
```
참고로, `ThenBy`가 아닌 `OrderBy`를 연결하여 사용하면 이전의 정렬이 무시되니 주의해야 합니다.

# 2차 내림차순 정렬
LINQ 쿼리에서 `orderby ..., ... descending` 절을 사용해 특정 요소들을 기준으로 1, 2차 내림차순 정렬을 할 수 있습니다.   
```cs
IEnumerable<Person> peopleQuery = from person in people
                                 orderby person.Grade, person.Id descending
                                 select person;

foreach (Person person in peopleQuery)
{
    Console.WriteLine($"{person.Grade}-{person.Id}. {person.Name}");
    // 1-2. Colin
    // 2-3. Alice
    // 2-1. Bob
    // 3-4. Dave
}
```
다음과 같이 코드가 작성되면, Grade에 따라 오름차순 정렬되고 Id에 따라 내림차순으로 정렬됩니다.   

메서드 방식은 다음과 같이 나타낼 수 있습니다.
```cs
IEnumerable<Person> peopleQuery = people.OrderBy(p => p.Grade).ThenByDescending(p => p.Id);
```
