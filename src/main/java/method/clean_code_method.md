# Clean Code

## 작게 만들어라
- 오랜 시행착오를 바탕으로 작은 함수가 좋다고 확신한다.

### 블록과 들여쓰기
- `if`, `else`, `while` 문 등에 들어가는 블록은 한 줄이 좋다.
- 그러면 바깥을 감싸는 함수가 작아질 뿐 아니라, 블록 안에서 호출하는 함수 이름을 적절히 짓는다면, 코드를 이해하기도 쉬워진다.
- 함수에서 들여쓰기 수준은 1단이나 2단을 넘어서면 안 된다.

## 한 가지만 해라
- 함수는 한 가지를 해야 한다. 그 한 가지를 잘 해야 한다. 그 한 가지만을 해야한다.
- 지정된 함수 이름 아래에서 추상화 수준이 하나인 단계만 수행한다면 그 함수는 한 가지 작업만 한다.
- 우리가 함수를 만드는 이유는 큰 개념을 다음 추상화 수준에서 여러 단계로 나눠 수행하기 위해서다.
- 단순히 다른 표현이 아니라 의미 있는 이름으로 다른 함수를 추출할 수 있다면, 그 함수는 여러 작업을 하는 셈이다.

### 함수 내 섹션
- 한 가지 작업만 하는 함수는 자연스럽게 섹션으로 나누기 어렵다.

## 함수 당 추상화 수준은 하나로
- 함수가 확실히 한 가지 작업만 하려면 함수 내 모든 문장의 추상화 수준이 동일해야 한다.
- 한 함수 내에 추상화 수준을 섞으면 코드를 읽는 사람이 헷갈린다.

### 위에서 아래로 코드 읽기 : 내려가기 규칙
- 한 함수 다음에는 추상화 수준이 한 단계 낮은 함수가 온다.
- 위에서 아래로 프로그램을 읽으면 함수 추상화 수준이 한 번에 한 단계씩 낮아진다. 이것이 '내려가기 규칙'이다.
- 핵심은 짧으면서도 한 가지만 하는 함수다.

## Switch문
- 각 switch문을 저차원 클래스에 숨기고 절대로 반복하지 않는 방법이 있다.

```java
// bad 
// 1. 함수가 길다.
// 2. 한 가지 작업만 수행하지 않는다.
// 3. SRP를 위반한다.
// 4. OCP를 위반한다.
public Money calculatePay(Employee e) throws InvalidEmployeeType {
    switch (e.type) {
        case COMMISSIONED:
            return calculateCummisionedPay(e);
        case HOURLY:
            return calculateHourlyPay(e);
        case SALARIED:
            return calculateSalariedPay(e);
        default:
            throw new InvalidEmployeeType(e.type);
    }
}

// good
// switch문을 추상 팩토리에 숨긴다.
public abstract class Employee {
    public abstract boolean isPayday();
    public abstract Money calculatePay();
    public abstract void deliveryPay(Money pay);
}

public interface EmployeeFactory {
    public Employee makeEmployee(EmployeeRecord r) throws InvalidEmployeeType;
}

public class EmployeeFactoryImpl implements EmployeeFactory {
    public Employee makeEmployee(EmployeeRecord r) throws InvalidEmployeeType {
        switch (r.type) {
            case COMMISSIONED:
                return new CommisionedEmployee(r);
            case HOURLY:
                return new HourlyEmployee(r);
            case SALARIED:
                return new SalariedEmployee(r);
            default:
                throw new InvalidEmployeeType(r.type);
        }
    }
}
```

## 서술적인 이름을 사용하라
- 함수가 작고 단순할수록 서술적인 이름을 고르기도 쉬워진다.
- 길고 서술적인 이름이 짧고 어려운 이름보다 좋다.
- 서술적인 이름을 사용하면 개발자 머릿속에서도 설계가 뚜렷해지므로 코드를 개선하기 쉬워진다.
- 이름을 붙일 때는 일관성이 있어야 한다. 모듈 내에서 함수 이름은 같은 문구, 명사, 동사를 사용한다.

## 함수 인수
- 인수는 가급적 적게 사용한다.
- 인수는 어렵다. 인수는 개념을 이해하기 어렵게 만든다.
- 인수가 3개를 넘어가면 인수마다 유효한 값으로 모든 조합을 구성해 테스트하기가 상당히 부담스러워진다.
- 최선은 입력 인수가 없는 경우이며, 차선은 입력 인수가 1개뿐인 경우다.

### 많이 쓰는 단항 형식
- 인수에 질문을 던지는 경우
    - `boolean fileExists("MyFile")`
