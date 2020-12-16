--- 
machine_translated : true 
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd 
toc_priority : 39 
toc_title : "\ u6570 \ u5B57" 
--- 

# 숫자 {#numbers} 

`numbers (N)`- Returns a table with the single 'number'0에서 N-1 까지의 정수를 포함하는 열 (UInt64). 
`numbers (N, M)`- 하나의 테이블을 반환합니다 'number'n에서 (N + M-1)까지의 정수를 포함하는 열 (UInt64). 

유사한`system.numbers` 테이블에 사용할 수 시험 및 발생 연속 값`numbers (N, M)`능률적`system.numbers`. 

다음 쿼리는 동일합니다 : 

```sql 
SELECT * FROM numbers ( 10); 
SELECT * FROM numbers (0, 10); 
SELECT * FROM system.numbers LIMIT 10; 
``` 

예 : 

```sql 
- Generate a sequence of dates from 2010-01-01 to 2010-12-31
select toDate ( '2010-01-01') + number as d FROM numbers (365); 
```

원본 기사 (https://clickhouse.tech/docs/en/query_language/table_functions/numbers/) <! - hide ->
