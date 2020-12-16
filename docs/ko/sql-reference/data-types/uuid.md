--- 
machine_translated : true 
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd 
toc_priority : 46 
toc_title : UUID 
--- 

# UUID {# uuid-data-type} 

보편적 고유 식별자 (UUID) 레코드를 식별하는 데 사용되는 16 바이트의 번호입니다 . UUID에 대한 자세한 내용은 다음을 참조하십시오 [Wikipedia] (https://en.wikipedia.org/wiki/Universally_unique_identifier) 

UUID 형의 값의 예는 다음과 같습니다 : 

```text 
61f0c404-5cb3 -11e7-907b-a6006ad3dba0 
``` 

새로운 레코드를 삽입 할 때 UUID 문자열 값을 지정하지 않으면 UUID 값이 0으로 채워진됩니다 : 

```text 
00000000-0000-0000-0000-000000000000 
``` 

## 생성하는 방법 {# how-to-generate} 

Uuid 값을 생성하기 위해 ClickHouse은 [generateuidv4 (../../ sql-reference / functions / uuid-functions.md) 기능. 

## 예 {# usage-example}

** 예 1 ** 

이 예에서는 UUID 형의 열이있는 테이블을 만들고 테이블에 값을 삽입하는 방법을 보여줍니다. 

```sql 
CREATE TABLE t_uuid (x UUID, y String) ENGINE = TinyLog 
``` 

```sql 
INSERT INTO t_uuid SELECT generateUUIDv4 () 'Example 1' 
``` 

```sql 
SELECT * FROM t_uuid 
``` 

```text 
┌────────────────────────────────────x─┬─y──── ─────┐ 
│ 417ddc5d-e556-4d27-95dd-a34d84e46a50 │ Example 1 │ 
└───────────────────────────── ─────────┴───────────┘ 
``` 

** 예 2 ** 

이 예에서는 새 레코드를 삽입 할 때 UUID 열의 값은 지정되지 않습니다. 

```sql 
INSERT INTO t_uuid (y) VALUES ( 'Example 2') 
``` 

```sql 
SELECT * FROM t_uuid 
``` 

```text
┌────────────────────────────────────x─┬─y──────── ─┐ 
│ 417ddc5d-e556-4d27-95dd-a34d84e46a50 │ Example 1 │ 
│ 00000000-0000-0000-0000-000000000000 │ Example 2 │ 
└─────────────────── ───────────────────┴───────────┘ 
``` 

## 제한 {#restrictions} 

UUID 데이터 형식은 다음 함수 만 지원합니다 [문자열] (string.md) 데이터 형식도 지원하고 있습니다 (예, 분 (../../ sql-reference / aggregate-functions / reference.md # agg_function-min) [최대] (../../ sql-reference / aggregate-functions / reference.md # agg_function-max) and [카운트] (../../ sql-reference / aggregate-functions / reference.md # agg_function-count)).

Uuid 데이터 형식은 산술에서는 지원되지 않습니다 (예를 들어, abs (../../ sql-reference / functions / arithmetic-functions.md # arithm_func-abs)) 또는 다음과 같은 집계 함수 [일본] (../../ sql-reference / aggregate-functions / reference.md # agg_function-sum) 및 avg (../../ sql-reference / aggregate-functions / reference.md # agg_function -avg) 

원본 기사 (https://clickhouse.tech/docs/en/data_types/uuid/) <! - hide ->
