---
machine_translated : true
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd
toc_priority : 63
toc_title : "Null \ u8A31 \ u5BB9 \ u5F15 \ u6570 \ u306E \ u64CD \ u4F5C"
---

# Null 허용 집계를 조작하는 함수 {# functions-for-working-with-nullable-aggregates}

## isNull {#isnull}

인수가 NULL (../../ sql-reference / syntax.md # null-literal).

```sql
isNull (x)
```

** 파라미터 **

-`x` - A value with a non-compound data type.

** 반환 값 **

-`1` 만약`x`는`NULL`.
-`0` 만약`x`가 아닌`NULL`.

** 예 **

입력 테이블

```text
┌─x─┬────y─┐
│ 1 │ ᴺᵁᴸᴸ │
│ 2 │ 3 │
└───┴──────┘
```

쿼리

```sql
SELECT x FROM t_null WHERE isNull (y)
```

```text
┌─x─┐
│ 1 │
└───┘
```

## isNotNull {#isnotnull}

인수가 NULL (../../ sql-reference / syntax.md # null-literal).

```sql
isNotNull (x)
```

** 파라미터 : **

-`x` - A value with a non-compound data type.

** 반환 값 **

-`0` 만약`x`는`NULL`.
-`1` 만약`x`가 아닌`NULL`.

** 예 **

입력 테이블

```text
┌─x─┬────y─┐
│ 1 │ ᴺᵁᴸᴸ │
│ 2 │ 3 │
└───┴──────┘
```

쿼리

```sql
SELECT x FROM t_null WHERE isNotNull (y)
```

```text
┌─x─┐
│ 2 │
└───┘
```

## 합체 {#coalesce}

왼쪽에서 오른쪽으로 확인 여부`NULL` 인수를 전달 처음 non를 돌려줍니다 -`NULL` 인수.

```sql
coalesce (x, ...)
```

** 파라미터 : **

- 비 복합 형의 숫자 ​​매개 변수. 모든 매개 변수에 대응하고있는 것이 필요한 데이터 형식입니다.

** 반환 값 **

- 최초의 비 -`NULL` 인수.
-`NULL` 모든 인수가`NULL`.

** 예 **

고객에게 연락하는 여러 방법을 지정할 수있는 연락처 목록을 검토하십시오.

```text
┌─name─────┬─mail─┬─phone─────┬──icq─┐
│ client 1 │ ᴺᵁᴸᴸ │ 123-45-67 │ 123 │
│ client 2 │ ᴺᵁᴸᴸ │ ᴺᵁᴸᴸ │ ᴺᵁᴸᴸ │
└──────────┴──────┴───────────┴──────┘
```

그`mail`와`phone` 필드는 String 형이지만,`icq` 필드는`UInt32` 따라서 변환해야합니다`String`.

연락처 목록에서 고객에게 먼저 사용 가능한 연락처를 얻는 방법 :

```sql
SELECT coalesce (mail, phone, CAST (icq 'Nullable (String)')) FROM aBook
```

```text
┌─name─────┬─coalesce (mail, phone, CAST (icq 'Nullable (String)')) ─┐
│ client 1 │ 123-45-67 │
│ client 2 │ ᴺᵁᴸᴸ │
└──────────┴────────────────────────────────────── ────────────────┘
```

## ifNull {#ifnull}

Main 인수가 다음의 경우 대체 값을 반환합니다`NULL`.

```sql
ifNull (x, alt)
```

** 파라미터 : **

-`x` - The value to check for`NULL`.
-`alt` - The value that the function returns if`x`는`NULL`.

** 반환 값 **

- 값`x`, if`x`가 아닌`NULL`.
- 값`alt`, if`x`는`NULL`.

** 예 **

```sql
SELECT ifNull ( 'a', 'b')
```

```text
┌─ifNull ( 'a', 'b') ─┐
│ a │
└──────────────────┘
```

```sql
SELECT ifNull (NULL, 'b')
```

```text
┌─ifNull (NULL, 'b') ─┐
│ b │
└───────────────────┘
```

## 누리후 {#nullif}

쯔づゥ쯔. `NULL` 인수가 동일한 경우.

```sql
nullIf (x, y)
```

** 파라미터 : **

`x`,`y` - Values ​​for comparison. They must be compatible types, or ClickHouse will generate an exception.

** 반환 값 **

-`NULL` 인수가 동일한 경우.
- 그`x` 인수가 동일하지 않은 경우의 값입니다.

** 예 **

```sql
SELECT nullIf (1, 1)
```

```text
┌─nullIf (1, 1) ─┐
│ ᴺᵁᴸᴸ │
└──────────────┘
```

```sql
SELECT nullIf (1, 2)
```

```text
┌─nullIf (1, 2) ─┐
│ 1 │
└──────────────┘
```

## assumeNotNull {#assumenotnull}

결과는 type의 값이됩니다 [Null 가능 (../../ sql-reference / data-types / nullable.md) 비를 위해 -`Nullable` 값이`NULL`.

```sql
assumeNotNull (x)
```

** 파라미터 : **

-`x` - The original value.

** 반환 값 **

- 비의 원래 값 -`Nullable` 그렇지 않은 경우에는 유형`NULL`.
- 비의 기본값 -`Nullable` 원래 값이`NULL`.

** 예 **

생각`t_null` 테이블.

```sql
SHOW CREATE TABLE t_null
```

```text
┌─statement─────────────────────────────────────────────── ──────────────────┐
│ CREATE TABLE default.t_null (x Int8, y Nullable (Int8)) ENGINE = TinyLog │
└───────────────────────────────────────────────── ──────────────────────────┘
```

```text
┌─x─┬────y─┐
│ 1 │ ᴺᵁᴸᴸ │
│ 2 │ 3 │
└───┴──────┘
```

을 적용하는`assumeNotNull` 대한 함수`y` 라인.

```sql
SELECT assumeNotNull (y) FROM t_null
```

```text
┌─assumeNotNull (y) ─┐
│ 0 │
│ 3 │
└──────────────────┘
```

```sql
SELECT toTypeName (assumeNotNull (y)) FROM t_null
```

```text
┌─toTypeName (assumeNotNull (y)) ─┐
│ Int8 │
│ Int8 │
└──────────────────────────────┘
```

## toNullable {#tonullable}

인수의 형태를 다음과 같이 변환합니다`Nullable`.

```sql
toNullable (x)
```

** 파라미터 : **

-`x` - The value of any non-compound type.

** 반환 값 **

- A를 가지는 입력 값`Nullable` 타입.

** 예 **

```sql
SELECT toTypeName (10)
```

```text
┌─toTypeName (10) ─┐
│ UInt8 │
└────────────────┘
```

```sql
SELECT toTypeName (toNullable (10))
```

```text
┌─toTypeName (toNullable (10)) ─┐
│ Nullable (UInt8) │
└────────────────────────────┘
```

원본 기사 (https://clickhouse.tech/docs/en/query_language/functions/functions_for_nulls/) <! - hide ->
