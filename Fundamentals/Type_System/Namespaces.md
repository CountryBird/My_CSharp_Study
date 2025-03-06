네임스페이스는 C#에서 크게 2가지 개념으로 사용할 수 있습니다.

## 1. 타 언어의 라이브러리와 유사한 개념 
```cs
using System;
Console.WriteLine("Hello World!");
```
콘솔 창에 출력하는 `WriteLine()` 메서드는, `System` 네임스페이스의 `Console` 클래스에 포함되어 있습니다.

이와 유사하게, .NET은 네임스페이스를 사용하여 여러 클래스를 구현했으며 이를 이용해 타 언어의 라이브러리를 불러오듯이 사용할 수 있습니다.

## 2. 대규모 프로그래밍 프로젝트에서 클래스/메서드의 범위 제어
```cs
// Program2.cs
namespace program2
{
	class Program2
	{
		public void method()
		{
			Console.WriteLine("Program2의 메서드");
		}
	}
}
```
다음과 같이 임의의 네임스페이스로 설정한 코드가 있다고 가정하면,
```cs
// Program.cs
using program2;
class Program
{
    private static void Main(string[] args)
    {
        Program2 p = new Program2();
        p.method(); // Program2의 메서드
    }

}
```
`using`을 통해 네임스페이스를 불러 오는 것으로 다른 파일에서도 해당 코드를 사용할 수 있습니다.

이와 같은 방식으로 대규모 프로젝트에서 클래스/메서드의 범위를 제어할 수 있습니다.

**결론적으로 네임스페이스의 기본 목적은 코드의 구조화와 충돌 방지인데, 1번은 "외부 코드"를, 2번은 "내부 코드"를 관리하는 차이만 있을 뿐, 그 핵심 개념은 비슷하다고 볼 수 있습니다.**

# using 키워드
`using` 키워드는 네임스페이스에서 모든 형식을 가져오는 역할을 합니다.
추가적으로 적용할 수 있는 한정자는 크게 `global`과 `static`가 있습니다.

## global
```cs
global using System;
```
한 파일에서 다음과 같이 `using`을 사용하면,
```cs
class Program2
{
    public void method()
    {
        Console.WriteLine();
    }
}
```
같은 프로젝트 내의 파일은 따로 네임스페이스를 가져오지 않아도 가져온 것 처럼 사용할 수 있습니다.

### global::
`global::`은 `global`과 유사하지만 사용 방향이 조금 다릅니다.
```cs
using System;

class A
{
    public static string x = "global";
}
class Program
{
    private static void Main(string[] args)
    {
        string A = "local";
        Console.WriteLine(A); // local
        Console.WriteLine(A.x); // 에러
        Console.WriteLine(global::A.x); // global
    }
}
```
전역으로 존재하는 _클래스 A_ 가 있고, Main 내부에 지역으로 존재하는 _문자열 A_ 가 있습니다.

단순히 지역 변수를 출력하는 것은 가능하지만, 그 다음 줄과 같이 코드를 작성하는 경우 A를 문자열이라 판단해 x에 접근할 수 없습니다.

이러한 경우 현재 작성자가 의미하는 바가 전역임을 코드에게 알리는 키워드가 global::입니다.

## static
```cs
using static System.Console;
```
한 파일에서 다음과 같이 `using`을 사용하면,

```cs
class Program
{
    private static void Main(string[] args)
    {
        WriteLine("Hello World!");
    }
}
```
해당 파일에서는 따로 형식 이름을 지정해주지 않고 참조가 가능해집니다.

편리하고 가독성이 좋아진다는 장점이 있지만,

정적 멤버에만 사용이 가능하다는 단점과, 동일한 이름의 멤버를 가져왔을 때 충돌이 발생할 수 있다는 단점이 있습니다.


## using 별칭
```cs
using myMath = System.Math;
class Program
{
    private static void Main(string[] args)
    {
        Console.WriteLine(myMath.Sqrt(4));
    }
}
```
다음과 같이 원하는 네임스페이스에 대해 별칭을 지정하여 사용할 수 있습니다.

`using` 별칭은 다른 `using` 지시문 선언에 사용할 수 없습니다.
