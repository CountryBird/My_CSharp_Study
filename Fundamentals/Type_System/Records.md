`record` 키워드는 C# 9.0 이후 사용할 수 있는 키워드입니다.
클래스와 유사한 점이 있지만, 일부 차이가 있습니다.

* 값 기반 비교
  클래스 같은 참조 형식의 경우의 '같다'의 의미는 동일한 개체를 참조하는 경우를 말합니다.
  때문에 같은 클래스로 만든 인스턴스이더라도 각각 다른 개체를 의미하기 때문에 일반적인 `==`나 `Equals()`로 같다고 판단하지 않습니다.

  하지만 record의 경우 같다고 판단합니다.
  ```cs
    public class MyClass(int a)
  {
      public int num = a;
  }
  public record MyRecord(int a)
  {
      public int num = a;
  
  }
  private static void Main(string[] args)
  {
      MyClass myClass1 = new MyClass(1); 
      MyClass myClass2 = new MyClass(1);
  
      MyRecord myRecord1 = new MyRecord(1);
      MyRecord myRecord2 = new MyRecord(1);
      
      Console.WriteLine(myClass1==myClass2); // False
      Console.WriteLine(myRecord1==myRecord2); // True
  }
  ```
* 불변
  > 가변 - 동일한 메모리에 값을 다시 쓸 수 있음
  > 
  > 불변 - 동일한 메모리에 값을 다시 쓸 수 없음 -> 갱신 시 새로운 메모리 주소 필요

  ```cs
    public class MyClass
  {
      public int num = 0;
  }
  public record MyRecord
  {
      public int num = 0;
  }
  private static void Main(string[] args)
  {
      MyClass myClass = new MyClass();
      Console.WriteLine(myClass.GetHashCode()); // 43942917
  
      myClass.num = 1;
      Console.WriteLine(myClass.GetHashCode()); // 43942917
  
      MyRecord myRecord = new MyRecord();
      Console.WriteLine(myRecord.GetHashCode()); // -957334475
  
      myRecord.num = 1;
      Console.WriteLine(myRecord.GetHashCode()); // -957334474
  }
  // 값 변경 후 차이를 보이기 위한 것이며, 값 자체는 달라질 수 있습니다.
  ```
  불변 객체의 경우, 생성 이후 객체의 상태를 변경할 수 없으며 변경 시 새로운 주소를 기반으로 새로 생성됩니다.
  이와 달리 클래스와 같은 가변 객체는 주소가 변하지 않습니다.

  이러한 특성 덕분에, 불변 객체는 항상 같은 값을 유지한다고 확정할 수 있어 스레드 안정성을 보장하는 장점이 있습니다.

  ('같은 주소를 바라본다는 것 == 같은 값이라는 것' 을 보장) 
