# Pagenation(페이지네이션)이란?
  - 게시판에서 한 페이지 당 일정한 양의 게시글이 화면에 나오고 page별로 나누어 사용자가 볼 수 있도록 게시글 데이터를 정렬해 놓는 것을 페이지네이션이라고 한다.

## Pagenation의 방법
  - 페이지네이션의 방법에는 2가지가 있다.
    - 1. Spring Data JPA를 활용한 방법
    - 2. Spring Data JDBC를 활용한 방법
  - 2가지 모두 동일하게 페이징이 가능하다. 우선 2번 방법은 추후에 확인하고 Spring Data-JPA를 활용해 페이지네이션을 구현해보자

### Spring Data JPA를 활용한 Pagenation.
  1. 우선 게시글 데이터의 게시글처럼 DB에서 가져올 객체를 가정한다

    ``` java
    @Entity
    public class Product {

        @Id
        private long id;
        private String name;
        private double price;
        // constructors, getters and setters
    }
    ```
    2. Pagenation을 위한 Repository 만들기
    ```java
    public interface ProductRepository extends PagingAndSortingRepository<Product, Integer> {
        List<Product> findAllByPrice(double price, Pageable pageable);
    }
    ```
      - 페이지네시션을 위한 레포지토리 ProductRepository를 만들었다.
      - 해당 레포지토리는 PagingAndSortingRepository이라는 레포지토리 인터페이스를 상속받고 있다.
      - 때문에 해당 조상 인터페이스의 findAll(Pageable pageable)와 findAll(Sort sort)를 사용할 수 있게 되었다.
        - 해당 인터페이스에는 이 2개 메서드만 선언되어 있다.
        - 또한 PagingAndSortingRepository는 CrudRepository를 또 상속하고 있기에 CrudRepository의 메서드들을 사용할 수 있다.
        - 현재 선언되어 있는 findAllByPrice 메서드는 이름에서 알 수 있듯이 가격을 조건으로 DB에서 데이터를 가져올 수 있는 기능의 메서드이다.
          - PagingAndSortingRepository를 확장할 경우 이처럼 findAll()의 매개변수로 Sort와 Pageable을 함께 사용하여 가져올 데이터들을 필터링 할 수 있다.

    3. 페이지네이션 구현하기
      - 페이지네이션을 하려면 그전에 DB에서 우리는 데이터들을 가져와야 한다.(findAll(Pageable pageable))
        - findAll(Sort sort)은 타입이 Iterable<T>이기 때문이다.
        - Page<T> findAll(Pageable pageable);
        ※ Pageable 인터페이스에 대해서는 따로 정리할 예정.
      - 이를 위해선 매개변수로 들어갈 Pageble 타입의 객체를 만들어야 한다.
      - Pageble의 객체 생성에는 Pageable ofSize(int pageSize)이 사용된다.
        - 더 정확히 말하자면 ofSize의 리턴값이 PageRequest.of(0, pageSize)인데 이 PageRequest는 Pageable(인터페이스)-AbstractPageRequest(추상클래스)-PageRequest(클래스)로 상속관계가 연결된 구조이다. 즉 Pageble을 구현한 것이 PageRequest.
      - 어떤 메소드를 사용할지 알았으니 객체를 생성해보자

      ```java
      Pageable firstPageWithTwoElements = PageRequest.of(0, 2);  
      Pageable secondPageWithFiveElements = PageRequest.of(1, 5);
      ```  
      - 첫번째는 데이터를 2개씩 나누고 그 중 첫 페이지의 값들 가져오라는 요청(PageRequest) 객체
      - 두번째는 데이터를 5개씩 나누고 그 중 두번째 페이의 값들을 가져오라는 요청(PageRequest) 객체이다.
      - 요청을 전달할 객체를 매개변수로 하여 우리가 원하는 DB의 데이터들을 가져오자.
      ```java
      Page<Product> allProducts = productRepository.findAll(firstPageWithTwoElements);
      List<Product> allTenDollarProducts =
        productRepository.findAllByPrice(10, secondPageWithFiveElements);
      ```  
      - findAll()의 기본 리턴타입은 Page<T>이다. 원한다면 사용자가 Page<T>, a Slice<T>, or a List<T> 중에서 원하는 타입으로 커스텀해 paginated data를 가져올 수도 있다.
        - 차이를 간단히만 설명하면 Page<T> 객체 생성시 DB의 값을 전부 확인하는 작업을 거치기에 전체를 count하는 기능이 들어간다. 만약 이러한 기능을 소모하고 싶지 않다면(Overhead cost) Slice<T> or a List<T>를 사용하면 된다.
      - 첫줄은 findAll을 통해 모든 DB값을 확인하고 그중 페이징 조건(firstPageWithTwoElements)에 해당하는 값들을 Page<Product>으로 담았다.
      - 다음 줄은 페이징 조건 + 가격(10)이라는 조건을 추가로 주어 원하는 값을 가져왔고 이를 List<Product> 타입으로 담아왔다.(Sort sort, Pageble pagable)
      - 이로서 DB와의 통신이 마무리 되었다.

    4. 페이지네이션과 Sorting
      - 위의 내용에서 조금 확장 하여 findAll와 같은 메서드의 매개변수 값에 추가로 조건을 넣어 정렬기능을 넣은 값들을 가져올 수도 있다.
        - 예를 들어 최신순으로 게시글을 정렬하면 20-19-18 역순으로 데이터들을 클라이언트에게 보여주어야 하지만 실제 DB의 게시판(테이블)의 게시글들은 오름차순일 것이다. 즉 데이터들을 요청할 때 아예 오름차순으로 달라고 하자는 것.
      ```java
        Page<Product> allProductsSortedByName = productRepository.findAll(Sort.by("name"));
      ```
      - 단순하게 어떤 조건(필터링)없이 값을 다 가져오고자 한다면 productRepository의 findAll의 매개변수로 정렬 조건(Sort.by("name"))만 넣어주면 된다.
      - 만약 정렬 조건 + 페이징조건을 함께 적용한다면 이를 포함한 요청객체를 만들어주면 된다.
      ```java
      Pageable sortedByName =
        PageRequest.of(0, 3, Sort.by("name"));
      Pageable sortedByPriceDesc =
        PageRequest.of(0, 3, Sort.by("price").descending());
      Pageable sortedByPriceDescNameAsc =
        PageRequest.of(0, 5, Sort.by("price").descending().and(Sort.by("name")));
      ```
      - PageRequest.of의 3번째 매개변수로 정렬 조건을 넣을 수 있다.
      - .descending()를 통해 내림차순으로 데이터를 요청할 수 있다.
      - 또한 마지막 처럼 여러개의 정렬 조건을 적용할 수도 있다.(가격과 이름으로 정렬)
