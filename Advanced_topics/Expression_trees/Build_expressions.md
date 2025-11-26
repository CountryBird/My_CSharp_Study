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
