---
machine_translated : true
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd
toc_priority : 34
toc_title : "\ u30B5 \ u30DF \ u30F3 \ u30B0 \ u30DE \ u30FC \ u30B2 \ u30C4 \ u30EA \ u30FC"
---

# 합산 마 게임 트리 {#summingmergetree}

엔진에서 상속합니다 [메루게쯔리 (mergetree.md # table_engines-mergetree) 차이는 데이터 부분을 병합 할 때`SummingMergeTree` 테이블 ClickHouse 모든 행을 동일한 기본 키 (또는 더 정확하게 같은 키)로 바꿉니다 [정렬 키 (mergetree.md)) 숫자 데이터 형식의 열 집계 값을 갖는 행. 정렬 키가 단일 키 값이 여러 행에 대응하도록 구성되어 있으면 스토리지 용량을 크게 절감하고 데이터 선택 속도가 빨라집니다.

엔진을 함께 사용하는 것이 좋습니다`MergeTree` 완전한 데이터를 저장하는`MergeTree` 테이블 및 사용`SummingMergeTree` 예를 들어, 보고서 준비시 등 집계 데이터를 저장하는 경우. 이러한 접근을 방지 마라 귀중한 데이터에 따라 올바르게 구성이 유효한 유형을 사용합니다.

## 테이블 만들기 {# creating a-table}

```sql
CREATE TABLE [IF NOT EXISTS] [db] table_name [ON CLUSTER cluster]
(
    name1 [type1] [DEFAULT | MATERIALIZED | ALIAS expr1,
    name2 [type2] [DEFAULT | MATERIALIZED | ALIAS expr2]
    ...
) ENGINE = SummingMergeTree (columns)
[PARTITION BY expr]
[ORDER BY expr]
[SAMPLE BY expr]
[SETTINGS name = value ...]
```

설명 요청 매개 변수의 참조 요청에 대한 설명 (../../../ sql-reference / statements / create.md).

** Sumingmergetree 매개 변수 **

-`columns` - 값이 집계되는 열의 이름을 가진 튜플. 모든 매개 변수.
    열은 숫자 여야합니다. 기본 키에 포함 할 필요가 있습니다.

    만약`columns` 지정되지 않은 ClickHouse 기본 키에없는 숫자 데이터 형식을 가진 모든 열의 값을 집계합니다.

** 쿼리 절 **

을 만들 때`SummingMergeTree` 같은 테이블 [어구] (mergetree.md)을 만들 때처럼 필요합니다. `MergeTree` 테이블.

<details markdown = "1">

<summary> 추천되고 있지 않습니다 법률 테이블을 만들 </ summary>

!!! attention "주의"
    가능하면 이전 프로젝트를 위의 방법으로 전환하십시오.

```sql
CREATE TABLE [IF NOT EXISTS] [db] table_name [ON CLUSTER cluster]
(
    name1 [type1] [DEFAULT | MATERIALIZED | ALIAS expr1,
    name2 [type2] [DEFAULT | MATERIALIZED | ALIAS expr2]
    ...
) ENGINE [=] SummingMergeTree (date-column [, sampling_expression] (primary, key), index_granularity [columns)
```

다음을 제외한 모든 매개 변수`columns`와 같은 의미를 가진`MergeTree`.

-`columns` - tuple with names of columns values ​​of which will be summarized. Optional parameter. For a description, see the text above.

</ details>

## 예 {# usage-example}

다음 표를 생각합니다 :

```sql
CREATE TABLE summtt
(
    key UInt32,
    value UInt32
)
ENGINE = SummingMergeTree ()
ORDER BY key
```

데이터를 삽입하기 :

```sql
INSERT INTO summtt Values ​​(1,1), (1,2), (2,1)
```

ClickHouse은 완전하지 모든 행을 합계 수 있습니다 (아래 참조] (# data-processing)) 때문에 집계 함수를 사용합니다`sum`와`GROUP BY` 쿼리 내의 절.

```sql
SELECT key, sum (value) FROM summtt GROUP BY key
```

```text
┌─key─┬─sum (value) ─┐
│ 2 │ 1 │
│ 1 │ 3 │
└─────┴────────────┘
```

## 데이터 처리 {# data-processing}

데이터가 테이블에 삽입되면 그대로 저장됩니다. ClickHouse 데이터의 삽입 된 부분을 정기적으로 병합하고이 같은 기본 키를 가진 행이 합산되어 결과 데이터의 각 부분에 대해 라인에서 대체

ClickHouse can merge the data parts so that different resulting parts of data cat consist rows with the same primary key, ie the summation will be incomplete. Therefore (`SELECT`) 집계 함수 [화 () (../../. ./sql-reference/aggregate-functions/reference.md#agg_function-sum)와`GROUP BY` 위의 예에서 설명했듯이 쿼리에서 어구를 사용해야합니다.

### 총의 일반적인 규칙 {# common-rules-for-summation}

숫자 데이터 형식의 열 값이 집계됩니다. 열 집합은 매개 변수에 의해 정의됩니다`columns`.

총의 모든 열의 값이 0이면 행은 삭제됩니다.

Column이 기본 키에없고 집계되지 않은 경우에는 기존 값에서 임의의 값이 선택됩니다.

기본 키 열의 값은 집계되지 않습니다.

### Aggregatefunction 열의 합계 {# the-summation-in-the-aggregatefunction-columns}

열에 [AggregateFunction 유형 (../../../ sql-reference / data-types / aggregatefunction.md) ClickHouse으로 행동으로 간주 [AggregatingMergeTree (aggregatingmergetree.md) 기능에 따라 집계하는 엔진.

### 중첩 {# nested-structures}

테이블에 중첩 된 데이터 구조 및 가공 "라고되어 있습니다.

중첩 된 테이블의 이름이`Map` 또한 다음 조건 적어도 두 개의 열이 포함되어 있습니다 :

- 최초의 열은 숫자입니다`(* Int *, Date, DateTime)`또는 문자열`(String, FixedString)`그것을 부릅시다`key`,
- 다른 열은 산술입니다`(* Int * Float32 / 64)`그것을 부릅시다`(values ​​...)`

그런 다음이 중첩 된 테이블은`key => (values ​​...)`행을 병합 할 때 두 데이터 집합의 요소는`key` 대응하는 총과`(values ​​...) `.

예 :

```text
[(1, 100) + (2, 150) -> (1, 100), (2, 150)
[(1, 100) + (1 150) -> (1, 250)
[(1, 100) + (1 150), (2, 150) -> (1 250), (2, 150)
[(1, 100), (2, 150) + (1 -100) -> (2, 150)
```

데이터를 요청할 때, sumMap (키, 값) (../../../ sql-reference / aggregate-functions / reference.md)의 집합을위한 함수`Map`.

중첩 된 데이터 구조의 경우 합계를 위해 열 튜플에 열을 지정할 필요가 없습니다.

원본 기사 (https://clickhouse.tech/docs/en/operations/table_engines/summingmergetree/) <! - hide ->
