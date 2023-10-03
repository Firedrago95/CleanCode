#### **클래스 체계**

-   클래스는 상수, 변수, 함수로 구성되어있다.
-   변수는 정적(static) -> 공개(public) -> 비공개(private) 순서로 나온다.
-   비공개 함수는 자신을 호출하는 공개함수 직후에 넣는다.

#### \- 캡슐화

-   반드시 private 만 사용해야 하는 것은 아니다. 테스트를 위해 protect 사용하는 경우도 있다.
-   하지만 캡슐화를 풀어주는 것은 언제나 최후의 수단이다. 

#### **클래스는 작아야 한다!**

-   함수의 최소화 기준은 '추상화 단계' **클래스**의 최소화 기준은 '**책임**

```java
// 너무 많은 책임을 가진 클래스
public class SuperDashboard extends JFrame implements MetaDataUser {
    public String getCustomizerLanguagePath()
    public void setSystemConfigPath(String systemConfigPath) 
    public String getSystemConfigDocument()
    public void setSystemConfigDocument(String systemConfigDocument) 
    public boolean getGuruState()
    public boolean getNoviceState()
    public boolean getOpenSourceState()
    public void showObject(MetaObject object) 
    public void showProgress(String s)
    public boolean isMetadataDirty()
    public void setIsMetadataDirty(boolean isMetadataDirty)
    public Component getLastFocusedComponent()
    public void setLastFocused(Component lastFocused)
    public void setMouseSelectState(boolean isMouseSelected) 
    public boolean isMouseSelected()
    public LanguageManager getLanguageManager()
    public Project getProject()
    public Project getFirstProject()
    public Project getLastProject()
    public String getNewProjectName()
    ...
}
```

메서드 갯수를 줄여보자

```java
public class SuperDashboard extends JFrame implements MetaDataUser {
    public Component getLastFocusedComponent()
    public void setLastFocused(Component lastFocused)
    public int getMagorVersionNumber()
    public int getMinorVersionNumber()
    public int getBuildNumber()
}
```

5개의 메서드로 줄였지만, 여전히 책임이 너무 크다. 

-   클래스의 이름이 간결하지 못하다면 (if, and, but 등이 사용되고, 25자가 넘어간다면) 클래스의 책임이 과중한것

#### \-  단일 책임 원칙 (Single responsibility Principle)

-   클래스의 **책임**, 즉 **변경할 이유가 하나**여야 한다.

```java
public class Version {
    public int getMajorVersionNumber()
    public int getMinorVersionNumber()
    public int getBuildNumber()
}
```

위 Super Dashboard 클래스는 변경 이유가 두가지 ( 버전변경 , JFrame 버전 변경) 그래서 Version 클래스로 분리하여 하나의 책임만 부여

-   작은 클래스 여러개가 큰 클래스 몇개보다 좋다.   
    1\. 코드의 양은 크게 차이가 없다. 게다가 작을수록 변경에 효과적으로 대처할 수 있다.  
    2\. 돌아가는 코드 작성과 깨끗한 코드 작성은 별개의 작업이다. 깨끗한 코드 작성을 위한 추가적인 노력이 필요하다. 

#### \-  응집도

-   클래스의 **응집도**가 높아야한다. 응집도가 높아지도록 **클래스를 쪼갤** 수록 단일책임을 가지게 된다.
-   응집도는 **메서드와 변수가 서로 의존**하며 **논리적인 단위**로 묶인다는 의미이다.  
    여러 함수가 인스턴스 변수를 많이 사용할 수록 응집도가 높아진다.

```java
public class Stack {
    private int topOfStack = 0;
    List<Integer> elemets = new LinkedList<integer>();
    
    public int size() {
        return topOfStack;
    }
    
    public void push(int element) {
        topOfStack++;
        elemets.add(element);
    }
    
    public itn pop() throws PoppedWhenEmpty {
        if (topOfStack == 0) {
            throw new PoppedWhenEmpty();
        }
        int element = elements.get(--topOfStack);
        elements.remove(topOfStack);
        return element;
    }
}
```

모든 메서드에서 인스턴스 변수를 사용하고 있다. 응집도가 높은 바람직한 클래스다.

#### \- 응집도를 유지하면 작은 클래스 여럿이 나온다.

-   함수를 작게, 매개변수를 짧게 만들다보면 인스턴스변수가 늘어나고 응집도가 낮아진다.  
    클래스를 작게 쪼개야 하는 신호다.  (**응집력을 잃는다면 쪼개라!**)

#### **변경하기 쉬운 클래스**

-   새 기능을 수정하거나 기존 기능을 변경할때 건드릴 코드가 최소인 시스템 구조가 바람직하다.   
    이상적인 시스템이라면 새 기능을 추가할 때 시스템을 확장할 뿐 기존코드를 변경하지 않는다.

#### \- 변경으로부터 격리

-   구체적인 구현클래스에 의존하는 클래스는 테스트가 힘들다. 
-   인터페이스와 추상 클래스를 사용해 구현이 미치는 영향을 격리하면 테스트가 편리해진다.
