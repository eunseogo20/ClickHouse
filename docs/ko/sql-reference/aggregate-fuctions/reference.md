---
machine_translated : true
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd
toc_priority : 36
toc_title : "\u53C2\u7167"
---

# 집계 함수의 참조 {# aggregate-functions-reference}

## 카운트 {# agg_function-count}

행 또는 not-NULL 값을 계산합니다.

ClickHouse는 다음 구문을 지원합니다`count` :
-`count (expr)`또는`COUNT (DISTINCT expr)`.
-`count ()`또는`COUNT (*)`그`count ()`구문은 ClickHouse 다릅니다.

** 파라미터 **

기능은 걸릴 수 있습니다 :

- 제로 파라미터.
- 한 [식](../syntax.md#syntax-expressions).

** 반환 값 **

- 함수가 매개 변수없이 호출 된 경우 행 수가 계산됩니다.
- 만약 식 (../syntax.md#syntax-expressions) 전달되면 함수는이식이 not null을 반환 횟수를 계산합니다. 식이 a를 돌려주는 경우 [Nullable](../../sql-reference/data-types/nullable.md) - 형태의 값 다음 결과`count` 가만히 있지`Nullable` 표현식을 반환 경우 함수는 0을 반환합니다`NULL` 모든 행에 대해.

두 경우 모두, 반환 형식은 다음과 같습니다 [UInt64](../../sql-reference/data-types/int-uint.md).

** 자세한 **

클릭 하우스는`COUNT (DISTINCT ...)`구문. 이 구조의 동작은 [count_distinct_implementation](../../operations/settings/settings.md#settings-count_distinct_implementation) 설정. 그것은 어떤 것을 정의합니다 [uniq*](#agg_function-uniq) 함수는 작업을 수행하는 데 사용됩니다. 기본값은 [uniqExact](#agg_function-uniqexact) 기능.

그`SELECT count () FROM table` 테이블 항목의 수가 별도로 저장되지 않기 때문에 쿼리는 최적화되지 않습니다. 테이블에서 작은 열을 선택하고 그 안에있는 값의 개수를 계산합니다.

** 예 **

예 1 :

```sql
SELECT count () FROM t
```

```text
┌─count () ─┐
│         5 │
└───────────┘
```

예 2 :

```sql
SELECT name, value FROM system.settings WHERE name = 'count_distinct_implementation'
```

```text
┌─name──────────────────────────┬─value─────┐
│ count_distinct_implementation │ uniqExact │
└───────────────────────────────┴───────────┘
```

```sql
SELECT count (DISTINCT num) FROM t
```

```text
┌─uniqExact (num) ─┐
│                3 │
└──────────────────┘
```

이 예에서는`count (DISTINCT num)`에 의해 실행됩니다. `uniqExact`에 따라 기능`count_distinct_implementation` 설정.

## 모든 (x) {# agg_function-any}

최초로 검출 된 값을 선택합니다.
쿼리는 매번 다른 순서로 임의의 순서로 수행 할 수 있으므로이 함수의 결과는 불확실합니다.
결정적인 결과를 얻기 위해서는 'min'또는 'max'함수 대신에 'any'.

경우에 따라서는 실행 순서에 의존 할 수 있습니다. 이것은 SELECT가 ORDER BY를 사용하는 하위 쿼리에서 오는 경우에 적용됩니다.

때 a`SELECT` 쿼리는`GROUP BY` 클러스터의 모든 수식은 클러스터의 모든 식을 필요로합니다. `SELECT``HAVING`, and`ORDER BY` 절은 키 또는 집계 함수에서 계산됩니다. 즉, 테이블에서 선택된 각 열은 키 또는 집계 함수 내에서 사용해야합니다. MySQL은 같은 동작을 얻으려면 다른 열을`any` 집계 함수.

## anyHeavy (x) {#anyheavyx}

를 사용하여 자주 발생하는 값을 선택합니다. [헤비 히터](http://www.cs.umd.edu/~samir/498/karp.pdf) 알고리즘 각 쿼리의 실행 스레드에서 케이스의 절반이 넘는 값이있는 경우이 값이 반환됩니다. 일반적으로 결과는 비 결정적입니다.

```sql
anyHeavy (column)
```

** 인수 **

-`column` - The column name.

** 예 **

을 가지고 [ontime](../../getting-started/example-datasets/ontime.md) 데이터 - 세트의 자주 발생하는 값을 선택하고`AirlineID` 라인.

```sql
SELECT anyHeavy (AirlineID) AS res
FROM ontime
```

```text
┌───res─┐
│ 19690 │
└───────┘
```

## anyLast (x) {#anylastx}

마지막에 검출 된 값을 선택합니다.
결과는 마찬가지로 불확정입니다. `any` 기능.

## 그룹 비트 명령 {#groupbitand}

비트 단위로 적용`AND` 수의 시리즈를 위해.

```sql
groupBitAnd (expr)
```

** 파라미터 **

`expr` - An expression that results in`UInt *`유형.

** 반환 값 **

값`UInt *`유형.

** 예 **

테스트 데이터 :

```text
binary decimal
00101100 = 44
00011100 = 28
00001101 = 13
01010101 = 85
```

쿼리 :

```sql
SELECT groupBitAnd (num) FROM t
```

어디`num` 테스트 데이터를 포함하는 열입니다.

결과 :

```text
binary decimal
00000100 = 4
```

## groupBitOr {#groupbitor}

비트 단위로 적용`OR` 수의 시리즈를 위해.

```sql
groupBitOr (expr)
```

** 파라미터 **

`expr` - An expression that results in`UInt *`유형.

** 반환 값 **

값`UInt *`유형.

** 예 **

테스트 데이터 :

```text
binary decimal
00101100 = 44
00011100 = 28
00001101 = 13
01010101 = 85
```

쿼리 :

```sql
SELECT groupBitOr (num) FROM t
```

어디`num` 테스트 데이터를 포함하는 열입니다.

결과 :

```text
binary decimal
01111101 = 125
```

## groupBitXor {#groupbitxor}

비트 단위로 적용`XOR` 수의 시리즈를 위해.

```sql
groupBitXor (expr)
```

** 파라미터 **

`expr` - An expression that results in`UInt *`유형.

** 반환 값 **

값`UInt *`유형.

** 예 **

테스트 데이터 :

```text
binary decimal
00101100 = 44
00011100 = 28
00001101 = 13
01010101 = 85
```

쿼리 :

```sql
SELECT groupBitXor (num) FROM t
```

어디`num` 테스트 데이터를 포함하는 열입니다.

결과 :

```text
binary decimal
01101000 = 104
```

## groupBitmap {#groupbitmap}

부호없는 정수 열에서 비트 맵 또는 집계, uint64 형의 기수를 돌려줍니다. [비트 맵](../../sql-reference/functions/bitmap-functions.md).

```sql
groupBitmap (expr)
```

** 파라미터 **

`expr` - An expression that results in`UInt *`유형.

** 반환 값 **

값`UInt64` 타입.

** 예 **

테스트 데이터 :

```text
UserID
1
1
2
3
```

쿼리 :

```sql
SELECT groupBitmap (UserID) as num FROM t
```

결과 :

```text
num
3
```

## min (x) {# agg_function-min}

최소 값을 계산합니다.

## max (x) {# agg_function-max}

최대 값을 계산합니다.

## 아루구민 (arg, val) {# agg-function-argmin}

계산합니다 'arg'최소 값 'val'값. 여러 가지 값이있는 경우 'arg'최소에 'val'발견 된 최초의 이러한 값이 출력됩니다.

** 예 **

```text
┌─user─────┬─salary─┐
│ director │   5000 │
│ manager  │   3000 │
│ worker   │   1000 │
└──────────┴────────┘
```

```sql
SELECT argMin (user, salary) FROM salary
```

```text
┌─argMin (user, salary) ─┐
│ worker                 │
└────────────────────────┘
```

## 아루구 맥스 (arg, val) {# agg-function-argmax}

계산합니다 'arg'최대 값 'val'값. 여러 가지 값이있는 경우 'arg'의 최대 값의 경우 'val'발견 된 최초의 이러한 값이 출력됩니다.

## sum (x) {# agg_function-sum}

합계를 계산합니다.
숫자로만 작동합니다.

## sumWithOverflow (x) {#sumwithoverflowx}

결과는 입력 매개 변수와 같은 데이터 형식을 사용하여 숫자의 합계를 계산합니다. 총이 데이터 유형의 최대 값을 초과하면 함수는 오류를 반환합니다.

숫자로만 작동합니다.

## sumMap (키, 값) sumMap (튜플 (키, 값))) {# agg_functions-summap}

총 'value'에 지정된 키에 따라 정렬 'key'배열
키와 값의 배열의 튜플을 전달할 키와 값의 두 배열을 전달할 수와 동의어이다.
요소의 수 'key'와 'value'합계 각 행에 대해 동일해야합니다.
Returns a tuple of two arrays : keys in sorted order, and values ​​summed for the corresponding keys.

예 :

```sql
CREATE TABLE sum_map (
    date Date,
    timeslot DateTime,
    statusMap Nested (
        status UInt16,
        requests UInt64
    ),
    statusMapTuple Tuple (Array (Int32) Array (Int32))
) ENGINE = Log;
INSERT INTO sum_map VALUES
    ( '2000-01-01', '2000-01-01 00:00:00', 1, 2, 3, 10, 10, 10, (1, 2, 3, 10, 10, 10))
    ( '2000-01-01', '2000-01-01 00:00:00', 3, 4, 5, 10, 10, 10, (3, 4, 5, 10, 10, 10))
    ( '2000-01-01', '2000-01-01 00:01:00', 4, 5, 6, 10, 10, 10, (4, 5, 6, 10, 10, 10))
    ( '2000-01-01', '2000-01-01 00:01:00', 6, 7, 8, 10, 10, 10, (6, 7, 8, 10, 10, 10));

SELECT
    timeslot,
    sumMap (statusMap.status, statusMap.requests)
    sumMap (statusMapTuple)
FROM sum_map
GROUP BY timeslot
```

```text
┌────────────timeslot─┬─sumMap (statusMap.status, statusMap.requests) ─┬─sumMap (statusMapTuple) ─────────┐
│ 2000-01-01 00:00:00 │ (1,2,3,4,5, 10,10,20,10,10) │ ([1,2,3,4,5] [10,10,20,10,10]) │
│ 2000-01-01 00:01:00 │ (4,5,6,7,8, 10,10,20,10,10) │ ([4,5,6,7,8] [10,10,20,10,10]) │
└─────────────────────┴─────────────────────────── ───────────────────┴────────────────────────────── ──┘
```

## skewPop {#skewpop}

를 계산하는 [왜곡](https://en.wikipedia.org/wiki/Skewness) 시퀀스.

```sql
skewPop (expr)
```

** 파라미터 **

`expr` - [식](../syntax.md#syntax-expressions) 번호를 반환한다.

** 반환 값 **

The skewness of the given distribution : Type - [Float64](../../sql-reference/data-types/float.md)

** 예 **

```sql
SELECT skewPop (value) FROM series_with_value_column
```

## skewSamp {#skewsamp}

를 계산하는 샘플 [비대칭](https://en.wikipedia.org/wiki/Skewness) 시퀀스.

전달 된 값이 샘플을 형성하는 경우, 확률 변수의 왜곡 정도의 불편 추정치를 나타냅니다.

```sql
skewSamp (expr)
```

** 파라미터 **

`expr` - [식](../syntax.md#syntax-expressions) 번호를 반환한다.

** 반환 값 **

The skewness of the given distribution : Type - [Float64](../../sql-reference/data-types/float.md) 만약`n <= 1` (`n`은 샘플의 크기입니다) 그 함수가 반환`nan`.

** 예 **

```sql
SELECT skewSamp (value) FROM series_with_value_column
```

## 커트 팝 {#kurtpop}

를 계산하는 [첨도](https://en.wikipedia.org/wiki/Kurtosis) 시퀀스.

```sql
kurtPop (expr)
```

** 파라미터 **

`expr` - [식](../syntax.md#syntax-expressions) 번호를 반환한다.

** 반환 값 **

The kurtosis of the given distribution : Type - [Float64](../../sql-reference/data-types/float.md)

** 예 **

```sql
SELECT kurtPop (value) FROM series_with_value_column
```

## 쿠루차ン뿌 {#kurtsamp}

를 계산하는 [첨도의 샘플](https://en.wikipedia.org/wiki/Kurtosis) 시퀀스.

전달 된 값이 그 표본을 형성하는 경우, 확률 변수의 첨도의 불편 추정치를 나타냅니다.

```sql
kurtSamp (expr)
```

** 파라미터 **

`expr` - [식](../syntax.md#syntax-expressions) 번호를 반환한다.

** 반환 값 **

The kurtosis of the given distribution : Type - [Float64](../../ sql-reference / data-types / float.md) 만약`n <= 1` (`n`은 샘플의 크기입니다) 함수는`nan`.

** 예 **

```sql
SELECT kurtSamp (value) FROM series_with_value_column
```

## avg (x) {# agg_function-avg}

평균을 계산합니다.
숫자로만 작동합니다.
결과는 항상 Float64입니다.

## avgWeighted {#avgweighted}

[계산 가중 산술 평균](https://en.wikipedia.org/wiki/Weighted_arithmetic_mean).

** 구문 **

```sql
avgWeighted (x, weight)
```

** 파라미터 **

-`x` - Values ​​[정수](../data-types/int-uint.md) 또는 [부동 소수점](../data-types/float.md).
-`weight` - Weights of the values ​​[정수](../data-types/int-uint.md) 또는 [부동 소수점](../data-types/float.md).

유형`x`와`weight` 동일해야합니다.

** 반환 값 **

- 가중 평균.
-`NaN` 모든 가중치가 0이라면.

유형 : [Float64](../data-types/float.md).

** 예 **

쿼리 :

```sql
SELECT avgWeighted (x, w)
FROM values ​​( 'x Int8, w Int8'(4, 1), (1, 0), (10, 2))
```

결과 :

```text
┌─avgWeighted (x, weight) ─┐
│                        8 │
└──────────────────────────┘
```

## uniq {# agg_function-uniq}

인수가 다른 값의 대략의 수를 계산합니다.

```sql
uniq (x [, ...])
```

** 파라미터 **

이 함수는 가변 수의 매개 변수를 취합니다. 변수가 있습니다`Tuple``Array``Date``DateTime``String` 또는 숫자.

** 반환 값 **

- A [UInt64](../../sql-reference/data-types/int-uint.md) - 유형 번호.

** 구현 세부 사항 **

함수 :

- 분석의 모든 매개 변수의 해시를 계산하고 그것을 계산에 사용합니다.

-를 사용하여 적응 샘플링 알고리즘입니다. 계산 상태에서는 함수는 65536까지의 요소 해시 값의 샘플을 사용합니다.

        This algorithm is very accurate and very efficient on the CPU. When the query contains several of these functions, using`uniq` is almost as fast as using other aggregate functions.

- 결과를 명확하게 제공합니다 (쿼리 처리 순서에 의존하지 않습니다).

사용을 권장합니다이 기능은 거의 모든 시나리오.

** 참고하십시오. **

- [uniqCombined](#agg_function-uniqcombined)
- [uniqCombined64](#agg_function-uniqcombined64)
- [uniqHLL12](#agg_function-uniqhll12)
- [uniqExact(#agg_function-uniqexact)

## uniqCombined {# agg_function-uniqcombined}

다른 인수 값의 대략의 수를 계산합니다.

```sql
uniqCombined (HLL_precision) (x [, ...])
```

그`uniqCombined` 함수는 다른 값의 수를 계산하기위한 좋은 선택입니다.

** 파라미터 **

이 함수는 가변 수의 매개 변수를 취합니다. 변수가 있습니다`Tuple``Array``Date``DateTime``String` 또는 숫자.

`HLL_precision`는 셀 수의 바닥 2 로그입니다. [HyperLogLog](https://en.wikipedia.org/wiki/HyperLogLog) 옵션에서 다음과 같이 함수를 사용할 수 있습니다`uniqCombined (x [, ...])`. 기본값`HLL_precision` 17이며, 이것은 실질적으로 96KiB의 공간 (2 ^ 17 셀 각 6 비트)이다.

** 반환 값 **

- 번호 [UInt64](../../sql-reference/data-types/int-uint.md) - 유형 번호.

** 구현 세부 사항 **

함수 :

- 해시 (64 비트 해시`String` 그렇지 않으면 32 비트) 분석의 모든 매개 변수에 대해 그것을 계산에 사용합니다.

- 배열, 해시 테이블, HyperLogLog 및 오류 정정 테이블의 조합을 사용합니다.

        For a small number of distinct elements, an array is used. When the set size is larger, a hash table is used. For a larger number of elements, HyperLogLog is used, which will occupy a fixed amount of memory.

- 결과를 명확하게 제공합니다 (쿼리 처리 순서에 의존하지 않습니다).

!!! note "주"
    비 32 비트 해시를 사용하기 때문에 -`String` 유형은 결과보다 훨씬 큰 기수에 대한 매우 높은 오차를 갖는`UINT_MAX` (오류는 수십억 다른 값 후 즉시 발생합니다 )이 경우 다음을 사용해야합니다 [uniqCombined64](#agg_function-uniqcombined64)

비교되는 [uniq](#agg_function-uniq) 함수는`uniqCombined` :

- 몇 배 적은 메모리를 소비합니다.
- 몇 배 높은 정밀도로 계산합니다.
- 일반적으로 약간 성능이 낮다. 시나리오에 따라`uniqCombined`는 어떻게 얽혀 있는지를 조사`uniq` 예를 들어, 네트워크를 통해 다수의 통합 상태를 전송하는 분산 쿼리입니다.

** 참고하십시오. **

- [uniq](#agg_function-uniq)
- [uniqCombined64](#agg_function-uniqcombined64)
- [uniqHLL12](#agg_function-uniqhll12)
- [uniqExact](#agg_function-uniqexact)

## uniqCombined64 {# agg_function-uniqcombined64}

같은 [uniqCombined](#agg_function-uniqcombined) 그러나 모든 데이터 형식에 64 비트 해시를 사용합니다.

## uniqHLL12 {# agg_function-uniqhll12}

다른 인수 값의 대략의 수를 계산합니다. [HyperLogLog](https://en.wikipedia.org/wiki/HyperLogLog) 알고리즘

```sql
uniqHLL12 (x [, ...])
```

** 파라미터 **

이 함수는 가변 수의 매개 변수를 취합니다. 변수가 있습니다`Tuple``Array``Date``DateTime``String` 또는 숫자.

** 반환 값 **

- A [UInt64](../../sql-reference/data-types/int-uint.md) - 유형 번호.

** 구현 세부 사항 **

함수 :

- 분석의 모든 매개 변수의 해시를 계산하고 그것을 계산에 사용합니다.

- HyperLogLog 알고리즘을 사용하여 다른 인수 값의 수를 근사합니다.

        212 5-bit cells are used. The size of the state is slightly more than 2.5 KB. The result is not very accurate (up to 10 % error) for small data sets (<10K elements) However, the result is fairly accurate for high-cardinality data sets (10K-100M), with a maximum error of ~ 1.6 %. Starting from 100M, the estimation error increases, and the function will return very inaccurate results for data sets with extremely high cardinality (1B + elements) .

- 확실한 결과를 제공합니다 (쿼리 처리 순서에 의존하지 않습니다).

이 기능의 사용은 권장하지 않습니다. 대부분의 경우, uniq(#agg_function-uniq) 또는 uniqCombined(#agg_function-uniqcombined) 기능.

** 참고하십시오. **

- [uniq](#agg_function-uniq)
- [uniqCombined](#agg_function-uniqcombined)
- [uniqExact](#agg_function-uniqexact)

## uniqExact {# agg_function-uniqexact}

다른 인수 값의 정확한 수를 계산합니다.

```sql
uniqExact (x [, ...])
```

사용하는`uniqExact` 당신이 절대적으로 정확한 결과가 필요한 경우 함수. 그렇지 않으면, [uniq](#agg_function-uniq) 기능.

그`uniqExact` 함수는 더 많은 메모리를 사용`uniq` 상태의 크기는 다른 값의 수가 증가함에 따라 무한한 성장을 가지는 때문이다.

** 파라미터 **

이 함수는 가변 수의 매개 변수를 취합니다. 변수가 있습니다`Tuple``Array``Date``DateTime``String` 또는 숫자.

** 참고하십시오. **

- [uniq](#agg_function-uniq)
- [uniqCombined](#agg_function-uniqcombined)
- [uniqHLL12](#agg_function-uniqhll12)

## groupArray (x), groupArray (max_size) (x) {# agg_function-grouparray}

인수 값의 배열을 만듭니다.
값은 임의의 (불확실한) 순서로 배열에 추가 할 수 있습니다.

두 번째 버전 (와`max_size` 매개 변수) 결과 배열의 크기를 다음으로 제한합니다`max_size` 요소.
예를 들어,`groupArray (1) (x)`와 같다. `[any (x)`.

경우에 따라서는 실행 순서에 의존 할 수도 있습니다. 이것은 다음의 경우에 적용됩니다`SELECT`을 사용하는 하위 쿼리에서 온다`ORDER BY`.

## 그룹 파 라인 셀 타트 {#grouparrayinsertat}

지정된 위치에 배열에 값을 삽입합니다.

** 구문 **

```sql
groupArrayInsertAt (default_x, size) (x, pos);
```

있는 쿼리에서 여러 값이 동일한 위치에 삽입 된 경우, 함수는 다음과 같이 작동합니다 :

- 쿼리가 단일 스레드에서 실행되는 경우 삽입 된 첫 번째 값이 사용됩니다.
- 쿼리가 여러 스레드에서 실행되는 경우 결과 값은 삽입 된 값 중 하나입니다.

** 파라미터 **

-`x` - Value to be inserted. 식 (../ syntax.md # syntax-expressions) 중 하나입니다 [해당 데이터 형식](../../sql-reference/data- types/index.md).
-`pos` - Position at which the specified element`x` 삽입되어야합니다. 배열의 인덱스 번호는 제로로부터 시작됩니다. [UInt32](../../sql-reference/data-types/int-uint.md#uint-ranges).
-`default_x`- Default value for substituting in empty positions. Optional parameter. [식](../syntax.md#syntax-expressions) 결과 데이터 유형은`x` 매개 변수. 만약`default_x`은 정의되어 있지 않다. [기본값](../../sql-reference/statements/create.md#create-default-values) 사용됩니다.
-`size`- Length of the resulting array. Optional parameter. When using this parameter, the default value`default_x` 지정해야합니다. [UInt32](../../sql-reference/data-types/int-uint.md#uint-ranges).

** 반환 값 **

- 값이 삽입 된 배열.

유형 : [배열](../../sql-reference/data-types/array.md#data-type-array).

** 예 **

쿼리 :

```sql
SELECT groupArrayInsertAt (toString (number) number * 2) FROM numbers (5);
```

결과 :

```text
┌─groupArrayInsertAt (toString (number), multiply (number 2)) ─┐
│ [ '0', '', '1', '', '2', '', '3', '', '4']                   │
└──────────────────────────────────────────────────────────────┘
```

쿼리 :

```sql
SELECT groupArrayInsertAt ( '-') (toString (number) number * 2) FROM numbers (5);
```

결과 :

```text
┌─groupArrayInsertAt ( '-') (toString (number), multiply (number 2)) ─┐
│ [ '0', '-', '1', '-', '2', '-', '3', '-', '4']                      │
└─────────────────────────────────────────────────────────────────────┘
```

쿼리 :

```sql
SELECT groupArrayInsertAt ( '-'5) (toString (number) number * 2) FROM numbers (5);
```

결과 :

```text
┌─groupArrayInsertAt ( '-'5) (toString (number), multiply (number 2)) ─┐
│ [ '0', '-', '1', '-', '2']                                           │
└──────────────────────────────────────────────────────────────────────┘
```

하나의 위치에 요소의 멀티 스레드 삽입.

쿼리 :

```sql
SELECT groupArrayInsertAt (number, 0) FROM numbers_mt (10) SETTINGS max_block_size = 1;
```

이 쿼리의 결과로, 당신은 임의의 정수를 가져옵니다`[0,9]`범위 예 :

```text
┌─groupArrayInsertAt (number 0) ─┐
│ [7]                            │
└────────────────────────────────┘
```

## 그룹 팔레 고구마 빙 숨 {# agg_function-grouparraymovingsum}

입력 값의 이동 합계를 계산합니다.

```sql
groupArrayMovingSum (numbers_for_summing)
groupArrayMovingSum (window_size) (numbers_for_summing)
```

이 기능 할 수있는 창 크기로 파라미터합니다. 지정되지 않은 경우, 함수는 열의 행 수에 동일한 창 크기를 가지고 있습니다.

** 파라미터 **

-`numbers_for_summing` - 식 (../ syntax.md # syntax-expressions) 숫자 데이터 형식의 값입니다.
-`window_size` - Size of the calculation window.

** 반환 값 **

- 입력 데이터와 동일한 크기와 형태의 배열.

** 예 **

샘플 테이블 :

```sql
CREATE TABLE t
(
    `int` UInt8,
    `float` Float32,
    `dec` Decimal32 (2)
)
ENGINE = TinyLog
```

```text
┌─int─┬─float─┬──dec─┐
│   1 │   1.1 │ 1.10 │
│   2 │   2.2 │ 2.20 │
│   4 │   4.4 │ 4.40 │
│   7 │  7.77 │ 7.77 │
└─────┴───────┴──────┘
```

쿼리 :

```sql
SELECT
    groupArrayMovingSum (int) AS I,
    groupArrayMovingSum (float) AS F,
    groupArrayMovingSum (dec) AS D
FROM t
```

```text
┌─I──────────┬─F───────────────────────────────┬─D ──────────────────────┐
│ [1,3,7,14] │ [1.1,3.3000002,7.7000003,15.47] │ [1.10,3.30,7.70,15.47]  │
└────────────┴─────────────────────────────────┴── ──────────────────────┘
```

```sql
SELECT
    groupArrayMovingSum (2) (int) AS I,
    groupArrayMovingSum (2) (float) AS F,
    groupArrayMovingSum (2) (dec) AS D
FROM t
```

```text
┌─I──────────┬─F───────────────────────────────┬─D ──────────────────────┐
│ [1,3,6,11] │ [1.1,3.3000002,6.6000004,12.17] │ [1.10,3.30,6.60,12.17]  │
└────────────┴─────────────────────────────────┴── ──────────────────────┘
```

## groupArrayMovingAvg {# agg_function-grouparraymovingavg}

입력 값의 이동 평균을 계산합니다.

```sql
groupArrayMovingAvg (numbers_for_summing)
groupArrayMovingAvg (window_size) (numbers_for_summing)
```

이 기능 할 수있는 창 크기로 파라미터합니다. 지정되지 않은 경우, 함수는 열의 행 수에 동일한 창 크기를 가지고 있습니다.

** 파라미터 **

-`numbers_for_summing` - [식](../syntax.md#syntax-expressions) 숫자 데이터 형식의 값입니다.
-`window_size` - Size of the calculation window.

** 반환 값 **

- 입력 데이터와 동일한 크기와 형태의 배열.

이 함수는 [제로 반올림](https://en.wikipedia.org/wiki/Rounding#Rounding_towards_zero) 결과 데이터 형식의 소수 자릿수를 자릅니다.

** 예 **

샘플 테이블`b` :

```sql
CREATE TABLE t
(
    `int` UInt8,
    `float` Float32,
    `dec` Decimal32 (2)
)
ENGINE = TinyLog
```

```text
┌─int─┬─float─┬──dec─┐
│   1 │   1.1 │ 1.10 │
│   2 │   2.2 │ 2.20 │
│   4 │   4.4 │ 4.40 │
│   7 │  7.77 │ 7.77 │
└─────┴───────┴──────┘
```

쿼리 :

```sql
SELECT
    groupArrayMovingAvg (int) AS I,
    groupArrayMovingAvg (float) AS F,
    groupArrayMovingAvg (dec) AS D
FROM t
```

```text
┌─I─────────┬─F─────────────────────────────────── ┬─D─────────────────────┐
│ [0,0,1,3] │ [0.275,0.82500005,1.9250001,3.8675] │ [0.27,0.82,1.92,3.86]  │
└───────────┴───────────────────────────────────── ┴───────────────────────┘
```

```sql
SELECT
    groupArrayMovingAvg (2) (int) AS I,
    groupArrayMovingAvg (2) (float) AS F,
    groupArrayMovingAvg (2) (dec) AS D
FROM t
```

```text
┌─I─────────┬─F────────────────────────────────┬─D ─────────────────────┐
│ [0,1,3,5] │ [0.55,1.6500001,3.3000002,6.085] │ [0.55,1.65,3.30,6.08]  │
└───────────┴──────────────────────────────────┴── ─────────────────────┘
```

## groupUniqArray (x), groupUniqArray (max_size) (x) {# groupuniqarrayx-groupuniqarraymax-sizex}

다른 인수 값에서 배열을 만듭니다. 메모리 소비량은`uniqExact` 기능.

두 번째 버전 (와`max_size` 매개 변수) 결과 배열의 크기를 다음으로 제한합니다`max_size` 요소.
예를 들어,`groupUniqArray (1) (x)`와 같다. `[any (x)`.

## 분 정도 수 {#quantile}

근사치를 계산합니다 [분 위수](https://en.wikipedia.org/wiki/Quantile) 수치 데이터 시퀀스.

이 함수가 적용됩니다 [공기통 표본 추출](https://en.wikipedia.org/wiki/Reservoir_sampling) 8192까지 공기통의 크기 및 표본 추출을 위해 난수 생성기를 사용합니다. 결과는 비 결정적입니다. 정확한 분위 값을 얻으려면 다음 수식을 사용합니다 [quantileExact](#quantileexact) 기능.

이상을 사용하는 경우`quantile *`쿼리의 서로 다른 수준의 함수는 내부 상태는 결합되지 않습니다 (즉, 쿼리 동작 할 수있을만큼 효율적이지 않습니다). 이 경우, [분 위수](#quantiles) 기능.

** 구문 **

```sql
quantile (level) (expr)
```

일명`median`.

** 파라미터 **

-`level` - Level of quantile. Optional parameter. Constant floating-point number from 0 to 1. We recommend using a`level` 범위의 값`[0.01, 0.99]`기본값은 0.5입니다. 에서`level = 0.5`이 함수는 [중앙값](https://en.wikipedia.org/wiki/Median).
-`expr` - Expression over the column values ​​resulting in numeric [데이터 형식](../../ sql-reference / data-types / index.md # data_types), [Date](../../sql -reference/data-types/date.md) 또는 [DateTime](../../sql-reference/data-types/datetime.md).

** 반환 값 **

- 지정된 레벨의 근사 분위 값.

유형 :

- [Float64](../../sql-reference/data-types/float.md) 숫자 데이터 형식 입력의 경우.
- [Date](../../sql-reference/data-types/date.md) 입력 값이`Date` 타입.
- [DateTime](../../sql-reference/data-types/datetime.md) 입력 값이`DateTime` 타입.

** 예 **

입력 테이블 :

```text
┌─val─┐
│   1 │
│   1 │
│   2 │
│   3 │
└─────┘
```

쿼리 :

```sql
SELECT quantile (val) FROM t
```

결과 :

```text
┌─quantile (val) ─┐
│             1.5 │
└─────────────────┘
```

** 참고하십시오. **

- [중간](#median)
- [분 위수](#quantiles)

## quantileDeterministic {#quantiledeterministic}

근사치를 계산합니다 [분 위수](https://en.wikipedia.org/wiki/Quantile) 수치 데이터 시퀀스.

이 함수가 적용됩니다 [공기통 표본 추출](https://en.wikipedia.org/wiki/Reservoir_sampling) 공기통의 크기 8192까지 및 표본 추출의 결정 론적 알고리즘을 사용합니다. 결과는 결정적입니다. 정확한 분위 값을 얻으려면 다음 수식을 사용합니다 [quantileExact](#quantileexact) 기능.

이상을 사용하는 경우`quantile *`쿼리의 서로 다른 수준의 함수는 내부 상태는 결합되지 않습니다 (즉, 쿼리 동작 할 수있을만큼 효율적이지 않습니다). 이 경우, [분 위수](#quantiles) 기능.

** 구문 **

```sql
quantileDeterministic (level) (expr, determinator)
```

일명`medianDeterministic`.

** 파라미터 **

-`level` - Level of quantile. Optional parameter. Constant floating-point number from 0 to 1. We recommend using a`level` 범위의 값`[0.01, 0.99]`기본값은 0.5입니다. 에서`level = 0.5`이 함수는 [중앙값 (https://en.wikipedia.org/wiki/Median).
-`expr` - Expression over the column values ​​resulting in numeric 데이터 형식 (../../ sql-reference / data-types / index.md # data_types), 날짜 (../../ sql -reference / data-types / date.md) 또는 DateTime (../../ sql-reference / data-types / datetime.md).
-`determinator` - Number whose hash is used instead of a random number generator in the reservoir sampling algorithm to make the result of sampling deterministic. As a determinator you can use any deterministic positive number, for example, a user id or an event id . If the same determinator value occures too often, the function works incorrectly.

** 반환 값 **

- 지정된 레벨의 근사 분위 값.

유형 :

- [Float64](../../sql-reference/data-types/float.md) 숫자 데이터 형식 입력의 경우.
- [Date](../../sql-reference/data-types/date.md) 입력 값이`Date` 타입.
- [DateTime](../../sql-reference/data-types/datetime.md) 입력 값이`DateTime` 타입.

** 예 **

입력 테이블 :

```text
┌─val─┐
│   1 │
│   1 │
│   2 │
│   3 │
└─────┘
```

쿼리 :

```sql
SELECT quantileDeterministic (val 1) FROM t
```

결과 :

```text
┌─quantileDeterministic (val 1) ─┐
│                            1.5 │
└────────────────────────────────┘
```

** 참고하십시오. **

- [중간](#median)
- [분 위수](#quantiles)

## quantileExact {#quantileexact}

정확하게 계산하는 [분 위수](https://en.wikipedia.org/wiki/Quantile) 수치 데이터 시퀀스.

To get exact value, all the passed values ​​are combined into an array, which is then partially sorted. Therefore, the function consumes`O (n)`메모리, 어디`n` 전달 된 값의 수입니다. 그러나 소수 값의 경우,이 함수는 매우 효과적입니다.

이상을 사용하는 경우`quantile *`쿼리의 서로 다른 수준의 함수는 내부 상태는 결합되지 않습니다 (즉, 쿼리 동작 할 수있을만큼 효율적이지 않습니다). 이 경우, [분 위수](#quantiles) 기능.

** 구문 **

```sql
quantileExact (level) (expr)
```

일명`medianExact`.

** 파라미터 **

-`level` - Level of quantile. Optional parameter. Constant floating-point number from 0 to 1. We recommend using a`level` 범위의 값`[0.01, 0.99]`기본값은 0.5입니다. 에서`level = 0.5`이 함수는 [중앙값](https://en.wikipedia.org/wiki/Median).
-`expr` - Expression over the column values ​​resulting in numeric [데이터 형식](../../sql-reference/data-types/index.md#data_types), [Date](../../sql -reference/data-types/date.md) 또는 DateTime (../../sql-reference/data-types/datetime.md).

** 반환 값 **

- 지정된 수준의 분위 값.

유형 :

- [Float64](../../sql-reference/data-types/float.md) 숫자 데이터 형식 입력의 경우.
- [Date](../../sql-reference/data-types/date.md) 입력 값이`Date` 타입.
- [DateTime](../../sql-reference/data-types/datetime.md) 입력 값이`DateTime` 타입.

** 예 **

쿼리 :

```sql
SELECT quantileExact (number) FROM numbers (10)
```

결과 :

```text
┌─quantileExact (number) ─┐
│                       5 │
└─────────────────────────┘
```

** 참고하십시오. **

- [중간](#median)
- [분 위수](#quantiles)

## quantileExactWeighted {#quantileexactweighted}

정확하게 계산하는 [분 위수](https://en.wikipedia.org/wiki/Quantile) 각 요소의 가중치를 고려하여 수치 데이터 시퀀스의 가중치를 지정합니다.

To get exact value, all the passed values ​​are combined into an array, which is then partially sorted. Each value is counted with its weight, as if it is present`weight` times. A hash table is used in the algorithm. Because of this, if the passed values ​​are frequently repeated, the function consumes less RAM than [quantileExact](#quantileexact)이 함수는 다음 대신 사용할 수 있습니다`quantileExact` 그리고 가중치 1을 지정합니다 .

이상을 사용하는 경우`quantile *`쿼리의 서로 다른 수준의 함수는 내부 상태는 결합되지 않습니다 (즉, 쿼리 동작 할 수있을만큼 효율적이지 않습니다). 이 경우, 분 위수 (# quantiles) 기능.

** 구문 **

```sql
quantileExactWeighted (level) (expr, weight)
```

일명`medianExactWeighted`.

** 파라미터 **

-`level` - Level of quantile. Optional parameter. Constant floating-point number from 0 to 1. We recommend using a`level` 범위의 값`[0.01, 0.99]`기본값은 0.5입니다. 에서`level = 0.5`이 함수는 [중앙값](https://en.wikipedia.org/wiki/Median).
-`expr` - Expression over the column values ​​resulting in numeric [데이터 형식](../../ sql-reference / data-types / index.md # data_types), [Date](../../sql-reference/data-types/date.md) 또는 [DateTime](../../sql-reference/data-types/datetime.md).
-`weight` - Column with weights of sequence members. Weight is a number of value occurrences.

** 반환 값 **

- 지정된 수준의 분위 값.

유형 :

- [Float64](../../sql-reference/data-types/float.md) 숫자 데이터 형식 입력의 경우.
- [Date](../../sql-reference/data-types/date.md) 입력 값이`Date` 타입.
- [DateTime](../../sql-reference/data-types/datetime.md) 입력 값이`DateTime` 타입.

** 예 **

입력 테이블 :

```text
┌─n─┬─val─┐
│ 0 │   3 │
│ 1 │   2 │
│ 2 │   1 │
│ 5 │   4 │
└───┴─────┘
```

쿼리 :

```sql
SELECT quantileExactWeighted (n, val) FROM t
```

결과 :

```text
┌─quantileExactWeighted (n, val) ─┐
│                               1 │
└─────────────────────────────────┘
```

** 참고하십시오. **

- [중간](#median)
- [분 위수](#quantiles)

## 쿠온 타이밍 {#quantiletiming}

결정된 정밀도는 [분 위수](https://en.wikipedia.org/wiki/Quantile) 수치 데이터 시퀀스.

결과는 결정적입니다 (쿼리 처리 순서에 의존하지 않습니다). 이 기능을 최적화 및 배열의 분포 같은 적재 웹 페이지에서 백엔드 대응.

이상을 사용하는 경우`quantile *`쿼리의 서로 다른 수준의 함수는 내부 상태는 결합되지 않습니다 (즉, 쿼리 동작 할 수있을만큼 효율적이지 않습니다). 이 경우, [분 위수](#quantiles) 기능.

** 구문 **

```sql
quantileTiming (level) (expr)
```

일명`medianTiming`.

** 파라미터 **

-`level` - Level of quantile. Optional parameter. Constant floating-point number from 0 to 1. We recommend using a`level` 범위의 값`[0.01, 0.99]`기본값은 0.5입니다. 에서`level = 0.5`이 함수는 [중앙값](https://en.wikipedia.org/wiki/Median).

-`expr` - [식](../syntax.md#syntax-expressions) a를 돌려 열 값에 대해 [float*](../../sql-reference/data-types/float .md) - 유형 번호.

        - If negative values are passed to the function, the behavior is undefined.
        - If the value is greater than 30,000 (a page loading time of more than 30 seconds), it is assumed to be 30,000.

** 정확도 **

계산은 다음의 경우에 정확합니다 :

- 값의 총계는 5670을 넘지 않습니다.
- 값의 수는 5670을 초과하지만 페이지로드 시간은 1024ms 미만입니다.

그렇지 않으면 계산의 결과는 16ms의 가장 가까운 배수로 반올림됩니다.

!!! note "주"
    페이지로드 시간의 분위 수를 계산하기 위해이 기능은 능률적이고 정확합니다 [분 위수](#quantile).

** 반환 값 **

- 지정된 수준의 분위 값.

유형 :`Float32`.

!!! note "주"
    함수에 값이 전달되지 않은 경우 (사용하는 경우`quantileTimingIf`), [NAN](../../sql-reference/data-types/float.md#data_type-float-nan-inf) 반환됩니다. 이 목표는 이러한 경우를 제로로되는 경우와 구별하는 것입니다. 더보기 [ORDER BY 절](../statements/select/order-by.md#select-order-by) 정렬 지침`NaN` 값.

** 예 **

입력 테이블 :

```text
┌─response_time─┐
│            72 │
│           112 │
│           126 │
│           145 │
│           104 │
│           242 │
│           313 │
│           168 │
│           108 │
└───────────────┘
```

쿼리 :

```sql
SELECT quantileTiming (response_time) FROM t
```

결과 :

```text
┌─quantileTiming (response_time) ─┐
│                             126 │
└─────────────────────────────────┘
```

** 참고하십시오. **

- [중간](#median)
- [분 위수](#quantiles)

## quantitetimingweighted {#quantiletimingweighted}

결정된 정밀도는 [분 위수](https://en.wikipedia.org/wiki/Quantile) 각 시퀀스 멤버의 가중치에 따라 수치 데이터 시퀀스.

결과는 결정적입니다 (쿼리 처리 순서에 의존하지 않습니다). 이 기능을 최적화 및 배열의 분포 같은 적재 웹 페이지에서 백엔드 대응.

이상을 사용하는 경우`quantile *`쿼리의 서로 다른 수준의 함수는 내부 상태는 결합되지 않습니다 (즉, 쿼리 동작 할 수있을만큼 효율적이지 않습니다). 이 경우, [분 위수](#quantiles) 기능.

** 구문 **

```sql
quantileTimingWeighted (level) (expr, weight)
```

일명`medianTimingWeighted`.

** 파라미터 **

-`level` - Level of quantile. Optional parameter. Constant floating-point number from 0 to 1. We recommend using a`level` 범위의 값`[0.01, 0.99]`기본값은 0.5입니다. 에서`level = 0.5`이 함수는 [중앙값](https://en.wikipedia.org/wiki/Median).

-`expr` - [식](../syntax.md#syntax-expressions) a를 돌려 열 값에 대해 [float*](../../sql-reference/data-types/float .md) - 유형 번호.

        - If negative values are passed to the function, the behavior is undefined.
        - If the value is greater than 30,000 (a page loading time of more than 30 seconds), it is assumed to be 30,000.

-`weight` - Column with weights of sequence elements. Weight is a number of value occurrences.

** 정확도 **

계산은 다음의 경우에 정확합니다 :

- 값의 총계는 5670을 넘지 않습니다.
- 값의 수는 5670을 초과하지만 페이지로드 시간은 1024ms 미만입니다.

그렇지 않으면 계산의 결과는 16ms의 가장 가까운 배수로 반올림됩니다.

!!! note "주"
    페이지로드 시간의 분위 수를 계산하기 위해이 기능은 능률적이고 정확합니다 [분 위수](#quantile).

** 반환 값 **

- 지정된 수준의 분위 값.

유형 :`Float32`.

!!! note "주"
    함수에 값이 전달되지 않은 경우 (사용하는 경우`quantileTimingIf`), [NAN](../../sql-reference/data-types/float.md#data_type-float-nan-inf) 반환됩니다. 이 목표는 이러한 경우를 제로로되는 경우와 구별하는 것입니다. 더보기 [ORDER BY 절](../statements/select/order-by.md#select-order-by) 정렬 지침`NaN` 값.

** 예 **

입력 테이블 :

```text
┌─response_time─┬─weight─┐
│            68 │      1 │
│           104 │      2 │
│           112 │      3 │
│           126 │      2 │
│           138 │      1 │
│           162 │      1 │
└───────────────┴────────┘
```

쿼리 :

```sql
SELECT quantileTimingWeighted (response_time, weight) FROM t
```

결과 :

```text
┌─quantileTimingWeighted (response_time, weight) ─┐
│                                             112 │
└─────────────────────────────────────────────────┘
```

** 참고하십시오. **

- [중간] (# median)
- [분 위수 (# quantiles)

## quantileTDigest {#quantiletdigest}

근사치를 계산합니다 [분 위수 (https://en.wikipedia.org/wiki/Quantile)를 이용한 수치 데이터 시퀀스의 t- 다이제스트] (https://github.com/tdunning/t- digest / blob / master / docs / t-digest-paper / histo.pdf) 알고리즘

최대 오차는 1 %입니다. 메모리 소비는`log (n)`여기`n` 값의 수입니다. 결과는 쿼리의 실행 순서에 따라, 비 결정적입니다.

기능의 성능은 성능보다 낮습니다 [분 위수 (# quantile) 또는 쿠온 타이밍 (# quantiletiming) 상태 크기와 정밀도의 비율에 관해서는,이 함수는`quantile`.

이상을 사용하는 경우`quantile *`쿼리의 서로 다른 수준의 함수는 내부 상태는 결합되지 않습니다 (즉, 쿼리 동작 할 수있을만큼 효율적이지 않습니다). 이 경우, 분 위수 (# quantiles) 기능.

** 구문 **

```sql
quantileTDigest (level) (expr)
```

일명`medianTDigest`.

** 파라미터 **

-`level` - Level of quantile. Optional parameter. Constant floating-point number from 0 to 1. We recommend using a`level` 범위의 값`[0.01, 0.99]`기본값은 0.5입니다. 에서`level = 0.5`이 함수는 [중앙값 (https://en.wikipedia.org/wiki/Median).
-`expr` - Expression over the column values ​​resulting in numeric 데이터 형식 (../../ sql-reference / data-types / index.md # data_types), 날짜 (../../ sql -reference / data-types / date.md) 또는 DateTime (../../ sql-reference / data-types / datetime.md).

** 반환 값 **

- 지정된 레벨의 근사 분위 값.

유형 :

- [Float64 (../../ sql-reference / data-types / float.md) 숫자 데이터 형식 입력의 경우.
- 날짜 (../../ sql-reference / data-types / date.md) 입력 값이`Date` 타입.
- [DateTime (../../ sql-reference / data-types / datetime.md) 입력 값이`DateTime` 타입.

** 예 **

쿼리 :

```sql
SELECT quantileTDigest (number) FROM numbers (10)
```

결과 :

```text
┌─quantileTDigest (number) ─┐
│ 4.5 │
└─────────────────────────┘
```

** 참고하십시오. **

- [중간] (# median)
- [분 위수 (# quantiles)

## quantileTDigestWeighted {#quantiletdigestweighted}

근사치를 계산합니다 [분 위수 (https://en.wikipedia.org/wiki/Quantile)를 이용한 수치 데이터 시퀀스의 t- 다이제스트] (https://github.com/tdunning/t- digest / blob / master / docs / t-digest-paper / histo.pdf) 알고리즘이 함수는 각 시퀀스 멤버의 가중치를 고려합니다. 최대 오차는 1 %입니다. 메모리 소비는`log (n)`여기`n` 값의 수입니다.

기능의 성능은 성능보다 낮습니다 [분 위수 (# quantile) 또는 쿠온 타이밍 (# quantiletiming) 상태 크기와 정밀도의 비율에 관해서는,이 함수는`quantile`.

결과는 쿼리의 실행 순서에 따라, 비 결정적입니다.

이상을 사용하는 경우`quantile *`쿼리의 서로 다른 수준의 함수는 내부 상태는 결합되지 않습니다 (즉, 쿼리 동작 할 수있을만큼 효율적이지 않습니다). 이 경우, 분 위수 (# quantiles) 기능.

** 구문 **

```sql
quantileTDigest (level) (expr)
```

일명`medianTDigest`.

** 파라미터 **

-`level` - Level of quantile. Optional parameter. Constant floating-point number from 0 to 1. We recommend using a`level` 범위의 값`[0.01, 0.99]`기본값은 0.5입니다. 에서`level = 0.5`이 함수는 [중앙값 (https://en.wikipedia.org/wiki/Median).
-`expr` - Expression over the column values ​​resulting in numeric 데이터 형식 (../../ sql-reference / data-types / index.md # data_types), 날짜 (../../ sql -reference / data-types / date.md) 또는 DateTime (../../ sql-reference / data-types / datetime.md).
-`weight` - Column with weights of sequence elements. Weight is a number of value occurrences.

** 반환 값 **

- 지정된 레벨의 근사 분위 값.

유형 :

- [Float64 (../../ sql-reference / data-types / float.md) 숫자 데이터 형식 입력의 경우.
- 날짜 (../../ sql-reference / data-types / date.md) 입력 값이`Date` 타입.
- [DateTime (../../ sql-reference / data-types / datetime.md) 입력 값이`DateTime` 타입.

** 예 **

쿼리 :

```sql
SELECT quantileTDigestWeighted (number 1) FROM numbers (10)
```

결과 :

```text
┌─quantileTDigestWeighted (number 1) ─┐
│ 4.5 │
└────────────────────────────────────┘
```

** 참고하십시오. **

- [중간] (# median)
- [분 위수 (# quantiles)

## 중앙값 {#median}

그`median *`함수는 해당 함수의 별칭입니다`quantile *`기능. 수치 데이터 샘플의 평균을 계산합니다.

함수 :

-`median` - Alias ​​for [분 위수 (# quantile).
-`medianDeterministic` - Alias ​​for [quantileDeterministic (# quantiledeterministic).
-`medianExact` - Alias ​​for [quantileExact (# quantileexact).
-`medianExactWeighted` - Alias ​​for [quantileExactWeighted (# quantileexactweighted).
-`medianTiming` - Alias ​​for [쿠온 타이밍 (# quantiletiming).
-`medianTimingWeighted` - Alias ​​for [quantitetimingweighted (# quantiletimingweighted).
-`medianTDigest` - Alias ​​for [quantileTDigest (# quantiletdigest).
-`medianTDigestWeighted` - Alias ​​for [quantileTDigestWeighted (# quantiletdigestweighted).

** 예 **

입력 테이블 :

```text
┌─val─┐
│ 1 │
│ 1 │
│ 2 │
│ 3 │
└─────┘
```

쿼리 :

```sql
SELECT medianDeterministic (val 1) FROM t
```

결과 :

```text
┌─medianDeterministic (val 1) ─┐
│ 1.5 │
└─────────────────────────────┘
```

## quantiles (level1, level2 ...) (x) {#quantiles}

모든 분위 함수에 대응하는 분위 함수가 있습니다 :`quantiles``quantilesDeterministic``quantilesTiming``quantilesTimingWeighted``quantilesExact``quantilesExactWeighted``quantilesTDigest` 이러한 함수는 나열된 수준의 모든 분위 수를 하나의 경로 계산 결과 값의 배열을 돌려줍니다.

## varSamp (x) {#varsampx}

금액을 계산합니다`Σ ((x - x̅) ^ 2) / (n - 1)`여기`n`은 샘플 크기이며,`x̅`의 평균입니다`x`.

전달 된 값이 그 표본을 형성하는 경우, 확률 변수의 분산의 불편 추정치를 나타냅니다.

쯔づゥ쯔. `Float64`. 때`n <= 1` 반환`+ ∞`.

!!! note "주"
    이 기능의 이용을 수치 적으로 불안정한 알고리즘입니다. 필요하면 숫자 안정성 (https://en.wikipedia.org/wiki/Numerical_stability) 계산에서는`varSampStable` 기능. 그것은 늦게 작동하지만, 계산 오차는 낮아집니다.

## varPop (x) {#varpopx}

금액을 계산합니다`Σ ((x - x̅) ^ 2) / n` 여기에서`n`은 샘플 크기이며,`x̅`의 평균입니다`x`.

즉, 값의 집합에 대한 분산. 쯔づゥ쯔. `Float64`.

!!! note "주"
    이 기능의 이용을 수치 적으로 불안정한 알고리즘입니다. 필요하면 숫자 안정성 (https://en.wikipedia.org/wiki/Numerical_stability) 계산에서는`varPopStable` 기능. 그것은 늦게 작동하지만, 계산 오차는 낮아집니다.

## stddevSamp (x) {#stddevsampx}

결과의 제곱근과 동일합니다`varSamp (x)`.

!!! note "주"
    이 기능의 이용을 수치 적으로 불안정한 알고리즘입니다. 필요하면 숫자 안정성 (https://en.wikipedia.org/wiki/Numerical_stability) 계산에서는`stddevSampStable` 기능. 그것은 늦게 작동하지만, 계산 오차는 낮아집니다.

## stddevPop (x) {#stddevpopx}

결과의 제곱근과 동일합니다`varPop (x)`.

!!! note "주"
    이 기능의 이용을 수치 적으로 불안정한 알고리즘입니다. 필요하면 숫자 안정성 (https://en.wikipedia.org/wiki/Numerical_stability) 계산에서는`stddevPopStable` 기능. 그것은 늦게 작동하지만, 계산 오차는 낮아집니다.

## topK (N) (x) {#topknx}

지정된 열 중 가장 빈도가 높은 값의 배열을 돌려줍니다. 결과 배열은 값의 근사치 빈도의 내림차순으로 정렬됩니다 (값 자체가 없습니다).

를 구현한다. [걸러진 공간 절약 (http://www.l2f.inesc-id.pt/~fmmb/wiki/uploads/Work/misnis.ref0a.pdf) TopK를 분석하는 알고리즘. 병렬 공간 절약 (https://arxiv.org/pdf/1401.0702.pdf).

```sql
topK (N) (column)
```

이 함수는 보증 된 결과를 제공하지 않습니다. 특정 상황에서 오류가 발생하여 가장 빈번한 값이없는 빈번한 값이 반환 될 수 있습니다.

우리는 사용을 추천합니다`N <10` 가치; 성능은 큰 줄어 듭니다`N` 값. 최대`N = 65536`.

** 파라미터 **

- 'N'반환 요소의 수입니다.

이 매개 변수를 생략하면 기본값 10이 사용됩니다.

** 인수 **

- 'x'- The value to calculate frequency.

** 예 **

을 가지고 [정시] (../../ getting-started / example-datasets / ontime.md) 데이터 집합에서 가장 자주 발생하는 세 가지 값을 선택합니다. `AirlineID` 라인.

```sql
SELECT topK (3) (AirlineID) AS res
FROM ontime
```

```text
┌─res─────────────────┐
│ [19393,19790,19805] │
└─────────────────────┘
```

## 톱 웨이트 {#topkweighted}

유사한`topK` 그러나 정수의 추가 인수를 취합니다 -`weight` 모든 값이 고려됩니다`weight` 주파수 계산을위한 시간.

** 구문 **

```sql
topKWeighted (N) (x, weight)
```

** 파라미터 **

-`N` - The number of elements to return.

** 인수 **

-`x` - The value.
-`weight` - The weight [UInt8 (../../ sql-reference / data-types / int-uint.md).

** 반환 값 **

대략적인 가중치의 합이 최대 값의 배열을 돌려줍니다.

** 예 **

쿼리 :

```sql
SELECT topKWeighted (10) (number, number) FROM numbers (1000)
```

결과 :

```text
┌─topKWeighted (10) (number, number) ──────────┐
│ [999,998,997,996,995,994,993,992,991,990] │
└───────────────────────────────────────────┘
```

## 코바루산뿌 (x, y) {# covarsampx-y}

값을 계산합니다`Σ ((x - x̅) (y - y̅)) / (n - 1)`.

Float64를 돌려줍니다. 때`n <= 1`, returns + ∞.

!!! note "주"
    이 기능의 이용을 수치 적으로 불안정한 알고리즘입니다. 필요하면 숫자 안정성 (https://en.wikipedia.org/wiki/Numerical_stability) 계산에서는`covarSampStable` 기능. 그것은 늦게 작동하지만, 계산 오차는 낮아집니다.

## 코바루폿뿌 (x, y) {# covarpopx-y}

값을 계산합니다`Σ ((x - x̅) (y - y̅)) / n`.

!!! note "주"
    이 기능의 이용을 수치 적으로 불안정한 알고리즘입니다. 필요하면 숫자 안정성 (https://en.wikipedia.org/wiki/Numerical_stability) 계산에서는`covarPopStable` 기능. 그것은 늦게 작동하지만, 계산 오차는 낮아집니다.

## corr (x, y) {# corrx-y}

피어슨 상관 계수를 계산합니다 :`Σ ((x - x̅) (y - y̅)) / sqrt (Σ ((x - x̅) ^ 2) * Σ ((y - y̅) ^ 2))`.

!!! note "주"
    이 기능의 이용을 수치 적으로 불안정한 알고리즘입니다. 필요하면 숫자 안정성 (https://en.wikipedia.org/wiki/Numerical_stability) 계산에서는`corrStable` 기능. 그것은 늦게 작동하지만, 계산 오차는 낮아집니다.

## categoricalInformationValue {#categoricalinformationvalue}

값을 계산합니다`(P (tag = 1) - P (tag = 0)) (log (P (tag = 1)) - log (P (tag = 0)))`카테고리마다.

```sql
categoricalInformationValue (category1, category2, ..., tag)
```

결과는 이산 (무조건) 형상의 방법을 보여줍니다`[category1, category2 ...]`의 값을 예측하는 학습 모델에 기여하는`tag`.

## simpleLinearRegression {#simplelinearregression}

단순 (한 차원) 선형 회귀를 실행합니다.

```sql
simpleLinearRegression (x, y)
```

파라미터 :

-`x` - Column with dependent variable values.
-`y` - Column with explanatory variable values.

반환 값 :

상수`(a, b)`결과 행의`y = a * x + b`.

** 예 **

```sql
SELECT arrayReduce ( 'simpleLinearRegression'[0, 1, 2, 3, 0, 1, 2, 3])
```

```text
┌─arrayReduce ( 'simpleLinearRegression'[0, 1, 2, 3, 0, 1, 2, 3]) ─┐
│ (1,0) │
└───────────────────────────────────────────────── ──────────────────┘
```

```sql
SELECT arrayReduce ( 'simpleLinearRegression'[0, 1, 2, 3, 3, 4, 5, 6])
```

```text
┌─arrayReduce ( 'simpleLinearRegression'[0, 1, 2, 3, 3, 4, 5, 6]) ─┐
│ (1,3) │
└───────────────────────────────────────────────── ──────────────────┘
```

## stochasticLinearRegression {# agg_functions-stochasticlinearregression}

이 함수는 확률 적 선형 회귀를 구현합니다. 학습 속도 및 L2 정규화 계수, 미니 배치 크기의 사용자 정의 매개 변수를 지원하고 가중치를 업데이트하는 방법은 거의 없습니다 ([아담] (https://en.wikipedia.org/wiki/Stochastic_gradient_descent#Adam) (기본적으로 사용), 단순 SGD (https://en.wikipedia.org/wiki/Stochastic_gradient_descent), 기세 (https://en.wikipedia.org/wiki/Stochastic_gradient_descent#Momentum), 네스테로후 (https : //mipt.ru/upload/medialibrary/d7e/41-91.pdf)).

### 매개 변수 {# agg_functions-stochasticlinearregression-parameters}

이 4 사용자 정의 매개 변수. 기본값이 사용되지만, 좋은 모델은 매개 변수의 조정이 필요합니다.

```text
stochasticLinearRegression (1.0, 1.0, 10 'SGD')
```

1.`learning rate` 경사 강하 단계를 실행했을 때의 단계 길이 계수입니다. 너무 학습 율의 원인이 무한한 양의 모델입니다. 디폴트는`0.00001`.
2.`l2 regularization coefficient` 오버 적합을 방지하는 데 도움이 될 수 없습니다. 디폴트는`0.1`.
3.`mini-batch size` 그라데이션을 계산 그라데이션 하강 단계를 수행하기 위해 총되는 요소의 수를 설정합니다. 순수 확률 강하는 하나의 요소를 사용하지만, 작은 배치 (약 10 요소)를 가진다고 기울기 단계가 더 안정적입니다. 디폴트는`15`.
4.`method for updating weights` 그들은`Adam` (기본적으로)`SGD``Momentum``Nesterov``Momentum`와`Nesterov` 좀 더 많은 계산과 메모리를 필요로한다 수렴 속도와 확률 구배 법의 안정성 측면에서 도움이 될거야.

### 사용법 {# agg_functions-stochasticlinearregression-usage}

`stochasticLinearRegression` 모델의 근사와 새로운 데이터의 예측입니다. 모델을 준수하고 나중에 사용하기 위해 그 상태를 저장하기 위해`-State` 기본적으로 상태 (모델의 무게 등)을 저장하는 combinator.
예측하려면 함수를 사용합니다 [evalMLMethod] (../ functions / machine-learning-functions.md # machine_learning_methods-evalmlmethod)는 상태를 인수로 예측하는 기능도 갖추고 있습니다.

<a name="stochasticlinearregression-usage-fitting"> </a>

** 1 ** 맞는

이러한 쿼리를 사용할 수있다.

```sql
CREATE TABLE IF NOT EXISTS train_data
(
    param1 Float64,
    param2 Float64,
    target Float64
) ENGINE = Memory;

CREATE TABLE your_model ENGINE = Memory AS SELECT
stochasticLinearRegressionState (0.1, 0.0, 5, 'SGD') (target, param1, param2)
AS state FROM train_data;
```

여기에 데이터를 삽입해야합니다`train_data` 테이블. 매개 변수의 수는 고정되어 있지 않고, 전달 된 인수의 수에만 의존합니다`linearRegressionState` 모든 수치이어야합니다.
대상 값을 가진 열 (예측하는 것을 배우고 싶다)가 첫 번째 인수로 삽입되는 점에 유의하십시오.

** 2 ** 예측

테이블에 상태를 저장 한 후 예측에 여러 번 사용하거나 다른 상태와 병합하여 새로운 더 나은 모델을 생성 할 수 있습니다.

```sql
WITH (SELECT state FROM your_model) AS model SELECT
evalMLMethod (model, param1, param2) FROM test_data
```

쿼리는 예측치의 라인을 돌려줍니다. 그 첫 번째 인수에주의하십시오`evalMLMethod`는`AggregateFunctionState` 개체 다음은 피쳐 라인입니다.

`test_data` 같은 테이블입니다`train_data`이 포함되지 않을 수 있습니다.

### 노트 {# agg_functions-stochasticlinearregression-notes}

1. 통합 모델은 사용자의 작성 등의 쿼리 :
    `sql SELECT state1 + state2 FROM your_models`
    어디`your_models` 테이블의 두 모델입니다. 이 쿼리는 new를 반환합니다`AggregateFunctionState` 개체

2. 사용자가 만든 모델의 가중치를 독자적인 목적으로 취득 할 수 있습니다. `-State` combinator가 사용됩니다.
    `sql SELECT stochasticLinearRegression (0.01) (target, param1, param2) FROM train_data`
    이러한 쿼리는 모델에 적합하고 그 무게를 반환합니다 - 처음에는 모델의 파라미터에 대응하는 가중치이며, 마지막은 편견입니다. 따라서 위의 예에서는 쿼리는 3 개의 값을 가지는 라인을 돌려줍니다.

** 참고하십시오. **

- [stochasticLogisticRegression (# agg_functions-stochasticlogisticregression)
- 선형 회귀와 로지스틱 회귀의 차이 (https://stackoverflow.com/questions/12146914/what-is-the-difference-between-linear-regression-and-logistic-regression)

## stochasticLogisticRegression {# agg_functions-stochasticlogisticregression}

이 함수는 확률 적 로지스틱 회귀를 구현합니다. 이것은 이진 분류 문제에 사용할 수있는 stochasticLinearRegression와 같은 사용자 정의 매개 변수를 지원하고 동일한 방식으로 작동합니다.

### 매개 변수 {# agg_functions-stochasticlogisticregression-parameters}

매개 변수는 stochasticLinearRegression와 동일합니다 :
`learning rate``l2 regularization coefficient``mini-batch size``method for updating weights`.
자세한 내용은 매개 변수 (# agg_functions-stochasticlinearregression-parameters).

```text
stochasticLogisticRegression (1.0, 1.0, 10 'SGD')
```

1. 맞는

<! - ->

    See the`Fitting` section in the [stochasticLinearRegression (# stochasticlinearregression-usage-fitting) description.

    Predicted labels have to be in \ [- 1, 1 \].

1. 예측

<! - ->

    Using saved state we can predict probability of object having label`1`.

    ```sql
    WITH (SELECT state FROM your_model) AS model SELECT
    evalMLMethod (model, param1, param2) FROM test_data
    ```

    The query will return a column of probabilities. Note that first argument of`evalMLMethod` is`AggregateFunctionState` object, next are columns of features.

    We can also set a bound of probability, which assigns elements to different labels.

    ```sql
    SELECT ans <1.1 AND ans> 0.5 FROM
    (WITH (SELECT state FROM your_model) AS model SELECT
    evalMLMethod (model, param1, param2) AS ans FROM test_data)
    ```

    Then the result will be labels.

    `test_data` is a table like`train_data` but may not contain target value.

** 참고하십시오. **

- [stochasticLinearRegression (# agg_functions-stochasticlinearregression)
- 선형 회귀와 로지스틱 회귀의 차이. (https://stackoverflow.com/questions/12146914/what-is-the-difference-between-linear-regression-and-logistic-regression)

## groupBitmapAnd {#groupbitmapand}

비트 맵 열 AND를 계산하고 uint64 형의 기수를 돌려줍니다. [비트 맵] (../../ sql-reference / functions / bitmap-functions.md).

```sql
groupBitmapAnd (expr)
```

** 파라미터 **

`expr` - An expression that results in`AggregateFunction (groupBitmap, UInt *)`타입.

** 반환 값 **

값`UInt64` 타입.

** 예 **

```sql
DROP TABLE IF EXISTS bitmap_column_expr_test2;
CREATE TABLE bitmap_column_expr_test2
(
    tag_id String,
    z AggregateFunction (groupBitmap, UInt32)
)
ENGINE = MergeTree
ORDER BY tag_id;

INSERT INTO bitmap_column_expr_test2 VALUES ( 'tag1', bitmapBuild (cast (1,2,3,4,5,6,7,8,9,10] as Array (UInt32))));
INSERT INTO bitmap_column_expr_test2 VALUES ( 'tag2', bitmapBuild (cast (6,7,8,9,10,11,12,13,14,15] as Array (UInt32))));
INSERT INTO bitmap_column_expr_test2 VALUES ( 'tag3', bitmapBuild (cast (2,4,6,8,10,12] as Array (UInt32))));

SELECT groupBitmapAnd (z) FROM bitmap_column_expr_test2 WHERE like (tag_id 'tag %');
┌─groupBitmapAnd (z) ─┐
│ 3 │
└───────────────────┘

SELECT arraySort (bitmapToArray (groupBitmapAndState (z))) FROM bitmap_column_expr_test2 WHERE like (tag_id 'tag %');
┌─arraySort (bitmapToArray (groupBitmapAndState (z))) ─┐
│ [6,8,10] │
└───────────────────────────────────────────────── ─┘
```

## groupBitmapOr {#groupbitmapor}

비트 맵 열 OR를 계산하고 uint64 형의 기수를 돌려줍니다. [비트 맵] (../../ sql-reference / functions / bitmap-functions.md). 이것은`groupBitmapMerge`.

```sql
groupBitmapOr (expr)
```

** 파라미터 **

`expr` - An expression that results in`AggregateFunction (groupBitmap, UInt *)`타입.

** 반환 값 **

값`UInt64` 타입.

** 예 **

```sql
DROP TABLE IF EXISTS bitmap_column_expr_test2;
CREATE TABLE bitmap_column_expr_test2
(
    tag_id String,
    z AggregateFunction (groupBitmap, UInt32)
)
ENGINE = MergeTree
ORDER BY tag_id;

INSERT INTO bitmap_column_expr_test2 VALUES ( 'tag1', bitmapBuild (cast (1,2,3,4,5,6,7,8,9,10] as Array (UInt32))));
INSERT INTO bitmap_column_expr_test2 VALUES ( 'tag2', bitmapBuild (cast (6,7,8,9,10,11,12,13,14,15] as Array (UInt32))));
INSERT INTO bitmap_column_expr_test2 VALUES ( 'tag3', bitmapBuild (cast (2,4,6,8,10,12] as Array (UInt32))));

SELECT groupBitmapOr (z) FROM bitmap_column_expr_test2 WHERE like (tag_id 'tag %');
┌─groupBitmapOr (z) ─┐
│ 15 │
└──────────────────┘

SELECT arraySort (bitmapToArray (groupBitmapOrState (z))) FROM bitmap_column_expr_test2 WHERE like (tag_id 'tag %');
┌─arraySort (bitmapToArray (groupBitmapOrState (z))) ─┐
│ [1,2,3,4,5,6,7,8,9,10,11,12,13,14,15] │
└───────────────────────────────────────────────── ┘
```

## groupBitmapXor {#groupbitmapxor}

비트 맵 열의 XOR를 계산하고 uint64 형의 기수를 돌려줍니다. [비트 맵] (../../ sql-reference / functions / bitmap-functions.md).

```sql
groupBitmapOr (expr)
```

** 파라미터 **

`expr` - An expression that results in`AggregateFunction (groupBitmap, UInt *)`타입.

** 반환 값 **

값`UInt64` 타입.

** 예 **

```sql
DROP TABLE IF EXISTS bitmap_column_expr_test2;
CREATE TABLE bitmap_column_expr_test2
(
    tag_id String,
    z AggregateFunction (groupBitmap, UInt32)
)
ENGINE = MergeTree
ORDER BY tag_id;

INSERT INTO bitmap_column_expr_test2 VALUES ( 'tag1', bitmapBuild (cast (1,2,3,4,5,6,7,8,9,10] as Array (UInt32))));
INSERT INTO bitmap_column_expr_test2 VALUES ( 'tag2', bitmapBuild (cast (6,7,8,9,10,11,12,13,14,15] as Array (UInt32))));
INSERT INTO bitmap_column_expr_test2 VALUES ( 'tag3', bitmapBuild (cast (2,4,6,8,10,12] as Array (UInt32))));

SELECT groupBitmapXor (z) FROM bitmap_column_expr_test2 WHERE like (tag_id 'tag %');
┌─groupBitmapXor (z) ─┐
│ 10 │
└───────────────────┘

SELECT arraySort (bitmapToArray (groupBitmapXorState (z))) FROM bitmap_column_expr_test2 WHERE like (tag_id 'tag %');
┌─arraySort (bitmapToArray (groupBitmapXorState (z))) ─┐
│ [1,3,5,6,8,10,11,13,14,15] │
└───────────────────────────────────────────────── ─┘
```

원본 기사 (https://clickhouse.tech/docs/en/query_language/agg_functions/reference/) <! - hide ->
