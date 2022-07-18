# 10장. 클래스

## 클래스 체계

### 클래스 체계

- 클래스 내부의 구성 순서는 다음과 같다.
    1. 변수 목록
        1. static public
        2. static private
        3. private
        
        (public 변수가 필요한 경우는 거의 없다.)
        
    2. public 함수
    3. private 함수
        - 자신을 호출하는 public 함수 직후에 넣는다.

### 캡슐화

- 캡슐화 : 외부에서 쉽게 접근하지 못하도록 *정보를 은닉*하는 자바의 특성
- protected
    - 같은 패키지 안에서 테스트 코드가 함수나 변수를 사용하는 경우, 해당 함수나 변수를 protected로 선언하거나, 패키지 전체로 공개한다.
        
        (protected : 동일 패키지 내에 존재하거나 파생 클래스인 경우에만 접근 가능)
        
- 캡슐화를 풀어주는 결정은 언제나 최후의 수단이다. 최대한 비공개 상태를 유지해라.

## 클래스는 작아야 한다!

### 얼마나 작아야 하는가?

- *클래스가 맡은 책임*을 척도로 한다.
- 클래스의 이름을 지을 때 간결한 이름이 떠오르지 않는다면 클래스의 크기가 너무 큰 것이다.

### 단일 책임 원칙

- **SRP**(Single Responsibility Principle)
- 클래스나 모듈을 **변경할 이유**가 단 하나뿐이어야 한다는 원칙
    
    (’변경할 이유’가 바로 클래스의 **책임**이다**.**)
    

### 응집도

- 응집도가 높은 클래스란?
    - 변수가 메서드마다 사용될수록 응집도가 높다.
    - 예시
        
        ```java
        // Stack을 구현한 코드, 응집도가 높은 편이다.
        
        public class Stack {
        	private int topOfStack = 0;
        	List<Integer> elements = new LinkedList<Integer>();
        
        	public int size() { 
        		return topOfStack;
        	}
        
        	public void push(int element) { 
        		topOfStack++; 
        		elements.add(element);
        	}
        	
        	public int pop() throws PoppedWhenEmpty { 
        		if (topOfStack == 0)
        			throw new PoppedWhenEmpty();
        		int element = elements.get(--topOfStack); 
        		elements.remove(topOfStack);
        		return element;
        	}
        }
        ```
        
- 응집도를 유지하면 작은 클래스 여럿이 나온다
    1. 변수를 아주 많이 사용하는 큰 함수가 하나 있다.
    2. 큰 함수의 일부를 작은 함수 하나로 빼내고 싶은데, 빼내려는 코드가 큰 함수에 정의된 변수 넷을 사용한다.
    3. 그래서 네 변수를 작은 함수의 인스턴스 변수로 바꾸어주었다.
    4. 이렇게 하면 클래스가 응집력을 잃는다. 몇몇 함수만 사용하는 인스턴스 변수가 늘어나기 때문이다.
    5. 그러나, 이때 이 함수를 독자적인 클래스로 분리할 수 있다. 그러면 응집력도 잃지 않는다.
    
     → 따라서 응집도를 유지하면 작은 클래스가 여럿이 나온다.
    

## 변경하기 쉬운 클래스

### 변경으로부터 격리

- 코드는 변하기 마련이다. 요구사항이 늘 변하기 때문이다.
- 추상 클래스(abstract)
    - 인터페이스와 추상 클래스를 사용하면 구현이 미치는 영향을 격리할 수 있다.
        
        (추상 클래스 : 추상 메서드를 선언하여 자손 클래스가 상속하여 완성하도록 유도하는 클래스)
        
        (인터페이스 : 기본 설계도와 같은 역할. 꼭 구현되어야 하는 기능의 껍데기만 적어놓는다.)
        
- *결합도*를 낮추면 유연성과 재사용성이 높아진다.
    - 또한, 자연스럽게 DIP를 따르게 된다. (DIP는 클래스가 구현이 아니라 추상화에 의존해야 한다는 원칙이다.)
