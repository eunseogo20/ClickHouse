---
machine_translated : true
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd
toc_priority : 38
toc_title : SHOW
---

# 쿼리를 표시 {# show-queries}

## SHOW CREATE TABLE {# show-create-table}

```sql
SHOW CREATE [TEMPORARY] [TABLE | DICTIONARY] [db] table [INTO OUTFILE filename] [FORMAT format]
```

하나를 반환합니다`String`- 유형 'statement'column, which contains a single value - the`CREATE` 지정한 개체를 만드는 데 사용하는 쿼리.

## SHOW DATABASES {# show-databases}

```sql
SHOW DATABASES [INTO OUTFILE filename] [FORMAT format]
```

목록의 모든 데이터베이스입니다.
이 쿼리는 다음과 같습니다`SELECT name FROM system.databases [INTO OUTFILE filename] [FORMAT format]`.

## SHOW PROCESSLIST {# show-processlist}

```sql
SHOW PROCESSLIST [INTO OUTFILE filename] [FORMAT format]
```

내용을 출력합니다. 시스템 프로세스 (../../ operations / system-tables.md # system_tables-processes) 현재 처리되고있는 쿼리의 목록을 포함하는 테이블. `SHOW PROCESSLIST` 쿼리.

그`SELECT * FROM system.processes` 쿼리를 돌려줍니다 데이터에 현재 모든 쿼리.

Tip (콘솔에서 실행) :

```bash
$ watch -n1 "clickhouse-client --query = 'SHOW PROCESSLIST'"
```

## SHOW TABLES {# show-tables}

테이블의 목록을 표시합니다.

```sql
SHOW [TEMPORARY] TABLES [{FROM | IN} <db>] [LIKE '<pattern>'| WHERE expr] [LIMIT <N> [INTO OUTFILE <filename>] [FORMAT <format>
```

만약`FROM` 절이 지정되지 않은 경우 쿼리는 현재 데이터베이스에서 테이블 목록을 반환합니다.

과 같은 결과를 얻을 수 있습니다`SHOW TABLES` 다음 방법으로 쿼리를 실행합니다 :

```sql
SELECT name FROM system.tables WHERE database = <db> AND name LIKE <pattern>] [LIMIT <N> [INTO OUTFILE <filename>] [FORMAT <format>
```

** 예 **

다음 쿼리는 테이블 목록에서 처음 두 행을 선택합니다. `system` 이름이 포함 된 데이터베이스`co`.

```sql
SHOW TABLES FROM system LIKE '% co %'LIMIT 2
```

```text
┌─name───────────────────────────┐
│ aggregate_function_combinators │
│ collations │
└────────────────────────────────┘
```

## SHOW DICTIONARIES {# show-dictionaries}

목록을 표시합니다 외부 사전 (../../ sql-reference / dictionaries / external-dictionaries / external-dicts.md).

```sql
SHOW DICTIONARIES [FROM <db>] [LIKE '<pattern>'] [LIMIT <N> [INTO OUTFILE <filename>] [FORMAT <format>
```

만약`FROM` 절이 지정되지 않은 경우 쿼리는 현재 데이터베이스에서 사전의 목록을 반환합니다.

과 같은 결과를 얻을 수 있습니다`SHOW DICTIONARIES` 다음 방법으로 쿼리를 실행합니다 :

```sql
SELECT name FROM system.dictionaries WHERE database = <db> AND name LIKE <pattern>] [LIMIT <N> [INTO OUTFILE <filename>] [FORMAT <format>
```

** 예 **

다음 쿼리는 테이블 목록에서 처음 두 행을 선택합니다. `system` 이름이 포함 된 데이터베이스`reg`.

```sql
SHOW DICTIONARIES FROM db LIKE '% reg %'LIMIT 2
```

```text
┌─name─────────┐
│ regions │
│ region_names │
└──────────────┘
```

## SHOW GRANTS {# show-grants-statement}

사용자의 권한을 표시합니다.

### 구문 {# show-grants-syntax}

```sql
SHOW GRANTS [FOR user]
```

User가 지정되어 있지 않은 경우 쿼리는 현재 사용자의 권한을 반환합니다.

## SHOW CREATE USER {# show-create-user-statement}

A에서 사용 된 매개 변수를 나타냅니다 사용자 만들기 (create.md # create-user-statement).

`SHOW CREATE USER` 사용자 암호를 출력하지 않습니다.

### 구문 {# show-create-user-syntax}

```sql
SHOW CREATE USER [name | CURRENT_USER]
```

## SHOW CREATE ROLE {# show-create-role-statement}

A에서 사용 된 매개 변수를 나타냅니다 역할 만들기 (create.md # create-role-statement)

### 구문 {# show-create-role-syntax}

```sql
SHOW CREATE ROLE name
```

## SHOW CREATE ROW POLICY {# show-create-row-policy-statement}

A에서 사용 된 매개 변수를 나타냅니다 행 정책 만들기 (create.md # create-row-policy-statement)

### 구문 {# show-create-row-policy-syntax}

```sql
SHOW CREATE [ROW] POLICY name ON [database] table
```

## SHOW CREATE QUOTA {# show-create-quota-statement}

A에서 사용 된 매개 변수입니다 할당량 만들기 (create.md # create-quota-statement)

### 구문 {# show-create-row-policy-syntax}

```sql
SHOW CREATE QUOTA [name | CURRENT]
```

## SHOW CREATE SETTINGS PROFILE {# show-create-settings-profile-statement}

A에서 사용 된 매개 변수를 나타냅니다 설정 프로파일 만들기 (create.md # create-settings-profile-statement)

### 구문 {# show-create-row-policy-syntax}

```sql
SHOW CREATE [SETTINGS] PROFILE name
```

원본 기사 (https://clickhouse.tech/docs/en/query_language/show/) <! - hide ->
