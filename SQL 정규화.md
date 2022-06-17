# 정규화
  - 정규화를 쉽게 말하자면 데이터의 중복을 최소화하고 주제별로 나누는 프로세스라고 할 수 있다.
  - 중복 제거, 분할을 통해 데이터를 한군데에 몰아 저장함으로써 발생가능한 문제들을 방지하는 것이 정규화의 목적이다.

## 정규화의 단계
  - 크게 3가지로 볼 수 있다.

### 1정규화(1 Normal Form)
  - 테이블의 컬럼이 원자값(즉 하나의 값만을 가지도록)을 갖도록 테이블을 나누는 것
  ``` sql
  CREATE TABLE order_table(
	주문번호 INTEGER NOT NULL,
	날짜 DATE,
	고객이름 VARCHAR(10),
	연락처 VARCHAR(11),
	주문상품 VARCHAR(100)
	);

INSERT INTO order_table VALUES(1, "2020-01-01", "박상민", "01012345678", "0001 OO 1개, 0002 XX 10개");
INSERT INTO order_table VALUES(2, "2020-01-02", "강윤석", "01011112222", "0001 OO 2개, 0002 XX 3개");
INSERT INTO order_table VALUES(3, "2020-02-01", "구본효", "01022223333", "0001 OO 3개, 0003 YY 1개");
```
  - 위 SQL문으로 테이블을 생성하면 주문상품의 값은 "0001 OO 1개, 0002 XX 10개"처럼 여러개의 값이 한칸에 저장된다.(원자성 위배)
  - 이를 해결하기 위해 여러 값들을 각각의 컬럼에 저장되도록 나누어야 한다.
``` SQL
CREATE TABLE order_list CREATE TABLE order_list SELECT 주문상품,
	SUBSTRING_INDEX(주문상품, ', ', 1) AS 상품,
	SUBSTRING_INDEX(주문상품, ' ', 1) AS 상품코드,
	SUBSTRING_INDEX(SUBSTRING_INDEX(주문상품,' ', 2), ' ', -1) AS 상품명,
	SUBSTRING_INDEX(SUBSTRING_INDEX(SUBSTRING_INDEX(주문상품, ' ', 3), '개', 1),' ',-1) AS 구매개수
	FROM order_table

UNION

SELECT 주문상품,
	SUBSTRING_INDEX(주문상품, ', ', -1) AS 상품,
	SUBSTRING_INDEX(SUBSTRING_INDEX(주문상품, ', ', -1), ' ', 1) AS 상품코드,
	SUBSTRING_INDEX(SUBSTRING_INDEX(SUBSTRING_INDEX(주문상품,', ', -1), ' ', 2), ' ', -1) AS 상품명,
	SUBSTRING_INDEX(SUBSTRING_INDEX(SUBSTRING_INDEX(주문상품, ', ', -1), ' ', -1), '개',1) AS 구매개수
	FROM order_table;
```
  -
