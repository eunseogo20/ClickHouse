---
machine_translated : true
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd
toc_priority : 38
toc_title : "\uD30C\uB77C \uBA54\uD2B8\uB9AD"
---

# 파라 메트릭 집계 함수 {#aggregate_functions_parametric}

일부 집계 함수는 인수 열 (압축에 사용됨)뿐만 아니라 매개 변수 집합 (초기화를위한 상수)을 허용 할 수 있습니다. 구문은 하나가 아닌 두 쌍의 대괄호입니다. 첫 번째는 매개 변수 용이고 두 번째는 인수 용입니다.

## 히스토그램 {#histogram}

적응 히스토그램을 계산합니다. 정확한 결과를 보장하는 것은 아닙니다.

```sql
histogram (number_of_bins) (values)
```

함수는 다음을 사용합니다 [스트리밍 병렬 의사 결정 트리 알고리즘](http://jmlr.org/papers/volume11/ben-haim10a/ben-haim10a.pdf). 히스토그램 빈의 경계는 새로운 데이터가 함수에 들어가면 조정 있습니다. 일반적인 케이스에서는 빈의 폭은 동일하지 않습니다.

** 파라미터 **

`number_of_bins` - 히스토그램의 빈 수에 대한 상한입니다. 이 함수는 빈 수를 자동으로 계산합니다. 지정된 수의 빈에 도달하려고 시도하지만 실패하면 더 적은 수의 빈을 사용합니다.
`values` - [표현](../ syntax.md # syntax-expressions) 결과로 입력 값이 생성됩니다.

** 반환 값 **

- 튜플(../../ sql-reference / data-types / tuple.md)의 [배열](../../ sql-reference / data-types / array.md) 다음과 같은 형식0 :

        ```
        [(lower_1, upper_1, height_1) ... (lower_N, upper_N, height_N)
        ```

        -`lower` - Lower bound of the bin.
        -`upper` - Upper bound of the bin.
        -`height` - Calculated height of the bin.

** 예 **

```sql
SELECT histogram (5) (number + 1)
FROM (
    SELECT *
    FROM system.numbers
    LIMIT 20
)
```

```text
┌─histogram (5) (plus (number 1)) ──────────────────────────────────── ───────┐
│ [(1,4.5,4) (4.5,8.5,4) (8.5,12.75,4.125) (12.75,17,4.625) (17,20,3.25)      │
└───────────────────────────────────────────────── ───────────────────────────┘
```

히스토그램을 시각화하려면 [바](../../sql-reference/functions/other-functions.md #function-bar) 함수 예 :

```sql
WITH histogram (5) (rand () % 100) AS hist
SELECT
    arrayJoin (hist) .3 AS height,
    bar (height, 0, 6, 5) AS bar
FROM
(
    SELECT *
    FROM system.numbers
    LIMIT 20
)
```

```text
┌─height─┬─bar───┐
│ 2.125 │ █▋    │
│ 3.25 │ ██▌     │
│ 5.625 │ ████▏ │
│ 5.625 │ ████▏ │
│ 3.375 │ ██▌    │
└────────┴───────┘
```

이 경우 히스토그램 빈의 경계를 모르는 것을 기억해야합니다.

## sequenceMatch (pattern) (timestamp, cond1, cond2 ...) {# function-sequencematch}

여부를 확인합니다 서열을 포함하는 이벤트의 체인과 일치하는 패턴입니다.

```sql
sequenceMatch (pattern) (timestamp, cond1, cond2 ...)
```

!!! warning "경고"
    같은 초에서 발생하는 이벤트는 결과에 영향을 미치는 정의되지 않은 순서로 순서에 존재할 가능성이 있습니다.

** 파라미터 **

-`pattern` - Pattern string. See [패턴 구문](#sequence-function-pattern-syntax).

-`timestamp` - Column considered to contain time data. Typical data types are`Date`와`DateTime`.도 사용할 수 있습니다 대응 [UInt](../../sql-reference/data-types/int- uint.md) 데이터 형식입니다.

-`cond1``cond2` - Conditions that describe the chain of events. Data type :`UInt8` 최대 32 개의 조건 인수를 전달할 수 있습니다. 이 함수는 이러한 조건에 설명되어있는 이벤트 만 고려합니다. 시퀀스에 조건으로 기술되지 않은 데이터가 포함되어있는 경우, 함수는 그들을 건너 뜁니다.

** 반환 값 **

- 패턴이 일치하는 경우는 1.
- 패턴이 일치하지 않는 경우는 0.

유형 :`UInt8`.

<a name="sequence-function-pattern-syntax"> </a>
** 패턴 구문 **

-`(? N)`- Matches the condition argument at position`N`. 조건은`[1, 32]`범위 예`(? 1)`에 전달 된 인수와 일치합니다. `cond1` 매개 변수.

-`*`- Matches any number of events. You do not need conditional arguments to match this element of the pattern.

-`(? t operator value)`- Sets the time in seconds that should separate two events. For example, pattern`(? 1) (? t> 1800) (? 2)`서로 1800 초 이상 발생하는 이벤트와 일치합니다. 이러한 이벤트 사이에 임의의 수의 이벤트를 배치 할 수 있습니다. 를 사용할 수 있습니다`> =``>`,`<`,`<=`연산자.

** 예 **

데이터를 고려`t` 테이블 :

```text
┌─time─┬─number─┐
│ 1    │ 1      │
│ 2    │ 3      │
│ 3    │ 2      │
└──────┴────────┘
```

쿼리 실행 :

```sql
SELECT sequenceMatch ( '(? 1) (? 2)') (time, number = 1, number = 2) FROM t
```

```text
┌─sequenceMatch ( '(? 1) (? 2)') (time, equals (number 1), equals (number 2)) ─┐
│                                                                            1 │
└─────────────────────────────────────────────────────── ──────────────────────┘
```

함수는 숫자 2가 번호 1에 이은 이벤트 체인을 발견했습니다. 번호는 이벤트로 기술되어 있지 않기 때문에, 그들 사이의 번호 3을 건너 뛰었습니다. 예제 주어진 이벤트 체인을 검색 할 때이 번호를 고려하고 있다면 그에 대한 조건을 작성해야합니다.

```sql
SELECT sequenceMatch ( '(? 1) (? 2)') (time, number = 1, number = 2, number = 3) FROM t
```

```text
┌─sequenceMatch ( '(? 1) (? 2)') (time, equals (number 1), equals (number 2), equals (number 3)) ─┐
│                                                                                               0 │
└───────────────────────────────────────────────── ───────────────────────────────────────────────┘
```

이 경우 번호 3 이벤트가 1과 2 사이에서 발생했기 때문에 함수는 패턴과 일치하는 이벤트 체인을 찾을 수 없습니다. 같은 경우 4 번 조건을 체크 한 경우, 시퀀스는 패턴과 일치합니다.

```sql
SELECT sequenceMatch ( '(? 1) (? 2)') (time, number = 1, number = 2, number = 4) FROM t
```

```text
┌─sequenceMatch ( '(? 1) (? 2)') (time, equals (number 1), equals (number 2), equals (number 4)) ─┐
│                                                                                               1 │
└───────────────────────────────────────────────── ───────────────────────────────────────────────┘
```

** 참고하십시오. **

- [시퀀스 카운트](# function-sequencecount)

## sequenceCount (pattern) (time, cond1, cond2 ...) {# function-sequencecount}

패턴에 일치하는 이벤트 체인의 수를 계산합니다. 이 함수는 중복되지 않는 이벤트 체인을 검색합니다. 현재 체인이 일치 한 후 다음 체인의 검색을 시작합니다.

!!! warning "경고"
    같은 초에서 발생하는 이벤트는 결과에 영향을 미치는 정의되지 않은 순서로 순서에 존재할 가능성이 있습니다.

```sql
sequenceCount (pattern) (timestamp, cond1, cond2 ...)
```

** 파라미터 **

-`pattern` - Pattern string. See [패턴 구문](# sequence-function-pattern-syntax).

-`timestamp` - Column considered to contain time data. Typical data types are`Date`와`DateTime`.도 사용할 수 있습니다 대응 [UInt](../../sql-reference/data-types/int-uint.md) 데이터 형식입니다.

-`cond1``cond2` - Conditions that describe the chain of events. Data type :`UInt8` 최대 32 개의 조건 인수를 전달할 수 있습니다. 이 함수는 이러한 조건에 설명되어있는 이벤트 만 고려합니다. 시퀀스에 조건으로 기술되지 않은 데이터가 포함되어있는 경우, 함수는 그들을 건너 뜁니다.

** 반환 값 **

- 일치하는 고유 한 이벤트 체인의 수입니다.

유형 :`UInt64`.

** 예 **

데이터를 고려`t` 테이블 :

```text
┌─time─┬─number─┐
│ 1    │ 1      │
│ 2    │ 3      │
│ 3    │ 2      │
│ 4    │ 1      │
│ 5    │ 3      │
│ 6    │ 2      │
└──────┴────────┘
```

숫자 2 숫자 1 이후 몇번 발생 하는지를 계산합니다. :

```sql
SELECT sequenceCount ( '(? 1) * (? 2)') (time, number = 1, number = 2) FROM t
```

```text
┌─sequenceCount ( '(? 1) * (? 2)') (time, equals (number 1), equals (number 2)) ─┐
│                                                                              2 │
└───────────────────────────────────────────────── ──────────────────────────────┘
```

** 참고하십시오. **

- [시퀀스 매치](# function-sequencematch)

## 창 판넬 {#windowfunnel}

슬라이드 시간 창에서 이벤트 체인을 검색하고 체인에서 발생한 이벤트의 최대 개수를 계산합니다.

기능의 작동에 따라 알고리즘 :

-이 함수는 체인의 첫 번째 조건을 트리거 이벤트 카운터를 1로 설정하면 데이터를 검색합니다. 이것은 슬라이딩 윈도우가 시작되는 순간입니다.

- 그래서 체인이 순차적에서 윈도우 카운터를 증가됩니다. 시퀀스의 이벤트가 실패 카운터 증가됩니다.

- 다양한 완료 지점에서 데이터에 여러 이벤트 체인이있는 경우, 함수는 가장 긴 체인의 크기 만 출력합니다.

** 구문 **

```sql
windowFunnel (window [mode]) (timestamp, cond1, cond2, ..., condN)
```

** 파라미터 **

-`window` - Length of the sliding window in seconds.
-`mode` -이 옵션의 인수입니다.
    -` 'strict'` - 때`'strict'` 설정되어있는 경우 windowFunnel ()는 고유 값에 대해서만 조건을 적용합니다.
-`timestamp` - Name of the column containing the timestamp. Data types supported : 날짜 (../../ sql-reference / data-types / date.md) [DateTime](../../sql-reference/data-types/datetime.md # data_type-datetime) 기타 부호없는 정수 (timestamp가 지원에도 불구하고`UInt64` 값은 Int64 최대 값을 초과 할 수 없습니다 (2 ^ 63 -1).
-`cond` - Conditions or data describing the chain of events [UInt8](../../sql-reference/data-types/int-uint.md).

** 반환 값 **

슬라이딩 시간 창의 체인에서의 연속적인 트리거 조건의 최대 수.
선택의 모든 체인이 분석됩니다.

유형 :`Integer`.

** 예 **

사용자가 전화를 선택하고 온라인 상점에서 두 번 구입하는 데 충분한 시간이 있는지 여부를 판단합니다.

다음의 일련의 이벤트를 설정합니다 :

1. 사용자가 저장소 계정에 로그인 한 (`eventID = 1003`).
2. 사용자가 전화를 검색하는 (`eventID = 1007, product = 'phone'`).
3. 사용자가 주문한 (`eventID = 1009`).
4. 사용자가 다시 주문했습니다 (`eventID = 1010`).

입력 테이블 :

```text
┌─event_date─┬─user_id─┬───────────timestamp─┬─eventID─┬─product─┐
│ 2019-01-28 │       1 │ 2019-01-29 10:00:00 │    1003 │ phone   │
└────────────┴─────────┴─────────────────────┴─────────┴─────────┘
┌─event_date─┬─user_id─┬───────────timestamp─┬─eventID─┬─product─┐
│ 2019-01-31 │ 1       │ 2019-01-31 09:00:00 │    1007 │ phone   │
└────────────┴─────────┴─────────────────────┴─────────┴─────────┘
┌─event_date─┬─user_id─┬───────────timestamp─┬─eventID─┬─product─┐
│ 2019-01-30 │ 1       │ 2019-01-30 08:00:00 │    1009 │ phone   │
└────────────┴─────────┴─────────────────────┴─────────┴─────────┘
┌─event_date─┬─user_id─┬───────────timestamp─┬─eventID─┬─product─┐
│ 2019-02-01 │ 1       │ 2019-02-01 08:00:00 │    1010 │ phone   │
└────────────┴─────────┴─────────────────────┴─────────┴─────────┘
```

사용자의 거리를 조사`user_id` 2019 년에 체인을 빠져 나갈 수 있었다.

쿼리 :

```sql
SELECT
    level,
    count () AS c
FROM
(
    SELECT
        user_id,
        windowFunnel (6048000000000000) (timestamp, eventID = 1003, eventID = 1009, eventID = 1007, eventID = 1010) AS level
    FROM trend
    WHERE (event_date> = '2019-01-01') AND (event_date <= '2019-02-02')
    GROUP BY user_id
)
GROUP BY level
ORDER BY level ASC
```

결과 :

```text
┌─level─┬─c─┐
│     4 │ 1 │
└───────┴───┘
```

## 유지 {#retention}

이 함수는 1부터 32까지의 형식 인수 조건 세트를 인수로 사용합니다`UInt8` 여부를 나타내는 특정 조건을 충족하는 이벤트입니다.
모든 조건을 인수로 지정할 수 있습니다 [WHERE] (../../ sql-reference / statements / select / where.md # select-where)).

첫 번째 조건을 제외한 조건은 쌍으로 적용됩니다 : 첫 번째와 두 번째가 참이면 두 번째 결과는 참입니다 처음과 fird이 사실이라면 세 번째 결과는 참입니다.

** 구문 **

```sql
retention (cond1, cond2, ..., cond32);
```

** 파라미터 **

-`cond` - an expression that returns a`UInt8` 결과 (1 또는 0).

** 반환 값 **

1 또는 0의 배열.

- 1 - condition was met for the event.
- 0 - condition was not met for the event.

유형 :`UInt8`.

** 예 **

는 계산의 예를 생각해 봅시다`retention` 사이트 트래픽을 결정하는 기능.

** 1 ** Сreate a table to illustrate an example.

```sql
CREATE TABLE retention_test (date Date, uid Int32) ENGINE = Memory;

INSERT INTO retention_test SELECT '2020-01-01', number FROM numbers (5);
INSERT INTO retention_test SELECT '2020-01-02', number FROM numbers (10);
INSERT INTO retention_test SELECT '2020-01-03', number FROM numbers (15);
```

입력 테이블 :

쿼리 :

```sql
SELECT * FROM retention_test
```

결과 :

```text
┌───────date─┬─uid─┐
│ 2020-01-01 │   0 │
│ 2020-01-01 │   1 │
│ 2020-01-01 │   2 │
│ 2020-01-01 │   3 │
│ 2020-01-01 │   4 │
└────────────┴─────┘
┌───────date─┬─uid─┐
│ 2020-01-02 │   0 │
│ 2020-01-02 │   1 │
│ 2020-01-02 │   2 │
│ 2020-01-02 │   3 │
│ 2020-01-02 │   4 │
│ 2020-01-02 │   5 │
│ 2020-01-02 │   6 │
│ 2020-01-02 │   7 │
│ 2020-01-02 │   8 │
│ 2020-01-02 │   9 │
└────────────┴─────┘
┌───────date─┬─uid─┐
│ 2020-01-03 │   0 │
│ 2020-01-03 │   1 │
│ 2020-01-03 │   2 │
│ 2020-01-03 │   3 │
│ 2020-01-03 │   4 │
│ 2020-01-03 │   5 │
│ 2020-01-03 │   6 │
│ 2020-01-03 │   7 │
│ 2020-01-03 │   8 │
│ 2020-01-03 │   9 │
│ 2020-01-03 │  10 │
│ 2020-01-03 │  11 │
│ 2020-01-03 │  12 │
│ 2020-01-03 │  13 │
│ 2020-01-03 │  14 │
└────────────┴─────┘
```

** 2 ** 사용자를 고유 ID로 그룹화`uid`를 사용하여`retention` 기능.

쿼리 :

```sql
SELECT
    uid,
    retention (date = '2020-01-01'date = '2020-01-02'date = '2020-01-03') AS r
FROM retention_test
WHERE date IN ( '2020-01-01', '2020-01-02', '2020-01-03')
GROUP BY uid
ORDER BY uid ASC
```

결과 :

```text
┌─uid─┬─r───────┐
│   0 │ [1,1,1] │
│   1 │ [1,1,1] │
│   2 │ [1,1,1] │
│   3 │ [1,1,1] │
│   4 │ [1,1,1] │
│   5 │ [0,0,0] │
│   6 │ [0,0,0] │
│   7 │ [0,0,0] │
│   8 │ [0,0,0] │
│   9 │ [0,0,0] │
│  10 │ [0,0,0] │
│  11 │ [0,0,0] │
│  12 │ [0,0,0] │
│  13 │ [0,0,0] │
│  14 │ [0,0,0] │
└─────┴─────────┘
```

** 3 ** 하루의 사이트 방문의 총 수를 계산합니다.

쿼리 :

```sql
SELECT
    sum (r [1]) AS r1,
    sum (r [2]) AS r2,
    sum (r [3]) AS r3
FROM
(
    SELECT
        uid,
        retention (date = '2020-01-01'date = '2020-01-02'date = '2020-01-03') AS r
    FROM retention_test
    WHERE date IN ( '2020-01-01', '2020-01-02', '2020-01-03')
    GROUP BY uid
)
```

결과 :

```text
┌─r1─┬─r2─┬─r3─┐
│  5 │  5 │  5 │
└────┴────┴────┘
```

어디에 :

-`r1`-2020-01-01 동안 사이트를 방문한 고유 방문자 수 (`cond1` 조건).
-`r2`-2020-01-01에서 2020-01-02 동안 특정 기간에 사이트를 방문한 고유 방문자 수 (`cond1`와`cond2` 조건).
-`r3`-2020-01-01에서 2020-01-03 동안 특정 기간에 사이트를 방문한 고유 방문자 수 (`cond1`와`cond3` 조건).

## uniqUpTo (N) (x) {#uniquptonx}

Calculates the number of different argument values ​​if it is less than or equal to N. If the number of different argument values ​​is greater than N, it returns N + 1.

작은 Ns의 사용을 위해 추천되는 10까지 N의 최대 값은 100입니다.

집계 함수의 상태에 대해서는 1 + N \ * 바이트 값의 크기에 동일한 메모리를 사용합니다.
문자열의 경우 8 바이트의 비 암호화 해시를 저장합니다. 즉, 계산은 문자열에 근사됩니다.

이 함수는 어떤 인수도 작동합니다.

큰 N 값이 사용되며 고유 값의 개수가 N보다 약간 작은 경우를 제외하고는 가능한 빠르게 작동합니다.

사용 예 :

```text
Problem : Generate a report that shows only keywords that produced at least 5 unique users.
Solution : Write in the GROUP BY query SearchPhrase HAVING uniqUpTo (4) (UserID)> = 5
```

[원본 기사](https://clickhouse.tech/docs/en/query_language/agg_functions/parametric_functions/) <! - hide ->

## sumMapFiltered (keys_to_keep) (키, 값) {# summapfilteredkeys-to-keepkeys-values}

같은 동작 [서브맵](reference.md # agg_functions-summap) 그러나 키의 배열은 매개 변수로 전달됩니다. 이것은 키의 기수가 높은 경우에 특히 유용합니다.
