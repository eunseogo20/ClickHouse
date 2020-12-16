--- 
machine_translated : true 
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd 
toc_priority : 44 
toc_title : odbc 
--- 

# odbc {# table-functions-odbc} 

연결되어있는 테이블을 리턴합니다 [ODBC] (https://en.wikipedia.org/ wiki / Open_Database_Connectivity) 

```sql 
odbc (connection_settings, external_database, external_table) 
``` 

파라미터 : 

-`connection_settings` - Name of the section with connection settings in the `odbc.ini` 파일 
-`external_database` - Name of a database in an external DBMS. 
-`external_table` - Name of a table in the`external_database`.

ODBC 연결을 안전하게 구현하기 위해 ClickHouse 다른 프로그램을 사용합니다`clickhouse-odbc-bridge`. ODBC 드라이버가 직접로드되는 경우`clickhouse-server` 드라이버의 문제로 충돌 ClickHouse 서버입니다. ClickHouse는 자동으로 시작합니다`clickhouse-odbc-bridge` 그것이 필요한 때. ODBC bridge 프로그램은`clickhouse-server`. 

를 가진 필드`NULL` 외부 테이블의 값은 기본 데이터 형식의 기본값으로 변환됩니다. 예를 들어, 원격 MySQL 테이블 필드에`INT NULL` 0으로 변환되는 형태 (ClickHouse의 기본값`Int32` 데이터 형식). 

## 예 {# usage-example} 

** Pps 인터랙티브 MySQL을 설치 눈금 ** 

이 예에서는 Ubuntu Linux18.04 및 MySQL server5.7이 선택되어 있습니다. 

UnixODBC와 MySQL Connector가 설치되어 있는지 확인합니다. 

기본적으로 설치된 경우 패키지에서) ClickHouse 시작하고 사용자`clickhouse`. 따라서 MySQL 서버에서이 사용자를 작성하고 구성해야합니다. 

```bash 
$ sudo mysql 
``` 

```sql
mysql> CREATE USER 'clickhouse'@ 'localhost'IDENTIFIED BY 'clickhouse'; 
mysql> GRANT ALL PRIVILEGES ON *. * TO 'clickhouse'@ 'clickhouse'WITH GRANT OPTION; 
``` 

다음 연결을 설정합니다` / etc / odbc.ini`. 

```bash 
$ cat /etc/odbc.ini 
[mysqlconn] 
DRIVER = /usr/local/lib/libmyodbc5w.so 
SERVER = 127.0.0.1 
PORT = 3306 
DATABASE = test 
USERNAME = clickhouse 
PASSWORD = clickhouse 
``` 

연결을 확인하려면`isql` unixODBC의 임명에서 공용품. 

```bash 
$ isql -v mysqlconn 
+ ------------------------- + 
| Connected! | 
| | 
...


```text 
mysql> CREATE TABLE`test`.`test` (
    ->`int_id` INT NOT NULL AUTO_INCREMENT, 
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
+ - ---- + ---------- + ----- + ---------- + 
| 1 | NULL | 2 | NULL | 
+ ------ + ---------- + ----- + ---------- + 
1 row in set (0,00 sec) 
```

ClickHouse MySQL의 테이블에서 데이터 검색 : 

```sql 
SELECT * FROM odbc ( 'DSN = mysqlconn', 'test', 'test') 
``` 

```text 
┌─int_id─┬─int_nullable─┬─ float─┬─float_nullable─┐ 
│ 1 │ 0 │ 2 │ 0 │ 
└────────┴──────────────┴───────┴─ ───────────────┘ 
``` 

## 참조. {# see-also} 

- [ODBC 외부 사전 (../../ sql-reference / dictionaries / external-dictionaries / external-dicts-dict-sources.md # dicts-external_dicts_dict_sources-odbc) 
- [ODBC 테이블 엔진 (../../ engines / table-engines / integrations / odbc.md) 

원본 기사 (https://clickhouse.tech/docs/en/query_language/table_functions/jdbc/) <! - hide ->
