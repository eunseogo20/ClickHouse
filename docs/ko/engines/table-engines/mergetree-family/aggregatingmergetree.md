--- 
machine_translated : true 
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd 
toc_priority : 35 
toc_title : AggregatingMergeTree 
--- 

# Aggregatingmergetree {#aggregatingmergetree} 

엔진에서 상속합니다 [메루게쯔리 (mergetree.md # table_engines-mergetree) 데이터 부분의 병합 논리를 변경한다. ClickHouse 모든 행을 동일한 기본 키 (또는보다 정확히 동일한 키)로 바꿉니다 [정렬 키 (mergetree.md)) 집계 함수 상태의 조합을 포함하는 단일 행 (하나의 데이터 부분에 )를 가진다. 

다음을 사용할 수 있습니다`AggregatingMergeTree` 집계됐다 실현보기를 포함 증분 데이터 집계 용 테이블. 

엔진은 다음 형식의 모든 열을 처리합니다 : 

- [AggregateFunction (../../../ sql-reference / data-types / aggregatefunction.md) 
- [SimpleAggregateFunction] (../. ./../sql-reference/data-types/simpleaggregatefunction.md) 

사용하는 것이 좋습니다`AggregatingMergeTree` 주문에 따라 행 수를 줄일 경우.

## 테이블 만들기 {# creating a-table} 

```sql 
CREATE TABLE [IF NOT EXISTS] [db] table_name [ON CLUSTER cluster] 
( 
    name1 [type1] [DEFAULT | MATERIALIZED | ALIAS expr1, 
    name2 [ type2] [DEFAULT | MATERIALIZED | ALIAS expr2, 
    ... 
) ENGINE = AggregatingMergeTree () 
[PARTITION BY expr] 
[ORDER BY expr] 
[SAMPLE BY expr] 
[TTL expr] 
[SETTINGS name = value, ...] 
` `` 

설명 요청 매개 변수의 참조 요청에 대한 설명 (../../../ sql-reference / statements / create.md) 

** 쿼리 절 ** 

을 만들 때`AggregatingMergeTree` 같은 테이블 [어구 (mergetree.md)을 만들 때처럼 필요합니다. `MergeTree` 테이블. 

<details markdown = "1"> 

<summary> 추천되고 있지 않습니다 법률 테이블을 만들 </ summary>

!!! attention "주의" 
    가능하면 이전 프로젝트를 위의 방법으로 전환하십시오. 

```sql 
CREATE TABLE [IF NOT EXISTS] [db] table_name [ON CLUSTER cluster] 
( 
    name1 [type1] [DEFAULT | MATERIALIZED | ALIAS expr1, 
    name2 [type2] [DEFAULT | MATERIALIZED | ALIAS expr2, 
    ... . 
) ENGINE [=] AggregatingMergeTree (date-column [, sampling_expression] (primary, key), index_granularity) 
``` 

모든 매개 변수는 in과 같은 의미를가집니다`MergeTree`. 
</ details> 

## 선택 삽입 {# select-and-insert} 

데이터를 삽입하려면 iNSERT sELECT (../../../ sql-reference / statements / insert-into.md) aggregate-State-functions를 사용 쿼리. 
데이터를 선택할 때`AggregatingMergeTree` 테이블 사용`GROUP BY` 데이터를 삽입 할 때와 같은 집계 함수이지만,`-Merge` 접미사.

결과`SELECT` 쿼리 값`AggregateFunction` type은 모든 ClickHouse 출력 형식에 대해 구현 고유의 바이너리 표현을 갖습니다. 예를 들어, 데이터를 덤프하는 경우,`TabSeparated` 포맷`SELECT` 다음이 덤프를 다음과 같이로드합니다`INSERT` 쿼리. 

## 통합 실현보기의 예 {# example-of-an-aggregated-materialized-view} 

`AggregatingMergeTree` 이것은`test.visits` 테이블 : 

```sql 
CREATE MATERIALIZED VIEW test.basic 
ENGINE = AggregatingMergeTree () PARTITION BY toYYYYMM (StartDate) ORDER BY (CounterID, StartDate) 
AS SELECT 
    CounterID, 
    StartDate, 
    sumState (Sign) AS Visits, 
    uniqState (UserID) AS Users 
FROM test.visits 
GROUP BY CounterID, StartDate; 
``` 

에 데이터를 삽입하는` test.visits` 테이블. 

```sql
INSERT INTO test.visits ... 
``` 

데이터는 테이블과 뷰 모두에 삽입됩니다`test.basic` 그것은 집계를 수행합니다. 

집계 데이터를 검색하려면 다음과 같은 쿼리를 실행해야합니다`SELECT ... GROUP BY ...`뷰에서`test.basic` : 

```sql 
SELECT 
    StartDate, 
    sumMerge (Visits) AS Visits, 
    uniqMerge (Users) AS Users 
FROM test.basic 
GROUP BY StartDate 
ORDER BY StartDate; 
``` 

원래 기사 (https://clickhouse.tech/docs/en/operations/table_engines/aggregatingmergetree/) <! - -hide ->
