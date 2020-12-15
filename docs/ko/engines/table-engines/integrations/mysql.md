--- 
machine_translated : true 
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd 
toc_priority : 33 
toc_title : MySQL 
--- 

# Mysql {#mysql} 

MySQL 엔진은 다음 작업을 수행 할 수 있습니다`SELECT` 원격 MySQL 서버에 저장되어있는 데이터에 대한 쿼리. 

## 테이블 만들기 {# creating a-table} 

```sql 
CREATE TABLE [IF NOT EXISTS] [db] table_name [ON CLUSTER cluster] 
( 
    name1 [type1] [DEFAULT | MATERIALIZED | ALIAS expr1] [TTL expr1 , 
    name2 [type2] [DEFAULT | MATERIALIZED | ALIAS expr2] [TTL expr2, 
    ... 
) ENGINE = MySQL ( 'host : port', 'database', 'table', 'user', 'password'[, replace_query 'on_duplicate_clause']); 
```

상세 설명을 참조하십시오 [CREATE TABLE (../../../ sql-reference / statements / create.md # create-table-query) 쿼리. 

테이블 구조는 원래 MySQL 테이블 구조와 다를 수 있습니다 : 

- 컬럼 이름은 원래 MySQL 테이블과 동일해야하지만,이 컬럼의 일부만을 임의의 순서로 사용할 수 있습니다. 
- 열의 형태는 원래 MySQL 테이블의 형태와 다를 수 있습니다. ClickHouse은 [캐스트] (../../../ sql-reference / functions / type-conversion-functions.md # type_conversion_function-cast) ClickHouse 데이터 형의 값. 

** 엔진 변수 ** 

-`host : port` - MySQL server address. 

-`database` - Remote database name. 

-`table` - Remote table name. 

-`user` - MySQL user. 

-`password` - User password . 

-`replace_query` - Flag that converts`INSERT INTO`에 쿼리`REPLACE INTO` 만약`replace_query = 1` 쿼리가 할당됩니다.

-`on_duplicate_clause` - The`ON DUPLICATE KEY on_duplicate_clause`에 추가되는 식`INSERT` 쿼리. 

    예 :`INSERT INTO t (c1, c2) VALUES ( 'a', 2) ON DUPLICATE KEY UPDATE c2 = c2 + 1` 여기에서`on_duplicate_clause`는`UPDATE c2 = c2 + 1`.를 참조하십시오. [MySQL 문서 (https://dev.mysql.com/doc/refman/8.0/en/insert-on-duplicate.html) 이것을 찾으려면`on_duplicate_clause`로 사용할 수 있습니다`ON DUPLICATE KEY` 절. 

    지정하려면`on_duplicate_clause` 합격해야합니다`0`에`replace_query` 매개 변수. 당신이 동시에 통과하면`replace_query = 1`과`on_duplicate_clause`, ClickHouse는 예외를 생성합니다. 

심플`WHERE` 다음과 같은 어구`=! =,>,> =, <, <=`MySQL 서버에서 실행됩니다. 

나머지 조건과`LIMIT` 샘플링 제약은 MySQL에 쿼리가 종료 된 후에 만 ClickHouse에서 실행됩니다. 

## 예 {# usage-example} 

MySQL의 테이블 : 

```text 
mysql> CREATE TABLE`test`.`test` (
    ->`int_id` INT NOT NULL AUTO_INCREMENT, 
1 row in set (0,00 sec) 
```
    ->`int_nullable` INT NULL DEFAULT NULL, 
    ->`float` FLOAT NOT NULL, 
    ->`float_nullable` FLOAT NULL DEFAULT NULL, 
    -> PRIMARY KEY (`int_id`)); 
Query OK, 0 rows affected (0,09 sec) 

mysql> insert into test (`int_id``float`) VALUES (1,2); 
Query OK, 1 row affected (0,00 sec) 

mysql> select * from test; 
+ ------ + ---------- + ----- + ---------- + 
| int_id | int_nullable | float | float_nullable | 
+ ------ + ----- ----- + ----- + ---------- + 
| 1 | NULL | 2 | NULL | 
+ ------ + ---------- + ----- + ---------- + 

ClickHouse 테이블 위에서 만든 MySQL의 테이블에서 데이터를 검색하는 : 

```sql 
CREATE TABLE mysql_table 
(
    `float_nullable` Nullable (Float32), 
    `int_id` Int32 
) 
ENGINE = MySQL ( 'localhost : 3306', 'test', 'test', 'bayonet', '123') 
``` 

```sql 
SELECT * FROM mysql_table 
``` 

```text 
┌─float_nullable─┬─int_id─┐ 
│ ᴺᵁᴸᴸ │ 1 │ 
└────────────────┴────────┘ 
`` ` 

## 참조. {# see-also} 

- [소노 물론 mysql 테이블 함수 (../../../ sql-reference / table-functions / mysql.md) 
- 외부 사전의 소스로 MySQL을 사용 ( ../../../sql-reference/dictionaries/external-dictionaries/external-dicts-dict-sources.md#dicts-external_dicts_dict_sources-mysql)

원본 기사 (https://clickhouse.tech/docs/en/operations/table_engines/mysql/) <! - hide ->
