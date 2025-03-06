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
using을 통해 네임스페이스를 불러 오는 것으로 다른 파일에서도 해당 코드를 사용할 수 있습니다.

이와 같은 방식으로 대규모 프로젝트에서 클래스/메서드의 범위를 제어할 수 있습니다.

**결론적으로 네임스페이스의 기본 목적은 코드의 구조화와 충돌 방지인데, 1번은 "외부 코드"를, 2번은 "내부 코드"를 관리하는 차이만 있을 뿐, 그 핵심 개념은 비슷하다고 볼 수 있습니다.**
# using
