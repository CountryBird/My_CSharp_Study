LINQ에서 _분할_ 은, 요소를 재배열하지 않고 입력 시퀀스를 두 개의 섹션으로 나눠 하나를 반환하는 작업을 의미합니다.   

![image](https://github.com/user-attachments/assets/7072f525-0141-4843-b654-9acf38811749)     
위 그림과 같이 일부 요소를 반환하거나, 일부 요소를 건너뛰고 반환하는 것이 가능합니다.  

# Take
`Take`를 사용해 시퀀스의 특정 개수만큼까지 가져올 수 있습니다. 
```cs
 int[] nums = { 1, 2, 3, 4, 5, 6, 7, 8, 9, 10 };

 var takeNums = nums.Take(3);
 foreach (int num in takeNums)
 {
     Console.Write($"{num} ");
     // 1 2 3
 }
```

# Skip
`Skip`을 사용해 시퀀스의 특정 개수만큼을 건너뛰고 가져올 수 있습니다.   
```cs
int[] nums = { 1, 2, 3, 4, 5, 6, 7, 8, 9, 10 };

var skipNums = nums.Skip(3);
foreach (int num in skipNums)
{
    Console.Write($"{num} ");
    // 4 5 6 7 8 9 10
}
```

# TakeWhile
`TakeWhile`은 내부의 람다식이 만족되지 않을 때까지 요소를 가져옵니다.
```cs
int[] nums = { 1, 2, 3, 4, 5, 6, 7, 8, 9, 10 };

var takeWhileNums = nums.TakeWhile(n=>n<5);
foreach (int num in takeWhileNums)
{
    Console.Write($"{num} ");
    // 1 2 3 4
}
```

# SkipWhile
`SkipWhile`은 내부의 람다식이 만족되는 요소를 건너뛰어 가져옵니다.     
```cs
int[] nums = { 1, 2, 3, 4, 5, 6, 7, 8, 9, 10 };

var skipWhileNums = nums.SkipWhile(n=>n<5);
foreach (int num in skipWhileNums)
{
    Console.Write($"{num} ");
    // 5 6 7 8 9 10
}
```

첫번째로 만족되는 요소를 건너뛰는 개념이며, 한 번 건너뛴 이후로 후속 요소는 람다 식에 필터링되지 않습니다.
```cs
var skipWhileNums = nums.SkipWhile(n=>n%2!=0);
foreach (int num in skipWhileNums)
{
    Console.Write($"{num} ");
    // 2 3 4 5 6 7 8 9 10
}
```

# Chunk
`Chunk`는, 지정된 수만큼의 요소를 가진 시퀀스로 분할합니다.       
예로, nums.Chunk(3)으로 인해 분할된 시퀀스는 각각 3개의 요소를 가집니다. (마지막에 요소가 부족한 경우 제외)
```cs
int[] nums = { 1, 2, 3, 4, 5, 6, 7, 8, 9, 10 };

var chunkNums = nums.Chunk(3);

int chunkCount = 1;
foreach ( var chunk in chunkNums )
{
    Console.WriteLine($"Chunk {chunkCount++}");
    foreach(var num in chunk)
    {
        Console.WriteLine(num);
    }
}
//Chunk 1
//1
//2
//3
//Chunk 2
//4
//5
//6
//Chunk 3
//7
//8
//9
//Chunk 4
//10
```
