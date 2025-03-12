`패턴 매칭`은 식에 특정 특징이 있는지 테스트하는 기법입니다.  
`is`와 `switch`가 이를 지원합니다.

# null 검사
```cs
int? maybe = 1;
int? maybe2 = null;
int maybe3 = null; // 에러

if(maybe is int)
{
   Console.WriteLine(maybe);
}
else
{
   Console.WriteLine("null");
}
```
`?`는 타입 뒤에 붙는 키워드로, null을 허용한다는 의미입니다.  
`객체 is 타입`은 객체가 해당 타입인지 확인하고 True, False를 반환합니다.  


C# 7.0부터, 클래스 객체와 같은 경우 타입 캐스팅을 사용하지 않고 `객체 is 타입 새로운_객체`와 같은 형식으로 적을 수 있게 되었습니다.
```cs
 class Shape
 {
     public virtual void draw()
     {
         Console.WriteLine("just draw");
     }
 }
 class Circle : Shape
 {
     public override void draw()
     {
         Console.WriteLine("draw circle");
     }
 }

```
```cs
Shape s = new Circle();
if (s is Circle c)
{
    c.draw();
}
/*
if(s is Circle)
{
    Circle c = (Circle)s;
    c.draw();
}
*/
else
{
    Console.WriteLine("null");
}
```
if문과 주석 안의 if문은 같은 동작을 나타내며 결과 또한 같은 것을 확인할 수 있습니다.  
```cs
int? maybe = null;
if (maybe is not null)
{
    Console.WriteLine(maybe);
}
else
{
    Console.WriteLine("null");
}
```
응용하여, `not` 키워드를 사용해 더 유연한 조건 작성도 가능합니다.

# 이산적인 값 비교
```cs
static public string grade(int score) =>
    score switch
    {
        > 90 => "A",
        > 70 => "B",
        > 50 => "C",
        > 30 => "D",
        _ => "F"
    };
```
이 코드에서 `=>`는 2가지 용도로 쓰입니다.
- 람다 표현식
: `매개변수 => 리턴값`의 형태로 하여금 쓸 수 있는 람다 표현식은 코드를 더 간결하게 해줍니다.  
코드를 예로 들면, int인 score를 파라미터로 받아 string을(왜 string인지는 후술) 리턴하는 람다 grade 함수를 만드는 것 입니다.  
- 스위치 표현식
: C# 8.0부터 기존에 switch(객체) {case: ~ break; case: ~ break} 형태에서 벗어나 쓰이는 방법입니다.
case, return, break 등의 키워드가 너무 많이 반복되는 것을 없애 코드가 더 간결해집니다.
`조건 => 결과`의 형태로 나타나기에, 코드에서는 string 값이 나옵니다.

마지막으로, switch문의 `_`는 디폴트 값(모든 case를 만족하지 않는 경우 사용)을 의미합니다.

# 여러 입력
지금까지의 패턴은 하나의 입력을 전제하고 설명하였습니다. 개체의 여러 속성을 한 번에 검사하는 것도 가능합니다.  
```cs
public class MyClass
{
    public int myInt;
    public bool plus;
    public MyClass(int myInt, bool plus)
    {
        this.myInt = myInt;
        this.plus = plus;
    }   
}
static public string myMethod(MyClass myClass) =>
    myClass switch
    {
        { myInt: > 10, plus: true } => "big plus",
        { myInt: < 10, plus: true } => "small plus",
        { plus:false } => "minus",
         null => "null"
    };
```
위와 같이, `{}`를 통해 여러 속성을 전제하고 switch문을 작성할 수도 있습니다. 
```cs
MyClass myClass1 = new MyClass(20, true);
MyClass myClass2 = new MyClass(1, true);
MyClass myClass3 = new MyClass(2, false);
MyClass myClass4 = null;

Console.WriteLine(myMethod(myClass1)); // big plus
Console.WriteLine(myMethod(myClass2)); // small plus
Console.WriteLine(myMethod(myClass3)); // minus
Console.WriteLine(myMethod(myClass4)); // null
```

# 리스트 패턴
리스트나, 배열과 같은 시퀀스 형태의 모든 요소에 일일이 패턴을 적용시킬 필요는 없습니다.

```cs
string[] person = { "male", "Korea","Kim", "19" };
Console.WriteLine(
    person switch
    {
        ["male", _,..] => "male"
    });
Console.WriteLine(
    person switch
    {
        ["male", _, _,var age] => age
    });
```
`..`는 이 후의 문자를 배열로 묶어 하나로 인식합니다. 때문에 원래 패턴 매칭에서는 요소의 수를 맞춰주어야 하지만 해당 키워드를 사용하면 생략이 가능해집니다.    

`_`는 무시, 즉 해당 부분은 매칭에 참여시키지 않겠다는 의미 입니다.  
