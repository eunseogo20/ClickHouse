---
machine_translated : true
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd
toc_priority : 36
toc_title : "\ u6298 \ u308A \ u305F \ u305F \ u307F \ u30DE \ u30FC \ u30B8 \ u30C4 \ u30EA \ u30FC"
---

# 축소 병합 트리 {# table_engine-collapsingmergetree}

엔진에서 상속합니다 [메루게쯔리 (mergetree.md) 가산 논리 행 붕괴 데이터 부품의 통합 알고리즘입니다.

`CollapsingMergeTree` 정렬 키의 모든 필드의 경우 행의 쌍을 비동기 적으로 제거 (축소)합니다 (`ORDER BY`)는 특정 분야를 제외하고 동일합니다`Sign` 이것은`1` 하면`-1` 값. 쌍없는 행은 유지됩니다. 자세한 내용은 붕괴 (# table_engine-collapsingmergetree-collapsing) 문서의 섹션.

엔진은 꽤 저장 양을 줄이고 효율을의 증가 할지도 모릅니다`SELECT` 결과로 쿼리.

## 테이블 만들기 {# creating a-table}

```sql
CREATE TABLE [IF NOT EXISTS] [db] table_name [ON CLUSTER cluster]
(
    name1 [type1] [DEFAULT | MATERIALIZED | ALIAS expr1,
    name2 [type2] [DEFAULT | MATERIALIZED | ALIAS expr2]
    ...
) ENGINE = CollapsingMergeTree (기호)
[PARTITION BY expr]
[ORDER BY expr]
[SAMPLE BY expr]
[SETTINGS name = value ...]
```

쿼리 매개 변수에 대한 설명은 쿼리 설명] (../../../ sql-reference / statements / create.md).

** CollapsingMergeTree 파라미터 **

-`sign` - Name of the column with the type of row :`1`은 "state"행`-1`은 "cancel"로우

    Column data type -`Int8`.

** 쿼리 절 **

을 만들 때`CollapsingMergeTree` 테이블 같은 쿼리 절 (mergetree.md # table_engine-mergetree-creating a-table)을 만들 때처럼 필요합니다. `MergeTree` 테이블.

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
) ENGINE [=] CollapsingMergeTree (date-column [, sampling_expression] (primary, key), index_granularity, sign)
```

다음을 제외한 모든 매개 변수`sign`와 같은 의미를 가진`MergeTree`.

-`sign` - Name of the column with the type of row :`1` - "state"행`-1` - "cancel"로우

    Column Data Type -`Int8`.

</ details>

## 축소 {# table_engine-collapsingmergetree-collapsing}

### 데이터 {#data}

생각해야하는 상황이 저장 지속적으로 변화하는 데이터 객체입니다. 개체의 행을 하나 가지고 변경시에 업데이트하는 것은 논리적이지만, DBMS에서는 스토리지의 데이터를 갱신해야하기 때문에 업데이트 작업은 비용 포획이 필요한 경우에 데이터를 쓰려면 신속하게 업데이트 할 수 없지만 때의 변화를 같은 순서로하고 있습니다.

특정 열을 사용하는`Sign` 만약`Sign = 1` 이것은 행이 객체의 상태임을 의미합니다. "State"행 If`Sign = -1` 이는 동일한 속성을 가진 객체의 상태를 취소하는 것을 의미합니다. "cancel"로우

예를 들어, 사용자가 사이트에서 체크 한 페이지 수와 거기에 있던 시간을 계산하고 싶다고합니다. 어떤 시점에서, 사용자 활동 상태에서 다음 줄을 씁니다 :

```text
┌──────────────UserID─┬─PageViews─┬─Duration─┬─Sign─┐
│ 4324182021466249494 │ 5 │ 146 │ 1 │
└─────────────────────┴───────────┴──────────┴──── ──┘
```

어떤 시점에서 나중에 우리는 사용자의 활동의 변경을 등록하고 다음 두 행에서 그것을 씁니다.

```text
┌──────────────UserID─┬─PageViews─┬─Duration─┬─Sign─┐
│ 4324182021466249494 │ 5 │ 146 │ -1 │
│ 4324182021466249494 │ 6 │ 185 │ 1 │
└─────────────────────┴───────────┴──────────┴──── ──┘
```

첫 번째 줄은 개체 (사용자)의 이전 상태를 취소합니다. 취소 된 상태의 정렬 키 필드를 복사합니다. `Sign`.

다음 줄에는 현재의 상태가 포함됩니다.

사용자 활동의 마지막 상태 만이 필요하므로 행

```text
┌──────────────UserID─┬─PageViews─┬─Duration─┬─Sign─┐
│ 4324182021466249494 │ 5 │ 146 │ 1 │
│ 4324182021466249494 │ 5 │ 146 │ -1 │
└─────────────────────┴───────────┴──────────┴──── ──┘
```

개체의 잘못된 (이전) 상태를 축소 제거 할 수 있습니다. `CollapsingMergeTree` 데이터 부품의 병합 중에 작업을 수행합니다.

왜 우리는로드 된 각 변경을위한 두 줄이 필요합니다 [알고리즘] (# table_engine-collapsingmergetree-collapsing-algorithm) 단락.

** 이러한 접근 방식 **

1. 프로그램을 쓰고 데이터 뜻의 객체를 취소 할 수 있습니다. "취소"문자열은 "상태"문자열을 기록하고 그 반대의 경우 복사 정렬 키 'Sign'필드에 기록합니다. 이렇게 증가 된 초기 크기로 저장할 수있는 데이터입니다.
2. 열 긴 성장 배열은 쓰기의 부하에 의한 엔진의 효율을 저하시킵니다. 더 간단한 데이터보다 높은 효율.
3.`SELECT` 결과 개체 변경 내용의 일관성에 크게 의존합니다. 삽입을위한 데이터를 준비 할 때 정확합니다. 예를 들어, 세션의 깊이 등 부가 아닌 지표의 음수 등 일관성없는 데이터에서 예측할 수없는 결과를 얻을 수 있습니다.

### 알고리즘 {# table_engine-collapsingmergetree-collapsing-algorithm}

ClickHouse가 데이터 파트를 병합하면 같은 정렬 키를 가진 연속 된 행의 각 그룹 (`ORDER BY`)은 다음의 두 행으로 축소된다. `Sign = 1` ( "state"행)과 다른`Sign = -1` ( "cancel"행). 즉, 항목 붕괴합니다.

各り 그 결과 얻어진 데이터는 부분 ClickHouse 성 :

1. 첫 번째 "cancel"그리고 마지막으로 "state"행이 "state"와 "cancel"행은 일치하고 마지막 행은 "state"로우
2. 마지막 "상태"보다 더 많은 경우 "상태"라인보다 "취소"라인입니다.
3. 첫 번째 "cancel"더 있으면 행 "cancel"행보다 "state"행.
4. 다른 모든 경우에는 행의 모두.

또한 적어도 2 이상이있는 경우 "state"행보다 "cancel"행 또는 적어도 2 이상의 "cancel"다음 줄 "state"병합은 계속되지만, ClickHouse는이 상황을 논리적 오류로 처리 서버 로그 에 기록합니다. 동일한 데이터를 여러 번 삽입 된 경우이 오류가 발생할 수 있습니다.

따라서 붕괴는 통계 계산 결과를 바꾸지한다.
변경은 서서히 붕괴되고 결국 거의 모든 개체의 마지막 상태 만이 남았다.

그`Sign` 병합 알고리즘은 같은 정렬 키를 가진 모든 행이 동일한 결과 데이터 부분에 동일한 물리적 서버에있는 것을 보증하는 것이 아니기 때문에 반드시 ClickHouse 프로세스`SELECT` 여러 스레드에서 쿼리를 실행하고 결과 행의 순서를 예측할 수 없습니다. 완전히 취득 할 필요가있는 경우 집계가 필요합니다 "collapsed"데이터에서`CollapsingMergeTree` 테이블.

축소를 종료하려면 다음 쿼리를 작성합니다`GROUP BY` 부호를 설명하는 어구 및 집계 함수. 예를 들어, 수량을 계산하려면 다음을 사용합니다`sum (Sign)`대신`count ()`. 뭔가의 합계를 계산하려면 다음과 같이합니다`sum (Sign * x) `대신`sum (x)`, 등등, 또한 추가`HAVING sum (Sign)> 0`.

집계`count``sum`와`avg`이 방법으로 계산할 수 있습니다. 집계`uniq` 산출 할 경우에는 개체는 적어도 하나의 상태는 아직 무너지지 않았다. 집계`min`와`max` 계산할 수 없습니다. `CollapsingMergeTree` 축소 된 상태 값의 이력은 저장되지 않습니다.

집계없이 데이터를 추출해야하는 경우 (예를 들어, 특정 조건에 맞는 최신의 값을 갖는 행이 존재하는지 여부를 확인하는 경우)에는 다음의`FINAL` 모디파이어`FROM` 절 . 이러한 접근 방식은 크게 낮은 효율성이다.

## 예 {# example-of-use}

데이터 예 :

```text
┌──────────────UserID─┬─PageViews─┬─Duration─┬─Sign─┐
│ 4324182021466249494 │ 5 │ 146 │ 1 │
│ 4324182021466249494 │ 5 │ 146 │ -1 │
│ 4324182021466249494 │ 6 │ 185 │ 1 │
└─────────────────────┴───────────┴──────────┴──── ──┘
```

테이블 만들기 :

```sql
CREATE TABLE UAct
(
    UserID UInt64,
    PageViews UInt8,
    기간 UInt8,
    Sign Int8
)
ENGINE = CollapsingMergeTree (Sign)
ORDER BY UserID
```

데이터 삽입 :

```sql
INSERT INTO UAct VALUES (4324182021466249494 5, 146 1)
```

```sql
UAct 값에 삽입 (4324182021466249494, 5, 146, -1), (4324182021466249494, 6, 185, 1)
```

우리는 두 가지를 사용합니다`INSERT` 두 개의 다른 데이터 부분을 만들 쿼리. 하나의 쿼리에서 데이터를 삽입하면 ClickHouse는 하나의 데이터 부분을 만들고 병합을 실행하지 않습니다.

데이터 검색 :

```sql
SELECT * FROM UAct
```

```text
┌──────────────UserID─┬─PageViews─┬─Duration─┬─Sign─┐
│ 4324182021466249494 │ 5 │ 146 │ -1 │
│ 4324182021466249494 │ 6 │ 185 │ 1 │
└─────────────────────┴───────────┴──────────┴──── ──┘
┌──────────────UserID─┬─PageViews─┬─Duration─┬─Sign─┐
│ 4324182021466249494 │ 5 │ 146 │ 1 │
└─────────────────────┴───────────┴──────────┴──── ──┘
```

우리는 무엇을보고 어디서 무너지고 있습니까?

두와`INSERT` 쿼리를 만들고 두 개의 데이터 요소입니다. `SELECT` 쿼리가 두 개의 스레드에서 실행되어 임의의 행 순서가 생성되었습니다. 데이터 부품의 병합이 아직 없기 때문에 축소가 발생하지 않았습니다. ClickHouse 예측할 수없는 미지의 순간에 데이터 부분을 병합합니다.

따라서 집계가 필요합니다.

```sql
SELECT
    UserID,
    sum (PageViews * Sign) AS PageViews,
    sum (Duration * Sign) AS Duration
FROM UAct
GROUP BY UserID
HAVING sum (Sign)> 0
```

```text
┌──────────────UserID─┬─PageViews─┬─Duration─┐
│ 4324182021466249494 │ 6 │ 185 │
└─────────────────────┴───────────┴──────────┘
```

집계를 필요로하지 않고 강제로 붕괴시키고 싶은 경우는 다음을 사용할 수 있습니다`FINAL` 수식을위한`FROM` 절.

```sql
SELECT * FROM UAct FINAL
```

```text
┌──────────────UserID─┬─PageViews─┬─Duration─┬─Sign─┐
│ 4324182021466249494 │ 6 │ 185 │ 1 │
└─────────────────────┴───────────┴──────────┴──── ──┘
```

데이터를 선택하는이 방법은 매우 비효율적입니다. 큰 테이블에는 사용하지 마십시오.

## 다른 접근 방법의 예 {# example-of-another-approach}

데이터 예 :

```text
┌──────────────UserID─┬─PageViews─┬─Duration─┬─Sign─┐
│ 4324182021466249494 │ 5 │ 146 │ 1 │
│ 4324182021466249494 │ -5 │ -146 │ -1 │
│ 4324182021466249494 │ 6 │ 185 │ 1 │
└─────────────────────┴───────────┴──────────┴──── ──┘
```

아이디어 병합이 고려에 키 필드 만하는 것입니다. 그리고 "Cancel"줄 우리는 부호 열을 사용하지 않고 총 때 행의 이전 버전을 동일 음수 값을 지정할 수 있습니다. 이 방법에서는 데이터 형식을 변경해야합니다`PageViews``Duration` uint8 - \> Int16의 음수 값을 저장한다.

```sql
CREATE TABLE UAct
(
    UserID UInt64,
    PageViews Int16,
    Duration Int16,
    Sign Int8
)
ENGINE = CollapsingMergeTree (Sign)
ORDER BY UserID
```

하자 시험 방법 :

```sql
insert into UAct values ​​(4324182021466249494 5, 146, 1);
insert into UAct values ​​(4324182021466249494 -5, -146, -1);
insert into UAct values ​​(4324182021466249494 6, 185, 1);

select * from UAct final; // avoid using final in production (just for a test or small tables)
```

```text
┌──────────────UserID─┬─PageViews─┬─Duration─┬─Sign─┐
│ 4324182021466249494 │ 6 │ 185 │ 1 │
└─────────────────────┴───────────┴──────────┴──── ──┘
```

```sql
SELECT
    UserID,
    sum (PageViews) AS PageViews,
    sum (Duration) AS Duration
FROM UAct
GROUP BY UserID
```텍스트
┌──────────────UserID─┬─PageViews─┬─Duration─┐
│ 4324182021466249494 │ 6 │ 185 │
└─────────────────────┴───────────┴──────────┘
```

```sqk
select count () FROM UAct
```

```text
┌─count () ─┐
│ 3 │
└─────────┘
```

```sql
테이블 UAct 최종 최적화;

select * FROM UAct
```

```text
┌──────────────UserID─┬─PageViews─┬─Duration─┬─Sign─┐
│ 4324182021466249494 │ 6 │ 185 │ 1 │
└─────────────────────┴───────────┴──────────┴──── ──┘
```

원본 기사 (https://clickhouse.tech/docs/en/operations/table_engines/collapsingmergetree/) <! - hide ->
