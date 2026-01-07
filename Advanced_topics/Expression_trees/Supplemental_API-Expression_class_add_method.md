_Add_ 메서드는 _BinaryExpression_ 속성이 구현 메서드로 설정된 _Method_ 를 반환합니다.           
_Type_ 은 노드의 형식으로 설정됩니다.        
노드가 해제되면 _IsLifted_, _IsLiftedToNull_ 속성은 둘 다 true 입니다. 
_IsEditable_ 속성은 true 입니다.          

# 구현 방법
- Left와 Right의 Type 속성이 덧셈 연산자를 오버로드하는 사용자 정의 타입일 경우, 해당 메서드를 나타내는 MethodInfo가 구현 메서드입니다.
- 그렇지 않다면, Left와 Right의 Type은 숫자형이며, 구현 메서드는 null입니다.

#  
