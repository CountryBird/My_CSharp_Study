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
이를 개선하여, 더 범용적인 식 노드 `Visitor`를 구축해 보겠습니다.            

이를 위해서는 재귀 알고리즘을 작성해야 하며, 모든 노드는 자식이 있을 수 있는 타입일 수 있습니다.             
자식이 있는 노드는 그 자식을 방문하고 그 노드가 무엇인지 결정해야 합니다.            

```cs
using System.Linq.Expressions;

namespace Visitors;
// Base Visitor class:
public abstract class Visitor
{
    private readonly Expression node;

    protected Visitor(Expression node) => this.node = node;

    public abstract void Visit(string prefix);

    public ExpressionType NodeType => node.NodeType;
    public static Visitor CreateFromExpression(Expression node) =>
        node.NodeType switch
        {
            ExpressionType.Constant => new ConstantVisitor((ConstantExpression)node),
            ExpressionType.Lambda => new LambdaVisitor((LambdaExpression)node),
            ExpressionType.Parameter => new ParameterVisitor((ParameterExpression)node),
            ExpressionType.Add => new BinaryVisitor((BinaryExpression)node),
            _ => throw new NotImplementedException($"Node not processed yet: {node.NodeType}"),
        };
}

// Lambda Visitor
public class LambdaVisitor : Visitor
{
    private readonly LambdaExpression node;
    public LambdaVisitor(LambdaExpression node) : base(node) => this.node = node;

    public override void Visit(string prefix)
    {
        Console.WriteLine($"{prefix}This expression is a {NodeType} expression type");
        Console.WriteLine($"{prefix}The name of the lambda is {((node.Name == null) ? "<null>" : node.Name)}");
        Console.WriteLine($"{prefix}The return type is {node.ReturnType}");
        Console.WriteLine($"{prefix}The expression has {node.Parameters.Count} argument(s). They are:");
        // Visit each parameter:
        foreach (var argumentExpression in node.Parameters)
        {
            var argumentVisitor = CreateFromExpression(argumentExpression);
            argumentVisitor.Visit(prefix + "\t");
        }
        Console.WriteLine($"{prefix}The expression body is:");
        // Visit the body:
        var bodyVisitor = CreateFromExpression(node.Body);
        bodyVisitor.Visit(prefix + "\t");
    }
}

// Binary Expression Visitor:
public class BinaryVisitor : Visitor
{
    private readonly BinaryExpression node;
    public BinaryVisitor(BinaryExpression node) : base(node) => this.node = node;

    public override void Visit(string prefix)
    {
        Console.WriteLine($"{prefix}This binary expression is a {NodeType} expression");
        var left = CreateFromExpression(node.Left);
        Console.WriteLine($"{prefix}The Left argument is:");
        left.Visit(prefix + "\t");
        var right = CreateFromExpression(node.Right);
        Console.WriteLine($"{prefix}The Right argument is:");
        right.Visit(prefix + "\t");
    }
}

// Parameter visitor:
public class ParameterVisitor : Visitor
{
    private readonly ParameterExpression node;
    public ParameterVisitor(ParameterExpression node) : base(node)
    {
        this.node = node;
    }

    public override void Visit(string prefix)
    {
        Console.WriteLine($"{prefix}This is an {NodeType} expression type");
        Console.WriteLine($"{prefix}Type: {node.Type}, Name: {node.Name}, ByRef: {node.IsByRef}");
    }
}

// Constant visitor:
public class ConstantVisitor : Visitor
{
    private readonly ConstantExpression node;
    public ConstantVisitor(ConstantExpression node) : base(node) => this.node = node;

    public override void Visit(string prefix)
    {
        Console.WriteLine($"{prefix}This is an {NodeType} expression type");
        Console.WriteLine($"{prefix}The type of the constant value is {node.Type}");
        Console.WriteLine($"{prefix}The value of the constant value is {node.Value}");
    }
}
```
위의 코드는 식 트리에서 나올 수 있는 모든 노드 유형을 처리하지는 않고, 일부만 처리할 수 있는 형태입니다.           

다만 이 `Visitor`를 실행하면 꽤 유용한 정보를 얻을 수 있는데,         
`Visitor.CreateFromExpression` 메서드의 기본 케이스에서 처리되지 않은               
새 노드 타입이 발견되면 에러 콘솔에 메시지를 출력합니다.                  

이를 통해 아직 처리되지 않은 표현식 트리 노드가 있을 때 사용은 못하지만,         
에러가 발생하지 않고 이를 개선하는 방향을 제시하는 구조로 되어있음을 확인할 수 있습니다.               
 
이에 따른 출력은 다음과 같습니다.          
```console
This expression is a/an Lambda expression type
The name of the lambda is <null>
The return type is System.Int32
The expression has 2 argument(s). They are:
        This is an Parameter expression type
        Type: System.Int32, Name: a, ByRef: False
        This is an Parameter expression type
        Type: System.Int32, Name: b, ByRef: False
The expression body is:
        This binary expression is a Add expression
        The Left argument is:
                This is an Parameter expression type
                Type: System.Int32, Name: a, ByRef: False
        The Right argument is:
                This is an Parameter expression type
                Type: System.Int32, Name: b, ByRef: False
```

# 더 많은 피연산자를 포함하는 덧셈식
노드 타입을 더하기로 유지한 채로, 좀 더 복잡한 예제를 시도해 보겠습니다.                

```cs
Expression<Func<int>> sum = () => 1 + 2 + 3 + 4;
```
위와 같은 식이 있을 때, 모든 값이 상수이기 때문에            
컴파일러는 미리 계산해서 10이라는 하나의 상수 식 트리로 단순화합니다.           

때문에 식 트리를 보고 싶은 경우 상수 하나를 변수로 바꾸는 것이 가장 좋습니다.
```cs
Expression<Func<int, int>> sum = a => 1 + a + 3 + 4;
```

덧셈의 개념 상, 하나의 `+` 연산자에 왼쪽/오른쪽의 두 항을 가져야합니다.           
따라서 이 식을 트리로 만드려면 다음과 같은 구성이 가능합니다.              
```cs
1 + (2 + (3 + 4))   // 오른쪽 결합 (Right associative)
((1 + 2) + 3) + 4   // 왼쪽 결합 (Left associative)
(1 + 2) + (3 + 4)
1 + ((2 + 3) + 4)
(1 + (2 + 3)) + 4
```

_실제 C# 컴파일러는 식 트리를 왼쪽 결합 형태로 생성합니다._
```cs
((1 + a) + 3) + 4
```

앞서 설정한 Visitor로 이 식에 대해 실행하면 다음과 같은 결과를 확인할 수 있습니다.             
```cs
This expression is a/an Lambda expression type
The name of the lambda is <null>
The return type is System.Int32
The expression has 1 argument(s). They are:
        This is an Parameter expression type
        Type: System.Int32, Name: a, ByRef: False
The expression body is:
        This binary expression is a Add expression
        The Left argument is:
                This binary expression is a Add expression
                The Left argument is:
                        This binary expression is a Add expression
                        The Left argument is:
                                This is an Constant expression type
                                The type of the constant value is System.Int32
                                The value of the constant value is 1
                        The Right argument is:
                                This is an Parameter expression type
                                Type: System.Int32, Name: a, ByRef: False
                The Right argument is:
                        This is an Constant expression type
                        The type of the constant value is System.Int32
                        The value of the constant value is 3
        The Right argument is:
                This is an Constant expression type
                The type of the constant value is System.Int32
                The value of the constant value is 4
```

# 예제 확장
