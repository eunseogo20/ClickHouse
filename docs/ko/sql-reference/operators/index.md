--- 
machine_translated : true 
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd 
toc_priority : 37 
toc_title : "\ u6F14 \ u7B97 \ u5B50" 
--- 

# 연산자 {#operators} 

ClickHouse는, 우선 순위, 우선 순위 및 연관 따라 쿼리 분석 단계에서 연산 아이를 해당 함수로 변환합니다. 

## 액세스 연산자 {# access-operators} 

`a [N]`- Access to an element of an array. The`arrayElement (a, N)`기능. 

`aN` - Access to a tuple element. The`tupleElement (a, N)`기능. 

## 수치 부정 연산자 {# numeric-negation-operator} 

`-a` - The`negate (a)`기능. 

## 곱하기 연산자와 나누기 연산자 {# multiplication-and-division-operators} 

`a * b` - The`multiply (a, b)`기능. 

`a / b` - The`divide (a, b)`기능. 

`a % b` - The`modulo (a, b)`기능.

## 더하기 연산자와 빼기 {# addition-and-subtraction-operators} 

`a + b` - The`plus (a, b)`기능. 

`a - b` - The`minus (a, b)`기능. 

## 비교 연산자 {# comparison-operators} 

`a = b` - The`equals (a, b)`기능. 

`a == b` - The`equals (a, b)`기능. 

`a! = b` - The`notEquals (a, b)`기능. 

`a <> b` - The`notEquals (a, b)`기능. 

`a <= b` - The`lessOrEquals (a, b)`기능. 

`a> = b` - The`greaterOrEquals (a, b)`기능. 

`a <b` - The`less (a, b)`기능. 

`a> b` - The`greater (a, b)`기능. 

`a LIKE s` - The`like (a, b)`기능. 

`a NOT LIKE s` - The`notLike (a, b)`기능. 

`a BETWEEN b AND c` - The same as`a> = b AND a <= c`. 

`a NOT BETWEEN b AND c` - The same as`a <b OR a> c`.

## 데이터 집합을 사용하는 연산자 {# operators-for-working-with-data-sets} 

*보기 [연산자] (in.md) * 

`a IN ...`- The`in (a b)`기능. 

`a NOT IN ...`- The`notIn (a, b)`기능. 

`a GLOBAL IN ...`- The`globalIn (a, b)`기능. 

`a GLOBAL NOT IN ...`- The`globalNotIn (a, b)`기능. 

## 날짜와 시간을 조작하는 연산자 {# operators-datetime} 

### EXTRACT {# operator-extract} 

```sql 
EXTRACT (part FROM date); 
``` 

특정 날짜에서 부품을 추출합니다. 예를 들어, 특정 날짜에서 월 또는 시간에서 초를 얻을 수 있습니다. 

그`part` 파라미터 취득하는 날짜의 어느 부분을 지정합니다. 사용 가능한 값은 다음과 같습니다 : 

-`DAY` - The day of the month. Possible values : 1-31. 
-`MONTH` - The number of a month. Possible values : 1-12. 
-`YEAR` - The year.
-`SECOND` - The second. Possible values : 0-59. 
-`MINUTE` - The minute. Possible values : 0-59. 
-`HOUR` - The hour. Possible values : 0-23. 

그`part` 매개 변수 대소 문자를 구별한다. 

그`date` 매개 변수 처리 날짜 또는 시간을 지정합니다. 어느 날짜 (../../ sql-reference / data-types / date.md) 또는 DateTime (../../ sql-reference / data-types / datetime.md) 타입에 대응 하고 있습니다. 

예 : 

```sql 
SELECT EXTRACT (DAY FROM toDate ( '2017-06-15')); 
SELECT EXTRACT (MONTH FROM toDate ( '2017-06-15')); 
SELECT EXTRACT (YEAR FROM toDate ( '2017- 06-15 ')); 
``` 

다음 예제에서는 테이블을 만들고 거기에 값을 삽입합니다`DateTime` 타입. 

```sql 
CREATE TABLE test.Orders 
( 
    OrderId UInt64, 
    OrderName String,
    OrderDate DateTime 
) 
ENGINE = Log; 
``` 
│ 2008 │ 10 │ 11 │ 13 │ 23 │ 44 │
 
```sql
INSERT INTO test.Orders VALUES (1, 'Jarlsberg Cheese', toDateTime ( '2008-10-11 13:23:44')); 
``` 

```sql 
SELECT 
    toYear (OrderDate) AS OrderYear, 
    toMonth (OrderDate) AS OrderMonth, 
    toDayOfMonth (OrderDate) AS OrderDay, 
    toHour (OrderDate) AS OrderHour, 
    toMinute (OrderDate) AS OrderMinute, 
    toSecond (OrderDate) AS OrderSecond 
FROM test.Orders; 
``` 

```text 
┌─OrderYear─┬─OrderMonth─ ┬─OrderDay─┬─OrderHour─┬─OrderMinute─┬─OrderSecond─┐ 
```
└───────────┴────────────┴──────────┴───────────┴─ ────────────┴─────────────┘ 

