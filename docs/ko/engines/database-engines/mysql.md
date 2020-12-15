---
machine_translated : true
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd
toc_priority : 30
toc_title : MySQL
---

# MySQL {#mysql}

로 연결하는 데이터베이스의 원격 MySQL 서버를 실행`INSERT`와`SELECT` ClickHouse와 MySQL간에 데이터를 교환하기위한 쿼리.

그`MySQL` 데이터베이스 엔진`SHOW TABLES` 또는`SHOW CREATE TABLE`.

다음 쿼리를 실행할 수 없습니다 :

-`이름 바꾸기`
-`테이블 만들기`
-`AGE`

## 데이터베이스 만들기 {# creating a-database}

```SQL
데이터베이스 생성 [존재하지 않는 경우] db_name [클러스터 클러스터에서]
ENGINE = MySQL ( 'host : port', [ 'database'| database], 'user', 'password')
```

** 엔진 변수 **

-`host : port` — MySQL 서버 주소.
-`database` — 원격 데이터베이스 이름입니다.
-`user` — MySQL 사용자.
-`password` — 사용자 비밀번호입니다.

## 데이터 형식 지원 {# data_types-support}

| MySQL | 클릭 하우스 |
|----------------------------------|--------------------------------------------------------------|
| 서명되지 않은 TINYINT | [UInt8] (../../ sql-reference / data-types / int-uint.md) |
| TINYINT | [Int8] (../../ sql-reference / data-types / int-uint.md) |
| UNSIGNED SMALLINT | [UInt16] (../../ sql-reference / data-types / int-uint.md) |
| SMALLINT | [Int16] (../../ sql-reference / data-types / int-uint.md) |
| UNSIGNED INT, UNSIGNED MEDIUMINT | [UInt32] (../../ sql-reference / data-types / int-uint.md) |
| INT, MEDIUMINT | [Int32] (../../ sql-reference / data-types / int-uint.md) |
| UNSIGNED BIGINT | [UInt64] (../../ sql-reference / data-types / int-uint.md) |
| BIGINT | [Int64] (../../ sql-reference / data-types / int-uint.md) |
| FLOAT | [Float32] (../../ sql-reference / data-types / float.md) |
| 더블 | [Float64] (../../ sql-reference / data-types / float.md) |
| DATE | 날짜 (../../ sql-reference / data-types / date.md) |
| DATETIME, TIMESTAMP | [DateTime] (../../ sql-reference / data-types / datetime.md) |
| 바이너리 | [FixedString] (../../ sql-reference / data-types / fixedstring.md) |

다른 모든 MySQL 데이터 형식으로 변환 된 문자열 (../../ sql-reference / data-types / string.md).

[Null 가능 (../../ sql-reference / data-types / nullable.md) 지원됩니다.

## 사용 예 {# examples-of-use}

MySQL의 테이블 :

```텍스트
mysql> USE 테스트;
데이터베이스 변경

mysql> CREATE TABLE`mysql_table` (
    ->`int_id` INT NOT NULL AUTO_INCREMENT,
    ->`float` FLOAT NOT NULL,
    -> PRIMARY KEY (`int_id`));
쿼리 OK, 영향을받는 행 0 개 (0,09 초)

mysql> mysql_table에 삽입 (`int_id`,`float`) VALUES (1,2);
쿼리 OK, 1 개 행이 영향을 받음 (0,00 초)

mysql> select * from mysql_table;
+------+-----+
| int_id | 가치 |
+------+-----+
|      1 |     2 |
+------+-----+
1 행 세트 (0,00 초)
```

ClickHouse 데이터베이스 MySQL 서버와의 데이터 교환 :

```SQL
CREATE DATABASE mysql_db ENGINE = MySQL ( 'localhost : 3306', 'test', 'my_user', 'user_password')
```

```SQL
데이터베이스 표시
```

```텍스트
┌─name─────┐
│ 기본 │
│ mysql_db │
│ 시스템 │
└──────────┘
```

```SQL
mysql_db에서 테이블보기
```

```텍스트
┌─name─────────┐
│ mysql_table │
└──────────────┘
```

```SQL
SELECT * FROM mysql_db.mysql_table
```

```텍스트
┌─int_id─┬─value─┐
│      1 │     2 │
└────────┴───────┘
```

```SQL
mysql_db.mysql_table 값에 INSERT INSERT (3,4)
```

```SQL
SELECT * FROM mysql_db.mysql_table
```

```텍스트
┌─int_id─┬─value─┐
│      1 │     2 │
│      3 │     4 │
└────────┴───────┘
```

원본 기사 (https://clickhouse.tech/docs/en/database_engines/mysql/) <! - hide ->
