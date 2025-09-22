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
C#에서 중간 언어, IL에 해당하는 단계에서는 `protected`, `internal` 등의 키워드가 리플렉션 API에 사용되지 않습니다.        

- 리플렉션을 사용하며 internal 메서드를 사용하려면 _IsAssembly_ 를 사용합니다.
- 리플렉션을 사용하며 protected internal 메서드를 사용하려면 _IsFamilyOrAssembly_ 를 사용합니다.

# 속성 작업
속성은 거의 모든 선언에서 사용할 수 있지만, 특정 특성은 그렇지 않은 경우도 있습니다.        
속성은 대괄호 안에 속성 이름을 적어 선언 위에 붙입니다.      

```cs
[Serializable]
public class SampleClass
{
    // Objects of this type can be serialized.
}
```
`Serializable` 속성은 이 클래스가 _직렬화_ 가능함을 표시합니다.                
_직렬화_ 란 객체를 파일, 네트워크 등으로 전송할 수 있는 형태로 변환하는 것을 의미합니다.              

```cs
[System.Runtime.InteropServices.DllImport("user32.dll")]
extern static void SampleMethod();
```
`DllImportAttribute` 속성은 외부 DLL 함수를 호출할 때 사용합니다.         
해당 속성을 통해 C#에서 C/C++ DLL 함수를 직접 호출할 수 있습니다.        

```cs
void MethodA([In][Out] ref double x) { }
void MethodB([Out][In] ref double x) { }
void MethodC([In, Out] ref double x) { }
```
한 선언에 대해 여러 속성을 붙일 수 있습니다.         
`In`과 `Out` 속성은 해당 메서드 파라미터가 입력용인지, 출력용인지 지정하는데                     
여러 속성을 붙임으로써 입출력 둘 다에 사용한다는 것을 명시할 수 있습니다.        

```cs
[Conditional("DEBUG"), Conditional("TEST1")]
void TraceMethod()
{
    // ...
}
```
일부 속성은 같은 선언에 여러 번 적용 가능합니다.        
`Conditional` 속성은 특정 컴파일 심볼이 정의되어 있을 때만 메서드 호출이 컴파일되도록 할 수 있는데,       
이를 여러 번 적용하여 컴파일되는 경우의 수를 늘릴 수 있습니다.          

## 속성 파라미터
속성에도 파라미터를 줄 수 있는데, 파라미터는 크게 _위치 기반_ 과 _이름 기반_ 으로 나뉩니다.          

1. 위치 기반 파라미터
: 속성의 생성자의 파라미터를 의미합니다.
: 항상 첫 번째로 지정하며, 생략할 수 없는 필수 값이 여기에 들어갑니다.

2. 이름 기반 파라미터
: 속성의 public property나 field를 지정할 때 사용합니다.
: 위치 기반 파라미터 뒤에 올 수 있으며, 순서는 자유롭게 사용 가능합니다.
: 대부분 선택 사항이며, 생략하면 기본값을 사용합니다.

```cs
[DllImport("user32.dll")]
[DllImport("user32.dll", SetLastError=false, ExactSpelling=false)]
[DllImport("user32.dll", ExactSpelling=false, SetLastError=false)]
```
해당 코드는 동일한 DllImport 특성을 보여줍니다.       
첫 번째는 위치 기반 파라미터이며, 이 후에 오는 파라미터는 이름 기반 파라미터입니다.        

## 속성 대상
