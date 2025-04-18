# 쿼리란 무엇이며, 무엇을 하는 것일까?
_쿼리_ 는, 데이터 소스에서 검색/반환된 데이터가 가져야 하는 형태, 조직을 지정하는 개념입니다.    
쿼리를 통한 결과랑은 엄연히 다른 개념입니다.    

일반적으로, 데이터는 논리적으로 같은 종류의 시퀀스로 구성되기에,     
애플리케이션은 `IEnumerable<T>`, `IQueryable<T>`로 통일하여 보고 T만을 변환시켜 사용하는 형태로 인식합니다.     
예를 들어, LINQ to XML과 같은 경우 `IEnumerable<XElement>`로 봅니다.     

소스 시퀀스에서, 쿼리는 크게 3가지 중 하나를 수행할 수 있습니다.    

**- 하위 집합을 검색만 하는 형태**        
: 단순히 정렬, 검색만을 하여 개별 요소를 수정하지 않는 형태입니다. 
```cs
IEnumerable<int> highScoresQuery =
    from score in scores
    where score > 80
    orderby score descending
    select score;
```

**- 하위 집합을 검색하여 새 형식으로 변환하는 형태**     
: 이전과 같이 요소 시퀀스를 검색한 후, 데이터 소스에는 없는 새로운 형태로 변환시키는 형태입니다.    
이러한 방법을 이용해, int의 결과를 string으로 바꾸는 등의 쿼리도 작성할 수 있습니다.  
```cs
IEnumerable<string> highScoresQuery2 =
    from score in scores
    where score > 80
    orderby score descending
    select $"The score is {score}";
```

**- 원본 데이터에 대한 특정 값을 검색하는 형태**      
: 또 다른 시퀀스로 반환되는 다른 형태와 다르게, 개수, 최대/최소값과 같은 데이터에 관한 특정 값을 검색하는 형태입니다.     
```cs
IEnumerable<int> highScoresQuery3 =
    from score in scores
    where score > 80
    select score;

var scoreCount = highScoresQuery3.Count();
```

# 쿼리 식이란?
