
### Intro

의도를 분명히 표현하는 함수를 어떻게 구현할 수 있을까?

어떤 속성을 부여해야 처음 읽는 사람이 프로그램의 내부를 직관적으로 파악할 수 있을까?

### 1. 단순화하기

- **작게 만들어라 (중첩 구조를 피하라)**
    - 블록과 들여쓰기
    대개 함수를 호출하는 부분인 if문/else문/while문 등에 들어가는 블록은 한 줄이어야 한다.
        
        추가적으로, 각 함수들의 들여쓰기 수준이 2단을 넘어서지 않고, 함수 명이 명확하다면 코드를 이해하기 훨씬 쉬워진다.
        
        ```java
        public static String renderPageWithSetupsAndTeardowns( PageData pageData, boolean isSuite) throws Exception {
        	boolean isTestPage = pageData.hasAttribute("Test");
        	if (isTestPage) {
        		WikiPage testPage = pageData.getWikiPage();
        		StringBuffer newPageContent = new StringBuffer();
        		includeSetupPages(testPage, newPageContent, isSuite);
        		newPageContent.append(pageData.getContent());
        		includeTeardownPages(testPage, newPageContent, isSuite);
        		pageData.setContent(newPageContent.toString());
        	}
        	return pageData.getHtml();
        }
        ```
        
        ```java
        public static String renderPageWithSetupsAndTeardowns( PageData pageData, boolean isSuite) throws Exception { 
           if (isTestPage(pageData)) 
           	includeSetupAndTeardownPages(pageData, isSuite); 
           return pageData.getHtml();
        }
        ```
        
- **한 가지만 해라**
    - 한 가지 작업만 잘 수행해야 한다.
    추상화 수준이 하나인 단계만 수행한다면 한 가지 작업만 하는 것.
    - 함수 내 섹션
        
        한 함수를 여러 섹션으로 나눌 수 있다면, 그 함수는 여러 작업을 하는  것이다.
        
    - 위에서 아래로 코드 읽기: 내려가기 규칙
    코드는 위에서 아래로 이야기처럼 읽혀야 좋다.
        
        함수 추상화 부분이 한 번에 한 단계씩 낮아지는 것(내려가기 규칙)이 가장 이상적이다.
        
    
    ```
    TO 설정 페이지와 해제 페이지를 포함하려면, 설정 페이지를 포함하고, 테스트 내용을 포함하고, 해제 페이지를 포함한다.
    	TO 설정 페이지를 포함하려면, 슈트이면 슈트 설정 페이지를 포함한 후 ....
    	TO 슈트 설정 페이지를 포함하려면, 부모 계층에서 .....
    	TO 부모 계층을 검색하려면, .....
    ```
    

### 2. Switch문

switch문, if/else 구문의 연속은 작게 만들기 어렵다.

```java
public Money calculatePay(Employee e) throws InvalidEmployeeType {
	switch (e.type) {
		case COMMISSIONED:
			return calculateCommissionedPay(e);
		case HOURLY:
			return calculateHourlyPay(e);
		case SALARIED:
			return calculateSalariedPay(e);
		default:
			throw new InvalidEmployeeType(e.type);
	}
}
```

위의 함수는

1. 함수가 길다
2. 한가지 작업만 수행하지 않는다.
3. 코드를 변경할 이유가 많기 때문에 SRP를 위반한다.
4. 새 직원 유형을 추가할 때 마다 코드 변경의 가능성이 있으므로 OCP를 위반한다.

라는 문제점들이 존재한다.

다형성을 이용하여 위의 문제점들을 해결한 함수가 아래이다.

switch문을 abstract factory에 숨겨 다형적 객체를 생성하는 코드 안에서만 switch를 사용하도록 한다.

