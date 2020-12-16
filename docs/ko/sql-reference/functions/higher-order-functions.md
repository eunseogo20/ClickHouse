---
machine_translated : true
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd
toc_priority : 57
toc_title : "\ u9AD8 \ u6B21"
---

# 고차 함수 {# higher-order-functions}

##`->`연산자, lambda (params, expr) 함수 {# operator-lambdaparams-expr-function}

Allows describing a lambda function for passing to a higher-order function. The left side of the arrow has a formal parameter, which is any ID, or multiple formal parameters - any IDs in a tuple. The right side of the arrow has an expression that can use these formal parameters, as well as any table columns.

예 :`x -> 2 * x, str -> str! = Referer.`

고차 함수는 함수의 인수로 람다 함수만을 받아 들일 수 있습니다.

여러 인수를 받아들이는 lambda 함수는 고차 함수에 전달할 수 있습니다. 이 경우 고차 함수는 이러한 인수가 지원하는 동일한 길이의 몇 가지 배열이 전달됩니다.

다음과 같은 일부 기능은 [arrayCount (# higher_order_functions-array-count) 또는 아레이사무 (# higher_order_functions-array-count) 첫 번째 인수 (람다 함수)는 생략 할 수 있습니다. 이 경우 동일한 사상이 가정된다.

Lambda 함수는 다음의 함수는 생략 할 수 없습니다.

- [arrayMap (# higher_order_functions-array-map)
- [arrayFilter (# higher_order_functions-array-filter)
- [arrayFill (# higher_order_functions-array-fill)
- [arrayReverseFill (# higher_order_functions-array-reverse-fill)
- [arraySplit (# higher_order_functions-array-split)
- [arrayReverseSplit (# higher_order_functions-array-reverse-split)
- [arrayFirst (# higher_order_functions-array-first)
- [arrayFirstIndex (# higher_order_functions-array-first-index)

### arrayMap (func, arr1, ...) {# higher_order_functions-array-map}

원래 응용 프로그램에서 얻은 배열을 돌려줍니다. `func`의 각 요소에 대한 함수`arr` 배열

예 :

```sql
SELECT arrayMap (x -> (x + 2), 1, 2, 3]) as res;
```

```text
┌─res─────┐
│ [3,4,5] │
└─────────┘
```

다른 배열에서 요소의 튜플을 만드는 방법을 보여줍니다 :

```sql
SELECT arrayMap ((x, y) -> (x, y), 1, 2, 3, 4, 5, 6]) AS res
```

```text
┌─res─────────────────┐
│ [(1,4), (2,5), (3,6) │
└─────────────────────┘
```

첫 번째 인수 (람다 함수)는 생략 할 수 없다는 점에 유의하십시오. `arrayMap` 기능.

### arrayFilter (func, arr1, ...) {# higher_order_functions-array-filter}

요소만을 포함한 배열을 돌려줍니다. `arr1` 그래서`func` 0 이외의 것을 돌려줍니다.

예 :

```sql
SELECT arrayFilter (x -> x LIKE '% World %', 'Hello', 'abc World']) AS res
```

```text
┌─res───────────┐
│ [ 'abc World'] │
└───────────────┘
```

```sql
SELECT
    arrayFilter (
        (i, x) -> x LIKE '% World %',
        arrayEnumerate (arr)
        [ 'Hello', 'abc World'] AS arr)
    AS res
```

```text
┌─res─┐
│ [2] │
└─────┘
```

첫 번째 인수 (람다 함수)는 생략 할 수 없다는 점에 유의하십시오. `arrayFilter` 기능.

### arrayFill (func, arr1, ...) {# higher_order_functions-array-fill}

스캔을 통해`arr1` 최초의 요소로부터 마지막 ​​요소로 바꿉니다`arr1 [i]`에 의해`arr1 [i - 1]`만약`func` 0을 반환합니다. 첫 번째 요소`arr1` 교환되지 않습니다.

예 :

```sql
SELECT arrayFill (x -> not isNull (x), 1, null, 3, 11, 12, null, null, 5, 6, 14, null, null) AS res
```

```text
┌─res──────────────────────────────┐
│ [1,1,3,11,12,12,12,5,6,14,14,14] │
└──────────────────────────────────┘
```

첫 번째 인수 (람다 함수)는 생략 할 수 없다는 점에 유의하십시오. `arrayFill` 기능.

### arrayReverseFill (func, arr1, ...) {# higher_order_functions-array-reverse-fill}

스캔을 통해`arr1` 마지막 요소에서 첫 번째 요소로 바꿉니다`arr1 [i]`에 의해`arr1 [i + 1]`만약`func` 0을 반환합니다. 마지막 요소`arr1` 교환되지 않습니다.

예 :

```sql
SELECT arrayReverseFill (x -> not isNull (x), 1, null, 3, 11, 12, null, null, 5, 6, 14, null, null) AS res
```

```text
┌─res────────────────────────────────┐
│ [1,3,3,11,12,5,5,5,6,14, NULL, NULL] │
└────────────────────────────────────┘
```

첫 번째 인수 (람다 함수)는 생략 할 수 없다는 점에 유의하십시오. `arrayReverseFill` 기능.

### arraySplit (func, arr1, ...) {# higher_order_functions-array-split}

분할`arr1` 여러 배열. 때`func` 0 이외의 것을 반환하면 배열은 요소의 왼쪽에 분할됩니다. 배열은 첫 번째 요소 앞에 분할되지 않습니다.

예 :

```sql
SELECT arraySplit ((x, y) -> y, 1, 2, 3, 4, 5, 1, 0, 0, 1, 0) AS res
```

```text
┌─res─────────────┐
│ [1,2,3], [4,5] │
└─────────────────┘
```

첫 번째 인수 (람다 함수)는 생략 할 수 없다는 점에 유의하십시오. `arraySplit` 기능.

### arrayReverseSplit (func, arr1, ...) {# higher_order_functions-array-reverse-split}

분할`arr1` 여러 배열. 때`func` 0 이외의 것을 반환하면 배열은 요소의 오른쪽에 분할됩니다. 배열은 마지막 요소 다음에 분할되지 않습니다.

예 :

```sql
SELECT arrayReverseSplit ((x, y) -> y, 1, 2, 3, 4, 5, 1, 0, 0, 1, 0) AS res
```

```text
┌─res───────────────┐
│ [1] [2,3,4], [5] │
└───────────────────┘
```

첫 번째 인수 (람다 함수)는 생략 할 수 없다는 점에 유의하십시오. `arraySplit` 기능.

### arrayCount (\ func \] arr1, ...) {# higher_order_functions-array-count}

Func이 0 이외의 것을 반환 arr 배열의 요소의 수를 돌려줍니다. 만약 'func'지정되지 않으면 배열의 제로 이외의 요소의 수를 돌려줍니다.

### arrayExists (\ func \] arr1, ...) {# arrayexistsfunc-arr1}

적어도 하나의 요소가있는 경우는 1을 반환합니다 'arr'그래서 'func'0 이외의 것을 돌려줍니다. 그렇지 않으면 0을 반환합니다.

### arrayAll (\ func \] arr1, ...) {# arrayallfunc-arr1}

경우 1을 반환합니다 'func'의 모든 요소에 대해 0이 아닌 것을 돌려줍니다 'arr'그렇지 않으면 0을 반환합니다.

### arraySum (\ func \] arr1, ...) {# higher-order-functions-array-sum}

의 합계를 반환합니다. 'func'값. 함수를 생략하면 배열 요소의 합계가 반환됩니다.

### arrayFirst (func, arr1, ...) {# higher_order_functions-array-first}

첫 번째 요소를 반환합니다. 'arr1'이에 대한 배열 'func'0 이외의 것을 돌려줍니다.

첫 번째 인수 (람다 함수)는 생략 할 수 없다는 점에 유의하십시오. `arrayFirst` 기능.

### arrayFirstIndex (func, arr1, ...) {# higher_order_functions-array-first-index}

첫 번째 요소의 인덱스를 돌려줍니다. 'arr1'이에 대한 배열 'func'0 이외의 것을 돌려줍니다.

첫 번째 인수 (람다 함수)는 생략 할 수 없다는 점에 유의하십시오. `arrayFirstIndex` 기능.

### arrayCumSum (\ func \] arr1, ...) {# arraycumsumfunc-arr1}

소스 배열의 요소의 부분 일식 (실행 합계)의 배열을 돌려줍니다. 만약`func` 함수가 지정되면, 배열 요소의 값은 총하기 전에이 함수에 의해 변환됩니다.

예 :

```sql
SELECT arrayCumSum (1, 1, 1, 1]) AS res
```

```text
┌─res──────────┐
│ [1, 2, 3, 4] │
└──────────────┘
```

### arrayCumSumNonNegative (arr) {#arraycumsumnonnegativearr}

같은`arrayCumSum` 소스 배열의 요소의 부분합의 배열을 돌려줍니다 (실행 합계). 다른`arrayCumSum` 반환 된 값에 제로 미만의 값이 포함되어있는 경우, 값은 제로 대체되고 계산은 제로 파라미터에서 실행됩니다. 예를 들면 :

```sql
SELECT arrayCumSumNonNegative (1, 1, -4, 1]) AS res
```

```text
┌─res───────┐
│ [1,2,0,1] │
└───────────┘
```

### arraySort (\ func \] arr1, ...) {# arraysortfunc-arr1}

요소를 정렬 된 결과 배열을 반환합니다`arr1` 오름차순으로. 만약`func` 함수가 지정된 정렬 순서는 함수의 결과에 의해 결정됩니다`func` 배열 (배열)의 요소에 적용됩니다)

그 [슈워츠 변환 (https://en.wikipedia.org/wiki/Schwartzian_transform) 분류의 효율성을 개선하는 데 사용됩니다.

예 :

```sql
SELECT arraySort ((x, y) -> y, 'hello', 'world', 2, 1]);
```

```text
┌─res────────────────┐
│ [ 'world', 'hello'] │
└────────────────────┘
```

자세한 내용은`arraySort` 방법은 참조하십시오 [배열을 조작하기위한 함수 (array-functions.md # array_functions-sort) 섹션

### arrayReverseSort (\ func \] arr1, ...) {# arrayreversesortfunc-arr1}

요소를 정렬 된 결과 배열을 반환합니다`arr1` 내림차순으로. 만약`func` 함수가 지정된 정렬 순서는 함수의 결과에 의해 결정됩니다`func` 배열 (배열)의 요소에 적용됩니다.

예 :

```sql
SELECT arrayReverseSort ((x, y) -> y, 'hello', 'world', 2, 1]) as res;
```

```text
┌─res───────────────┐
│ [ 'hello', 'world'] │
└───────────────────┘
```

자세한 내용은`arrayReverseSort` 방법은 참조하십시오 [배열을 조작하기위한 함수 (array-functions.md # array_functions-reverse-sort) 섹션

원본 기사 (https://clickhouse.tech/docs/en/query_language/functions/higher_order_functions/) <! - hide ->
