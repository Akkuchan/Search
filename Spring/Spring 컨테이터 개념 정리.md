## Spring Container(IoC 컨테이너)
  - Bean 객체에 대한 생명주기 관리
    - 빈 객체의 인스턴스화, 구성, 생명주기, 제거를 처리한다.
  - POJO의 생성과 초기화, 서비스, 소멸에 대한 권한을 가진다.

### 컨테이너란?
  - 톰캣 같은 WAS는 서블릿 컨테이너라고 부른다. 톰캣을 실행하면 톰캣이 서블릿의 생성, 초기화, 서비스 실행, 소멸에 관한 모든 권한을 갖고 서블릿을 관리하기 때문.

빈팩토리
    - 스프링 설정파일에 등록된 Bean 객체를 생성하고 관리하는 컨테이너

스프링빈
  - 빈이란? 어플리케이션에서 사용하는 객체
  - 설정 정보와 함께 스프링 컨테이너에 등록된 객체를 의미
  - 즉 스프링 컨테이너(IoC 컨테이너)가 관리하는 객체

  자바 빈 vs 스프링 빈
    - 두가지는 분명 다름
    - 자바빈의 경우 단순히 클래스에서 게터세터만 가진 클래스를 의미. 게터세터외의 메소드는 존재하지 않는다. 생성자도 없음

빈데피니션
  - 빈 설정 메타정보
    - 스프링 컨테이너는 빈데피니션이라는 추상화를 통해 스프링 빈을 생성함.

    -AppConfig.class를 파라미터로 ac(ApplicationContext)를 생성하게 되면 내부적으로는 AppConfig.classs 정보를 바탕으로 BeanDefinition 인터페이스의 구현체 중 하나인 객체를 만든다.. 그걸 바탕으로 스프링은 빈을 생성하게 된다.
    - AnnotationConfigApplicationContext에 AppConfig.class를 넘겨줬을 때 BeanDefinition의 구현체인 AnnotatedGenericBeanDefinition을 만들게 된다. 해당 객체에서 Bean메타정보를 가지고 Spring Container에서 Bean을 생성하게 된다.
