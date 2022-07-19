### Intro

1997년만 해도 TDD(Test Driven Development)라는 개념을 아무도 몰랐다. 대다수에게 단위 테스트란 자기 프로그램이 '돌아간다'는 사실만 확인하는 일회성 코드에 불과했다.

지금은 애자일과 TDD 덕택에 단위 테스트를 자동화하는 프로그래머들이 이미 많아졌으며 점점 더 늘어나는 추세다. 하지만 제대로 된 테스트 케이스를 작성하기 위해서는 고려해야 할 점이 많다.

### 1. TDD 법칙 세 가지

TDD는 실제 코드를 짜기 전에 단위 테스트부터 짜라고 요구한다.

1. 실패하는 단위 테스트를 작성할 때까지 실제 코드를 작성하지 않는다.
2. 컴파일은 실패하지 않으면서 실행이 실패하는 정도로만 단위 테스트를 작성한다.
3. 현재 실패하는 테스트를 통과할 정도로만 실제 코드를 작성한다.

위 세 가지 규칙을 따르면 개발과 테스트가 대략 30초 주기로 묶인다. 테스트 코드와 실제 코드가 함께 나올뿐더러 테스트 코드가 실제 코드보다 불과 몇 초 전에 나온다.

이렇게 하면 매일 수십 개, 매달 수백 개, 매년 수천 개에 달하는 테스트 케이스, 실제 코드를 사실상 전부 테스트하는 테스트 케이스가 나온다. 

실제 코드와 맞먹을 정도로 방대한 테스트 코드는 심각한 관리 문제를 유발하기도 한다.

### 2. 깨끗한 테스트 코드 유지하기

테스트 코드에 실제 코드와 동일한 품질 기준을 적용하지 않는다면,

즉, 테스트 코드를 **지저분해도 빨리**(변수 이름은 신경 쓰지 않고, 간결하거나 서술적이지 않고, 잘 설계하거나 주의해서 분리하지 않고) 그저 돌아만 가도록 한다면

테스트 코드를 아예 만들지 않는 것보다 못하다.

이유는 실제 코드가 진화하면 테스트 코드도 변해야 한다는 데 있다.

테스트 코드는 지저분할수록 변경하기 어려워진다. 실제 코드를 짜는 시간보다 테스트 케이스를 추가하는 시간이 더 걸리기 십상이다. 

실제 코드를 변경해 기존의 테스트 케이스가 실패하기 시작하면, 지저분한 코드로 인해 점점 더 통과시키기 어려워진다. 새 버전을 출시할 때마다 팀이 테스트 케이스를 유지하고 보수하는 비용도 늘어나고 결국 테스트 슈트를 폐기하지 않으면 안 되는 상황에 처한다.

하지만 테스트 슈트가 없으면 개발자는 자신이 수정한 코드가 제대로 도는지 확인할 방법이 없다. 결함율이 높아지기 시작한다. 의도하지 않은 결함 수가 많아지면 개발자는 변경을 주저한다. 변경하면 득보다 해가 크다 생각해 더 이상 코드를 정리하지 않는다. 그러면서 코드가 망가지기 시작한다. 

따라서 **테스트 코드는 실제 코드 못지 않게 중요하다.** 테스트 코드는 사고와 설계와 주의가 필요하며 실제 코드 못지 않게 깨끗하게 짜야 한다.

- **테스트는 유연성, 유지보수성, 재사용성을 제공한다**
    
    테스트 코드가 지저분하면 실제 코드도 지저분해진다. 코드를 변경하는 능력이 떨어지며 코드 구조를 개선하는 능력도 떨어진다. 결국 테스트 코드를 잃어버리고 실제 코드도 망가진다.
    
    테스트 코드가 없으면 실제 코드를 유연하게 만드는 버팀목도 사라진다. 이 때, 코드에 유연성, 유지보수성, 재사용성을 제공하는 버팀목이 바로 **단위 테스트**다. 
    
    이유는 단순하다. **테스트 케이스가 있으면 변경이 쉬워지기 때문이다.**
    
    테스트 케이스가 없다면 모든 변경이 잠정적인 버그다. 아키텍쳐가 아무리 유연하고 잘 나눴더라도, 테스트 케이스가 없으면 개발자는 버그가 두려워 변경을 주저한다.
    
    하지만 테스트 케이스가 있다면 공포는 사실상 사라지며 테스트 커버리지가 높을수록 공포는 줄어든다. 아키텍처가 부실한 코드나 설계가 모호하고 엉망인 코드라도 별다른 우려 없이, 오히려 안심하고 아키텍처와 설계를 개선할 수 있다.
    
