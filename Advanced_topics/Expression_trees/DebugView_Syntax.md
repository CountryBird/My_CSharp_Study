DebugView 속성은 식 트리의 문자열 렌더링을 제공합니다.          

# ParameterExpression
`ParameterExpression` 변수는 시작 부분에 `$` 기호와 함께 표시됩니다.          
매개 변수에 이름을 지정하지 않았다면 자동 생성된 이름 (예: `$var1` 또는 `$var2`)이 할당됩니다.

```cs
ParameterExpression numParam =  Expression.Parameter(typeof(int), "num");
/*
    $num
*/

ParameterExpression numParam =  Expression.Parameter(typeof(int));
/*
    $var1
*/
```

# ConstantExpression
`ConstantExpression`은 정수 값, 문자열 및 상수, `null`의 값을 나타내는 개체를 나타냅니다.        
표준 접미사가 C# 리터럴인 숫자 형식의 경우 접미사가 값에 추가됩니다.       

유형|키워드|접미사
-----|------|-------
System.UInt32|uint|U
System.Int64|long|L
System.UInt64|ulong|UL
System.Double|double|D
System.Single|float|F
System.Decimal|decimal|M

```cs
int num = 10;
ConstantExpression expr = Expression.Constant(num);
/*
    10
*/

double num = 10;
ConstantExpression expr = Expression.Constant(num);
/*
    10D
*/
```

# BlockExpression
`BlockExpression`은 블록이 마지막 식의 타입과 다를 때만 `<>` 안에 표시됩니다.          
그렇지 않은 경우에는 표시되지 않습니다.         
```cs
BlockExpression block = Expression.Block(Expression.Constant("test"));
/*
    .Block() {
        "test"
    }
*/

BlockExpression block =  Expression.Block(typeof(Object), Expression.Constant("test"));
/*
    .Block<System.Object>() {
        "test"
    }
*/
```
