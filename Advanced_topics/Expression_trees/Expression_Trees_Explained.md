표현식 트리는 코드를 정의하는 데이터 구조입니다.            
식 트리는 컴파일러가 코드를 분석하고 컴파일된 출력을 생성하는 데 사용하는 것과 동일한 구조를 기반으로 합니다.             

```cs
var sum = 1 + 2;
```
위의 코드를 식 트리로 분석하면 트리에 여러 노드가 포함됩니다.               
가장 상위의 노드는 _대입이 있는 변수 선언문_, `var sum = 1 + 2` 입니다.                   
변수 선언, 할당 연산자, 우측의 식과 같은 여러 자식 노드가 포함됩니다.               

등호의 오른쪽을 구성하는 식을 좀 더 자세히 살펴봅시다.                  
`1 + 2`는 이진 더하기 식입니다.             
이진 더하기 식에는 더하기 연산자 `+`에 대해 왼쪽 노드에 `1`, 오른쪽 노드에 `2`로 값을 나타내는 자식 노드가 있습니다.          

전체 문은 트리로 구성되며, 루트 노드에서 시작하고 트리의 각 노드로 이동하여 문을 구성하는 코드를 볼 수 있습니다.          
- **할당이 있는 변수 선언 문** `var sum = 1 + 2;`
  - _암시적 변수 타입 선언_ `var sum` 
    - 암시적 var 키워드 `var`
    - 변수 이름 선언 `sum`
      
  - _대입 연산자_ `=`
    
  - _이진 추가 식_ `1 + 2`
    - 왼쪽 피연산자 `1`
    - 더하기 연산자 `+`
    - 오른쪽 피연산자 `2`

위의 트리는 실제 코드에 비해 복잡하게 보일 수 있지만 강력한 효과를 가집니다.             
동일한 프로세스를 사용하여 훨씬 복잡한 식을 분해할 수 있습니다.         

---

```cs
var finalAnswer = this.SecretSauceFunction(
    currentState.createInterimResult(),
    currentState.createSecondValue(1, 2),
    decisionServer.considerFinalOptions("hello")
) + MoreSecretSauce('A', DateTime.Now, true);
```
_식 트리_ 의 구조로 보면 다음과 같습니다.       

- `this.SecretSauceFunction`
  - 트리에서의 노드 종류: MethodCallExpression
  - this 객체의 메서드 호출
- `currentState.createInterimResult()`
  - 트리에서의 노드 종류: MethodCallExpression
  - currentState 객체의 메서드 호출
- `currentState.createSecondValue(1, 2)`
  - 트리에서의 노드 종류: MethodCallExpression + ConstantRxpressions
  - 인자 1, 2는 상수 노드
- `decisionServer.considerFinalOptions("hello")`
  - 트리에서의 노드 종류: MethodCallExpression + ConstantExpressions
  - 문자열 상수 "hello"
- `MoreSecretSauce('A', DateTime.Now, true)`
  - 트리에서의 노드 종류: MethodCallExpression + ConstantExpressions
  - 문자, 시간, 불리어 상수
- `+`
  - 트리에서의 노드 종류: BinaryExpression
  - 두 메서드 호출의 결과를 더함
- `finalAnswer = ...`
  - 트리에서의 노드 종류: AssignmentExpression
  - 최종적으로 변수에 대입
