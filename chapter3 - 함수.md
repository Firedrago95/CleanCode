> [1\. 작게만들어라](#c1)
> 
> [2\. 한 가지만 해라](#c2)
> 
> [3\. Switch문](#c3)
> 
> [4\. 서술적인 이름을 사용하라](#c4)
> 
> [5\. 함수인수](#c5)
> 
> [6\. 부수 효과를 일으키지 마라](#c6)
> 
> [7\. 명령과 조회를 분리하라](#c7)
> 
> [8\. 오류 코드보다 예외를 사용하라](#c8)
> 
> [9\. 반복하지 마라](#c9)
> 
> [10\. 구조적 프로그래밍](#c10)
> 
> [11\. 함수를 짜는 방법](#c11)

## **작게만들어라**

-   함수는 작을 수록 좋다.

```java
//원래의 함수
public static String renderPageWithSetupsAndTeardowns(
  pageData pageData, boolean isSuite) throws Exception {
  boolean isTestPage = pageData.hasAttribute("Test");
  if (isTestPage) {
    wikiPage testPage = pageData.getWikiPage();
    StringBuffer newPageContent = new StringBuffer();
    includeSetupPages(testPage, newPageContent, isSuite);
    newPageContent.append(pageData.getContent());
    includeTeardownPages(testPage, newPageContent, isSuite);
    pageData.setContent(newPageContent.tostring());
  }
  return pageData.getHtml();
}

// 함수를 줄인 경우
public static String renderPageWithSetupsAndTeardowns (
  PageData pageData, boolean isSuite) throws Exception {
  if (isTestPage(pageData))
    includeSetupAndTeardownPages(pageData, isSuite);
  return pageData.getHtml();
}
```

-   함수의 **들여쓰기 수준은 2단을 넘으면 안된다**. 중첩 구조가 생길만큼 함수가 커지면 안된다

## **한 가지만 해라**

-   함수가 한가지만 하는지 판단하는 기준은 **추상화 수준이 하나**의 단계인지 살피는 것이다.
-   함수는 추상화 수준이 **한단계씩 낮아지면서 이야기**처럼 읽혀야 한다.
-   의미있는 이름으로 함수를 추출할 수 있다면 그 함수는 여러작업을 하는 것이다.

## **Switch문**

-   Switch 문은 다형성을 이용하여 철저히 숨기고, 반복하지 않도록 만든다.

```java
// Switch 문은 비슷한 구조를 많이 만들어낼 위험이 있다.
public Money CalculatePay(Employee e) { 
throws InvalidEmployeeType {
  switch (e.type) {
    case COMMISIONED:
      return calculatecommisionedPay(e);
    case HOURLY:
      return calculateHourlyPay(e);
    case SALARIED:
      return calculateSalariedPay(e);
    default:
      throw new InvalidemployeeType(e.type);
   }
}
```

```java
public abstract class Employee {
  public abstract boolean isPayday();
  public abstract Money calculatePay();
  public abstract void deliverpay(Money pay);
}
---------------------------------------------------
public interface EmployeeFactory {
  public Employee makeEmployee(EmployeeRecord r) throws InvalidEmployeeType;
}
---------------------------------------------------
// 인터페이스 구현체로 switch를 숨기고 반복하지 않도록 만든다
public class EmployeeFactoryImpl implements EmployeeFactory {
  public Employee makeEmployee(EmployeeRecord r) throws InvalidEmployeeType {
    switch (e.type) {
      case COMMISIONED:
        return calculatecommisionedPay(e);
      case HOURLY:
        return calculateHourlyPay(e);
      case SALARIED:
        return calculateSalariedPay(e);
      default:
        throw new InvalidemployeeType(e.type);
   	}
  }
}
```

## **서술적인 이름을 사용하라**

-   길고 서술적인 이름은 설계를 뚜렷하게 만들고, 코드 개선을 쉽게 만든다.
-   includeSetupAndTeardownPages , includeTeardownPages 등은 나쁜 이름이 아니다

## **함수인수**

-   이상적인 인수는 0개 , 다음으로 1개 , 그다음으로 2개 , 3개의 인수는 가능한 피해야한다.  
    인수는 코드를 이해하기 어렵게 만든다. 
-   인수가 1개인 경우는 인수에 질문을 던지는 경우, 변환해 결과를 반환하는 경우, 이벤트 함수의 입력인수 세가지
-   부울 값을 넘기는 플래그 인수는 되도록 피할 것
-   좌표값 (0, 0 ) 같은 자연적인 경우가 아니라면, 2개의 인수는 인수객체로 합치거나 (x, y => Point)   
    인수를 **클래스의 멤버변수**로 만들거나, **인수 이름의 클래스 멤버함수**로 정의한다.

## **부수 효과를 일으키지 마라**

-   부수효과는 함수가 외부에 영향을 미치는 경우를 말하며, 시간적결합, 순서종속을 초래한다.  
    (실행 순서가 강제되는 것)

```java
public class UserValidator {
    private Cryptographer cryptographer;
    
    public boolean checkPassword(String userName, String password) {
        User user = UserGateway.findByName(userName);
        if (user != User.NULL) {
          String codedPhrase = user.getPhraseEncodedByPassword();
          String phrase = cryptographer.decrypt(codedPhrase, password);
          if("Valid Password".equals(phrase)) {
            Session.initialize(); // 세션 초기화 (부수효과) , 시간적 결합 발생
            return true;
          }
        }
        return false;
    }
}
```

## **명령과 조회를 분리하라**

-   함수는 뭔가를 **수행** 하거나, **답**하거나 둘중 하나만 해야한다.  
    객체 상태를 변경하거나,  객체정보를 반환하거나 하나만 해야한다.
-   if (set("username", "unclebob")) 은 set 이라는 명령을 수행하는 동시에 답까지 하고 있다. 혼란을 유발한다.

## **오류 코드보다 예외를 사용하라**

-   오류코드는 실행코드와 오류처리가 섞인 구조이므로 try/ cath문으로 분리한다.

```java
if (deletePage(page) == E_OK) {
	if (registry.deleteReference(page.name) == E_OK) {
		if (configKeys.deleteKey(page.name.makeKey()) == E_OK) {
			logger.log("page deleted");
		} else {
			logger.log("configKey not deleted");
		}
	} else {
		logger.log("deleteReference from registry failed"); 
	} 
} else {
	logger.log("delete failed"); return E_ERROR;
}
```

```
public void delete(Page page) {
	try {
		deletePageAndAllReferences(page);
  	} catch (Exception e) {
  		logError(e);
  	}
}

private void deletePageAndAllReferences(Page page) throws Exception { 
	deletePage(page);
	registry.deleteReference(page.name); 
	configKeys.deleteKey(page.name.makeKey());
}

private void logError(Exception e) { 
	logger.log(e.getMessage());
}
```

-   오류를 사용하면 , 반드시 오류를 어딘가에서 정의해야한다.  오류코드가 변하면 재컴파일/ 재배치가 필요하다.  
    예외는 Exception 클래스에서 파생되므로 재컴파일/재배치 없이도 새로운 클래스를 추가 할 수 있다.

```java
// Enum 의 형태로 정의된 에러, 의존성이 생긴다.
public enum Error { 
	OK,
	INVALID,
	NO_SUCH,
	LOCKED,
	OUT_OF_RESOURCES, 	
	WAITING_FOR_EVENT;
}
```

### **반복하지 마라**

-   소프트웨어 개발에서 지금까지 일어난 모든 혁신은 중복을 제거하기 위한 지속적인 노력이다.   
    중복은 코드 길이를 늘리고, 오류의 가능성을 높인다.

### **구조적 프로그래밍**

-   함수를 작게 만든다면 return, break , continue 문을 사용해도 괜찮다.

### **함수를 짜는 방법**

-   처음부터 완벽한 코드를 짤 수 없다. 코드를 다듬고, 함수를 만들고, 이름을 바꾸고 중복을 제거한다. 
-   항상 단위테스트를 작성한다.
