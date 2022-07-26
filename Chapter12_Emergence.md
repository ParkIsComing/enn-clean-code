# 12장. 

*창발 = 나타남, 떠오름*

## 창발적 설계로 깔끔한 코드를 구현하자

- 단순한 설계 규칙 네 가지로 우수한 설계의 창발성을 촉진할 수 있다.(=나타나게 할 수 있다.)
    1. 모든 테스트를 실행한다.
    2. 중복을 없앤다.
    3. 프로그래머 의도를 표현한다.
    4. 클래스와 메서드 수를 최소로 줄인다.

## 단순한 설계 규칙 1 : 모든 테스트를 실행하라

- 테스트가 가능한 시스템이어야 한다.
    - 당연하지만 중요한 말이다.
    - 테스트가 가능한 시스템을 만들려고 애쓰면 설계 품질이 높아진다.
- 철저한 테스트가 가능한 시스템을 만들면 더 나은 설계가 얻어진다.
    1. 크기가 작고 목적 하나만 수행해야함 → SRP(단일 책임 원칙) 준수
    2. 결합도가 낮아야함 → DIP(의존 관계 역전 원칙) 준수

## 단순한 설계 규칙 2~4 : 리팩터링

- 테스트 케이스를 모두 작성했다면 단순한 설계 규칙 2~4를 사용하여 리팩터링을 해도 괜찮다.
- 테스트 케이스가 있으므로, 코드를 정리하면서 시스템이 깨질까 걱정할 필요가 없다.

## 중복을 없애라

- 중복은 추가 작업, 추가 위험, 불필요한 복잡도를 뜻하므로 제거해 줘야 한다.
- 예시1
    - 원래코드
        
        ```java
        int size() {}
        boolean isEmpty{}
        ```
        
    - 수정코드
        - size 메서드와 boolean 메서드의 구현 내용이 비슷하므로 size 메서드를 이용해 boolean 메서드를 구현한다.
        
        ```java
        boolean isEmpty() {
          return 0 == size();
        }
        ```
        
- 예시2 - 중복 코드를 함수로 빼기
    - 원래코드
        
        ```java
        public void scaleToOneDimension(float desiredDimension, float imageDimension) {
          if (Math.abs(desiredDimension - imageDimension) < errorThreshold)
            return;
          float scalingFactor = desiredDimension / imageDimension;
          scalingFactor = (float)(Math.floor(scalingFactor * 100) * 0.01f);
        
          RenderedOp newImage = ImageUtilities.getScaledImage(image, scalingFactor, scalingFactor);
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
        
    - 수정코드
        - scaleToOneDimension 메서드와 rotate 메서드의 일부코드가 동일하므로 중복을 제거한다.
        
        ```java
        public void scaleToOneDimension(float desiredDimension, float imageDimension) {
          if (Math.abs(desiredDimension - imageDimension) < errorThreshold)
            return;
          float scalingFactor = desiredDimension / imageDimension;
          scalingFactor = (float) Math.floor(scalingFactor * 10) * 0.01f);
          replaceImage(ImageUtilities.getScaledImage(image, scalingFactor, scalingFactor));
        }
        
        public synchronized void rotate(int degrees) {
          replaceImage(ImageUtilities.getRotatedImage(image, degrees));
        }
        
        private void replaceImage(RenderedOpnewImage) {
          image.dispose();
          System.gc();
          image = newImage;
        }
        ```
        
- 예시3 - TEMPLATE METHOD 패턴으로 고차원 중복 제거하기
    - 원래코드
        
        ```java
        public class VacationPolicy {
          public void accrueUSDDivisionVacation() {
            // 지금까지 근무한 시간을 바탕으로 휴가 일수를 계산하는 코드
            // ...
            // 휴가 일수가 **미국 최소** 법정 일수를 만족하는지 확인하는 코드
            // ...
            // 휴가 일수를 급여 대장에 적용하는 코드
            // ...
          }
        
          public void accrueEUDivisionVacation() {
            // 지금까지 근무한 시간을 바탕으로 휴가 일수를 계산하는 코드
            // ...
            // 휴가 일수가 **유럽연합** 최소 법정 일수를 만족하는지 확인하는 코드
            // ...
            // 휴가 일수를 급여 대장에 적용하는 코드
            // ...
          }
        }
        ```
        
    - 수정코드
        - 최소 법정 일수를 계산하는 알고리즘은 직원 유형에 따라 살짝 변한다. 그 외에는 두 메서드가 거의 동일하다.
        - template method 패턴을 적용해 중복을 제거한다.
            - template method 패턴이란?
                
                일부분을 서브 클래스로 캡슐화해 전체 구조는 바꾸지 않으면서 특정 단계의 수행 내용을 바꾸는 패턴
                
        
        ```java
        abstract public class VacationPolicy {
          public void accrueVacation() {
            caculateBseVacationHours();
            alterForLegalMinimums();
            applyToPayroll();
          }
        
          private void calculateBaseVacationHours() { /* ... */ };
          abstract protected void alterForLegalMinimums();
          private void applyToPayroll() { /* ... */ };
        }
        
        public class USVacationPolicy extends VacationPolicy {
          @Override protected void alterForLegalMinimums() {
            // 미국 최소 법정 일수를 사용한다.
          }
        }
        
        public class EUVacationPolicy extends VacationPolicy {
          @Override protected void alterForLegalMinimums() {
            // 유럽연합 최소 법정 일수를 사용한다.
          }
        }
        ```
        

## 표현하라

- 다른 개발자가 코드를 오해하지 않도록, 개발자의 의도를 분명히 표현해야 한다.
- 개발자의 의도를 표현하는 방법
    1. 좋은 이름을 선택한다.
    2. 함수와 클래스 크기를 가능한 줄인다.
    3. 표준 명칭을 사용한다.
        - 예시
            - 클래스가 COMMAND나 VISITOR와 같은 표준 패턴을 사용해 구현된다면 클래스 이름에 패턴 이름을 넣어준다.
    4. 단위 테스트 케이스를 꼼꼼히 작성한다.
        - 잘 만든 테스트 케이스를 읽어보면 클래스 기능이 한눈에 들어온다.

## 클래스와 메서드 수를 최소로 줄여라

- 함수와 클래스의 크기를 최소한으로 줄이되, 함수와 클래스 **수**를 *가능한* 줄여라
    - 즉, 크기를 줄이자고 조그만 함수와 클래스를 수없이 만들어서는 안된다
- 이 규칙은 간단한 설계 규칙 네 개 중 우선순위가 가장 낮다.
