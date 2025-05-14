두 데이터 소스 간의 _조인_ 이란, 
한 데이터 소스의 객체를 다른 데이터 소스의 공통 속성을 공유하는 객체와 연결하는 것을 의미합니다.    

서로 직접적으로 연결되어 있지 않은 데이터 소스들 사이의 관계를 표현하고 데이터를 추출할 때       
유용하게 사용할 수 있습니다.    

LINQ에서는 `Join`과 `GroupJoin`을 제공합니다.     
`Join`은 내부 조인의 개념을 구현하며,      
두 데이터 소스에서 키 값이 같은 요소들만 반환합니다.     

`GroupJoin`은 왼쪽 외부 조인의 개념을 구현하며,       
각 왼쪽 요소에 대해, 매칭되는 오른쪽 요소들을 그룹으로 반환합니다.    

![image](https://github.com/user-attachments/assets/59e3c08d-add0-41fa-bf87-cb7131eea623)       

# Join
키 선택기 함수를 기준으로 두 시퀀스를 조인한 다음, 값 쌍을 추출합니다.  
`외부 컬렉션.Join(내부 컬렉션, 외부 컬렉션 비교 키, 내부 컬렉션 비교 키, 조인 결과)`의 방식으로 이루어져 있습니다.   

```cs
var customers = new[]
{
    new { Id = 1, Name = "Alice" },
    new { Id = 2, Name = "Bob" }
};

var orders = new[]
{
    new { CustomerId = 1, Product = "Book" },
    new { CustomerId = 2, Product = "Pen" },
    new { CustomerId = 1, Product = "Laptop" }
};

var result = customers.Join(orders, c => c.Id, o => o.CustomerId, (c, o) => new { c.Name, o.Product });
foreach(var r in result)
{
  Console.WriteLine($"{r.Name} - {r.Product}");
}
// Alice - Book
// Alice - Laptop
// Bob - Pen
```

쿼리 방식으로는 다음과 같이 나타낼 수 있습니다.     
```cs
var result = from customer in customers
             join order in orders
             on customer.Id equals order.CustomerId
             select new { customer.Name, order.Product };
```

# GroupJoin
키 선택기 함수를 기준으로 두 시퀀스를 조인한 다음 결과로 생성된 일치 항목을 요소마다 그룹화합니다.        
`외부 컬렉션.GroupJoin(내부 컬렉션, 외부 컬렉션 비교 키, 내부 컬렉션 비교 키, 조인 결과)`의 방식으로 이루어져 있습니다.   

```cs
var result = customers.GroupJoin(orders, c => c.Id, o => o.CustomerId, (c, orderGroup) => new { c.Name, orderGroup });
foreach(var r in result)
{
  Console.WriteLine($"{r.Name}: ");
  foreach(var o in r.orderGroup)
  {
    Console.WriteLine($"- {o.Product}");
  }
}
// Alice:
// - Book
// - Pen
// Bob:
// - Laptop
```
위와 같이 왼쪽 시퀀스 요소 하나에 오른쪽 시퀀스의 여러 요소들을 그룹화하여 할당합니다.     
때문에 `SelectMany` 등의 평탄화 작업을 추가적으로 수행하는 것도 가능합니다.    

쿼리 방식으로는 다음과 같이 나타낼 수 있습니다.  
```cs
var result = from customer in customers
             join order in orders
             on customer.Id equals order.CustomerId into orderGroup
             select new { customer.Name, orderGroup };
```
`into`를 통해 그룹을 지정하고, 그 그룹을 새 객체에 할당하는 방식으로 `GroupJoin`을 구현할 수 있습니다.   

# 내부 조인
관계형 데이터베이스 용어에서 _내부 조인_ 이란,       
첫 번째 컬렉션의 요소가 두 번째 컬렉션의 일치하는 모든 요소에 대해 **한 번씩** 나타나는 것을 의미합니다.      
때문에 일치하는 요소가 없다면, 컬렉션의 요소가 결과에 포함되지 않을 수도 있습니다.     

C#에서는 `Join` 메서드가 이에 해당하며,   
크게 4가지 개념으로 변형되어 사용됩니다.     

## 단일 키 조인
단순히 컬렉션 하나에 하나의 요소를 키로 사용하는 방법입니다.   
이전 단락의 `Join`의 코드가 단일 키 조인 방식을 사용한 대표적인 예입니다.    

## 복합 키 조인
키를 설정할 때, 여러 가지의 요소를 키로 사용하는 방법입니다.     
키에 레이블을 지정하는 경우 각 키에 대해서는 동일한 레이블이 존재해야 하며, 동일한 순서로 나타나야 합니다.   

```cs
var customers = new[]
{
  new { Id = 1, Region = "Seoul", Name = "Alice" },
  new { Id = 2, Region = "Busan", Name = "Bob" },
  new { Id = 3, Region = "Busan", Name = "Colin" }
};

var orders = new[]
{
  new { CustomerId = 1, Region = "Seoul", Product = "Book" },
  new { CustomerId = 2, Region = "Seoul", Product = "Pen" },
  new { CustomerId = 3, Region = "Busan", Product = "Laptop" }
};

var result = customers.Join(orders,
  c => new { c.Id, c.Region }, o => new { Id = o.CustomerId, o.Region }, // 레이블 이름 동일하게 함에 주의
  (c, o) => new { c.Name, o.Product });

foreach (var r in result)
{
  Console.WriteLine($"{r.Name} - {r.Product}");
}
// Alice - Book
// Colin - Laptop
```

쿼리 방식으로는 다음과 같은 형식으로 나타낼 수 있습니다.
```cs
var result = from customer in customers
             join order in orders
             on new { customer.Id, customer.Region } equals new { Id = order.CustomerId, order.Region }
             select new { customer.Name, order.Product };
```

## 다중 조인
조인 절의 결과로 또 다시 조인을 수행하여, 여러 조인 작업을 추가하는 방법입니다.   
C#의 각 `join` 절은 지정된 데이터 소스를 이전 조인의 결과와 연결시킬 수 있습니다.   

```cs
var customers = new[]
{
    new { Id = 1, Name = "Alice" },
    new { Id = 2, Name = "Bob" }
};

var orders = new[]
{
    new { CustomerId = 1, Product = "Book" },
    new { CustomerId = 2, Product = "Pen" },
    new { CustomerId = 1, Product = "Laptop" }
};

var deliveries = new[]
{
  new { Product = "Book", Day = 3 },
  new { Product = "Pen", Day = 1 },
  new { Product = "Laptop", Day = 10 }
};

var result = customers.Join(orders, customer => customer.Id, order => order.CustomerId, (customer, order) => new { customer, order })
  .Join(deliveries, cAndO => cAndO.order.Product, delivery => delivery.Product, (cAndO, delivery) => new { cAndO.customer.Name, cAndO.order.Product, delivery.Day });

foreach (var r in result)
{
  Console.WriteLine($"{r.Name}'s {r.Product} arrives in {r.Day} days.");
}
// Alice's Book arrives in 3 days.
// Bob's Pen arrives in 1 days.
// Colin's Laptop arrives in 10 days.
```

쿼리 방식으로는 다음과 같이 나타낼 수 있습니다.
```cs
var result = from customer in customers
             join order in orders on customer.Id equals order.CustomerId
             join delivery in deliveries on order.Product equals delivery.Product
             select new
             {
                customer.Name,
                order.Product,
                delivery.Day
             };
```

## 내부 조인 안에서의 그룹화된 조인
`GroupJoin`을 사용하여 내부 조인을 구현하는 방법입니다.     
그룹 조인을 통하여 왼쪽 요소에 대해 매칭되는 오른쪽 요소들을 그룹화하여 조인합니다.   

그 다음에, `into`를 사용해 평탄화 작업을 합니다. 이는 결국 1:1 대응으로 펼친 내부 조인과 같은 효과를 만듭니다.
```cs
var result1 = customers.GroupJoin(orders, c => c.Id, o => o.CustomerId, (c, orderGroup) => new { c.Name, orderGroup })
    .SelectMany(x => x.orderGroup, (x,order) => new {Name = x.Name, Product = order.Product});
    // orderGroup을 지정하여 평탄화 한 후, Name과 엮어 새로운 객체를 만듦

var result2 = from customer in customers
              join order in orders on customer.Id equals order.CustomerId into cAndO
              from co in cAndO
              select new
              {
                  Name = customer.Name, // customer는 메인 시퀀스 변수라 LINQ 전체에서 사용 가능
                  Product = co.Product // order는 join 절의 임시 변수라 사용 못함
              };
```
각각 메서드 스타일과, 쿼리 스타일로 결과를 보일 수 있습니다.     

# 그룹화 조인 수행
