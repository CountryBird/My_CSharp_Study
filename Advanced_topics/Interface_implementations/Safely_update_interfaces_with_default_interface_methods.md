인터페이스 멤버 선언 시 구현을 정의할 수 있습니다.           
해당 기능을 사용하는 가장 일반적인 시나리오는, 많은 클라이언트가 이미 릴리즈한 인터페이스에 멤버를 안전하게 추가하는 것입니다.           

# 시나리오 개요
아래의 예시를 _고객 라이브러리 버전 1_ 로 가정합니다.               
이 라이브러리를 빌드한 회사는 고객에 대한 인터페이스를 다음과 같이 정의하였습니다.        
```cs
public interface ICustomer
{
    IEnumerable<IOrder> PreviousOrders { get; }

    DateTime DateJoined { get; }
    DateTime? LastOrder { get; }
    string Name { get; }
    IDictionary<DateTime, string> Reminders { get; }
}
```
또한 주문에 대한 인터페이스를 다음과 같이 정의하였습니다.        
```cs
public interface IOrder
{
    DateTime Purchased { get; }
    decimal Cost { get; }
}
```

다음 릴리스를 위해 라이브러리를 업그레이드할 차례입니다.           
_주문이 많은 고객에 대해 로열티 할인_ 을 제공하는 기능을 추가하려합니다.              

다만 이러한 경우, 문제가 하나 발생합니다.      
`ICustomer`에 해당 기능을 추가할 경우, 이를 구현하는 모든 클래스들은 다시 이 기능에 대한 추가 코드가 필요합니다.        
이러한 문제를 줄이고자 인터페이스에 멤버를 구현할 수 있는 기능을 사용할 수 있습니다.     

# 기본 인터페이스 메서드를 사용하여 업그레이드
_주문이 많으 고객에 대해 로열티 할인_ 기능을 도입해보도록 합시다.                  

해당 기능을 구현하기 위해, **주문 수** (할인을 받기 위한 조건), **할인 비율** 이 필요합니다.         
이를 인터페이스에 반영해보겠습니다.              
```cs
// This method belongs in the ICustomer interface (ICustomer.cs).
// Version 1:
public decimal ComputeLoyaltyDiscount()
{
    DateTime TwoYearsAgo = DateTime.Now.AddYears(-2);
    if ((DateJoined < TwoYearsAgo) && (PreviousOrders.Count() > 10))
    {
        return 0.10m;
    }
    return 0;
}
```

인터페이스에 구현을 추가하는 것으로 다음과 같은 확장성을 가지게 됩니다.     
```cs
SampleCustomer c = new SampleCustomer("customer one", new DateTime(2010, 5, 31))
{
    Reminders =
    {
        { new DateTime(2010, 08, 12), "childs's birthday" },
        { new DateTime(2012, 11, 15), "anniversary" }
    }
};

SampleOrder o = new SampleOrder(new DateTime(2012, 6, 1), 5m);
c.AddOrder(o);

o = new SampleOrder(new DateTime(2013, 7, 4), 25m);
c.AddOrder(o);

// Check the discount:
ICustomer theCustomer = c;
Console.WriteLine($"Current discount: {theCustomer.ComputeLoyaltyDiscount()}");
```

# 매개 변수화 제공
위의 방법은 구현이 다소 제한적인 부분이 있습니다.          
새로운 시스템을 도입하여 구매 횟수, 멤버십 기간, 할인율에 대해 다른 값을 도입할 수 있도록 합시다.                

```cs
// These methods belong in the ICustomer interface (ICustomer.cs).
// Version 2:
public static void SetLoyaltyThresholds(
    TimeSpan ago,
    int minimumOrders = 10,
    decimal percentageDiscount = 0.10m)
{
    length = ago;
    orderCount = minimumOrders;
    discountPercent = percentageDiscount;
}
private static TimeSpan length = new TimeSpan(365 * 2, 0,0,0); // two years
private static int orderCount = 10;
private static decimal discountPercent = 0.10m;

public decimal ComputeLoyaltyDiscount()
{
    DateTime start = DateTime.Now - length;

    if ((DateJoined < start) && (PreviousOrders.Count() > orderCount))
    {
        return discountPercent;
    }
    return 0;
}
```
할인을 위한 실제 값들에 해당하는 부분은 `private`으로 제한하고, 메서드를 `public`으로 한정함으로써                
보다 더 바람직한 코딩 스타일을 유지할 수 있습니다.             

또한 해당 방식도 인터페이스 내 구현 기능을 사용해 확정성면에서 이득을 취할 수 있습니다.     

# 기본 구현 확장
지금까지의 코드는, 사용자가 기본 구현과 같은 내용이거나, 아예 관련 없는 규칙에 대한 시나리오의 구현이었습니다.              
최종 기능으로, 기본 구현을 기반으로 빌드할 수 있는 시나리오를 사용하도록 설정합니다.       

새 고객의 첫 주문에서 50% 할인을 제공하고, 기존 고객은 표준 할인을 받기 위해,                       
이 인터페이스르 구현하는 모든 클래스가 구현에서 코드를 다시 사용할 수 있도록 해야 합니다.             

```cs
// These methods belong in the ICustomer interface (ICustomer.cs).
public decimal ComputeLoyaltyDiscount() => DefaultLoyaltyDiscount(this);
protected static decimal DefaultLoyaltyDiscount(ICustomer c)
{
    DateTime start = DateTime.Now - length;

    if ((c.DateJoined < start) && (c.PreviousOrders.Count() > orderCount))
    {
        return discountPercent;
    }
    return 0;
}
```
이 인터페이스를 구현하는 클래스의 구현에서, 재정의는 정적 헬퍼 메서드를 호출하여 해당 논리를 확장하고,            
이를 통해 새 고객에 대해 할인을 제공할 수 있게 됩니다.           
```cs
// This method belongs in the SampleCustomer class (SampleCustomer.cs) that implements ICustomer.
public decimal ComputeLoyaltyDiscount()
{
   if (PreviousOrders.Any() == false)
        return 0.50m;
    else
        return ICustomer.DefaultLoyaltyDiscount(this);
}
```
