
# Http의 Content-type
- OAth2.0을 통해 구글 로그인을 구현하던 "Content type 'application/x-www-form-urlencoded;charset=UTF-8' not supported"를 마주하였다.
- 찾아보니 내가 응답으로 보내려는 객체의 내부 값을 따로 호출해 Html의 <form> 태그와 함께 사용하려고 하였다.
- 하지만 HTML을 사용하기 위해 응답은 Json이 아닌 x-www-form-urlencoded 타입으로 반환된다는 것을 알게되었다.

## Http의 Content-type
- Content-type이란 Http Header에 포함되는 값중 하나이다.
- 클라이언트에게 응답으로 전달되는(반환되는) 컨턴츠의 유형이 무엇인지를 명시한다.
- 어렵게 말하면 리소스의 media type을 나타낸다.
  - MIME type 이라고도 불리며 현재는 media type이라고 부른다. 파일의 형식을 나타내는 문자열로 컨텐츠의 형식을 나타낸다. 파일의 확장자명(.png, .xmsl)과 같은 역할로 이해하면 된다.
- POST, PUT 등의 HTTP 요청에서 서버가 어떤 유형의 데이터를 클라이언트에게 주었는지 알 수 있게 해준다.   

  ```
  Content-Type: text/html; charset=utf-8
  Content-Type: multipart/form-data; boundary=something
  ```
## Content-type의 종류
- 종류는 크게
  1. Mutipart Related 타입
  2. XMl Media 타입
  3. Application 타입
  4. 오디오 타입
  5. Multipart 타입
  6. TEXT 타입
  7. file 타입
  이 있다.
- 각 타입별로 다양한 타입이 존재하기에 세부 타입은 필요할 때 살펴보면 될 것 같다.
  출처: https://iamawebdeveloper.tistory.com/88

### 주요 Content-type
- 수많은 타입을 하나씩 볼 수는 없기에 위의 에러코드를 참고해 2가지만 체크해보자

#### Application/json
  - json은 자바스크립트의 객체 표기법에서 파생되었기에 유ㅏ한 형태를 지닌다.
  - Json 데이터는 다음 규칙에 따라 작성된다.
    1. 기본적으로 이름과 값이라는 2가지 구조로 이루어진다.
    2. 여러 값을 나열할 때는 ','를 사용한다.
    3. 객체는 중괄호({})로 표시한다.
    4. 배열은 대괄호([])로 표시한다.
    5. 마지막에는 콜론으로(;)로 마무리한다.
  - 내부 값으로는 다음의 값 유형이 올 수 있다.
    1. 숫자(number)
    2. 문자열(string)
    3. 불리언(boolean)
    4. 객체(object)
    5. 배열(array)
    6. NULL
  ```java
  {
      "name": "식빵",
      "family": "웰시코기",
      "age": 1,
      "weight": 2.14
  }
  "dog": [
    {"name": "식빵", "family": "웰시코기", "age": 1, "weight": 2.14},
    {"name": "콩콩", "family": "포메라니안", "age": 3, "weight": 2.5},
    {"name": "젤리", "family": "푸들", "age": 7, "weight": 3.1}
]
  ```

- Application/x-www-form-urlencode
  - 해당 형식의 모양은 키와 값이 '='로 연결되고 각 항목ㅇ느 &로 연결되는 형태이다.
  ```
    page=hundred&book=nottinghill
  ```
- 이를 Http Post 요청의 응답으로 body에 담아 보내면  
