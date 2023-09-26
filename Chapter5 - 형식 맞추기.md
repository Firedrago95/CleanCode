## 목차
> [\- 형식을 맞추는 목적](#c1)
> 
> [\- 적절한 행 길이를 유지하라](#c2)
> 
> [1\. 신문기사처럼 작성하라](#c3)
> 
> [2\. 개념은 빈 행으로 분리하라](#c4)
> 
> [3\. 세로 밀집도](#c5)
> 
> [4\. 수직 거리](#c6)
> 
> [5\. 세로 순서](#c7)
> 
> [\- 가로형식 맞추기](#c8)
> 
> [1\. 가로 공백과 밀집도](#c9)
> 
> [2\. 가로 정렬](#c10)
> 
> [3\. 들여쓰기](#c11)
> 
> [4\. 가짜범위](#c12)
> 
> [\- 팀 규칙](#c13)
> 
> [\- 밥 아저씨의 형식 규칙](#c14)

### **형식을 맞추는 목적**

-   코드의 기능은 변하지만 스타일과 가독성은 유지보수, 확장성에 계속 영향을 끼친다. '기능하는 코드' 보다 중요한 것은 '**원할한 소통**'을 가능하게 하는 코드이다.  
      
    

### 적절한 행 길이를 유지하라

-   대부분의 프로그램은 200줄 미만의 코드로 동작한다. 코드의 길이를 줄여라

### **신문기사처럼 작성하라**

-   이름만 보고도 올바른 모듈인지 판단할 수 있어야한다. 잘 짜여진 기사 처럼, 첫문단은 전체를 표현한다.
-   첫 부분은 고차원 개념과 알고리즘을 설명한다. 아래로 내려갈 수록 세세하게 묘사한다.

### **개념은 빈 행으로 분리하라**

-   일련의 행 묶음은 하나의 완결된 생각을 표현한다. 생각과 생각사이는 빈행으로 구분한다.
-   패키지 선언부, import 문, 각 함수 사이에는 빈 행이 들어간다.

```java
// 행의 구분이 없는 경우 
package fitnesse.wikitext.widgets;
import java.util.regex.*;
public class BoldWidget extends ParentWidget {
	public static final String REGEXP = "'''.+?'''";
	private static final Pattern pattern = Pattern.compile("'''(.+?)'''",
		Pattern.MULTILINE + Pattern.DOTALL);
	public BoldWidget(ParentWidget parent, String text) throws Exception {
		super(parent);
		Matcher match = pattern.matcher(text); match.find(); 
		addChildWidgets(match.group(1));}
	public String render() throws Exception { 
		StringBuffer html = new StringBuffer("<b>"); 		
		html.append(childHtml()).append("</b>"); 
		return html.toString();
	} 
}
```

(숨이 턱 막힌다)

```java
// 행 구분을 하는 경우
package fitnesse.wikitext.widgets;

import java.util.regex.*;

public class BoldWidget extends ParentWidget {
	public static final String REGEXP = "'''.+?'''";
	private static final Pattern pattern = Pattern.compile("'''(.+?)'''", 
		Pattern.MULTILINE + Pattern.DOTALL
	);
	
	public BoldWidget(ParentWidget parent, String text) throws Exception { 
		super(parent);
		Matcher match = pattern.matcher(text);
		match.find();
		addChildWidgets(match.group(1)); 
	}
	
	public String render() throws Exception { 
		StringBuffer html = new StringBuffer("<b>"); 
		html.append(childHtml()).append("</b>"); 
		return html.toString();
	} 
}
```

###   
**세로 밀집도**

-   줄바꿈이 개념을 구분한다면 세로 밀집도는 연관성을 의미한다. 밀접한 행은 세로 가까이 놓여야 한다.

```java
/**
* The class name of the reporter listener 
*/
private String m_className;

/**
* The properties of the reporter listener 
*/
private List<Property> m_properties = new ArrayList<Property>();
public void addProperty(Property property) { 
m_properties.add(property);
```

(멤버변수가 주석으로 인해 분리되었다)

```java
public class ReporterConfig {
  private String m_className;
  private List<Property> m_properties = new ArrayList<Property>();
	
  public void addProperty(Property property) { 
    m_properties.add(property);
  }
```

(멤버변수가 세로로 가까이 놓이고 , 함수에 공백, 쉽게 읽힌다)

### **수직 거리**

-   서로 밀접한 개념은 세로로 가까이 둬야한다. 그렇지 않으면 미로같은 코드가 될 수 있다.
-   같은 파일에 속할 정도로 밀접한 두 개념은 세로 거리로 연관성을 표현한다.
-   변수는 사용하는 위치에 최대한 가까이 선언한다.   
    지역변수는 함수 맨 앞에 선언한다. (지역변수)  
      
    

```java
// InputStream 지역변수는 함수 맨앞에 선언한다
private static void readPreferences() {
  InputStream is = null;
  try {
    is = new FileInputStream(getPreferencesFile()); 
    setPreferences(new Properties(getPreferences())); 
    getPreferences().load(is);
  } catch (IOException e) { 
  try {
    if (is != null) 
      is.close();
  } catch (IOException e1) {
    } 
  }
}
```

```java
// 긴 함수에서는 블록 상단 또는 루프 직전에 변수를 선언 할 수 있다.
for (XmlTest test : m_suite.getTests()) {
  TestRunner tr = m_runnerFactory.newTestRunner(this, test);
  tr.addListener(m_textReporter); 
  m_testRunners.add(tr);

  invoker = tr.getInvoker();
	
  for (ITestNGMethod m : tr.getBeforeSuiteMethods()) { 
    beforeSuiteMethods.put(m.getMethod(), m);
  }

  for (ITestNGMethod m : tr.getAfterSuiteMethods()) { 
    afterSuiteMethods.put(m.getMethod(), m);
  } 
}
```

-   인스턴스 변수는 클래스 맨 처음에 선언한다. (인스턴스 변수)
-   한 함수가 다른 함수를 호출한다면 (종속함수) 두 함수는 세로로 가까이 배치한다.  
    호출하는 함수가 호출되는 함수보다 위에 위치한다.

```java
... // 호출하는 함수와 호출되는 함수를 세로로 가깝게 둔다.
public Response makeResponse(FitNesseContext context, Request request) throws Exception {
    String pageName = getPageNameOrDefault(request, "FrontPage");
    loadPage(pageName, context); 
    if (page == null)
        return notFoundResponse(context, request); 
    else
        return makePageResponse(context); 
    }

private String getPageNameOrDefault(Request request, String defaultPageName) {
    String pageName = request.getResource(); 
    if (StringUtil.isBlank(pageName))
        pageName = defaultPageName;

    return pageName; 
}
...
```

-   비슷한 동작을 수행하는 (개념적 유사성) 등 친화도가 높은 코드를 가까이 배치한다.

```java
// 서로를 호출하는 것을 제외하고 봐도, 기능이나 구조가 유사하다 (친화도가 높다)
public class Assert {
  static public void assertTrue(String message, boolean condition) {
      if (!condition) 
          fail(message);
  }

  static public void assertTrue(boolean condition) { 
      assertTrue(null, condition);
  }

  static public void assertFalse(String message, boolean condition) { 
      assertTrue(message, !condition);
  }

  static public void assertFalse(boolean condition) { 
      assertFalse(null, condition);
  } 
...
```

### **세로 순서**

-   가장 중요한 개념을 가장 먼저 표현한다. 중요한 사항을 표현할대는 세세한 사항을 최대한 배제한다.

### 가로형식 맞추기

-   통계적으로 한 행은 80자를 넘지 않는다. 최대 120자 정도로 행 길이를 제한하라

### **가로 공백과 밀집도**

-   가로 공백은 밀접한 개념과 느슨한 개념을 표현한다.

```java
//할당 연산자 기준으로 양옆에 공백 두 항을 확실히 구분한다
totalChar += lineSize;

//함수 이름과 이어지는 괄호 사이에는 공백을 넣지 않는다
recordWidestLine(lineSize);

//함수 안의 인수는 공백으로 분리한다. 별개라는 사실을 강조한다
go(lineSize, lineCount);

//곱셈은 공백이 없고, 덧셈은 공백을 준다. 곱셈은 하나의 항이기 때문
return b*b - 4*a*c
```

### **가로 정렬**

-   선언문과 할당문을 정렬하지 않는다. 정렬하지 않을때 결함을 찾기가 더 쉬워진다. 

```java
// 변수유형을 무시하고 변수명을 먼저 보게된다. 정렬하지 말자
public class FitNesseExpediter implements ResponseSender {
	private		Socket		  socket;
	private 	InputStream 	  input;
	private 	OutputStream 	  output;
	private 	Reques		  request; 		
	private 	Response 	  response;	
	private 	FitNesseContex	  context;
 ...
```

###   
**들여쓰기**

-   들여쓰기한 파일은 구조가 한눈에 들어온다. 들여쓰기는 범위로 이루어진 계층을 표현한다.

### **가짜범위**

-   빈 while 문이나 for 문은 최대한 사용을 피하되 피하지 못한다면 올바로 들여쓰고 괄호로 감싸준다.

### **팀 규칙**

-   소스코드 스타일은 일관되어야한다. 팀에서 정한 규칙이 있다면 팀원은 그 규칙을 따라야한다.

### **밥 아저씨의 형식 규칙**

-   저자가 위의 형식 규칙을 적용하여 작성한 코드를 감상해보자

```java
public class CodeAnalyzer implements JavaFileAnalysis { 
	private int lineCount;
	private int maxLineWidth;
	private int widestLineNumber;
	private LineWidthHistogram lineWidthHistogram; 
	private int totalChars;
	
	public CodeAnalyzer() {
		lineWidthHistogram = new LineWidthHistogram();
	}
	
    // 자바 파일을 담은 List를 반환
	public static List<File> findJavaFiles(File parentDirectory) { 
		List<File> files = new ArrayList<File>(); 
		findJavaFiles(parentDirectory, files);
		return files;
	}
	
	private static void findJavaFiles(File parentDirectory, List<File> files) {
		for (File file : parentDirectory.listFiles()) {
			if (file.getName().endsWith(".java")) 
				files.add(file);
			else if (file.isDirectory()) 
				findJavaFiles(file, files);
		} 
	}
	
    // 한줄씩 분석
	public void analyzeFile(File javaFile) throws Exception { 
		BufferedReader br = new BufferedReader(new FileReader(javaFile)); 
		String line;
		while ((line = br.readLine()) != null)
			measureLine(line); 
	}
	
	private void measureLine(String line) { 
		lineCount++;
		int lineSize = line.length();
		totalChars += lineSize; 
		lineWidthHistogram.addLine(lineSize, lineCount);
		recordWidestLine(lineSize);
	}
	
	private void recordWidestLine(int lineSize) { 
		if (lineSize > maxLineWidth) {
			maxLineWidth = lineSize;
			widestLineNumber = lineCount; 
		}
	}
	
    //getter 모음
	public int getLineCount() { 
		return lineCount;
	}

	public int getMaxLineWidth() { 
		return maxLineWidth;
	}

	public int getWidestLineNumber() { 
		return widestLineNumber;
	}

	public LineWidthHistogram getLineWidthHistogram() {
		return lineWidthHistogram;
	}
	
    // 한줄당 평균 글자수 구하기
	public double getMeanLineWidth() { 
		return (double)totalChars/lineCount;
	}
    
	// 중앙값을 기반으로 글자 수 반환
	public int getMedianLineWidth() {
		Integer[] sortedWidths = getSortedWidths(); 
		int cumulativeLineCount = 0;
		for (int width : sortedWidths) {
			cumulativeLineCount += lineCountForWidth(width); 
			if (cumulativeLineCount > lineCount/2)
				return width;
		}
		throw new Error("Cannot get here"); 
	}
	
	private int lineCountForWidth(int width) {
		return lineWidthHistogram.getLinesforWidth(width).size();
	}
	
	private Integer[] getSortedWidths() {
		Set<Integer> widths = lineWidthHistogram.getWidths(); 
		Integer[] sortedWidths = (widths.toArray(new Integer[0])); 
		Arrays.sort(sortedWidths);
		return sortedWidths;
	} 
}
```
