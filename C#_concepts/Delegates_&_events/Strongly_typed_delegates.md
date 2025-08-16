.NET에서는  `delegate` 키워드를 사용해 특정 메서드 시그니처에 맞는 델리게이트 타입을 정의할 수 있습니다.    
이는 타입 안정성을 제공하며, _델리게이트에 등록되는 메서드들은 정해진 시그니처를 따라야함을 나타냅니다._      

이러한 방식은 컴파일 시 컴파일러가 타입을 자동 생성해주고, 타입 안정성도 갖춘다는 장점이 있지만,    
새로운 메서드 시그니처가 필요할 때마다 새로운 델리게이트 타입을 정의해야한다는 단점도 있습니다.   

이러한 문제를 해결하기 위해 .NET Core에서는 **재사용 가능한 내장 델리게이트 타입**이 존재합니다.       
```cs
public delegate void Action();
public delegate void Action<in T>(T arg);
public delegate void Action<in T1, in T2>(T1 arg1, T2 arg2);
```
`Action` 대리자는 총 16개의 인수를 포함할 수 있는 변형이 있으며,       
이러한 방식을 통해 유연성을 극대화할 수 있습니다.    

`void` 반환 타입의 대리자 형식에 대해 `Action` 타입을 사용할 수 있습니다.     

---

값을 반환하는 대리자 타입에는 `Func` 타입을 사용할 수 있습니다.      
```cs
public delegate TResult Func<out TResult>();
public delegate TResult Func<in T1, out TResult>(T1 arg);
public delegate TResult Func<in T1, in T2, out TResult>(T1 arg1, T2 arg2);
```
`Func`도 동일하게, 총 16개의 인수를 포함할 수 있는 변형이 있습니다.    
또한 기존 `Func` 규칙과 동일하게, 마지막 타입이 반환 타입을 의미합니다.    

---

`Predicate` 타입의 대리자도 있는데,    
이는 특정 조건을 검사하여 True/False를 반환하는 전용 대리자입니다.  

때문에 `Predicate<T>`는 `Func<T,bool>`와 같은 의미로 사용됩니다.     
```cs
Func<string, bool> TestForString;
Predicate<string> AnotherTestForString;
```
단, 내부적으로 사용되는 타입이 다른 개념이기 때문에        
이 두 대리자들을 교환하며 사용할 수는 없습니다.     