```java
public abstract class Employee {
	public abstract boolean isPayday();
	public abstract Money calculatePay();
	public abstract void deliverPay(Money pay);
}
-----------------
public interface EmployeeFactory {
	public Employee makeEmployee(EmployeeRecord r) throws InvalidEmployeeType; 
}
-----------------
public class EmployeeFactoryImpl implements EmployeeFactory {
	public Employee makeEmployee(EmployeeRecord r) throws InvalidEmployeeType {
		switch (r.type) {
			case COMMISSIONED:
				return new CommissionedEmployee(r) ;
			case HOURLY:
				return new HourlyEmployee(r);
			case SALARIED:
				return new SalariedEmploye(r);
			default:
				throw new InvalidEmployeeType(r.type);
		} 
	}
}
```

### 3. 서술적인 함수 이름 사용하기

- 길고 서술적인 이름이 짧고 어려운 이름보다 좋다.
    
    ex) testaleHtml → SetupTeardownIncluder
    

                     

- 일관성 있는 서술적인 이름을 사용한다.
    
    ex) includeSetupAndTeardownPages, includeSetupPages,  includeSuiteSetupPage
    

### 4. 함수 인수

함수에서 이상적인 인수 개수는 0개(무항)이다.

인수는 코드 이해를 어렵게 하는 요소이므로 입력 인수의 최선은 0개이고, 차선은 1개뿐인 경우이다.

출력 인수(함수의 반환값이 아닌 입력 인수로 결과를 받는 경우)는 입력 인수보다 이해하기 어려우므로 웬만하면 쓰지 않는 것이 좋다.

- **단항 형식**
    1. 인수에 질문을 던지는 경우
        
        `boolean fileExists(“MyFile”);`
        
    2. 인수를 뭔가로 변환해 결과를 변환하는 경우
        
        `InputStream fileOpen(“MyFile”);`
        
    3. 코드에 명확하게 이벤트 함수임이 드러나는 경우
    
    위의 3가지가 아니라면 단항 함수는 가급적 피         다.
    
- **플래그 인수**
    
    플래그 인수는 사용하지 마라. bool 값을 넘기는 것 자체가 그 함수는 한꺼번에 여러가지 일을 처리한다고 공표하는 것과 마찬가지다.
    
- **이항 함수**
    
    단항 함수보다 이해하기가 어렵다.
    
    2개의 인수의 자연적인 순서가 있어야 하는 Point 클래스처럼 이항 함수가 적절한 경우도 있다.
    
    `Point p = new Point(x,y);` 
    
    이처럼 무조건 나쁜 것은 아니지만, 인수가 1개일 때보다 이해가 어렵고 위험이 따르므로 가능하면 단항으로 바꾸도록 한다.
    
- **삼항 함수**
    
    이항 함수보다 이해하기 훨씬 어렵고 위험도 2배 이상 늘어나므로 신중히 고려하라.
    
- **인수 객체**
    
    인수가 2개 이상 필요할 경우, 일부 인수를 독자적인 클래스 변수로 선언할 가능성을 살펴보자.
    
    `Circle makeCircle(double x, double y, double radius);`
    
    `Circle makeCircle(Point center, double radius);`
    
    x,y를 인자로 넘기는 것보다 Point를 넘기는 것이 더 낫다.                  
    
- **인수 목록**
    
    때로는 인수 개수가 가변적인 함수도 필요하다.
    
     `String.format("%s worked %.2f hours.", name, hours);`
    
    이 함수의 가변 인수 전부를 동등하게 취급했을 때 List형 인수 하나로 취급할 수 있기 때문에 String.format 함수는 사실상 이항 함수라고 할 수 있다.
    
- **동사와 키워드**
    - 단항 함수는 함수와 인수가 동사/명사 쌍을 이뤄야 한다.
        
        `writeField(name);`
        
    - 함수 이름에 키워드(인수 이름)을 추가하면 인수 순서를 기억할 필요가 없어진다.
        
        `assertExpectedEqualsActual(expected, actual);`
        

### 5. 부수 효과 일으키지 말기

부수 효과는 함수에서 한 가지를 하겠다고 약속하고는 남몰래 다른 짓을 하는 것이다.

한 함수에서는 딱 한 가지만 수행할 것!

