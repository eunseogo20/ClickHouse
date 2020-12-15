---
machine_translated : true
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd
toc_priority : 37
toc_title : "\ u30D0 \ u30FC \ u30B8 \ u30E7 \ u30CB \ u30F3 \ u30B0 \ u30B3 \ u30E9 \ u30D7 \ u30B7 \ u30F3 \
  \ u30B0 \ u30DE \ u30FC \ u30B2 \ u30C3 \ u30C8 \ u30EA \ u30FC "
---

# 버전 관리 코라뿌신구 마 겟 리 {#versionedcollapsingmergetree}

이 엔진 :

- 그럼 빨리 쓰기 객체는 항상 변화하고 있습니다.
- 삭제 이전 객체 상태의 배경입니다. 따라서 저장된 양이 크게 감소됩니다.

섹션을 참조 [붕괴 (# table_engines_versionedcollapsingmergetree) 자세한 내용은.

엔진에서 상속합니다 [메루게쯔리 (mergetree.md # table_engines-mergetree) 추가 한 논리 붕괴 행 알고리즘을위한 통합 데이터 부품입니다. `VersionedCollapsingMergeTree`과 같은 목적을 수행하고 있습니다 접기 병합 트리 (collapsingmergetree.md)가 다른 붕괴 알고리즘을 삽입하고 데이터를 임의의 순서로 여러 스레드). 특히`Version` 열은 잘못된 순서로 삽입 되어도 행을 적절하게 축소하는 데 도움이됩니다. 대조적으로,`CollapsingMergeTree` 엄격하게 연속 삽입 만 허용합니다.

## 테이블 만들기 {# creating a-table}

```sql
CREATE TABLE [IF NOT EXISTS] [db] table_name [ON CLUSTER cluster]
(
    name1 [type1] [DEFAULT | MATERIALIZED | ALIAS expr1,
    name2 [type2] [DEFAULT | MATERIALIZED | ALIAS expr2]
    ...
) ENGINE = VersionedCollapsingMergeTree (sign, version)
[PARTITION BY expr]
[ORDER BY expr]
[SAMPLE BY expr]
[SETTINGS name = value ...]
```

쿼리 매개 변수에 대한 설명은 쿼리 설명] (../../../ sql-reference / statements / create.md).

** 엔진 변수 **

```sql
VersionedCollapsingMergeTree (sign, version)
```

-`sign` - Name of the column with the type of row :`1`은 "state"행`-1`은 "cancel"로우

    열의 데이터 형식은 다음과 같습니다`Int8`.

-`version` - Name of the column with the version of the object state.

    열의 데이터 형식은 다음과 같습니다`UInt *`.

** 쿼리 절 **

을 만들 때`VersionedCollapsingMergeTree` 테이블 같은 [어구] (mergetree.md)을 만들 때 필요합니다. `MergeTree` 테이블.

<details markdown = "1">

<summary> 추천되고 있지 않습니다 법률 테이블을 만들 </ summary>

!!! attention "주의"
    용도로는 사용하지 마십시오 방법으로 새 ​​프로젝트 가능하면 이전 프로젝트를 위의 방법으로 전환합니다.

```sql
CREATE TABLE [IF NOT EXISTS] [db] table_name [ON CLUSTER cluster]
(
    name1 [type1] [DEFAULT | MATERIALIZED | ALIAS expr1,
    name2 [type2] [DEFAULT | MATERIALIZED | ALIAS expr2]
    ...
) ENGINE [=] VersionedCollapsingMergeTree (date-column [, samp # table_engines_versionedcollapsingmergetreeling_expression] (primary, key), index_granularity, sign, version)
```

다음을 제외한 모든 매개 변수`sign`와`version`와 같은 의미를 가진`MergeTree`.

-`sign` - Name of the column with the type of row :`1`은 "state"행`-1`은 "cancel"로우

    Column Data Type -`Int8`.

-`version` - Name of the column with the version of the object state.

    열의 데이터 형식은 다음과 같습니다`UInt *`.

</ details>

## 붕괴 {#table_engines_versionedcollapsingmergetree}

### 데이터 {#data}

우나 상황을 생각할 필요가있는 경우 저장 지속적으로 변화하는 데이터 객체입니다. 개체에 대해 하나의 행을 가져, 변경이있을 때마다 그 행을 업데이트하는 것이 좋습니다. 그러나 DBMS에서는 스토리지의 데이터를 갱신해야하기 때문에 업데이트 작업은 비용과 시간이 소요됩니다. 업데이트는 없지만 필요한 경우 기록 데이터는 이미 쓰기 수정 같은 순서로하고 있습니다.

사용하는`Sign` 줄을 쓸 때의 열. 만약`Sign = 1` 이것은 행이 객체의 상태임을 의미합니다 ( "state"행). 만약`Sign = -1`이 같은 특성을 가진 개체의 상태의 취소를 나타냅니다 ( "cancel"행). 또한`Version` 객체의 각 상태를 고유 번호로 식별해야합니다.

예를 들어, 사용자가 어떤 사이트를 방문했는지, 얼마나 시간이 있었는지를 계산합니다. 어떤 시점에서 다음 줄을 사용자 활동 상태에서 설명합니다 :

```text
┌──────────────UserID─┬─PageViews─┬─Duration─┬─Sign─┬─Version─┐
│ 4324182021466249494 │ 5 │ 146 │ 1 │ 1 |
└─────────────────────┴───────────┴──────────┴──── ──┴─────────┘
```

후 어느 시점에서 사용자 활동의 변경을 등록하고 다음 두 행에서 그것을 씁니다.

```text
┌──────────────UserID─┬─PageViews─┬─Duration─┬─Sign─┬─Version─┐
│ 4324182021466249494 │ 5 │ 146 │ -1 │ 1 |
│ 4324182021466249494 │ 6 │ 185 │ 1 │ 2 |
└─────────────────────┴───────────┴──────────┴──── ──┴─────────┘
```

첫 번째 줄은 개체 (사용자)의 이전 상태를 취소합니다. 취소 된 상태의 모든 필드를 복사합니다. `Sign`.

다음 줄에는 현재의 상태가 포함됩니다.

사용자 활동의 마지막 상태 만이 필요하므로 행

```text
┌──────────────UserID─┬─PageViews─┬─Duration─┬─Sign─┬─Version─┐
│ 4324182021466249494 │ 5 │ 146 │ 1 │ 1 |
│ 4324182021466249494 │ 5 │ 146 │ -1 │ 1 |
└─────────────────────┴───────────┴──────────┴──── ──┴─────────┘
```

개체의 잘못된 (이전) 상태를 축소하여 제거 할 수 있습니다. `VersionedCollapsingMergeTree`이 것을 융합시킨 데이터의 부품입니다.

변경마다 두 행이 필요한 이유에 대해서는 다음을 참조하십시오 [알고리즘] (# table_engines-versionedcollapsingmergetree-algorithm).

** 사용상의주의 **

1. 프로그램을 쓰고 데이터 뜻 개체를 해제합니다. 그 "cancel"복사해야합니다. "state"반대 문자열`Sign`이 증가 초기 사이즈로 저장이 가능한 데이터를 기록한다.
2. 열 긴 성장 배열은 쓰기 부하를 위해 엔진의 효율을 저하시킨다. 더 간단한 데이터 더 나은 효율성.
3.`SELECT` 결과 개체 변경 내용의 일관성에 크게 의존합니다. 삽입을위한 데이터를 준비 할 때 정확합니다. 세션의 깊이 등 부가 아닌 지표의 음수 등 일관성없는 데이터에서 예측할 수없는 결과를 얻을 수 있습니다.

### 알고리즘 {# table_engines-versionedcollapsingmergetree-algorithm}

ClickHouse 데이터 파트를 병합 할 때 같은 기본 키 및 버전과 다른 행의 각 쌍을 제거합니다`Sign`. 행의 순서는 중요하지 않습니다.

ClickHouse는 데이터를 삽입 할 때 기본 키에 의해 행을 정렬합니다. 만약`Version` 열이 기본 키에없는 경우 ClickHouse 그것을 마지막 필드로 암시 적으로 기본 키에 추가하고 순서에 사용합니다.

## 데이터 선택 {# selecting-data}

ClickHouse 동일한 기본 키를 가진 모든 행이 같은 결과의 데이터 부분 또는 동일한 물리적 서버에있는 것을 보증하는 것은 아닙니다. 이것은 데이터의 기록 및 후속 데이터 부분의 병합에 모두 적용됩니다. 또한 ClickHouse 프로세스`SELECT` 다중 스레드를 사용하여 쿼리를 실행하고 결과 행의 순서를 예측할 수 없습니다. 이것은 완전히 취득 할 필요가있는 경우 통합이 필요하다는 것을 의미합니다 "collapsed"a에서 데이터`VersionedCollapsingMergeTree` 테이블.

축소를 종료하려면`GROUP BY` 부호를 설명하는 어구 및 집계 함수. 예를 들어, 수량을 계산하려면 다음을 사용합니다`sum (Sign)`대신`count ()`. 뭔가의 합계를 계산하려면 다음과 같이합니다`sum (Sign * x) `대신`sum (x)`를 추가합니다. `HAVING sum (Sign)> 0`.

집계`count``sum`와`avg`이 방법으로 계산할 수 있습니다. 집계`uniq` 개체가 적어도 하나의 비 축소 상태를 가지는 경우에 계산할 수 있습니다. 집계`min`와`max` 계산할 수없는 것은`VersionedCollapsingMergeTree` 축소 된 상태 값의 이력은 저장되지 않습니다.

데이터를 추출 할 필요가있는 경우 "collapsing"집계 (예를 들어, 확인 열이 존재하는 최신의 값과 일치 조건)을 사용할 수 있습니다`FINAL` 모디파이어`FROM` 절. 이 방법은 비효율적으로 이용해서는 없습니다 큰 수 있습니다.

## 예 {# example-of-use}

데이터 예 :

```text
┌──────────────UserID─┬─PageViews─┬─Duration─┬─Sign─┬─Version─┐
│ 4324182021466249494 │ 5 │ 146 │ 1 │ 1 |
│ 4324182021466249494 │ 5 │ 146 │ -1 │ 1 |
│ 4324182021466249494 │ 6 │ 185 │ 1 │ 2 |
└─────────────────────┴───────────┴──────────┴──── ──┴─────────┘
```

테이블 만들기 :

```sql
CREATE TABLE UAct
(
    UserID UInt64,
    PageViews UInt8,
    Duration UInt8,
    Sign Int8,
    Version UInt8
)
ENGINE = VersionedCollapsingMergeTree (Sign, Version)
ORDER BY UserID
```

데이터 삽입 :

```sql
INSERT INTO UAct VALUES (4324182021466249494 5, 146, 1, 1)
```

```sql
INSERT INTO UAct VALUES (4324182021466249494 5, 146, -1, 1), (4324182021466249494 6, 185, 1, 2)
```

우리는 두 가지를 사용합니다`INSERT` 두 개의 다른 데이터 부분을 만들 쿼리. 단일 쿼리에서 데이터를 삽입하면 ClickHouse는 하나의 데이터 파트를 만들고 병합을 실행할 수 없습니다.

데이터 검색 :

```sql
SELECT * FROM UAct
```

```text
┌──────────────UserID─┬─PageViews─┬─Duration─┬─Sign─┬─Version─┐
│ 4324182021466249494 │ 5 │ 146 │ 1 │ 1 │
└─────────────────────┴───────────┴──────────┴──── ──┴─────────┘
┌──────────────UserID─┬─PageViews─┬─Duration─┬─Sign─┬─Version─┐
│ 4324182021466249494 │ 5 │ 146 │ -1 │ 1 │
│ 4324182021466249494 │ 6 │ 185 │ 1 │ 2 │
└─────────────────────┴───────────┴──────────┴──── ──┴─────────┘
```

우리는 여기에서 무엇을보고, 붕괴 된 부분은 어디에 있나요?
두 데이터 파트를 만들었습니다`INSERT` 쿼리. 그`SELECT` 쿼리는 두 스레드에서 실행되고 결과는 행의 임의의 순서입니다.
붕괴 발생하지 않으므로 데이터의 파트가 이루어지고 있습니다. ClickHouse는 예측할 수없는 미지의 시점에서 데이터 파트를 병합합니다.

이것이 통합이 필요한 이유입니다 :

```sql
SELECT
    UserID,
    sum (PageViews * Sign) AS PageViews,
    sum (Duration * Sign) AS Duration,
    Version
FROM UAct
GROUP BY UserID, Version
HAVING sum (Sign)> 0
```

```text
┌──────────────UserID─┬─PageViews─┬─Duration─┬─Version─┐
│ 4324182021466249494 │ 6 │ 185 │ 2 │
└─────────────────────┴───────────┴──────────┴──── ─────┘
```

집계를 필요로하지 않고 강제로 축소 싶다면 다음을 사용할 수 있습니다`FINAL` 모디파이어`FROM` 절.

```sql
SELECT * FROM UAct FINAL
```

```text
┌──────────────UserID─┬─PageViews─┬─Duration─┬─Sign─┬─Version─┐
│ 4324182021466249494 │ 6 │ 185 │ 1 │ 2 │
└─────────────────────┴───────────┴──────────┴──── ──┴─────────┘
```

이것은 데이터를 선택하는 매우 비효율적 인 방법입니다. 큰 테이블에는 사용하지 마십시오.

원본 기사 (https://clickhouse.tech/docs/en/operations/table_engines/versionedcollapsingmergetree/) <! - hide ->
