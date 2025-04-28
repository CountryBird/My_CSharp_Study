# 쿼리 식
쿼리 식은 SQL이나 XQuery와 비슷한 형태의 구문을 사용하여
`System.Collections.Generic.IEnumerable<T>` 컬렉션을 쿼리할 수 있습니다.     

컴파일 시간에 쿼리 문은 메서드 형태로 변환되어 호출됩니다.
```cs
var query = from str in stringArray
            group str by str[0] into stringGroup
            orderby stringGroup.Key
            select stringGroup;
```

# 암시적으로 형식화된 변수
`var` 한정자를 사용하여 컴파일러에게 형식에 대한 부분을 맡겨 유추하여 할당되도록 할 수 있습니다.     

`var`로 선언된 변수는 단지 시작 타입을 암시적으로 선언하는 것이기 때문에,       
여전히 강력한 타입으로 선언되어 타입을 변할 수 없습니다.  

# 객체 및 컬렉션 이니셜라이저
객체 및 컬렉션 이니셜라이저를 사용하여 객체에 대한 생성자를 직접 호출하지 않고도 객체를 초기화할 수 있습니다.      
```cs
var stud = new Student { Id = 1, Name = "Alice" };
```
기본 생성자를 호출하고 이후에 값을 넣는 개념이기 때문에      
해당 클래스에서 생성자를 별도로 선언했다면 제대로 작동하지 않을 수도 있습니다.    

이러한 객체 및 컬렉션 이니셜라이저를 새로운 객체를 LINQ에도 사용할 수 있습니다.    
```cs
var id5Stu = from s in students
         where s.Id > 5
         select new Student { Id = s.Id-5,Name=s.Name};
```

# 익명 형식
