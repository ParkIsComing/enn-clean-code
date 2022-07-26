# chapter14_Successive Refinement

요약

- 하나의 프로젝트를 점진적으로 리팩토링하여 코드를 개선해나가는 방법
- 단순히 돌아가는 코드는 심하게 망가지는 사례가 된다. 나쁜코드는 개발 프로젝트에 악영향을 미치는 요인이며, 깨끗한 코드로 개선할 수 있지만 비용이 엄청나게 많이 든다.
- 처음부터 코드를 깨끗하게 유지하고 깔끔하게 유지해야한다.

소개

- 이 장은 명령형 인수 구문분석기(main함수의 args로 넘어오는 문자열을 분석하는 프로그램)코드에 대한 점진적인 개선을 보여주는 사례 연구이다. 먼저 출발은 좋았으나 확장성이 부족했던 모듈을 소개한다. 그다음, 모듈을 개선하고 정리하는 단계를 살펴본다.
- 프로그램을 짜다 보면 종종 명령행 인수의 구문을 분석할 필요가 생긴다. 편리한 유틸리티가 없다면 메인함수로 넘어오는 문자열 배열을 직접 분석하게 된다. 여러가지 훌륭한 유틸리티가 있지만, 내 사정에 딱 맞는 유틸리티가 없다면 직접 짜겠다고 결심한다. 새로 짠 유틸리티를 Args라고 부르자.
- Args는 사용법이 간단하다. Args 생성자에 인수 문자열과 형식 문자열을 넘겨 Args인스턴스를 생성한 후 Args 인스턴스에다 인수 값을 질의한다.
- String[] args는 매개변수이며, 매개변수는 메소드가 호출될 때 전달된 입력값을 메소드 내부로 전달하는 역할을 하는 변수이다. 데이터형은 String[] 인데, String[] 은 문자열을 담고 있는 배열이다.

## 14-1 간단한 Args사용법

```jsx
public static void main(String[] args) {
  try {
    Args arg = new Args("l,p#,d*", args);
    boolean logging = arg.getBoolean('l');
    int port = arg.getInt('p');
    String directory = arg.getString('d');
    executeApplication(logging, port, directory);
  } catch (ArgsException e) {
    System.out.print("Argument error: %s\n", e.errorMessage());
  }
}
```

- 매개변수 두 개로 Args클래스의 인스턴스 생성
    - 첫째 매개변수는 형식 또는 스키마를 지정하는데, “l,p#,d*”는 명령행 인수 세개를 정의한다
    - 둘째 매개변수는 main으로 넘어온 명령형 인수 배열 자체이다.
- 생성자에서 ArgsException이 발생하지 않았다면 명령행 인수의 구문을 성공적으로 분석했으며 Args인스턴스에 질의를 던져도 좋다는 말이다.
- 인수 값을 가져오려면, getBoolean, getInteger, getString등의 메서드를 사용한다.

# Args구현

