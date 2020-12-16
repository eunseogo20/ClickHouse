---
machine_translated : true
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd
toc_priority : 46
toc_title : "\ u914D \ u5217 \ u306E \ u64CD \ u4F5C"
---

# 배열을 조작하기위한 함수 {# functions-for-working-with-arrays}

## 하늘 {# function-empty}

하늘의 배열의 경우 1 비어 있지 않은 배열의 경우는 0을 돌려줍니다.
결과 형식은 UInt8입니다.
이 함수는 문자열에 대해서도 작동합니다.

## 노텐뿌티 {# function-notempty}

하늘의 배열의 경우는 0, 비어 있지 않은 배열의 경우는 1을 돌려줍니다.
결과 형식은 UInt8입니다.
이 함수는 문자열에 대해서도 작동합니다.

## 길이 {# array_functions-length}

배열의 항목 수를 반환합니다.
결과 형식은 UInt64입니다.
이 함수는 문자열에 대해서도 작동합니다.

## emptyArrayUInt8, emptyArrayUInt16, emptyArrayUInt32, emptyArrayUInt64 {# emptyarrayuint8-emptyarrayuint16-emptyarrayuint32-emptyarrayuint64}

## emptyArrayInt8, emptyArrayInt16, emptyArrayInt32, emptyArrayInt64 {# emptyarrayint8-emptyarrayint16-emptyarrayint32-emptyarrayint64}

## emptyArrayFloat32, emptyArrayFloat64 {# emptyarrayfloat32-emptyarrayfloat64}

## emptyArrayDate, emptyArrayDateTime {# emptyarraydate-emptyarraydatetime}

## emptyArrayString {#emptyarraystring}

제로 인수를 적절한 형식의 빈 배열을 반환합니다.

## 엠프 티 어 라이트 싱글 {#emptyarraytosingle}

빈 배열을 받아 기본값과 동일한 한 요소 배열을 돌려줍니다.

## 범위 (종료), 범위 (시작, 종료 \ 단계 \]) {# rangeend-rangestart-end-step}

시작에서 종료 -1까지의 숫자 배열을 단계별로 돌려줍니다.
인수가`start` 기본값은 0입니다.
인수가`step` 기본값은 1입니다.
그것은 pythonic처럼 작동합니다`range`. 그러나 차이는 모든 인수의 형식은`UInt` 숫자 다
경우에 따라서는 총 길이가 100 백만 이상의 요소의 배열이 데이터 블록에 생성되면 예외가 throw됩니다.

## array (x1, ...), operator \ [x1, ... \] {# arrayx1-operator-x1}

함수의 인수 배열을 만듭니다.
인수는 상수이며, 최소의 공통 형을 가지는 형태를 가질 필요가 있습니다. 그렇지 않으면 만들 배열의 형태가 명확하지 않기 때문입니다. 즉,이 함수를 사용하여 빈 배열을 만들 수 없습니다 (그렇게는 'emptyArray \ *'이상의 기능).
를 반환 'Array (T)' "result"를 입력합니다. 'T'전달 된 인수 중 최소의 공통 형식입니다.

## arrayConcat {#arrayconcat}

인수로 전달 된 배열을 결합합니다.

```sql
arrayConcat (arrays)
```

** 파라미터 **

-`arrays` - Arbitrary number of arguments of [배열] (../../ sql-reference / data-types / array.md) 타입.
    ** 예 **

<! - ->

```sql
SELECT arrayConcat (1, 2, 3, 4, 5, 6]) AS res
```

```text
┌─res───────────┐
│ [1,2,3,4,5,6] │
└───────────────┘
```

## arrayElement (arr, n) 연산자 arr \ n \] {# arrayelementarr-n-operator-arrn}

인덱스를 가지는 요소를 가져옵니다`n` 배열에서`arr``n` 임의의 정수이어야합니다.
배열의 인덱스는 배열에서 시작됩니다.
음의 색인이 지원됩니다. 이 경우 끝에서 번호가 매겨진 대응하는 요소를 선택한다. 예를 들어,`arr [-1]`배열의 마지막 항목입니다.

인덱스가 배열 범위 밖에있는 경우, 디폴트 값 (수치의 경우는 0, 문자열의 경우 빈 문자열)을 반환합니다. 비 정수 배열과 정수 인덱스 0의 경우를 제외합니다 (이 경우 오류가 발생합니다`Array indices are 1-based`).

## has (arr, elem) {# hasarr-elem}

확인 여부 'arr'배열 'elem'요소.
요소가 배열 내에없는 경우는 0을 반환 요소가 배열 내에없는 경우는 1을 돌려줍니다.

