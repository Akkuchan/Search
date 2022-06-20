# Annotation을 통한 DI
  - Annotation이 무엇인지 모른다면 java의 Annotation를 학습해보자
  - xml을 이용하는 것 보다 수월하게 DI를 수행할 수 있다.
  - 스프링 어노테이션에는 여러가지가 있다.
  --- Spring MVC 어노테이션 ---
    - @Service
    - @Repository
    - @Component
    - @Autowired
    - @Transactional
    - @Scope
  --- Spring MVC 어노테이션 ---
    - @Controller
    - @RequestMapping
    - @PathVariable
    - @RequestParam
    - @ModelAttribute
    - @SessionAttributes
  ---Spring Security 어노테이션 ---
    - @PreAuthorize

  ```xml
  <bean id="b" class= "data.B2"/>
  <bean id="console" class="service.S">
      <property name="b" ref="b" />
  </bean>
  ```
  위와 같이 xml을 사용하다가 부품을 data.B3로 바꾼다고 가정하자.
  xml의 코드를 B3로 바꾸면 되는데 이렇게 바꾸는 것조차 더 개선할 수 있는 방법이 바로 어노테이션을 사용하는 것이다.
  즉 코드 자체에 어노테이션을 밖아 주는 것으로 DI를 끝내버리는 것.

## @AutoWired  
  ```xml
  <bean class="spring.di.entity.NewlecExam"/><!-- id값이 없어도, 즉 객체에 이름을 안 지어줘도 bean 객체는 생성된다 -->
  ```
  - @AutoWired를 통해 <property name="exam"  ref="exam"/> 코드를 대체할 수 있다.
    ```java
    @AutoWired
    @Override
    public void setExam(Exam exam){
      this.exam = exam;
    }
    ```
    - InlineExamConsole 클래스의 setter 메소드이다. 인터페이스의 setExam을 구현하기에 @Override가 있다.
    - 여기에 @AutoWired를 추가하면 더이상  InlineExamConsole 객체의 내부멤버인  private Exam exam을 xml에서 생성하지 않아도 된다.
      - 이는 어떻게 가능할까? xml의 <bean id="console" class="spring.di.ui.InlineExamConsole">를 통해 객체가 만들어지면 Spring이 자동으로 해당 클래스내의 @AutoWired가 명시된 멤버가 있는지 확인해준다.
      - 이때 주의할 점은 Spring이 자동으로 @AutoWired를 찾으라는 명령을 따로 해야한다는 것을 잊지 말아야 한다. 그렇지 않으면 Spring이 내부 멤버를 확인하는 작업을 하지 않기 때문
        - 이를 위해서 namespace에서 context 패키지를 가져오도록 설정해야한다.
          - <context:annotation-config/> 이 코드만 작성한다면 된다.

    - @AutoWired는 이렇게 자동으로 DI를 해주기는 하지만 약간의 문제가 있다.
    - setter()는 매개변수로 값이나 객체를 넣어주어 멤버변수를 초기화한다.
    - 근데 이때 어떤 매개변수가 들어가는지에 대한 설정은 앞선 예시에서는 찾을 수 없다.
      - 쉽게 말해 컨테이너의 bean 객체들중에 어떤 Exam 타입의 객체를 넣을 것이냐가 명시되지 않았다는 의미, Exam 타입이 여러개일 수도 있고 아에 Exam 타입 객체가 없을 수도 있으니까

    - 위 자바 setter()의 매개변수는 Exam타입의 객체 exam이다.
    - 만약 Exam 타입의 객체가 컨테이너내에 1개만 존재한다면 따로 어떤 값을 매개변수로 넣을지 지정하지 않아도 된다(어차피 1개 밖에 없으니 그걸 넣으면 되니까)
    - 하지만 여러개가 있을 경우에는 어떤 Exam 타입의 객체를 매개변수로 삼을지를 정해야 한다.
    - 이전에 우리는 property 태그를 통해 인스턴스 멤버를 초기화 할 때 id라를 태그를 추가해 어떤 멤버에 특정 값을 넣을지 지정했다.  
    - 마찬가지로 id 태그를 이용해 어떤 Exam 객체를 넣을지 지정할 수 있다.

### @AutoWired와 @Qualifier의 활용    
    ```xml
      <bean id= "exam1" class="spring.di.entity.NewlecExam"/>
      <bean id= "exam2" class="spring.di.entity.NewlecExam"/>
    ```
    - 위 코드를 통해 컨테이너에 NewlecExam 객체 "exam1"과 "exam2"가 생성된다.
    이때 특정 bean을 선택하기 위해서 NewlecExam의 setter()에 **@Qualifier** 라는 어노테이션을 활용할 수 있다.
    ```java
    @AutoWired
    @Qualifier("exam2")
    @Override
    public void setExam(Exam exam){
      this.exam = exam;
    }
    ```
    - @Qualifier("exam2")코드를 통해서 매개변수로 들어갈 Exam 객체를 id= "exam2" 인 bean 객체로 지정할 수가 있다.
      - 만약 @Qualifier를 쓰기 싫다면 exam이라는 매개변수 객체명을 exam2로 바꾸어도 실행이 된다.  


