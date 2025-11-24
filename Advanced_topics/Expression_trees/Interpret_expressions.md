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
해당 예제는 가장 기본적인 식 트리만 처리합니다.             
이 섹션에서 본 코드는 상수 및 `+` 연산자만 처리합니다.        

다음은 팩토리얼 식에 대한 적용 예제입니다.          
```cs
Expression<Func<int, int>> factorial = (n) =>
    n == 0 ?
    1 :
    Enumerable.Range(1, n).Aggregate((product, factor) => product * factor);
```
이 코드에서는 식 트리를 빌드하는 두 가지 제한 사항을 보여주는 예시이기도 합니다.       

첫째, 문자 람다는 허용되지 않습니다.        
C#에서의 루프(`while`, `for`), 조건(`if/else`) 등의 제어 구조를 사용할 수 없습니다.         

둘째, 동일한 식을 재귀적으로 호출할 수 없습니다.        
이미 대리자가 있는 경우에는 가능하지만, 식 트리 양식으로는 호출할 수 없습니다.         

이 식에서는 이러한 모든 타입의 노드가 발생합니다.      
1. 동일 식
2. 곱하기
3. 조건부 삼항
4. 메서드 호출 식

`Visitor` 알고리즘을 수정하는 한 가지 방법은 계속 실행하면서 절에 도달할 때마다 노드 형식을 작성하는 것입니다.       
몇 번의 반복 후에는 각 잠재적 노드가 표시됩니다.      
```cs
public static Visitor CreateFromExpression(Expression node) =>
    node.NodeType switch
    {
        ExpressionType.Constant    => new ConstantVisitor((ConstantExpression)node),
        ExpressionType.Lambda      => new LambdaVisitor((LambdaExpression)node),
        ExpressionType.Parameter   => new ParameterVisitor((ParameterExpression)node),
        ExpressionType.Add         => new BinaryVisitor((BinaryExpression)node),
        ExpressionType.Equal       => new BinaryVisitor((BinaryExpression)node),
        ExpressionType.Multiply    => new BinaryVisitor((BinaryExpression) node),
        ExpressionType.Conditional => new ConditionalVisitor((ConditionalExpression) node),
        ExpressionType.Call        => new MethodCallVisitor((MethodCallExpression) node),
        _ => throw new NotImplementedException($"Node not processed yet: {node.NodeType}"),
    };
```

`ConditionalVisitor`와 `MethodCallVisitor`가 두 노드를 처리합니다.      
```cs
public class ConditionalVisitor : Visitor
{
    private readonly ConditionalExpression node;
    public ConditionalVisitor(ConditionalExpression node) : base(node)
    {
        this.node = node;
    }

    public override void Visit(string prefix)
    {
        Console.WriteLine($"{prefix}This expression is a {NodeType} expression");
        var testVisitor = Visitor.CreateFromExpression(node.Test);
        Console.WriteLine($"{prefix}The Test for this expression is:");
        testVisitor.Visit(prefix + "\t");
        var trueVisitor = Visitor.CreateFromExpression(node.IfTrue);
        Console.WriteLine($"{prefix}The True clause for this expression is:");
        trueVisitor.Visit(prefix + "\t");
        var falseVisitor = Visitor.CreateFromExpression(node.IfFalse);
        Console.WriteLine($"{prefix}The False clause for this expression is:");
        falseVisitor.Visit(prefix + "\t");
    }
}

public class MethodCallVisitor : Visitor
{
    private readonly MethodCallExpression node;
    public MethodCallVisitor(MethodCallExpression node) : base(node)
    {
        this.node = node;
    }

    public override void Visit(string prefix)
    {
        Console.WriteLine($"{prefix}This expression is a {NodeType} expression");
        if (node.Object == null)
            Console.WriteLine($"{prefix}This is a static method call");
        else
        {
            Console.WriteLine($"{prefix}The receiver (this) is:");
            var receiverVisitor = Visitor.CreateFromExpression(node.Object);
            receiverVisitor.Visit(prefix + "\t");
        }

        var methodInfo = node.Method;
        Console.WriteLine($"{prefix}The method name is {methodInfo.DeclaringType}.{methodInfo.Name}");
        // There is more here, like generic arguments, and so on.
        Console.WriteLine($"{prefix}The Arguments are:");
        foreach (var arg in node.Arguments)
        {
            var argVisitor = Visitor.CreateFromExpression(arg);
            argVisitor.Visit(prefix + "\t");
        }
    }
}
```

식 트리의 출력은 다음과 같습니다.            
```console
This expression is a/an Lambda expression type
The name of the lambda is <null>
The return type is System.Int32
The expression has 1 argument(s). They are:
        This is an Parameter expression type
        Type: System.Int32, Name: n, ByRef: False
The expression body is:
        This expression is a Conditional expression
        The Test for this expression is:
                This binary expression is a Equal expression
                The Left argument is:
                        This is an Parameter expression type
                        Type: System.Int32, Name: n, ByRef: False
                The Right argument is:
                        This is an Constant expression type
                        The type of the constant value is System.Int32
                        The value of the constant value is 0
        The True clause for this expression is:
                This is an Constant expression type
                The type of the constant value is System.Int32
                The value of the constant value is 1
        The False clause for this expression is:
                This expression is a Call expression
                This is a static method call
                The method name is System.Linq.Enumerable.Aggregate
                The Arguments are:
                        This expression is a Call expression
                        This is a static method call
                        The method name is System.Linq.Enumerable.Range
                        The Arguments are:
                                This is an Constant expression type
                                The type of the constant value is System.Int32
                                The value of the constant value is 1
                                This is an Parameter expression type
                                Type: System.Int32, Name: n, ByRef: False
                        This expression is a Lambda expression type
                        The name of the lambda is <null>
                        The return type is System.Int32
                        The expression has 2 arguments. They are:
                                This is an Parameter expression type
                                Type: System.Int32, Name: product, ByRef: False
                                This is an Parameter expression type
                                Type: System.Int32, Name: factor, ByRef: False
                        The expression body is:
                                This binary expression is a Multiply expression
                                The Left argument is:
                                        This is an Parameter expression type
                                        Type: System.Int32, Name: product, ByRef: False
                                The Right argument is:
                                        This is an Parameter expression type
                                        Type: System.Int32, Name: factor, ByRef: False
```

# 예제 라이브러리 확장
이 섹션의 샘플은 식 트리에서 노드를 방문하고 검사하는 핵심 기술을 보여줍니다.      
식 트리에서 노드를 방문하고 액세스하는 핵심 작업에 집중하기 위해 노드 유형을 간소화했습니다.        

첫째, Visitor는 정수인 상수만 처리합니다.                     
마지막 예제에서도 가능한 노드 형식의 하위 집합을 인식합니다.              
여전히 실패하게 만드는 입력값이 존재하며, 이후 확장을 통해 노드 형식을 처리할 수 있습니다.        

둘째, 이 문서에 라이브러리는 데모 및 학습을 위해서 빌드된 것이기 때문에 최적화되지 않았습니다.     
