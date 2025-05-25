TAP(Task Asynchronous Programming), _작업 비동기 프로그래밍 모델_ 은,          
일반적인 비동기 코딩 방식 위에, 추상화 계층을 하나 더 제공합니다.     

이전에는 `Thread`, `BeginInvoke`, `EndInvoke` 등의 저수준의 방식으로 코드를 작성했으나,      
TAP 모델은 복잡한 로직은 숨기고 간단한 키워드를 사용해 비동기 코드를 작성하는 것이 가능하게 합니다.      

일반적인 구문의 형태와 유사하게 작성되지만, 비동기로 동작해      
작업 완료 시점에 따라 실행되는 순서가 다르게 할 수 있습니다.    

아침 식사를 만드는 코드를 통해 비동기 처리의 예를 들어 보겠습니다.

# 동기 프로그래밍
![image](https://github.com/user-attachments/assets/21de30af-2843-4d84-b231-8f91ad84f48b)         
코드의 각 메서드를 보면 알 수 있듯이,        
특정 객체(음식을 의미)에 대해 파라미터를 받아 처리하는 형태를 띄고 있습니다.     

참고로, `Task.Delay(시간)`은 원래 비동기적으로 '시간'만큼 스레드를 멈추는 메서드지만    
해당 예제에서는 동기적으로 처리하기 위해 `Wait()` 메서드를 사용해 강제로 기다립니다.   

또한 `Stopwatch`의 `StartNew`, `Stop`을 사용해 세밀하게 시간을 측정할 수 있습니다. 
```cs
internal class Bacon { }
internal class Coffee { }
internal class Egg { }
internal class Juice { }
internal class Toast { }

class Program
{
    static void Main(string[] args)
    {
        Stopwatch sw = Stopwatch.StartNew();

        Coffee cup = PourCoffee();
        Console.WriteLine("coffee is ready");

        Egg eggs = FryEggs(2);
        Console.WriteLine("eggs are ready"); // 3ms + 3ms = 6ms 소요

        Bacon bacon = FryBacon(3);
        Console.WriteLine("bacon is ready"); // 3ms + 3ms = 6ms 소요

        Toast toast = ToastBread(2); // 3ms 소요
        ApplyButter(toast);
        ApplyJam(toast);
        Console.WriteLine("toast is ready");

        Juice oj = PourOJ();
        Console.WriteLine("oj is ready");
        Console.WriteLine("Breakfast is ready!");

        sw.Stop()
        Console.WriteLine($"Cooking Time: {sw.Elapsed.TotalSeconds} s");
        // Cooking Time: 15.0554367 s
    }

    private static Juice PourOJ()
    {
        Console.WriteLine("Pouring orange juice");
        return new Juice();
    }

    private static void ApplyJam(Toast toast) =>
        Console.WriteLine("Putting jam on the toast");

    private static void ApplyButter(Toast toast) =>
        Console.WriteLine("Putting butter on the toast");

    private static Toast ToastBread(int slices)
    {
        for (int slice = 0; slice < slices; slice++)
        {
            Console.WriteLine("Putting a slice of bread in the toaster");
        }
        Console.WriteLine("Start toasting...");
        Task.Delay(3000).Wait();
        Console.WriteLine("Remove toast from toaster");

        return new Toast();
    }

    private static Bacon FryBacon(int slices)
    {
        Console.WriteLine($"putting {slices} slices of bacon in the pan");
        Console.WriteLine("cooking first side of bacon...");
        Task.Delay(3000).Wait();
        for (int slice = 0; slice < slices; slice++)
        {
            Console.WriteLine("flipping a slice of bacon");
        }
        Console.WriteLine("cooking the second side of bacon...");
        Task.Delay(3000).Wait();
        Console.WriteLine("Put bacon on plate");

        return new Bacon();
    }

    private static Egg FryEggs(int howMany)
    {
        Console.WriteLine("Warming the egg pan...");
        Task.Delay(3000).Wait();
        Console.WriteLine($"cracking {howMany} eggs");
        Console.WriteLine("cooking the eggs ...");
        Task.Delay(3000).Wait();
        Console.WriteLine("Put eggs on plate");

        return new Egg();
    }

    private static Coffee PourCoffee()
    {
        Console.WriteLine("Pouring coffee");
        return new Coffee();
    }
}
```
실행 환경에 따라 일부 차이는 있을 수 있지만, 대략적으로 15초 가량 걸리는 것을 알 수 있습니다.

# 차단하지 않고 기다리기 
이전의 코드는, 작업이 수행되는 동안 스레드가 다른 작업을 수행하지 못하도록 하는 코드입니다.    

요리에 비유하면, 한 요리가 완료되는 동안 쭉 그 음식만을 보고 있는 것을 의미합니다.    
그 음식 요리 이외에 다른 재료 준비, 전화 같은 행동은 절대 할 수 없습니다.
```cs
static async Task Main(string[] args)
{
    Coffee cup = PourCoffee();
    Console.WriteLine("coffee is ready");

    Egg eggs = await FryEggsAsync(2);
    Console.WriteLine("eggs are ready");

    Bacon bacon = await FryBaconAsync(3);
    Console.WriteLine("bacon is ready");

    Toast toast = await ToastBreadAsync(2);
    ApplyButter(toast);
    ApplyJam(toast);
    Console.WriteLine("toast is ready");

    Juice oj = PourOJ();
    Console.WriteLine("oj is ready");
    Console.WriteLine("Breakfast is ready!");
}
```
이 코드는 메서드를 실행하고, 기다리는 것 (`await`)을 의미합니다.      
결국 실행 후 기다림이라는 관점에서, 동기 프로그래밍과 결과에서 차이는 없지만      
스레드가 차단되는 것이 아니기 때문에 원한다면 다른 작업이 가능하다는 차이가 있습니다.    

# 동시에 작업 시작
```cs
Stopwatch sw = Stopwatch.StartNew();

Coffee cup = PourCoffee();
Console.WriteLine("Coffee is ready");

Task<Egg> eggsTask = FryEggsAsync(2);
Task<Bacon> baconTask = FryBaconAsync(3);
Task<Toast> toastTask = ToastBreadAsync(2); // 요리는 이 시점에서 이미 시작함

Toast toast = await toastTask;
ApplyButter(toast);
ApplyJam(toast);
Console.WriteLine("Toast is ready");
Juice oj = PourOJ();
Console.WriteLine("Oj is ready");

Egg eggs = await eggsTask;
Console.WriteLine("Eggs are ready");
Bacon bacon = await baconTask;
Console.WriteLine("Bacon is ready");

Console.WriteLine("Breakfast is ready!");
sw.Stop();
Console.WriteLine($"Cooking Time: {sw.Elapsed.TotalSeconds} s");
// Cooking Time: 6.0378935 s
```
앞서 말했듯이, 실행 시간은 환경에 따라 일부 차이가 있을 수 있습니다.         

위 코드는, 비동기적으로 실행할 필요가 있는 메서드들은 `Task` 를 사용해 미리 실행을 시켜놓는 개념입니다. 
이후 `await`으로 기다리고 있던 코드를 마저 실행합니다.    
때문에 직전의 코드와 다르게 _실행_ 과 _기다리는 것_ 이 분리되어서 더 효율적인 코드 실행이 가능해집니다.     

![image](https://github.com/user-attachments/assets/ff7d2ed4-3159-4990-bd70-6f7fcd8bec49)
다만, 이 코드는 eggs 와 bacon이 작업을 끝낸 후       
필요 이상으로 시간이 흐른다는 점에서 일부 비효율적일 수 있습니다.         
(요리를 예로 음식을 태웠다고 비유할 수 있습니다.)   

# Task를 통한 조합 지원
특정 코드가 하나의 단위로 묶일 수 있다고 생각되면,          
동기/비동기를 조합하여 작성할 수도 있습니다.     
다만 `await` 를 사용하기 때문에 메서드는 `async`를 가져야합니다.
```cs
static async Task<Toast> MakeToastWithButterAndJamAsync(int number)
{
    var toast = await ToastBreadAsync(number);
    ApplyButter(toast);
    ApplyJam(toast);

    return toast;
}
```

# 비동기 예외 처리
비동기 메서드도 동기 메서드와 마찬가지로 `throw`를 할 수 있습니다.      

비동기 메서드 내부에서 예외가 발생하더라도, **즉시 발생하지 않습니다.**    
단, 예외가 사라지는 것이 아닌 `Task`에 캡슐화되는 개념이기 때문에 `await`하는 순간 그 때 예외가 throw 됩니다.

```cs
private static async Task<Toast> ToastBreadAsync(int slices)
{
    for (int slice = 0; slice < slices; slice++)
    {
        Console.WriteLine("Putting a slice of bread in the toaster");
    }
    Console.WriteLine("Start toasting...");
    await Task.Delay(2000);
    Console.WriteLine("Fire! Toast is ruined!");
    throw new InvalidOperationException("The toaster is on fire");
    await Task.Delay(1000);
    Console.WriteLine("Remove toast from toaster");

    return new Toast();
}
```
`await`이 예외를 `throw`하기 때문에,      
잡아주지 않으면, 사실상 예외가 사라진 효과가 나며       
잡아주는 경우, 동기 메서드와 동일하게 `try-catch`가 없으면 계속해서 전파됩니다.

# await을 Task에 적용
`await` 클래스의 메서드를 사용하여 `Task` 식을 개선할 수 있습니다.     
`WhenAll`를 사용하면 비동기 작업 여러 개를 동시에 실행하고 모두 완료될 때까지 기다릴 수 있습니다.     
그렇기 때문에, 해당 메서드 안에는 `Task` 타입이어야 한다는 제약 조건이 있습니다.      

처리하고자 하는 작업의 결과 값이 메서드의 결과 값이 됩니다.   
```cs
await Task.WhenAll(eggsTask, baconTask, toastTask);
Console.WriteLine("Eggs are ready");
Console.WriteLine("Bacon is ready");
Console.WriteLine("Toast is ready");
Console.WriteLine("Breakfast is ready!");
```

이러한 형식을 응용하여 `Task`의 컬렉션 형태를 만들어 등록한 뒤, 일괄 처리가 가능하게 할 수 있습니다.    
또한 `WhenAny` 메서드는 작업 중 가장 처음으로 완료되는 작업에 대한 결과를 보여줍니다.    
```cs
var breakfastTasks = new List<Task> { eggsTask, baconTask, toastTask };
while (breakfastTasks.Count > 0)
{
    Task finishedTask = await Task.WhenAny(breakfastTasks);
    if (finishedTask == eggsTask)
    {
        Console.WriteLine("Eggs are ready");
    }
    else if (finishedTask == baconTask)
    {
        Console.WriteLine("Bacon is ready");
    }
    else if (finishedTask == toastTask)
    {
        Console.WriteLine("Toast is ready");
    }
    await finishedTask;
    breakfastTasks.Remove(finishedTask);
}
```
