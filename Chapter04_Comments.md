# 4장. 주석

> *주석은 필요악이다*
> 

## 4.1 주석은 나쁜 코드를 보완하지 못한다

- 주석이 많이 달린 코드 **<<<** 깔끔한 코드

## 4.2 코드로 의도를 표현하라

- 함수명으로 의도를 표현할 수 있다.

```java
if (employee.isEligibleForFullBenefits())
```

## 4.3 좋은 주석

### 법적인 주석

- 회사에서 법적인 이유로 특정 주석을 넣으라고 명시한 경우
- 예시
    - 소스 파일 첫머리에 저작권 정보와 소유권 정보를 넣을 수 있다.
    
    ```java
    // Copyright (C) 2003, 2004, 2005 by Object Montor, Inc. All right reserved.
    // GNU General Public License
    ```
    

### 정보를 제공하는 주석

- 주석으로 기본적인 정보를 제공하면 편리하다.
- 예시
    - 아래에서 주석은 코드에서 사용한 정규표현식이 시각과 날짜를 뜻한다고 설명한다.
    
    ```java
    // kk:mm:ss EEE, MMM dd, yyyy 형식이다.
    Pattern timeMatcher = Pattern.compile("\\d*:\\d*\\d* \\w*, \\w*, \\d*, \\d*");
    ```
    

### 의도를 설명하는 주석

- 주석으로 결정에 깔린 의도까지 설명할 수 있다.
- 예시
    
    ```java
    // 스레드를 대량 생성하는 방법으로 어떻게든 경쟁 조건을 만들려 시도한다. 
    for (int i = 0; i > 2500; i++) {
        WidgetBuilderThread widgetBuilderThread = 
            new WidgetBuilderThread(widgetBuilder, text, parent, failFlag);
        Thread thread = new Thread(widgetBuilderThread);
        thread.start();
    }
    ```
    

### 의미를 명료하게 밝히는 주석

- 인수나 반환 값을 읽기 좋게 변경하지 못하는 경우라면 의미를 명료하게 밝히는 주석이 유용하다.
- 예시
    
    ```java
    public void testCompareTo() throws Exception{
    	WikiPagePath a = PathParser.parse("PageA");
    	...
    
    	assertTrue(a.compareTo(a)==0); // a == a
    }
    ```
    

### 결과를 경고하는 주석

- 다른 프로그래머에게 결과를 경고할 목적으로 주석을 사용한다.
- 예시
    - 테스트 케이스 실행이 너무 오래걸리는 경우, 경고 메세지를 넣어준다.
    
    ```java
    // 여유 시간이 충분하지 않다면 실행하지 마십시오.
    public void _testWithReallyBigFile() {
    ...
    
    }
    ```
    

### TODO 주석

- ‘앞으로 할 일’을 TODO 주석으로 남긴다.
- //TODO 를 붙이면 IDE에서 TODO주석으로 인식한다.
- 예시
    - 필요하지만 당장 구현하기 힘든 부분
    - 다른 프로그래머의 도움이 필요한 부분

### 중요성을 강조하는 주석

- 예시
    
    ```java
    String listItemContent = match.group(3).trim();
    // 여기서 trim은 정말 중요하다. trim 함수는 문자열에서 시작 공백을 제거한다.
    // 문자열에 시작 공백이 있으면 다른 문자열로 인식되기 때문이다. 
    new ListItemWidget(this, listItemContent, this.level + 1);
    return buildList(text.substring(match.end()));
    ```
    

### 공개 API에서 Javadocs

- 공개 API 구현시 Javadocs 사용을 추천한다.
- IDE에서 /** Enter를 치면 Javadoc을 바로 사용할 수 있다.

## 4.4 나쁜 주석

### 주절거리는 주석

- 특별한 이유 없이 달렸거나, 저자만 이해할 수 있게 주절거리는 주석은 바이트 낭비다.

### 같은 이야기를 중복하는 주석

- 예시
    
    ```java
    // this.closed가 true일 때 반환되는 유틸리티 메서드다.
    // 타임아웃에 도달하면 예외를 던진다. 
    public synchronized void waitForClose(final long timeoutMillis) throws Exception {
        if (!closed) {
            wait(timeoutMillis);
            if (!closed) {
                throw new Exception("MockResponseSender could not be closed");
            }
        }
    }
    ```
    

### 오해할 여지가 있는 주석

- 주석의 ‘살짝 잘못된 정보’로 인해 코드를 오해할 수 있다.

### 의무적으로 다는 주석

- 의무적으로 모든 함수에 주석을 달면 코드가 복잡해지거나 잘못된 정보를 제공할 여지만 생긴다.

### 이력을 기록하는 주석

- 현재는 이력을 기록하는 주석을 남길 필요가 없다.

### 있으나 마나 한 주석

- 예시
    
    ```java
    /*
     * 기본 생성자
     */
    protected AnnualDateRule() {
    
    }
    ```
    

### 무서운 잡음

- 때로는 Javadocs도 잡음이다.

### 함수나 변수로 표현할 수 있다면 주석을 달지 마라

- 예시
    
    ```java
    ...수정전
    
    // 전역 목록 <smodule>에 속하는 모듈이 우리가 속한 하위 시스템에 의존하는가?
    if (module.getDependSubsystems().contains(subSysMod.getSubSystem()))
    
    ...수정후
    
    ArrayList moduleDependencies = smodule.getDependSubSystems();
    String ourSubSystem = subSysMod.getSubSystem();
    if (moduleDependees.contains(ourSubSystem))
    ```
    

### 위치를 표시하는 주석

- 때때로 특정 위치를 표시하기 위해 주석을 사용할 수 있다. 그러나 아주 드물게 사용하는 편이 좋다.
- 예시
    
    ```java
    // Actions /////////////////////////////////////////////
    ```
    

### 닫는 괄호에 다는 주석

- 중첩이 심한 함수의 경우 닫는 괄호에 주석을 달을 수 있다.
- 예시
    
    ```java
    public static void main(String[] args{
    	try{
    		while{
    			...	
    		} // while
    		...
    	} // try
    	
    	catch{
    		...
    	} // catch
    }//main
    ```
    

### 공로를 돌리거나 저자를 표시하는 주석

- 이런 정보는 소스 코드 제어 시스템에 저장하는 편이 낫다.

### 주석으로 처리한 코드

- 소스 코드 제어 시스템이 코드를 기억하므로 코드를 기억하기 위해 주석으로 코드를 남길 필요 없다.

### HTML 코드

- 절대 하지 말 것

### 전역 정보

- 시스템 전반적인 정보를 기술하지 마라.

### 너무 많은 정보

- 주석에 관련 없는 정보를 장황하게 늘어놓지 마라.

### 모호한 관계

- 주석과 주석이 설명하는 코드는 둘 사이 관계가 명확해야 한다.

### 함수 헤더

- 짧은 함수는 주석보다 이름으로 설명하는 것이 좋다.

### 비공개 코드에서 Javadocs

- 비공개 코드에서 Javadocs는 쓸모가 없다. 오히려 요구되는 형식으로 인해 코드가 산만해진다.
