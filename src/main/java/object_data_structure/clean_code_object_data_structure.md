# Clean Code

## 자료 추상화
- 아래는 구현을 노출한다.

```java
public class Point {
    public double x;
    public double y;
}
```

- 아래는 자료구조 이상을 표현한다.
- 클래스 메서드가 접근 정책을 강제한다.
- 추상 인터페이스를 제공해 사용자가 구현을 모른 채 자료의 핵심을 조작할 수 있어야 진정한 의미의 클래스다.

```java
public interface Point {
    double getX();
    double getY();
    void setgCartesian(double x, double y);
    double getR();
    double getTheta();
    void setPolar(double r, double theta);
}
```

- 자료를 세세하게 공개하기보다는 추상적인 개념으로 표현하는 편이 좋다.

## 자료/객체 비대칭
- **객체**는 추상화 뒤로 자료를 숨긴 채 자료를 다루는 함수만 공개한다.
- **자료 구조**는 자료를 그대로 공개하며 별다른 함수는 제공하지 않는다.

- 아래의 각 도형은 간단한 자료구조다. 아무 메서드도 제공하지 않는다. (절차지향)
- 도형이 동작하는 방식은 `Geometry` 클래스에서 구현한다.
- 함수를 추가하기 위해선, 도형 클래스는 아무 영향도 받지 않고 `Geometry`에만 수정하면 된다.
- 새 도형을 추가하고 싶다면, `Geometry` 클래스에 속한 함수를 모두 고쳐야 한다.

```java
public class Square {
    public Point topLeft;
    public double side;
}

public class Rectangle {
    public Point topLeft;
    public double height;
    public double width;
}

public class Circle {
    public Point center;
    public double radius;
}

public class Geometry {
    public final double PI = 3.14;

    public double area(Object shape) throws NoSuchSpaceException {
        if (shape instanceof Square) {
            ...
        } else if (shape instanceof Rectangle) {
            ...
        } else if (shape instanceof Circle) {
            ...
        }
        throw new NoSuchShapeException();
    }
}
```

- 아래는 객체지향적인 도형 클래스다.
- 새 도형을 추가해도 기존 함수에 아무런 영향을 미치지 않는다.
- 새 함수를 추가하고 싶다면 도형 클래스를 전부 고쳐야 한다.

```java
public class Square implements Shape {
    public Point topLeft;
    public double side;

    public double area() {
        ...
    }
}

public class Rectangle implements Shape {
    public Point topLeft;
    public double height;
    public double width;

    public double area() {
        ...
    }
}

public class Circle implements Shape {
    public Point center;
    public double radius;

    public double area() {
        ...
    }
}
```

- 절차적인 코드는 기존 자료구조를 변경하지 않으면서 새 함수를 추가하기 쉽다.
- 객체 지향 코드는 기존 함수를 변경하지 않으면서 새 클래스를 추가하기 쉽다.
- 절차적인 코드는 새로운 자료 구조를 추가하기 어렵다. 그러려면 모든 함수를 고쳐야 한다.
- 객체 지향 코드는 새로운 함수를 추가하기 어렵다. 그러려면 모든 클래스를 고쳐야 한다.
- 추후 새로운 자료 타입이 필요한 경우, 클래스와 객체 지향 기법이 적합하다.
- 추후 새로운 함수가 필요한 경우, 절차적인 코드와 자료 구조가 좀 더 적합하다.

## 디미터 법칙
- 모듈은 자신이 조작하는 객체의 속사정을 몰라야 한다는 법칙이다.
- 다음 코드는 디미터 법칙을 어기고 있다.
    - `final String outputDir = ctxt.getOptions().getScratchDir().getAbsolutePath();`

### 기차 충돌
- 여러 객차가 한 줄로 이어진 기차처럼 보이기에, 위와 같은 예시는 **기차 충돌**이라고 부른다.
- 조잡하다고 여겨지는 방식이므로 피하는 것이 좋다.
- 아래와 같이 바꿔본다.
    - 디미터 법칙에 위반하는지 여부는 `ctxt`, `Options`, `ScatchDir`이 객체인지 자료구조인지에 달렸다.
    - 객체라면 내부구조를 숨겨야 하므로 디미터 법칙을 위반한다.
    - 자료 구조라면 내부 구조를 노출하므로 디미터 법칙이 적용되지 않는다.

```java
Options opts = ctxt.getOptions();
File scratchDir = opts.getScratchDir();
final String outputDir = scratchDir.getAbsolutePath();
```

### 잡종 구조
- 이런 혼란으로 반은 객체, 반은 자료구조인 잡종구조가 발생한다.
- 양쪽 세상의 단점만 모아놨으므로 되도록 피하는 편이 좋다.

### 구조체 감추기
- `ctxt.createScratchFileStream(classFileName);`

## 자료 전달 객체
- 대표적으로 DTO로, 공개 변수만 있고 함수가 없는 클래스다.
- 빈 객체는 공개 변수를 조회/설정 함수로 조작하는데, 별다른 이익이 없다.

### 활성 레코드
- 공개 변수가 있거나 비공개 변수에 조회/설정 함수가 있는 자료구조이다.
- 대개 `save`나 `find`와 같은 탐색 함수도 제공한다.
- 활성 레코드에 비즈니스 규칙 메서드를 추가하기도 하는데, 이러면 잡종구조가 되어버린다.
- 활성 레코드는 자료 구조로 취급되고, 비즈니스 규칙을 담으면서 내부 자료를 숨기는 객체는 따로 생성한다.

## 결론
- 객체는 동작을 공개하고 자료를 숨긴다.
- 그래서 기존 동작을 변경하지 않으면서 새 객체 타입을 추가하기는 쉬운 반면, 기존 객체에 새 동작을 추가하기는 어렵다.
- 자료 구조는 별다른 동작 없이 자료를 노출한다.
- 그래서 기존 자료 구조에 새 동작을 추가하기는 쉬우나, 기존 함수에 새 자료 구조를 추가하기는 어렵다.
- 어떤 시스템을 구현할 때, 새로운 자료 타입을 추가하는 유연성이 필요하면 객체가 더 적합하다.
- 다른 경우로 새로운 동작을 추가하는 유연성이 필요하면 자료 구조와 절차적인 코드가 더 적합하다.
