.NET 런타임에는 식 트리에서 작동하는 많은 클래스 목록이 있습니다.         
`System.Linq.Expressions`에서 전체 리스트를 볼 수 있습니다.        

# System.Linq.Expression 및 파생 타입
식 트리 작업의 복잡성 중 하나는 프로그램의 여러 곳에서 다양한 종류의 표현이 존재한다는 점입니다.           

할당 식을 생각해 보았을 때,             
할당의 오른쪽에는 상수, 변수, 메서드 호출 식 등이 올 수 있습니다.           

이러한 언어의 유연성은 식 트리를 보았을 때 트리의 노드에서 다양한 식 형식이 발생할 수 있음을 의미합니다.      

기본 Expression 클래스에는 `NodeType`이 포함되어 있는데       
열거 가능한 타입인 `ExpressionType`으로 반환됩니다.     

노드의 타입을 알고 나면 해당 타입으로 캐스팅하고, 특정 작업을 수행할 수 있게 됩니다.             
또한 해당 식의 특정 속성을 사용할 수 있게 됩니다.      

이 코드는 변수 액세스 식에 대한 변수의 이름을 출력하는 코드입니다.             
노드 타입을 확인한 다음, 캐스팅을 하여 특정 타입의 속성을 확인하는 방법을 확인할 수 있습니다.           
```cs
Expression<Func<int, int>> addFive = (num) => num + 5;

if (addFive is LambdaExpression lambdaExp)
{
    var parameter = lambdaExp.Parameters[0];  -- first

    Console.WriteLine(parameter.Name);
    Console.WriteLine(parameter.Type);
}
```

# 표현식 트리 만들기
클래스에는 `System.Linq.Expression` 식을 만드는 많은 _정적 메서드_ 도 포함되어 있습니다.            
이 메서드들은 인수를 사용해서 자식 노드를 만들어 식 노드를 만듭니다.             

이를 반복하여 리프 노드로부터 식을 빌드할 수 있고,               
아래의 코드는 Add 식 트리를 만드는 코드입니다.       

```cs
// Addition is an add expression for "1 + 2"
var one = Expression.Constant(1, typeof(int));
var two = Expression.Constant(2, typeof(int));
var addition = Expression.Add(one, two);
```

# API 탐색
C# 언어의 거의 모든 구문 요소에 매팽되는 식 노드 형식이 있습니다.            
각 형식에는 해당 형식의 언어 요소에 대한 특정 메서드가 있습니다.                  

식 트리를 사용할 때 외울 것이 많고 복잡해 보이지만,                  
세 가지 기본적인 방향을 중심으로 접근하면 효과적으로 개념을 습득할 수 있습니다.                  

1. `ExpressionType` 열거형 살펴보기            
: `ExpressionType`은 C#의 각 문법 요소가 어떤 종류의 노드인지 정의해 놓은 enum입니다.               
이를 살펴봄으로써 식 트리에서 어떤 종류의 노드를 다룰 수 있는지 알 수 있고, 처리 로직을 결정하는데 도움을 줍니다.

2. `Expression` 클래스의 정적 메서드 사용하기
: `System.Linq.Expressions.Expression` 클래스는 식 트리를 구성할 때 사용하는 팩토리 메서드를 제공합니다.              
이 메서드들은 트리를 노드 단위로 조립하도록 설계되어 있으며, 따라서 트리를 생성할 때 사용할 수 있습니다.              

3. `ExpressionVisitor` 클래스로 트리 수정하기
: `ExpressionVisitor`는 표현식 트리를 탐색하면서 수정하거나 새로운 트리를 생성할 때 사용하는 도구입니다.
기존 트리를 기반으로 변경된 트리를 만들고 싶을 때 사용하는 핵심 도구입니다.                     
