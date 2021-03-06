# Chapter 11. 시스템
높은 추상화 수준, 즉 시스템 수준에서도 꺠끗함을 유지하는 방법

## 시스템 제작과 시스템 사용을 분리하라
>제작은 사용과 아주 다르다.
즉 소프트웨어 시스템은 애플리케이션 객체를 제작하고 의존성을 서로 연결하는 준비 과정과 준비과정 이후에 이어지는 런타임 로직을 분리해야 한다.

### 체계적이고 탄탄한 시스템을 만들고 싶다면 모듈성을 깨서는 절대로 안된다.
1. Main 분리
- 시스템 생성과 시스템 사용을 분리하는 한가지 방법
- 생성과 관련한 코드는 모두 main이나 main이 호출하는 모듈로 옮기고, 나머지 시스템은 모든 객체가 생성되었고 모든 의존성이 연결되었다고 가정함

2. 팩토리
- 객체가 생성되는 시점을 애플리케이션이 결정할 필요가 생김
- 이때는 ABSTRACT FACTORY 패턴을 사용
- 그러면 객체가 생성되는 시점은 애플리케이션이 결정하지만 객체를 생성하는 코드는 애플리케이션이 알 수 없음

3. 의존성 주입
- 사용과 제작을 분리하는 강력한 메커니즘
- 제어 역전 기법을 의존성 관리에 적용한 메커니즘
- 제어 역전 : 한 객체가 맡은 보조 책임을 새로운 객체에게 전적으로 떠넘김, 이때 새로운 객체는 넘겨받은 책임만 맡으므로 단일 책임 원칙을 지키게 됨
- 이처럼 의존성 관리 맥락에서 객체는 의존성 자체를 인스턴스로 만드는 책임을 지지 않고, 이런 책임을 다른 전담 메커니즘에 넘김

## 확장
>소프트웨어 시스템은 물리적인 시스템과 다르다, 관심사를 적절히 분리해 관리한다면 소프트웨어 아키텍처는 점진적으로 발전할 수 있다.

이때, 테스트 주도 개발(TDD), 리팩터링, 그리고 이를 통해 얻어지는 깨끗한 코드는 코드 수준에서 시스템을 조정하고 확장하기 쉽게 만든다.

## Cross-Cutting Concerns 해결을 위한 세 가지 방법
- 자바 프록시
- 순수 자바 AOP 프레임 워크
- AspectJ 관점

## 테스트 주도 시스템 아키텍처 구축
- 코드 레벨에서부터 아키텍쳐와 분리된(decouple된) 프로그램 작성은 당신의 아키텍쳐를 test drive하기 쉽게 만들어 준다.
- 처음에는 작고 간단한 구조에서 시작하지만 필요에 따라 새로운 기술을 추가해 정교한 아키텍쳐로 진화할 수 있다.
- 또한 decouple된 코드는 user story, 규모 변화와 같은 변경사항에 더 빠르게 대처할 수 있도록 우리를 도와 준다.
- 도리어 BDUF(Big Design Up First)와 같은 방식은 변경이 생길 경우 기존의 구조를 버려야 한다는 심리적 저항, 아키텍쳐에 따른 디자인에 대한 고민 등 변화에 유연하지 못한 단점들을 가져오게 된다.
- 초기 EJB와 같이 너무 많은 엔지니어링이 가미되어 많은 concern들을 묶어버리지 않으며 오히려 많은 부분들을 숨기는 것이 아름다운 구조를 가져올 것이다.

## 의사 결정을 최적화하라
- 모듈을 나누고 관심사를 분리하면 지엽적인 관리와 결정이 가능해진다.
- 도시든 소프트웨어든 시스템에서 한 사람이 모든 결정을 내리긴 어렵다.
- 그러므로 가장 적합한 사람에게 책임을 맡기면 가장 좋다.
- 때때로 가능한 마지막 순간까지 결정을 미루는 방법이 최선이 될 수도 있다. 최대한 정보를 모아 최선의 결정을 내리기 위해서다.
- 관심사를 모듈로 분리한 POJO 시스템은 이런 방식에 적합하다. 최신 정보에 기만해 최선의 시점에 최적의 결정을 내리기 쉬워진다.

## 명백한 가치가 있을 때 표준을 현명하게 사용하라
- EJB2는 단지 표준이라는 이유만으로 많은 팀이 사용했다.
- 가볍고 간단한 설계로 충분했을 프로젝트에서도 EJB2를 체택했다.
- 표준을 사용하면 아이디어와 컴포넌트를 재사용하기 쉽고 적절한 경험을 가진 사람을 구하기 쉬우며 좋은 아이디어는 캡슐화 하기 쉽다.
- 하지만 때로는 표준을 만드는 시간이 너무 오래 걸려 업계가 기다리지 못하는 경우도 있다.

## 시스템은 도메인 특화 언어가 필요하다
- 소프트웨어 분야에서도 최근 들어 DSL (Domain Specific Language)가 조명받기 시작했다.
- DSL은 간단한 스크립트 언어나 표준 언어로 구현한 API를 말한다.
- DSL로 짠 코드는 도메인 전문가가 작성한 구조처럼 잘 읽힌다.
- 좋은 DSL은 도메인 개념과 그 개념을 구현한 코드 사이에 존재하는 의사소통 간격을 줄여준다.
- 효과적으로 사용한 DSL은 추상화 수준을 코드 관용구나 디자인 패턴 이상으로 끌여올려준다. 그래서 개발자가 적절한 추상화 수준에서 코드 의도를 표현할 수 있다.