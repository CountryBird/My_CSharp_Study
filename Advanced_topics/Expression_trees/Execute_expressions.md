_식 트리_ 는 일부 코드를 나타내는 데이터 구조입니다.               
식 트리가 나타내는 .NET 코드를 실행하려면 이를 실행 가능한 IL 명령으로 변환해야 합니다.           
식 트리를 실행하면 값이 반환되거나 메서드 호출과 같은 작업만 수행할 수 있습니다.      

람다 식을 나타내는 식 트리는 `LambdaExpression`이거나 `Expression<TDelegate>`가 있고               
이러한 식 트리를 실행하려면 Compile을 호출하여 실행 가능한 대리자를 만든 다음 대리자를 호출합니다.             

식 트리가 람다 식을 나타내지 않는 경우, ` Lambda<TDelegate>(Expression, IEnumerable<ParameterExpression>) `메서드를 호출하여        
원래 식 트리를 본문으로 포함하는 새 람다 식을 만들 수 있습니다.                        

# 함수에 대한 람다 식
C#의 모든 표현식은 `System.Linq.Expressions.Expression`을 기반으로 합니다.          
그 중 실행 가능한 형태로 변환할 수 있는 건 `LambdaExpression` 입니다.              

- `LambdaExpression`은 모든 람다 표현식의 공통 기반 클래스
- `Expression<TDelegate>`는 `Func<>`, `Action<>` 같은 델리게이트 타입과 연결되는 구체적인 클래스

예를 들어 즉, `Expression<TDelegate>` 타입은, 내부적으로는 `LambdaExpression`의 하위 클래스입니다.              

-------

`LambdaExpression.Compile()`을 호출하면,              
표현식 트리를 실제 실행 가능한 IL 코드로 바꿔줍니다.        

```cs
Expression<Func<int>> add = () => 1 + 2;
var func = add.Compile(); // func은 Func<int> 타입의 델리게이트
var result = func();      // 실제 실행 -> 3
Console.WriteLine(result);
```

------

`CompileToMethod`는 `System.Reflection.Emit.MethodBuilder`를 이용해           
IL 코드로 직접 내장된 메서드를 생성하는 기능입니다.            

예전 .NET Framework에서는 어셈블리 생성용 코드에선 사용했지만,                
.NET Core 이후에서는 지원되지 않으므로 `Compile()`을 사용하는 편이 낫습니다.            

----------

선택 사항으로,              
`DebugInfoGenerator`를 사용하면 `Compile()` 할 때 생성된 코드에 디버깅용 심볼 정보를 넣을 수 있습니다.            

# 실행 및 생명주기
`LambdaExpression.Compile()`을 호출하면, 대리자를 호출하여 코드를 실행합니다.                     
앞의 코드에서는 `add.Compile()` 대리자를 반환합니다. `func()`를 호출하여 대리자를 활성화하고 코드를 실행합니다.        

해당 대리자는 식 트리의 코드를 나타냅니다. 해당 대리자의 핸들을 유지하고 나중에 호출할 수 있습니다.                   
코드를 실행할 때마다 식 트리를 컴파일할 필요가 없습니다.              
식 트리는 변경할 수 없으며 나중에 동일한 식 트리를 컴파일하면 동일한 코드를 실행하는 대리자를 만들 수 있습니다.           

# 제한 사항
람다 식을 대리자로 컴파일하고, 해당 대리자를 호출하는 것은 식 트리를 사용하여 수행할 수 있는 간단한 작업 중 하나입니다.             
그러나 이 작업을 수행하기 위해 일부 주의해야 할 사항이 있습니다.             

----------

C#에서 람다식은 자기 바깥에 있는 지역 변수를 사용할 수 있고, 이를 _클로저_ 라고 부릅니다.          
```cs
var x = 5;
Func<int, int> f = (n) => x + n;
```
다음과 같은 코드가 있을 때, `f`는 `x`라는 변수를 값을 복사하는 것이 아닌 참조를 저장합니다.             
때문에 이후에 `x`의 값이 바뀌면 `f`가 사용하느 값도 바뀝니다.            

식 트리도 마찬가지로 `.Compile()`하면 결국 람다식 기반의 델리게이트를 만드는 것이고,              
식 트리도 내부적으로 클로저를 만드는 꼴이 됩니다.        

이 때 만약, 참조하는 변수가 Dispose가 된다면 문제가 생깁니다.       
```cs
using (var res = new Resource())
{
    Expression<Func<int, int>> expr = (n) => res.Argument + n;
    var func = expr.Compile();
    return func;
}
```
`res`는 `using` 블록이 끝나는 순간 `Dispose()` 가 호출되는데,              
`expr`은 `res`를 참조하고 이를 컴파일한 `func` 같은 대리자는 없어진 리소스를 기억하는 꼴이 됩니다.               

# 요약
람다 식을 나타내는 식 트리를 컴파일하여 실행할 수 있는 대리자를 만들 수 있습니다.                
식 트리는 코드를 실행하는 하나의 메커니즘을 제공합니다.       

식 트리는 사용자가 만든 지정된 구문에 대해 실행할 코드를 나타냅니다.           
코드를 컴파일하고 실행하는 환경이 식을 만드는 환경과 일치하는 한 모든 것이 예상대로 작동합니다.      

그렇지 않은 경우는, 예측 가능하며 식 트리를 사용하는 코드의 첫번째 테스트에서 오류가 포착됩니다.     