### @AutoWired의 위치와 require 옵션
    - 이전에 DI의 방법 2가지 setter()와 생성자 활용이 있다고 했는데 마찬가지로 @AutoWired 또한 생성자에도 붙어 쓸 수 있다.
      ```java
        @AutoWired
        public InlineExamConsole(Exam exam){
          this exam= exam;
        }
      ```
      - 여기서 또한 특정 Exam 객체를 매개변수로 한다면 @Qualifier를 쓸 수 있는데 추가로 만약 생성자의 매개변수가 여러개이고 이에 대한 Bean 객체를 각각 지정할 수도 있다.

      ```java
      @AutoWired
      public InlineExamConsole(
          @Qualifier("exam1") Exam exam1,
          @Qualifier("exam2") Exam exam2){
        this exam= exam1;
        this exam= exam2;
      }
      ```
      - 위 코드는 클래스가 Exam 객체를 2개 가지고 있어 초기화한다는 의미이네 크게 상관은 없다.
      - 중요한 것은 여러 매개변수로 생성자를 초기화하는 것도 가능하다는 것이다.

    - 추가로 @AutoWired를 통한 DI방법이 한가지 더 있는데 바로 멤버 변수인 Exam 객체에 바로 사용하는 방법이다.
    ```java
      @AutoWired
      private Exam exam;

      public InlineExamConsole(){
      }
    ```
    - 이를 통해 setter()나 Overloaded Constructor(매개변수 있는 생성자) 없이도 객체 생성이 가능하다.
      - 하지만! 이것 또한 객체를 만드는 것이고 매개변수가 없기 때문에 기본 생성자가 반드시 들어가야 한다.

#### require옵션
    - 앞에서 컨테이너에 Bean 객체가 없다면 에러가 난다고 했다.(nullPoint에러)
    - 하지만 객체가 없더라도 JVM에서 객체를 생성하는데는 문제가 없게 할 필요가 있을 수도 있기에 이를 해결할 방법이 바로 require옵션이다
    ```java
    @AutoWired(require=false)
    private Exam exam;
    }
    ```
    - 이렇게 옵션을 false로 설정하면 컨테이너 객체를 통해 클래스 객체의 호출이 가능해진다.
      - 물론 Exam 타입의 객체가 필요한(의존성이 있는) 메서드 혹은 멤버 변수를 호출하지는 못 한다.


## @Component를 이용한 객체 생성
  - 앞에서 우리는 xml에서 bean 태그를 이용해 컨테이너안에 bean 객체를 생성하였다.
  - 이러한 객체 생성을 @Component가 대신할 수 있다.
  ```java
    class InlineExamConsole{
      @AutoWired
      public void setExam(Exam exam){
        this.exam = exam
      }
    }
  ```
  ```xml  
  <context:annotation-config/>
  <bean id="exam" class="spring.di.entity.NewlecExam"/>
  <bean id="console" class="spring.di.ui.InlineExamConsole"/>
  ```
  - java 코드와 xml 코드가 다음과 같이 설정되어있을 때 console 객체가 생성되는 것을 @Component로 대체가능하다
  ```java
    @Component
    class InlineExamConsole{
      @AutoWired
      public void setExam(Exam exam){
        this.exam = exam
      }
    }
  ```
  ```xml  
  <context:component-scan base-package="spring.di.entity" />
  <bean id="exam" class="spring.di.entity.NewlecExam"/>
  ```
  - 이전 코드와 달라진 것은 @Component와 <context:component-scan base-package="spring.di.entity" />이다
  - 전자는 생성할 객체의 클래스를 명시하는 어노테이션이다
  - 후자는 base-package에 설정된 패키지에서 component 어노테이션이 붙은 클래스를 찾으라(scan)는 의미이다.
  - 이때 @Component를 통해 어떤 클래스를 객체화할 지 알게 되었다. 그 말을 내부 멤버변수인 private Exam exam도 알아서 만들어 질 것이라는 것!
  - @AutoWired도 있기에 어떻게 객체화할지는 정해져 있으므로 <context:annotation-config/> 태그는 불필요하기에 지워도 된다.

