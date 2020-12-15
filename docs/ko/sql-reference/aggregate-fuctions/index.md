--- 
machine_translated : true 
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd 
toc_folder_title : "\ u96C6 \ u8A08 \ u95A2 \ u6570" 
toc_priority : 33 
toc_title : "\ u306F \ u3058 \ u3081 \ u306B" 
--- 

# 집계 함수 {# aggregate-functions} 

집계 함수 [표준] (http://www.sql-tutorial.com/sql-aggregate-functions-sql-tutorial) 데이터베이스 전문가가 예측 가능한 방법. 

ClickHouse 또한 지원합니다 : 

- 파라 메트릭 집계 함수 (parametric-functions.md # aggregate_functions_parametric) 열 이외에 다른 매개 변수를 허용합니다. 
- [연결자 (combinators.md # aggregate_functions_combinators) 집계 함수의 동작을 변경합니다. 

## 널 처리 {# null-processing} 

집계중인 모든`NULL`s는 생략됩니다. 

** 예 ** 

다음 표 있다면 : 

```text 
┌─x─┬────y─┐ 
│ 1 │ 2 │ 
│ 2 │ ᴺᵁᴸᴸ │ 
│ 3 │ 2 │ 
│ 3 │ 3 │ 
│ 3 │ ᴺᵁᴸᴸ │ 
└───┴──────┘ 
``` 

의 값을 합계 할 필요가 있다고합시다 `y` 열 : 

```sql 
SELECT sum (y) FROM t_null_big 
``` 

    ┌─sum (y) ─┐ 
    │ 7 │ 
    └────────┘ 

그`sum` 함수 해석합니다` NULL`로`0` 특히 이것은 함수가 모든 값이 다음과 같은 선택 범위의 입력을받은 경우`NULL` 결과는 다음과 같습니다`0`없는`NULL`. 

지금 즉시 사용할 수있는`groupArray` 배열을 생성하는 함수`y` 열 : 

```sql 
SELECT groupArray (y) FROM t_null_big 
``` 

```text 
┌─groupArray (y) ─┐ 
│ [2 2,3] │ 
└───────────────┘ 
``` 

`groupArray` 포함하지 않는`NULL` 결과의 배열.

원본 기사 (https://clickhouse.tech/docs/en/query_language/agg_functions/) <! - hide ->
