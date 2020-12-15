--- 
machine_translated : true 
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd 
toc_priority : 35 
toc_title : ODBC 
--- 

# ODBC {# table-engine-odbc} 

ClickHouse이 외부 데이터베이스에 연결할 수 있도록하는 [ODBC] (https : //en.wikipedia. org / wiki / Open_Database_Connectivity) 

ODBC 연결을 안전하게 구현하기 위해 clickHouse 다른 프로그램을 사용합니다`clickhouse-odbc-bridge`. ODBC 드라이버가 직접로드되는 경우`clickhouse-server` 드라이버 문제 에서 충돌 ClickHouse 서버입니다. ClickHouse는 자동으로 시작합니다`clickhouse-odbc-bridge` 그것이 필요한 때. ODBC bridge 프로그램은`clickhouse-server`. 

이 엔진은 [Null 가능 (../../../ sql-reference / data-types / nullable.md) 데이터 형식입니다. 

## 테이블 만들기 {# creating a-table} 

```sql 
CREATE TABLE [IF NOT EXISTS] [db] table_name [ON CLUSTER cluster] 
(
    name1 [type1] 
    name2 [type2, 
    ... 
) 
ENGINE = ODBC (connection_settings, external_database, external_table) 
``` 

에 대한 자세한 설명을 참조하십시오 [CREATE TABLE (../../../ sql-reference / statements / create.md # create-table-query) 쿼리. 

테이블 구조는 소스 테이블 구조와 다를 수 있습니다 : 

- 열 이름은 소스 테이블과 동일해야하지만, 이러한 열의 일부를 임의의 순서로 사용할 수 있습니다. 
- 열 형식은 소스 테이블의 형태와 다를 수 있습니다. ClickHouse은 [캐스트] (../../../ sql-reference / functions / type-conversion-functions.md # type_conversion_function-cast) ClickHouse 데이터 형의 값. 

** 엔진 변수 ** 

-`connection_settings` - Name of the section with connection settings in the `odbc.ini` 파일 
## 예 {# usage-example } 
-`external_database` - Name of a database in an external DBMS.
-`external_table` - Name of a table in the`external_database`. 


** 획득 데이터에서 현지 MySQL 설치 눈금 ** 

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

다음 연결을 설정합니다`/ etc / odbc.ini`. 

```bash 
$ cat /etc/odbc.ini 
[mysqlconn] 
DRIVER = /usr/local/lib/libmyodbc5w.so 
SERVER = 127.
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
``` 

MySQL의 테이블 : 

```text 
mysql> CREATE TABLE`test`.`test` ( 
    ->`int_id` INT NOT NULL AUTO_INCREMENT, 
    ->`int_nullable` INT NULL DEFAULT NULL, 
    ->`float` FLOAT NOT NULL, 
    ->` float_nullable` FLOAT NULL DEFAULT NULL, 
    -> PRIMARY KEY (`int_id`)); 
Query OK, 0 rows affected (0,09 sec)

mysql> insert into test (`int_id``float`) VALUES (1,2);
Query OK, 1 row affected (0,00 sec) 

mysql> select * from test; 
+ ------ + ---------- + ----- + ------ ---- + 
| int_id | int_nullable | float | float_nullable | 
+ ------ + ---------- + ----- + ---------- + 
| 1 | NULL | 2 | NULL | 
+ ------ + ---------- + ----- + ---------- + 
1 row in set ( 0,00 sec) 
``` 

ClickHouse 테이블, MySQL 테이블에서 데이터를 검색하는 : 

```sql 
CREATE TABLE odbc_t 
( 
    `int_id` Int32, 
    `float_nullable` Nullable (Float32) 
) 
ENGINE = ODBC ( 'DSN = mysqlconn' 'test', 'test') 
``` 

```sql 
SELECT * FROM odbc_t 
``` 

```text 
┌─int_id─┬─float_nullable─┐
│ 1 │ ᴺᵁᴸᴸ │ 
└────────┴────────────────┘ 
``` 

## 참조. {# see-also} 

- [ODBC 외부 사전 (../../../ sql-reference / dictionaries / external-dictionaries / external-dicts-dict-sources.md # dicts-external_dicts_dict_sources-odbc) 
- [ ODBC 테이블 함수 (../../../ sql-reference / table-functions / odbc.md) 

원본 기사 (https://clickhouse.tech/docs/en/operations/table_engines/odbc/ ) <! - hide ->
