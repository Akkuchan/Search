
 <img src="../images\스프링 프레임워크의 구조.png" width="400px" height = "300px" />

## DAO
  - Data Access Object의 약자
  - repository package
  - 실제로 DB에 접근하는 객체
  - Service와 DB를 연결하는 고리역할
  - SQL을 사용해(개발자가 직접 코딩) DB에 접근한 후 적절한 CRUD API를 제공한다.
    - JPA 대부분의 기본적이 CRUD 메서드를 제공한다.
    - extends JpaRepository<User, Long>
    ```java
    public intergace QuestionRepository extends CrudRepository<Question, Long>{

    }
    ```
  - Object 단위 -> SQL을 이용한 CRUD -> DB의  Record 단위로 저장되는 순서

## DTO
  - Data Transfer Object
  - dto package
  - 계층간의 데이터 교환을 위한 객체(JAVA Beans)이다
    - DB에서 데이터를 얻어 Service나 Controller 등으로 보낼 때 이를 담는 객체
    - 즉 DB의 데이터가 Presentation Logic Tier로 넘어오게 될 때는 DTO의 못브으로 바뀌어 오고가는 것
    - 로직이 없는 순수한 데이터 그 자체인 객체로 getter, setter 메서드만 있다.
    - 다만 DB에서 꺼낸 값을 임의로 변경은 불가능하기에 DTO 클래스에는 setter가 없다.(따라서 생성자로 값을 할당)
  - Request와 Response용 DTO는 View를 위한 클래스
    - 자주 변경이 필요한 클래스
    - Presentation Model
    - toEntity() 메서드로 DTO에서 필요한 부분을 이용하여 Entity로 만든다.
    - 또한 Controller layer에서 Response DTO 형태로 클라이언트에ㅐ서 전달한다.


## Entity Class란
    - domain package

    - 실제 DB의 테이블과 매칭될 클래스
    - 즉, 테이블과 링크될 클래스임을 나타낸다.
    - Entity 클래스 또는 가장 Core한 클래스라고 부른다.
    - @Entity, @Column, @Id 등을 이용
    - 최대한 외부에서 Entity 클래스의 getter method를 사용하지 않도록 해당 클래스 안에서 필요한 로직 method을 구현한다.
    - 단, Domain Logic만 가지고 있어야 하고 Presentation Logic을 가지고 있어서는 안된다.
    - 여기서 구현한 method는 주로 Service Layer에서 사용한다.
    - 참고! Entity 클래스와 DTO 클래스를 분리하는 이유
    - View Layer와 DB Layer의 역할을 철저하게 분리하기 위해서 테이블과 매핑되는 Entity 클래스가 변경되면 여러 클래스에 영향을 끼치게 되는 반면 View와 통신하는 DTO 클래스(Request / Response 클래스)는 자주 변경되므로 분리해야 한다.
    - Domain Model을 아무리 잘 설계했다고 해도 각 View 내에서 Domain Model의 getter만을 이용해서 원하는 정보를 표시하기가 어려운 경우가 종종 있다. 이런 경우 Domain Model 내에 Presentation을 위한 필드나 로직을 추가하게 되는데, 이러한 방식이 모델링의 순수성을 깨고 Domain Model 객체를 망가뜨리게 된다.
    - 또한 Domain Model을 복잡하게 조합한 형태의 Presentation 요구사항들이 있기 때문에 Domain Model을 직접 사용하는 것은 어렵다.
    - 즉 DTO는 Domain Model을 복사한 형태로, 다양한 Presentation Logic을 추가한 정도로 사용하며 Domain Model 객체는 Persistent만을 위해서 사용한다.
    https://gmlwjd9405.github.io/2018/12/25/difference-dao-dto-entity.html
