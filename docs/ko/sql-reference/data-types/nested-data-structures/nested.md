---
machine_translated : true
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd
toc_priority : 57
toc_title : "\ u30CD \ u30B9 \ u30C8 \ u3055 \ u308C \ u305F (Name1Type1, Name2Type2 ...)"
---

# Nested (name1 Type1, Name2 Type2, ...) {# nestedname1-type1-name2-type2}

A nested data structure is like a table inside a cell. The parameters of a nested data structure - the column names and types - are specified the same way as in a [CREATE TABLE (../../../ sql- reference / statements / create.md) 쿼리. 각 테이블의 행은 중첩 된 데이터 구조의 임의의 수의 행에 대응할 수 있습니다.

예 :

```sql
CREATE TABLE test.visits
(
    CounterID UInt32,
    StartDate Date,
    Sign Int8,
    IsNew UInt8,
    VisitID UInt64,
    UserID UInt64,
    ...
    Goals Nested
    (
        ID UInt32,
        Serial UInt32,
        EventTime DateTime,
        Price Int64,
        OrderID String,
        CurrencyID UInt32
    ),
    ...
) ENGINE = CollapsingMergeTree (StartDate, intHash32 (UserID) (CounterID, StartDate, intHash32 (UserID) VisitID) 8192, Sign)
```

이 예에서는`Goals` 전환에 대한 데이터를 포함하는 중첩 된 데이터 구조. 각 행 'visits'table은 제로 또는 숫자의 변환에 대응할 수 있습니다.

하나의 중첩 수준 만 지원됩니다. 배열을 포함하는 중첩 된 구조체의 열은 다차원 배열과 동일하므로 지원이 제한되어 있습니다 (MergeTree 엔진이 열을 테이블에 저장하는 지원

대부분의 경우 중첩 된 데이터 구조를 조작하는 경우, 그 열은 점으로 구분 된 열 이름으로 지정됩니다. 이러한 열은 일치하는 형태의 배열을 구성합니다. 하나의 중첩 된 데이터 구조의 모든 열 배열의 길이는 동일합니다.

예 :

```sql
SELECT
    Goals.ID,
    Goals.EventTime
FROM test.visits
WHERE CounterID = 101500 AND length (Goals.ID) <5
LIMIT 10
```

```text
┌─Goals.ID───────────────────────┬─Goals.EventTime───────────────── ────────────────────────────────────────────────── ────────┐
│ [1073752,591325,591325] │ [ '2014-03-17 16:38:10', '2014-03-17 16:38:48', '2014-03-17 16:42:27'] │
│ [1073752] │ [ '2014-03-17 00:28:25'] │
│ [1073752] │ [ '2014-03-17 10:46:20'] │
│ [1073752,591325,591325,591325] │ [ '2014-03-17 13:59:20', '2014-03-17 22:17:55', '2014-03-17 22:18:07' '2014-03-17 22:18:51'] │
│ [] │ [] │
│ [1073752,591325,591325] │ [ '2014-03-17 11:37:06', '2014-03-17 14:07:47', '2014-03-17 14:36:21'] │
│ [] │ [] │
│ [] │ [] │
│ [591325,1073752] │ [ '2014-03-17 00:46:05', '2014-03-17 00:46:05'] │
│ [1073752,591325,591325,591325] │ [ '2014-03-17 13:28:33', '2014-03-17 13:30:26', '2014-03-17 18:51:21' '2014-03-17 18:51:45'] │
└────────────────────────────────┴──────────────── ────────────────────────────────────────────────── ─────────────────────────┘
```

중첩 된 데이터 구조는 동일한 길이의 여러 열 배열의 집합이라고 생각하는 것이 가장 간단합니다.

SELECT 쿼리에서 각 컬럼이 아닌 중첩 된 데이터 구조 전체 이름을 지정할 수는 ARRAY JOIN 절뿐입니다. 자세한 내용은 "ARRAY JOIN clause"예 :

```sql
SELECT
    Goal.ID,
    Goal.EventTime
FROM test.visits
ARRAY JOIN Goals AS Goal
WHERE CounterID = 101500 AND length (Goals.ID) <5
LIMIT 10
```

```text
┌─Goal.ID─┬──────Goal.EventTime─┐
│ 1073752 │ 2014-03-17 16:38:10 │
│ 591325 │ 2014-03-17 16:38:48 │
│ 591325 │ 2014-03-17 16:42:27 │
│ 1073752 │ 2014-03-17 00:28:25 │
│ 1073752 │ 2014-03-17 10:46:20 │
│ 1073752 │ 2014-03-17 13:59:20 │
│ 591325 │ 2014-03-17 22:17:55 │
│ 591325 │ 2014-03-17 22:18:07 │
│ 591325 │ 2014-03-17 22:18:51 │
│ 1073752 │ 2014-03-17 11:37:06 │
└─────────┴─────────────────────┘
```

중첩 된 데이터 구조 전체에 대해 SELECT를 실행할 수 없습니다. 명시 적으로 나열 할 수있는 것은 그 일부인 개별 열뿐입니다.

INSERT 쿼리는 중첩 된 데이터 구조의 모든 구성 요소 열 배열을 개별적으로 전달해야합니다 (개별 열 배열과 마찬가지로). 삽입시에 같은 길이 인 것이 확인됩니다.

DESCRIBE 쿼리의 경우 중첩 된 데이터 구조의 열은 동일한 방식으로 별도로 표시됩니다.

중첩 된 데이터 구조의 요소 ALTER 쿼리에 제한이 있습니다.

원본 기사 (https://clickhouse.tech/docs/en/data_types/nested_data_structures/nested/) <! - hide ->