`NULL` 값으로 처리됩니다.

```sql
SELECT has (1, 2, NULL, NULL)
```

```text
┌─has (1, 2, NULL, NULL) ─┐
│ 1 │
└─────────────────────────┘
```

## hasAll {#hasall}

있는 배열이 다른 배열의 하위 집합인지 여부를 확인합니다.

```sql
hasAll (set, subset)
```

** 파라미터 **

-`set` - Array of any type with a set of elements.
-`subset` - Array of any type with elements that should be tested to be a subset of`set`.

** 반환 값 **

-`1`, if`set` 모든 요소가 포함되어 있습니다`subset`.
-`0` 그렇지 않으면

** 독특한 특성 **

- 하늘의 배열은 임의의 배열의 일부입니다.
-`Null` 값으로 처리됩니다.
- 두 배열의 값의 순서는 중요하지 않습니다.

** 예 **

`SELECT hasAll ([], [])`1을 반환합니다.

`SELECT hasAll (1, Null, Null)`1을 반환합니다.

`SELECT hasAll (1.0, 2, 3, 4, 1, 3])`1을 반환합니다.

`SELECT hasAll ( 'a', 'b', 'a'])`1을 반환합니다.

`SELECT hasAll ([1], [ 'a'])`0을 반환합니다.

`SELECT hasAll ([1, 2, 3, 4], [1, 2, 3, 5])`0을 반환합니다.

## 하사니 {#hasany}

여부를 판별 두 배열이 서로의 교차점에있다.

```sql
hasAny (array1, array2)
```

** 파라미터 **

-`array1` - Array of any type with a set of elements.
-`array2` - Array of any type with a set of elements.

** 반환 값 **

-`1`, if`array1`와`array2` 적어도 하나의 같은 요소가 있습니다.
-`0` 그렇지 않으면

** 독특한 특성 **

-`Null` 값으로 처리됩니다.
- 두 배열의 값의 순서는 중요하지 않습니다.

** 예 **

`SELECT hasAny ([1], [])`쯔づゥ쯔. `0`.

`SELECT hasAny (Null, Null 1])`쯔づゥ쯔. `1`.

`SELECT hasAny (- 128 1. 512] [1])`쯔づゥ쯔. `1`.

`SELECT hasAny ([1, 2, 3, 4], 'a', 'c'])`쯔づゥ쯔. `0`.

`SELECT hasAll ([1, 2, 3, 4], [1, 2], [1, 2])`쯔づゥ쯔. `1`.

## 인덱스 (arr, x) {# indexofarr-x}

최초의 인덱스를 돌려줍니다 'x'배열 내에있는 경우는 요소 (1부터 시작), 그렇지 않은 경우는 0.

예 :

```sql
SELECT indexOf (1, 3, NULL, NULL, NULL)
```

```text
┌─indexOf (1, 3, NULL, NULL, NULL) ─┐
│ 3 │
└───────────────────────────────────┘
```

에 설정된 요소`NULL` 정상 값으로 처리됩니다.

## 카운트 장 루이 (arr, x) {# countequalarr-x}

X와 동일한 배열의 요소의 수를 돌려줍니다. arrayCount (elem - \> elem = x, arr)와 동등합니다.

`NULL` 요소는 개별 값으로 처리됩니다.

예 :

```sql
SELECT countEqual (1, 2, NULL, NULL, NULL)
```

```text
┌─countEqual (1, 2, NULL, NULL, NULL) ─┐
│ 2 │
└──────────────────────────────────────┘
```

## 아레 (arr) {# array_functions-arrayenumerate}

Returns the array \ 1, 2, 3, ..., length (arr) \]

이 함수는 일반적으로 배열 결합에 사용됩니다. 배열 결합을 적용한 후 배열마다 한 번만 뭔가를 셀 수 있습니다. 예 :

```sql
SELECT
    count () AS Reaches,
    countIf (num = 1) AS Hits
FROM test.hits
ARRAY JOIN
    GoalsReached,
    arrayEnumerate (GoalsReached) AS num
WHERE CounterID = 160656
LIMIT 10
```

```text
┌─Reaches─┬──Hits─┐
│ 95606 │ 31406 │
└─────────┴───────┘
```

이 예에서는 Reach는 전환 수 (배열 결합을 적용한 후받은 문자열) Hits는 페이지 뷰 (배열 결합 전의 문자열)입니다. 이 특정 경우에는 동일한 결과를 쉽게 얻을 수 있습니다 :

