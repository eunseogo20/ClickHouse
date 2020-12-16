--- 
machine_translated : true 
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd 
toc_priority : 42 
toc_title : mysql 
--- 

# mysql {#mysql} 

허가`SELECT` 원격 MySQL 서버에 저장되어있는 데이터에 대해 수행되는 쿼리. 

```sql 
mysql ( 'host : port', 'database', 'table', 'user', 'password'[, replace_query 'on_duplicate_clause']); 
``` 

** 파라미터 ** 

-`host : port `- MySQL server address. 

-`database` - Remote database name. 

-`table` - Remote table name. 

-`user` - MySQL user. 

-`password` - user password. 

-`replace_query` - Flag that converts`INSERT INTO`에 쿼리`REPLACE INTO` 만약`replace_query = 1` 쿼리를 대체합니다.

-`on_duplicate_clause` - The`ON DUPLICATE KEY on_duplicate_clause`에 추가되는 식`INSERT` 쿼리. 

        Example :`INSERT INTO t (c1, c2) VALUES ( 'a', 2) ON DUPLICATE KEY UPDATE c2 = c2 + 1`, where`on_duplicate_clause` is`UPDATE c2 = c2 + 1`. See the MySQL documentation to find which`on_duplicate_clause` you can use with the`ON DUPLICATE KEY` clause. 

        to specify`on_duplicate_clause` you need to pass`0` to the`replace_query` parameter. If you simultaneously pass`replace_query = 1` and`on_duplicate_clause`, ClickHouse generates an exception. 

심플`WHERE` 다음과 같은 어구`=! =,>,> =, <, <=`현재 MySQL 서버에서 실행되고 있습니다. 

나머지 조건과`LIMIT` 샘플링 제약은 MySQL에 쿼리가 종료 된 후에 만 ClickHouse에서 실행됩니다. 

** 반환 값 ** 

원래 MySQL 테이블과 같은 열이있는 테이블 개체입니다. 

## 예 {# usage-example}

MySQL의 테이블 : 

```text 
mysql> CREATE TABLE`test`.`test` ( 
    ->`int_id` INT NOT NULL AUTO_INCREMENT, 
    ->`int_nullable` INT NULL DEFAULT NULL, 
    ->`float` FLOAT NOT NULL, 
    - >`float_nullable` FLOAT NULL DEFAULT NULL, 
    -> PRIMARY KEY (`int_id`)); 
Query OK, 0 rows affected (0,09 sec) 

mysql> insert into test (`int_id``float`) VALUES (1, 2); 
Query OK, 1 row affected (0,00 sec) 

mysql> select * from test; 
+ ------ + ---------- + ----- + --- ------- + 
| int_id | int_nullable | float | float_nullable | 
+ ------ + ---------- + ----- + -------- - + 
| 1 | NULL | 2 | NULL | 
+ ------ + ---------- + ----- + ---------- + 
1 row in set (0,00 sec) 
` ``

ClickHouse에서 데이터 선택 : 

```sql 
SELECT * FROM mysql ( 'localhost : 3306', 'test', 'test', 'bayonet', '123') 
``` 

```text 
┌─int_id─┬ ─int_nullable─┬─float─┬─float_nullable─┐ 
│ 1 │ ᴺᵁᴸᴸ │ 2 │ ᴺᵁᴸᴸ │ 
└────────┴──────────────┴──── ───┴────────────────┘ 
``` 

## 참조. {# see-also} 

- [소노 'MySQL'표 엔진 (../../ engines / table-engines / integrations / mysql.md) 
- 외부 사전의 소스로 MySQL을 사용 (../ ../sql-reference/dictionaries/external-dictionaries/external-dicts-dict-sources.md#dicts-external_dicts_dict_sources-mysql)

원본 기사 (https://clickhouse.tech/docs/en/query_language/table_functions/mysql/) <! - hide ->
