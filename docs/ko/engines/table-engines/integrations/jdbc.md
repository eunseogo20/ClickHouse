--- 
machine_translated : true 
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd 
toc_priority : 34 
toc_title : JDBC 
--- 

# JDBC {# table-engine-jdbc} 

ClickHouse이 외부 데이터베이스에 연결할 수 있도록하는 [JDBC] (https : //en.wikipedia. org / wiki / Java_Database_Connectivity) 

JDBC 연결을 구현하려면 ClickHouse 다른 프로그램을 사용합니다 [clickhouse-jdbc-bridge (https://github.com/alex-krash/clickhouse-jdbc-bridge) 수행 하십주었습니다. 

이 엔진은 [Null 가능 (../../../ sql-reference / data-types / nullable.md) 데이터 형식입니다. 

## 테이블 만들기 {# creating a-table} 

```sql 
CREATE TABLE [IF NOT EXISTS] [db] table_name 
( 
    columns list ... 
) 
ENGINE = JDBC (dbms_uri, external_database, external_table) 
```

** 엔진 변수 ** 

-`dbms_uri` - URI of an external DBMS. 

    형식 :`jdbc : <driver_name> : // <host_name> : <port> /? user = <username> 및 password = <password>`. 
    MySQL 예 :`jdbc : mysql : // localhost : 3306 /? user = root & password = root`. 

-`external_database` - Database in an external DBMS. 

-`external_table` - Name of the table in`external_database`. 

## 사용 예 {# usage-example} 

콘솔 클라이언트에 직접 연결하여 mySQL server에서 테이블을 만들 : 

```text 
mysql> CREATE TABLE`test`.`test` ( 
    ->`int_id` INT NOT NULL AUTO_INCREMENT, 
    -> `int_nullable` INT NULL DEFAULT NULL, 
    ->`float` FLOAT NOT NULL, 
    ->`float_nullable` FLOAT NULL DEFAULT NULL, 
    ->PRIMARY KEY (`int_id`));
Query OK, 0 rows affected (0,09 sec) 
)

mysql> insert into test (`int_id``float`) VALUES (1,2); 
Query OK, 1 row affected (0,00 sec) 

mysql> select * from test; 
+ ------ + - -------- + ----- + ---------- + 
| int_id | int_nullable | float | float_nullable | 
+ ------ + ------- --- + ----- + ---------- + 
| 1 | NULL | 2 | NULL | 
+ ------ + ---------- + - ---- + ---------- + 
1 row in set (0,00 sec) 
``` 

ClickHouse server에서 테이블을 만들고 거기에서 데이터를 선택한다 : 

```sql 
CREATE TABLE jdbc_table 
( 
    `int_id` Int32, 
    `int_nullable` Nullable (Int32), 
    `float` Float32, 
    `float_nullable` Nullable (Float32)
ENGINE JDBC ( 'jdbc : mysql : // localhost : 3306 /? user = root & password = root', 'test', 'test') 
``` 

```sql 
SELECT * 
FROM jdbc_table 
``` 

```text 
┌─ int_id─┬─int_nullable─┬─float─┬─float_nullable─┐ 
│ 1 │ ᴺᵁᴸᴸ │ 2 │ ᴺᵁᴸᴸ │ 
└────────┴──────────────┴─ ──────┴────────────────┘ 
``` 

## 참조. {# see-also} 

- [JDBC 테이블 함수 (../../../ sql-reference / table-functions / jdbc.md) 

원본 기사 (https://clickhouse.tech/docs / en / operations / table_engines / jdbc /) <! - hide ->
