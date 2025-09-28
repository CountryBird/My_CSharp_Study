속성을 사용하여 메모리에서 구조체가 레이아웃되는 방식을 변경할 수 있습니다.        

예로, `StructLayout(LayoutKind.Explicit)` 및 `FieldOffset` 특성을 사용해 C/C++의 공용 구조체로 알려진 항목을 만들 수 있습니다.      

`StructLayout(LayoutKind.Explicit)`은 구조체의 필드가 메모리에 어떻게 배치될지 개발자가 지정할 수 있습니다.      
기본적으로 C#의 구조체는 컴파일러가 자동으로 정렬하여 메모리를 배치하지만, 이 옵션을 사용하는 경우 개발자가 지정한 위치에 필드를 둡니다.    

`FieldOffset(n)`은 구조체의 해당 필드가 시작할 메모리 위치를 직접 지정합니다.      

## C/C++의 Union 기능 구현
```cs
[StructLayout(LayoutKind.Explicit)]
struct TestUnion
{
    [FieldOffset(0)] public int i;
    [FieldOffset(0)] public double d;
    [FieldOffset(0)] public char c;
    [FieldOffset(0)] public byte b;
}
```
모든 필드가 메모리의 같은 위치에서 시작되며, 이에 따라 필드들은 서로 메모리를 공유합니다.      
즉, C/C++의 union과 동일한 효과를 낼 수 있습니다.      

## 명시적 메모리 배치
```cs
[StructLayout(LayoutKind.Explicit)]
struct TestExplicit
{
    [FieldOffset(0)]  public long lg;   // 0~7 바이트
    [FieldOffset(0)]  public int i1;    // 0~3 바이트
    [FieldOffset(4)]  public int i2;    // 4~7 바이트
    [FieldOffset(8)]  public double d;  // 8~15 바이트
    [FieldOffset(12)] public char c;    // 12~13 바이트
    [FieldOffset(14)] public byte b;    // 14번째 바이트
}
```
메모리 공유, 세밀한 위치 지정을 동시에 이루어진 예제로,          
저수준 데이터 처리, 하드웨어 메모리 맵핑, 네이티브 라이브러리 데이터 교환 등에 유용합니다.        
