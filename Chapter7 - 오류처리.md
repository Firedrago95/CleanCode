## 목차
[오류 코드보다 예외를 사용하라](#c1)

[Try-Catch-finally 문부터 작성하라](#c2)

[미확인(Unchecked) 예외를 사용하라](#c3)

[예외에 의미를 제공하라](#c4)

[호출자를 고려해 예외 클래스를 정의하라](#c5)

[정상 흐름을 정의하라](#c6)

[Null을 반환하지 마라](#c7)

[Null을 전달하지 마라](#c8)

[결론](#c9)

#### **INTRO**

-   오류처리는 깨끗한 코드와 연관성이 크다.  
    오류처리 코드가 여기저기 흩어져서 프로그램이 이해하기 힘들어진다면 깨끗한 코드라고 할 수 없다.

#### **오류 코드보다 예외를 사용하라**

-   오류코드를 사용하면 오류를 정의해야한다. (변경시 재컴파일이 요구된다.)
-   오류코드는 호출한 즉시 오류를 확인해야 하기때문에 논리코드와 오류처리의 코드가 섞인다.

```java
public class DeviceController {
  ...
  Devicehandle handle = getHandle(DEV1);
  //호출한 즉시 오류 확인해야함, 디바이스 상태를 점검한다.
  if (handle != Devicehandle.INCALID) { //오류가 상수형태로 미리 정의
    //레코드 필드에 디바이스 상태를 저장한다.
    retrieveDeviceRecord(handle);
    //디바이스가 일시정지 상태가 아니라면 종료한다.
    if (record.getStatus() != DEVICE_SUSPENDED) {
      pauseDevice(handle);
      clearDeviceWorkQueue(handle);
      closeDevice(handle);
    } else {
      logger.log("Device suspended. Unable to shut down");
    }
  } else {
    logger.log("Invalid handle for; " + DEV!.toString());
  }
  ...
}
```

오류 대신에 예외를 던지도록 리팩토링 해보자

```java
public class DeviceController {
    ...
    // 예외 처리
    public void sendShutDown() {
        try {
            tryToShutDown();
        } catch (DeviceShutDownError e) {
            logger.log(e);
        }
    }
    
    //논리 처리 
    private void tryToShutDown() {
        // 예외 발생 가능 부분
        DeviceHandle handle = getHandle(DEV1);
        DeviceRecord record = retrieveDeviceRecord(handle);
        
        pauseDevice(handle);
        clearDeviceWorkQueue(handle);
        closeDevice(handle);
    }
    
    // 예외발생
    private DeviceHandel getHandle(DeviceID id) {
        ...
        throw new DeviceShutDownError("Invalid handle for: " + id.toString());
    }
    
    ...
}
```

코드가 깔끔해지고, 오류처리와 알고리즘이 분리되어 코드 품질도 향상된다.

#### **Try-Catch-finally 문부터 작성하라**

-   1\. 강제로 예외를 일으키는 코드를 작성  2. 테스트를 통과하도록 코드를 작성한다.  3. 리팩토링한다. (TDD)

1\. 테스트 코드 작성

```java
// 예외를 발생시키는 테스트코드 작성
@Test(expected = StorageException.class)
public void retrieveSectionShouldThrownOnInvalidFileName() {
    sectionStore.retrieveSection("invalid - file");
}
```

2\. 단위 테스트에 맞춰 코드 구현

```java
// 실패하는 코드에서 -> 테스트 통과하는 코드 작성
public List<RecordedGrip> retrieveSection(String sectionName) {
    try {
        FileInputStream stream = new FileInputStream(sectionName);
    } catch (Exception e) {
        throw new StorageException("retrieval error", e);
    }
    return new ArrayList<RecordedGrip>();
}
```

3\. 리팩토링 한다

```java
// 예외의 범위를 좁힌다 Exception -> FileNotFoundException
public List<RecordedGrip> retrieveSection(String sectionName) {
    try {
        FileInputStream stream = new FileInputStream(sectionName);
    } catch (FileNotFoundException e) {
        throw new StorageException("retrieval error", e);
    }
    return new ArrayList<RecordedGrip>();
}
```

####   
**미확인(Unchecked) 예외를 사용하라**

-   미확인 예외란 실행단계에서 발생하는 예외 (개발자가 예상할 수 없음, 주로 RuntimeException 클래스 및 그 하위) 
-   확인된 예외는 연쇄적인 throw - throws -... 형태를 만들 수 있다. 이는 캡슐화를 깨고, 의존성을 높인다.

#### **예외에 의미를 제공하라**

-   오류 메세지에 실패한 연산 이름과 실패유형도 함께 언급한다.

#### **호출자를 고려해 예외 클래스를 정의하라**

-   오류의 유형보다 오류를 처리하는 방식에 집중하라 처리방식이 같다면 하나의 예외를 반환하도록 줄인다

```java
ACMEPort port = new ACMEPort(12);

// 오류처리의 방식이 비슷하다
try {
    port.open();
} catch (DeviceResponseException e) {
    reportPortError(e);
    logger.log("Device response exception",e);
} catch (ATM1212UnlockedException e) {
    reportPortError(e);
    logger.log("Unlock exception",e);
} ...
```

```java
LocalPort port = new LocalPort(12);

// 처리방식이 비슷하므로 하나의 예외만 반환하도록 리팩토링
try {
    port.open();
} catch (PortDeviceFailure e) {
    reportError(e);
    logger.log(e.getMessage(), e);
}
```

-   외부 API를 감싸는 것이 래퍼 (Wrapper) 클래스로 감싸는 것이 최선이다
    1.  외부 라이브러리와 프로그램 사이에서 의존성이 크게 줄어든다
    2.  테스트 코드를 넣어 테스트 하기 쉬워진다 
    3.  API 설계방식에 구애받지 않는다.  : 클래스 안에서 사용하기 편하게 정의하면 된다

```java
public class LocalPort {
    private ACMEPort innerPort;
    
    public LocalPort(int portNumber) {
        innerPort = new ACMEPort(portNumber);
    }
    
    public void open() {
        try {
            innerPort.open();
        } catch (DeviceResponseException e) {
            throw new PortDeviceFailure(e);
        }
        ...
    }
}
```

#### **정상 흐름을 정의하라**

-   예외 처리를 할 필요가 없다면 더 좋은 코드가 된다. 그 중 하나가 '**특수 사례 패턴**' 이다.

```java
try {
    MealExpenses expenses = expenseReportDAO.getMeals(employee.getID());
    m_toatl += expenses.getTotal();
} catch (MealExpensesNotFound e) {
    m_total += getMealPerDiem();
}
```

위 코드를 '**Special Case Pattern**' 을 사용해 리팩토링 해보자

```java
public class PerDiemMealExpenses implements MealExpenses {
    // 인터페이스에서 정의한 getTotal() 
    public int getTotal() {
        // 기본값으로 일일 기본 식비를 반환
    }
}
```

DAO 에서 항상 MealExpenses 객체를 반환하도록 수정하고, 특수한 상황에 맞는 구현 클래스를 정의

```
MealExpenses expenses = expenseReportDAO.getMeals(employee.getID());
m_total += expenses.getTotal();
```

훨씬 더 간결한 코드가 만들어진다.

#### **Null을 반환하지 마라**

-   null을 반환하는 코드는 일거리를 늘리고, 호출자에게 문제를 떠넘긴다. (null 확인을 시키기 때문)
-   null을 반환하는 대신, **예외**를 던지거나, **특수사례 객체**를 반환하는 방식을 사용한다.

```java
List<Employee> employees = getEmplyees();
if (employees != null) {
    for (Emplyee e : employees) {
        totalPay += e.getPay();
    }
}
```

null 반환 하니까 if 문으로 확인해야한다. 차라리 **빈 list를 반환**하도록 바꾼다면?

```java
List<Employee> employees = getEmplyees();
for (Emplyee e : employees) {
    totalPay += e.getPay();
}
```

#### **Null을 전달하지 마라**

-   null을 전달하는것은 null을 반환하는 방식보다 더 나쁘다.
-   **null 인수를 깔끔하게 처리하는 방법은 거의 없다.** 따라서 애초에 **null을 전달하지 마라**

#### **결론**

-   깨끗한 코드는 읽기 좋아야 하고, **안정성도 높아야**한다.
-   오류처리는 프로그램 논리와 분리해 독자적인 사안으로 고려해야 독립적 추론과 유지보수성을 크게 높일 수 있다
