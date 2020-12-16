--- 
machine_translated : true 
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd 
toc_priority : 52 
toc_title : "AggregateFunction (\ u540D \ u524D, types_of_arguments ...)" 
--- 

# AggregateFunction (name, types_of_arguments ...) {# data-type-aggregatefunction} 

Aggregate functions can have an implementation-defined intermediate state that can be serialized to an AggregateFunction (...) data type and stored in a table, usually, by means of [실현보기 (../../ sql-reference / statements / create.md # create-view) 집계 함수의 상태를 생성하는 일반적인 방법은 집계 함수를 호출하는 것입니다. `-State` 접미사. 미래 집계 최종 결과를 얻으려면 같은 집계 함수를 사용해야합니다. `-Merge` 접미사. 

`AggregateFunction` - parametric data type. 

** 파라미터 ** 

- 집계 함수의 이름입니다.

        If the function is parametric, specify its parameters too. 

- 집계 함수의 인수 형식입니다. 

** 예 ** 

```sql 
CREATE TABLE t 
( 
    column1 AggregateFunction (uniq, UInt64), 
    column2 AggregateFunction (anyIf, String UInt8), 
    column3 AggregateFunction (quantiles (0.5, 0.9), UInt64) 
) ENGINE = ... 
` `` 

[uniq (../../ sql-reference / aggregate-functions / reference.md # agg_function-uniq) anyIf (임의 (../../ sql-reference / aggregate-functions / reference .md # agg_function-any) + 만약 (../../ sql-reference / aggregate-functions / combinators.md # agg-functions-combinator-if)) 및 분 위수 (../. ./sql-reference/aggregate-functions/reference.md) ClickHouse에서 지원되는 집계 함수입니다. 

## 사용법 {#usage} 

### 데이터 삽입 {# data-insertion}

데이터를 삽입하려면`INSERT SELECT` 골재를 사용하여`-State`- 기능. 

** 함수의 예 ** 

```sql 
uniqState (UserID) 
quantilesState (0.5, 0.9) (SendTiming) 
``` 

대응하는 기능과는 대조적으로`uniq`와`quantiles``-State`- 함수는 최종 값 대신 상태를 반환합니다. 즉, 그들은 값을 반환합니다`AggregateFunction` 타입. 

결과`SELECT` 쿼리 값`AggregateFunction` type은 모든 ClickHouse 출력 형식에 대해 구현 고유의 바이너리 표현을 갖습니다. 예를 들어, 데이터를 덤프하는 경우,`TabSeparated` 포맷`SELECT`이 덤프는 다음을 사용하여로드 백 수 있습니다`INSERT` 쿼리. 

### 데이터 선택 {# data-selection} 

데이터를 선택할 때`AggregatingMergeTree` 테이블 사용`GROUP BY` 데이터를 삽입 할 때와 같은 집계 함수이지만,`-Merge` 접미사. 

를 가진 집합 함수`-Merge` suffix는 상태 세트를 취득하고이를 결합하여 전체 데이터 집계 결과를 반환합니다. 

예를 들어, 다음 두 쿼리는 동일한 결과를 반환합니다 : 

```sql 
SELECT uniq (UserID) FROM table

SELECT uniqMerge (state) FROM (SELECT uniqState (UserID) AS state FROM table GROUP BY RegionID) 
``` 

## 예 {# usage-example} 

더보기 [AggregatingMergeTree (../../ engines / table-engines / mergetree-family / aggregatingmergetree.md) 엔진의 설명. 

원본 기사 (https://clickhouse.tech/docs/en/data_types/nested_data_structures/aggregatefunction/) <! - hide ->
