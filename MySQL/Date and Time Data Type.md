# MySQL에서의 Date와 Time Data Type 문법

MySQL에서 날짜와 시간을 나타내는 데이터 타입은 크게 `DATE`, `TIME`, `DATETIME`, `TIMESTAMP` 그리고 `YEAR`가 있다.
참고로, `TIME`, `DATETIME`, `TIMESTAMP`는 최대 마이크로세컨드(6자리) 까지 허용하고 표기는 `type_name(fsp)` 형태로 표기한다. type_name은 TIME, DATETIME, TIMESTAMP 중 하나로 정의할 수 있고 fsp는 소수 초 정밀도라고 생각하자. fsp는 그러면 0부터 최대 6까지 표기할 수 있을 것이다. fsp는 생략하면 기본값으로 0이 세팅되는데 버전에 따라 6으로 세팅되는 경우도 있다고 하니 참고하자.

## TIMESTAMP와 DATETIME에서 Automatic initialization and updating properties

> [13.2.5 Automatic Initialization and Updating for TIMESTAMP and DATETIME](https://dev.mysql.com/doc/refman/8.4/en/timestamp-initialization.html)

-   MySQL 공식문서에서는 TIMESTAMP와 DATETIME 컬럼은 테이블에서 현재의 TIMESTAMP를 Default로 할당할 수 있고 자동 UPDATE도 가능하다고 표기해놨다.

## DATE

-   `1000-01-01` 부터 `9999-12-31` 까지 커버가 가능하다.
-   `YYYY-MM-DD` 형태로 저장한다.
-   하지만 다른 string 또는 number 형태의 데이터들도 허용한다.

## DATETIME(fsp)

-   DATE와 TIME의 조합이다.
-   `1000-01-01 00:00:00.000000` 부터 `9999-12-31 23:59:59.499999` 까지 커버가 가능하다.
-   `YYYY-MM-DD hh:mm:ss[.fraction]` 형태로 저장한다.
-   DATE 타입과 마찬가지로 다른 string 또는 number 형태의 데이터들도 허용한다.
-   fsp는 optional value로 0부터 6까지 할당할 수 있다. 기본값은 0이다.
-   `DEFAULT`와 `ON UPDATE`로 Automatic Initialization과 Update를 할 수 있다.

## TIMESTAMP(fsp)

-   `1970-01-01 00:00:01.000000 UTC` 부터 `2038-01-19 03:14:07.499999 UTC` 까지 커버가 가능하다.
