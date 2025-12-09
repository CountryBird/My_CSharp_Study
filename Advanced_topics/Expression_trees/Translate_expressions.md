식 트리의 수정된 복사본을 빌드하는 동안 식 트리의 각 노드를 방문하는 방법에 대해 알아봅시다.             
생성된 알고리즘을 변경하고, 로그 추가, 메서드 호출 가로채기, 추적 등 여러 용도로 사용할 수 있습니다.           

```cs
private static Expression ReplaceNodes(Expression original)
{
    if (original.NodeType == ExpressionType.Constant)
    {
        return Expression.Multiply(original, Expression.Constant(10));
    }
    else if (original.NodeType == ExpressionType.Add)
    {
        var binaryExpression = (BinaryExpression)original;
        return Expression.Add(
            ReplaceNodes(binaryExpression.Left),
            ReplaceNodes(binaryExpression.Right));
    }
    return original;
}
```
해당 코드는 상수 노드를 찾으면 해당 노드에 10을 곱한 값의 노드로 변환하는 코드입니다.           

```cs
var one = Expression.Constant(1, typeof(int));
var two = Expression.Constant(2, typeof(int));
var addition = Expression.Add(one, two);
var sum = ReplaceNodes(addition);
var executableFunc = Expression.Lambda(sum);

var func = (Func<int>)executableFunc.Compile();
var answer = func();
Console.WriteLine(answer);
```
새 트리 빌드는 기존 트리의 노드를 방문하고 새 노드를 만들어 트리에 삽입하는 조합입니다.               

새로운 트리를 만드는 것은 기존 트리의 노드를 방문하고, 새 노드를 생성하여 트리에 삽입하는 과정의 조합입니다.            

기존 트리의 노드를 두 트리에서 모두 사용할 수 있는 이유는 기존 트리의 노드는 수정할 수 없기 때문입니다.             
노드를 재사용하면 메모리를 크게 절약할 수 있으며, 동일한 노드는 트리 전체, 또는 여러 표현식 트리에서 반복하여 사용할 수 있습니다.       

# 트래버스 수행 및 추가 작업 실행
덧셈에서의 부분 합계 반환 코드를 작성해봅시다.          
상수 식은 값을 가지며, 덧셈 식의 경우 트리 순회 후 왼쪽/오른쪽의 피연사자의 합이 결과로 생성됩니다.          

```cs
var one = Expression.Constant(1, typeof(int));
var two = Expression.Constant(2, typeof(int));
var three = Expression.Constant(3, typeof(int));
var four = Expression.Constant(4, typeof(int));
var addition = Expression.Add(one, two);
var add2 = Expression.Add(three, four);
var sum = Expression.Add(addition, add2);

// Declare the delegate, so you can call it
// from itself recursively:
Func<Expression, int> aggregate = null!;
// Aggregate, return constants, or the sum of the left and right operand.
// Major simplification: Assume every binary expression is an addition.
aggregate = (exp) =>
    exp.NodeType == ExpressionType.Constant ?
    (int)((ConstantExpression)exp).Value :
    aggregate(((BinaryExpression)exp).Left) + aggregate(((BinaryExpression)exp).Right);

var theSum = aggregate(sum);
Console.WriteLine(theSum);
```
이 코드는, 첫 번째 검색에서 자식을 방문합니다.           
상수 노드가 발견되면 상수의 값을 반환하며 더하기 노드를 이를 통한 합계 계산 값을 볼 수 있습니다.          

다음은 추적 정보를 포함하는 코드인 `Aggreagate`의 업데이트 버전입니다.       
```cs
private static int Aggregate(Expression exp)
{
    if (exp.NodeType == ExpressionType.Constant)
    {
        var constantExp = (ConstantExpression)exp;
        Console.Error.WriteLine($"Found Constant: {constantExp.Value}");
        if (constantExp.Value is int value)
        {
            return value;
        }
        else
        {
            return 0;
        }
    }
    else if (exp.NodeType == ExpressionType.Add)
    {
        var addExp = (BinaryExpression)exp;
        Console.Error.WriteLine("Found Addition Expression");
        Console.Error.WriteLine("Computing Left node");
        var leftOperand = Aggregate(addExp.Left);
        Console.Error.WriteLine($"Left is: {leftOperand}");
        Console.Error.WriteLine("Computing Right node");
        var rightOperand = Aggregate(addExp.Right);
        Console.Error.WriteLine($"Right is: {rightOperand}");
        var sum = leftOperand + rightOperand;
        Console.Error.WriteLine($"Computed sum: {sum}");
        return sum;
    }
    else throw new NotSupportedException("Haven't written this yet");
}
```
이에 따른 출력은 다음과 같습니다.      
```console
10
Found Addition Expression
Computing Left node
Found Addition Expression
Computing Left node
Found Constant: 1
Left is: 1
Computing Right node
Found Constant: 2
Right is: 2
Computed sum: 3
Left is: 3
Computing Right node
Found Addition Expression
Computing Left node
Found Constant: 3
Left is: 3
Computing Right node
Found Constant: 4
Right is: 4
Computed sum: 7
Right is: 7
Computed sum: 10
10
```

## 수정된 복사본 만들기
```cs
public class AndAlsoModifier : ExpressionVisitor
{
    public Expression Modify(Expression expression)
    {
        return Visit(expression);
    }

    protected override Expression VisitBinary(BinaryExpression b)
    {
        if (b.NodeType == ExpressionType.AndAlso)
        {
            Expression left = this.Visit(b.Left);
            Expression right = this.Visit(b.Right);

            // Make this binary expression an OrElse operation instead of an AndAlso operation.
            return Expression.MakeBinary(ExpressionType.OrElse, left, right, b.IsLiftedToNull, b.Method);
        }

        return base.VisitBinary(b);
    }
}
```
이 클래스는 `ExpressionVisitor`를 상속하며 `AND` 조건을 나타내기 위해 특수화된 클래스입니다.           

위 클래스의 동작을 통해 `AND` 조건은 `OR`로 변경됩니다.            
`VisitiBinary`를 통해 변경되는데, `AND` 조건인 경우 `OR`로 변경되고,          
그렇지 않은 경우 기본 클래스의 구현 형식을 따라갑니다.              

Main 메서드에 다음 코드를 추가하여 식 트리를 만들고 수정할 수 있게 됩니다.        
```cs
Expression<Func<string, bool>> expr = name => name.Length > 10 && name.StartsWith("G");
Console.WriteLine(expr);

AndAlsoModifier treeModifier = new AndAlsoModifier();
Expression modifiedExpr = treeModifier.Modify((Expression)expr);

Console.WriteLine(modifiedExpr);

/*  This code produces the following output:

    name => ((name.Length > 10) && name.StartsWith("G"))
    name => ((name.Length > 10) || name.StartsWith("G"))
*/
```

# 더 알아보기
본 샘플에서 식 트리가 나타내는 알고리즘을 트래버스하고 해석하는 것을 보았습니다.            
식 트리는 코드 집합을 검사하고, 해당 코드를 변경하고, 변경된 버전을 실행하는데 도움을 줍니다.        

식 트리는 불변이므로 기존 식 트리의 구성 요소를 사용하여 새 식 트리를 만드는 형태로 진행됩니다.        
이 때 노드를 다시 사용하는 것으로 수정된 식 트리에 필요한 메모리 양이 최소화됩니다.      
