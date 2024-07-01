# Clean Code

## 외부 코드 사용하기
- 패키지 제공자나 프레임워크 제공자는 적용성을 최대한 넓히려 애쓴다.
- 사용자는 자신의 요구에 집중하는 인터페이스를 바란다.
- 이런 긴장으로 인해 시스템 경계에서 문제가 생길 소지가 많다.
- 아래 코드에서 `Map`이 반환하는 `Object`를 올바른 유형으로 변환할 책임은 `Map`을 사용하는 클라이언트에 있다.

```java
Map sensors = new HashMap();
Sensor s = (Senson) sensors.get(sensorId);
```

- 아래와 같이 캡슐화 하는 것이 좀 더 좋은 방법이다. Sensor의 사용자는 제네릭스가 사용되었는지 여부에 신경 쓸 필요가 없다.

```java
public class Sensors {
    private Map sensors = new HashMap();

    public Sensor getById(String id) {
        return (sensor) sensors.get(id);
    }
}
```

- Map과 같은 경계 인터페이스를 이용할 때는 이를 이용하는 클래스나 클래스 계열 밖으로 노출되지 않도록 주의한다.

## 경계 살피고 익히기
- 외부 패키지 테스트가 우리 책임은 아니다.
- 하지만 우리 자신을 위해 우리가 사용할 코드를 테스트하는 편이 바람직하다.
- 간단한 테스트 케이스를 작성해 외부 코드를 익히는 것을 학습 테스트라고 한다.
- 학습 테스트는 API를 사용하려는 목적에 초점을 맞춘다.

## log4j 익히기

```java
@Test
public void testLogCreate() {
    Logger logger = Logger.toLogger("MyLogger");
    logger.info("hello");
}
```

- 돌려봤더니 `Appender`가 필요하다는 오류 발생

```java
@Test
public void testLogAddAppender() {
    Logger logger = Logger.toLogger("MyLogger");
    ConsoleAppender appender = new ConsoleAppender();
    logger.addApender(appender);
    logger.info("hello");
}
```

- 출력 스트림이 없다는 사실 발견

```java
@Test
public void testLogAddAppender() {
    Logger logger = Logger.toLogger("MyLogger");
    logger.removeAllAppenders();
    logger.addApender(new ConsoleAppender(
        new PatternLayout("%p %t %m%n"),
        ConsoleAppender.SYSTEM_OUT
    );
    logger.info("hello");
}
```

- 이제 학습했으니, 독자적인 로거 클래스로 캡슐화하면 나머지 프로그램들은 log4j 경계 인터페이스를 몰라도 된다.

## 학습 테스트는 공짜 이상이다.
- 학습 테스트는 이해도를 높여주는 정확한 실험이다.
- 투자하는 노력보다 얻는 성과가 더 크다.
- 패키지 새 버전이 나온다면 학습 테스트를 돌려 차이가 있는지 확인한다.
- 이런 경계 테스트가 있다면 패키지의 새 버전으로 이전하기 쉬워진다.

## 아직 존재하지 않는 코드를 사용하기
- 경계와 관련해 또 다른 유형은 아는 코드와 모르는 코드를 분리하는 경계다.
- 이 모르는 경계를 인터페이스로 구현하면 우리가 인터페이스를 전적으로 통제한다는 장점이 생긴다.
- 또한 코드 가독성도 높아지고 코드 의도도 분명해진다.

## 깨끗한 경계
- 소프트웨어 설계가 우수하다면 변경하는데 많은 투자와 재작업이 필요하지 않다.
- 통제하지 못하는 코드를 사용할 때는 너무 많은 투자를 하거나 향후 변경 비용이 지나치게 커지지 않도록 각별히 주의해야 한다.
- 통제가 불가능한 외부 패키지에 의존하는 대신 통제가 가능한 우리 코드에 의존하는 편이 훨씬 좋다.
- 외부 패키지를 호출하는 코드를 가능한 줄여 경계를 관리하자.
- 새로운 클래스로 경계를 감싸거나 아니면 **Adapter 패턴**을 사용해 우리가 원하는 인터페이스를 패키지가 제공하는 인터페이스로 변환하자.
