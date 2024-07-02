# Clean Code

## TDD 법칙 세 가지
- 첫째 법칙
    - 실패하는 단위 테스트를 작성할 때까지 실제 코드를 작성하지 않는다.
- 둘째 법칙
    - 컴파일은 실패하지 않으면서 실행이 실패하는 정도로만 단위 테스트를 작성한다.
- 셋째 법칙
    - 현재 실패하는 테스트를 통과할 정도로만 실제 코드를 작성한다.
- 위 세가지 규칙을 따르면 개발과 테스트가 대략 30초 주기로 묶인다.
- 이렇게 일하면 매일 수십 개, 매달 수백 개, 매년 수천 개에 달하는 테스트 케이스가 나온다.

## 깨끗한 테스트 코드 유지하기
- 실제 코드가 진화하면 테스트 코드도 변해야 한다.
- 실제 코드를 변경해 기존 테스트 케이스가 실패하기 시작하면, 지저분한 코드로 인해 실패하는 테스트 케이스를 점점 더 통과시키키 어려워진다.
- 그래서 테스트 코드는 계속해서 늘어나는 부담이 되어버린다.
- 실패를 초래한 원인은 테스트 코드를 막 짜도 좋다고 허용한 결정이었다. 테스트 코드를 깨끗하게 짰다면 테스트에 쏟아 부은 노력은 허사로 돌아가지 않았을 터이다.
- 테스트 코드는 사고와 설계와 주의가 필요하다. 실제 코드 못지 않게 깨끗하게 짜야 한다.

### 테스트는 유연성, 유지보수성, 재사용성을 제공한다.
- 테스트 코드를 깨끗하게 유지하지 않으면 결국은 잃어버린다.
- 그리고 테스트 케이스가 없으면 실제 코드를 유연하게 만드는 버팀목도 사라진다.
- 테스트는 유연성, 유지보수성, 재사용성을 제공한다. 테스트 케이스가 있으면 변경이 쉬워지기 때문이다.
- 따라서 테스트 코드가 지저분하면 코드를 변경하는 능력이 떨어지며 코드 구조를 개선하는 능력도 떨어진다.

## 깨끗한 테스트 코드
- 어쩌면 가독성은 실제 코드보다 테스트 코드에 더더욱 중요하다.
- 명료성, 단순성, 풍부한 표현력이 필요하다. 테스트 코드는 최소의 표현으로 많은 것을 나타내야 한다.

```java
// bad
public void testGetPageHieratchyAsXml() throws Exception {
    crawler.addPage(root, PathParser.parse("PageOne"));
    crawler.addPage(root, PathParser.parse("PageOne.ChildOne"));
    crawler.addPage(root, PathParser.parse("PageTwo"));

    request.setResource("root");
    request.addInput("type", "pages");
    Responder responder = new SerializedPageResponder();
    SimpleResponse response =
        (SimpleResponse) responder.makeResponse(new FitNesseContext(root), request);
    String xml = response.getContent();

    assertEquals("text/xml", response.getContentType());
    assertSubString("<name>PageOne</name>", xml);
    assertSubString("<name>PageTwo</name>", xml);
    assertSubString("<name>ChildOne</name>", xml);
}

public void testGetPageHieratchyAsXmlDoesntContainSymbolicLinks() throws Exception {
    WikiPage pageOne = crawler.addPage(root, PathParser.parse("PageOne"));
    crawler.addPage(root, PathParser.parse("PageOne.ChildOne"));
    crawler.addPage(root, PathParser.parse("PageTwo"));

    PageData data = pageOne.getData();
    WikiPageProperties properties = data.getProperties();
    WikiPageProperty symLinks = properties.set(SymbolicPage.PROPERTY_NAME);
    symLinks.set("SymPage", "PageTwo");
    pageOne.commit(data);

    request.setResource("root");
    request.addInput("type", "pages");
    Responder responder = new SerializedPageResponder();
    SimpleResponse response =
        (SimpleResponse) responder.makeResponse(new FitNesseContext(root), request);
    String xml = response.getContent();

    assertEquals("text/xml", response.getContentType());
    assertSubString("<name>PageOne</name>", xml);
    assertSubString("<name>PageTwo</name>", xml);
    assertSubString("<name>ChildOne</name>", xml);
    assertNotSubString("SymPage", xml);
}

public void testGetDataAsHtml() throws Exception {
    crawler.addPage(root, PathParser.parse("TestPageOne"), "test page");

    request.setResource("TestPageOne"); request.addInput("type", "data");
    Responder responder = new SerializedPageResponder();
    SimpleResponse response =
        (SimpleResponse) responder.makeResponse(new FitNesseContext(root), request);
    String xml = response.getContent();

    assertEquals("text/xml", response.getContentType());
    assertSubString("test page", xml);
    assertSubString("<Test", xml);
}
```

