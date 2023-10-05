#### **창발적 설계로 깔끔한 코드를 구현하자**

-   지키기만 해도 깔끔한 설계를 가능하게하는 4가지 원칙 (중요도 순)

1.   모든 **테스트**를 실행한다
2.  **중복**을 없앤다
3.  개발자의 **의도를 표현**한다
4.  클래스와 메서드를 **최소**로 줄인다.

#### **모든 테스트를 실행하라**

-   테스트 코드를 작성하는 것만으로 코드의 품질이 향상된다.
-   테스트 하기 쉬운 코드를 작성하기 위한 노력이 코드의 응집도를 높이고, 결합도를 낮춘다.

#### **중복을 없애라**

-   중복을 없애기 위해 , 메서드를 분리하고, 클래스로 나눈다. 이 과정에서 더 좋은 코드가 탄생한다.

```java
int size() {//사이즈 구하기}
boolean isEmpty() {//비었는지 확인}
```

두가지 메서드를 각각 따로 구현 할 수 도 있지만

```java
int size() {//사이즈 구하기}
boolean isEmpty {
    return 0 == size();
}
```

size 메서드만 구현하고 이를 isEmpty에서 이용하는 방법도 있다. 코드를 중복하여 구현할 필요가 없다.

-   단 몇 줄의 중복이라도 제거하려는 의지를 가져야 한다.

```java
public void scaleToOneDimension(float desiredDimension, float imageDimension) {
    if (Math.abs(desiredDimension - imageDimension) < errorThreshold)
        return;
    float scalingFactor = desiredDimension / imageDimension;
    scalingFactor = (float)(Math.floor(scalingFactor * 100) * 0.01f);
    
    RenderedOp newImage = ImageUtilities.getScaledImage(image, scalingFactor);
    image.dispose();
    System.gc();
    image = newImage;
}

public synchronized void rotate(int degrees) {
    RenderedOp newImage = ImageUtilities.getRotatedImage(image, degrees);
    image.dispose();
    System.gc();
    image = newImage;
}
```

중복되는 몇줄의 코드를 분리한다. 이미지 크기변경, 이미지 회전과 별도로 이미지 재등록의 역할까지 하고 있음을 알 수 있다. 중복을 분리하니, 단일책임의 원칙이 위반되었음이 드러난다. 

```java
public void scaleToOneDimension(float desiredDimension, float imageDimension) {
    if (Math.abs(desiredDimension - imageDimension) < errorThreshold)
        return;
    float scalingFactor = desiredDimension / imageDimension;
    scalingFactor = (float)(Math.floor(scalingFactor * 100) * 0.01f);
    
    replaceImage(ImageUtilities.getScaledImage(image, scalingFactor));
}

public synchronized void rotate(int degrees) {
    replaceImage(ImageUtilities.getRotatedImage(image, degrees));
}

// 단일책임원칙 위반 - > 별도의 클래스로 분리해야 
private void replaceImage(TenderedOp newImage) {
    imgae.dispose();
    System.gc();
    image = newImage;
}
```

-   TEMPLATE METHOD 패턴의 활용

```java
public class VacationPolicy {
    public void accrueUSDivisionVacation() {
        // 지금까지 근무한 시간을 바탕으로 휴가일수 계산
        //...
        // 휴가 일수가 미국 최소 법정 일수를 만족하는지 계산
        //...
        // 휴가 일수를 급여 대장에 적용
    }
    
    public void accrueEUDivisionVacation() {
        // 지금까지 근무한 시간을 바탕으로 휴가일수 계산
        //...
        // 휴가 일수가 유럽 최소 법정 일수를 만족하는지 계산
        //...
        // 휴가 일수를 급여 대장에 적용
    }
}
```

Template Method 패턴을 적용하면 공통부분은 상위 클래스에서 규정하고, 중복되지 않는 정보만 하위클래스에서 구현한다.

```java
abstract public class VacationPolicy {
    public void accrueVacation() {
        calculateBaseVacationHours();   //기본 휴가계산
        alterForLegalminimums();        //지역별 최소일수 계산
        applyToPayaroll();              //급여대장에 기록
    }
    
    private calculateBaseVacationHours() { /*...*/};
    abstract protected alterForLegalMinimums();   // 지역마다 다르므로 추상메서드 
    private applyToPayroll() {/*...*/};
}

public USVacationPolicy extends VacationPolicy {
    @Override
    protected alterForLegalMinimuns() {
       //미국 최소 법정 일수를 사용
    }
}

public EUVacationPolicy extends VacationPolicy {
    @Override
    protected alterForLegalMinimuns() {
       //유럽 최소 법정 일수를 사용
    }
}
```

#### **표현하라**

-   개발자의 의도가 명확하게 표현될 수록 유지보수 비용이 줄어들고 결함이 줄어든다.
-   자신의 코드에 '주의'를 기울이는 '노력'이 필요하다.

1.   좋은 이름을 선택한다
2.  함수와 클래스 크기를 가능한 줄인다.
3.  표준명칭을 사용한다.  ex) 디자인 패턴명
4.  단위 테스트 케이스를 꼼꼼히 작성한다.

#### **클래스와 메서드 수를 최소로 줄여라**

-   함수와 클래스 크기를 작게 유지하면서 동시에 시스템의 크기도 작게 유지한다.
