_식 트리_ 는 일부 코드를 나타내는 데이터 구조입니다.               
식 트리가 나타내는 .NET 코드를 실행하려면 이를 실행 가능한 IL 명령으로 변환해야 합니다.           
식 트리를 실행하면 값이 반환되거나 메서드 호출과 같은 작업만 수행할 수 있습니다.      

람다 식을 나타내는 식 트리는 `LambdaExpression`이거나 `Expression<TDelegate>`가 있고               
이러한 식 트리를 실행하려면 Compile을 호출하여 실행 가능한 대리자를 만든 다음 대리자를 호출합니다.             

식 트리가 람다 식을 나타내지 않는 경우, ` Lambda<TDelegate>(Expression, IEnumerable<ParameterExpression>) `메서드를 호출하여        
원래 식 트리를 본문으로 포함하는 새 람다 식을 만들 수 있습니다.                        

# 함수에 대한 람다 식