```java
// good
public void testGetPageHierarchyAsXml() throws Exception {
    makePages("PageOne", "PageOne.ChildOne", "PageTwo");

    submitRequest("root", "type:pages");

    assertResponseIsXML();
    assertResponseContains(
        "<name>PageOne</name>", "<name>PageTwo</name>", "<name>ChildOne</name>");
}

public void testSymbolicLinksAreNotInXmlPageHierarchy() throws Exception {
    WikiPage page = makePage("PageOne");
    makePages("PageOne.ChildOne", "PageTwo");

    addLinkTo(page, "PageTwo", "SymPage");

    submitRequest("root", "type:pages");

    assertResponseIsXML();
    assertResponseContains(
        "<name>PageOne</name>", "<name>PageTwo</name>", "<name>ChildOne</name>");
    assertResponseDoesNotContain("SymPage");
}

public void testGetDataAsXml() throws Exception {
    makePageWithContent("TestPageOne", "test page");

    submitRequest("TestPageOne", "type:data");

    assertResponseIsXML();
    assertResponseContains("test page", "<Test");
}
```

- 테스트 코드는 본론에 돌입해 진짜 필요한 자료 유형과 함수만 사용한다.

### 도메인에 특화된 테스트 언어
- 이렇게 구현한 함수와 유틸리티는 테스트 코드에서 사용하는 특수 API가 된다. 즉, 테스트를 구현하는 당사자와 나중에 테스트를 읽어볼 독자를 도와주는 테스트 언어다.

### 이중 표준
- 테스트 코드에 적용하는 표준은 실제 코드에 적용하는 표준과 확실히 다르다.
- 단순하고, 간결하고, 표현력이 풍부해야 하지만, 실제 코드만큼 효율적일 필요는 없다.

## 테스트 당 assert 하나
- `assert`문이 단 하나인 함수는 결론이 하나라서 코드를 이해하기 쉽고 빠르다.
- 불행하게도, 테스트를 분리하면 중복되는 코드가 많아진다. 이는 Template Method 패턴으로 제거할 수 있다.

### 테스트 당 개념 하나
- 이것저것 잡다한 개념을 연속으로 테스트하는 긴 함수는 피한다.

```java
// bad
public void testAddMonths() {
    SerialDate d1 = SerialDate.createInstance(31, 5, 2004);

    SerialDate d2 = SerialDate.addMonths(1, d1); 
    assertEquals(30, d2.getDayOfMonth()); 
    assertEquals(6, d2.getMonth()); 
    assertEquals(2004, d2.getYYYY());

    SerialDate d3 = SerialDate.addMonths(2, d1); 
    assertEquals(31, d3.getDayOfMonth()); 
    assertEquals(7, d3.getMonth()); 
    assertEquals(2004, d3.getYYYY());

    SerialDate d4 = SerialDate.addMonths(1, SerialDate.addMonths(1, d1)); 
    assertEquals(30, d4.getDayOfMonth());
    assertEquals(7, d4.getMonth());
    assertEquals(2004, d4.getYYYY());
}
```

## F.I.R.S.T
- 깨끗한 테스트를 만드는 다섯가지 규칙
### Fast(빠르게)
- 테스트는 빨라야 한다.
- 테스트가 느리면 자주 돌릴 엄두를 못내고, 초반에 문제를 찾아내 고치지 못한다.
- 결국 코드 품질이 망가지기 시작한다.

### Independent(독립적)
- 각 테스트는 서로 의존하면 안 된다.
- 어떤 순서로 실행해도 괜찮아야 한다.

### Repeatable(반복 가능)
- 테스트는 어떤 환경에서도 반복 가능해야 한다.
- 테스트가 돌아가지 않는 환경이 하나라도 있다면 테스트가 실패한 이유를 둘러댈 변명이 생긴다.

### Self-Validating(자가검증)
- 테스트는 Bool 값을 결과로 내야 한다.
- 성공 아니면 실패다.
- 통과 여부를 알려고 로그 파일을 읽게 만들어선 안 된다.

### Timely(적시)
- 테스트는 적시에 작성해야 한다.
- 단위 테스트는 테스트하려는 실제 코드를 구현하기 직전에 구현한다.
- 실제 코드를 구현한 다음에 테스트 코드를 만들면 실제 코드가 테스트하기 어렵다는 사실을 발견할지도 모른다.

## 결론
- 테스트 코드는 실제 코드만큼이나 프로젝트 건강에 중요하다. 어쩌면 실제 코드보다 더 중요하다.
- 테스트 코드는 지속적으로 깨끗하게 관리하자.
- 표현력을 높이고 간결하게 정리하자.
- 테스트 API를 구현해 도메인 특화 언어(DSL)을 만들자. 그러면 그만큼 테스트 코드를 짜기가 쉬워진다.
