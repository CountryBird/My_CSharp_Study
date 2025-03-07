# 참조 형식
class 키워드를 통해 사용자가 정의하는 형식은 _참조 형식_ 입니다.

따라서 초기화하지 않을 경우 null 값이 포함되며,

초기화의 경우에 new 연산자를 사용해 생성하거나 기존의 인스턴스를 할당해 사용하여야 합니다.

# 클래스 선언
클래스 선언 문의 구조는 [접근 제한자] - class - [식별자] 순입니다.
```cs
public class MyClass
{
 // 본문
}
```
정의의 나머지 부분(괄호 안)은 동작, 데이터가 정의되는 클래스 본문입니다.
## 접근 제한자
C#에는 크게 4가지의 접근 제한자가 있습니다.

클래스, 변수, enum, 함수에 대해 붙을 수 있으며, 이름대로 접근 수준을 제한하는 역할을 합니다.
### public
허용 범위가 가장 큰 접근 수준입니다.
해당 키워드를 사용하면 모든 코드에서 해당 요소에 접근이 가능합니다.
```cs
public class MyClass{}
```
### internal
동일한 어셈블리 파일(.exe, .dll) 내에서만 접근 가능합니다.
단일 어셈블리 파일의 경우라면 `public`과 차이가 없다고 볼 수 있습니다.
```cs
internal class MyClass{}
```
클래스의 경우 접근 제한자를 지정해 주지 않으면 `internal`이 기본값입니다.
### protected
해당 클래스와 그 클래스를 상속한 클래스에 한해 접근이 가능합니다.
```cs
protected class MyClass{}
```
### private
허용 범위가 가장 작은 접근 수준입니다.
해당 클래스가 아닌 경우 접근할 수 없습니다.
```cs
private class MyClass{}
```
메서드, 속성, 필드의 경우 접근 제한자를 지정해 주지 않으면 `private`이 기본값입니다.

# 생성자 및 초기화
클래스의 인스턴스를 만들 때, 해당 필드나 속성을 초기화하는 방법에는 여러 가지가 있습니다.

```cs
public class MyClass
{
  private int A = 1;
  private int B = 1;
}
```
다음과 같이 선언 시에 설정해 주는 방법이 있고,

```cs
public class MyClass
{
  private int A;
  private int B;

  public MyClass(int a,int b){ A = a; B = b}
}
```
클래스와 동일한 이름의 메서드인 `생성자`를 사용하여 인스턴스 생성 시마다 자동으로 값이 할당되게 할 수 있습니다.
```cs
public class MyClass
{
    private int A;
    private int B;

    public MyClass(int a,int b) => (A, B) = (a, b);
}
```
위와 같이 생성자를 사용하는 방법도 있습니다.

## required 키워드
```cs
public class MyClass
{
    public required int A;
    public int B;
}
```
`required` 키워드를 사용하면 _해당 필드 값은 반드시 초기화되어야 함_ 을 설정할 수 있습니다.
```cs
MyClass myClass = new MyClass(); // 에러
MyClass myClass2 = new MyClass() { A = 0 }; // 정상 실행
MyClass myClass3 = new MyClass() { B = 0 }; // 에러
```

# 클래스 상속
클래스는 객체 지향 프로그래밍의 특성인 **상속** 개념을 사용할 수 있습니다.
`sealed`로 정의되거나, 생성자인 경우를 제외하고는 부모 클래스의 모든 멤버를 상속합니다.

`자식 클래스:부모 클래스`와 같은 형식으로 상속을 사용합니다.

## sealed
`sealed` 키워드는 상속을 할 때 해당 부분이 클래스에서 상속되지 않도록 하는 역할을 합니다.
클래스 자체에 적용하여 클래스 자체의 상속을 막는 방법이 있고, 메서드만 적용하여 일부만 제한하는 것도 가능합니다.

필드는 상속과 오버라이딩 개념과 관련이 없기 때문에 `sealed`키워드를 사용하지 않습니다.

### 클래스 sealed
```cs
 sealed public class SealedClass
 {

 }
 public class SealedClassChild : SealedClass // 에러
 {

 }
```
sealed 키워드가 붙은 클래스는 상속할 수 없습니다.
### 메서드 sealed
```cs
public class MyClass
{
    public virtual void method() {}
}
public class MyChildClass : MyClass
{
    sealed public void method() { } // 에러
    sealed public override void method() { } // 정상 실행
}
```
최초 클래스는 `sealed`를 사용할 수 없으며, (최초에는 sealed를 사용할 이유가 없습니다) `virtual` 키워드를 사용해야 이 후 클래스에서 `sealed`를 사용할 수 있습니다.

상속을 받고자 하는 메서드는 위와 같이 `sealed`와 `override`를 같이 적어야 합니다.
`virtual`, `override`, `abstract`와 같은 상속 관련 키워드는 추후 자세히 다룰 예정입니다.
