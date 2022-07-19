## 오류 코드보다 예외를 사용


- 오류 코드를 반환하는 방식

```java
public class DeviceController {
  ...
  public void sendShutDown() {
    DeviceHandle handle = getHandle(DEV1);
    // Check the state of the device
    if (handle != DeviceHandle.INVALID) {
      // Save the device status to the record field
      retrieveDeviceRecord(handle);
      // If not suspended, shut down
      if (record.getStatus() != DEVICE_SUSPENDED) {
        pauseDevice(handle);
        clearDeviceWorkQueue(handle);
        closeDevice(handle);
      } else {
        logger.log("Device suspended. Unable to shut down");
      }
    } else {
      logger.log("Invalid handle for: " + DEV1.toString());
    }
  }
  ...
}
```

- 예외 방식
```java
public class DeviceController {
  ...
  public void sendShutDown() {
    try {
      tryToShutDown();
    } catch (DeviceShutDownError e) {
      logger.log(e);
    }
  }
    
  private void tryToShutDown() throws DeviceShutDownError {
    DeviceHandle handle = getHandle(DEV1);
    DeviceRecord record = retrieveDeviceRecord(handle);
    pauseDevice(handle); 
    clearDeviceWorkQueue(handle); 
    closeDevice(handle);
  }
  
  private DeviceHandle getHandle(DeviceID id) {
    ...
    throw new DeviceShutDownError("Invalid handle for: " + id.toString());
    ...
  }
  ...
}
```
<br>

## Try-Catch-Finally문부터 작성

- try블록에서 무슨 예외가 생기든 catch블록은 일관성을 유지하는 것이 중요
- 예외가 발생할 수 있는 코드를 짤 때는 try-catch-finally문으로 시작하는 게 나음
- try-catch 구조로 예외의 유형을 좁히고 TDD를 사용해 필요한 논리를 추가한다.
  
  *TDD(Test-Driven Development): 선 테스트 후 개발 방식. 즉 먼저 테스트를 작성한 후 테스트를 통과하기 위한 코드를 개발한다.
  

<br>

## 미확인 예외를 사용하라
  <hr/>

- 확인된 예외: 컴파일 시점에 예외 처리 여부 확인
  - ex) `FileNotFoundException`, `SQLException`
- 미확인된 예외: 런타임 시점에 예외 처리 여부 확인
  - ex) 모든 `RuntimeException`을 상속하는 예외
- 확인된 예외는 OCP(Open/Close Principle) 원칙을 위반
  - if 메서드에서 확인된 예외를 던졌는데 catch 블록이 세 단계 위에 있음
-> 그 사이 메서드 모두가 선언부에 해당 예외를 정의해야 함
-> 하위 단계에서 코드 변경 시 상위 단계의 메서드 선언부를 전부 고쳐야함
-> 캡슐화가 깨짐
- 참고) 아주 중요한 라이브러리를 작성한다면 모든 예외를 잡아야 함

<br>

## 예외에 의미를 제공하라

- 예외를 던질 때는 전후 상황을 충분히 덧붙임
- 자바는 모든 예외에 호출스택을 제공하지만 실패한 코드의 의도를 파악하려면 그것만으로는 부족
- 오류메세지에 실패한 연산 이름과 실패 유형에 대한 정보를 담아 예외와 함께 던짐

<br>

## 호출자를 고려해 예외 클래스를 정의

- 오류를 분류하여 잡아냄
ex) 오류가 발생한 위치, 오류의 유형 등으로 분류
- 외부 API를 사용할 때는 감싸기 기법을 사용
  - 외부 API를 감싸면 외부 라이브러리와 프로그램 사이의 의존성이 줄어듦
  - 감싸기 클래스에서 외부 API를 호출하는 대신 테스트 코드를 넣어주는 방법으로 프로그램 테스트 가능
  
## null을 반환하지 마라

- null을 반환하는 코드는 일거리를 늘릴 뿐만 아니라 호출자에게 문제를 떠넘김
- 해결책: 
  - 메서드에서 null을 리턴하고 싶다면 그 대신 예외를 던지거나 특수 사례 객체를 반환
  - 사용하려는 외부 API가 null을 반환한다면 감싸기 메서드를 구현해 예외를 던지거나 특수 사례 객체를 반환

  *특수 사례 패턴(Special Case Pattern): 클래스를 만들거나 객체를 조작해 특수 사례를 처리하는 방식
  
 - 예시
나쁜 예시(getEmployees()가 null을 반환할 수 있음)
```java
List<Employee> employees = getEmployees();
  if (employees != null) {
	for(Employee e : employees) {
		totalPay += e.getPay();
	}
}
```

   좋은 예시(getEmployees()가 null 대신 빈 리스트를 반환할 수 있음)
   
```java
  List<Employee> employees = getEmployees();
  for(Employee e : employees) {
    totalPay += e.getPay();
  }
  
  public List<Employee> getEmployees() {
    if( .. there are no employees .. )
      return Collections.emptyList();
    }
}
```

## null을 전달하지 마라

- 메서드에서 null을 반환하는 것도 나쁘지만 메서드로 null을 전달하는 방식은 더 나쁘다.
- 정상적인 인수로 null을 기대하는 API가 아니라면 메서드를 null을 전달하는 것은 지양
-  인수로 null을 전달하면 ``NullPointerException`` 이 발생


## 결론
- 오류처리를 프로그램 로직과 분리하면 독립적인 추론이 가능해지며 코드 유지보수성도 크게 높아진다.
	
