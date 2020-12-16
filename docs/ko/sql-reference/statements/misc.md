---
machine_translated : true
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd
toc_priority : 41
toc_title : "\ u305D \ u306E \ u4ED6"
---

# 기타 쿼리 {# miscellaneous-queries}

## ATTACH {#attach}

이 쿼리는 동일합니다`CREATE`하지만

- 말 대신`CREATE` 그것은 단어를 사용합니다`ATTACH`.
- 쿼리는 디스크에 데이터를 작성하는 것이 아니라 데이터가 이미 적절한 위치에 있다고 가정하고 테이블에 대한 정보를 서버에 추가하면됩니다.
    연결 쿼리를 실행하면 서버는 테이블의 존재를 알고 있습니다.

테이블이 이전에 분리 된 경우 (`DETACH`) 그 구조가 알려진 것을 의미한다, 당신은 구조를 정의하지 않고 약어를 사용할 수 있습니다.

```sql
ATTACH TABLE [IF NOT EXISTS] [db] name [ON CLUSTER cluster]
```

이 쿼리는 서버를 시작할 때 사용됩니다. 서버에 매장의 테이블 메타 데이터로 파일`ATTACH` 단순히 부팅시 실행되는 쿼리 (서버에서 명시 적으로 생성되는 시스템 테이블 제외).

## CHECK TABLE {# check-table}

테이블의 데이터가 손상 여부를 확인합니다.

```sql
CHECK TABLE [db] name
```

그`CHECK TABLE` query는 실제 파일 크기와 서버에 저장되어있는 기대 값을 비교합니다. 파일 크기가 저장된 값과 일치하지 않는 경우, 데이터가 손상된 것을 의미합니다. 이이 발생할 수 있습니다 예를 들어, 시스템 충돌시 쿼리를 실행합니다.

쿼리 응답은`result` 한 줄의 라인. 행의 값은 다음과 같습니다
[부울] (../../ sql-reference / data-types / boolean.md) 유형 :

- 0- 테이블의 데이터가 손상되어 있습니다.
- 1- 데이터 무결성을 유지합니다.

그`CHECK TABLE` 쿼리는 다음 테이블 엔진 :

