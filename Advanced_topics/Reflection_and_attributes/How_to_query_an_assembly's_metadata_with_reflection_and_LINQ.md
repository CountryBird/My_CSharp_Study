.NET 리플렉션 API를 사용하여 .NET 어셈블리에서 메타데이터를 검사하고                 
해당 메타데이터에서의 형식, 매개 변수 컬렉션 등을 만들 수 있습니다.               

또한 이러한 컬렉션은 `IEnumerable<T>` 인터페이스를 지원하므로 _LINQ_ 를 사용하여 쿼리할 수 있습니다.        

다음 예제에서는 LINQ를 리플렉션과 함께 사용하여 지정된 조건과 일치하는 메서드에 대한 특정 메타데이터를 검색하는 방법을 보여 줍니다.       
```cs
Assembly assembly = Assembly.Load("System.Private.CoreLib, Version=7.0.0.0, Culture=neutral, PublicKeyToken=7cec85d7bea7798e");
var pubTypesQuery = from type in assembly.GetTypes()
                    where type.IsPublic
                    from method in type.GetMethods()
                    where method.ReturnType.IsArray == true
                        || (method.ReturnType.GetInterface(
                            typeof(System.Collections.Generic.IEnumerable<>).FullName!) != null
                        && method.ReturnType.FullName != "System.String")
                    group method.ToString() by type.ToString();

foreach (var groupOfMethods in pubTypesQuery)
{
    Console.WriteLine($"Type: {groupOfMethods.Key}");
    foreach (var method in groupOfMethods)
    {
        Console.WriteLine($"  {method}");
    }
}
```
이 예제에서는 Assembly.GetTypes 메서드를 사용하여 public 타입만 반환되도록 지정된 어셈블리의 형식 배열을 반환합니다.        
각 public 타입에 대해 하위 쿼리는 Type.GetMethods 호출에서 반환되는 MethodInfo 배열을 사용하여 생성됩니다.          
이러한 결과는 반환 타입이 배열이거나 IEnumerable<T>를 구현하는 타입인 메서드만 반환하도록 필터링됩니다.            
마지막으로, 이러한 결과는 타입 이름을 키로 하여 그룹화됩니다.          
