# Chapter6. 객체와 자료구조
## 자료 추상화
```java
public class Point {
  public double x;
  public double y;
}
```
- 점이 직교좌표계를 사용함을 나타냄
- 개별적으로 좌표값을 읽고 설정하도록 강제함
- 변수를 private로 설정하더라도 get, set 함수를 제공한다면 구현을 외부로 노출하게 됨

```java
public interface Point {
  double getX();
  double getY();
  boid setCartesian(double x, double y);
  double getR();
  double getTheta();
  void setPolar(double r, double theta);
}
```
- 점이 직교좌표계를 사용하는지 극좌표계를 사용하는지 알 수 없음
- 클래스 메서드가 접근 정책을 강제함
- 좌표를 읽을 때는 각 값을 개별적으로 읽어야하지만, 좌표를 설정할 때는 두 값을 한번에 설정해야함
- **이 코드가 위의 코드보다 더 좋은 코드이다**

단지 변수 사이에 get, set 함수 계층을 넣어 다룬다고 클래스가 되는 것이 아니라 추상화를 통해 구현을 감추어야 한다.
>추상 인터페이스를 제공해 사용자가 구현을 모른 채 자료의 핵심을 조작할 수 있어야 진정한 의미의 클래스다.

## 자료/객체 비대칭
**객체**는 추상화 뒤로 자료를 숨긴 채 자료를 다루는 함수만 공개한다.<br/>
**자료구조**는 자료를 그대로 공개하며 별다른 함수를 제공하지 않는다.
>객체 - 자료구조 두 정의는 본질적으로 상반된다.

```java
public class Square {
  public Point topLeft;
  public double side;
}

public class Circle {
  public point center;
  public double radius;
}

public class Geometry {
  public final double PI = 3.141592653589793;
  
  public double area(Object shape) throws NoSuchShapeException
  {
    if (shape instanceof Square) {
      Square s = (Square)shape;
        return s.side * s.side;
    }
    else if (shape instanceof Circle) {
      Circle c = (Circle)shape;
      return PI * c.radius * c.radius;
    }
    throw new NoSuchShapeException();
  }
}
```
- 절차적인 코드
- Geometry 클래스에 둘레 길이를 구하는 perimeter() 함수를 추가하는 경우, 도형 클래스는 아무런 영향을 받지 않음
- 반대로 새 도형을 추가하는 경우, Geometry 클래스에 속한 함수를 모두 고쳐야 함

```java
public class Square implements Shape {
  private Point topLeft;
  private double side;
  
  public double area() {
    return side * side;
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

- 객체 지향 코드
- 절차적인 코드와 상호 보완적인 특질이 있음
- 이를 통해 객체와 자료구조는 근본적으로 양분됨을 알 수 있다.

>자료구조를 사용하는 절차적인 코드는 기존 자료구조를 변경하지 않으면서 새 클래스를 추가하기 쉽다. 반면 객체 지향 코드는 기존 함수를 변경하지 않으면서 새 클래스를 추가하기 쉽다.

>절차적인 코드는 모든 함수를 고쳐야 하기 때문에 새로운 자료구조를 추가하기 어렵다. 반대로 객체 지향 코드는 모든 클래스를 고쳐야 하기 때문에 새로운 함수를 추가하기 어렵다.

### *결과적으로 객체 지향 코드에서 어려운 변경은 절차적인 코드에서 쉬우며, 절차적인 코드에서 어려운 변경은 객체 지향 코드에서 쉽다.*
새로운 함수가 아니라 새로운 자료 타입이 필요한 경우에는 클래스와 객체 지향 기법이 가장 적합하다. 반면, 새로운 자료타입이 아니라 새로운 함수가 필요한 경우는 절차적인 코드와 자료 구조가 좀 더 적합하다.

## 디미터 법칙
잘 알려진 휴리스틱으로, 모듈은 자신이 조작하는 객체의 속사정을 몰라야 한다는 법칙
- "클래스 C의 메서드 f는 다음과 같은 객체의 메서드만 호출해야 한다"고 주장
  - 클래스 C
  - f가 생성한 객체
  - f 인수로 넘어온 객체
  - C 인스턴스 변수에 저장된 객체
- 하지만 위 객체에서 허용된 메서드가 반환하는 객체의 메서드는 호출하면 안됨.
```java
final String outputDir = ctxt.getOptions().getScratchDir().getAbsolutePath();
```
- 이 코드는 디미터 법칙을 어김
- getOptions() 함수가 반환하는 객체의 getScratchDir() 함수를 호출한 후 getScratchDir() 함수가 반환하는 객체의 getAbsolutePath() 함수를 호출하기 때문
- 흔히 위와 같은 코드를 **기차 충돌**이라 부름
- 일반적으로 조잡하다 여겨지는 방식이므로 피하는 편이 좋음

자료구조는 무조건 함수 없이 공개 변수만 포함하고 객체는 비공개 변수와 공개 함수를 포함한다면 문제는 간단해짐<br/>
하지만 단순한 자료 구조에도 get, set 함수를 정의하라 요구하는 프레임워크와 표준이 존재함(예, bean)<br/>
이러한 문제를 통해 절반은 객체, 절반은 자료 구조인 *잡종 구조*가 종종 나옴
- 잡종구조는 새로운 함수는 물론이고 새로운 자료 구조도 추가하기 어려움
- 따라서 잡종 구조는 되도록 피하는 편이 좋음

## 자료 전달 객체
자료 구조체의 전형적인 형태는 공개 변수만 있고 함수가 없는 클래스다. 이런 자료구조체를 때로는 자료 전달 객체, DTO(Data Transfer Object)라 한다.
- DTO는 데이터베이스와 통신하거나 소켓에서 받은 메시지의 구문을 분석할 때 유용
- 흔히 데이터베이스에 저장된 가공되지 않은 정보를 애플리케이션 코드에서 사용할 객체로 변환하는 일련의 단계에서 가장 처음ㅇ르ㅗ 사용하는 구조체
- 일반적인 형태는 bean 구조이다.
### 활성 레코드
DTO의 특수한 형태, 공개 변수가 있거나 비공개 변수에 set, get 함수가 있는 자료 구조지만, 대개 save, find와 같은 탐색 함수도 제공
- 데이터베이스 테이블이나 다른 소스에서 자료를 직접 볒환한 결과
- 활성 레코드에 비즈니스 규칙 메서드를 추가해 자료 구조를 객체로 취급하는 경우가 흔하지만, 바람직하지 않다.(잡종 구조이기 때문)
- 활성 레코드는 자료구조로 취급한다.

