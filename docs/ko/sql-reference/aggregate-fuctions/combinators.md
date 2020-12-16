---
machine_translated : true
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd
toc_priority : 37
toc_title : "\ u30B3 \ u30F3 \ u30D3 \ u30CD \ u30FC \ u30BF"
---

# 집계 함수 연결자 {#aggregate_functions_combinators}

집계 함수의 이름에 접미사를 추가 할 수 있습니다. 그러면 집계 함수의 동작 방법이 변경됩니다.

## - 만약 {# agg-functions-combinator-if}

접미사 -If는 집계 함수의 이름에 추가 할 수 있습니다. 이 경우 집계 함수는 추가 인수 인 조건 (Uint8 유형)을 허용합니다. 집계 함수는 조건을 트리거하는 행만 처리합니다. 조건이 한 번도 트리거되지 않은 경우 기본값 (일반적으로 0 또는 빈 문자열)을 반환합니다.

예 :`sumIf (column, cond)`,`countIf (cond)`,`avgIf (x, cond)`,`quantilesTimingIf (level1, level2) (x, cond)`,`argMinIf (arg, val, cond) `등.

조건 집계 함수를 사용하면 하위 쿼리 및 하위 쿼리를 사용하지 않고 여러 조건의 집계를 한 번에 계산할 수 있습니다. `JOIN` 예를 들어, Yandex 중. Metrica 조건부 집계 함수를 사용하여 세그먼트 비교 기능을 구현합니다.

## - 배열 {# agg-functions-combinator-array}

-Array 접미사는 임의의 집계 함수에 추가 할 수 있습니다. 이 경우, 집계 함수는 'Array (T)'형태 (배열) 대신에 'T'인수를 입력합니다. 집계 함수가 복수의 인수를 받아들이는 경우, 이것은 동일한 길이의 배열이 아니면 안됩니다. 배열을 처리하는 경우, 집계 함수는 모든 배열 요소에 걸쳐 원래의 집계 함수와 동일하게 작동합니다.

예 1 :`sumArray (arr)`- 모든 `arr`배열의 모든 요소를 합계합니다. 이 예제에서는 보다 쉽게 작성할 수 있습니다 :`sum (arraySum (arr))`.

예 2 :`uniqArray (arr)`- 모든 'arr'배열의 고유 요소 수를 계산합니다. 이것은 간단한 방법으로 할 수 있습니다 :`uniq (arrayJoin (arr))` 하지만 항상 쿼리에 'arrayJoin'을 추가 할 수 있는 것은 아닙니다.

-If과 -Array을 결합 할 수 있습니다. 그러나 'Array'첫번째로 와야합니다. 'If'예 :`uniqArrayIf (arr, cond)`,`quantilesTimingArrayIf (level1, level2) (arr, cond)`이 순서로 인해 'cond'인수 배열은 없습니다.

## - 상태 {# agg-functions-combinator-state}

