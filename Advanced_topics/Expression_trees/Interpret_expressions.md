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

```console
This is a/an Constant expression type
The type of the constant value is System.Int32
The value of the constant value is 24
```

# 더하기 식
다음과 같은 예제가 있습니다.       
```cs
Expression<Func<int>> sum = () => 1 + 2;
```
루트 노드는 `LambdaExpression`이며,                 
아 코드에서 메인이 되는 코드를 찾기 위해서는 해당 노드의 자식을 찾아야 합니다.            

이 식의 각 노드를 검사하기 위해서는 여러 노드를 재귀적으로 방문해야 합니다.               
다음은 간단한 첫 번째 구현 예제입니다.        
```cs
Expression<Func<int, int, int>> addition = (a, b) => a + b;

Console.WriteLine($"This expression is a {addition.NodeType} expression type");
Console.WriteLine($"The name of the lambda is {((addition.Name == null) ? "<null>" : addition.Name)}");
Console.WriteLine($"The return type is {addition.ReturnType.ToString()}");
Console.WriteLine($"The expression has {addition.Parameters.Count} arguments. They are:");
foreach (var argumentExpression in addition.Parameters)
{
    Console.WriteLine($"\tParameter Type: {argumentExpression.Type.ToString()}, Name: {argumentExpression.Name}");
}

var additionBody = (BinaryExpression)addition.Body;
Console.WriteLine($"The body is a {additionBody.NodeType} expression");
Console.WriteLine($"The left side is a {additionBody.Left.NodeType} expression");
var left = (ParameterExpression)additionBody.Left;
Console.WriteLine($"\tParameter Type: {left.Type.ToString()}, Name: {left.Name}");
Console.WriteLine($"The right side is a {additionBody.Right.NodeType} expression");
var right = (ParameterExpression)additionBody.Right;
Console.WriteLine($"\tParameter Type: {right.Type.ToString()}, Name: {right.Name}");
```
위의 결과는 다음과 같습니다.                  
```console
This expression is a/an Lambda expression type
The name of the lambda is <null>
The return type is System.Int32
The expression has 2 arguments. They are:
        Parameter Type: System.Int32, Name: a
        Parameter Type: System.Int32, Name: b
The body is a/an Add expression
The left side is a Parameter expression
        Parameter Type: System.Int32, Name: a
The right side is a Parameter expression
        Parameter Type: System.Int32, Name: b
```

이전 코드 샘플에서 이러한 형태를 확인할 수 있습니다.             
