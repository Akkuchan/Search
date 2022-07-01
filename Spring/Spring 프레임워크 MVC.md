# MVC 패턴
 - 어플리케이션을 크게 모델(Model, View, Controller) 세 영역으로 구분하고 영역간의 결합도를 최소화한 패턴
  - 패턴: 어떤 작업을 하는데 있어 수많은 개발자들이 공통되게 코드를 작성하는 방식

 <img src="../images\MVC.png" width ="400px" height="300px" />

## Model
  - 프로그램 실행에 직접 관여하여 작업하는 것이 아닌, 데이터를 담고 DB로 데이터를 보내는 작업에 쓰이는 객체를 말한다.
    - 어플이케이션의 상태를 저장하고, View에서 요청하는 상태질의에 응답하고, 컨트롤러에서 알려주는 상태, 환경의 변화에 맞게 Model의 것도 변화시키고 이를 다시 View에서 알려주는 역할을 한다.
  - DTO를 통해 상태 값을 주고 박으며, 영속 저장된 데이터왕의 통신을 위해 DTO를 사용한다.

## View
  - 모델을 렌더링해 실제 사용자에게 보여지는 부분을 이야기한다.
  - 모델의 변화된 상태를 요청하여 업데이트 한다.
  - 뷰에서 사용자가 하는 행위들을 컨트롤러에게 전달한다.
  - 컨트롤러가 선택한 뷰에 접근할 수 있도록 허가한다.

## Controller
  - 뷰에 대한 하나의 진입점으로 어플리케이션의 행위를 정의해둔 영역을 말한다.
  - 특정 결과에 대해 뷰를 성택하여 응답하는 역할

### MVC 활용 예시
    1. 사용자 등록을 위해 HTML 페이지 작성 - View 영역,
      - Ex. 파일이름 : Round10_Resiter.html
    2. HTML 페이지에서 넘어오는 데이터를 저장하는 DTO 클래스를 작성한다. - Model 영역
      - Ex. 파일이름 : Round10_MemberDTO.java
    3. 사용자 정보를 관리하기 위해 DBMS의 테이블을 생성한다 - 물리적 저장 영역(Oracle, mysql 등의 서버에 생성)
      -  Ex. 파일이름 : Round10_Member,  필드이름: Num, Name, Tel, Addr
    4. DTO에서 넘어온 데이터를 실제 DBMS에 저장하는 작업을 처리하는 DAO클래스를 작성한다. - Model 영역
      -  Ex. 파일이름 : Round10_MemberDAO.java
    5. 사용자가 HTML 페이지에 입력한 정보를 DTO에 담아 그것을 DAO 클래스의 관련 메서드에 전송하는 서블릿 클래스를 작성한다. - Controller 영역
    6. 처리 결과의 성공과 실패 여부를 출력하는 뷰 페이지를 작성한다. - View영역
      -  Ex. 파일이름 : Round10_Success.html

## 스프링에서 지원하는 MVC의 특징
  - 모델2 아키텍처 지원
  - 스프링과 다른 모듈과의 연계가 용이
  - 타일즈(tiles)나 사이트메시(sitemesh) 같은 View 기술과의 연계가 용이
  - 태그 라이브러리를 통해 message 출력, theme 적용 그리고 입력 폼을 보다 쉽게 구현할 수 있음.

## 스프링 프레임워크의 MVC 구조도 및 구성요소
<img src="../images\MVC패턴.png" width ="400px" height="300px" />

  1. 브라우저가 DispatcherServlet에 URL로 접근하여 해당정보를 요청한다.
  2. 핸들러 매핑에서 해당 요청에 대해 매핑된 컨트롤러가 있는지 요청
  3. 매핑된 컨트롤러에 대해 처리를 요청
  4. 컨트롤러가 클라이언트의 요청을 처이한 결과와 View 이름을 ModelAndView에 저장해서 DispatcherServlet으로 반환
  5. DispatcherServlet에서는 컨트롤러에서 보내온 View 이름을 ViewResolver로 보내 해당 View를 요청한다.
  6. ViewResolver는 요청한 View를 보낸다.
  7. View의 처리결과를 DispatcherServlet으로 보낸다
  8. DispatcherServlet은 최종 결과를 브라우저로 전송


  | 구성 요소 | 설명 |
  |:---|:---|
  | `DispatcherServlet` | 클라이언트의 요청을 전달받아 해당 요청에 대한 컨트롤러를 선택하여 클라이언트의 요청을 전달한다. 또한 컨트롤러가 반환한 값을 View에 전달하여 알맞은 응답을 생성한다. |
  | `HandlerMapping` | 클라이언트가 요청한 URL을 처리할 컨트롤러를 지정한다 |
  | `Controller` | 클라이언트의 요청을 처리한 후 그 결과를 DispatcherServlet에 전달한다. |
  | `ModelAndView` | 컨트롤러가 처리한 결과 및 뷰 선택에 필요한 정보를 저장한다. |
  | `ViewResolver` | 컨트롤러의 처리결과를 전달할 뷰를 지정한다.|
  | `View` | 컨트롤러의 처리결과 화면을 생성한다. |


  ※ 실습관련 용어
  | 파일 | 설명 |
  |:---|:---|
  | `web.xml` | 브라우저에서 *.do로 요청 시 스프링의 DispatcherServlet 클래스가 요청을 받을 수 있게 서블릿 매핑을 합니다. | `action-servlet.xml` | 스프링 프레임워크에서 필요한 빈들을 설정합니다. |
  | `SimpleUrlController.java` | 매핑된 요청에 대해 컨트롤러의 기능을 수행합니다. |
  | `index.jsp` | 요청에 대해 컨트롤러가 브라우저로 전송하는 JSP입니다. |  
