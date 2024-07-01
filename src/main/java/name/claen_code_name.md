# Clean Code

## 의도를 분명하게 밝혀라
- 좋은 이름을 지으려면 시간이 걸리지만 좋은 이름으로 절약하는 시간이 훨씬 더 짧다.
- 변수의 존재 이유는? 수행 기능은? 사용 방법은? -> 이름이 의도를 모두 표현하는가?

```java
// bad
int d;

// good
int elapsedTimeInDays;
```

- 코드 맥락이 코드 자체에 명시적으로 드러나야 한다.

```java
// bad
public List<int[]> getThem(List<int[]> theList) {
    List<int[]> list1 = new ArrayList<>();
    for (int[] x : theList)
        if (x[0] == 4)
            list1.add(x);
    return list1;
}

// good
public List<int[]> getFlaggedCells(List<int[]> gameBoard) {
    final int STATUS_VALUE = 0;
    final int FLAGGED = 1;

    List<int[]> flaggedCells = new ArrayList<>();
    for (int[] cell : gameBoard)
        if (cell[STATUS_VALUE] == FLAGGED)
            flaggedCells.add(cell);
    return flaggedCells;
}
```

## 그릇된 정보를 피하라
- 그릇된 단서는 코드 의미를 흐린다.
- 나름대로 널리 쓰이는 의미가 있는 단어를 다른 의미로 사용해도 안 된다.
- 여러 계정을 그룹으로 묶었을 때, 실제 List가 아니라면, `accountList`라 명명하지 않는다.
- 서로 흡사한 이름을 사용하지 않도록 주의한다.
- 유사한 개념은 유사한 표기법을 사용한다.(일관성)
- 이름으로 그릇된 정보를 제공하는 끔찍한 예가 소문자 `L`이나 대문자 `O` 변수다.

## 의미있게 구분하라
- 컴파일러를 통과할지라도 연속된 숫자를 덧붙이거나 불용어를 추가하는 방식은 적절하지 못하다.
- 이름이 달라야 한다면 의미도 달라져야 한다.

```java
// bad
public static void copyChars(char[] a1, char[] a2) {
    for (int i = 0; i < a1.length; i++) {
        a2[i] = a1[i];
    }
}
```

- 불용어를 추가한 이름 역시 아무런 정보도 제공하지 못한다.
- `Info`, `Data`는 `a`, `an`, `the` 처럼 의미가 불분명한 불용어다.
- 불용어는 보통 중복이다.

```java
// bad
getActiveAccount();
getActiveAccounts();
getActiveAccountInfo();
```

- 읽는 사람이 차이를 알도록 이름을 지어라.

## 발음하기 쉬운 이름을 사용하라
- 발음하기 쉬운 이름은 중요하다. 프로그램은 사회 활동이기 때문이다.

```java
// bad 
class DtaRcrd102 {
    private Date genymdhms;
    private final String pszqint = "102";
}

// good
class Customer {
    private Date generationTimestamp;
    private final String recordId = "102";
}
```

## 검색하기 쉬운 이름을 사용하라
- `e`라는 문자열은 적합하지 못하다. 검색이 어렵다.
- 이름 길이는 범위 크기에 비례해야 한다.

```java
// bad
for (int j = 0; j < 34; j++) {
    s += (t[j]*4)/5;
}

// good
int realDaysPerIdealDay = 4;
final int WORK_DAYS_PER_WEEK = 5;

for (int j = 0; j < NUMBER_OF_TASKS; j++) {
    int realTaskDays = taskEstimate[j] * realDaysPerIdealDay;
    int realTaskWeeks = ( realTaskDays / WORK_DAYS_PER_WEEK);
    sum += realTaskWeeks;
}
```

## 인코딩을 피하라
- 문제 해결에 집중하는 개발자에게 인코딩은 불필요한 정신적 부담이다. 인코딩한 이름은 거의 발음하기 어려우며 오타가 생기기 좋다.
### 헝가리안 표기법
- 이제는 방해가 된다.

```java
// bad
PhoneNumber phoneString; // 타입은 바뀌어도 이름이 바뀌진 않는다.
```

### 멤버 변수 접두어
- 멤버 변수에 `m_`을 붙일 필요 없다. 클래스와 함수는 접두어가 필요없을 정도로 작아야 마땅하다.

```java
// bad
public class Part {
    private String m_dsc;
    void setName(String name) {
        m_dsc = name;
    }
}

// good
public class Part {
    String description;
    void setDescription(String description) {
        this.description = description;
    }
}
```

### 인터페이스 클래스와 구현 클래스
- (저자는 개인적으로) 인터페이스 이름은 접두어를 붙이지 않는 편이 좋다(고 생각한다).
- 인터페이스 클래스 이름과 구현 클래스 이름 중 하나를 인코딩해야 한다면 구현 클래스 이름을 택하겠다.

