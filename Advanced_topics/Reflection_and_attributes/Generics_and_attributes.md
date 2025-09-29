C#에서 속성은 클래스, 메서드, 필드, 프로퍼티 등에 메타데이터를 붙이는 방법입니다.            
이를 제네릭 타입과 조합하여 사용하면 몇 가지 제약이 있습니다.     

우선 용어 정리를 하고 넘어가자면,       
_열린 제네릭 타입_ 은 타입 인자를 아직 지정하지 않은 상태를 의미합니다.      
예로 `Dictionary<TKey, TValue>` 형태가 있습니다.        

_구체화된 닫힌 제네릭 타입_ 은 모든 타입 인자를 지정한 상태를 의미합니다.        
예로 `Dictionary<string, object>` 형태가 있습니다.       

_부분 구체화된 제네릭 타입_ 은 일부 타입 인자만 지정한 상태를 의미합니다.      
예로 `Dictionary<string, TValue>` 형태가 있으며, **이 형태는 속성에서 사용할 수 없습니다.**             

_제한 없는 제네릭 타입_ 은 타입 인자 자리는 있지만 지정하지 않은 상태를 의미합니다.       
예로 `Dictionary<,>` 형태가 있습니다.       

# 속성에서 제네릭 타입을 참조하는 방법
```cs
class CustomAttribute : Attribute
{
    public object? info;
}
```
`typeof()`를 사용해 `info`에 타입 정보를 넣을 수 있습니다.       

```cs
public class GenericClass1<T> { }

[CustomAttribute(info = typeof(GenericClass1<>))]
class ClassA { }

public class GenericClass2<T, U> { }

[CustomAttribute(info = typeof(GenericClass2<,>))]
class ClassB { }
```
`GenericClass1`, `GenericClass2`와 같이 타입을 지정하지 않는 형태는    
,를 통해 개수를 맞추는 방법만으로 사용이 가능합니다.       

```cs
public class GenericClass3<T, U, V> { }

[CustomAttribute(info = typeof(GenericClass3<int, double, string>))]
class ClassC { }
```
`GenericClass3`와 같이 모든 타입 인자를 채운 형태도 사용이 가능합니다.     

```cs
[CustomAttribute(info = typeof(GenericClass3<int, T, string>))]  // Error CS0416
class ClassD<T> { }
```
하지만 부분적으로 타입 인자를 채운 형태는 사용이 불가능합니다.       

--- 

C# 11부터는, 제네릭 자체가 Attribute가 되는 것도 가능합니다.       
```cs
public class CustomGenericAttribute<T> : Attribute { }
```
