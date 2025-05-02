_필터링_은 지정된 조건을 충족하는 요소만을 포함하도록 하는 작업을 의미합니다.     

LINQ에서는 주로 `where` 절을 사용해 필터링을 수행합니다.    
쿼리 방식, 메서드 방식 둘 다 사용 가능합니다.     

```cs
string[] words = { "cat", "plane", "keyboard", "jungle", "university", "a", "cloud", "strength", "bookstore", "elephant" };

var queryStyle = from word in words
                 where word.Length > 6
                 select word;

var methodStyle = words.Where(w => w.Length > 6);
```
