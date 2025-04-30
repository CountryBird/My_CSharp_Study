- _표준 쿼리 연산자_ 는 LINQ 패턴을 형성하는 키워드 및 메서드를 의미합니다.        
`select`/`.Select()`, `where`/`Where()` 등이 여기에 해당합니다.     

- C#에서는 자주 사용되는 쿼리 메서드를 LINQ 쿼리 키워드로써 정의해두었습니다.    
컴파일러는 이러한 키워드를 사용한 표현식을 메서드 호출로 변환하여 사용합니다.    

- System.Linq 네임스페이스에 속한 일부 메서드들 중에는 쿼리 형식으로는 표현할 수 없는 경우가 있습니다.      

- C# 런타임이나 NuGet 패키지 등에서 매 릴리스마다 LINQ 쿼리에 사용하는 새로운 메서드를 추가하는 경우가 있습니다. `System.Linq.Enumerable` API 문서를 통해 LINQ 메서드를 확인할 수 있습니다.    

# IEnumerable<T>와 IQueryable<T>
`IEnumerable<T>`와 `IQueryable<T>`는 런타임에 쿼리가 실행되는 방법에 따라 구분할 수 있습니다.     

`IEnuerable<T>`는 메모리 내 컬렉션을 대상으로 작업 시 사용됩니다.     
때문에 이미 로드된 데이터를 기반으로 메서드가 실행됩니다.     

쿼리 객체는 단순히 조건을 짜놓은 것에 불과하고, 실제 데이터 접근은 `foreach` 같은 열거 시점에 발생합니다.     
결국 모든 작업이 C# 코드 상에서 실행되기 때문에, 대량의 데이터 처리에는 비효율적일 수 있습니다.     

`IQueryable<T>`는 원격 데이터 소스를 대상으로 작업 시 사용됩니다.   
쿼리를 작성하면, 내부적으로 식 트리로 저장됩니다.    

이 식 트리가 SQL 쿼리 등으로 변환되어 데이터 소스에서 실행됩니다.     
따라서 쿼리를 작성해 보내고, 결과만 받아오는 형식이기 때문에 대량의 데이터 처리에도 효율적인 성능을 낼 수 있습니다.   

# 표준 쿼리 연산자 예시
```cs
string sentence = "A picture is worth a thousand words.";
string[] words = sentence.Split(' ');

var query = from word in words
            group word.ToUpper() by word.Length into gr
            orderby gr
            select new { Length = gr.Key, Words = gr };

var method = words.GroupBy(w => w.Length, w => w.ToUpper())
    .Select(g => new { Length = g.Key, Words = g })
    .OrderBy(o => o.Length);
```

# 쿼리 연산자 유형
