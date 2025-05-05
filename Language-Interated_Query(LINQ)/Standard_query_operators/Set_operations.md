LINQ의 _집합 작업_ 은 컬렉션 내의 동등한 요소가 있는지의 여부에 따라 결과 집합을 나타내는 작업을 의미합니다.       

# Distinct 및 DistinctBy
`Distict`는 컬렉션 내의 고유한 요소만을 포함하여 보일 수 있습니다.    
```cs
char[] letters = ['a', 'a', 'b', 'c', 'c', 'd', 'd', 'e'];

var distinctLetters = from letter in letters.Distinct()
                      select letter;

foreach (var dLetter in distinctLetters)
{
    Console.Write($"{dLetter} ");
    // a b c d e
}
```

`DistinctBy`는 `KeySelector`를 사용하는 `Distinct`입니다.    
고유 판단을 할 때, 람다 식을 사용해 판별 기준을 설정 가능합니다.    
```cs
string[] words = ["apple", "asia", "bear", "candy", "cool", "dragon", "doll", "eagle"];

var firstLetter = from letter in words.DistinctBy(fl => fl[0])
                  select letter;

foreach (var fl in firstLetter)
{
    Console.Write($"{fl} ");
    // apple bear candy dragon eagle
}
```
해당 예제에서는 단어의 첫번째 문자를 기준으로 고유 판단을 하였기 때문에,    
첫 문자 당 처음 나온 단어 하나씩만 선택된 것을 확인할 수 있습니다.    

# Except 및 ExceptBy