- 깨끗한 테스트 코드
    
    깨끗한 테스트 코드를 만들려면 세 가지가 필요하다.
    
    **가독성, 가독성, 가독성.**
    
    어쩌면 가독성은 실제 코드보다 테스트 코드에 더더욱 중요하다. 테스트 코드는 최소의 표현으로 많은 것을 나타내야 한다.
    
    ```java
    public void testGetPageHierarchyAsXml() throws Exception {
      makePages("PageOne", "PageOne.ChildOne", "PageTwo");
    
      submitRequest("root", "type:pages");
    
      assertResponseIsXML();
      assertResponseContains(
        "<name>PageOne</name>", "<name>PageTwo</name>", "<name>ChildOne</name>");
    }
    
    public void testSymbolicLinksAreNotInXmlPageHierarchy() throws Exception {
      WikiPage page = makePage("PageOne");
      makePages("PageOne.ChildOne", "PageTwo");
    
      addLinkTo(page, "PageTwo", "SymPage");
    
      submitRequest("root", "type:pages");
    
      assertResponseIsXML();
      assertResponseContains(
        "<name>PageOne</name>", "<name>PageTwo</name>", "<name>ChildOne</name>");
      assertResponseDoesNotContain("SymPage");
    }
    
    public void testGetDataAsXml() throws Exception {
      makePageWithContent("TestPageOne", "test page");
    
      submitRequest("TestPageOne", "type:data");
    
      assertResponseIsXML();
      assertResponseContains("test page", "<Test");
    }
    ```
    
    위의 테스트는 **BUILD-OPERATE-CHECK 패턴으로** 명확히 세 부분으로 나눠진다. 첫 부분은 테스트 자료를 만든다. 두 번째 부분은 테스트 자료를 조작하며, 세 번째 부분은 조작한 결과가 올바른지 확인한다.
    
    위 코드가 잡다하고 세세한 코드를 거의 다 없앴다는 사실에 주목한다. 본론에 돌입해 진짜 필요한 자료 유형과 함수만 사용한다. 그러므로 코드를 읽는 사람은 헷갈릴 필요 없이 코드가 수행하는 기능을 재빨리 이해할 수 있다.
    
- 도메인에 특화된 테스트 언어
    
    위 예시는 도메인에 특화된 언어DSL로 테스트 코드를 구현하는 기법을 보여준다. 
    
    흔히 쓰는 시스템 조작 API를 사용하는 대신 API 위에 함수와 유틸리티를 구현한 후 그 함수와 유틸리티를 사용하므로 테스트 코드를 짜기도 읽기도 쉬워진다. 
    
    이렇게 구현한 유틸리티는 테스트 코드에서 사용하는 특수 API가 된다. 
    
    즉, 테스트를 구현하는 당사자와 나중에 테스트를 읽어볼 독자를 도와주는 **테스트 언어**이다.
    
    이런 테스트 API는 처음부터 설계된 API가 아니다. 잡다하고 세세한 사항으로 범벅된 코드를 계속 리팩터링하다가 진화된 API다.
    
    이처럼 숙련된 개발자라면 자기 코드를 좀 더 간결하고 표현력이 풍부한 코드로 리팩터링해야 마땅하다.
    