```java
public class UserValidator {
	private Cryptographer cryptographer;
	public boolean checkPassword(String userName, String password) { 
		User user = UserGateway.findByName(userName);
		if (user != User.NULL) {
			String codedPhrase = user.getPhraseEncodedByPassword(); 
			String phrase = cryptographer.decrypt(codedPhrase, password); 
			if ("Valid Password".equals(phrase)) {
				Session.initialize();
				return true; 
			}
		}
		return false; 
	}
}
```

`Session.initialize();`는 함수 이름과 맞지 않는 부수 효과이다.

- 출력 인수
    
    출력 인수는 피해야 한다.
    
    우리는 인수를 일반적으로 입력으로 해석하기 때문에 거슬린다.
    
    함수에서 상태를 변경해야 한다면 함수가 속한 객체 상태를 변경하는 방식을 택하라.
    

### 6. 명령과 조회 분리하기

함수는 객체 상태를 변경하거나, 객체 정보를 반환하거나 둘 중 하나만 해야 한다. 그러지 않을 경우 혼란을 초래한다.

`public boolean set(String attribute, String value);`

위 같은 경우 속성 값 설정 성공 시 true, 실패시 false를 반환하므로 다음과 같은 괴상한 코드가 작성된다.

`if (set("username", "unclebob")) ...`

함수의 개발자는 set을 동사로 의도했지만 if문에 넣고 보면 형용사로 느껴진다.

그러므로 명령과 조회를 분리해 혼란을 주지 않도록 한다.

### 7. 오류 코드보다 예외 사용하기

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
	logger.log("delete failed"); 
	return E_ERROR;
}
```

try/catch를 사용하면 오류 처리 코드가 원래 코드에서 분리되므로 코드가 깔끔해 진다.

```java
try {
	deletePage(page);
	registry.deleteReference(page.name);
	configKeys.deleteKey(page.name.makeKey());
}
catch (Exception e) {
	logger.log(e.getMessage());
}
```

- try/catch 블록 뽑아내기
    
    try/catch는 정상 작동과 오류 처리 동작을 뒤섞는 추한 구조이므로 if/else 블록과 마찬가지로 별도 함수로 뽑아내는 편이 좋다.
    

```java
//예외 처리 동작하는 부분
public void delete(Page page) {
	try {
		deletePageAndAllReferences(page);
  	} catch (Exception e) {
  		logError(e);
  	}
}

//실제로 페이지 제거하는 함수. 정상 동작하는 부분
private void deletePageAndAllReferences(Page page) throws Exception {
	deletePage(page);
	registry.deleteReference(page.name);
	configKeys.deleteKey(page.name.makeKey());
}

private void logError(Exception e) {
	logger.log(e.getMessage());
}
```

- 오류 처리도 ‘한 가지’ 작업이다.

- Error.java 의존성 자석
    
    오류 코드를 반환한다는 것은, 어디에선가는 오류 코드를 정의한다는 뜻이다.
    
    ```java
    public enum Error {
    	OK,
    	INVALID,
    	NO_SUCH,
    	LOCKED,
    	OUT_OF_RESOURCES,
    	WAITING_FOR_EVENT;
    }
    ```
    
    이런 클래스는 의존성 자석이므로(다른 클래스에서 import해 사용해야 하므로), 변경될 때의 코스트가 크다. 
    
    그러므로 오류 코드 대신 예외를 사용하는 것이 더 안전하다.
    

### 8. 반복 금지

중복은 모든 소프트웨어에서 모든 악의 근원이므로 늘 중복을 제거하려는 지속적인 노력이 필요하다.

### 9. 구조적 프로그래밍

다익스트라의 구조적 프로그래밍의 원칙에 따르면, 모든 함수와 함수 내 모든 블록에는 입구와 출구가 하나만 존재해야 한다. 

즉, 함수는 return문이 하나여야 되며, 루프 안에서 break나 continue를 사용해선 안되고, **goto는 절대로 사용하면 안된다.** 

그러나 구조적 프로그래밍은 함수가 클 경우에만 상당 이익을 제공하므로, 함수를 작게 만든다면 오히려 return, break, continue를 여러 차례 사용하는 것이 함수의 의도를 표현하기 쉬워진다. 반면, goto문은 큰 함수에서만 의미가 있으므로 작은 함수에서는 피해야 한다.