```jsx
import static com.objectmentor.utilities.args.ArgsException.ErrorCode.*;

public class Args {
	private Map<Character, ArgumentMarshaler> marshalers;
	private Set<Character> argsFound;
	private ListIterator<String> currentArgument;

	public Args(String schema, String[] args) throws ArgsException {
		marshalers = new HashMap<Character, ArgumentMarshaler>();
		argsFound = new HashSet<Character>();
    
    parseSchema(schema);
    parseArgumentStrings(Arrays.asList(args)); 
  }
  
  private void parseSchema(String schema) throws ArgsException { 
    for (String element : schema.split(","))
      if (element.length() > 0) 
        parseSchemaElement(element.trim());
  }
  
  private void parseSchemaElement(String element) throws ArgsException { 
    char elementId = element.charAt(0);
    String elementTail = element.substring(1); validateSchemaElementId(elementId);
    if (elementTail.length() == 0)
      marshalers.put(elementId, new BooleanArgumentMarshaler());
    else if (elementTail.equals("*")) 
      marshalers.put(elementId, new StringArgumentMarshaler());
    else if (elementTail.equals("#"))
      marshalers.put(elementId, new IntegerArgumentMarshaler());
    else if (elementTail.equals("##")) 
      marshalers.put(elementId, new DoubleArgumentMarshaler());
else if (elementTail.equals("[*]"))
      marshalers.put(elementId, new StringArrayArgumentMarshaler());
    else
      throw new ArgsException(INVALID_ARGUMENT_FORMAT, elementId, elementTail);
  }
  
  private void validateSchemaElementId(char elementId) throws ArgsException { 
    if (!Character.isLetter(elementId))
      throw new ArgsException(INVALID_ARGUMENT_NAME, elementId, null); 
  }
  
  private void parseArgumentStrings(List<String> argsList) throws ArgsException {
    for (currentArgument = argsList.listIterator(); currentArgument.hasNext();) {
      String argString = currentArgument.next(); 
      if (argString.startsWith("-")) {
        parseArgumentCharacters(argString.substring(1)); 
      } else {
        currentArgument.previous();
        break; 
      }
    } 
  }
  
  private void parseArgumentCharacters(String argChars) throws ArgsException { 
    for (int i = 0; i < argChars.length(); i++)
      parseArgumentCharacter(argChars.charAt(i)); 
  }
  
  private void parseArgumentCharacter(char argChar) throws ArgsException { 
    ArgumentMarshaler m = marshalers.get(argChar);
    if (m == null) {
      throw new ArgsException(UNEXPECTED_ARGUMENT, argChar, null); 
    } else {
      argsFound.add(argChar); 
      try {
        m.set(currentArgument); 
      } catch (ArgsException e) {
        e.setErrorArgumentId(argChar);
        throw e; 
      }
    } 
  }
  
  public boolean has(char arg) { 
    return argsFound.contains(arg);
  }
  
  public int nextArgument() {
    return currentArgument.nextIndex();
  }
  
  public boolean getBoolean(char arg) {
    return BooleanArgumentMarshaler.getValue(marshalers.get(arg));
  }
  
  public String getString(char arg) {
    return StringArgumentMarshaler.getValue(marshalers.get(arg));
  }
  
  public int getInt(char arg) {
    return IntegerArgumentMarshaler.getValue(marshalers.get(arg));
  }
  
  public double getDouble(char arg) {
    return DoubleArgumentMarshaler.getValue(marshalers.get(arg));
  }
  
  public String[] getStringArray(char arg) {
    return StringArrayArgumentMarshaler.getValue(marshalers.get(arg));
  } 
}
```

- 잘 정리된 코드로, 코드가 위에서 아래로 잘 읽히고, 전반적으로 깔끔한 구조에 잘 짜인 프로그램으로 보인다.
- 예를 들어 날짜 인수나 복소수 인수 등 새로운 인수 유형을 추가하는 방법이 명백하다.

### 이런 코드를 어떻게 짰을까??

- 처음부터 깔끔한 코드를 구현하는 것은 어렵다. 먼저 지저분한 코드를 짠 뒤에 정리해서 깔끔한 코드를 만들어준다.
- 그저 돌아가기만하고 정리가 안된 코드는 나쁜코드이며,  심각하게 개발 프로젝트에 악영향을 미친다.
- 나쁜 코드를 깨끗한 코드로 개선하기 위해서는 비용과 시간이 많이 든다. 그렇기 때문에 처음에 작성한 코드를 곧바로 정리해서 깨끗하게 유지해야한다.

위의 Args클래스의 초안

