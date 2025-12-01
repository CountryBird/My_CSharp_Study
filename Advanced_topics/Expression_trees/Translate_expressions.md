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
