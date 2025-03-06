**C#은 다른 형끼리의 변환이 금지되어있고, 만약 변환을 하고싶다면 명시적으로 타입을 선언해 주어야 합니다.**
_==> 이러한 특성을 강 타입 언어라고 합니다._

```cs
int myInt = 0;
char myChar = 'a';
bool myBool = true;
```
각 변수가 이러하다고 가정하고,

```cs
int intandbool1 = myInt + myBool; // 에러
bool intandbool2 = myInt + myBool; // 에러

char charandbool1 = myChar + myBool; // 에러
bool charandbool2 = myChar + myBool; // 에러
```
다음과 같은 코드를 작성하면 타입 안정성을 지키기 위해 에러가 나는 것을 확인할 수 있습니다.

```cs
int intandchar1 = myInt + myChar; // 정상 실행
char intandchar2 = myInt + myChar; // 에러
```
하지만 다음과 같은 경우에는 한 쪽은 실행이 되는데,
이는 int가 32비트, char가 16비트 정수로 실행이 되기 때문이라고 합니다.
단, int가 char에 비해 범위가 크기 때문에 문제를 일으킬 가능성이 있어 다른 한 쪽은 에러를 발생시킵니다.

**핵심적으로, 타입들끼리 연산이 되었을 때 데이터에 지장이 가느냐를 기준으로 판단하는 것입니다.**

# 변수 선언에서 형식 지정
C#에서는 `var` 키워드를 사용해서 컴파일러가 타입을 추론하게 할 수 있습니다.
때문에 사용자 지정 형식 등의 복잡한 타입을 간단히 초기화 할 수 있습니다.
```cs
var myVar = 0;
myVar = 1; // 정상 실행
myVar = 'a'; // 정상 실행
myVar = true; // 에러
```
단, 타입이 자동적으로 변한다는 개념이 아닌 처음 초기화만 자동으로 된다는 관점으로 보는 것이 맞습니다.
