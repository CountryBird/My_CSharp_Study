_쿼리_ 는 데이터 소스에서 데이터를 검색하는 식입니다.     
데이터 소스는 SQL, XML 등의 각자의 고유한 쿼리 형식이 있기 때문에 개발자의 입장에서는 이를 위한 학습이 필요합니다.    
LINQ는 이러한 과정을 간소화하는 역할을 합니다.     

C# 객체를 사용할 수 있으며,     
같은 방법으로 하여금 XML, SQL 등의 데이터를 사용할 수 있도록 지원합니다.

# 쿼리 작업의 3가지 과정
모든 LINQ 쿼리 작업는 3가지 과정을 거칩니다.       

1. 데이터 소스 가져오기
2. 쿼리 만들기
3. 쿼리 실행하기

```cs
// 1. 데이터 소스 가져오기
int[] numbers = [1, 2, 3, 4, 5, 6];

// 2. 쿼리 생성하기
var numQuery = from num in numbers
               where (num%2) == 0
               select num;

// 3. 쿼리 실행하기
foreach (var num in numQuery)
{
    Console.Write("{0} ", num);
}
```

![image](https://github.com/user-attachments/assets/9ea73bf4-daba-4f8e-84d6-690a4a375fe1)       
**중요한 포인트는, 쿼리 선언과 쿼리 실행이 동시에 일어나지 않는다는 것입니다.**
