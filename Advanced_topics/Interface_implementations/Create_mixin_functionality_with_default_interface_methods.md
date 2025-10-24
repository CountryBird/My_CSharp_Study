인터페이스 멤버 선언 시 구현을 정의할 수 있습니다.             
이 기능은 인터페이스에 선언된 기능에 대한 기본 구현을 정의할 수 있는 새로운 기능을 제공합니다.              

클래스는 기능 재정의할 시기, 기본 기능을 사용할 시기, 불연속 기능에 대한 지원을 선언하지 않을 시기를 선택할 수 있습니다.        

- 불연속 기능을 설명하는 구현을 사용하여 인터페이스를 만듭니다.
- 기본 구현을 사용하는 클래스를 만듭니다.
- 기본 구현의 일부 또는 전체를 재정의하는 클래스를 만듭니다.

# 확장 메서드의 제한 사항
확장 메서드는 기존 클래스나 인터페이스에 새로운 메서드를 붙이는 방법입니다.             
예를 들어, IEnumerbale 인터페이스에 Where, Select 같은 LINQ 메서드들이 있는데 이는 확장 메서드로 구현된 방식입니다.       

단, 확장 메서드에는 한계가 있습니다.         

1. 컴파일 타임에 정해짐           
확장 메서드는 컴파일 타임에 변수의 선언된 타입을 기준으로 어떤 메서드를 호출할지 결정됩니다.           
즉, 실제 객체 타입이 아니라 변수의 선언 타입이 중요합니다.          

2.  모든 곳에서 접근 가능
확장 메서드는 _확장 메서드가 정의된 클래스_ 가 접근 가능한 모든 곳에서 사용할 수 있습니다.
하지만 이는, 그 확장 기능을 원치 않아도 막을 방법이 없다는 문제가 있습니다.      

- 대안
C# 8부터는 인터페이스에서 기본 구현을 정의할 수 있기 때문에, 확장 메서드의 한계가 문제가 되는 경우
이를 대체할 수 있습니다.

# 애플리케이션 설계
_가정 자동화_ 애플리케이션을 설계하고자 합니다.             
즉, 집 안의 전등과 표시등을 제어하는 프로그램을 만든다고 가정합니다.         

일반적으로 등을 켜고 끄는 기능, 등의 상태를 보이는 기능은 지원해야 하며,            
일부 고급 조명에 한해 _타이머에 맞춰 끄는 기능_, _일정 간격마다 점멸하는 기능_ 들을 추가할 수 있습니다.           

단, 모든 조명이 이런 고급 기능을 하드웨어적으로 지원하는 것은 아니기 때문에,          
모든 조명 클래스에 일일이 해당 기능들을 구현하는 것은 비효율적입니다.                      

기본 인터페이스 멤버가 생기면서 인터페이스 안에서도 기본 구현을 제공할 수 있게 되었습니다.                    
일반 조명 클래스는 인터페이스를 구현하고, 점멸 기능은 인터페이스의 기본 구현이 대신합니다.              
고급 조명 클래스는 점멸 기능을 재정의하여 자체적인 점멸 기능을 사용할 수 있습니다.          

# 인터페이스 만들기
우선, 조명의 동작만을 정의하는 인터페이스부터 정의해봅시다.            
```cs
public interface ILight
{
    void SwitchOn();
    void SwitchOff();
    bool IsOn();
}
```

천장등 같은 경우는, 다음 코드와 같이 인터페이스를 구현할 수 있습니다.
```cs
public class OverheadLight : ILight
{
    private bool isOn;
    public bool IsOn() => isOn;
    public void SwitchOff() => isOn = false;
    public void SwitchOn() => isOn = true;

    public override string ToString() => $"The light is {(isOn ? "on" : "off")}";
}
```

다음으로, 일정 시간 후 자동으로 꺼질 수 있는 조명의 인터페이스를 정의해 보겠습니다. 
```cs
public interface ITimerLight : ILight
{
    Task TurnOnFor(int duration);
}
```

