## 1\. 의도를 분명히 밝혀라

-   변수이름은 변수의 존재이유, 수행하는 기능, 사용방법을 드러내야한다.
-   변수에 주석이 필요하다면 실패한 이름이다.
-   변수의 의미를 함축하지 않는다.

```java
int d; // 경과시간(단위:날짜) 

int daysSinceCreation
```

-   개념과 의미가 함축적인 코드를 리팩토링

```java
public List<int[]> getThem() { //코드가 함축적이라 의미를 알기 힘든 코드
	List<int[]> list1 = new ArrayList<int[]>();
    for (int[] x : theList) {
    	if (x[0] == 4) {
        list1.add(x);
        }
    }
    return list1;
}

public List<int[]> getFlaggedCells() { //개념만 붙여줘도 읽기 편한 코드가 된다.
	List<int[]> flaggedCells = new ArrayList<int[]>();
    for (int[] cell : gameBoard) {
    	if (cell[STATUS_VALUE] == FAGGED) {
        	flaggedCells.add(cell);
        }
    }
    return flaggedCells;
}

public List<int[]> getFlaggedCells() { 
	List<int[]> flaggedCells = new ArrayList<int[]>();
    for (Cell cell : gameBoard) { // 클래스로 cell을 감싼다.
    	if (cell.isFlagged()) {   // 클래스에 메서드로 상수를 감춘다.
        	flaggedCells.add(cell);
        }
    }
    return flaggedCells;
}
```

## 2\. 그릇된 정보를 파악하라

-   약어를 사용하지 마라  hp  ==> 삼각형 빗변 , UNIX 명령어 
-   널리쓰이는 의미가 있는 경우에 다른의미로 사용하는 것을 피하라
-   자료구조의 유형을 이름에 직접 사용하는 것을 피하라 acountList

## 3\. 의미있게 구분하라

-   의미가 전혀없는 알파벳, 숫자 사용하지 말 것   a1, a2 , aN
-   불용어의 사용을 피할것  Product,  ProductInfo , ProductData // Money,  MoneyAmount

## 4\. 발음하기 쉬운 이름을 사용하라

-   프로그래밍은 사회적인 활동이다. (협업해야한다.) 발음하기 쉬운 이름이 필요하다

```java
class DtaRcrd102 { // 발음하기 힘든 클래스명
	private Date genymdhms; // 발음하기 힘든 전역변수명
    private Date modymdhms;
    private final String pszqint = "102";
}

class Customer { // 의미가 명확하고 발음하기 쉬운 이름
	private Date generationTimestamp; 
    private Date modificationTimestamp;
    private final String recordId = "102";
}
```

## 5\. 검색하기 쉬운 이름을 사용하라

-   상수 '5' 는 포함된 이름을 모두 찾은뒤 의미를 살펴야 하지만 MAX\_CLASS\_PER\_STUDENT는 바로 검색가능하다.

## 6\. 인코딩을 피하라

-   변수에 타입을 표시해줄 필요없다.
-   멤버변수 , 전역변수 구분을 위한 접두어를 붙이지 마라
-   인터페이스 보다는 구현클래스에 인코딩한다   ShapeFactory (인터페이스)  => ShapeFactoryImp (구현클래스)

## 7.  자신의 기억력을 자랑하지 마라

-   독자가 변수 이름을 자신이 아는 개념으로 변환해야 한다면 잘못된 이름이다.
-   훌륭한 프로그래머는 명료한 이름을 붙임으로써 변수를 기억할 필요가 없다.

## 8.  클래스이름과 메서드 이름

-   클래스는 명사나 명사구 이름
-   메서드는 동사나 동사구 이름
-   **생성자를 오버로딩** 해야할때는 '**정적팩토리 메서드**' 사용한다.

## 9\. 기발한 이름은 피하라

-   유머감각이 들어간 변수명, 특정 문화에서만 사용하는 이름은 피한다.

## 10\. 한 개념에 한 단어를 써라

-   같은 메서드를 fetch, retrieve, get 제각각 부르는것은 옳지못하다.
-   Controller, Manager, Driver 등의 클래스 이름을 섞어 사용하는것도 마찬가지 

## 11\. 말장난을 하지마라

-   다른 개념에 한 단어를 쓰지 않도록 하라 ( 위 10번과 반대)
-   add, insert , append는 구분되어야 할 때가 있다.

## 12\. 해법 영역에서 가져온 이름을 사용하라

-   전문용어, 알고리즘 이름, 패턴이름 , 수학 용어등을 사용하라 (코드를 읽는 사람은 프로그래머)
-   VISITOR 패턴, JobQueue 등 프로그래머에게 친숙한 개념

## 13\. 문제 영역에서 가져온 이름을 사용하라

-   해법영역(12번) 에서 가져올 이름이 없을때는 문제 도메인에서 이름을 가져온다.

## 14\. 의미있는 맥락을 추가하라

-   클래스 , 함수 안에서 변수는 맥락을 가질 수 있다. 맥락은 더 잘 이해할 수 있도록 해준다.

```java
// 맥락을 알기힘든 전역변수 , 긴 함수
private void printGuessStatistics(char candidate, int count) {
    String number;
    String verb;
    String pluralModifier;
    if (count == 0) {
    	number = "no";
        verb = "are";
        pluralModifier = "s";
    } else if (count == 1) {
    	number= "1";
        verb = "is";
        pluralModifier = "";
    } else {
    	number = Integer.toString(count);
        verb = "are";
        pluralModifier = "s";
    }
    String guessMessage = String.format (
    	"There %s %s %s%s", verb, number, candidate, pluralModifier
    );
    print(guessMessage)
}
```

 클래스와 함수로 맥락을 부여하고, 함수를 작게 리팩토링  

```java
public class GuessStatisticsMessage { // 클래스로 맥락 부여
	String number;
    String verb;
    String pluralModifier;
	
    // 문장출력
    public String make(char candidate, int count) { //함수 쪼개기 (한가지 일)
    	createPluralDependentMessageParts(count);
        return String.foramat(
        "There %s %s %s%s",
        verb, number, candidate, pluralModifier);
    }
    
    // 분기별 실행
    private void createPluralDependentMessageParts(int count) {
    	if (count == 0) {
        	thereAreNoLetters(count);
        } else if (count == 1) {
        	thereIsOneLetter(count);
        } else {
        	thereAreManyLetters(count);
        }
    }
    
    // 실제 구현
    private void thereAreManyLetters(int count) {
    	number = Integer.toString(count);
        verb = "are";
        pluralModifier = "s";
    }
    
    private void thereAreNoLetters(int count) {
    	number = "1";
        verb = "is";
        pluralModifier = "";
    }
    
    private void thereAreNoLetters(int count) {
    	number = "no";
        verb = "are";
        pluralModifier = "s";
    }
}
```

## 15\. 불필요한 맥락을 없애라

-   주유소와 관련된 프로젝트라고 모든 클래스의 앞에 GSD (GasStationDeluxe) 같은 접두사를 붙여서는 안된다.
-   접두사를 붙이는 것은 모듈의 재사용 관점에서도 옳지 못하다.
