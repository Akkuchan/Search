# XML 사용법
- 현재는 SPRING에서는 어노테이션을 이용해 DI를 구현하고 있다. 하지만 일부 오래된 프로젝트나 오픈소스들이 xml파일을 활용해 유지되기도 한다 또한 어노테이션 방법을 먼저 학습할 경우 몇몇 개념들이 머릿속에서 잘 그려지지 않아 이해에 어려움이 있기에 xml부터 어노테이션까지 SPRING DI 방법을 차례대로 발전과정을 따라서 이해도를 높이려고 한다.
## XML의 기본구조
  <img src="images\DI 구현 예시1.jpg" width 500px height 700px/>
  - 이전에 사용한 예시를 다시보면 Exam 인터페이스 타입의 객체를 직접 다른 부품(~ExamConsole의 클래스들)의 생성자 매개변수로 의존성을 주입하였다.
  - 하지만 이는 번거롭고 비효율적이다. 따라서 스프링에서는 이 DI를 대신할 XML 파일을 만들었다.
  - 기본 개념은 XML 파일에 DI할 클래스들간의 관계를 작성하여 xml파일을 통해 최종적으로 개발자가 이용할 결과물만 받게 한다는 것이다.
    - 쉽게 말해 직접 코드를 작성해 DI하는 것보다 따로 외부에서 작성할 수 있는, 분리된 xml파일 작성하는 방법이 더 쉽다는 의미이다.
  - Spring의 DI지시서(xml 파일)의 형식은 다음과 같다.
  ``` xml
  <?xml version="1.0" encoding="UTF-8"?>
    <beans xmlns="http://www.springframework.org/schema/beans"   
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"

    </bean>
  ```
  - 위의 xml 코드들은 다른 곳에서 긁어온 것이기에 의미는 없다. 그냥 대충 이런 모양이라는 것만 기억하자 실제로 현업에서 사용하는 Spring Boots와 차이가 존재한다. 참고만 할 것
  - 우리가 알아야 할 것은 위의 xml코드가 그림에서 main 메서드에 직접 작성한 DI코드들을 대신해준다는 것이다.
  - <bean>이라는 태그들 통해서 각 클래스의 인스턴스화를 대신할 수 있다.
  ``` xml
  // Exam exam = new NewlecExam();과 동일하다.
  <bean id= "exam" class="spring.di.entity.NewlecExam"/>
  <!-- ExamConsole console = new newExamConsole();과 동일하다.-->
  <bean id="console" class="spring.di.ui.newExamConsole">
      <!--console.setExam(exam);과 동일하다-->
      <property name="exam"  ref="exam"/><!--경우에 따라 value=""태그가 들어갈 수도 있다. 이는 아래서 따로 다룰 예정-->
  </bean>
  ```
  - id는 우리가 만들 객체의 이름을 의미하고 class는 인스턴스화할 클래스의 경로를 나타낸다. 상대경로로 NewlecExam라는 클래스 이름만 써도 되지만 클래스명이 중복될 수도 있기에 패키지 경로까지 써주는 것이 좋다.
### xml에서 DI를 하는 방법 2가지
  - Spring 개념 정리에서 설명한 것처럼 DI는 setter메서드를 통한 방법과 생성자를 통한 방법 2가지가 있었다.
  - 이따가 설명하겠지만 개발자는 xml을 통해 DI를 통한 객체 생성을 직접 안 할 수 있다.(일일이 DI 코드를 안 써도 된다는 의미)
  - 이 DI의 2가지 방법을 xml파일도 각각 구현할 수 있다.


#### setter()를 통한 DI
    ```xml
    <bean id="console" class="spring.di.ui.newExamConsole">
        //console.setExam(exam);과 동일하다
        <property name="exam"  ref="exam"/>// 경우에 따라 value=""태그가 들어갈 수도 있다. 이는 아래서 따로 다룰 예정
    </bean>
    ```
  - property는 <bean>으로 생성한 console이라는 객체의 멤버인 메서드 setExam을 호출하기 위한 태그이다.
    - name은 활용할 메서드명을 의미하는데 xml파일이 DI를 위한 메타데이터 파일인 것을 고려할 때 여기서 쓰이는 자바 메서드(setExam)는 설정을 위한 메서드인 것이다. 즉 setter 메서드라는 것. 따라서 "set"이라는 단어는 생략하며 대소문자를 고려하지 않기에 E -> e로한 exam만 기입해준다.
    - 사용할 메서드가 정해졌고 매개변수를 설정해야하는데 만약 매개변수가 기본 타입의 "값"이라면 value 태그를, 객체라면 해당 인스턴스의 이름을 적어준다.
      - 만약 멤버 변수인 int kor에 값을 50으로 설정하고 싶다면 아래와 같이 작성할 수 있다. 형식은 2가지로 쓰일 수 있다.
      ```Java
        <property name="kor"  value="50"/>
        //or
        <property name="kor">
          <value>10</value>
        </property>
      ```
      - 물론 선행되어야 할 것은 int kor를 주어진 값으로 초기화할 setter 메서드가 클래스에 정의되어 있어야 한다.(public int setKor(int kor){ this.kor=kor})