## 자신의 기억력을 자랑하지 마라
- 독자가 코드를 읽으면서 변수 이름을 자신이 아는 이름으로 변환해야 한다면 그 변수 이름은 바람직하지 못하다.
- 똑똑한 프로그래머와 전문가 프로그래머 사이에서 나타나는 차이점 하나만 들자면, 전문가 프로그래머는 명료함이 최고라는 사실을 이해한다.

## 클래스 이름
- 클래스 이름과 객체 이름은 명사나 명사구가 적합하다.

```java
// bad
CustomerManager, WifiPageProcessor, AccountData, AddressParserInfo

// good
Customer, WikiPage, Account, AddressParser
```

## 메서드 이름
- 메서드 이름은 동사나 동사구가 적합하다. (`postPayment`, `deletePage`, `save`)
- 생성자 중복정의를 할 때는 정적 팩토리 메서드를 사용한다. 메서드는 인수를 설명하는 이름을 사용한다.

```java
// bad
Complex fulcrumPoint = new Complex(23.0);

// good
Complex fulcrumPoint = Complex.fromRealNumber(23.0);
```

## 기발한 이름은 피하라
- 재미난 이름보다는 명료한 이름을 선택하라.
- 특정 문화에서만 사용하는 농담은 피하는 편이 좋다.
- 의도를 분명하고 솔직하게 표현하라.

## 한 개념에 한 단어를 사용하라
- 추상적인 개념 하나에 단어 하나를 선택해 이를 고수한다.
- 예를 들어, 똑같은 메서드를 클래스마다 `fetch`, `retrieve`, `get`으로 제각각 부르면 혼란스럽다.
- 마찬가지로, 동일 코드 기반에 `controller`, `manager`, `driver`를 섞어 쓰면 혼란스럽다.

## 말장난을 하지 마라
- 한 단어를 두 가지 목적으로 사용하지 마라.
- 예를 들면, 두 개를 더하거나 이어서 새로 값을 만드는 것은 `add`이나, 집합에 값 하나를 추가하는 것은 `add`가 아닌 `append`가 적당하다.

## 해법 영역에서 가져온 이름을 사용하라
- 코드를 읽을 사람도 프로그래머라는 사실을 명심한다.
- 모든 이름을 문제 영역(domain)에서 가져오는 정책은 현명하지 못하다.
- 같은 개념을 다른 이름으로 이해하던 동료들이 매번 고객에게 의미를 물어야 하기 때문이다.
- 기술 개념에는 기술 이름이 가장 적합한 선택이다.

## 문제 영역에서 가져온 이름을 사용하라
- 적절한 프로그래머 용어가 없다면 문제 영역(domain)에서 이름을 가져온다.
- 프로그래머가 분야 전문가에게 의미를 물어 파악할 수 있다.
- 우수한 프로그래머와 설계자라면 해법 영역과 문제 영역을 구분할 줄 알아야 한다.

## 의미 있는 맥락을 추가하라
- 모든 방법이 실패하면 마지막 수단으로 접두어를 붙인다.
- `state`만으로는 주소란 사실을 알 수 없으니, 이름을 `addrState`라고 붙이는 것이 맥락이 붙어 좋다.

```java
// bad
private void printGuessStatistics(char candidate, int count) {
    String number;
    String verb;
    String pluralModifier;
    if (count == 0) {
        number = "no";
        verb = "are";
        pluralModifier = "s";
    } else if (count == 1) {
        number = "1";
        vberb = "is";
        pluralModifier = "";
    } else { ... }

    String guessMessage = String.format(
        "There %s %s %s%s", verb, number, candidate, pluralModifier
    );
    print(guessMessage);
}

// good
// 의미있는 맥락을 나눔
public class GuessStatisticsMessage {
    private String number;
    private String verb;
    private String pluralModifier;

    public String make(char candidate, int count) {
        createPluralDependentParts(count);
        return String.format(
            "There %s %s %s%s", verb, number, candidate, pluralModifier
        );
    }

    private void createPluralDependentMessageParts(int count) {
        if (count == 0) {
            thereAreNoLetters();
        } else if (count == 1) {
            thereIsOneLetter();
        } else { ... }
    }

    private void thereIsOneLetter() {
        number = "1";
        vberb = "is";
        pluralModifier = "";
    }

    private void thereAreNoLetters() {
        number = "no";
        verb = "are";
        pluralModifier = "s";
    }
}
```

## 불필요한 맥락을 없애라
- 일반적으로는 짧은 이름이 긴 이름보다 좋다.
- 단, 의미가 분명한 경우에 한해서다.
- `accountAddress`와 `customerAddress`는 `Address` 클래스 인스턴스로는 좋은 이름이나, 클래스 이름으로는 적합하지 못하다.

## 마치면서
- 좋은 이름을 선택하는 능력은 기술, 비즈니스, 관리 문제가 아니라 교육 문제이다.
- 암기는 요즘 나오는 도구에게 맡기고, 우리는 문장이나 문단처럼 읽히는 코드가 아니면 적어도 표나 자료 구조처럼 읽히는 코드를 짜는 데만 집중해야 마땅하다.

---
