쿼리를 효과적으로 작성하려면 전체 쿼리 작업의 변수 형식이 어떻게 서로 관련되는지를 이해하여야 합니다.   
또한 `var`을 사용하면 변수를 암시적으로 설정할 수 있습니다.

LINQ 쿼리 작업은 데이터 소스, 쿼리 자체 및 쿼리 실행에서 엄격히 형식화됩니다.  
쿼리의 변수 형식은 데이터 소스의 타입 및 foreach 문의 반복 변수 타입과 호환되어야 하며,     
이 엄격한 형식화는 사용자가 컴파일 시간에 타입 오류가 catch되도록 합니다.

# 데이터 소스를 변환하지 않는 쿼리
예시 그림은 데이터 소스가 문자열 시퀀스이며, 쿼리 출력도 문자열 시퀀스입니다.        
![image](https://github.com/user-attachments/assets/bafa3cae-fa6d-4372-9fb7-164469a31401)

1. 데이터 소스의 타입에 따라 요소로 사용하는 범위 변수의 타입이 결정됩니다. (여기서는 String)
2. `select`된 개체의 타입에 따라 쿼리 변수의 타입이 결정됩니다.
`IEnumerable<선택된 개체 타입>`의 형태를 띕니다.   
3. `foreach`문에서 반복되며, 쿼리 변수가 문자열 시퀀스이기 때문에 반복 변수도 문자열입니다.

# 데이터 소스를 변환하는 쿼리
첫번째 예시 그림은 데이터 소스가 `Customer` 객체의 시퀀스이며, 쿼리 출력은 문자열 시퀀스입니다.       
![image](https://github.com/user-attachments/assets/e79dc1de-134f-491b-b37c-7bc14daab8e3)

1. 데이터 소스의 타입에 따라 요소로 사용하는 범위 변수의 타입이 결정됩니다. (여기서는 Customer)
2. `select` 문에서 Customer 객체가 아니라 내부 요소인 `Name` 속성인 String 타입이 반환됩니다.
3. `foreach`문에서 반복되는 요소 타입은 `string`으로 결정됩니다.

두번째 예시 그림은 익명 타입을 이용해 조금 더 복잡한 변환의 상황을 가정합니다.       
![image](https://github.com/user-attachments/assets/d63d5556-90e0-4b8c-9009-ac93d06c0874)

1. 데이터 소스의 타입에 따라 요소로 사용하는 범위 변수의 타입이 결정됩니다. (여기서는 Customer)
2. `select`문에서 Customer 객체의 내부 요소을 사용해 새로운 익명 타입 객체를 만듭니다.
3. `foreach`문에서 반복되는 요소 타입은 2번의 익명 타입으로 결정됩니다.

# 컴파일러에서 형식 정보를 유추하도록 허용
`var` 키워드를 사용하는 것으로, 개발자가 쿼리 작업의 타입을 암시적으로 지정할 수 있습니다.     
![image](https://github.com/user-attachments/assets/4bfa525a-885e-48a1-9426-3789a3b29820)

# LINQ 쿼리의 IEnumerable<T> 변수
LINQ 쿼리 변수는 `IEnumerable<T>` 또는 `IQueryable<T>` 등의 형식으로 형식화됩니다.     
이는 쿼리가 실행될 때 `T` 타입인 0개 이상의 객체 시퀀스를 생성한다는 의미로 받아 들일 수 있습니다.     
```cs
IEnumerable<Student> studentQuery = from student in students
                                    where student.Id > 3
                                    select student;
```

# 컴파일러에서 제네릭 타입 선언을 처리하도록 허용
클래스의 객체나 이름이 긴 객체의 경우 `IEnumerable<T>` 형식으로 적기 불편할 수 있습니다.    
원하는 경우 `var` 키워드를 사용해 제네릭 타입 구문을 간단히 적을 수 있습니다. 
```cs
var studentQuery = from student in students
                                    where student.Id > 3
                                    select student;
```
다만 `var` 키워드를 지나치게 사용하면 다른 개발자, 사용자가 코드를 이해하기 어려워질 수도 있기 때문에      
변수의 타입이 명확히 알 수 있거나, 명시적으로 지정하는 것이 중요하지 않은 경우에 사용하는 것이 좋습니다.   
