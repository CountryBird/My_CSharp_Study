CLR은 런타임에 제네릭 타입 정보에 엑세스할 수 있으므로,            
리플렉션을 사용하여 비제네릭 타입과 동일한 방식으로 제네릭 타입의 정보를 얻을 수 있습니다.         

## 제네릭 타입 관련 Reflection
- IsGenericType            
: 해당 타입이 제네릭 타입인지 확인합니다.
 
- GetGenericArguments           
: 제네릭 타입에 전달된 실제 타입 인자를 가져옵니다.

- GetGenericTypeDefinition          
: 현재 생성된 제네릭 타입에서 원본 제네릭 타입 정의를 가져옵니다.

- GetGenericParameterConstraints            
: 타입 매개변수에 설정된 제약 조건을 가져옵니다.

- ContainsGenericParameters            
: 타입이나 외부 타입/메서드에 아직 구체적인 타입이 지정되지 않은 타입 매개변수가 있는지 확인합니다.

- GenericParameterAttributes          
: 타입 매개변수의 특별한 제약 조건을 플래그 형태로 가져옵니다.

- GenericParameterPosition             
: 타입 매개변수가 제네릭 타입 정의에서 몇 번째 위치에 있는지 확인합니다.

- IsGenericParamenter            
: 타입이 제네릭 매개변수인지 확인합니다.

- IsGenericTypeDefinition            
: 타입이 제네릭 타입 정의인지 확인합니다.

- DeclaringMethod             
: 제네릭 타입 매개변수를 정의한 메서드를 가져옵니다.

- MakeGenericType              
: 제네릭 타입 정의에 실제 타입을 넣어 실제 타입을 생성합니다.

## 제네릭 메서드 관련 Reflection
- IsGenericMethod             
: 해당 메서드가 제네릭 메서드인지 확인합니다.

- GetGenericArguments              
: 메서드 제네릭 매개변수나 실제 타입 인자를 가져옵니다.

- GetGenericMethodDefinition            
: 현재 메서드의 제네릭 정의를 가져옵니다.

- ContainsGenericParameters              
: 메서드나 외부 타입/메서드에 아직 지정되지 않은 제네릭 매개변수가 있는지 확인합니다.

- IsGenericMethodDefinition               
: 현재 MethodInfo가 제네릭 메서드 정의인지 확인합니다.

- MakeGenericMethod                 
: 제네릭 메서드 정의에 실제 타입을 넣어 실제 메서드를 생성합니다.     
