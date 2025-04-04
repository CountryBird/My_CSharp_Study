`무시 항목`은 코드에서 의도적으로 사용하지 않는 의도를 컴파일러에게 전달하는 표시자 입니다.   
또한, 코드에서 변수를 사용하지 않는다는 것을 명확히 보이게 하기 때문에, 가독성 향상의 효과도 있습니다.  

무시 항목임을 지정하려면 `_`을 이름으로 할당합니다.

# 튜플 및 개체 분해
```cs
 static (int,string) tupleMethod()
 {
     return (1, "hello");
 }
```
int와 string의 튜플을 반환하는 메서드가 있다고 가정하고,
```cs
 var (_,s) = tupleMethod();
 Console.WriteLine(s); // hello
```
다음과 같이 코드를 작성하면 원하지 않는 값은 무시한 채 정상적으로 코드를 실행할 수 있습니다.  

# switch를 사용한 패턴 일치
무시 항목 키워드는 switch문에서도 사용 가능합니다.
```cs
 static void MyMethod(string arg) =>
     Console.WriteLine(arg switch
     {
         "hello" => "World!",
         null => "null",
         _=> "Anything"
     });
```
```cs
 MyMethod("hello"); // World!
 MyMethod(null); // null
 MyMethod("bye"); // Anything
```
모든 식은 `_`와 일치하기 때문에,   
"hello"도 아니며 null도 아닌 값이 `arg`로 들어오면 "Anything"을 출력합니다.

# out 매개변수를 사용한 메서드 호출
## out 키워드
`out` 키워드는 변수를 참조로 전달하고자 할 때 사용하는 키워드입니다. (원래 변수는 값 기반)   
값을 참조를 통해 연결시키는 형태이기 때문에 이름 그대로, 값을 밖으로 내보내는 역할을 할 수 있습니다.  
```cs
static void MyMethod(out int i)
{
    i = 1;
}
```
위와 같이 out 키워드를 사용한 메서드가 있다고 가정하면,
```cs
 int num;
 MyMethod(out num);
 Console.WriteLine(num); // 1
```
따로 초기화를 하지 않은 num 변수가 MyMethod의 out 키워드를 통해 참조 값이 넘어가게 되고,  
MyMethod 내에서 1로 초기화를 하고 나온 상태이기 때문에 1이 출력되는 것을 확인할 수 있습니다.

## out 키워드와 무시 항목
out 키워드로 선언된 변수는 참조의 형태로 전달되기 때문에, 무시 항목을 적용할 수 있습니다.
```cs
static void MyMethod(string s,out int i)
{
    i = 1;
    Console.WriteLine(s);
}
   
private static void Main(string[] args)
{
    int num;
    MyMethod("hello",out _); // hello
}
```
위와 같이 무시 항목을 적용해도 에러 없이 작동하는 것을 확인할 수 있습니다.

## 독립 실행형 무시 항목
```cs
public void method1(string arg)
{
    _ = arg ?? throw new ArgumentNullException(nameof(arg));
}

public void method2(string arg) 
{ 
    if(arg is null) 
        throw new ArgumentNullException(nameof(arg));
}
```
위와 같이 무시항목을 사용하여 코드를 간결하게 표현하는 것이 가능합니다.  
(`??`은 널 병합 연산자라고 하며, 왼쪽 피연산자가(여기서 arg) null이면 오른쪽 피연산자, 그렇지 않으면 그대로 반환하는 역할을 합니다.)  

이러한 방법을 이용해서, 필요 없는 값에 대한 의도적인 무시를 해서 컴파일 경고를 띄우지 않을 수 있습니다.

## 유효한 식별자 _
대개 `_`를 무시 항목의 의미로 쓰지만, 일반적인 변수처럼도 사용할 수 있습니다.
```cs
public void method1(int _)
{
    _ = 30;
    Console.WriteLine(_);
}
```
물론, 이러한 경우 하나의 변수처럼 쓰이는 모양이 되기 때문에 무시 항목의 의미로써 사용할 수는 없습니다.
```cs
public void method2(int _)
{
    var _ = 30; // 에러
}
```