기본 구현을 사용해 천장등 코드에 추가할 수 있지만,               
이 인터페이스 정의를 수정하여 확장 시 `virtual` 키워드를 통해 수정할 수 있게 하는 것이 더 좋습니다.             
```cs
public interface ITimerLight : ILight
{
    public async Task TurnOnFor(int duration)
    {
        Console.WriteLine("Using the default interface method for the ITimerLight.TurnOnFor.");
        SwitchOn();
        await Task.Delay(duration);
        SwitchOff();
        Console.WriteLine("Completed ITimerLight.TurnOnFor sequence.");
    }
}
```

이와 같은 방식에서 확장하여, 더 정교한 형식의 조명 프로토콜을 지원할 수 있습니다.           
```cs
public class HalogenLight : ITimerLight
{
    private enum HalogenLightState
    {
        Off,
        On,
        TimerModeOn
    }

    private HalogenLightState state;
    public void SwitchOn() => state = HalogenLightState.On;
    public void SwitchOff() => state = HalogenLightState.Off;
    public bool IsOn() => state != HalogenLightState.Off;
    public async Task TurnOnFor(int duration)
    {
        Console.WriteLine("Halogen light starting timer function.");
        state = HalogenLightState.TimerModeOn;
        await Task.Delay(duration);
        state = HalogenLightState.Off;
        Console.WriteLine("Halogen light finished custom timer function");
    }

    public override string ToString() => $"The light is {state}";
}
```
해당 코드에서 `TurnOnFor` 메서드는 `HalogenLight`가 _새로_ 정의한 것이기 때문에                     
`override`가 사용되지 않습니다.            

# 조합 및 매칭 기능
고급 기능을 도입할수록 기본 인터페이스의 장점을 더 명확히 할 수 있습니다.               
인터페이스를 사용하면 기능을 조합하고 일치시킬 수 있습니다.          

```cs
public interface IBlinkingLight : ILight
{
    public async Task Blink(int duration, int repeatCount)
    {
        Console.WriteLine("Using the default interface method for IBlinkingLight.Blink.");
        for (int count = 0; count < repeatCount; count++)
        {
            SwitchOn();
            await Task.Delay(duration);
            SwitchOff();
            await Task.Delay(duration);
        }
        Console.WriteLine("Done with the default interface method for IBlinkingLight.Blink.");
    }
}
```
기본 구현을 사용하여 모든 조명 클래스가 점멸하는 기능을 사용할 수 있고,                
천장등 클래스는 이러한 클래스를 모두 구현하여 기능을 모두 추가할 수 있습니다.                       
```cs
public class OverheadLight : ILight, ITimerLight, IBlinkingLight
{
    private bool isOn;
    public bool IsOn() => isOn;
    public void SwitchOff() => isOn = false;
    public void SwitchOn() => isOn = true;

    public override string ToString() => $"The light is {(isOn ? "on" : "off")}";
}
```

새롭게 정의하는 `LEDLight`는 `ITimerLight`와 `IBlinkingLight` 인터페이스를 구현하여           
타이머 기능과 점멸 기능을 사용할 수 있으며, 점멸 기능은 재정의하여 사용합니다.       
```cs
public class LEDLight : IBlinkingLight, ITimerLight, ILight
{
    private bool isOn;
    public void SwitchOn() => isOn = true;
    public void SwitchOff() => isOn = false;
    public bool IsOn() => isOn;
    public async Task Blink(int duration, int repeatCount)
    {
        Console.WriteLine("LED Light starting the Blink function.");
        await Task.Delay(duration * repeatCount);
        Console.WriteLine("LED Light has finished the Blink function.");
    }

    public override string ToString() => $"The light is {(isOn ? "on" : "off")}";
}
```

`ExtraFancyLight` 클래스는 타이머 기능과 점멸 기능 둘 다 재정의하여 사용합니다.           
```cs
public class ExtraFancyLight : IBlinkingLight, ITimerLight, ILight
{
    private bool isOn;
    public void SwitchOn() => isOn = true;
    public void SwitchOff() => isOn = false;
    public bool IsOn() => isOn;
    public async Task Blink(int duration, int repeatCount)
    {
        Console.WriteLine("Extra Fancy Light starting the Blink function.");
        await Task.Delay(duration * repeatCount);
        Console.WriteLine("Extra Fancy Light has finished the Blink function.");
    }
    public async Task TurnOnFor(int duration)
    {
        Console.WriteLine("Extra Fancy light starting timer function.");
        await Task.Delay(duration);
        Console.WriteLine("Extra Fancy light finished custom timer function");
    }

    public override string ToString() => $"The light is {(isOn ? "on" : "off")}";
}
```

