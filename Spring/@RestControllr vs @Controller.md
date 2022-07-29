# @RestController VS @Controller
- 스프링 MVC 패턴에서 컨트롤러를 지정하고 AOP를 설정해주는 어노테이션들이다.

## @Controller
- 컨트롤러를 설정하는 기본 어노테이션
- @RestController가 나오기 전까지 사용하였다.
- 실질적인 비즈니스 로직은 Service 계층에서 담당하지만 컨트롤러는 Handler Mapping에 따라
  1. request에 알맞는 Handler Method를 제공
  2. response를 Dispatcher Servlet에게 반환, DS가 이에 맞는 View를 반환하게 한다.

### @Controller의 특징
- 만약 반환되는 response가 "문자열"과 같은 단순값이 아닌 Data를 반환할 경우 이를 처리하기 위해 @ResponseBody를 활용하여 이를 Json 형식(혹은 XML 형식)으로 변환하여 반환하게 한다.
  ```java
  @Controller
  public class Controller {

    @GetMapping("/setmenu1")
        public @ResponseBody Collection setMenu1() {
            return List<Dishes> setMenu1;//"admin"은 Mustach에 의하여 동일명의 .html로 반환된다.
        }
      }
  ```
  ```
  setMenu1 { "Steak" : 1, "Soup" : 1, "Desert" : 1 }
  ```

- @Controller는 글자 그대로 컨트롤러 임을 명시하기에 Handler Method를 매핑하는데 쓰이는 @RequestMapping과 함께 사용하는 것이 주요 특징이다.

```java
@Controller
@RequestMapping("books")
public class SimpleBookController {

    @GetMapping("/{id}", produces = "application/json")
    public @ResponseBody Book getBook(@PathVariable int id) {
        return findBookById(id);
    }

    private Book findBookById(int id) {
        // ...
    }
```
<img src="../images/스프링 mvc구조.png" width="400px" height = "250px" / >
출처: "www.facebook.com/photo/?fbid=1873079892846068&set=gm.3389524044492534"

- Json 객체로 반환하기 위해 @ResponseBody를 사용한다.

## @RestController
  - SpringBoot 4이전까지는 이러한 방식을 통해 Json response를 다루었다.
  - 하지만 모든 Handler 매서드에 @ResponseBody 어노테이션을 작성해야 했기에 메서드 수많은 코드 중복이 발생하였다.
  - @RestController는 이러한 중복을 줄여준다.

### @RestController의 특징
  - 간단하게 @Controller와 @ResponseBody를 합친 것이라고 생각하면된다.
  - Json 객체를 보다 쉽게 반환하게 이해 사용한다.
  - 위의 setMenu1()예시에서 Controller 클래스단에 @RestController만 작성한다면 따로 @ResponseBody를 쓸 필요가 없다.

  ```java
  @RestController
  public class Controller {

    @GetMapping("/setmenu1")
        public @ResponseBody Collection setMenu1() {
            return List<Dishes> setMenu1;//"admin"은 Mustach에 의하여 동일명의 .html로 반환된다.
        }
      }
  ```

#### @RestController 사용시 주의점
  - 해당 어노테이션을 사용하면 @ResponseBody를 사용하지 않아도 Response를 Json으로 알아서 변환해 response할 것이다.
  - 하지만 주의할 점이 있다.
  - Json으로 반환하더라도 객체 그대로를 반환하기에 Http 프로토콜에 따라 응답에 맞는 HttpStatus를 설정해줄 수가 없다.
  - 따라서 응답 객체에 맞는 HttpStatus를 ResponseEntity로 감싸 반환해 줄 필요가 있다.

  ```java
  @RestController
  @RequestMapping("/admin")
  @RequiredArgsConstructor
  public class AdminController {

      private final AdminService adminService;

      @GetMapping(value = "/admins")
      public User findadmin(@RequestParam("adminName") String adminName){
          return adminService.findAdmin(admin);
      }

      @GetMapping(value = "/admins")
      public ResponseEntity<Admin> findAdminWithResponseEntity(@RequestParam("adminName") String adminName){
          return ResponseEntity.ok(adminervice.findAdmin(admin));
      }
  }
  ```





## 정리
  - @Controller는 View를 반환해야 할 경우에, @RestController는 Json 형식으로 반환해야할 경우에 적합한 어노테이션 설정이다.
  - 따라서 RESTFUL 프로젝트를 만들 경우 @RestController를 사용하는 것이 보다 바람직 할 것이다.


### 참고자료
  - https://www.baeldung.com/spring-controller-vs-restcontroller
  - https://dncjf64.tistory.com/288
  - 출처: https://mangkyu.tistory.com/49 [MangKyu's Diary:티스토리]
