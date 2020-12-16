---
machine_translated : true
machine_translated_rev : cbd8aa9052361a7ee11c209560cff7175c2b8e42
toc_priority : 39
toc_title : GRANT
---

# GRANT {#grant}

- 보조금 '권한 (# grant-privileges) ClickHouse 사용자 계정 또는 역할에.
- 원의 역할을 사용자 계정 또는 기타의 역할입니다.

권한을 취소하려면 [REVOKE (../../ sql-reference / statements / revoke.md) 계산서. 또한 부여 된 권한을 [SHOW GRANTS (../../ sql-reference / statements / show.md # show-grants-statement) 계산서.

## 권한 구문 부여 {# grant-privigele-syntax}

```sql
GRANT [ON CLUSTER cluster_name] privilege [(column_name [, ...])] [...] ON {db.table | db * | *. * | table | *} TO {user | role | CURRENT_USER} [...] [WITH GRANT OPTION]
```

-`privilege` - Type of privilege.
-`role` - ClickHouse user role.
-`user` - ClickHouse user account.

이`WITH GRANT OPTION` 절 부여`user` 또는`role` 실행 허가`GRANT` 쿼리. 사용자가 가지고있는 범위와 그 이하의 권한을 부여 할 수 있습니다.

## 롤 구문의 할당 {# assign-role-syntax}

```sql
GRANT [ON CLUSTER cluster_name] role [...] TO {user | another_role | CURRENT_USER} [...] [WITH ADMIN OPTION]
```

-`role` - ClickHouse user role.
-`user` - ClickHouse user account.

이`WITH ADMIN OPTION` 절 부여 [ADMIN OPTION] (# admin-option-privilege)에 대한 특권`user` 또는`role`.

## 사용법 {# grant-usage}

사용하려면`GRANT` 계정에는`GRANT OPTION` 특권 권한을 부여 할 계정 권한의 범위 내에서만입니다.

예를 들어, 관리자는`john` 쿼리에 의한 계정 :

```sql
GRANT SELECT (x, y) ON db.table TO john WITH GRANT OPTION
```

즉`john` 수행 할 권한이 있습니다 :

-`SELECT x, y FROM db.table`.
-`SELECT x FROM db.table`.
-`SELECT y FROM db.table`.

`john` 실행할 수없는`SELECT z FROM db.table`이`SELECT * FROM db.table` 또한 사용할 수 없습니다. 이 쿼리를 처리하면 ClickHouse은 데이터를 반환하지 않습니다. `x` 그리고`y`. 유일한 예외는 테이블에만 포함되어있는 경우입니다`x` 그리고`y` 라인. 이 경우 ClickHouse는 모든 데이터를 반환합니다.

또한`john`은`GRANT OPTION` 따라서 동일하거나 더 작은 범위의 권한을 가진 다른 사용자에게 부여 할 수 있습니다.

별표를 사용할 수있는 권한을 지정 (`*`) 테이블 또는 데이터베이스 이름 대신. 예를 들어,`GRANT SELECT ON db. * TO john` 쿼리 허가`john` 실행하려면`SELECT` 모든 테이블에 대한 쿼리`db` 데이터베이스. 또한 데이터베이스 이름을 생략 할 수 있습니다. 이 경우 현재 데이터베이스에 권한이 부여됩니다. 예를 들어,`GRANT SELECT ON * TO john` 현재 데이터베이스`GRANT SELECT ON mytable TO john` 권한을 부여합니다. `mytable` 현재 데이터베이스의 테이블.

액세스`system` database는 항상 허용됩니다 (이 데이터베이스는 쿼리 처리에 사용되기 때문에).

하나의 쿼리에서 여러 계정에 여러 권한을 부여 할 수 있습니다. 쿼리`GRANT SELECT, INSERT ON *. * TO john, robin` 계정을 허용`john` 그리고`robin` 실행하려면`INSERT` 그리고`SELECT` 쿼리 테이블의 모든 데이터베이스 서버에 복사합니다 .

## 특권 {# grant-privileges}

특권은 특정 유형의 쿼리를 실행할 수있는 권한입니다.

권한은 계층 구조가 있습니다. 허용되는 쿼리의 세트는 권한 범위에 따라 달라집니다.

특권 계층 :

- [SELECT] (# grant-select)
- [INSERT] (# grant-insert)
- [ALTER] (# grant-alter)
    -`ALTER TABLE`
        -`ALTER UPDATE`
        -`ALTER DELETE`
        -`ALTER COLUMN`
            -`ALTER ADD COLUMN`
            -`ALTER DROP COLUMN`
            -`ALTER MODIFY COLUMN`
            -`ALTER COMMENT COLUMN`
            -`ALTER CLEAR COLUMN`
            -`ALTER RENAME COLUMN`
        -`ALTER INDEX`
            -`ALTER ORDER BY`
            -`ALTER ADD INDEX`
            -`ALTER DROP INDEX`
            -`ALTER MATERIALIZE INDEX`
            -`ALTER CLEAR INDEX`
        -`ALTER CONSTRAINT`
            -`ALTER ADD CONSTRAINT`
            -`ALTER DROP CONSTRAINT`
        -`ALTER TTL`
        -`ALTER MATERIALIZE TTL`
        -`ALTER SETTINGS`
        -`ALTER MOVE PARTITION`
        -`ALTER FETCH PARTITION`
        -`ALTER FREEZE PARTITION`
    -`ALTER VIEW`
        -`ALTER VIEW REFRESH`
        -`ALTER VIEW MODIFY QUERY`
- CREATE (# grant-create)
    -`CREATE DATABASE`
    -`CREATE TABLE`
    -`CREATE VIEW`
    -`CREATE DICTIONARY`
    -`CREATE TEMPORARY TABLE`
- [DROP] (# grant-drop)
    -`DROP DATABASE`
    -`DROP TABLE`
    -`DROP VIEW`
    -`DROP DICTIONARY`
- [TRUNCATE (# grant-truncate)
- OPTIMIZE (# grant-optimize)
- [SHOW] (# grant-show)
    -`SHOW DATABASES`
    -`SHOW TABLES`
    -`SHOW COLUMNS`
    -`SHOW DICTIONARIES`
- [KILL QUERY (# grant-kill-query)
- [ACCESS MANAGEMENT] (# grant-access-management)
    -`CREATE USER`
    -`ALTER USER`
    -`DROP USER`
    -`CREATE ROLE`
    -`ALTER ROLE`
    -`DROP ROLE`
    -`CREATE ROW POLICY`
    -`ALTER ROW POLICY`
    -`DROP ROW POLICY`
    -`CREATE QUOTA`
    -`ALTER QUOTA`
    -`DROP QUOTA`
    -`CREATE SETTINGS PROFILE`
    -`ALTER SETTINGS PROFILE`
    -`DROP SETTINGS PROFILE`
    -`SHOW ACCESS`
        -`SHOW_USERS`
        -`SHOW_ROLES`
        -`SHOW_ROW_POLICIES`
        -`SHOW_QUOTAS`
        -`SHOW_SETTINGS_PROFILES`
    -`ROLE ADMIN`
- [SYSTEM] (# grant-system)
    -`SYSTEM SHUTDOWN`
    -`SYSTEM DROP CACHE`
        -`SYSTEM DROP DNS CACHE`
        -`SYSTEM DROP MARK CACHE`
        -`SYSTEM DROP UNCOMPRESSED CACHE`
    -`SYSTEM RELOAD`
        -`SYSTEM RELOAD CONFIG`
        -`SYSTEM RELOAD DICTIONARY`
        -`SYSTEM RELOAD EMBEDDED DICTIONARIES`
    -`SYSTEM MERGES`
    -`SYSTEM TTL MERGES`
    -`SYSTEM FETCHES`
    -`SYSTEM MOVES`
    -`SYSTEM SENDS`
        -`SYSTEM DISTRIBUTED SENDS`
        -`SYSTEM REPLICATED SENDS`
    -`SYSTEM REPLICATION QUEUES`
    -`SYSTEM SYNC REPLICA`
    -`SYSTEM RESTART REPLICA`
    -`SYSTEM FLUSH`
        -`SYSTEM FLUSH DISTRIBUTED`
        -`SYSTEM FLUSH LOGS`
- [INTROSPECTION (# grant-introspection)
    -`addressToLine`
    -`addressToSymbol`
    -`demangle`
- [SOURCES] (# grant-sources)
    -`FILE`
    -`URL`
    -`REMOTE`
    -`YSQL`
    -`ODBC`
    -`JDBC`
    -`HDFS`
    -`S3`
- [dictGet (# grant-dictget)

이 계층이 어떻게 처리되는지 예 :

-이`ALTER` 혜택을 포함하여 다른 모든`ALTER *`특권이다
-`ALTER CONSTRAINT` 포함한`ALTER ADD CONSTRAINT` 그리고`ALTER DROP CONSTRAINT` 특권이다

권한은 다른 수준에서 적용됩니다. 수준을 아는 것은 특권에 사용할 수있는 구문을 시사하고 있습니다.

레벨 (하위에서 상위로) :

-`COLUMN` - Privilege can be granted for column table, database, or globally.
-`TABLE` - Privilege can be granted for table, database, or globally.
-`VIEW` - Privilege can be granted for view, database, or globally.
-`DICTIONARY` - Privilege can be granted for dictionary, database, or globally.
-`DATABASE` - Privilege can be granted for database or globally.
-`GLOBAL` - Privilege can be granted only globally.
-`GROUP` - Groups privileges of different levels. When`GROUP`- 수준의 권한이 부여되어 사용되는 구문에 해당하는 그룹의 권한 만 부여됩니다.

허용되는 구문의 예 :

-`GRANT SELECT (x) ON db.table TO user`
-`GRANT SELECT ON db. * TO user`

금지 된 구문의 예 :

-`GRANT CREATE USER (x) ON db.table TO user`
-`GRANT CREATE USER ON db. * TO user`

특별 특전 [ALL] (# grant-all) 사용자 계정 또는 역할에 모든 권한을 부여합니다.

기본적으로 사용자 계정 또는 역할에는 권한이 없습니다.

사용자 또는 역할에 권한이없는 경우는 다음과 같이 표시됩니다 [NONE] (# grant-none) 특권

구현하여 쿼리에는 일련의 권한이 있어야합니다. 예를 들어, RENAME (../../ sql-reference / statements / misc.md # misc_operations-rename) 쿼리에 다음 권한이 있어야합니다 :`SELECT``CREATE TABLE``INSERT` 그리고` DROP TABLE`.

### SELECT {# grant-select}

실행을 허용 [SELECT] (../../ sql-reference / statements / select / index.md) 쿼리.

특권 레벨 :`COLUMN`.

** 설명 **

사용자의 교부를받는이 권한으로 실행`SELECT` 지정된 테이블 및 데이터베이스의 지정된 열 목록에 대한 쿼리. 사용자가 다른 열을 포함하는 경우 쿼리는 데이터를 반환하지 않습니다.

다음의 특권을 고려하십시오 :

```sql
GRANT SELECT (x, y) ON db.table TO john
```

이 특권은`john` 실행하려면`SELECT` 데이터를 포함하는 쿼리`x` 및 / 또는`y`의 열`db.table` 예를 들어,`SELECT x FROM db.table``john` 실행할 수없는` SELECT z FROM db.table`이`SELECT * FROM db.table` 또한 사용할 수 없습니다. 이 쿼리를 처리하면 ClickHouse은 데이터를 반환하지 않습니다. `x` 그리고`y`. 유일한 예외는 테이블에만 포함되어있는 경우입니다`x` 그리고`y`이 경우 ClickHouse는 모든 데이터를 반환합니다.

### INSERT {# grant-insert}

실행을 허용하려면 INSERT (../../ sql-reference / statements / insert-into.md) 쿼리.

특권 레벨 :`COLUMN`.

** 설명 **

사용자의 교부를받는이 권한으로 실행`INSERT` 지정된 테이블 및 데이터베이스의 지정된 열 목록에 대한 쿼리. 사용자가 다른 열을 포함하는 경우 지정된 쿼리는 데이터를 삽입하지 않습니다.

** 예 **

```sql
GRANT INSERT (x, y) ON db.table TO john
```

허용 된 권한은`john`에 데이터를 삽입하려면`x` 및 / 또는`y`의 열`db.table`.

### ALTER {# grant-alter}

실행을 허용 [ALTER] (../../ sql-reference / statements / alter.md) 다음의 특권 계층에 따라 쿼리를 실행합니다 :

-`ALTER` 레벨 :`COLUMN`.
    -`ALTER TABLE` 레벨 :`GROUP`
        -`ALTER UPDATE` 레벨 :`COLUMN` 별명 :`UPDATE`
        -`ALTER DELETE` 레벨 :`COLUMN` 별명 :`DELETE`
        -`ALTER COLUMN` 레벨 :`GROUP`
            -`ALTER ADD COLUMN` 레벨 :`COLUMN` 별명 :`ADD COLUMN`
            -`ALTER DROP COLUMN` 레벨 :`COLUMN` 별명 :`DROP COLUMN`
            -`ALTER MODIFY COLUMN` 레벨 :`COLUMN` 별명 :`MODIFY COLUMN`
            -`ALTER COMMENT COLUMN` 레벨 :`COLUMN` 별명 :`COMMENT COLUMN`
            -`ALTER CLEAR COLUMN` 레벨 :`COLUMN` 별명 :`CLEAR COLUMN`
            -`ALTER RENAME COLUMN` 레벨 :`COLUMN` 별명 :`RENAME COLUMN`
        -`ALTER INDEX` 레벨 :`GROUP` 별명 :`INDEX`
            -`ALTER ORDER BY` 레벨 :`TABLE` 별명 :`ALTER MODIFY ORDER BY``MODIFY ORDER BY`
            -`ALTER ADD INDEX` 레벨 :`TABLE` 별명 :`ADD INDEX`
            -`ALTER DROP INDEX` 레벨 :`TABLE` 별명 :`DROP INDEX`
            -`ALTER MATERIALIZE INDEX` 레벨 :`TABLE` 별명 :`MATERIALIZE INDEX`
            -`ALTER CLEAR INDEX` 레벨 :`TABLE` 별명 :`CLEAR INDEX`
        -`ALTER CONSTRAINT` 레벨 :`GROUP` 별명 :`CONSTRAINT`
            -`ALTER ADD CONSTRAINT` 레벨 :`TABLE` 별명 :`ADD CONSTRAINT`
            -`ALTER DROP CONSTRAINT` 레벨 :`TABLE` 별명 :`DROP CONSTRAINT`
        -`ALTER TTL` 레벨 :`TABLE` 별명 :`ALTER MODIFY TTL``MODIFY TTL`
        -`ALTER MATERIALIZE TTL` 레벨 :`TABLE` 별명 :`MATERIALIZE TTL`
        -`ALTER SETTINGS` 레벨 :`TABLE` 별명 :`ALTER SETTING``ALTER MODIFY SETTING``MODIFY SETTING`
        -`ALTER MOVE PARTITION` 레벨 :`TABLE` 별명 :`ALTER MOVE PART``MOVE PARTITION``MOVE PART`
        -`ALTER FETCH PARTITION` 레벨 :`TABLE` 별명 :`FETCH PARTITION`
        -`ALTER FREEZE PARTITION` 레벨 :`TABLE` 별명 :`FREEZE PARTITION`
    -`ALTER VIEW` 레벨 :`GROUP`
        -`ALTER VIEW REFRESH` 레벨 :`VIEW` 별명 :`ALTER LIVE VIEW REFRESH``REFRESH VIEW`
        -`ALTER VIEW MODIFY QUERY` 레벨 :`VIEW` 별명 :`ALTER TABLE MODIFY QUERY`

이 계층이 어떻게 처리되는지 예 :

-이`ALTER` 혜택을 포함하여 다른 모든`ALTER *`특권이다
-`ALTER CONSTRAINT` 포함한`ALTER ADD CONSTRAINT` 그리고`ALTER DROP CONSTRAINT` 특권이다

** 참고 **

-이`MODIFY SETTING` 권한을 변경할 수있는 테이블 엔진을 설정합니다. 설정 및 서버 구성 매개 변수에 영향을주지 않습니다.
-이`ATTACH` 작업이 필요합니다 [CREATE (# grant-create) 특권
-이`DETACH` 작업이 필요합니다 [DROP] (# grant-drop) 특권
-에 의해 돌연변이를 중지하려면 KILL MUTATION (../../ sql-reference / statements / misc.md # kill-mutation) 쿼리, 당신은이 돌연변이를 시작할 수있는 권한을 가져야 수 있습니다. 예를 들어,`ALTER UPDATE` 문의 필요합니다`ALTER UPDATE``ALTER TABLE` 또는`ALTER` 특권이다

### CREATE {# grant-create}

실행을 허용 [CREATE (../../ sql-reference / statements / create.md) 그리고 [ATTACH (../../ sql-reference / statements / misc.md # attach) DDL- 다음 특권 계층에 따른 쿼리 :

-`CREATE` 레벨 :`GROUP`
    -`CREATE DATABASE` 레벨 :`DATABASE`
    -`CREATE TABLE` 레벨 :`TABLE`
    -`CREATE VIEW` 레벨 :`VIEW`
    -`CREATE DICTIONARY` 레벨 :`DICTIONARY`
    -`CREATE TEMPORARY TABLE` 레벨 :`GLOBAL`

** 참고 **

- 삭제하고 만든 테이블은 사용자 요구 [DROP] (# grant-drop).

### DROP {# grant-drop}

실행을 허용 [DROP] (../../ sql-reference / statements / misc.md # drop) 그리고 [DETACH (../../ sql-reference / statements / misc.md # detach) 다음 특권 계층에 따라 쿼리를 실행합니다 :

-`DROP` 레벨 :
    -`DROP DATABASE` 레벨 :`DATABASE`
    -`DROP TABLE` 레벨 :`TABLE`
    -`DROP VIEW` 레벨 :`VIEW`
    -`DROP DICTIONARY` 레벨 :`DICTIONARY`

### TRUNCATE {# grant-truncate}

실행을 허용 [TRUNCATE (../../ sql-reference / statements / misc.md # truncate-statement) 쿼리.

특권 레벨 :`TABLE`.

### OPTIMIZE {# grant-optimize}

실행을 허용 [OPTIMIZE TABLE (../../ sql-reference / statements / misc.md # misc_operations-optimize) 쿼리.

특권 레벨 :`TABLE`.

### SHOW {# grant-show}

실행을 허용하는`SHOW``DESCRIBE``USE`, and`EXISTS` 다음 특권 계층에 따라 쿼리를 실행합니다 :

-`SHOW` 레벨 :`GROUP`
    -`SHOW DATABASES` 레벨 :`DATABASE`. 실행을 허용하는`SHOW DATABASES``SHOW CREATE DATABASE``USE <database>`쿼리.
    -`SHOW TABLES` 레벨 :`TABLE`. 실행을 허용하는`SHOW TABLES``EXISTS <table>``CHECK <table>`쿼리.
    -`SHOW COLUMNS` 레벨 :`COLUMN`. 실행을 허용하는`SHOW CREATE TABLE``DESCRIBE` 쿼리.
    -`SHOW DICTIONARIES` 레벨 :`DICTIONARY`. 실행을 허용하는`SHOW DICTIONARIES``SHOW CREATE DICTIONARY``EXISTS <dictionary>`쿼리.

** 참고 **

사용자는`SHOW` 특전의 경우는 다른 혜택에 대한 지정된 테이블, 사전 및 데이터베이스입니다.

### KILL QUERY {# grant-kill-query}

실행을 허용 [KILL (../../ sql-reference / statements / misc.md # kill-query-statement) 다음의 특권 계층에 따라 쿼리를 실행합니다 :

특권 레벨 :`GLOBAL`.

** 참고 **

`KILL QUERY` 특전으로 사용자를 살 쿼리의 기타 사용자

### ACCESS MANAGEMENT {# grant-access-management}

사용자, 역할 및 행 정책을 관리하는 쿼리를 실행할 수 있습니다.

-`ACCESS MANAGEMENT` 레벨 :`GROUP`
    -`CREATE USER` 레벨 :`GLOBAL`
    -`ALTER USER` 레벨 :`GLOBAL`
    -`DROP USER` 레벨 :`GLOBAL`
    -`CREATE ROLE` 레벨 :`GLOBAL`
    -`ALTER ROLE` 레벨 :`GLOBAL`
    -`DROP ROLE` 레벨 :`GLOBAL`
    -`ROLE ADMIN` 레벨 :`GLOBAL`
    -`CREATE ROW POLICY` 레벨 :`GLOBAL` 별명 :`CREATE POLICY`
    -`ALTER ROW POLICY` 레벨 :`GLOBAL` 별명 :`ALTER POLICY`
    -`DROP ROW POLICY` 레벨 :`GLOBAL` 별명 :`DROP POLICY`
    -`CREATE QUOTA` 레벨 :`GLOBAL`
    -`ALTER QUOTA` 레벨 :`GLOBAL`
    -`DROP QUOTA` 레벨 :`GLOBAL`
    -`CREATE SETTINGS PROFILE` 레벨 :`GLOBAL` 별명 :`CREATE PROFILE`
    -`ALTER SETTINGS PROFILE` 레벨 :`GLOBAL` 별명 :`ALTER PROFILE`
    -`DROP SETTINGS PROFILE` 레벨 :`GLOBAL` 별명 :`DROP PROFILE`
    -`SHOW ACCESS` 레벨 :`GROUP`
        -`SHOW_USERS` 레벨 :`GLOBAL` 별명 :`SHOW CREATE USER`
        -`SHOW_ROLES` 레벨 :`GLOBAL` 별명 :`SHOW CREATE ROLE`
        -`SHOW_ROW_POLICIES` 레벨 :`GLOBAL` 별명 :`SHOW POLICIES``SHOW CREATE ROW POLICY``SHOW CREATE POLICY`
        -`SHOW_QUOTAS` 레벨 :`GLOBAL` 별명 :`SHOW CREATE QUOTA`
        -`SHOW_SETTINGS_PROFILES` 레벨 :`GLOBAL` 별명 :`SHOW PROFILES``SHOW CREATE SETTINGS PROFILE``SHOW CREATE PROFILE`

이`ROLE ADMIN` 특전있는 사용자에게 할당 및 취소하기위한 역할을 포함하는 것은 아니므로 사용자 관리 옵션입니다.

### SYSTEM {# grant-system}

사용자에게 실행 허용 [SYSTEM] (../../ sql-reference / statements / system.md) 다음의 특권 계층에 따라 쿼리를 실행합니다.

-`SYSTEM` 레벨 :`GROUP`
    -`SYSTEM SHUTDOWN` 레벨 :`GLOBAL` 별명 :`SYSTEM KILL``SHUTDOWN`
    -`SYSTEM DROP CACHE` 별명 :`DROP CACHE`
        -`SYSTEM DROP DNS CACHE` 레벨 :`GLOBAL` 별명 :`SYSTEM DROP DNS``DROP DNS CACHE``DROP DNS`
        -`SYSTEM DROP MARK CACHE` 레벨 :`GLOBAL` 별명 :`SYSTEM DROP MARK``DROP MARK CACHE``DROP MARKS`
        -`SYSTEM DROP UNCOMPRESSED CACHE` 레벨 :`GLOBAL` 별명 :`SYSTEM DROP UNCOMPRESSED``DROP UNCOMPRESSED CACHE``DROP UNCOMPRESSED`
    -`SYSTEM RELOAD` 레벨 :`GROUP`
        -`SYSTEM RELOAD CONFIG` 레벨 :`GLOBAL` 별명 :`RELOAD CONFIG`
        -`SYSTEM RELOAD DICTIONARY` 레벨 :`GLOBAL` 별명 :`SYSTEM RELOAD DICTIONARIES``RELOAD DICTIONARY``RELOAD DICTIONARIES`
        -`SYSTEM RELOAD EMBEDDED DICTIONARIES` 레벨 :`GLOBAL` 별명 : R`ELOAD EMBEDDED DICTIONARIES`
    -`SYSTEM MERGES` 레벨 :`TABLE` 별명 :`SYSTEM STOP MERGES``SYSTEM START MERGES``STOP MERGES``START MERGES`
    -`SYSTEM TTL MERGES` 레벨 :`TABLE` 별명 :`SYSTEM STOP TTL MERGES``SYSTEM START TTL MERGES``STOP TTL MERGES``START TTL MERGES`
    -`SYSTEM FETCHES` 레벨 :`TABLE` 별명 :`SYSTEM STOP FETCHES``SYSTEM START FETCHES``STOP FETCHES``START FETCHES`
    -`SYSTEM MOVES` 레벨 :`TABLE` 별명 :`SYSTEM STOP MOVES``SYSTEM START MOVES``STOP MOVES``START MOVES`
    -`SYSTEM SENDS` 레벨 :`GROUP` 별명 :`SYSTEM STOP SENDS``SYSTEM START SENDS``STOP SENDS``START SENDS`
        -`SYSTEM DISTRIBUTED SENDS` 레벨 :`TABLE` 별명 :`SYSTEM STOP DISTRIBUTED SENDS``SYSTEM START DISTRIBUTED SENDS``STOP DISTRIBUTED SENDS``START DISTRIBUTED SENDS`
        -`SYSTEM REPLICATED SENDS` 레벨 :`TABLE` 별명 :`SYSTEM STOP REPLICATED SENDS``SYSTEM START REPLICATED SENDS``STOP REPLICATED SENDS``START REPLICATED SENDS`
    -`SYSTEM REPLICATION QUEUES` 레벨 :`TABLE` 별명 :`SYSTEM STOP REPLICATION QUEUES``SYSTEM START REPLICATION QUEUES``STOP REPLICATION QUEUES``START REPLICATION QUEUES`
    -`SYSTEM SYNC REPLICA` 레벨 :`TABLE` 별명 :`SYNC REPLICA`
    -`SYSTEM RESTART REPLICA` 레벨 :`TABLE` 별명 :`RESTART REPLICA`
    -`SYSTEM FLUSH` 레벨 :`GROUP`
        -`SYSTEM FLUSH DISTRIBUTED` 레벨 :`TABLE` 별명 :`FLUSH DISTRIBUTED`
        -`SYSTEM FLUSH LOGS` 레벨 :`GLOBAL` 별명 :`FLUSH LOGS`

이`SYSTEM RELOAD EMBEDDED DICTIONARIES` 의해 암묵적으로 부여되는 특권`SYSTEM RELOAD DICTIONARY ON *. *`특권이다

### INTROSPECTION {# grant-introspection}

사용을 허용 [내성 (../../ operations / optimizing-performance / sampling-query-profiler.md) 기능.

-`INTROSPECTION` 레벨 :`GROUP` 별명 :`INTROSPECTION FUNCTIONS`
    -`addressToLine` 레벨 :`GLOBAL`
    -`addressToSymbol` 레벨 :`GLOBAL`
    -`demangle` 레벨 :`GLOBAL`

### SOURCES {# grant-sources}

외부 데이터 소스의 사용을 허용합니다. 에 적용됩니다 [표 엔진 (../../ engines / table-engines / index.md) 그리고 테이블 함수 (../../ sql-reference / table-functions / index.md # table -functions).

-`SOURCES` 레벨 :`GROUP`
    -`FILE` 레벨 :`GLOBAL`
    -`URL` 레벨 :`GLOBAL`
    -`REMOTE` 레벨 :`GLOBAL`
    -`YSQL` 레벨 :`GLOBAL`
    -`ODBC` 레벨 :`GLOBAL`
    -`JDBC` 레벨 :`GLOBAL`
    -`HDFS` 레벨 :`GLOBAL`
    -`S3` 레벨 :`GLOBAL`

이`SOURCES` 특권은 모든 소스의 사용을 가능하게합니다. 또한 각 소스에 개별적으로 권한을 부여 할 수 있습니다. 소스를 사용하려면 추가 권한이 필요합니다.

예 :

- 테이블을 만들려면 [MySQL 테이블 엔진 (../../ engines / table-engines / integrations / mysql.md) 필요합니다`CREATE TABLE (ON db.table_name)`그리고`MYSQL` 특권 이다
-를 사용하려면 mysql 테이블 함수 (../../ sql-reference / table-functions / mysql.md) 필요합니다`CREATE TEMPORARY TABLE` 그리고`MYSQL` 특권이다

### dictGet {# grant-dictget}

-`dictGet` 별명 :`dictHas``dictGetHierarchy``dictIsIn`

사용자에게 실행 허용 [dictGet (../../ sql-reference / functions / ext-dict-functions.md # dictget), 디쿠타스 (../../ sql-reference / functions / ext -dict-functions.md # dicthas), dictGetHierarchy (../../ sql-reference / functions / ext-dict-functions.md # dictgethierarchy), 지쿠찌신 (../../ sql-reference /functions/ext-dict-functions.md#dictisin) 기능.

특권 레벨 :`DICTIONARY`.

** 예 **

-`GRANT dictGet ON mydb.mydictionary TO john`
-`GRANT dictGet ON mydictionary TO john`

### ALL {# grant-all}

보조금의 모든 권한을 규제 조직의 사용자 계정 또는 역할을한다.

### NONE {# grant-none}

권한은 부여되지 않습니다.

### ADMIN OPTION {# admin-option-privilege}

이`ADMIN OPTION` 특전있는 사용자 보조금의 역할을 다른 사용자입니다.

원본 기사 (https://clickhouse.tech/docs/en/query_language/grant/) <! - hide ->