```sql
SELECT
    sum (length (GoalsReached)) AS Reaches,
    count () AS Hits
FROM test.hits
WHERE (CounterID = 160656) AND notEmpty (GoalsReached)
```

```text
┌─Reaches─┬──Hits─┐
│ 95606 │ 31406 │
└─────────┴───────┘
```

이 함수는 고차 함수도 사용할 수 있습니다. 예를 들어, 조건에 일치하는 요소의 배열 인덱스를 얻기 위해 사용할 수 있습니다.

## arrayEnumerateUniq (arr ...) {#arrayenumerateuniqarr}

소스 배열과 같은 크기의 배열을 돌려줍니다.
예를 들어, arrayEnumerateUniq (\ 10, 20, 10, 30 \]) = \ 1, 1, 2, 1 \].

이 함수는 배열 조인 배열 요소의 집계를 사용하는 경우에 유용합니다.
예 :

```sql
SELECT
    Goals.ID AS GoalID,
    sum (Sign) AS Reaches,
    sumIf (Sign, num = 1) AS Visits
FROM test.visits
ARRAY JOIN
    Goals,
    arrayEnumerateUniq (Goals.ID) AS num
WHERE CounterID = 160656
GROUP BY GoalID
ORDER BY Reaches DESC
LIMIT 10
```

```text
┌──GoalID─┬─Reaches─┬─Visits─┐
│ 53225 │ 3214 │ 1097 │
│ 2825062 │ 3188 │ 1097 │
│ 56600 │ 2803 │ 488 │
│ 1989037 │ 2401 │ 365 │
│ 2830064 │ 2396 │ 910 │
│ 1113562 │ 2372 │ 373 │
│ 3270895 │ 2262 │ 812 │
│ 1084657 │ 2262 │ 345 │
│ 56599 │ 2260 │ 799 │
│ 3271094 │ 2256 │ 812 │
└─────────┴─────────┴────────┘
```

이 예에서는 각 골 ID는 전환 수 (골의 중첩 된 데이터 구조의 각 요소는 도달 한 골이며,이를 전환이라고합니다)과 접하고 배열 결합이 없으면 세션 수를 sum (Sign)으로 집계되었다. 그러나이 특정한 경우에 행에 중첩 된 Goals 구조가 곱셈되어 있기 때문에,이 후 각 세션을 한꺼번에 계산하기 위해 arrayEnumerateUniq (Goals.ID) 기능.

ArrayEnumerateUniq 함수는 인수와 같은 크기의 여러 배열을 취할 수 있습니다. 이 경우 모든 배열의 같은 위치에있는 요소의 튜플에 대해 고유성이 고려됩니다.

```sql
SELECT arrayEnumerateUniq (1, 1, 1, 2, 2, 2, 1, 1, 2, 1, 1, 2]) AS res
```

```text
┌─res───────────┐
│ [1,2,1,1,2,1] │
└───────────────┘
```

이것은 중첩 된 데이터 구조로 배열 조인을 사용하여이 구조의 여러 요소간에 더욱 집계하는 경우에 필요합니다.

## arrayPopBack {#arraypopback}

배열에서 마지막 항목을 삭제합니다.

```sql
arrayPopBack (array)
```

** 파라미터 **

-`array` - Array입니다.

** 예 **

```sql
SELECT arrayPopBack (1, 2, 3]) AS res
```

```text
┌─res───┐
│ [1,2] │
└───────┘
```

## arrayPopFront {#arraypopfront}

배열에서 첫 번째 항목을 삭제합니다.

```sql
arrayPopFront (array)
```

** 파라미터 **

-`array` - Array입니다.

** 예 **

```sql
SELECT arrayPopFront (1, 2, 3]) AS res
```

```text
┌─res───┐
│ [2,3] │
└───────┘
```

## arrayPushBack {#arraypushback}

배열의 마지막에 항목을 추가합니다.

```sql
arrayPushBack (array, single_value)
```

** 파라미터 **

-`array` - Array입니다.
-`single_value` - A single value. Only numbers can be added to an array with numbers, and only strings can be added to an array of strings. When adding numbers, ClickHouse automatically sets the`single_value` 배열의 데이터 형의 형태. ClickHouse 데이터 유형에 대한 자세한 내용은 다음을 참조하십시오 "데이터 형식 (../../ sql-reference / data-types / index.md # data_types)"수`NULL `이 함수는`NULL` 요소를 배열로 변환하고 배열 요소의 형태를 다음과 같이 변환합니다`Nullable`.

** 예 **

```sql
SELECT arrayPushBack ( 'a', 'b') AS res
```

