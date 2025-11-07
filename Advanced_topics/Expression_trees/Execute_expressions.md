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
