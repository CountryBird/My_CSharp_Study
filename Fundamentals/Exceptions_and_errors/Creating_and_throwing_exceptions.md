예외에는 `StackTrace`라는 이름의 속성이 있습니다.   
해당 속성에는 콜 스택에 대한 메서드의 이름과, 그 메서드에서 throw된 예외의 정보(파일 이름, 줄 번호)가 포함됩니다.   
```cs
static private void throwMehthod()
{
    throw new Exception();
}
```
```cs
 try
 {
     throwMehthod();
 }
 catch (Exception ex)
 {
     Console.WriteLine(ex.StackTrace);
    //at Program.throwMehthod() in C: \프로그램 경로\Program.cs:line 9
    //at Program.Main(String[] args) in C: \프로그램 경로\Program.cs:line 15
 }
```

예외에는 `Message`라는 이름의 속성도 있습니다.    
예외의 이유 등이 설명되는 문자열입니다.   
```cs
 try
 {
     throwMehthod();
 }
 catch (Exception ex)
 {
     Console.WriteLine(ex.Message);
    //Exception of type 'System.Exception' was thrown.
 }
```

# 예외를 throw할 때 피해야 할 것

## - 단순히 프로그램의 흐름을 제어하고자 예외를 사용하지 말 것.   
: 예외는 오류 조건의 보고 처리를 위한 것이지, 흐름 제어에 사용하지 않습니다.   
_흐름제어에 예외를 사용하면 코드가 비효율적이고 유지보수하기 어려워집니다._    

**흐름을 제어하는데에는 `if`나 `for`와 같은 키워드를 사용하는 것이 바람직합니다.**

## - 예외를 반환값이나 파라미터로 사용하지 말 것.
: 예외를 반환값으로 사용하거나 파라미터로 전달하는 것은 바람직하지 않습니다.    
호출자가 예외가 발생한 것인지 정상적인 결과인지를 알 수 없어 _코드의 가독성을 떨어뜨리며, 예외 처리 로직을 복잡하게 합니다._    

**예외는 발생 시 던져서 처리해야 합니다.**

## - `Exception`, `SystemException`, `NullReferenceExcpetion`, `IndexOutOfRangeException`은 의도적으로 던지지 말 것.
: `Exception`, `SystemException`은 너무 일반적인 예외이기 때문에, **이러한 예외를 던지는 것은 사실상 의미가 없습니다.**    

또한 `NullReferenceException`은 객체가 null일 때 발생하는 예외인데, 차라리 **null을 체크하는 코드를 작성하는 것이 바람직합니다.**

`IndexOutOfRangeException`은 배열이나 컬렉션에서 인덱스가 범위를 벗어나는 예외인데, 이도 마찬가지로 **인덱스를 잘못 참조하지 않도록 범위 체크를 하는 것이 바람직합니다.**

## - 디버그 모드에서만 발생하는 예외를 만들지 말 것.
: 디버그 모드에서만 발생하는 예외를 만들면, _추후 릴리즈 모드에서 다르게 동작하게 되어 일관성이 떨어집니다._   
개발 중에 발생하는 오류를 찾는 것이 목적이라면, 예외를 던지기보다 **`Debug.Assert`를 사용해 검증하는 방법이 더 좋습니다.**

# Task 반환 메서드의 예외
자세한 부분에 대해서는 추후 다루겠지만, 예외에 대해서 짚고 넘어갈 부분이 있습니다.    

`async`를 통해 선언된 메서드(비동기로 처리하고자 하는 메서드)에서 발생한 예외는 작업이 `await`(작업 종료까지 대기) 될 때까지 나타나지 않습니다.   
때문에, `await`이 없는 형태가 되면, 예외가 발생했지만 그대로 사라지는 일이 생길 수 있습니다.

메서드 비동기 부분을 입력하기 전에는 파라미터의 유효성을 검사하고, 해당 예외를 throw 해주는 것이 좋습니다.   
