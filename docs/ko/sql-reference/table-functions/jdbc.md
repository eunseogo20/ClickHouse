--- 
machine_translated : true 
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd 
toc_priority : 43 
toc_title : jdbc 
--- 

# jdbc {# table-function-jdbc} 

`jdbc (jdbc_connection_uri, schema table)`-JDBC 드라이버를 통해 연결된 테이블을 돌려줍니다 . 

이 테이블 함수는 별도의`clickhouse-jdbc-bridge` 실행하는 프로그램. 
Null 허용 형식을 지원합니다 (조회되는 원격 테이블의 DDL에 따라 달라집니다). 

** 예 ** 

```sql 
SELECT * FROM jdbc ( 'jdbc : mysql : // localhost : 3306 /? user = root & password = root', 'schema', 'table') 
``` 

```sql 
SELECT * FROM jdbc ( 'mysql : // localhost : 3306 /? user = root & password = root', 'schema', 'table') 
``` 

```sql 
SELECT * FROM jdbc ( 'datasource :
```

원본 기사 (https://clickhouse.tech/docs/en/query_language/table_functions/jdbc/) <! - hide ->