### Bean 객체 호출과 @Component
  - 이렇게 컨테이너에 객체가 생성되었다. 그렇다면 이를 개발자가 컨테이너의 객체들을 호출해 활용할 일만 남았다.
  - 하지만 몇가지 주의사항이 있다.
  ```Java
  ApplicationContext context = new ClassPathXmlApplicationContxt("spring/di/setting.xml");
  // "console"이라는 이름을 통해 해당 bean 객체 호출
  ExamConsole console = (ExamConsole)context.getBean("console");
  // ExamConsole.class 매개변수로 해당 타입의 객체를 호출
  ExamConsole console = context.getBean(ExamConsole.class);
  ```
  - main 메서드에서 다음 코드를 실행해 context라는 컨테이너 객체를 만들고 getBean()으로 원하는 bean 객체를 불러온다.
  - bean 객체를 호출하는 방법이 위에 나온 것처럼 2가지이다.
  - 후자의 경우 따로 지정할 필요없이 객체를 가져오지만 동일 타입 여러 객체가 컨테이너에 있을 때는 이를 지정해줘야한다.
  - 전잔의 경우 이름을 가져올 객체를 선택하기 때문에 이를 지정해주어야 한다.
  - 그런데 우리는 좀전에 xml에 있던 <bean id="console" class="spring.di.ui.InlineExamConsole"/> 코드를 지우고 @Component로 대체 했다
  - 따라서 @Component가 그 기능을 대신해야 한다.
  - 방법은 간단하다 @Component("console")형식으로 객체로 만들 클래스에 이름을 붙혀주면 된다.

### 기본값 설정을 위한 @Value 어노테이션
  - 아래와 같이 @Component를 통해 객체가 생성될 때 @Value가 멤버 변수의 초기화 값을 전해주게 된다.
  ```java
    @Component
    public class NewlecExam implements Exam{
      @Value("20")
      private int kor;
      @Value("10")
      private int eng;
      @Value("30")
      private int math;
      @Value("40")
      private int com;
    }
    ```

## JAVA Configuration
  - 지금까지 Annotation을 이용해 xml을 대신하기 위해 xml의 코드를 하나씩 어노테이션으로 대체해 왔다. 하지만 xml과 어노테이션을 함께 사용해 왔는데 이를 완전히 어노테이션으로 바꾸기 위해서는 JAVA Configuration 객체가 필요하다.
  ``` Java
   @Component
    public class NewlecExam implements Exam{
      ~~~~~~~~
    }
  ```
  - @Component가 xml의 bean 태그를 대체하고 있지만 여전히 xml에는 여러 xmlns 코드와 <context:component-scan base-package:"클래스 패키지 경로"">등의 어노테이션과 연결할 코드가 있어야 어노테이션을 통한 객체 생성이 가능해진다.
  - 따라서 이것조차 완전히 대체하여 xml을 필요없게 하기 위해서 몇가지 조건이 필요하다.
  - 지금 xml 파일에 남아있는 것은 DI를 위한 설정 코드들과 bean 태그로 이루어진 몇몇 객체 생성 코드가 있다.  
  - 이것을 우리는 자바 클래스로 대체할 수 있다.

  ```java
  // context:component-scan태그를 대체
  @ComponentScan("spring.di.ui")
  // 해당 클래스가 xml처럼 설정을 담당하는 클래스임을 명시
  @Configuration
  public class NewlecAppConfig {
    // xml bean 태그 대신 원하는 객체를 생성한다는 의미의 어노테이션
      @Bean
      public Exam exam(){// 형식은 메서드를 정의하는 것 같지만 exam()은 메서드가 아니다. bean 객체가 생성되고 식별할 이름이 "exam"이 된다는 의미
        return new NewlecExam();  
      }
  }

  ```
  - 이렇게 우리가 사용할 Configuration 클래스가 완성되었다.
  - 이를 개발자가 객체화해서 가져올 수 있도록 해야한다.
  ```java
    public static void main(String[] args) {
        ApplicationContext context = new ClassPathXmlApplicationContxt(spring.di.ui.setting.xml);
         ----------↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓-------------------
        ApplicationContext context = new AnnotationConfigApplicationContext(NewlecAppCongfig.class);
        ExamConsole console = (ExamConsole) context.getBean("console");
        console.print();
    }
  ```
  - 이전에는 xml파일을 사용하였기에 xml파일을 찾아 컨테이너 객체를 만들 수 있었다.
  - xml의 위치에 따라 ClassPathXmlApplicationContxt/FileSystemXmlApplicationContext/XmlWebApplicationContext 등의 메서드를 선택하였다.
  - 하지만 xml이 어노테이션을 위한 config 클래스로 대체되었기에 AnnotationConfigApplicationContext의 객체를 생성해 그 기능을 이용할 수 있게 되었다.
  - 만약 어러개의 여러개의 config파일을 설정하고 싶다면 다음과 같은 형식으로 할 수 있다.
  ``` java
  public static void main(String[] args) {
      ApplicationContext context = new AnnotationConfigApplicationContext(NewlecAppCongfig.class);
      context.register(NewlecAppCongfig.class, OtherConfig.class);
      ExamConsole console = (ExamConsole) context.getBean("console");
      console.print();
  }
  ```