이 연결자를 적용하면 집계 함수는 결과 값 (예를 들어, [uniq](reference.md #agg_function-uniq)함수의 고유 값 수)을 반환하지 않지만 집계의 중간 상태(`uniq`의경우 고유 값의 개수를 계산하는 해시 테이블입니다).를 반환한다. 이것은 추가 처리에 사용하거나 나중에 집계를 완료하기 위해 테이블에 저장할 수 있는`AggregateFunction (...)`입니다.

이들 국가는 이용 :

- [AggregatingMergeTree](../../ engines / table-engines / mergetree-family / aggregatingmergetree.md) 테이블 엔진.
- [finalizeAggregation](../../ sql-reference / functions / other-functions.md # function-finalizeaggregation) 기능.
- [runningAccumulate](../../ sql-reference / functions / other-functions.md # function-runningaccumulate) 기능.
- [-Merge](# aggregate_functions_combinators-merge) 연결자 조치
- [-MergeState](# aggregate_functions_combinators-mergestate) 연결자 조치

## - 병합 {# aggregate_functions_combinators-merge}

이 연결자를 적용하면 aggregate 함수는 중간 집계 상태를 인수로 받아 국가를 결합하여 집계를 종료하고 결과 값을 반환합니다.

## -MergeState {# aggregate_functions_combinators-mergestate}

-Merge 연결자와 같은 방법으로 중간 집계 상태를 병합합니다. 그러나 결과 값을 돌려주는 것이 아니라 -State 연결자와 비슷한 중간 집계 상태를 반환합니다.

## -ForEach {# agg-functions-combinator-foreach}

테이블 집계 함수를 해당 배열 항목을 집계 한 결과 배열을 반환 배열의 집계 함수로 변환합니다. 예를 들어,`sumForEach` 배열의 경우 `[10, 13, 5]`대응하는 배열 항목 함께 추가 한 후 `[1, 2]``[3, 4, 5]`와`[6, 7]`결과를 반환합니다

## - 오르 기본 {# agg-functions-combinator-ordefault}

집계 함수의 동작을 변경합니다.

집계 함수에 입력 값이없는 경우이 연결자를 사용하면 반환 값의 데이터 유형의 기본값이 반환됩니다. 하늘의 입력 데이터를 취할 수있는 집계 함수에 적용됩니다.

`-OrDefault` 다른 조합 장치와 함께 사용될 수있다.

** 구문 **

```sql
<aggFunction> OrDefault (x)
```

** 파라미터 **

-`x` - Aggregate function parameters.

** 반환 값 **

집계하는 것이없는 경우는 집계 함수의 반환 형식의 기본값을 반환합니다.

형식은 사용되는 집계 함수에 따라 달라집니다.

** 예 **

쿼리 :

```sql
SELECT avg (number) avgOrDefault (number) FROM numbers (0)
```

결과 :

```text
┌─avg (number) ─┬─avgOrDefault (number) ─┐
│ nan │ 0 │
└─────────────┴──────────────────────┘
```

또한`-OrDefault` 다른 연결자와 함께 사용할 수 있습니다. 집계 함수가 빈 입력을 받아들이지 않는 경우에 유용합니다.

쿼리 :

```sql
SELECT avgOrDefaultIf (x, x> 10)
FROM
(
    SELECT toDecimal32 (1.23, 2) AS x
)
```

결과 :

```text
┌─avgOrDefaultIf (x, greater (x 10)) ─┐
│ 0.00 │
└───────────────────────────────────┘
```

## - 오루누루 {# agg-functions-combinator-ornull}

집계 함수의 동작을 변경합니다.

이 연결자 집계 함수의 결과를 [Null 가능](../data-types/nullable.md) 데이터 형식입니다. 집계 함수 계산하는 값이없는 경우 다음 값이 반환됩니다 [NULL](../syntax.md #null-literal).

`-OrNull` 다른 조합 장치와 함께 사용될 수있다.

** 구문 **

```sql
<aggFunction> OrNull (x)
```

** 파라미터 **

-`x` - Aggregate function parameters.

** 반환 값 **

- 집계 함수의 결과는 다음과 같이 변환됩니다. `Nullable` 데이터 형식입니다.
-`NULL` 집계하는 것이없는 경우.

유형 :`Nullable (aggregate function return type)`.

** 예 **

집계 함수의 마지막에 `-orNull`추가. 

쿼리 :

```sql
SELECT sumOrNull (number) toTypeName (sumOrNull (number)) FROM numbers (10) WHERE number> 10
```

결과 :

```text
┌─sumOrNull (number) ─┬─toTypeName (sumOrNull (number)) ─┐
│ ᴺᵁᴸᴸ │ Nullable (UInt64) │
└───────────────────┴───────────────────────────── ──┘
```

또한`-OrNull` 다른 연결자와 함께 사용할 수 있습니다. 집계 함수가 빈 입력을 받아들이지 않는 경우에 유용합니다.

쿼리 :

```sql
SELECT avgOrNullIf (x, x> 10)
FROM
(
    SELECT toDecimal32 (1.23, 2) AS x
)
```

결과 :

```text
┌─avgOrNullIf (x, greater (x 10)) ─┐
│ ᴺᵁᴸᴸ │
└────────────────────────────────┘
```

## - 리샘플링 {# agg-functions-combinator-resample}

데이터를 그룹으로 분할하고, 그 그룹의 데이터를 개별적으로 집계 할 수 있습니다. 그룹은 열 값을 간격으로 분할하여 만들어집니다.

```sql
<aggFunction> Resample (start, end, step) (<aggFunction_params> resampling_key)
```

** 파라미터 **

-`start` - Starting value of the whole required interval for`resampling_key` 값.
-`stop` - Ending value of the whole required interval for`resampling_key` 값. 전체 구간은 포함되어 있지 않습니다`stop` 값`[start, stop)`.
-`step` - Step for separating the whole interval into subintervals. The`aggFunction` 이러한 부분 구간의 각각에 대해 독립적으로 실행됩니다.
-`resampling_key` - Column whose values are used for separating data into intervals.
-`aggFunction_params` -`aggFunction` 변수.

** 반환 값 **

- 배열`aggFunction` 각 하위 간격의 결과.

** 예 **

생각`people` 다음 데이터를 포함하는 테이블 :

```text
┌─name───┬─age─┬─wage─┐
│ John │ 16 │ 10 │
│ Alice │ 30 │ 15 │
│ Mary │ 35 │ 8 │
│ Evelyn │ 48 │ 11.5 │
│ David │ 62 │ 9.9 │
│ Brian │ 60 │ 16 │
└────────┴─────┴──────┘
```

는 그 나이의 간격에있는 사람의 이름을 가져 봅시다`[30,60)`와`[60,75)`나이에 정수 표현을 사용하기 때문에 나이를 가져옵니다` [30, 59]`와`[60,74]`간격.

배열의 이름을 집계하려면 [그룹 배열](reference.md #agg_function-grouparray) 집계 함수. 그것은 하나의 인수를 취합니다. 우리의 경우, 그것은`name` 라인. 그`groupArrayResample` 함수는`age` 연령별로 이름을 집계하는 열. 필요한 간격을 정의하기 위해`30, 75, 30` 인수`groupArrayResample` 기능.

```sql
SELECT groupArrayResample (30, 75, 30) (name, age) FROM people
```

```text
┌─groupArrayResample (30, 75, 30) (name, age) ─────┐
│ [ 'Alice', 'Mary', 'Evelyn', 'David', 'Brian'] │
└───────────────────────────────────────────────┘
```

결과를 생각해 봅시다.

`Jonh` 그는 너무 어려 때문에 샘플의 외부에 있습니다. 다른 사람은 지정된 나이 간격에 따라 분배됩니다.

이번은 지정된 나이 간격으로 사람들의 총 수와 그 평균 임금을 계산합시다.

```sql
SELECT
    countResample (30, 75, 30) (name, age) AS amount,
    avgResample (30, 75, 30) (wage, age) AS avg_wage
FROM people
```

```text
┌─amount─┬─avg_wage──────────────────┐
│ [3,2] │ [11.5,12.949999809265137] │
└────────┴───────────────────────────┘
```

[원본 기사](https://clickhouse.tech/docs/en/query_language/agg_functions/combinators/) <! - hide ->