# 패턴 일치를 사용하여 조명 타입 검색
C#의 `is`를 통한 패턴 일치 기능을 사용하여 지원되는 인터페이스를 검사하고 이에 따른 조명의 기능을 결정할 수 있습니다.          
```cs
private static async Task TestLightCapabilities(ILight light)
{
    // Perform basic tests:
    light.SwitchOn();
    Console.WriteLine($"\tAfter switching on, the light is {(light.IsOn() ? "on" : "off")}");
    light.SwitchOff();
    Console.WriteLine($"\tAfter switching off, the light is {(light.IsOn() ? "on" : "off")}");

    if (light is ITimerLight timer)
    {
        Console.WriteLine("\tTesting timer function");
        await timer.TurnOnFor(1000);
        Console.WriteLine("\tTimer function completed");
    }
    else
    {
        Console.WriteLine("\tTimer function not supported.");
    }

    if (light is IBlinkingLight blinker)
    {
        Console.WriteLine("\tTesting blinking function");
        await blinker.Blink(500, 5);
        Console.WriteLine("\tBlink function completed");
    }
    else
    {
        Console.WriteLine("\tBlink function not supported.");
    }
}
```

Main문에서는 각 조명 타입 객체를 차례로 만들고 해당 조명을 테스트할 수 있습니다.               
```cs
static async Task Main(string[] args)
{
    Console.WriteLine("Testing the overhead light");
    var overhead = new OverheadLight();
    await TestLightCapabilities(overhead);
    Console.WriteLine();

    Console.WriteLine("Testing the halogen light");
    var halogen = new HalogenLight();
    await TestLightCapabilities(halogen);
    Console.WriteLine();

    Console.WriteLine("Testing the LED light");
    var led = new LEDLight();
    await TestLightCapabilities(led);
    Console.WriteLine();

    Console.WriteLine("Testing the fancy light");
    var fancy = new ExtraFancyLight();
    await TestLightCapabilities(fancy);
    Console.WriteLine();
}
```

# 컴파일러가 최선의 구현을 결정하는 방법
`ILight` 인터페이스에 메서드를 추가하면 새로운 복잡성이 발생합니다.                
기본 인터페이스 메서드를 처리하는 방법은 여러 파생 인터페이스를 구현하는 구체적인 클래스에 대해 영향을 최소화할 수 있습니다.        

새 메서드를 통해 원래 인터페이스를 개선하여, 사용 방법을 변경할 수 있습니다.               
```cs
public enum PowerStatus
{
    NoPower,
    ACPower,
    FullBattery,
    MidBattery,
    LowBattery
}
```

이는 전원 상태를 표시하기 위한 열거형 형태입니다.            
기본 구현에서는 전원이 없다고 가정합니다.               
```cs
public interface ILight
{
    void SwitchOn();
    void SwitchOff();
    bool IsOn();
    public PowerStatus Power() => PowerStatus.NoPower;
}
```

`ILight` 인터페이스 및 이에서 파생된 인터페이스에서 이러한 변경 내용은 완전히 컴파일되어 기능을 사용할 수 있습니다.       
다만, 여러 파생 인터페이스에서 동일한 메서드를 재정의하는 것에는 주의가 필요합니다.                  
이러한 경우 클래스가 파생된 두 인터페이스를 모두 구현할 때마다 모호한 메서드 호출을 만들 수 있습니다.          
<img width="1040" height="960" alt="image" src="https://github.com/user-attachments/assets/240af5d7-2254-4347-a791-93fb54c16437" />

일반적으로 인터페이스 정의를 작게 유지하고, 하나의 기능에 집중해야 이러한 상황을 방지할 수 있습니다.         
