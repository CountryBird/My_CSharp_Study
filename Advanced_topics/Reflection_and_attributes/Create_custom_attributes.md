메타데이터에서 속성 정의를 빠르고 쉽게 할 수 있도록 `Attribute` 클래스를 지원하는데,            
이를 이요하여 고유한 사용자 지정 특성을 만들 수 있습니다.        

```cs
[System.AttributeUsage(System.AttributeTargets.Class |
                       System.AttributeTargets.Struct,
                       AllowMultiple = true)  // Multiuse attribute.
]
public class AuthorAttribute : System.Attribute
{
    string Name;
    public double Version;

    public AuthorAttribute(string name)
    {
        Name = name;

        // Default value.
        Version = 1.0;
    }

    public string GetName() => Name;
}
```
`AuthorAttribute`는 `System.Attribute`에서 파생되었기 때문에 사용자 정의 특성 클래스라고 할 수 있습니다.         

`AuthorAttribute`는 클래스 이름이며, Attribute 접미사를 관례로 붙여 사용합니다.           
해당 접미사를 붙인 경우에는 컴파일러가 해당 부분을 인식하여 `[Author]` 만으로도 사용할 수 있게 지원합니다.         

또한 `AttributeUsage`를 명시해 줌으로써 해당 속성이 클래스와 구조체에만 적용 가능하다는 것을 설정할 수 있습니다.     

`AllowMultiple = true`로 설정하면, 같은 타입에 여러 번 적용 가능하게도 설정 가능합니다.     

생성자에 매개변수를 `name`으로 설정함으로 `name`이 해당 특성의 위치 기반 매개변수 역할을 한다는 것을 설정할 수 있고,       
`Version` 필드는 그렇지 않음으로써 이름 기반 매개변수 역할을 한다는 것을 설정할 수 있습니다.      

```cs
[Author("P. Ackerman"), Author("R. Koch", Version = 2.0)]
public class ThirdClass
{
    // ...
}
```
다음과 같이 사용할 수 있습니다.   
