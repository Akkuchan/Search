#AOP(Aspect Oriented Programming)
  - 일반적으로 자바에서 중요시하는 OOP(Object Oriented Programming)는 사용자가 원하는 업무 기반에 로직에만 관심을 둔 프로그래밍 방법론이다.
  - 따라서 사용자가 요청하는 업무를 구현, 처리하기 위한 로직을 구현하는 것이 프로그래밍의 핵심이었다.
  - 그러다 보니 주 업무 로직 이외에 부가적으로 개발자 혹은 관리자가 해당 프로그램을 확인, 검사, 관리하기 위해 필요한 기능들의 필요성이 커졌다.
  - 즉 사용자 관점의 주 업무 이외의 개발자 중점의 로직이 필요한 것이다.
  - 이것이 관점에 따라서(사용자 vs 개발자, 관리자) 프로그래밍을 바라볼 수 있다는 개념이 바로 AOP이다.  

## AOP의 필요성 등장
  -  주기능인 비즈니스코드(Like 회원등급 기능 등등) 구현 시, 로깅, 보안, 트랜잭션 기능등의 보조기능을 매번 구현해야한다.
  - 프로그램의 규모가 커질 수록 이러한 보조 기능 코드가 반복되어 코드가 길어지고 복잡해진다. 즉 유지관리 문제가 커진다.
  - 이를 방지하고자 관점지향프로그래밍을 통해 주기능과 보조기능을 분리하고 하는 것이다.

## AOP관련 용어
  | 용어 | 설명 |
  |:---|:---|
  | `aspect` | 구현하고자 하는 보조기능을 의미, Concern 자체를 의미한다. |
  | `advice` | aspect의 실제 구현체(클래스 코드)를 의미. 메서드 호출을 기준으로 여러 지점에서 실행된다. |
  | `joinpoint` | advice를 적용하는 지점을 의미. 스프링은 method 결합점만 제공 |
  | `pointcut` | advice가 적용되는 대상을 지정한다. 패키지 이름/클래스이름/메서드이름을 정규식으로 지정하여 사용한다. |
  | `target` | advice가 적용되는 클래스를 의미, 주요 비즈니스 코드를 의미한다.  |
  | `weaving` | advice를 주기능에 적용하는 것을 의미 |
  | `Proxy` | target을 감싸고 있는 존재로 중간에 필요한 관심사들을 거쳐서 내부적으로 target을 호출하도록 작성된다. 스프링이 이를 자동으로 생성해줌 |

  - 스프링프레임워크에서 AOP 기능을 구현하는 방법
    - 스프링이 제공하는 API를 이용하기
    - @Aspect 애너테이션 이용하기

### 스프링 API를 이용한 AOP 기능 구현과정
      1. 타깃(Target) 클래스 지정
      2. 어드바이스(Advice) 클래스 지정
      3. 설정 파일에서 포인트컷(Pointcut) 지정
      4. 설정 파일에서 어드바이스와 포인트컷을 결합하는 어드바이저 설정
      5. 설정 파일에서 스프링 ProxyFactoryBean 클래스를 이용해 타깃에 어드바이스를 설정
      6. getBean()메서드로 빈 객체에 접근해 사용

### 스프링 API에서 제공하는 여러 Advice 인터페이스  
  | 인터페이스 | 추상 메서드 | 설명 |
  |:---|---|:---|
  | `MethodBeforeAdvice` | before(Method method, Object{} args, Object target) throws Throwable | 해당 메서드가 실행되기 전 실행 |
  | Method method : 대상 객체에서 실행된 메서드를 나타내는 메서드 객체 |
  | Object{} args : 메서드 인자 목록 |  
  | Object target : 대상 객체 |
  | `AfterReturningAdvice` | void afterReturning(Object returnValue, Method method, Object{} args, Object target) throws Throwable | 해당 메서드가 실행된 후 실행 |
  | Object returnValue : 대상 객체의 메서드가 반환하는 값 |
  | `ThrowsAdvice` | voic afterThrowing(Method method, Object{} args, Object target, Exception ex) | 해당 메서드에서 예외 발생 시 실행 |
  | Exception ex : 발생한 예외 타입 |
  | `MethodInterceptor` | Object invoke(MethodeInvocation invocation) throrws Throwable | 해당 메서드의 실행 전/후와 예외 발생 시 실행 |
  | MethodInvocation invocation : 대상 객체의 모든 정보를 담고 있는 객체(호출된 메서드, 인자) |
