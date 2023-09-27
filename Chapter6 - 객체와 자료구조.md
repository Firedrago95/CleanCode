### **들어가기 전에**

-   사용자가 내부 구현을 모른 채 핵심을 조작할 수 있어야 진정한 클래스다.  
    클래스는 외부에 구현을 드러내지 않는다.

```java
// 객체지향, 새로운 도형을 추가해도 함수에 아무런 영향이 없다.
// 새로운 함수를 추가하고 싶다면, 도형 클래스 전부를 고쳐야 한다.
public class Square implements Shape { 
  private Point topLeft;
  private double side;
  
  public double area() { 
    return side * side;
  } 
}

public class Rectangle implements Shape { 
  private Point topLeft;
  private double height;
  private double width;

  public double area() { 
    return height * width;
  } 
}

public class Circle implements Shape { 
  private Point center;
  private double radius;
  public final double PI = 3.141592653589793;

  public double area() {
    return PI * radius * radius;
  } 
}
```

### **자료 추상화**

-   구현을 감추기 위해서 추상인터페이스를 사용한다.

```java
// 구현을 외부에 드러내고 있다.
public class Point {
    public double x;
    public double y;
}
```

```java
// 인터페이스를 통해 구현을 철저하게 숨긴다. 
public interface Point {
    double getX();
    double getY();
    void setCartesian(double x, double y);
    double getR();
    double getTheta();
    void setPolar(double r, double theta);
}
```

-   자료를 세세하게 공개하기 보다 추상적인 개념으로 표현한다. 개발자는 객체가 포함하는 자료를 표현할   
    가장 좋은 방법을 심각하게 고민해야 한다.

```java
//구체적인 자료를 공개하는 코드
public interface Vehicle {
    double getFuelTankCapacityInfallons(); // 정확한 양, 멤버변수를 드러낸다.
    double getGallonsOfgasoline();
}

// 추상적인 개념으로 자료를 표현 
public interface Vehicle {
    double getPercentFuelremaining(); // 비율정보, 정보가 어디서 오는지 드러나지 않는다.
}
```

### **자료/객체 비대칭**

-   자료구조는 자료를 그대로 공개하며 별다른 함수는 제공하지 않는다.
-   새로운 자료타입이 아니라 새로운 함수가 필요한 경우에는 절차적인 코드와 자료구조가 더 적합하다.

```java
// 자료구조
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

// 새로운 함수를 추가해도 자료구조는 아무런 영향이 없다.
// 새로운 자료구조를 추가할때 모든 함수에 영향이 있다.
public class Geometry {
  public final double PI = 3.141592653589793;
  
  public double area(Object shape) throws NoSuchShapeException {
    if (shape instanceof Square) { 
      Square s = (Square)shape; 
      return s.side * s.side;
    } else if (shape instanceof Rectangle) { 
      Rectangle r = (Rectangle)shape; 
      return r.height * r.width;
    } else if (shape instanceof Circle) {
      Circle c = (Circle)shape;
      return PI * c.radius * c.radius; 
    }
    throw new NoSuchShapeException(); 
  }
}
```

-   객체는 추상화 뒤로 자료를 숨긴 채 자료를 다루는 함수만 공개한다.

```java
// 객체지향, 새로운 도형을 추가해도 함수에 아무런 영향이 없다.
// 새로운 함수를 추가하고 싶다면, 도형 클래스 전부를 고쳐야 한다.
public class Square implements Shape { 
  private Point topLeft;
  private double side;
  
  public double area() { 
    return side * side;
  } 
}

public class Rectangle implements Shape { 
  private Point topLeft;
  private double height;
  private double width;

  public double area() { 
    return height * width;
  } 
}

public class Circle implements Shape { 
  private Point center;
  private double radius;
  public final double PI = 3.141592653589793;

  public double area() {
    return PI * radius * radius;
  } 
}
```

### **디미터 법칙**

-   '구현은 숨겨야 한다' 는 대원칙에서 당연히 도출된다. (내 생각)
-   모듈은 자신이 사용하는 객체의 내부를 몰라야한다. 
-   클래스의 메서드는 다음과 같은 객체의 메서드만 호출해야 한다
    1.  자신이 속한 클래스
    2.  메서드 자신이 생성한 객체
    3.  메서드의 인수로 넘어온 객체
    4.  자기 클래스의 인스턴스 변수에 저장된 객체

```java
// '기차 충돌' 상황 디미터 법칙을 위반하는가?
final String outputDir = ctxt.getOptions().getScratchDir().getAbsolutePath();

// 이렇게 나누는 편이 더 좋다
Option opts = ctxt.getOption();
File scratchDir = opts.getScratchDir();
final String outputDir = scratchDir.getAbsolutePath();
```

(메서드 하나에 너무 많은 객체를 탐색하는데... 디미터 법칙 위반일까 아닐까? 우선 객체인지 자료구조인지 구분해보자)

**잡종구조**

-   위 예제는 get 함수를 사용하고 있다. get 함수는 자료구조, 객체 모두 사용될 수 있다. 혼란을 일으킨다.  
    (get 함수를 남발하면 안되는 이유)
-   기능을 수행하는 함수 + 공개변수나 공개 조회/설정 함수 둘다 가진 잡종 구조가 발생하지 않도록 해야한다

**구조체 감추기**

-   만약 ctxt 등이 객체라면? **객체에는 뭔가를 하라고 말해야지 내부를 드러내라고 하면 안된다.**  
    위 '기차 충돌'의 목적을 살펴보자 

```java
// 임시파일을 생성하기위해 '기차 충돌' 코드를 사용한 것이다.
String outFile = outputDir + "/" + className.replace('.', '/') + ".class"; 
FileOutputStream fout = new FileOutputStream(outFile); 
BufferedOutputStream bos = new BufferedOutputStream(fout);
```

-   임시파일을 생성하기위한 목적을 알았으므로 객체에게 직접 임시파일을 생성할것을 요청한다.

```java
// 객체에게 직접 '메시지'를 보낸다.
BufferedOutputStream bos = ctxt.createScratchFileStream(classFileName);
```

**객체에게 '메세지'를 보낸다. 내부의 정보를 달라고 하는 것이 아니라... 객체지향의 사실과 오해에서 도 중요하게 다루는 주제이다.**

### **자료 전달 객체**

-   자료 전달 구조체 DTO 가 있다. 데이터베이스와의 연결, 소켓에서 받은 메시지의 구문 분석등에 사용된다.
-   빈 (Bean) 구조도 있다. private 변수를 조회/ 설정 함수로 조작한다. 캡슐화의 이익을 제공하지는 않는다.

**활성 레코드**

-   DTO 의 특수한 형태이다. 공개변수가 있거나, 비공개 변수에 조회/설정 함수가 있는 자료구조지만  
    save나 find 같은 탐색 함수도 제공한다. 
-   여기에 **비지니스 규칙 메서드를 추가하지 않도록 주의**한다. 잡종구조가 만들어진다.

### **결론**

-   시스템을 구성할때 **새로운 자료타입을 추가**하는 유연성이 필요하다면 **객체**,  
    **새로운 동작을 추가**하는 유연성이 필요하면 **자료구조와 절차적 코드**가 더 적합하다.
-   우수한 개발자는 직면한 문제에 **최적인 해결책**을 선택해야한다.
