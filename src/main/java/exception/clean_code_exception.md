# Clean Code

- 오류 처리는 중요하다. 하지만 오류 처리 코드로 인해 프로그램 논리를 이해하기 어려워진다면 깨끗한 코드라 부르기 어렵다.

## 오류코드보다 예외를 사용하라
- 오류코드를 사용하면, 호출한 즉시 오류를 확인해야 되기 때문에 호출자 코드가 복잡해진다.

```java
// bad
if (handle != DeviceBandle.INVALID) {
    ...
}
```

- 예외를 던지면 호출자 코드가 깔끔해진다.

```java
// good
public void sendShutdown() {
    try {
        tryToShutdown();
    } catch (DeviceShutdownError e) {
        ...
    }
}
```

## Try-Catch-Finally 문부터 작성하라
- try 블록에서 무슨일이 생기든지 catch 블록은 프로그램 상태를 일관성 있게 유지해야 한다.
- 그러므로 예외가 발생할 코드를 짤 때는 try-catch-finally문으로 시작하는 편이 낫다.

## 미확인 예외를 사용하라
- 자바 첫 버전이 확인된 예외를 선보였던 당시는 Checked Exception이 멋진 아이디어로 여겨졌지만, 지금은 안정적인 소프트웨어를 제작하는 요소로 확인된 예외가 반드시 필요하지 않다는 사실이 분명해졌다.
- 확인된 예외는 OCP를 위반한다.
    - 메서드에 확인된 예외를 던졌는데 `catch` 블록이 세 단계 위에 있다면 그 사이 메서드가 모두 선언부에 해당 예외를 정의해야 한다.
- 확인된 오류를 던진다면 함수는 선언부에 `throws`절을 추가해야 한다.
- 일반적인 애플리케이션은 의존성이라는 비용이 이익보다 크다.

## 예외에 의미를 제공하라
- 예외를 던질 때는 전후 상황을 충분히 덧붙인다. 그러면 오류가 발생한 원인과 위치를 찾기가 쉬워진다.
- 오류 메시지에 정보를 담아 예외와 함께 던진다. 실패한 연산 이름과 실패 유형도 언급한다.

## 호출자를 고려해 예외 클래스를 정의하라
- 오류를 정의할 때 프로그래머에게 가장 중요한 관심사는 오류를 잡아내는 방법이 되어야 한다.

```java
// bad
try {
    port.open();
} catch (DeviceResponseException e) {
    ...
} catch (ATM1212UnlockedException e) {
    ...
}

// good
// 호출하는 라이브러리 API를 감싸면서 예외 유형 하나를 반환
try {
    port.open();
} catch (PortDeviceFailure e) {
    ...
}
```

- 실제로 외부 API를 사용할 때는 감싸기 기법이 최선이다.
- 외부 API를 감싸면 외부 라이브러리와 프로그램 사이에서 의존성이 크게 줄어든다.
- 감싸기 기법을 사용하면 특정 업체가 API를 설계한 방식에 발목 잡히지 않는다.

## 정상 흐름을 정의하라
- 아래 good 예시는 **특수 사례 패턴**이라 부른다.
- 클래스를 만들거나 객체를 조작해 특수 사례를 처리하는 방식이다.
- 그러면 클라이언트 코드가 예외적인 상황을 처리할 필요가 없어진다.

```java
// bad
try {
    MealExpenses expenses = expenseReportDAO.getMeals(employee.getID());
    m_total += expenses.getTotal();
} catch (MealExpensesNotFound e) {
    m_total += getMealPerDiem();
}

// good
MealExpenses expenses = expenseReportDAO.getMeals(employee.getID());
m_total += expenses.getTotal();

public class PerDiemMealExpenses implements MealExpenses {
    public int getTotal() {
        // 기본값으로 일일 기본 식비를 반환.
    }
}
```

## null을 반환하지 마라
- null을 반환하는 코드는 일거리를 늘릴 뿐만 아니라 호출자에게 문제를 떠넘긴다.
- 누구 하나라도 null을 확인하지 않으면 애플리케이션이 통제 불능에 빠질지도 모른다.
- null을 반환하고 싶은 유혹이 든다면, 예외를 던지거나 특수 사례 객체를 반환한다.

```java
// good
public List<Employee> getEmployees() {
    if (..직원이 없다면..) {
        return collections.emptyList();
    }
}
```

## null을 전달하지 마라
- 메서드에 null을 전달하는 방식은 더 나쁘다.
- 정상적인 인수로 null을 기대하는 API가 아니라면 메서드로 null을 전달하는 코드는 최대한 피한다.
- null 예외를 직접 잡거나, `assert`를 사용하는 것도 방법이다.

```java
public class MetricsCalculator {
    public double xProjection(Point p1, Point p2) {
        assert p1 != null : "p1 shoudl not be null";
        assert p2 != null : "p2 shoudl not be null";
        return (p2.x - p1.x) * 1.5;
    }
}
```

## 결론
- 깨끗한 코드는 읽기도 좋아야 하지만 안정성도 높아야 한다.
- 오류 처리를 프로그램 논리와 분리해 독자적인 사안으로 고려하면 튼튼하고 깨끗한 코드를 작성할 수 있다.
