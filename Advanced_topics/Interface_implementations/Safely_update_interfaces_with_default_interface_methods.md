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
