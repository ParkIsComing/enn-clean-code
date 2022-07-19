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

##자료/객체 비대칭
**객체**는 추상화 뒤로 자료를 숨긴 채 자료를 다루는 함수만 공개한다.
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

>(자료구조를 사용하는) 절차적인 코드는 기존 자료구조를 변경하지 않으면서 새 클래스를 추가하기 쉽다.
>반면 객체 지향 코드는 기존 함수를 변경하지 않으면서 새 클래스를 추가하기 쉽다.
<br/>
>절차적인 코드는 모든 함수를 고쳐야 하기 때문에 새로운 자료구조를 추가하기 어렵다.
>객체 지향 코드는 모든 클래스를 고쳐야 하기 때문에 새로운 함수를 추가하기 어렵다.

### *결과적으로 객체 지향 코드에서 어려운 변경은 절차적인 코드에서 쉬우며, 절차적인 코드에서 어려운 변경은 객체 지향 코드에서 쉽다.*
새로운 함수가 아니라 새로운 자료 타입이 필요한 경우에는 클래스와 객체 지향 기법이 가장 적합하다. 반면, 새로운 자료타입이 아니라 새로운 함수가 필요한 경우는 절차적인 코드와 자료 구조가 좀 더 적합하다.

## 디미터 법칙
잘 알려진 휴리스틱으로, 모듈은 자신이 조작하는 객체의 속사정을 몰라야 한다는 법칙