- 이중 표준
    
    **테스트 API코드에 적용하는 표준은 실제 코드에 적용하는 표준과 확실히 다르다.**
    단순하고, 간결하고, 표현력이 풍부해야 하지만, 실제 코드만큼 효율적일 필요는 없다. 실제 환경이 아니라 테스트 환경에서 돌아가는 코드이기 때문인데, 실제 환경과 테스트 환경은 요구사항이 판이하게 다르다.
    
    ```java
    @Test
    public void turnOnLoTempAlarmAtThreashold() throws Exception {
      hw.setTemp(WAY_TOO_COLD); 
      controller.tic(); 
      assertTrue(hw.heaterState());   
      assertTrue(hw.blowerState()); 
      assertFalse(hw.coolerState()); 
      assertFalse(hw.hiTempAlarm());       
      assertTrue(hw.loTempAlarm());
    }
    ```
    
    온도가 '급격하게 떨어지면' 경보, 온풍기, 송풍기가 모두 가동되는지 확인하는 코드이다.
    
    위 코드는 세세하게 읽어보아야 할 사항이 많다.
    
    tic함수가 무엇인지, 상태 이름과 상태 값을 하나하나 확인하느라 눈길이 이리저리 흩어진다. heaterState라는 상태 이름을 확인하고 왼쪽으로 눈길을 돌려 assertTrue를 읽는다. 이런 식으로 모든 state를 확인해야 하는 것은 번거롭다.
    
    ```java
    @Test
    public void turnOnCoolerAndBlowerIfTooHot() throws Exception {
      tooHot();
      assertEquals("hBChl", hw.getState()); 
    }
      
    @Test
    public void turnOnHeaterAndBlowerIfTooCold() throws Exception {
      tooCold();
      assertEquals("HBchl", hw.getState()); 
    }
    
    @Test
    public void turnOnHiTempAlarmAtThreshold() throws Exception {
      wayTooHot();
      assertEquals("hBCHl", hw.getState()); 
    }
    
    @Test
    public void turnOnLoTempAlarmAtThreshold() throws Exception {
      wayTooCold();
      assertEquals("HBchL", hw.getState()); 
    }
    ```
    
    → tic 함수는 wayTooCold라는 함수를 만들어 숨겼다. 그런데 assertEquals에 들어있는 이상한 문자열에 주목한다. 대문자는 '켜짐'이고 소문자는 '꺼짐'을 뜻한다. 문자는 항상 '{heater, blower, cooler, hi-temp-alarm, lo-temp-alarm}' 순서다.
    
    비롯 위 방식이 그릇된 정보를 피하라는 규칙의 위반에 가깝지만 여기서는 적절해 보인다. 일단 의미만 안다면 눈길이 문자열을 따라 움직이며 결과를 재빨리 판단할 수 있다.
    
    ```java
    public String getState() {
      String state = "";
      state += heater ? "H" : "h"; 
      state += blower ? "B" : "b"; 
      state += cooler ? "C" : "c"; 
      state += hiTempAlarm ? "H" : "h"; 
      state += loTempAlarm ? "L" : "l"; 
      return state;
    }
    ```
    
    위의 코드가 그리 효율적이지 못하다는 사실에 주목한다. 효율을 높이려면 StringBuffer가 더 적합하다. 그러나 StringBuffer는 보기에 흉하다. 실제 코드에서도 크게 무리가 아니라면 이를 피한다. 
    
    **실제 환경에서는 문제가 될 수 있지만 테스트 환경은 자원이 제한적일 가능성이 낮기 때문에 StringBuffer를 안 써서 치르는 대가가 미미하다.** 
    
    이것이 이중 표준의 본질이다. 실제 환경에서는 절대로 안 되지만 테스트 환경에서는 전혀 문제없는 방식이 있다. (대개 메모리나 CPU 효율과 관련 있는 경우)
    

### 3. 테스트 당 assert 하나

JUnit으로 테스트 코드를 짤 때 함수마다 assert를 단 하나만 사용해야 한다고 주장하는 학파가 있다. 가혹하다 여길지 모르지만 확실히 장점이 있다. 

assert가 하나라면 결론이 하나기 때문에 코드를 이해하기 빠르고 쉽다.

```java
public void testGetPageHierarchyAsXml() throws Exception { 
  givenPages("PageOne", "PageOne.ChildOne", "PageTwo");
  
  whenRequestIsIssued("root", "type:pages");
  
  thenResponseShouldBeXML(); 
}

public void testGetPageHierarchyHasRightTags() throws Exception { 
  givenPages("PageOne", "PageOne.ChildOne", "PageTwo");
  
  whenRequestIsIssued("root", "type:pages");
  
  thenResponseShouldContain(
    "<name>PageOne</name>", "<name>PageTwo</name>", "<name>ChildOne</name>"
  ); 
}
```

