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
