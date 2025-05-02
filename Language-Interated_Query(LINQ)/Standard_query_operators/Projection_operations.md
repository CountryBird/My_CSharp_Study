_프로젝션_ 은 객체를 새로운 형태로 변환하는 작업을 의미합니다.    

프로젝션을 사용하면 각 객체로부터 새로운 타입을 만들 수 있으며,    
특정 속성을 추출하여 수학적 연산을 수행할 수도 있습니다.    

# Select
`select` 절을 사용하여 프로젝션을 수행할 수 있습니다.  
예시에서는 각 단어에서 첫 글자만을 가져오는 역할을 합니다.   

```cs
 string[] words = { "cat", "plane", "keyboard", "jungle", "university", "a", "cloud", "strength", "bookstore", "elephant" };

 var query = from word in words
                  select word.Substring(0,1);
```

# SelectMany
다루고자 하는 데이터 컬렉션이 하나 이상 있을 때,    
쿼리 형태에서는 중첩 `from`문을, 메서드 형태에서는 `SelectMany`를 사용합니다.    

예를 들어 배열 안의 또 다른 배열 요소를 작업하려는 경우, 리스트 안에 string을 작업하려는 경우가 있습니다.   

이 방식을 통해 컬렉션 안의 컬렉션을 다뤄 일종의 _평탄화_ 작업이 가능해집니다.    

```cs
 List<string> phrases = ["an apple a day", "the quick brown fox"];

 var queryStyle = from phrase in phrases
                  from word in phrase.Split(' ')
                  select word;

 var methodStyle = phrases.SelectMany(phrase => phrase.Split(' '));
```

한 시퀀스가 또 다른 시퀀스와의 조합을 생성하는 것도 가능합니다.   
```cs
string[] top= { "shirt","jacket","sweater" };
string[] bottom = { "pants","jeans","shorts" };

var styleQuery = from t in top
           from b in bottom
           select (t, b);

var methodQuery = top.SelectMany(t => bottom,
   (t, b) => (t, b));

//(shirt, pants)
//(shirt, jeans)
//(shirt, shorts)
//(jacket, pants)
//(jacket, jeans)
//(jacket, shorts)
//(sweater, pants)
//(sweater, jeans)
//(sweater, shorts)
```
메서드 방식이 람다 식 형태이기 때문에 직관적이지 않을 수 있는데     
**요소 => 컬렉션** 은 각 요소에 대해 컬렉션을 반복함을 의미하고,    
쿼리 방식에서 `from`으로 새 컬렉션을 추가 하듯이 `,`로 컬렉션에 대한 파라미터를 지정하는 것이라 생각하면 편합니다.   

# Zip
`Zip` 메서드는 둘 이상의 시퀀스를 결합하는 메서드입니다.     

두 시퀀스의 동일한 위치에 있는 요소들을 병합하는 역할을 하며, 두 시퀀스의 길이가 다른 경우 짧은 쪽에 맞춰집니다.    

반환 타입은 지정된 위치에 맞게 생성된 튜플입니다.   
```cs
 IEnumerable<int> numbers = [ 1, 2, 3, 4, 5, 6 ];
 IEnumerable<char> letters = ['A', 'B', 'C', 'D'];

 var zip = numbers.Zip(letters);

 foreach (var z in zip)
 {
     Console.WriteLine(z);
     //(1, A)
     //(2, B)
     //(3, C)
     //(4, D)
 }
 foreach((int number,char letter) in zip)
 {
     Console.WriteLine($"{number} zipped with {letter}");
     //1 zipped with A
     //2 zipped with B
     //3 zipped with C
     //4 zipped with D
 }
```

# Select 및 SelectMany
