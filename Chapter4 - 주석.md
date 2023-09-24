> [주석은 나쁜 코드를 보완하지 못한다.](#c1)
> 
> [코드로 의도를 표현하라](#c2)
> 
> [\[좋은 주석\]](#c3)
> 
> [1\. 법적인 주석](#c4)
> 
> [2\. 정보를 제공하는 주석](#c5)
> 
> [3\. 의도를 설명하는 주석](#c6)
> 
> [4\. 의미를 명료하게 밝히는 주석](#c7)
> 
> [5\. 결과를 경고하는 주석](#c8)
> 
> [6\. TODO 주석](#c9)
> 
> [7\. 중요성을 강조하는 주석](#c10)
> 
> [8\. 공개 API에서 Javadocs](#c11)
> 
> [\[나쁜 주석\]](#c12)
> 
> [1\. 주절거리는 주석](#c13)
> 
> [2\. 같은 이야기를 중복하는 주석](#c14)
> 
> [3\. 의무적으로 다는 주석](#c15)
> 
> [4\. 이력을 기록하는 주석](#c16)
> 
> [5\. 있으나 마나 한 주석](#c17)
> 
> [6\. 무서운 잡음](#c18)
> 
> [7\. 함수나 변수로 표현 할 수 있다면 주석을 달지마라](#c19)
> 
> [8\. 위치를 표시하는 주석](#c20)
> 
> [9\. 닫는 괄호에 다는 주석](#c21)
> 
> [10\. 공로를 돌리거나 저자를 표시하는 주석](#c22)
> 
> [11\. 주석으로 처리한 코드](#c23)
> 
> [12\. HTML 주석](#c24)
> 
> [13\. 전역정보](#c25)
> 
> [14\. 너무 많은 정보](#c26)
> 
> [15\. 모호한 관계](#c27)
> 
> [16\. 함수 헤더](#c28)
> 
> [17\. 비공개 코드에서 Javadocs](#c29)

### **주석은 나쁜 코드를 보완하지 못한다.**

-   주석은 코드로 의도를 표현하지 못할때 사용한다. **코드만이 정확한 정보를 제공하는 유일한 출처**다  
    **주석을 줄이도록** 꾸준히 노력해야 한다.

### **코드로 의도를 표현하라**

-   깔끔하며 주석이 없는 코드가, 복잡하고 어수선하며 주석이 많은 코드보다 훨씬 좋다.

### 좋은 주석  -  유익하거나 필요한 주석 

###   
**법적인 주석**

-   소스파일 첫머리에 깔끔하게 정리된 저작권 정보와 소유권 정보

### **정보를 제공하는 주석**

-   기본적인 정보를 제공하는 주석, 하지만 이때도 코드로 주석을 없앨 수 있다.

```java
//테스트 중인 Responder 인스턴스를 반환한다
protected abstract Responder responderInstance();

//위 주석은 나쁘지 않지만, 함수명을 바꿔줌으로써 주석이 필요없어진다.
protected abstract Responder responderBeingTested();
```

```java
// kk:mm:ss EEE, MMM dd yyyy 형식이다. (정규표현식을 설명)
pattern timeMatcher = pattern.compile("\\d*:\\d*:\\d* \\w*, \\w* \\d*, \\d*");
```

### **의도를 설명하는 주석**

-   구현을 이해하는것을 넘어서 결정에 깔린 의도까지 설명하는 경우

```java
public int compareTo(Object O) {
    if (o instanceof WikiPagePath) {
        WikiPagePath p = (WikiPagePath) o;
        String compressedName = StringUtil.join(names,"");
        String compressedArgumentName = StringUtil.join(p.names, "");
        return compressedName.compareTo(compressedArgumentName);
    }
    return 1; //좋은 유형이므로 정렬 순위가 더 높다
}
```

(WikiPagePath 클래스의 객체와 비교하여 두 WikiPagePath 객체의 names 배열을 합친 문자열을 비교하고 그 결과에 따라 정렬 순서를 결정하는 코드 ( wikiPagePath 객체가 아니면 무조건 this 객체가 우선))

```java
public void testConcurrentAddWidgets() throws Exception {
    WidgetBuilder widgetBuilder = new WidgetBuilder(new Class[]{BoldWidget.class});
    String text = "'''bold text'''";
    PratentWidget parent = new BoldWidget(new MockWidgetRoot(), "'''bold text'''");
    AtomicBoolean failFlag = new AtomicBoolean();
    failFlag.set(false);

    // 스레드를 대량 생성하는 방법으로 어떻게든 경쟁 조건을 만들려 시도한다.
    for (int i=0; i<25000; i++) {
       WidgetBuilderThread widgetBuilderThread = new WidgetBuilderThread(widgetBuilder, text, parent, failFlag);
     Thread thread = new Thread(widgetBuilderThread);
    thread.start();
     }
    assertEquals(false, failFlag.get());
    }
}
```

 ( 솔직히 이 코드 이해못했다.;; 어렵다)

### **의미를 명료하게 밝히는 주석**

-   인수나 반환값을 명확하게 바꾸기 어려운 표준라이브러리나 변경불가 코드인 경우 의미를 밝혀주는 주석

### **결과를 경고하는 주석**

-   다른 프로그래머에게 결과를 경고하는 주석

```java
// 여유시간이 충분하지 않다면 실행하지 마십시오
public void _testWithReallyBigFile( ) {
	writeLinesToFile(10000000);
    response.setBody(testFile);
    response.readyToSend(this);
    String responseString = output.toString( );
    assertSubString("Content-Length: 1000000000", responsestring);
    assertTrue(bytesSent > 1000000000);
}
```

(큰 파일을 생성하고 해당 파일의 내용을 HTTP 응답으로 보내고, 그 응답의 내용과 길이를 확인하는 코드)

```java
public static SimpleDataformat makeStandardHttpDateFormat( ) {
	// SimpleDateFomat은 스레드에 안전하지 못하다.
    // 따라서 각 인스턴스를 독립적으로 생성해야 한다.
    SimpleDateFormat df = new SimpleDateFormat("EEE, dd MMM yyyy HH:mm:ss z");
    return df;
}
```

### **TODO 주석**

-   TODO 주석을 통해 '앞으로 할 일'을 주석으로 남긴다.
-   필요없는 기능의 삭제 알림, 문제를 봐달라는 요청, 좋은 이름을 부탁, 발생할 이벤트에 맞춰 수정할것 등

```java
// TODO-MdM 현재 필요하지 않다.
// 체크아웃 모델을 도입하면 함수가 필요 없다.
protected VersionInfo makeVersion() throws Exception {
	return null;
}
```

### **중요성을 강조하는 주석**

-   자칫 넘어갈 수 있는 중요한 내용을 강조하는 주석

```java
String listItemContent = match.group(3).trim();
// 여기서 trim은 정말 중요하다. trim 함수는 문자열에서 시작 공백을 제거한다.
// 문자열에 시작 공백이 있으면 다른 문자열로 인식되기 때문이다.
```

### **공개 API에서 Javadocs**

-   공개 API를 구현한다면 반드시 Javadocs도 깔끔하게 작성해야한다.   
    Javadocs도 주석처럼 잘못된 정보를 전달하지 않도록 주의해야한다.

### 나쁜 주석  - 대부분의 주석은 여기에 속한다

###   
**주절거리는 주석**

-   올바른 의미 판독이 불가능한 주석

```java
public void loadProperties() {
    try {
        String propertiesPath = propertiesLocation +"/" + PROPERTIES_FILE;
        FileInputStream propertiesStream = new FileInputStream(propertiesPath);
        loadedProperties.load(propertiesStream);
    } catch (IOException e) {
        // 속성 파일이 없다면 기본값을 모두 메모리로 읽어 들였다는 의미다.
    }
}
```

(파일이 없을때, 누가 어떻게 기본값을 읽어들이는지, 예외가 어디서 발생했는지 알 수 없다.저자만이 알고 있을 뿐이다.)

### **같은 이야기를 중복하는 주석**

-   코드의 내용과 주석의 내용이 중복되는 경우 
-   의도나 근거를 설명하는 것도 아니고 읽기도 쉽지 않다.

```java
// this.closed가 true일 때 반환되는 유틸리티 메서드다.
// 타임아웃에 도달하면 예외를 던진다.
public synchronized void waitForClose(final long timeoutMillis) throws Exception {
    if (!closed) {
        wait(timeoutMillis);
        if (!closed) 
          throw new Exception("MockResponseSender could not be closed"); 
    }
}
```

(의도나 근거도 없는데 부정확한 주석, this.closed가 true로 변하는 순간에 메서드는 반환되지 않는다.)

```java
/** 
* 컨테이너 관련된 Logger 구현
*/
protected Log logger = null;

/**
* 관련된 logger 이름
*/
protected string logName = null;

/**
* 사람이 읽을 수 있는 컨테이너 이름
*/
protected string name = null;
```

(주석의 목적에 기여하지 못한다.)

### **의무적으로 다는 주석**

-   모든 함수에 Javadocs를 달거나 모든 변수에 주석을 달아야 한다는 법칙은 없다. 오히려 혼동을 초래할 수 있다.

```java
/**
* @param title CD 제목
* @oaram author CD 저자
* @oaram tracks CD 트랙 숫자
* @param durationInMinutes CD 길이(단위: 분)
*/
public void addCD(String title, String author, int tracks, int durationInMinutes) {
    CD cd = new CD();
    cd.title = title;
    cd.author = author;
    cd.tracks = tracks;
    cd.duration = durationInMinutes;
    cdList.add(cd);
}
```

### **이력을 기록하는 주석**

-   더이상 편집 로그를 주석에 달지마라, github 에 맡겨라

###   
**있으나 마나 한 주석**

-   너무 당연한 사실을 언급하여, 새로운 정보제공이 없는 주석을 사용하지마라

```java
//기본 생성자
protected AnnualDateRule()

//월 중 일자
private int dayOfMonth;
```

```java
try {
  response.add(ErrorResponder.makeExceptionString(e));
  response.closeAll();
} catch {
  // 이게뭐야!!! 
}
```

(코드 짜다가 빡쳤나보다.....)

### **무서운 잡음**

-   Javadocs 문서도 잡음이 될 수 있다.

```java
/** The name. */
private String name;

/** The version */
private String version;
```

### **함수나 변수로 표현 할 수 있다면 주석을 달지마라**

-   주석은 코드를 개선하여 표현할 방법이 없을 때 사용하는 것이다.

```java
// 전역목록 <smodule>에 속하는 모듈이 우리가 속한 하위 시스템에 의존하는가?
if (smodule.getDependSubsystems().contains(subSysMod.getSubSystem()))

ArrayList moduleDependees = smodule.getDependendSubsystems();
String ourSubsystem = subsysMod.getSubSyste();
if (moduleDependees.contains(ourSubSystem))
```

(코드로 상세하게 표현하면 주석은 필요없어진다.)

### **위치를 표시하는 주석**

-   소스코드에 위치를 표시하기위한 주석은 가독성을 낮춘다.

### **닫는 괄호에 다는 주석**

-   닫는 괄호에 주석을 달고싶어진다면, 함수를 줄이도록 시도해보자

```java
try {
    while ((line = in.readLine()) != null) {
        lineCount++;
        charCount += line.length();
        string words[] = line.split("\\W");
        wordCount += words.length;
    } //while
    System.out.println("wordCount = " + wordCount);
    System.out.println("lineCount = " + lineCount);
    System.out.println("charCount = " + charCount);
} // try
```

(코드가 길면 주석으로 끝을 표시해주고 싶어진다. 주석대신 코드를 줄이자)

### **공로를 돌리거나 저자를 표시하는 주석**

-   Github 는 누가 언제 어떻게 추가했는지 똑똑히 기억한다. 주석으로 표시해줄 필요는 없다.

### **주석으로 처리한 코드**

-   주석으로 처리된 코드는 다른 사람들이 지우는것을 주저하게 만든다. 점점 쓸모없는 코드가 쌓인다.
-   코드를 기억하는 것은 Github에 맡겨라

### **HTML 주석**

-   HTML 주석은 가독성이 매우 떨어진다.  사용하지 말자

### **전역정보**

-   주석은 근처의 코드만 설명해야한다. 시스템 어딘가에 있는 다른 코드를 여기서 설명하지 마라

```java
// 적합성 테스트가 동작하는 포트: 기본값은 <b>8082</b>
public void setFitnessePort(int fitnessePort) {
    this.fitnessePort = fitnesseport;
}
```

(이 함수에서는 포트 기본값을 다루지 않는다. 다른 함수에 달아야할 주석인 것이다. HTML 주석은 덤이다.)

### **너무 많은 정보**

-   주석에다 관련없는 정보를 장황하게 늘어놓지 마라

### **모호한 관계**

-   주석과 주석이 설명하는 코드는 둘 사이의 관계가 명확해야 한다.

```java
/* 
* 모든 픽셀을 담을 만큼 충분한 배열로 시작한다(여기에 필터 바이트를 더한다.)
* 그리고 헤더 정보를 위해 200바이트를 더한다.
*/
this.pngBytes = new byte[((this.width+1) * this.height * 3) + 200];
```

(필터바이트의 의미가 불명확하다. 필터바이트는 +1인가?  \*3인가?)

### **함수 헤더**

-   짧은 함수는 설명이 필요없다. 이름을 잘 붙이고 한가지 일을 수행하는 짧은 함수는 함수헤더 주석이 필요없다.

### **비공개 코드에서 Javadocs**

-   공개하지 않을 코드라면 Javadocs는 필요없다. 

사용하지 말아야할 여러 주석 유형이 있는 것 처럼 보이지만 결국 **코드로 드러나지 않는 정보를 짧게 제공하는 주석**이 중요하다는 것을 알 수 있다. 하지만 그 어떤 경우에도 **주석은 없는 것이 최고**다. 코드가 그만큼 잘 표현되었다는 의미니까
