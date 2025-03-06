# My_CSharp_Study

Microsoft Learn의 C# 설명서를 참고하였으며, 궁금증과 코멘트를 추가로 작성하였습니다.

## Hello World

```cs
//이 부분은 한 줄 주석입니다.

/*이 부분은
   여러 줄 주석입니다.*/
```
주석은 코드 내에 포함되지 않습니다.
`//`는 해당 줄에 대해 주석을 적용하며, `ctrl + /`로 적용이 가능합니다.
`/*`와 `*/` 사이에 있는 코드는 주석이 적용되며, `ctrl + shift + /`로 적용이 가능합니다.

```cs
Console.Write("Hello?");
Console.WriteLine("Hello, World!");
```
`System` 네임스페이스에는 콘솔 창에 출력할 수 있는 `Console` 메서드를 지원합니다.

`Console.Write()`는 해당 문자열을 출력하며, `Console.WriteLine()`은 이에 줄바꿈을 추가합니다.

네임스페이스는 추후 자세히 다룰 예정입니다.