```text
┌─res───────┐
│ [ 'a', 'b'] │
└───────────┘
```

## 어레이 푸시 프런트 {#arraypushfront}

배열의 선두에 요소를 추가합니다.

```sql
arrayPushFront (array, single_value)
```

** 파라미터 **

-`array` - Array입니다.
-`single_value` - A single value. Only numbers can be added to an array with numbers, and only strings can be added to an array of strings. When adding numbers, ClickHouse automatically sets the`single_value` 배열의 데이터 형의 형태. ClickHouse 데이터 유형에 대한 자세한 내용은 다음을 참조하십시오 "데이터 형식 (../../ sql-reference / data-types / index.md # data_types)"수`NULL `이 함수는`NULL` 요소를 배열로 변환하고 배열 요소의 형태를 다음과 같이 변환합니다`Nullable`.

** 예 **

```sql
SELECT arrayPushFront ( 'b', 'a') AS res
```

```text
┌─res───────┐
│ [ 'a', 'b'] │
└───────────┘
```

## arrayResize {#arrayresize}

배열의 길이를 변경합니다.

```sql
arrayResize (array, size [, extender)
```

** 파라미터 : **

-`array` - Array입니다.
-`size` - Required length of the array.
    - 만약`size` 배열의 원래 크기보다 작은 경우, 배열은 오른쪽에서 잘립니다.
- 만약`size` 배열의 초기 크기보다 큰 경우, 배열은 다음과 같이 오른쪽으로 확장됩니다`extender` 배열 항목의 데이터 형식 값이나 기본값입니다.
-`extender` - Value for extending an array. Can be`NULL`.

** 반환 값 : **

길이의 배열`size`.

** 콜 예 **

```sql
SELECT arrayResize (1, 3)
```

```text
┌─arrayResize (1, 3) ─┐
│ [1,0,0] │
└─────────────────────┘
```

```sql
SELECT arrayResize (1, 3, NULL)
```

```text
┌─arrayResize (1, 3, NULL) ─┐
│ [1, NULL, NULL] │
└───────────────────────────┘
```

## 어레이 슬라이스 {#arrayslice}

배열 슬라이스를 반환합니다.

```sql
arraySlice (array, offset [, length])
```

** 파라미터 **

-`array` - Array of data.
-`offset` - Indent from the edge of the array. A positive value indicates an offset on the left, and a negative value is an indent on the right. Numbering of the array items begins with 1.
-`length` - 필요한 조각의 길이. 음수 값을 지정하면 함수는 열려있는 슬라이스를 반환합니다`[offset, array_length - length)`. 값을 생략하면 함수는 슬라이스를 반환합니다`[offset, the_end_of_array]`.

** 예 **

```sql
SELECT arraySlice (1, 2, NULL, 4, 5, 2, 3) AS res
```

```text
┌─res────────┐
│ [2, NULL 4] │
└────────────┘
```

배열 요소로 설정`NULL` 정상 값으로 처리됩니다.

## arraySort (\ func \] arr ...) {# array_functions-sort}

요소를 정렬합니다`arr` 오름차순 배열. 만약`func` 함수가 지정되면 정렬 순서는`func` 배열의 요소에 적용되는 함수. 만약`func` 여러 인수를 받아 들인다. `arraySort` 함수는 인수의 배열이 여러 전달됩니다. `func`에 대응한다. 자세한 예는 말에 나와 있습니다`arraySort` 설명.

정수 값 정렬의 예 :

```sql
SELECT arraySort (1, 3, 3, 0);
```

```text
┌─arraySort (1, 3, 3, 0) ─┐
│ [0,1,3,3] │
└─────────────────────────┘
```

문자열 값의 정렬의 예 :

```sql
SELECT arraySort ( 'hello', 'world', '!');
```

```text
┌─arraySort ( 'hello', 'world', '!') ─┐
│ [ '!', 'hello', 'world'] │
└────────────────────────────────────┘
```

다음의 정렬 순서를 생각하자`NULL``NaN`와`Inf` 값 :

```sql
SELECT arraySort (1 nan 2, NULL, 3, nan -4, NULL inf, -inf]);
```

```text
┌─arraySort (1 nan 2, NULL, 3, nan -4, NULL inf, -inf) ─┐
│ [-inf, -4,1,2,3, inf, nan, nan, NULL, NULL] │
└───────────────────────────────────────────────── ──────────┘
```

-`-Inf` 값은 배열의 맨 앞에 있습니다.
-`NULL` 값은 배열의 마지막에 있습니다.
-`NaN` 값은 직전입니다`NULL`.
-`Inf` 값은 직전입니다`NaN`.

