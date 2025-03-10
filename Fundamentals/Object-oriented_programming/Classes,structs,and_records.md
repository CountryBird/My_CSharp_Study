# 캡슐화
**캡슐화** 는 객체 지향 프로그래밍에서 자주 언급되는 원리 중 하나입니다.

객체의 속성과 행위를 하나로 묶고 구현 내용을 감춘다는 의미를 담고 있는 말인데, 이를 통해 유지보수성과 재사용성을 향상시킬 수 있습니다.

## Tell, Don't Ask
Tell, Don't Ask는 정보 은닉, 캡슐화와 관련된 분야에서 중요하게 받아들여지는 원칙입니다.

객체에게 데이터를 요구(Ask)하지말고, 행위까지 지시(Tell)하라는 의미입니다.

```cs
public class ShortWork
{
    public void work()
    {
        // LongWork 이전에 선행되어야 할 짧은 일
    }
}

public class LongWork
{
    public void work(ShortWork s)
    {
        s.work();
        // 긴 업무 코드
    }
}
```
이와 같은 예시가 있다고 가정해봅시다. 기본적으로 이 코드는 요구(Ask)를 하는 형태입니다.

ShortWork가 선행되고, 이 결과에 따라 수행하는 긴 코드를 가진 LongWork가 있습니다.

해당 코드만을 보았을 때는 큰 문제가 없어 보이지만, 문제는 코드가 추가됨에 있습니다.

**만약 LongWork와 유사한 형태의 (ShortWork 후 긴 코드의 업무) 코드가 더 추가되어야 한다면,**

**개발자는 이러한 중복되고 길기만 한 코드를 추가 시마다 일일히 적어주어야 합니다.** 

이는 생산성과 유지보수성을 떨어뜨립니다.

```cs
 public class ShortWork
 {
     public void work()
     {
         // LongWork 이전에 선행되어야 할 짧은 일

         // 긴 업무 코드
     }
 }

 public class LongWork
 {
     public void work(ShortWork s)
     {
         s.work();
     }
 }
```
때문에, 다음과 같이 코드가 고쳐져야 하며, LongWork는 ShortWork의 내부 형태를 자세히 알 필요 없이 메서드 하나만 지시하면(Tell) 되는 형태가 됩니다.

이러한 관점에서 캡슐화는 단순히 묶고 숨긴다는 개념이 아니라, 유지보수성과 재사용성을 향상시킨다는 의미를 갖는 것입니다.

# 멤버
C#에는 다른 언어와 다르게 전역 변수, 전역 메서드가 없습니다.
다음은 멤버가 될 수 있는 종류입니다.
- 필드값, 상수, 속성, 메서드, 생성자, 이벤트, 종료자, 인덱서, 연산자, 중첩 형식

# 접근성
`public`,`internal`, `protected`, `private` 외에도, 이들의 조합으로 이루어진 접근 제한자가 있습니다.
### protected internal
`internal`의 동일 어셈블리 개념과 `protected`의 상속 개념이 합쳐졌다고 보면 됩니다.

_동일 어셈블리면 문제 없이 사용할 수 있고, 다른 어셈블리여도 상속을 한 클래스의 경우 사용할 수 있습니다._

### private protected
`protected`의 범위가 더 좁아졌다고 보면 됩니다.

_기존 protected의 상속한 클래스면 된다 개념에 동일 어셈블리라는 제한이 추가됩니다._

# 상속
기본 클래스에서 상속되는 클래스는 생성자와 종료자를 제외하고, 모든 public, protected, intenal 멤버를 자동으로 포함합니다.

==> 별도 구현이 필요한 경우가 아니면 코드에 적을 필요 없음