- 로그 (../../ engines / table-engines / log-family / log.md)
- [TinyLog (../../ engines / table-engines / log-family / tinylog.md)
- 스트립 로그 (../../ engines / table-engines / log-family / stripelog.md)
- [메루게쯔리 족 (../../ engines / table-engines / mergetree-family / mergetree.md)

이것은 테이블이 다른 테이블 엔진의 원인이되는 예외입니다.

에서의 엔진`* Log` 가족은 실패의 자동 데이터 복구를 제공하지 않는다. 사용하는`CHECK TABLE` 적시에 데이터 손실을 추적하기위한 쿼리.

때문에`MergeTree` 가족 엔진`CHECK TABLE` 쿼리를 나타내는 상태 확인을위한 개인 데이터 테이블에 현지 서버입니다.

** 데이터가 손상된 경우 **

테이블이 손상된 경우 손상되지 않은 데이터를 다른 테이블에 복사 할 수 있습니다. 이렇게하려면 :

1. 손상된 테이블과 같은 구조의 새 테이블을 만듭니다. 이렇게 쿼리를 실행합니다`CREATE TABLE <new_table_name> AS <damaged_table_name>`.
2. 세트 [max_threads (../../ operations / settings / settings.md # settings-max_threads) 단일 스레드에서 다음 쿼리를 처리하려면 값을 1로 설정합니다. 이 쿼리`SET max_threads = 1`.
3. 쿼리`INSERT INTO <new_table_name> SELECT * FROM <damaged_table_name>`. 요청으로 인해 손상되지 않은 데이터가 손상된 테이블에서 다른 테이블로 복사됩니다. 손상된 부분의 이전 데이터 만 복사됩니다.
4. 다시 시작`clickhouse-client` 재설정하려면`max_threads` 값.

## DESCRIBE TABLE {# misc-describe-table}

```sql
DESC | DESCRIBE TABLE [db] table [INTO OUTFILE filename] [FORMAT format]
```

다음 값을 반환합니다`String` 유형 열 :

-`name` - Column name.
-`type`- Column type.
-`default_type` - Clause that is used in 기본 식 (create.md # create-default-values) (`DEFAULT``MATERIALIZED` 또는`ALIAS`) 기본식이 지정되어 있지 않은 경우, Column에는 빈 문자열이 포함됩니다.
-`default_expression` - Value specified in the`DEFAULT` 절.
-`comment_expression` - Comment text.

중첩 된 데이터 구조는 "expanded"형식. 각 열은 도트 후 다른 이름으로 별도로 표시됩니다.

## DETACH {#detach}

정보를 삭제합니다. 'name'서버에서 테이블. 서버는 테이블의 존재를 아는 것을 중지합니다.

```sql
DETACH TABLE [IF EXISTS] [db] name [ON CLUSTER cluster]
```

테이블의 데이터 또는 메타 데이터는 삭제되지 않습니다. 다음 서버를 시작할 때 서버는 메타 데이터를 읽고 테이블에 대해 다시 확인합니다.
마찬가지로, "detached"테이블을 사용하여 다시 붙일 수 있습니다`ATTACH` 쿼리 (메타 데이터가 포함되지 않은 시스템 테이블 제외).

없습니다`DETACH DATABASE` 쿼리.

## DROP {#drop}

이 쿼리 격려하고 조교 메뉴를 조립 :`DROP DATABASE`와`DROP TABLE`.

```sql
DROP DATABASE [IF EXISTS] db [ON CLUSTER cluster]
```

내부의 모든 테이블을 삭제 'db'데이터베이스를 삭제합니다. 'db'데이터베이스 자체.
만약`IF EXISTS` 데이터베이스가 존재하지 않으면 오류가 반환되지 않습니다.

```sql
DROP [TEMPORARY] TABLE [IF EXISTS] [db] name [ON CLUSTER cluster]
```

테이블을 삭제합니다.
만약`IF EXISTS` 테이블이 존재하지 않거나 데이터베이스가 존재하지 않으면 오류가 반환되지 않습니다.

    DROP DICTIONARY [IF EXISTS] [db] name

사전을 삭제합니다.
만약`IF EXISTS` 테이블이 존재하지 않거나 데이터베이스가 존재하지 않으면 오류가 반환되지 않습니다.

## DROP USER {# drop-user-statement}

사용자를 삭제합니다.

### 구문 {# drop-user-syntax}

```sql
DROP USER [IF EXISTS] name [, ...] [ON CLUSTER cluster_name]
```

## DROP ROLE {# drop-role-statement}

롤을 삭제합니다.

삭제 된 역할은 부여 된 모든 엔터티 취소됩니다.

### 구문 {# drop-role-syntax}

```sql
DROP ROLE [IF EXISTS] name [, ...] [ON CLUSTER cluster_name]
```

## DROP ROW POLICY {# drop-row-policy-statement}

행 정책을 삭제합니다.

삭제 된 행의 정책이 취소 모든 주체에 할당됩니다.

### 구문 {# drop-row-policy-syntax}

```sql
DROP [ROW] POLICY [IF EXISTS] name [...] ON [database] table [...] [ON CLUSTER cluster_name]
```

## DROP QUOTA {# drop-quota-statement}

할당량을 제거합니다.

삭제 범위가 취소 모든 주체에 할당됩니다.

### 구문 {# drop-quota-syntax}

```sql
DROP QUOTA [IF EXISTS] name [, ...] [ON CLUSTER cluster_name]
```

## DROP SETTINGS PROFILE {# drop-settings-profile-statement}

할당량을 제거합니다.

삭제 범위가 취소 모든 주체에 할당됩니다.

### 구문 {# drop-settings-profile-syntax}

```sql
DROP [SETTINGS] PROFILE [IF EXISTS] name [, ...] [ON CLUSTER cluster_name]
```

## EXISTS {# exists-statement}

```sql
EXISTS [TEMPORARY] [TABLE | DICTIONARY] [db] name [INTO OUTFILE filename] [FORMAT format]
```

하나를 반환합니다`UInt8`- 단일 값을 포함한 문자열을 입력합니다`0` 테이블 또는 데이터베이스가 존재하지 않는 경우, 또는`1` 지정된 데이터베이스에 테이블이 존재하는 경우.

## KILL QUERY {# kill-query-statement}

```sql
KILL QUERY [ON CLUSTER cluster]
  WHERE <where expression to SELECT FROM system.processes query>
  [SYNC | ASYNC | TEST]
  [FORMAT format]
```

현재 실행중인 쿼리를 강제로 종료하려고합니다.
종료 쿼리가 시스템에서 선택됩니다. 에 정의 된 기준을 사용하여 테이블을 처리합니다`WHERE` 절`KILL` 쿼리.

예 :

```sql
- Forcibly terminates all queries with the specified query_id :
KILL QUERY WHERE query_id = '2-857d-4a57-9ee0-327da5d60a90'

- Synchronously terminates all queries run by 'username':
KILL QUERY WHERE user = 'username'SYNC
```

읽기 전용 사용자는 자신의 쿼리 만 중지 할 수 있습니다.

기본적으로 비동기 버전의 쿼리가 사용됩니다 (`ASYNC`) 쿼리가 중지되었음을 확인을 기다리지 않는다.

동기화 버전 (`SYNC`) 모든 쿼리가 중지 될 때까지 대기하고 중지하면 각 프로세스에 대한 정보를 표시합니다.
응답은`kill_status` 열은 다음의 값을 가질 수 있습니다 :

1. 'finished'- The query was terminated successfully.
2. 'waiting'- Waiting for the query to end after sending it a signal to terminate.
3. The other values ​​explain why the query can not be stopped.

테스트 쿼리 (`TEST`) 사용자의 권한만을 확인하고 정지하는 쿼리의 목록을 표시합니다.

## KILL MUTATION {# kill-mutation}

```sql
KILL MUTATION [ON CLUSTER cluster]
  WHERE <where expression to SELECT FROM system.mutations query>
  [TEST]
  [FORMAT format]
```

취소 제거를 시도합니다 [돌연변이] (alter.md # alter-mutations) 현재 실행 중입니다 취소해야 돌연변이에서 선정합니다 [`system.mutations` (../../ operations / system- tables.md # system_tables-mutations)에 의해 지정된 필터를 사용하는 테이블`WHERE` 절`KILL` 쿼리.

테스트 쿼리 (`TEST`) 사용자의 권한만을 확인하고 정지하는 쿼리의 목록을 표시합니다.

예 :

```sql
- Cancel and remove all mutations of the single table :
KILL MUTATION WHERE database = 'default'AND table = 'table'

- Cancel the specific mutation :
KILL MUTATION WHERE database = 'default'AND table = 'table'AND mutation_id = 'mutation_3.txt'
```

The query is useful when a mutation is stuck and can not finish (eg if some function in the mutation query throws an exception when applied to the data contained in the table).

돌연변이에 의해 이미 변경된 내용은 롤백되지 않습니다.

## OPTIMIZE {# misc_operations-optimize}

```sql
OPTIMIZE TABLE [db] name [ON CLUSTER cluster] [PARTITION partition | PARTITION ID 'partition_id'] [FINAL] [DEDUPLICATE]
```

이 쿼리는 테이블 엔진을 사용하여 테이블의 데이터 부분의 예상치 못한 병합을 초기화하려고합니다. [메루게쯔리 (../../ engines / table-engines / mergetree-family / mergetree.md) 가족이다

그`OPTMIZE` 쿼리, 실현보기 (../../ engines / table-engines / special / materializedview.md) 그리고, 버퍼 (../../ engines / table-engines / special / buffer.md) 엔진이다 다른 테이블 엔진 지원합니다.

때`OPTIMIZE` 함께 사용됩니다 복제 병합 트리 (../../ engines / table-engines / mergetree-family / replication.md) 테이블 엔진 제품군은 ClickHouse 병합에 대한 작업을 만들고 모든 노드에서 실행을 기다립니다. `replication_alter_partitions_sync` 설정이 활성화되어 있습니다).

- 만약`OPTIMIZE` 어떤 이유로 병합을 실행하지 않고 클라이언트에 통지하지 않습니다. 알림을 활성화하려면 [optimize_throw_if_noop (../../ operations / settings / settings.md # setting-optimize_throw_if_noop) 설정.
-를 지정하면`PARTITION` 지정한 파티션 만 최적화됩니다. 파티션 식의 설정 방법 (alter.md # alter-how-to-specify-part-expr).
- 지정된 경우`FINAL` 최적화는 모든 데이터가 하나의 부분에 이미있는 경우에도 실행됩니다.
- 지정된 경우`DEDUPLICATE` 후 완전히 동일한 행이 중복 제거됩니다 (모든 열이 비교됩니다).

!!! warning "경고"
    `OPTIMIZE` 수없는 수정 "Too many parts"오류

## RENAME {# misc_operations-rename}

테이블의 이름을 변경합니다.

```sql
RENAME TABLE [db11] name11 TO [db12] name12, db21] name21 TO [db22] name22 ... [ON CLUSTER cluster]
```

모든 테이블 이름 변경 "글로벌 체인지의 잠금해야합니다. 테이블의 이름을 변경하는 것은 간단한 작업입니다. TO 후 별도의 데이터베이스를 지정한 경우, 테이블이 데이터베이스로 이동됩니다. 그러나 그 디렉토리의 데이터베이스에 저장되어있는 필요가있는 동일한 파일 시스템 (그렇지 않으면 오류를 반환한다.

## SET {# query-set}

```sql
SET param = value
```

할당`value`에`param` 설정 (../../ operations / settings / index.md) 현재 세션의 경우. 변경할 수 없습니다 서버 설정 (../../ operations / server-configuration-parameters / index.md) 여기다

지정한 설정 프로파일의 모든 값을 단일 쿼리에서 설정 할 수 있습니다.

```sql
SET profile = 'profile-name-from-the-settings-file'
```

자세한 내용은 [설정] (../../ operations / settings / settings.md).

## SET ROLE {# set-role-statement}

현재 사용자의 역할을 설정합니다.

### 구문 {# set-role-syntax}

```sql
SET ROLE {DEFAULT | NONE | role [...] | ALL | ALL EXCEPT role [...]}
```

## SET DEFAULT ROLE {# set-default-role-statement}

기본 사용자에게 역할을 설정합니다.

기본 역할을 자동으로 실행 된 사용자 로그인합니다. 기본으로 설정할 수는 이전에 부여 된 역할 만합니다. 역할이 사용자에게 부여되지 않은 경우 ClickHouse는 예외를 throw합니다.

### 구문 {# set-default-role-syntax}

```sql
SET DEFAULT ROLE {NONE | role [...] | ALL | ALL EXCEPT role [...]} TO {user | CURRENT_USER} [...]
```

### 예 {# set-default-role-examples}

여러 기본 역할을 사용자로 설정 :

```sql
SET DEFAULT ROLE role1, role2 ... TO user
```

부여 된 모든 롤을 기본 사용자로 설정합니다 :

```sql
SET DEFAULT ROLE ALL TO user
```

사용자의 기본 역할 제거 :

```sql
SET DEFAULT ROLE NONE TO user
```

세트의 부여 역할로 기본 예외를 제외하고 키트 :

```sql
SET DEFAULT ROLE ALL EXCEPT role1, role2 TO user
```

## TRUNCATE {# truncate-statement}

```sql
TRUNCATE TABLE [IF EXISTS] [db] name [ON CLUSTER cluster]
```

테이블에서 모든 데이터를 삭제합니다. 때 어구`IF EXISTS` 테이블이 존재하지 않는 경우 쿼리는 오류를 반환합니다.

그`TRUNCATE` 쿼리는 지원되지 않습니다 [표시] (../../ engines / table-engines / special / view.md) [파일] (../../ engines / table-engines / special /file.md) [URL] (../../ engines / table-engines / special / url.md) 및 Null (../../ engines / table-engines / special / null.md ) 테이블 엔진.

## USE {#use}

```sql
USE db
```

세션의 현재 데이터베이스를 설정할 수 있습니다.
현재 데이터베이스는 데이터베이스 쿼리에서 명시 적으로 정의되어 있지 않은 경우, 테이블의 검색에 사용됩니다.
세션의 개념이 없기 때문에 HTTP 프로토콜을 사용하는 경우이 쿼리를 실행할 수 없습니다.

원본 기사 (https://clickhouse.tech/docs/en/query_language/misc/) <! - hide ->