- 인수를 뭔가로 변환해 결과를 반환하는 경우
    - `InputStream fileOpen("MyFile")`
- 드물지만, 이벤트 형식(입력 인수만 있고, 출력인수는 없다.)
- 위 3가지가 아닌 경우, 가급적 단항 함수는 피한다.

### 플래그 인수
- 함수가 한꺼번에 여러가지를 처리한다고 공표하는 인수이다.
- 쓰지 않는다.

### 이항 함수
- 단항 함수보다 이해가 어려워진다.
- 인수들의 순서를 기억해야 하기 떄문에 좋지 않다.

### 삼항 함수
- 순서, 주춤, 무시로 야기되는 문제가 두 배 이상 늘어난다.

### 인수 객체
- 인수가 2~3개 필요하다면 일부를 독자적인 클래스 변수로 선언할 가능성을 짚어본다.
- `Circle makeCircle(Point center, double radius)`

### 인수 목록
- 가변 인수를 취하는 함수는 단항, 이항, 삼항 함수로 취급할 수 있다.

### 동사와 키워드
- 단항 함수는 함수와 인수가 동사/명사 쌍을 이뤄야 한다.
    - `write(name)`
- 함수에 키워드를 추가하면, 인수 순서를 기억할 필요가 없어진다.
    - `assertEquals` -> `assertExpectedEqualsActual`

## 부수 효과를 일으키지 마라
- 부수효과는 함수에서 한 가지를 하겠다고 약속하고서 몰래 다른짓을 하는 '거짓말'이다.

```java
// bad
// Session.initialize()는 사용자를 인증하면서 기존 세션 정보를 지워버린다.
// checkPasswordAndInitializeSession 이라는 이름이 더 어울린다.
public boolean checkPassword(String userName, String password) {
    User user = UserGateway.findByName(userName);
    if (user != User.NULL) {
        String codedPhrase = user.getPhraseEncodedByPassword();
        String phrase = cryptographer.decrypt(codingPhrase, password);
        if ("Valid Password".equals(phrase)) {
            Session.initialize();
            return true;
        }
    }
    return false;
}
```

### 출력 인수
- 객체 지향 언어에서는 출력 인수를 사용할 필요가 거의 없다.
- `this`를 활용한다.

```java
// bad
appendFooter(s);

// good
report.appendFooter();
```

## 명령과 조회를 분리하라
- 함수는 뭔가를 수행하거나 뭔가에 답하거나 둘 중 하나만 해야 한다.
- 둘 다 하면 안 된다.
- 명령과 조회를 분리해 혼란을 야기하지 않는다.

```java
// bad
public boolean set(String attribute, String value);
if (set("username", "uncle")) { ... }

// good
if (attributeExists("username")) {
    setAttribute("username", "uncle") {
        ...
    }
}
```

## 오류 코드보다 예외를 사용하라
- 명령 함수에서 오류 코드를 반환하는 방식은 명령/조회 분리 규칙을 미묘하게 위반한다.
- 동사/형용사 혼란을 일으키지 않는 대신 여러 단계로 중첩되는 코드를 야기한다.
- 예외를 사용하면 오류 처리 코드가 원래 코드에서 분리되므로 코드가 깔끔해진다.

```java
// bad
if (deletePage(page) == E_OK) {
    if (registry.deleteReference(page.name) == E_OK) {
        ...
    }
}

// good
try {
    deletePage(page);
    registry.deleteReference(page.name);
} catch (Exception e) {
    ...
}
```

### Try/Catch 블록 뽑아내기
- `try/catch`는 코드 구조에 혼란을 일으키며, 정상 동작과 오류 처리 동작을 뒤섞는다.
- 그러므로 `try/catch` 블록을 별도 함수로 뽑아내는 편이 좋다.
- 정상 동작과 오류 처리 동작을 분리하면 코드를 이해하고 수정하기 쉬워진다.

```java
// good
public void delete(Page page) {
    try {
        deletePageAndAllReference(page);
    } catch (Exception e) {
        logError(e);
    }
}

private void deletePageAndAllReference(Page page) throws Exception {
    deletePage(page);
    registry.deleteReference(page.name);
    configKeys.deleteKey(page.name.makeKey());
}
```

### 오류처리도 한 가지 작업이다.
- 오류를 처리하는 함수는 오류만 처리해야 마땅하다.

### Error.java 의존성 자석
- 오류 코드를 반환한다는 이야기는, 클래스든 열거형 변수든, 어디선가 오류 코드를 정의한다는 뜻이다.
- 오류 코드 대신 예외를 사용하면 새 예외는 Exeception 클래스에서 파생된다.
- 따라서 재컴파일/재배치 없이도 새 예외 클래스를 추가할 수 있다.

