_수량자 작업_ 은 시퀀스에서 조건을 충족하는 요소가 일부인지 전체인지를 나타내는 Boolean 값을 반환합니다.     
모두 메서드 방식을 사용되며, 쿼리 식 방식은 없습니다.    

![image](https://github.com/user-attachments/assets/eb385829-dc71-4bf9-8f36-cb7b0db83fe6)

# All
`All`은 시퀀스의 모든 요소가 조건을 만족하는지를 확인합니다.     
람다 식을 모든 요소가 만족하면 True, 그렇지 않으면 False를 반환합니다.  

```cs
class Student
{
  public int[] Scores;
  public string Name;
}
```
```cs
Student[] students = [ new Student { Scores = [50, 60, 30], Name = "Alice" }, new Student { Scores = [61, 70, 55], Name = "Bob" },
                          new Student { Scores = [62, 85, 71], Name = "Colin" }];

IEnumerable<Student> over60All = from student in students
                                 where student.Scores.All(score => score > 60)
                                 select student;

foreach(Student student in over60All)
{
  Console.WriteLine(student.Name);
  // Colin
}
```
해당 코드에선, 모든 Score의 값이 60을 넘는 Colin만이 선택됩니다.

# Any
