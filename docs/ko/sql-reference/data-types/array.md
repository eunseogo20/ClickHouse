---
machine_translated : true
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd
toc_priority : 51
toc_title : "\ u914D \ u5217 (T)"
---

# 배열 (t) {# data-type-array}

배열`T`- 유형 항목. `T` 배열을 포함한 모든 데이터 형식을 지정할 수 있습니다.

## 배열 만들기 {# creating-an-array}

함수를 사용하여 배열을 만들 수 있습니다 :

```sql
array (T)
```

대괄호를 사용 할 수 있습니다.

```sql
[]
```

배열 만들기 예 :

```sql
SELECT array (1, 2) AS x, toTypeName (x)
```

```text
┌─x─────┬─toTypeName (array (1, 2)) ─┐
│ [1,2] │ Array (UInt8) │
└───────┴─────────────────────────┘
```

```sql
SELECT [1, 2] AS x, toTypeName (x)
```

```text
┌─x─────┬─toTypeName ([1, 2]) ─┐
│ [1,2] │ Array (UInt8) │
└───────┴────────────────────┘
```

## 데이터 형의 조작 {# working-with-data-types}

즉시 배열을 만들 때 ClickHouse은 나열된 모든 인수를 포함 할 수있는 가장 좁은 데이터 형식으로 인수 형식을 자동으로 정의합니다. 중 하나가있는 경우 [Null 가능 (nullable.md # data_type-nullable) 또는 리터럴 [NULL] (../../ sql-reference / syntax.md # null-literal) 값을 지정하면 배열 요소의 형태도 다음과 같습니다 [Null 가능 (nullable.md).

ClickHouse 데이터 형식을 판별 할 수없는 경우 예외가 생성됩니다. 예를 들어, 이것은 문자열과 숫자를 동시에 배열을 만들려고 할 때 발생합니다 (`SELECT array (1, 'a')`).

자동 데이터 형 감지 예 :

```sql
SELECT array (1, 2, NULL) AS x, toTypeName (x)
```

```text
┌─x──────────┬─toTypeName (array (1, 2, NULL)) ─┐
│ [1,2, NULL] │ Array (Nullable (UInt8)) │
└────────────┴───────────────────────────────┘
```

호환되지 않는 데이터 형식의 배열을 만들려고하면 ClickHouse는 예외를 throw합니다 :

```sql
SELECT array (1, 'a')
```

```text
Received exception from server (version 1.1.54388) :
Code : 386. DB :: Exception : Received from localhost : 9000, 127.0.0.1 : DB :: Exception : There is no supertype for types UInt8, String because some of them are String / FixedString and some of them are not.
```

원본 기사 (https://clickhouse.tech/docs/en/data_types/array/) <! - hide ->
