특성, `Attribute`는 _선언적 정보_ 를 코드와 연결하는 강력한 방법을 제공합니다.       
특성과 프로그램 엔터티를 연결한 후에는 리플렉션 기술을 사용하여 런타임에 특성을 쿼리할 수 있습니다.          

특성에는 다음과 같은 속성이 있습니다.               
- 특성은 프로그램에 메타데이터를 추가합니다.
- 특성은 전체 어셈블리, 모듈 또는 클래스 및 속성과 같은 더 작은 프로그램 요소에 적용할 수 있습니다.
- 특성은 메서드 및 속성과 동일한 방식으로 인수를 파라미터를 가질 수 있습니다.
- 특성을 사용하면 프로그램이 리플렉션을 사용하여 다른 프로그램에서 자체 메타데이터 또는 메타데이터 검사가 가능합니다.

# Reflection 작업
Type API는 어셈블리, 모듈 및 타입을 설명합니다.         
리플렉션을 사용하여 타입의 인스턴스를 동적으로 만들거나, 타입을 기존 게체에 바인딩하거나,            
기존 개체에서 형식을 가져와 해당 메서드를 호출하거나 해당 필드 및 속성에 액세스할 수 있습니다.       

다음은 `GetType()` 메서드를 사용한 간단한 리플렉션 예제입니다.       
```cs
// Using GetType to obtain type information:
int i = 42;
Type type = i.GetType();
Console.WriteLine(type);

// Output
// System.Int32
```

다음 예제에서는 리플렉션을 사용하여 어셈블리의 전체 이름을 가져옵니다.
```cs
// Using Reflection to get information of an Assembly:
Assembly info = typeof(int).Assembly;
Console.WriteLine(info);

// Output
// System.Private.CoreLib, Version=7.0.0.0, Culture=neutral, PublicKeyToken=7cec85d7bea7798e
```

# IL의 키워드 차이
