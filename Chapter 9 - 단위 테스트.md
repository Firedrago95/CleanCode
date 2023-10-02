#### **TDD 법칙 세가지**

-   첫번째 : 실패하는 단위 테스트를 작성할 때까지 실제 코드를 작성하지 않는다.
-   두번째 : 컴파일은 실패하지 않으면서 실행시 실패하는 정도로만 단위 테스트를 작성한다
-   세번째 : 현재 실패하는 테스트를 통과할 정도로만 실제 코드를 작성한다.

#### **깨끗한 테스트 코드 유지하기**

-   테스트 코드는 실제 코드 못지 않게 사고와 설계와 주의가 필요하다
-   테스트 코드가 지저분하면 실제코드보다 더 복잡해진다.  
    테스트 코드를 없애면, 코드 변경이 어려워지고, 실제코드가 망가진다.

#### **깨끗한 테스트 코드**

-   깨끗한 테스트 코드를 위해서는 '가독성'을 높여야 한다.
-   가독성은 명료함, 단순성, 풍부한 표현력이 필요하다.

```java
public void testGetPageHierarchyAsXml() throws Exception {
    crawler.addPage(root, PathParser.parse("PageOne"));
    crawler.addPage(root, PathParser.parse("PageOne.ChildOne"));
    crawler.addPage(root, PathParser.parse("PageTwo"));
    
    request.setResource("root");
    request.addInput("type","pages");
    Responder responder = new SerializedPageResponder();
    SimpleResponse response = 
      (SimpleResponse) responder.makeResponse(new FitnesseContext(root), request);
    String xml = response.getContent();
    
    assertEquals("text/xml", response.getContentType());
    assertSubString("<name>PageOne</name>", xml);
    assertSubString("<name>PageTwo</name>", xml);
    assertSubString("<name>Childone</name>", xml);
}
```

코드가 복잡하다 더 간단히 만들어보자

```java
public void testGetPageHierarchyAsXml() throws Exception {
    makePages("PageOne", "PageOne.ChildOne", "PageTwo");
    
    SubmitRequest("root", "type:pages");
    
    assertResponseIsXml();
    assertResponseContains(
      "<name>PageOne</name>","<name>PageTwo</name>","<name>ChildOne</name>"
    );
}
```

BUILD - OPERATE - CHECK 패턴 (테스트자료 만들기, 테스트 자료 조작, 결과확인)  사용, 훨씬더 가독성이 좋아졌다.

#### \- 이중 표준

-   테스트 코드는 실제코드와 깨끗한 코드의 기준이 다를 수 있다.  
    메모리나 cpu 효율에 상대적으로 더 자유롭기 때문이다.

```java
@Test
public void turnOnLoTempAlarmAtThreshold() throws Exception {
    hw.setTemp(WAY_TOO_COLD);
    controller.tic();
    assertTrue(hw.heaterState());
    assertTrue(hw.blowerState());
    assertFalse(hw.coolerState());
    assertFalse(hw.hiTempAlarm());
    assertTrue(hw.loTempAlarm());
}
```

급격한 온도 하강시 상태체크 하는 테스트 코드인데 리팩토링 해보면

```java
@Test
public void turnOnLoTempAlarmAtThreshold() throws Exception {
    wayTooCold();
    assertEqualse("HBchL", hw.getState());
}
```

True , False를 대문자, 소문자로 표현했다. 한눈에 부울값을 파악할 수 있게 된다.

```java
public String getState() {
    String state = "";
    state += heater ? "H" : "h";
    state += blower ? "B" : "b";
    state += coller ? "C" : "c";
    state += hiTempAlarm ? "H" : "h";
    state += loTempAlarm ? "L" : "l";
    return state;
}
```

테스트 환경은 실제코드와 달리 자원이 제한적일 가능성이 낮다. StringBuffer가 더 효율적이지만 코드가 지저분해진다.  테스트 코드 환경에서는 깨끗한 코드에대한 다른 기준이 적용될 수 있다. (메모리나 cpu 제한에서 자유롭다)

#### **테스트당 assert 하나**

-   assert는 줄일 수록 좋다. 하지만 반드시 한개만 사용할 필요는 없다.  
    중복이 발생할 수 있기 때문이다.

```java
public void testGetPageHierarchyAsXml() throws Exception { 
    givenPages("PageOne", "PageOne.ChildOne", "PageTwo");
  
    whenRequestIsIssued("root", "type:pages");
  
    thenResponseShouldBeXML(); 
}

public void testGetPageHierarchyHasRightTags() throws Exception { 
    givenPages("PageOne", "PageOne.ChildOne", "PageTwo");
  
    whenRequestIsIssued("root", "type:pages");
  
    thenResponseShouldContain(
      "<name>PageOne</name>", "<name>PageTwo</name>", "<name>ChildOne</name>"
    ); 
}
```

assert 하나를 유지하려고 하니 중복이 발생한다. 상속이나, @Before를 사용할 수 있지만, 배보다 배꼽이 더 크다.

#### \- 테스트당 개념하나

-   개념당 assert문 수를 최소로 줄이고, 테스트 함수는 개념하나만 테스트 하자

```java
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

1\. 30일로 끝나는 달의 마지막 날짜는 31일이 될수 없다.

2\. 두달을 더하면 31일이 되어야한다

3\. 31일로 끝나는 달에 한달을 더하면 30일이 되어야 한다.     
총 세가지의 개념을 한번에 테스트 한다. 개념을 분리해야 한다.

#### **F.I.R.S.T**

-   빠르게 (Fast) : 테스트 코드는 실행이 빨라야 자주 확인할 수 있다.
-   독립적 (Independent) : 테스트 코드는 서로 의존하면 안된다. 의존이 발생하면 문제의 원인을 찾기 어렵다
-   반복가능 (Repeatable) : 테스트 코드는 어떤 환경에서도 반복가능해야한다. 
-   자가검증 (Self-Validating) : 테스트 코드는 스스로 부울 값을 반환해야한다. 주관적인 판단이 가능한 여지 주지않음
-   적시에 (Timely) : 테스트코드는 **실제코드 작성 직전에 작성**한다. 실제코드 이후에 작성하면 테스트 코드 작성이 어려운 경우가 생긴다.

#### **결론**

-   테스트 코드를 깨끗하게 유지해라
-   테스트 API를 구현하여 도메인 특화 언어를 만들자
