# 언어 버전
C# 컴파일러는 .NET SDK의 일부인데,        
이에 따라 컴파일러는 프로젝트에 대해 .NET 버전에 맞는 C# 언어 버전을 선택합니다.        
SDK 버전이 선택한 프레임워크보다 큰 경우 컴파일러에서 더 큰 언어 버전을 사용할 수 있습니다.             
또한 원한다면, 프로젝트에서 _LangVersion_ 요소를 설정하여 기본값을 변경할 수 있습니다.  

LangVersion 요소를 latest로 설정하는 것은 권장되지 않는데,       
latest는 이름 그대로 설치된 컴파일러가 최신 버전을 사용하다는 의미를 가집니다.       
이는 머신마다 변경될 수 있어 빌드를 불안정하게 만들 가능성이 있습니다.    

# 라이브러리 작성
공용 .NET 라이브러리를 만든 개발자라면 새로운 업데이트를 출시해야 하는 상황을 겪습니다.             
이에 따른 새 릴리즈를 만들 때 다음과 같은 몇 가지 사항을 고려해야 합니다.    

## 의미론적 버전 관리
특정 중요 시점 이벤트를 나타내기 위해 라이브러리의 버전에 적용되는 명명 규칙으로,             
라이브러리에 제공하는 버전 정보를 통해 개발자는 프로젝트 간의 호환성을 나타낼 수 있습니다.          

가장 기본적이고 통상적으로 사용되는 방법은 `MAJOR.MINOR.PATCH` 형식입니다.     
- `MAJOR`: 호환되지 않는 API 변경 사항이 있을 때 번호가 증가합니다.
- `MINOR`: 이전 버전과의 호환성은 유지하면서, 기능이 추가될 때 번호가 증가합니다.
- `PATCH`: 하위 호환성 버그 등을 수정했을 때 번호가 증가합니다.

## MAJOR 버전 번호 
MAJOR 버전 번호를 갱신할 수 있는 경우는 주로 다음과 같습니다.      
사용자는 이전 버전의 프로그램을 사용하지 못하게 되며, 이에 따라 업데이트가 필요합니다.        

- 공용 메서드 또는 속성 제거
```cs
// Version 1.0.0
public class Calculator
{
    public int Add(int a, int b) => a + b;
    public int Subtract(int a, int b) => a - b; // This method exists
}

// Version 2.0.0 - MAJOR increment required
public class Calculator
{
    public int Add(int a, int b) => a + b;
    // Subtract method removed - breaking change!
}
```

- 메서드 서명(파라미터) 변경
```cs
// Version 1.0.0
public void SaveFile(string filename) { }

// Version 2.0.0 - MAJOR increment required
public void SaveFile(string filename, bool overwrite) { } // Added required parameter
```

- 기존과 메서드 흐름이 변경되는 경우
```cs
// Version 1.0.0 - returns null when file not found
public string ReadFile(string path) => File.Exists(path) ? File.ReadAllText(path) : null;

// Version 2.0.0 - MAJOR increment required
public string ReadFile(string path) => File.ReadAllText(path); // Now throws exception when file not found
```

## MINOR 버전 번호
MINOR 버전 번호를 갱신할 수 있는 경우는 주로 다음과 같습니다.      
기존 코드를 수정하지 않고 새 기능을 추가하기 때문에, 사용자가 반드시 업데이트할 필요는 없습니다.     

- 새 공용 메서드 또는 속성 추가
```cs
// Version 1.0.0
public class Calculator
{
    public int Add(int a, int b) => a + b;
}

// Version 1.1.0 - MINOR increment
public class Calculator
{
    public int Add(int a, int b) => a + b;
    public int Multiply(int a, int b) => a * b; // New method added
}
```

- 새 오버로드 추가
```cs
// Version 1.0.0
public void Log(string message) { }

// Version 1.1.0 - MINOR increment
public void Log(string message) { } // Original method unchanged
public void Log(string message, LogLevel level) { } // New overload added
```

- 기존 메서드에 선택적 파라미터 추가
```cs
// Version 1.0.0
public void SaveFile(string filename) { }

// Version 1.1.0 - MINOR increment
public void SaveFile(string filename, bool overwrite = false) { } // Optional parameter
```

## 
