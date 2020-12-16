--- 
machine_translated : true 
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd 
toc_priority : 43 
toc_title : "\ u6761 \ u4EF6 \ u4ED8 \ u304D" 
--- 

# 조건 함수 {# conditional-functions} 

## 만약 {#if} 

조건 분기를 제어합니다. 과는 달리 대부분의 시스템 ClickHouse 항상 평가의 두 표현`then`와`else`. 

** 구문 ** 

```sql 
SELECT if (cond, then, else) 
``` 

조건`cond` 0이 아닌 값 평가 된 식의 결과를 반환합니다`then` 식의 결과`else` 존재하는 경우는 생략됩니다. 만약`cond` 제로 또는`NULL`의 결과는`then` 식은 생략되어`else` expression이있는 경우 expression이 반환됩니다. 

** 파라미터 ** 

-`cond` - The condition for evaluation that can be zero or not . The type is UInt8, Nullable (UInt8) or NULL. 
-`then` - 조건이 충족 된 경우에 반환되는 식입니다.
-`else` - 조건이 충족되지 않은 경우에 반환되는 식입니다. 

** 반환 값 ** 

이 함수는 실행됩니다`then`와`else` 식은 조건 여부에 따라 그 결과를 반환합니다`cond` 제로 여부가되어 버렸습니다. 

** 예 ** 

쿼리 : 

```sql 
SELECT if (1 plus (2, 2), plus (2, 6)) 
``` 

결과 : 

```text 
┌─plus (2, 2) ─┐ 
│ 4 │ 
└────────────┘ 
``` 

쿼리 : 

```sql 
SELECT if (0, plus (2, 2), plus (2, 6)) 
``` 

결과 : 

` ``text 
┌─plus (2, 6) ─┐ 
│ 8 │ 
└────────────┘ 
``` 

-`then`와`else` 일반적인 유형이 가장 낮은해야합니다. 

** 예 ** 

이 걸릴`LEFT_RIGHT` 테이블 : 

```sql 
SELECT * 
FROM LEFT_RIGHT

┌─left─┬─right─┐ 
│ ᴺᵁᴸᴸ │ 4 │ 
│ 1 │ 3 │ 
│ 2 │ 2 │ 
│ 3 │ 1 │ 
│ 4 │ ᴺᵁᴸᴸ │ 
└──────┴───────┘ 
``` 

다음 쿼리는 비교합니다`left`와`right` 값 : 

```sql 
SELECT 
    left, 
    right, 
    if (left <right 'left is smaller than right', 'right is greater or equal than left ') AS is_smaller 
FROM LEFT_RIGHT 
WHERE isNotNull (left) AND isNotNull (right) 

┌─left─┬─right─┬─is_smaller─────────────────────── ───┐ 
│ 1 │ 3 │ left is smaller than right │ 
│ 2 │ 2 │ right is greater or equal than left │ 
│ 3 │ 1 │ right is greater or equal than left │ 
└──────┴───────┴─────────────── ──────────────────────┘ 
```

참고 :`NULL`이 예제에서는 값은 사용되지 않습니다. 조건부 NULL 값 (# null-values-in-conditionals) 섹션 

## 삼항 연산자 {# ternary-operator} 

그것은 같이 일하는`if` 기능. 

구문 :`cond? then : else` 

쯔づゥ쯔. `then` 만약`cond` true (제로보다 큰) 평가됩니다. `else`. 

-`cond`의 형태가 아니면 안됩니다`UInt8`, and`then`와`else` 일반적인 유형이 가장 낮은해야합니다. 

-`then`와`else` 수 있습니다`NULL` 

** 참조. ** 

- [ifNotFinite (other-functions.md # ifnotfinite) 

## multif {#multiif} 

당신이 쓸 수 있습니다 [CASE] (../ operators / index.md # operator_case)보다 컴팩트하게 쿼리 연산자. 

구문 :`multiIf (cond_1, then_1, cond_2, then_2, ..., else)` 

** 파라미터 : ** 
-`else` - The result of the function if none of the conditions is met.
 
-`cond_N` - The condition for the function to return`then_N`.
-`then_N` - The result of the function when executed.

이 함수는`2N + 1` 변수. 

** 반환 값 ** 

함수는 다음 값 중 하나를 반환합니다`then_N` 또는`else` 조건에 따라`cond_N`. 

** 예 ** 

다시 사용하는`LEFT_RIGHT` 테이블. 

```sql 
SELECT 
    left, 
    right, 
    multiIf (left <right 'left is smaller'left> right 'left is greater'left = right 'Both equal', 'Null value'



조건은 항상 다음과 같습니다`0``1` 또는`NULL`. 수 있으므로 사용 조건과 결과를 직접 이런 : 

```sql 
SELECT left <right AS is_small 
FROM LEFT_RIGHT 

┌─is_small─ ┐ 
│ ᴺᵁᴸᴸ │ 
│ 1 │ 
│ 0 │ 
│ 0 │ 
│ ᴺᵁᴸᴸ │ 
└──────────┘ 
``` 

## 조건부 nULL 값 {# null-values-in-conditionals} 

때` NULL` 값은 조건에 포함 된 결과도 다음과 같습니다`NULL`. 

```sql 
SELECT 
    NULL <1, 
    2 <NULL, 
    NULL <NULL, 
    NULL = NULL
 
┌─less (NULL 1) ─┬─less (2, NULL) ─┬─less (NULL, NULL) ─┬─equals (NULL, NULL) ─┐
│ ᴺᵁᴸᴸ │ ᴺᵁᴸᴸ │ ᴺᵁᴸᴸ │ ᴺᵁᴸᴸ │ 
└───────────────┴───────────────┴──────── ──────────┴────────────────────┘ 
``` 

그 구축 문의 하십의 경우 유형`Nullable`. 

다음 예를 들어, equals 조건 추가에 실패하여이를 나타냅니다`multiIf`. 

```sql 
SELECT 
    left, 
    right, 
    multiIf (left <right 'left is smaller'left> right 'right is smaller' 'Both equal') AS faulty_result 
FROM LEFT_RIGHT 

┌─left─┬─right─┬─faulty_result────┐ 
│ ᴺᵁᴸᴸ │ 4 │ Both equal │ 
│ 1 │ 3 │ left is smaller │ 
│ 2 │ 2 │ Both equal │ 
│ 3 │ 1 │ right is smaller │ 
│ 4 │ ᴺᵁᴸᴸ │ Both equal │
└──────┴───────┴──────────────────┘ 
``` 

원래 기사 (https : // clickhouse. tech / docs / en / query_language / functions / conditional_functions /) <! - hide ->
