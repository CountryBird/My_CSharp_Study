C# 컴파일러는 지금까지 본 모든 식 트리를 만들었습니다.          
`Expression<Func<T>>` 또는 비슷한 유형으로 입력된 변수에 할당된 람다 표현식을 만들었습니다.             
많은 시나리오에서 실행 시 메모리에 표현식을 구축합니다.              

식 트리는 불변입니다. 이는 루트에서부터 트리를 만들어야 한다는 것을 의미하며                    
식 트리를 만드는 데 사용하는 API는 이러한 사실이 반영되어 있습니다.            
=> 노드를 만드는 데 사용하는 방법은 노드의 모든 자식을 인수로 간주합니다.       

# 노드 만들기
덧셈식부터 시작합니다.              
```cs
Expression<Func<int>> sum = () => 1 + 2;
```

해당 식 트리를 생성하기 위해서는 먼저 리프 노드를 생성해야 합니다.              
리프 노드는 상수이며, 이에 따라 `Constant`를 사용하여 노드를 만듭니다.     
```cs
var one = Expression.Constant(1, typeof(int));
var two = Expression.Constant(2, typeof(int));
```

다음으로, 더하기 식을 빌드합니다.           
```cs
var addition = Expression.Add(one, two);
```

더하기 식을 빌드한 후에는 람다 식을 만듭니다.          
```cs
var lambda = Expression.Lambda(addition);
```

이와 같은 식에서는 모든 호출을 하나의 문장으로 결합할 수 있습니다.             
```cs
var lambda2 = Expression.Lambda(
    Expression.Add(
        Expression.Constant(1, typeof(int)),
        Expression.Constant(2, typeof(int))
    )
);
```

# 트리 빌드
이전 섹션에서는 메모리에 식 트리를 빌드하는 기본 사항을 보여줍니다.            
더 복잡한 트리는 일반적으로 더 많은 노드 유형과 트리의 더 많은 노드를 의미합니다.           

인수 노드 및 메서드 호출 노드에 대한 형식을 살펴보겠습니다.          
```cs
Expression<Func<double, double, double>> distanceCalc =
    (x, y) => Math.Sqrt(x * x + y * y);
```

`x`, `y`와 같은 매개 변수 식을 만듭니다.          
```cs
var xParameter = Expression.Parameter(typeof(double), "x");
var yParameter = Expression.Parameter(typeof(double), "y");
```

이전에 보았던 패턴과 동일하게, 곱하기 식과 덧셈 식을 만듭니다.           
```cs
var xSquared = Expression.Multiply(xParameter, xParameter);
var ySquared = Expression.Multiply(yParameter, yParameter);
var sum = Expression.Add(xSquared, ySquared);
```

추가적으로, `Math.Sqrt`를 위한 메서드 호출 표현식을 만듭니다.
```cs
var sqrtMethod = typeof(Math).GetMethod("Sqrt", new[] { typeof(double) }) ?? throw new InvalidOperationException("Math.Sqrt not found!");
var distance = Expression.Call(sqrtMethod, sum);
```

`GetMethod` 호출은 메서드를 찾을 수 없는 경우 `null`을 반환할 수 있습니다.           
메서드 이름의 철자가 틀렸거나, 필요한 어셈블리가 로드되지 않은 경우가 대부분입니다.      

마지막으로 메서드 호출을 람다 식에 넣고 식을 정의합니다. 
```cs
var distanceLambda = Expression.Lambda(
    distance,
    xParameter,
    yParameter);
```

# 코드 심층 빌드
이러한 방식으로 빌드할 수 잇는 항목에 제한 사항이 있는 것은 아니지만,          
빌드하려는 식 트리가 복잡할수록 코드를 관리하고 읽기가 더 어려워집니다.           

```cs
Func<int, int> factorialFunc = (n) =>
{
    var res = 1;
    while (n > 1)
    {
        res = res * n;
        n--;
    }
    return res;
};
```
앞의 코드는 식 트리가 아닌 단순 대리자 형태입니다.          
`while` 루프를 빌드하기 위한 API는 없으며, 대신 조건부 테스트가 포함된 루프와 루프를 중단하기 위한 레이블 대상을 빌드해야 합니다.    

```cs
var nArgument = Expression.Parameter(typeof(int), "n");
var result = Expression.Variable(typeof(int), "result");

// Creating a label that represents the return value
LabelTarget label = Expression.Label(typeof(int));

var initializeResult = Expression.Assign(result, Expression.Constant(1));

// This is the inner block that performs the multiplication,
// and decrements the value of 'n'
var block = Expression.Block(
    Expression.Assign(result,
        Expression.Multiply(result, nArgument)),
    Expression.PostDecrementAssign(nArgument)
);

// Creating a method body.
BlockExpression body = Expression.Block(
    new[] { result },
    initializeResult,
    Expression.Loop(
        Expression.IfThenElse(
            Expression.GreaterThan(nArgument, Expression.Constant(1)),
            block,
            Expression.Break(label, result)
        ),
        label
    )
);
```
보이는 것과 같이 팩토리얼 코드는 훨씬 더 길고 복잡한 형태를 가집니다.           

# 코드 구성 요소 식 매핑