또한`arraySort`은 [고차 함수 (higher-order-functions.md) Lambda 함수를 첫 번째 인수로 전달할 수 있습니다. 이 경우 정렬 순서 배열의 요소에 적용된 lambda 함수의 결과에 의해 결정됩니다.

다음의 예를 생각해 봅시다 :

```sql
SELECT arraySort ((x) -> -x, 1, 2, 3]) as res;
```

```text
┌─res─────┐
│ [3,2,1] │
└─────────┘
```

For each element of the source array, the lambda function returns the sorting key, that is, \ 1 - \> -1, 2 - \> -2, 3 - \> -3 \]. Since the`arraySort` 함수 오름차순 키를 정렬하고, 결과는 \ [3,2,1 \]입니다. 따라서`(x) -> -x` 람다 함수는 내림차순 (# array_functions-reverse-sort) 정렬에서.

Lambda 함수는 여러 인수를받을 수 있습니다. 이 경우`arraySort` 함수 lambda 함수의 인수가 지원하는 동일한 길이의 몇 가지 배열. 결과 배열은 첫 번째 입력 배열의 요소로 구성되어 다음의 입력 배열의 요소는 정렬 키를 지정합니다. 예를 들면 :

```sql
SELECT arraySort ((x, y) -> y, 'hello', 'world', 2, 1]) as res;
```

```text
┌─res────────────────┐
│ [ 'world', 'hello'] │
└────────────────────┘
```

여기에서는 두 번째 배열 (\ [2,1 \)에 전달되는 요소는 소스 배열의 대응하는 요소의 정렬 키를 정의합니다 (\ 'hello', 'world'\ ') 그것은 \ 'hello'- \> 2 'world'- \> 1 \]. Since the lambda function does not use`x` 소스 배열의 실제 값은 결과의 순서에 영향을주지 않습니다. 그래서, 'hello'결과의 두 번째 요소입니다. 'world'처음입니다.

다른 예는 다음과 같다.

```sql
SELECT arraySort ((x, y) -> y [0, 1, 2, 'c', 'b', 'a') as res;
```

```text
┌─res─────┐
│ [2,1,0] │
└─────────┘
```

```sql
SELECT arraySort ((x, y) -> -y [0, 1, 2, 1, 2, 3]) as res;
```

```text
┌─res─────┐
│ [2,1,0] │
└─────────┘
```

!!! note "주"
    효율성을 분류하는 것을 개선하기 위해 [슈워츠 변환 (https://en.wikipedia.org/wiki/Schwartzian_transform)가 사용된다.

## arrayReverseSort (\ func \] arr ...) {# array_functions-reverse-sort}

요소를 정렬합니다`arr` 감소 배열. 만약`func` 함수를 지정합니다`arr` 결과에 따라 정렬됩니다. `func` 함수는 배열의 요소에 적용되어 그 후, 정렬 된 배열이 반대가됩니다. 만약`func` 여러 인수를 받아 들인다. `arrayReverseSort` 함수는 인수의 배열이 여러 전달됩니다. `func`에 대응한다. 자세한 예는 말에 나와 있습니다`arrayReverseSort` 설명.

정수 값 정렬의 예 :

```sql
SELECT arrayReverseSort (1, 3, 3, 0);
```

```text
┌─arrayReverseSort (1, 3, 3, 0) ─┐
│ [3,3,1,0] │
└────────────────────────────────┘
```

문자열 값의 정렬의 예 :

```sql
SELECT arrayReverseSort ( 'hello', 'world', '!');
```

```text
┌─arrayReverseSort ( 'hello', 'world', '!') ─┐
│ [ 'world', 'hello', '!'] │
└───────────────────────────────────────────┘
```

다음의 정렬 순서를 생각하자`NULL``NaN`와`Inf` 값 :

```sql
SELECT arrayReverseSort (1 nan 2, NULL, 3, nan -4, NULL inf, -inf) as res;
```

```text
┌─res───────────────────────────────────┐
│ [inf, 3,2,1, -4, -inf, nan, nan, NULL, NULL] │
└───────────────────────────────────────┘
```

-`Inf` 값은 배열의 맨 앞에 있습니다.
-`NULL` 값은 배열의 마지막에 있습니다.
-`NaN` 값은 직전입니다`NULL`.
-`-Inf` 값은 직전입니다`NaN`.

또한`arrayReverseSort`은 [고차 함수 (higher-order-functions.md) Lambda 함수를 첫 번째 인수로 전달할 수 있습니다. 예를 나타낸다.

```sql
SELECT arrayReverseSort ((x) -> -x, 1, 2, 3]) as res;
```

```text
┌─res─────┐
│ [1,2,3] │
└─────────┘
```

배열은 다음과 같이 정렬됩니다 :

1. 첫째로, 소스 배열 (\ [1,2,3 \]) 배열의 요소에 적용된 lambda 함수의 결과에 따라 정렬됩니다. 결과는 배열 \ [3,2,1 \]입니다.
2. 이전 단계에서 취득 된 배열은 반대입니다. 따라서 최종 결과는 \ [1,2,3 \]입니다.

Lambda 함수는 여러 인수를받을 수 있습니다. 이 경우`arrayReverseSort` 함수 lambda 함수의 인수가 지원하는 동일한 길이의 몇 가지 배열. 결과 배열은 첫 번째 입력 배열의 요소로 구성되어 다음의 입력 배열의 요소는 정렬 키를 지정합니다. 예를 들면 :

```sql
SELECT arrayReverseSort ((x, y) -> y, 'hello', 'world', 2, 1]) as res;
```

```text
┌─res───────────────┐
│ [ 'hello', 'world'] │
└───────────────────┘
```

이 예제에서는 배열은 다음과 같이 정렬됩니다 :

1. 먼저 소스 배열 (\ 'hello', 'world'\]) 배열의 요소에 적용된 lambda 함수의 결과에 따라 정렬됩니다. 두 번째 배열 (\ [2,1 \)에 전달되는 요소는 소스 배열의 대응하는 요소의 정렬 키를 정의합니다. 결과는 배열입니다 \ 'world', 'hello'\ '.
2. 이전 단계에서 정렬 된 배열은 반대입니다. 그래서 최종 결과는 \ 'hello', 'world'\ '.

다른 예는 다음과 같다.

```sql
SELECT arrayReverseSort ((x, y) -> y, 4, 3, 5, 'a', 'b', 'c']) AS res;
```

```text
┌─res─────┐
│ [5,3,4] │
└─────────┘
```

```sql
SELECT arrayReverseSort ((x, y) -> -y, 4, 3, 5, 1, 2, 3]) AS res;
```

```text
┌─res─────┐
│ [4,3,5] │
└─────────┘
```

## arrayUniq (arr ...) {#arrayuniqarr}

하나의 인수가 전달되면 배열 내의 다른 요소의 수가 계산됩니다.
여러 인수가 전달되면 여러 배열의 해당 위치에있는 요소의 다른 튜플의 수를 계산합니다.

배열 내의 고유 항목의 목록을 검색하려면 arrayReduce을 사용할 수 있습니다 ( 'groupUniqArray', arr).

## 어레이 조인 (arr) {# array-functions-join}

특별한 기능. 섹션을 참조 [ "ArrayJoin function"(array-join.md # functions_arrayjoin).

## arrayDifference {#arraydifference}

인접 배열 요소의 차이를 계산합니다. 첫 번째 요소가 0으로되는 배열을 돌려줍니다. `a [1] - a [0]`etc. The type of elements in the resulting array is determined by the type inference rules for subtraction (eg`UInt8` -`UInt8` =`Int16`).

** 구문 **

```sql
arrayDifference (array)
```

** 파라미터 **

-`array` - [배열] (https://clickhouse.tech/docs/en/data_types/array/).

** 반환 값 **

인접한 요소 사이의 차이의 배열을 돌려줍니다.

유형 : UInt \ * (https://clickhouse.tech/docs/en/data_types/int_uint/#uint-ranges), Int \ * (https://clickhouse.tech/docs/en/data_types/ int_uint / # int-ranges), 플로트 \ * (https://clickhouse.tech/docs/en/data_types/float/).

** 예 **

쿼리 :

```sql
SELECT arrayDifference (1, 2, 3, 4)
```

결과 :

```text
┌─arrayDifference (1, 2, 3, 4) ─┐
│ [0,1,1,1] │
└───────────────────────────────┘
```

결과 형식 Int64 의한 오버 플로우의 예 :

쿼리 :

```sql
SELECT arrayDifference (0 10000000000000000000)
```

결과 :

```text
┌─arrayDifference (0 10000000000000000000) ─┐
│ [0, -8446744073709551616] │
└────────────────────────────────────────────┘
```

## 어레이 디스트 {#arraydistinct}

배열을 받아 별도의 요소만을 포함한 배열을 돌려줍니다.

** 구문 **

```sql
arrayDistinct (array)
```

** 파라미터 **

-`array` - [배열] (https://clickhouse.tech/docs/en/data_types/array/).

** 반환 값 **

개별 요소를 포함한 배열을 돌려줍니다.

** 예 **

쿼리 :

```sql
SELECT arrayDistinct (1, 2, 2, 3, 1)
```

결과 :

```text
┌─arrayDistinct (1, 2, 2, 3, 1) ─┐
│ [1,2,3] │
└────────────────────────────────┘
```

## 어레이 뉴 메 라틴 세 (arr) {# array_functions-arrayenumeratedense}

소스 배열과 같은 크기의 배열을 돌려줍니다.

예 :

```sql
SELECT arrayEnumerateDense (10, 20, 10, 30)
```

```text
┌─arrayEnumerateDense (10, 20, 10, 30) ─┐
│ [1,2,1,3] │
└───────────────────────────────────────┘
```

## 어레이 인터 섹트 (arr) {# array-functions-arrayintersect}

여러 배열을 가지고 모든 소스 배열에있는 요소를 포함한 배열을 돌려줍니다. 결과의 배열 내의 요소의 순서는 첫 번째 배열과 동일합니다.

예 :

```sql
SELECT
    arrayIntersect (1, 2, 1, 3, 2, 3]) AS no_intersect,
    arrayIntersect (1, 2, 1, 3, 1, 4]) AS intersect
```

```text
┌─no_intersect─┬─intersect─┐
│ [] │ [1] │
└──────────────┴───────────┘
```

## arrayReduce {#arrayreduce}

집계 함수를 배열 요소에 적용하고 그 결과를 반환합니다. 집계 함수의 이름은 작은 따 quotes 문자열로 전달됩니다` 'max'``'sum'` 파라 메트릭 집계 함수를 사용하는 경우, 매개 변수는 함수 이름 뒤에 괄호로 표시됩니다` ' uniqUpTo (6) '`.

** 구문 **

```sql
arrayReduce (agg_func, arr1, arr2, ..., arrN)
```

** 파라미터 **

-`agg_func` - The name of an aggregate function which should be a constant [문자열] (../../ sql-reference / data-types / string.md).
-`arr` - Any number of [배열] (../../ sql-reference / data-types / array.md) 집계 함수의 매개 변수로 문자열을 입력합니다.

** 반환 값 **

** 예 **

```sql
SELECT arrayReduce ( 'max', 1, 2, 3])
```

```text
┌─arrayReduce ( 'max', 1, 2, 3]) ─┐
│ 3 │
└───────────────────────────────┘
```

집계 함수가 복수의 인수를 취하는 경우,이 함수는 동일한 크기의 여러 배열에 적용해야합니다.

```sql
SELECT arrayReduce ( 'maxIf', 3, 5, 1, 0)
```

```text
┌─arrayReduce ( 'maxIf', 3, 5, 1, 0) ─┐
│ 3 │
└──────────────────────────────────────┘
```

파라 메트릭 집계 함수의 예 :

```sql
SELECT arrayReduce ( 'uniqUpTo (3)', 1, 2, 3, 4, 5, 6, 7, 8, 9, 10)
```

```text
┌─arrayReduce ( 'uniqUpTo (3)', 1, 2, 3, 4, 5, 6, 7, 8, 9, 10) ─┐
│ 4 │
└───────────────────────────────────────────────── ────────────┘
```

## arrayReduceInRanges {#arrayreduceinranges}

지정된 범위의 배열 요소에 집계 함수를 적용하여 각 범위에 해당하는 결과를 포함한 배열을 돌려줍니다. 함수는 multiple와 같은 결과를 반환합니다`arrayReduce (agg_func, arraySlice (arr1, index, length), ...)`.

** 구문 **

```sql
arrayReduceInRanges (agg_func, ranges, arr1, arr2, ..., arrN)
```

** 파라미터 **

-`agg_func` - The name of an aggregate function which should be a constant [문자열] (../../ sql-reference / data-types / string.md).
-`ranges` - The ranges to aggretate which should be an [배열] (../../ sql-reference / data-types / array.md)의 튜플 (../../ sql-reference / data-types / tuple.md) 여기에는 각 범위의 인덱스 및 길이가 포함됩니다.
-`arr` - Any number of [배열] (../../ sql-reference / data-types / array.md) 집계 함수의 매개 변수로 문자열을 입력합니다.

** 반환 값 **

** 예 **

```sql
SELECT arrayReduceInRanges (
    'sum'
    [(1, 5), (2, 3), (3, 4), (4, 4),
    [1000000, 200000, 30000, 4000, 500, 60, 7]
) AS res
```

```text
┌─res─────────────────────────┐
│ [1234500,234000,34560,4567] │
└─────────────────────────────┘
```

## 어레이 리버스 (arr) {#arrayreverse}

역순으로 요소를 포함한 원래의 배열과 같은 크기의 배열을 돌려줍니다.

예 :

```sql
SELECT arrayReverse (1, 2, 3])
```

```text
┌─arrayReverse (1, 2, 3]) ─┐
│ [3,2,1] │
└─────────────────────────┘
```

## 리버스 (arr) {# array-functions-reverse}

동의어 [ "arrayReverse"(# arrayreverse)

## 어레이 플래시 텐 {#arrayflatten}

배열의 배열을 평면 배열로 변환합니다.

함수 :

- 중첩 된 배열의 임의의 깊이에 적용됩니다.
- 이미 평면 배열은 변경하지 않습니다.

평탄화 된 배열을 포함한 모든 요소를 ​​모든 소스 배열.

** 구문 **

```sql
flatten (array_of_arrays)
```

일명`flatten`.

** 파라미터 **

-`array_of_arrays` - [배열] (../../ sql-reference / data-types / array.md) 배열. 예를 들어,`[1,2,3], [4,5]`.

** 예 **

```sql
SELECT flatten ([1], [2], [3]]])
```

```text
┌─flatten (array (array ([1]), array ([2] [3]))) ─┐
│ [1,2,3] │
└─────────────────────────────────────────────┘
```

## 어레이 컴팩트 {#arraycompact}

배열에서 연속적인 중복 요소를 제거합니다. 결과 값의 순서는 소스 배열의 순서에 의해 결정됩니다.

** 구문 **

```sql
arrayCompact (arr)
```

** 파라미터 **

`arr` - The [배열] (../../ sql-reference / data-types / array.md) 검사한다.

** 반환 값 **

고유 한 배열.

유형 :`Array`.

** 예 **

쿼리 :

```sql
SELECT arrayCompact (1, 1, nan, nan, 2, 3, 3, 3)
```

결과 :

```text
┌─arrayCompact (1, 1, nan, nan, 2, 3, 3, 3) ─┐
│ [1 nan, nan 2,3] │
└────────────────────────────────────────────┘
```

## arrayZip {#arrayzip}

여러 배열을 하나의 배열에 결합합니다. 결과 배열 인수의 순서로 쌍으로 그룹화 된 소스 배열의 대응하는 요소가 포함되어 있습니다.

** 구문 **

```sql
arrayZip (arr1, arr2, ..., arrN)
```

** 파라미터 **

-`arrN` - [배열] (../ data-types / array.md).

함수는 다른 형식의 숫자 배열을 취할 수 있습니다. 모든 입력 배열은 같은 크기이어야합니다.

** 반환 값 **

- 소스 배열의 요소를 그룹화 한 배열 [튜플 (../ data-types / tuple.md) 튜플의 데이터 형식은 입력 배열의 형태와 동일한 배열을 전달하는 순서와 동일합니다.

유형 : 배열 (../ data-types / array.md).

** 예 **

쿼리 :

```sql
SELECT arrayZip ( 'a', 'b', 'c', 5, 2, 1])
```

결과 :

```text
┌─arrayZip ( 'a', 'b', 'c', 5, 2, 1]) ─┐
│ [( 'a', 5), ( 'b', 2), ( 'c', 1) │
└──────────────────────────────────────┘
```

## 어레이 오크 {#arrayauc}

계산 AUC (기계 학습의 개념이다 곡선 아래의 면적 자세한 내용을 참조하십시오 : https : //en.wikipedia.org/wiki/Receiver_operating_characteristic#Area_under_the_curve).

** 구문 **

```sql
arrayAUC (arr_scores, arr_labels)
```

** 파라미터 **
-`arr_scores` - scores prediction model gives.
-`arr_labels` - labels of samples, usually 1 for positive sample and 0 for negtive sample.

** 반환 값 **
FLOAT64 형의 AUC 값을 반환합니다.

** 예 **
쿼리 :

```sql
select arrayAUC (0.1, 0.4, 0.35, 0.8, 0, 0, 1, 1)
```

결과 :

```text
┌─arrayAUC (0.1, 0.4, 0.35, 0.8, 0, 0, 1, 1) ─┐
│ 0.75 │
└──────────────────────────────────────── --- ──┘
```

원본 기사 (https://clickhouse.tech/docs/en/query_language/functions/array_functions/) <! - hide ->