```jsx
import java.text.ParseException; 
import java.util.*;

public class Args {
  private String schema;
  private String[] args;
  private boolean valid = true;
  private Set<Character> unexpectedArguments = new TreeSet<Character>(); 
  private Map<Character, Boolean> booleanArgs = new HashMap<Character, Boolean>();
  private Map<Character, String> stringArgs = new HashMap<Character, String>(); 
  private Map<Character, Integer> intArgs = new HashMap<Character, Integer>(); 
  private Set<Character> argsFound = new HashSet<Character>();
  private int currentArgument;
  private char errorArgumentId = '\0';
  private String errorParameter = "TILT";
  private ErrorCode errorCode = ErrorCode.OK;
  
  private enum ErrorCode {
    OK, MISSING_STRING, MISSING_INTEGER, INVALID_INTEGER, UNEXPECTED_ARGUMENT}
    
  public Args(String schema, String[] args) throws ParseException { 
    this.schema = schema;
    this.args = args;
    valid = parse();
  }
  
  private boolean parse() throws ParseException { 
    if (schema.length() == 0 && args.length == 0)
      return true; 
    parseSchema(); 
    try {
      parseArguments();
    } catch (ArgsException e) {
    }
    return valid;
  }
  
  private boolean parseSchema() throws ParseException { 
    for (String element : schema.split(",")) {
      if (element.length() > 0) {
        String trimmedElement = element.trim(); 
        parseSchemaElement(trimmedElement);
      } 
    }
    return true; 
  }
  
  private void parseSchemaElement(String element) throws ParseException { 
    char elementId = element.charAt(0);
    String elementTail = element.substring(1); 
    validateSchemaElementId(elementId);
    if (isBooleanSchemaElement(elementTail)) 
      parseBooleanSchemaElement(elementId);
    else if (isStringSchemaElement(elementTail)) 
      parseStringSchemaElement(elementId);
    else if (isIntegerSchemaElement(elementTail)) 
      parseIntegerSchemaElement(elementId);
    else
      throw new ParseException(String.format("Argument: %c has invalid format: %s.", 
        elementId, elementTail), 0);
    } 
  }
    
  private void validateSchemaElementId(char elementId) throws ParseException { 
    if (!Character.isLetter(elementId)) {
      throw new ParseException("Bad character:" + elementId + "in Args format: " + schema, 0);
    }
  }
  
  private void parseBooleanSchemaElement(char elementId) { 
    booleanArgs.put(elementId, false);
  }
  
  private void parseIntegerSchemaElement(char elementId) { 
    intArgs.put(elementId, 0);
  }
  
  private void parseStringSchemaElement(char elementId) { 
    stringArgs.put(elementId, "");
  }
  
  private boolean isStringSchemaElement(String elementTail) { 
    return elementTail.equals("*");
  }
  
  private boolean isBooleanSchemaElement(String elementTail) { 
    return elementTail.length() == 0;
  }
  
  private boolean isIntegerSchemaElement(String elementTail) { 
    return elementTail.equals("#");
  }
  
  private boolean parseArguments() throws ArgsException {
    for (currentArgument = 0; currentArgument < args.length; currentArgument++) {
      String arg = args[currentArgument];
      parseArgument(arg); 
    }
    return true; 
  }
  
  private void parseArgument(String arg) throws ArgsException { 
    if (arg.startsWith("-"))
      parseElements(arg); 
  }
  
  private void parseElements(String arg) throws ArgsException { 
    for (int i = 1; i < arg.length(); i++)
      parseElement(arg.charAt(i)); 
  }
  
  private void parseElement(char argChar) throws ArgsException { 
    if (setArgument(argChar))
      argsFound.add(argChar); 
    else 
      unexpectedArguments.add(argChar); 
      errorCode = ErrorCode.UNEXPECTED_ARGUMENT; 
      valid = false;
  }
  
  private boolean setArgument(char argChar) throws ArgsException { 
    if (isBooleanArg(argChar))
      setBooleanArg(argChar, true); 
    else if (isStringArg(argChar))
      setStringArg(argChar); 
    else if (isIntArg(argChar))
      setIntArg(argChar); 
    else
      return false;
    
    return true; 
  }
  
  private boolean isIntArg(char argChar) {
    return intArgs.containsKey(argChar);
  }
  
  private void setIntArg(char argChar) throws ArgsException { 
    currentArgument++;
    String parameter = null;
    try {
      parameter = args[currentArgument];
      intArgs.put(argChar, new Integer(parameter)); 
    } catch (ArrayIndexOutOfBoundsException e) {
      valid = false;
      errorArgumentId = argChar;
      errorCode = ErrorCode.MISSING_INTEGER;
      throw new ArgsException();
    } catch (NumberFormatException e) {
      valid = false;
      errorArgumentId = argChar; 
      errorParameter = parameter;
      errorCode = ErrorCode.INVALID_INTEGER; 
      throw new ArgsException();
    } 
  }
  
  private void setStringArg(char argChar) throws ArgsException { 
    currentArgument++;
    try {
      stringArgs.put(argChar, args[currentArgument]); 
    } catch (ArrayIndexOutOfBoundsException e) {
      valid = false;
      errorArgumentId = argChar;
      errorCode = ErrorCode.MISSING_STRING; 
      throw new ArgsException();
    } 
  }
  
  private boolean isStringArg(char argChar) { 
    return stringArgs.containsKey(argChar);
  }
  
  private void setBooleanArg(char argChar, boolean value) { 
    booleanArgs.put(argChar, value);
  }
  
  private boolean isBooleanArg(char argChar) { 
    return booleanArgs.containsKey(argChar);
  }
  
  public int cardinality() { 
    return argsFound.size();
  }
  
  public String usage() { 
    if (schema.length() > 0)
      return "-[" + schema + "]"; 
    else
      return ""; 
  }
  
  public String errorMessage() throws Exception { 
    switch (errorCode) {
      case OK:
        throw new Exception("TILT: Should not get here.");
      case UNEXPECTED_ARGUMENT:
        return unexpectedArgumentMessage();
      case MISSING_STRING:
        return String.format("Could not find string parameter for -%c.", errorArgumentId);
      case INVALID_INTEGER:
        return String.format("Argument -%c expects an integer but was '%s'.", errorArgumentId, errorParameter);
      case MISSING_INTEGER:
        return String.format("Could not find integer parameter for -%c.", errorArgumentId);
    }
    return ""; 
  }
  
  private String unexpectedArgumentMessage() {
    StringBuffer message = new StringBuffer("Argument(s) -"); 
    for (char c : unexpectedArguments) {
      message.append(c); 
    }
    message.append(" unexpected.");
    
    return message.toString(); 
  }
  
  private boolean falseIfNull(Boolean b) { 
    return b != null && b;
  }
  
  private int zeroIfNull(Integer i) { 
    return i == null ? 0 : i;
  }
  
  private String blankIfNull(String s) { 
    return s == null ? "" : s;
  }
  
  public String getString(char arg) { 
    return blankIfNull(stringArgs.get(arg));
  }
  
  public int getInt(char arg) {
    return zeroIfNull(intArgs.get(arg));
  }
  
  public boolean getBoolean(char arg) { 
    return falseIfNull(booleanArgs.get(arg));
  }
  
  public boolean has(char arg) { 
    return argsFound.contains(arg);
  }
  
  public boolean isValid() { 
    return valid;
  }
  
  private class ArgsException extends Exception {
  } 
}
```

- 돌아가긴 하지만, 엉망인 코드이다.
- 인스턴스 변수 개수가 너무 많고, 이해하기 어려운 문자열, HashSet, Treeset, try-catch문 등 지저분하다.
- 처음에는 Booleann, 그 다음에는 String과 Integer인수 유형을 추가하면서 코드는 통제를 벗어나기 시작했고, 엉망이 되었다.

### 개선방향-일단 멈춤

- 더 이상의 기능을 추가하지 않고 리팩터링 시작
- 점진적인 개선
    - 개선이라는 이름으로 구조를 크게 뒤집는 것은 프로그램을 망치는 방법이다.
    - TDD(Test Driven Development)기법을 사용한다.
        - 언제 어느 때라도 시스템이 돌아가야한다. 시스템을 망가뜨리는 변경을 허용하지 않고, 변경된 후에도 시스템이 변경전과 똑같이 돌아가야한다는 말이다.
        - 자동화 된 테스트 슈트를 사용한다.
        

# 결론

- 코드를 작성할때 미루지 말고 리팩터링을 하고 깔끔하게 유지하자.