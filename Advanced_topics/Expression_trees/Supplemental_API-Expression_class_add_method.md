_Add_ 메서드는 _BinaryExpression_ 속성이 구현 메서드로 설정된 _Method_ 를 반환합니다.           
_Type_ 은 노드의 형식으로 설정됩니다.        
노드가 해제되면 _IsLifted_, _IsLiftedToNull_ 속성은 둘 다 true 입니다. 
_IsEditable_ 속성은 true 입니다.          

# 구현 방법
- Left와 Right의 Type 속성이 덧셈 연산자를 오버로드하는 사용자 정의 타입일 경우, 해당 메서드를 나타내는 MethodInfo가 구현 메서드입니다.
- 그렇지 않다면, Left와 Right의 Type은 숫자형이며, 구현 메서드는 null입니다.

# 노드 타입 및 Lifted vs non-Lifted
- 구현 메서드가 `null`이 아닌 경우:                
  - 왼쪽/오른쪽 타입이 메서드의 매개변수 타입과 맞는 경우:           
    메서드의 반환 타입을 그대로 사용하고             
    이를 non-Lifted 되었다고 봅니다.         

  - 왼쪽/오른쪽 타입 둘 중 하나가 Nullable이고 실제 값 타입이 메서드 인자와 같은 경우나
   메서드 반환 타입이 일반 값 타입일 경우:             
   메서드 반환 타입을 Nullable로 감싼 타입을 사용하고            
   이를 Lifted 되었다고 봅니다.

- 구현 메서드가 없는 경우
  - 둘 다 Nullable이 아닐 때, 타입을 그대로 사용합니다.
  - 둘 다 Nullable일 때, 타입을 Nullable 타입으로 사용합니다.             
