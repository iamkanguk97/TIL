# MySQL BOOLEAN AND TINYINT

> **회사에서 테이블의 상태 필드에 대해서 TINYINT 컬럼 타입을 적용하고 있었다. 나는 CHAR(1) 또는 VARCHAR로 "ACTIVE와 INACTIVE"를 사용하고 있었는데, TINYINT가 정확히 무엇이고 어떤 방법이 더 좋을지 고민하면서 작성해보려고 한다. 그리고 BOOLEAN 타입을 대체할 수 있는 컬럼 타입이 어떤건지를 한번 고민해보자.**

## BOOLEAN 컬럼

<img src="https://github.com/user-attachments/assets/1dc01e22-2865-46cd-b535-fdcda01d1469" />

- BOOLEAN 컬럼 타입을 사용해서 테이블을 생성하게 되면 `TINYINT(1)`로 컬럼이 생성된다. 그리고 BOOLEAN 타입의 컬럼에 값을 저장하거나 조회할 때는 `TRUE/FALSE` 또는 `0/1`을 사용한다.
- 다시 말해서, MySQL은 Boolean Type을 지원하는 Built-in 기능이 없다는 것이다.
- 그리고 WHERE 조건절에 BOOLEAN 타입 컬럼을 사용하는 경우에는 TRUE/FALSE 또는 0/1로 조건을 걸어주어야 한다. 그렇지 않으면 원하는 결과값이 나오지 않을 수 있다.

## 데이터 삽입

- `enabled` 컬럼에 0,1 또는 true/false 값 외에 다른 숫자값을 삽입하게 되면 들어가는 것을 확인할 수 있다.
- **그렇기 때문에 Boolean 타입 사용시에는 반드시 주의가 필요하다!**

## TINYINT

- 8Bit, (un)signed integer 데이터 타입이다.
- 즉, 정수값이고 1Byte(8Bit)의 고정 크기를 가진다.
- 실제로 MySQL에서 Boolean 필드를 선언할 때 TINYINT 타입을 사용하는 것이 좋다고 한다.
- 그리고 MySQL 5.7 버전 이후로 BOOLEAN 타입으로 지정하면 자동으로 TINYINT로 처리된다.

## 결론

- MySQL 기준 TINYINT가 권장되고, BOOLEAN이나 BOOL로 타입을 지정해줄 수 있다. 그렇지만 내부적으로는 TINYINT를 사용한다.

---

### Reference

- [MySQL BOOLEAN vs TINYINT](https://velog.io/@gagaeun/MySQL-BOOLEAN-vs-TINYINT)
- [TINYINT와 BIT 차이 그리고 BOOLEAN](https://unluckyjung.github.io/db/2023/06/18/tinyint-vs-bit/)
- [MySQL BOOLEAN 컬럼](https://medium.com/daangn/mysql-boolean-%EC%BB%AC%EB%9F%BC-7abd9b35c664)
