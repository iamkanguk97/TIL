# JSON_ARRAY 메서드와 JSON_ARRAYAGG 메서드의 차이점

> 회사에서는 여러 개의 ROW에 대해서 JSON_ARRAY와 JSON_ARRAYAGG 등 다양한 MySQL 내장 함수를 사용하고 있다. JSON_ARRAY와 JSON_ARRAYAGG 메서드의 차이점에 대해서 궁금해져서 오늘 한번 정리해보려고 한다.

## JSON_ARRAY

> 문자열을 JSON_ARRAY 타입으로 변환해서 사용할 수 있는 내장 함수입니다. 키가 필요없고 값만 나열해준다면 바로 사용이 가능하다.

```sql
SELECT JSON_ARRAY(1, "abc", NULL, TRUE, CURTIME());   # [1, "abc", null, true, "10:11:32"]
```

## JSON_ARRAYAGG

> -   JSON_ARRAYAGG는 공식문서 상에서 Aggregate Function(집계함수) 으로 분류된다. 보통 GROUP BY와 함께 사용하는게 좋다 (사용해야 한다?)
> -   마찬가지로 단일 배열로 결과값을 반환한다.

```sql
SELECT o_id, attribute, value FROM t3;

# o_id / attribute / value
# 2 / color / red
# 2 / fabric / silk
# 3 / color / green
# 3 / shape / square

SELECT o_id, JSON_ARRAYAGG(attribute) AS attributes FROM t3 GROUP BY o_id;

# o_id / attributes
# 2 / ["color", "fabric"]
# 3 / ["color", "shape"]
```

위의 예제와 같이 사용할 수 있다.

## 발생한 이슈

### 첫번째 쿼리

> SELECT 되어있는 쿼리문들 중 한 줄을 발췌했습니다. 특정 컬럼을 통해 GROUP BY가 적용되어 있다고 가정하겠습니다.

```sql
IF(t3.temp IS NULL, null, JSON_ARRAYAGG(t3.temp)) AS temp
```

-   temp라는 값이 만약에 ROW로 여러 개의 값이 올 수 있다면 아마 다음과 같이 결과값이 나올 것이다.
-   temp가 null일 경우에는 null을, 그렇지 않은 경우에는 JSON_ARRAYAGG를 적용한다는 것이다.

```text
// GROUPBY가 적용된 컬럼 / 결과값
// typeA / ["0123", "0123", "0124", "..."]
// typeB / ["1234", "1234", "5678", "..."]
```

정상적으로 값이 잘 들어온다.

### 두번째 쿼리

```sql
IF(t3.temp IS NULL, '[]', JSON_ARRAYAGG(t3.temp)) AS temp
```

-   IF 함수의 반환 타입은 모든 반환 값의 공통 타입으로 결정된다. 그래서 null과 JSON_ARRAYAGG는 JSON 타입이지만, '[]'은 문자열 타입이다.
-   그래서 IF 함수는 문자열 타입으로 반환되기 때문에 JSON_ARRAYAGG의 결과값도 문자열 형태로 반환된다.

이 점은 참고하자!