## 반복하지 마라
- 중복은 문제다.
- 코드 길이가 늘어날 뿐 아니라 알고리즘이 변하면 네 곳이나 손봐야 한다.
- 게다가 어느 한 곳이라도 빠뜨리는 바람에 오류가 발생할 확률도 네 배나 높다.

## 구조적 프로그래밍
- `Edsger Dijkstra`는 모든 함수와 함수 내 모든 블록에 입구와 출구가 하나만 존재해야 한다고 말한다.
- 가급적 `return`문은 하나이고, `break`나 `continue`를 사용해선 안된다.
- 함수를 작게 만든다면 간혹 `return`, `break`, `continue`를 여러 차례 사용해도 괜찮다.

## 함수를 어떻게 짜죠?
1. 처음에는 길고 복잡하게, 들여쓰기도 많고 중복 루프도 많다. 인수 목록도 아주 길다. 이름은 즉흥적이고 중복된다. 다만, 단위 테스트를 만든다.
2. 코드를 다듬고, 함수를 만들고, 이름을 바꾸고, 중복을 제거한다. 메소드를 줄이고 순서를 바꾼다.

## 결론
- 함수는 그 언어에서 동사이며, 클래스는 명사다.
- 프로그래밍의 기술은 언제나 언어 설계의 기술이다.
- 대가 프로그래머는 시스템을 구현할 프로그램이 아니라 풀어갈 이야기로 여긴다.
- 프로그래밍 언어라는 수단을 사용해 좀 더 풍부하고 좀 더 표현력이 강한 언어를 만들어 이야기를 풀어간다.

```java
// good example

public class SetupTeardownIncluder {
  private PageData pageData;
  private boolean isSuite;
  private WikiPage testPage;
  private StringBuffer newPageContent;
  private PageCrawler pageCrawler;

  public static String render(PageData pageData) throws Exception {
    return render(pageData, false);
  }

  public static String render(PageData pageData, boolean isSuite)
    throws Exception {
    return new SetupTeardownIncluder(pageData).render(isSuite);
  }

  private SetupTeardownIncluder(PageData pageData) {
    this.pageData = pageData;
    testPage = pageData.getWikiPage();
    pageCrawler = testPage.getPageCrawler();
    newPageContent = new StringBuffer();
  }

  private String render(boolean isSuite) throws Exception {
    this.isSuite = isSuite;
    if (isTestPage())
      includeSetupAndTeardownPages();
    return pageData.getHtml();
  }

  private boolean isTestPage() throws Exception {
    return pageData.hasAttribute("Test");
  }

  private void includeSetupAndTeardownPages() throws Exception {
    includeSetupPages();
    includePageContent();
    includeTeardownPages();
    updatePageContent();
  }
  private void includeSetupPages() throws Exception {
    if (isSuite)
      includeSuiteSetupPage();
    includeSetupPage();
  }

  private void includeSuiteSetupPage() throws Exception {
    include(SuiteResponder.SUITE_SETUP_NAME, "-setup");
  }

  private void includeSetupPage() throws Exception {
    include("SetUp", "-setup");
  }

  private void includePageContent() throws Exception {
    newPageContent.append(pageData.getContent());
  }

  private void includeTeardownPages() throws Exception {
    includeTeardownPage();
    if (isSuite)
      includeSuiteTeardownPage();
  }

  private void includeTeardownPage() throws Exception {
    include("TearDown", "-teardown");
  }

  private void includeSuiteTeardownPage() throws Exception {
    include(SuiteResponder.SUITE_TEARDOWN_NAME, "-teardown");
  }

  private void updatePageContent() throws Exception {
    pageData.setContent(newPageContent.toString());
  }

  private void include(String pageName, String arg) throws Exception {
    WikiPage inheritedPage = findInheritedPage(pageName);
    if (inheritedPage != null) {
      String pagePathName = getPathNameForPage(inheritedPage);
      buildIncludeDirective(pagePathName, arg);
    }
  }

  private WikiPage findInheritedPage(String pageName) throws Exception {
    return PageCrawlerImpl.getInheritedPage(pageName, testPage);
  }

  private String getPathNameForPage(WikiPage page) throws Exception {
    WikiPagePath pagePath = pageCrawler.getFullPath(page);
    return PathParser.render(pagePath);
  }

  private void buildIncludeDirective(String pagePathName, String arg) {
    newPageContent
      .append("\n!include ")
      .append(arg)
      .append(" .")
      .append(pagePathName)
      .append("\n");
  }
}
```