다음의 예를 볼 수 있습니다 [테스트] (https://github.com/ClickHouse/ ClickHouse / blob / master / tests / queries / 0_stateless / 00619_extract.sql) 

### iNTERVAL {# operator-interval} 

을 만듭니다. 간격 (../../ sql-reference / data-types / special-data-types / interval.md) - 산술 연산에서 사용될 형식의 값 날짜 (../../ sql -reference / data-types / date.md) 및 DateTime (../../ sql-reference / data-types / datetime.md) - 값을 입력합니다. 

간격 유형 : 
-`SECOND` 
-`MINUTE` 
-`HOUR` 
-`DAY` 
-`WEEK` 
-`MONTH` 
-`QUARTER` 
-`YEAR` 

!!! warning "경고"
    다른 유형의 간격은 결합 할 수 없습니다. 다음과 같은 표현은 사용할 수 없습니다`INTERVAL 4 DAY 1 HOUR`. 간격은 간격의 최소 단위보다 작은 단위 또는 동일한 단위로 지정합니다. `INTERVAL 25 HOUR` 다음의 예와 같이 연속 된 작업을 사용할 수 있습니다. 

예 : 
 
```sql
SELECT now () AS current_date_time, current_date_time + INTERVAL 4 DAY + INTERVAL 3 HOUR 
``` 

```text 
┌───current_date_time─┬─plus (plus (now (), toIntervalDay (4)), toIntervalHour (3 )) ─┐ 
│ 2019-10-23 11:16:28 │ 2019-10-27 14:16:28 │ 
└─────────────────────┴ ────────────────────────────────────────────────── ──────┘ 
``` 

** 참조. ** 

- 간격 (../../ sql-reference / data-types / special-data-types / interval.md) 데이터 형식 
- [toInterval (../../ sql-reference / functions / type-conversion-functions.md # function-tointerval) 형식 변환 함수
 
## 논리 부정 연산자 {# logical-negation-operator} 

`nOT a` - The`not (a)`기능. 

## 논리 And 연산자 {# logical-and-operator} 

`a AND b` - The`and (a, b)`기능.

## 논리 OR 연산자 {# logical-or-operator} 

`a OR b` - The`or (a, b)`기능. 

## 조건 연산자 {# conditional-operator} 

`a? b : c` - The`if (a, b, c)`기능. 

참고 : 

조건 연산자는 b와 c의 값을 계산하고 조건 a가 충족되었는지 여부를 확인하고 해당 값을 반환합니다. 만약`b` 또는`C`는 [배열 조인 () (../../ sql-reference / functions / array-join.md # functions_arrayjoin) 함수는 각 행에 관계없이 복제됩니다 "a" 조건이다 

## 조건식 {#operator_case} 

```sql 
CASE [x] 
    WHEN a THEN b 
    [WHEN ... THEN ...] 
    [ELSE c] 
END 
``` 

만약`x`가 지정된다. `transform (x, a, ..., b, ..., c)`function is used. Otherwise -`multiIf (a, b, ..., c)`. 

이없는 경우`ELSE c` 절 식의 기본값은 다음과 같습니다`NULL`. 

그`transform` 함수는 작동하지 않습니다`NULL`.

## 연결 연산자 {# concatenation-operator} 

`s1 || s2` - The`concat (s1, s2) function.` 

## 람다 작성 연산자 {# lambda-creation-operator} 

`x -> expr` - The`lambda (x, expr) function.` 

다음 연산자는 괄호이기 때문에 우선 순위가 없습니다 : 

## 배열 생성 연산자 {# array-creation-operator} 

`[x1, ...]` - The`array (x1, ...) function.` 

## 튜플 생성 연산자 {# tuple-creation-operator} 

`(x1, x2, ...)`- The`tuple (x2, x2, .. ) function.` 

## 연상 성 {#associativity} 

모든 이항 연산자는 연상을두고 있습니다. 예를 들어,`1 + 2 + 3`로 변환됩니다`plus (plus (1, 2), 3)`. 
때로는 이것은 당신이 예상 한대로 작동하지 않습니다. 예를 들어,`SELECT 4> 2> 3` 결과는 0이됩니다. 
## 체크`NULL` {# checking-for-null}

효율성을 위해`and`와`or` 함수는 임의의 수의 인수를 받아들입니다. 해당 사슬`AND`와`OR` 연산자는 이러한 함수의 단일 호출로 변환됩니다.  
    -`0` 값이`NULL`.


클릭 하우스는`IS NULL`와`IS NOT NULL` 연산자. 

### IS NULL {# operator-is-null} 

- 위해 [Null 가능 (../../ sql-reference / data-types / nullable.md) 형의 값은`IS NULL` 연산 아이의 반환 값 : 
    -`1` 값이`NULL`. 
    -`0` 그렇지 않으면 
- 기타 값은`IS NULL` 연산자는 항상 반환합니다`0`. 

<! - - > 

```sql 
SELECT x + 100 FROM t_null WHERE y IS NULL 
``` 

```text 
┌─plus (x 100) ─┐ 
│ 101 │ 
└────────────── ┘ 
``` 

### iS nOT nULL {# is-not-null} 

- 위해 [null 가능 (../../ sql-reference / data-types / nullable.md) 형의 값은 `IS NOT NULL` 연산자의 반환 값 : 
    -`1` 그렇지 않으면 
- 기타 값은`IS NOT NULL` 연산자는 항상 반환합니다`1`. 

<! - -> 

`` `sql
SELECT * FROM t_null WHERE y IS NOT NULL 
``` 

```text 
┌─x─┬─y─┐ 
│ 2 │ 3 │ 
└───┴───┘ 
``` 

원래 기사 (https : //clickhouse.tech/docs/en/query_language/operators/) <! - hide ->
