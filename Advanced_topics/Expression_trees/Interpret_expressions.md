다음 코드 예제에서는 `num => num <5`를 나타내는 식 트리를 분해하는 방법을 보여 줍니다.        
```cs
// Add the following using directive to your code file:
// using System.Linq.Expressions;

// Create an expression tree.
Expression<Func<int, bool>> exprTree = num => num < 5;

// Decompose the expression tree.
ParameterExpression param = (ParameterExpression)exprTree.Parameters[0];
BinaryExpression operation = (BinaryExpression)exprTree.Body;
ParameterExpression left = (ParameterExpression)operation.Left;
ConstantExpression right = (ConstantExpression)operation.Right;

Console.WriteLine($"Decomposed expression: {param.Name} => {left.Name} {operation.NodeType} {right.Value}");

// This code produces the following output:

// Decomposed expression: num => num LessThan 5
```

식 트리의 구조를 검사하는 코드를 작성해 보겠습니다.       
식 트리의 모든 노드 `Expression` 클래스에서 파생된 클래스 객체입니다.        

이러한 설계를 통해 식 트리의 모든 노드를 방문하는 작업은 비교적 간단한 재귀 작업입니다.       
일반적인 전략은 루트 노드에서 시작하여 노드의 종류를 결정하는 것입니다.         

노드 타입에 자식이 있는 경우 자식을 재귀적으로 방문합니다.           
각 자식 노드에서 루트 노드에서 사용되는 프로세스를 반복합니다.            
타입을 확인하고 타입에 자식이 있는 경우 각 자식을 방문합니다.        

# 자식이 없는 식 검사 
가장 간단한 예시로, 식 트리의 각 노드를 방문해 보겠습니다.        
다음은 상수 식을 만든 후 해당 속성을 검사하는 코드입니다.        

```cs
var constant = Expression.Constant(24, typeof(int));

Console.WriteLine($"This is a/an {constant.NodeType} expression type");
Console.WriteLine($"The type of the constant value is {constant.Type}");
Console.WriteLine($"The value of the constant value is {constant.Value}");
```
위의 코드는 다음 출력을 출력합니다.      

```cs
This is a/an Constant expression type
The type of the constant value is System.Int32
The value of the constant value is 24
```

# 더하기 식