위 함수는 원래 하나였던 테스트를 두 개로 쪼개 각각 assert를 수행한다.

그러나 읽기는 쉬워졌지만 중복되는 코드가 많아졌다.

TEMPLATE METHOD 패턴을 사용하면 중복을 제거할 수 있다. given/when 부분을 부모 클래스에 두고 then 부분을 자식 클래스에 두면 된다. 아니면 완전히 독자적인 테스트 클래스를 만들어 @Before 함수에 given/when 부분을 넣고 @Test 함수에 then 부분을 넣어도 된다. 하지만 모두가 배보다 배꼽이 더 크다. 

이것저것 감안해 보면 결국 목록 9-2처럼 assert 문을 여럿 사용하는 편이 좋다.

그럼에도 단일 assert문 규칙은 훌룽한 지침이므로 최대한 줄이는 것이 좋다.

- 테스트 당 개념 하나
    
    "테스트 함수마다 한 개념만 테스트하라”
    
    이것저것 잡다한 개념을 연속으로 테스트하는 긴 함수는 피한다.
    
    한 함수에 모두 몰아넣으면 독자가 각 절이 거기에 존재하는 이유와 각 절이 테스트하는 개념을 모두 이해해야 한다.
    

**그러므로 최종적으로 가장 좋은 규칙은 "개념 당 assert 문 수를 최소로 줄여라"와 "테스트 함수 하나는 개념 하나만 테스트하라"라 하겠다.**

### 4. F.I.R.S.T.

깨끗한 테스트가 따르는 규칙 다섯 가지이다.

1. **빠르게Fast:**테스트는 빨라야 한다. 
    
    테스트는 빨리 돌아야 한다. 테스트가 느리면 자주 돌릴 엄두를 못 낸다. 자주 돌리지 않으면 초반에 문제를 찾아내 고치지 못한다. 코드를 마음껏 정리하지도 못한다. 결국 코드 품질이 망가지기 시작한다.
    
2. **독립적으로Independent:**각 테스트를 서로 의존하면 안 된다.
    
    한 테스트가 다음 테스트가 실행될 환경을 준비해서는 안 된다. 각 테스트는 독립적으로 그리고 어떤 순서로 실행해도 괜찮아야 한다. 테스트가 서로에게 의존하면 하나가 실패할 때 나머지도 잇달아 실패하므로 원인을 진단하기 어려워지며 후반 테스트가 찾아내야 할 결함이 숨겨진다.
    
3. **반복 가능하게Repeatable:**테스트는 어떤 환경에서도 반복 가능해야 한다. 
    
    실제 환경, QA 환경, 버스를 타고 집으로 가는 길에 사용하는 노트북 환경(네트워크가 연결되지 않은)에서도 실행할 수 있어야 한다. 테스트가 돌아가지 않는 환경이 하나라도 있다면 테스트가 실패한 이유를 둘러댈 변명이 생긴다. 게다가 환경이 지원되지 않기에 테스트를 수행하지 못하는 상황에 직면한다.
    
4. **자가 검증하는Self-Validating:** 테스트는 bool값으로 결과를 내야 한다. 
    
    성공 아니면 실패다. 통과 여부를 알리고 로그 파일을 읽게 만들어서는 안 된다. 통과 여부를 보려고 텍스트 파일 두 개를 수작업으로 비교하게 만들어서도 안 된다. 테스트가 스스로 성공과 실패를 가늠하지 않는다면 판단은 주관적이 되며 지루한 수작업 평가가 필요하게 된다.
    
5. **적시에Timely:** 테스트는 적시에 작성해야 한다.
    
    단위 테스트는 테스트하려는 실제 코드를 구현하기 직전에 구현한다. 실제 코드를 구현한 다음에 테스트 코드를 만들면 실제 코드가 테스트하기 어렵다는 사실을 발견할지도 모른다. 어떤 실제 코드는 테스트하기 너무 어렵다고 판명날지 모른다. 테스트가 불가능하도록 실제 코드를 설계할지도 모른다.