#### 생성자를 통한 DI
    ```xml
    <bean id="exam" class="spring.di.entity.NewlecExam">
        //console.setExam(exam);과 동일하다
        <Constructor-arg index="0" value="10"/>
        <Constructor-arg index="1" value="20"/>
        <Constructor-arg index="2" value="30"/>
        <Constructor-arg index="3" value="40"/>
    </bean>
    ```
    - Constructor-arg 태그를 통해 <bean>을 통해 생성되는 객체 생성시에 멤버 변수의 값들을 순서대로 초기화한다.
    - 순서에 따라 값이 초기화 되지만 초기화할 변수를 index 태그를 통해 초기화 순서를 표시할 수도 있고 "kor"와 같이 구체적인 변수명을 명시할 수 있다.
      - ex. <Constructor-arg index="kor" value="10"/>
    - 또한 변수의 타입까지 명시할 수 있다.
      - ex. <Constructor-arg index="eng" type="flaot" value="10"/>

    - 이렇게 Constructor-arg 태그를 통한 방법조차 번거롭다는 이유에서 개선 방법이 나오게 되었다.
      - namespace 설정(config)를 통해서 xmlns;p="http~~~~~"라는 코드를 추가할 수 있다.
      ```Java
      <bean id="exam" class="spring.di.entity.NewlecExam" p:kor="10" p:eng="10">
      ```
      - 이렇게 p라는 태그에 변수명과 초기화할 값만 넣어주면 주어진 값으로 초기화된 객체가 생성된다.

### 컬렉션 생성과 목록 DI
  ```Java
    List<Exam>  exams = new ArrayList<>();
    exams.add(new NelecExam(1,1,1,1));

    for(Exam e : exams)
      System.out.println(e);
  ```
  - 위의 코드를 xml에서 구현하려면 아래와 같이 작성한다.
  ```xml
  <!-- List<Exam>  exams = new ArrayList<>();과 동일한 코드 -->
  <bean id="console" class="java.util.ArrayList" />
    <Constructor-arg>
        <list>
            <bean class= "spring.di.entity.NewlecExam" p:kor="10" p:eng="10"/>
        </list>        
    </Constructor-arg>
  </bean>
  ```
  - 가장 바깥의 bean 태그는 컨테이너에서 ArrayList타입의 객체를 생성한다. 이 ArrayList의 내부 멤버는 list 타입으로 생성되는데 내부 bean 태그가 ArrayList의 멤버가 될 하나의 객체를 생성한다.
  - 이때 내부 bean 태그처럼 새로 멤버 객체를 생성하는 것이 아니라 기존에 컨테이너내에서 생성된 객체를 가져와 초기화하는 방법도 가능하다.
  ``` xml
  <bean id="console" class="java.util.ArrayList" />
    <Constructor-arg>
        <list>
            <ref bean="exam"/> <!-- 앞서 bean 태그를 통해 생성한 bean 객체 'exam'을 ArrayList의 멤버로 넣겠다는 의미 -->
        </list>        
    </Constructor-arg>
  </bean>
  ```

  - 이렇게 생성자를 통해서 컬렉션 bean 객체를 만드는 방법 이외에 다른 방법으로 컬렉션 bean을 만드는 방법도 있다.
  - Namespace config에서 util 기능을 활용하면 된다.
  ```xml
    xmlns:util="http~~~~ blah blah" <!-- 해당 코드를 추가해 아래 코드를 사용할 수 있도록 웹에서 소스를 가져온다는 의미 -->
    <util:list id="exams" list-class="java.util.ArrayList">
        <bean class="spring.di.entity.NewlecExam" p:kor="10" p:eng="10"/>
    </util:list>  
  ```
  - 위 코드는 그 위 코드와 동일한 기능을 수행한다. 보다 간결하게 코드를 작성할 수 있게 된다.


## Spring DI 컨테이너
  - Spring DI 컨테이너가 관리하는 객체를 빈(bean)이 빈들을 관리한다는 의미로 컨테이너를 빈 팩토리(BeanFactory)라고 부른다.
  - 객체의 생성과 객체 사이의 런타임(run-time)관계를 Di 관점에서 볼 때는 컨테이너를 BeanFactory라고 한다.
  - 이 BeanFactory에 여러가지 컨테이너 기능을 추가한 인터페이스로 ApplicationContext가 존재하고 사용방법에 따라 인스턴스화할 구현 클래스가 나뉘어진다.
    1. ClassPathXmlApplicationContxt 클래스
    - 어플리케이션(xml파일)의 위치를 매개변수로 삼을 때는 ClassPathXmlApplicationContxt의 객체 사용
      - ex. ApplicationContext context = new ClassPathXmlApplicationContxt;
    2. FileSystemXmlApplicationContext 클래스
      - ex. ApplicationContext context = new FileSystemXmlApplicationContext(C:USER/...~~~~)
    3. XmlWebApplicationContext 클래스
    - xml파일이 웹상에 위치할 때 매개변수로 url을 넣는다.
    4. AnnotationConfigApplicationContext 클래스
    - java 소스코드에 직접 어노테이션을 작성하고 이를 통해 DI 실행한다

    ```Java
    ApplicationContext context = new ClassPathXmlApplicationContxt("spring/di/setting.xml")
    ExamConsole console = context.getbean(ExamConsole.class)
    ```
    - 이렇게 ApplicationContext 타입으로 만들어진 객체(이름은 context라 하겠다)는 **xml파일을 통해 만들어진, 서로 의존성을 가지는 객체들이 담겨있는 컨테이너를 참조한다.**
    - getbean()를 통해서 컨테이너에 존재하는 ExamConsole 클래스 타입의 객체를 가져올 수 있다.  
