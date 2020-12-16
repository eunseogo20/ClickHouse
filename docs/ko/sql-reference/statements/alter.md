---
machine_translated : true
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd
toc_priority : 36
toc_title : ALTER
---

## ALTER {#query_language_queries_alter}

그`ALTER` 쿼리 만 지원`* MergeTree` 테이블뿐만 아니라`Merge`와`Distributed` 쿼리에 여러 종류가 있습니다.

### 라인 작업 {# column-manipulations}

테이블 구조 변경.

```sql
ALTER TABLE [db] .name [ON CLUSTER cluster] ADD | DROP | CLEAR | COMMENT | MODIFY COLUMN ...
```

쿼리는 쉼표로 구분 된 작업 목록을 지정합니다.
각 동작은 열에 대한 작업입니다.

다음 작업이 지원됩니다 :

- [ADD COLUMN] (# alter_add-column) - Adds a new column to the table.
- [DROP COLUMN] (# alter_drop-column) - Deletes the column.
- [CLEAR COLUMN] (# alter_clear-column) - Resets column values.
- [COMMENT COLUMN] (# alter_comment-column) - Adds a text comment to the column.
- [MODIFY COLUMN] (# alter_modify-column) - Changes column 's type, default expression and TTL.

이러한 행동을 자세히 설명합니다.

#### ADD COLUMN {# alter_add-column}

```sql
ADD COLUMN [IF NOT EXISTS] name [type] [default_expr] [codec] [AFTER name_after]
```

지정된 테이블에 새 열을 추가합니다`name``type`,`codec` (create.md # codecs)와`default_expr` (절을 참조 기본 식 (create.md # create -default-values)).

만약`IF NOT EXISTS` 열이 이미 존재하는 경우, 쿼리는 오류를 반환하지 않습니다. 지정한 경우`AFTER name_after` (다른 열의 이름) 열은 테이블의 컬럼 목록에서 지정된 열 뒤에 추가됩니다. 그렇지 않으면 열이 테이블의 마지막에 추가됩니다. 테이블의 선두에 열을 추가하는 방법은 없다는 점에 유의하십시오. 일련의 행동에 대해`name_after` 이전 작업 중 하나에서 추가 된 열 이름을 지정할 수 있습니다.

열을 추가하면 데이터에서 작업을 수행하지 않고 테이블 구조가 변경됩니다. 그 후, 데이터는 디스크에 표시되지 않습니다`ALTER` 테이블에서 읽을 때 열의 데이터가없는 경우는 디폴트 값이 입력됩니다 (기본식이있는 경우 기본 식을 실행하거나 제로 열 표시 디스크와 통합 데이터 부품 (메루게쯔리 (../../ engines / table-engines / mergetree-family / mergetree.md)).

이러한 접근 방법은`ALTER` 오래된 데이터의 양을 증가시키지 않고 즉시 쿼리.

예 :

```sql
ALTER TABLE visits ADD COLUMN browser String AFTER user_id
```

#### DROP COLUMN {# alter_drop-column}

```sql
DROP COLUMN [IF EXISTS] name
```

이름의 열을 제거합니다`name` 만약`IF EXISTS` 열이없는 경우 쿼리는 오류를 반환하지 않습니다.

컴퓨터의 데이터를 삭제하는 파일 시스템. 이 삭제 전체 파일 쿼리가 거의 완료합니다.

예 :

```sql
ALTER TABLE visits DROP COLUMN browser
```

#### CLEAR COLUMN {# alter_clear-column}

```sql
CLEAR COLUMN [IF EXISTS] name IN PARTITION partition_name
```

재설정 데이터 라인의 지정된 파티션 부분의 파티션 이름 설정에 대한 자세한보기 [파티션 표현식을 지정하는 방법 (# alter-how-to-specify-part-expr).

만약`IF EXISTS` 열이없는 경우 쿼리는 오류를 반환하지 않습니다.

예 :

```sql
ALTER TABLE visits CLEAR COLUMN browser IN PARTITION tuple ()
```

#### COMMENT COLUMN {# alter_comment-column}

```sql
COMMENT COLUMN [IF EXISTS] name 'comment'
```

열 코멘트를 추가합니다. 만약`IF EXISTS` 열이없는 경우 쿼리는 오류를 반환하지 않습니다.

각 열은 하나의 댓글이 있습니다. 열 코멘트가 이미 존재하는 경우 새 코멘트 전에 댓글을 덮어 씁니다.

코멘트는`comment_expression` 의해 반환되는 열 [DESCRIBE TABLE (misc.md # misc-describe-table) 쿼리.

예 :

```sql
ALTER TABLE visits COMMENT COLUMN browser 'The table shows the browser used for accessing the site.'
```

#### MODIFY COLUMN {# alter_modify-column}

```sql
MODIFY COLUMN [IF EXISTS] name [type] [default_expr] [TTL]
```

이 쿼리는`name` 열 속성 :

- 유형

- 기본 식

- TTL

        For examples of columns TTL modifying, see [Column TTL (../ engines / table_engines / mergetree_family / mergetree.md # mergetree-column-ttl).

만약`IF EXISTS` 열이없는 경우 쿼리는 오류를 반환하지 않습니다.

형식을 변경하면 값은 다음과 같이 변환됩니다 [토타이뿌 (../../ sql-reference / functions / type-conversion-functions.md) 기능을 그들에게 적용했다. 기본 식만이 변경된 경우 쿼리는 복잡한 처리를하지 않고, 거의 즉시 완료됩니다.

예 :

```sql
ALTER TABLE visits MODIFY COLUMN browser Array (String)
```

Changing the column type is the only complex action - it changes the contents of files with data. For large tables, this may take a long time.

어떤 처리 단계가 있습니다 :

- 준비 일시 (새로운 파일이 수정 데이터입니다.
- 오래된 파일의 이름을 변경한다.
- 일시 (새) 파일의 이름을 이전 이름으로 변경합니다.
- 오래된 파일을 삭제합니다.

첫 번째 단계의 시간이 걸립니다. 이 단계에서 오류가 발생하면 데이터는 변경되지 않습니다.
연속하는 하나의 단계에서 오류가 발생하면 데이터를 수동으로 복원 할 수 있습니다. 예외는 오래된 파일이 파일 시스템에서 제거되었지만 새 파일의 데이터가 디스크에 기록되지 않고 손실 된 경우입니다.

그`ALTER` 열을 변경하는 쿼리가 복제됩니다. 명령은 ZooKeeper에 저장되며 각 복제본은 그들을 적용합니다. 모든`ALTER` 쿼리는 같은 순서로 실행됩니다. 쿼리는 다른 복제본에서 적절한 조치가 완료 될 때까지 기다립니다. 그러나 복제 된 테이블의 열을 변경하는 쿼리는 중단되고 모든 작업이 비동기 적으로 실행됩니다.

#### 쿼리 제한 변경 {# alter-query-limitations}

그`ALTER` 쿼리를 생성 및 삭제 개별 요소 (열)을 중첩 된 데이터 구조가 전체 중첩 데이터 구조입니다. 중첩 된 데이터 구조를 추가하려면 다음과 같은 이름의 열을 추가 할 수 있습니다`name.nested_name` 그리고 유형`Array (T)`중첩 된 데이터 구조는 도트 전에 같은 접두사를 가지는 이름을 가진 여러 배열 열과 동일합니다.

기본 키 또는 샘플링 키의 열을 제거하는 것은 지원되지 않습니다. `ENGINE` 식). 기본 키에 포함되는 열의 형태를 변경하는 것은 이러한 변경으로 인해 데이터가 변경되지 않은 경우에만 가능합니다 (예를 들어, 열거 형 값을 추가하거나 형태를 변경`DateTime`에`UInt32`).

만약`ALTER` 쿼리에 필요한 테이블의 변경을 실시하는 것만으로는 충분하지 않습니다. [INSERT SELECT (insert-into.md # insert_query_insert-select)를 사용하여 테이블을 전환합니다 [RENAME] (misc.md # misc_operations-rename) 이전 테이블을 조회하여 삭제합니다. 를 사용 할 수 있습니다 클릭 하우스 - 복사기 (../../ operations / utilities / clickhouse-copier.md)의 대안으로`INSERT SELECT` 쿼리.

그`ALTER` 쿼리의 모든 블록을로드라고 쓰고 있습니다. 즉,`SELECT` 때 실행되는`ALTER` 쿼리는`ALTER` 쿼리는 그것이 완료 될 때까지 기다립니다. 동시에 같은 테이블에 대한 모든 새로운 쿼리는 그동안 대기합니다`ALTER` 달리고있는

데이터 자체를 저장하지 않는 테이블의 경우 (다음과 같이`Merge`와`Distributed`)`ALTER` 테이블 구조를 변경하는 것만으로, 하위 테이블의 구조는 변경되지 않습니다. 예를 들어,`Distributed` 테이블, 당신도 실행해야합니다`ALTER` 테이블의 모든 수 있습니다.

### 키 식에 의한 조작 {# manipulations-with-key-expressions}

다음 명령 :

```sql
MODIFY ORDER BY new_expression
```

이것은,`MergeTree` (../../ engines / table-engines / mergetree-family / mergetree.md) 가족 (포함
복제 (../../ engines / table-engines / mergetree-family / replication.md) 테이블). 이 명령은
[소트 키 (../../ engines / table-engines / mergetree-family / mergetree.md) 테이블
에`new_expression` (식 또는 식의 튜플). 기본 키는 그대로 유지됩니다.

이 명령은 메타 데이터 만 변경한다는 의미에서 경량입니다. 데이터 부분의 속성을 유지하려면
행은 정렬 키 식으로 정렬됩니다 기존의 열을 포함하는 식을 추가 할 수 없습니다
에 의해 추가 된 열만`ADD COLUMN` 같은 명령`ALTER` 쿼리).

### 조작 데이터를 페이 지표 {# manipulations-with-data-skipping-indices}

이것은,`* MergeTree` (../../ engines / table-engines / mergetree-family / mergetree.md) 가족 (포함
복제 (../../ engines / table-engines / mergetree-family / replication.md) 테이블). 다음 작업
사용할 수 있습니다 :

-`ALTER TABLE [db] .name ADD INDEX name expression TYPE type GRANULARITY value AFTER name [AFTER name2]`- 부가가치 지수의 설명을 테이블 메타 데이터를 지칭합니다.

-`ALTER TABLE [db] .name DROP INDEX name` - 제거 할 지표의 설명에서 테이블 메타 데이터를 삭제를 행 지수의 파일에서 디스크.

이 명령은 가볍고 있다는 의미에서만 변화 메타 데이터의 삭제 파일입니다.
또한 그들은 복제됩니다 (ZooKeeper를 통해 인덱스 메타 데이터를 동기화).

### 제약 개입 {# manipulations-with-constraints}

자세한 내용은 여기 [제약] (create.md # constraints)

제약 조건은 다음 구문을 사용하여 추가하거나 삭제할 수 있습니다 :

```sql
ALTER TABLE [db] .name ADD CONSTRAINT constraint_name CHECK expression;
ALTER TABLE [db] .name DROP CONSTRAINT constraint_name;
```

쿼리에 추가 또는 제거 약 메타 데이터의 제약에서 테이블에서 신속하게 처리합니다.

제약 체크 * 실행되지 않습니다 * 기존의 데이터가 추가 된 경우.

변경된 내용의 복제 테이블 방송에 사육사에 적용됩니다 다른 복제.

### 파티션 및 부품 조작 {# alter_manipulations-with-partitions}

다음 작업은 [분할] (../../ engines / table-engines / mergetree-family / custom-partitioning-key.md) 사용할 수 있습니다 :

- [DETACH PARTITION (# alter_detach-partition) - Moves a partition to the`detached` 디렉토리와 그것을 잊어.
- [DROP PARTITION (# alter_drop-partition) - Deletes a partition.
- [ATTACH PART \ | PARTITION (# alter_attach-partition) - Adds a part or partition from the`detached` 테이블에 디렉토리.
- [ATTACH PARTITION FROM (# alter_attach-partition-from) - Copies the data partition from one table to another and adds.
- [REPLACE PARTITION (# alter_replace-partition) - 복사 할 데이터를 분할에서 테이블에 대체.
- [MOVE PARTITION TO TABLE (# alter_move_to_table-partition) (# alter_move_to_table-partition) - 데이터 - 파티션 테이블에서 다른 테이블로 이동합니다.
- [CLEAR COLUMN IN PARTITION (# alter_clear-column-partition) - 파티션 내의 지정된 열의 값을 재설정합니다.
- [CLEAR INDEX IN PARTITION (# alter_clear-index-partition) - 재설정 지정된 보조 인덱스 파티션
- [FREEZE PARTITION (# alter_freeze-partition) - Creates a backup of a partition.
- [FETCH PARTITION (# alter_fetch-partition) - Downloads a partition from another server.
- [MOVE PARTITION \ | PART (# alter_move-partition) - Move partition / data part to another disk or volume.

<! - ->

#### DETACH PARTITION {# alter_detach-partition}

```sql
ALTER TABLE table_name DETACH PARTITION partition_expr
```

지정된 파티션의 모든 데이터를`detached` 디렉토리. 서버를 잊는다는 주택의 데이터를 분배하지 않는 경우가 있습니다. 서버는이 데이터를 알지 못한다. [ATTACH (# alter_attach-partition) 쿼리.

예 :

```sql
ALTER TABLE visits DETACH PARTITION 201901
```

섹션에서 파티션 식의 설정 정보 파티션 표현식을 지정하는 방법 (# alter-how-to-specify-part-expr).

쿼리가 실행 된 후 데이터를 사용하여 좋아하는 일을 할 수 있습니다. `detached` directory - delete it from the file system, or just leave it.

This query is replicated - it moves the data to the`detached` 모든 복제본이 쿼리는 리더 복제에서만 실행할 수 있습니다. 하는 경우는 복제는 전통적인 접근하는`SELECT` 쿼리를 실행하는 시스템 복제 (../../ operations / system-tables.md # system_tables-replicas) 테이블. 또는`DETACH` 쿼리는 모든 복제 - 모든 복제 예외를 throw 이외의 리더 복제.

#### DROP PARTITION {# alter_drop-partition}

```sql
ALTER TABLE table_name DROP PARTITION partition_expr
```

지정한 파티션을 테이블에서 제거합니다. 이 쿼리 태그의 분할로 정지 또는 삭제하겠습니다 데이터를 영구적으로 약 10 분입니다.

섹션에서 파티션 식의 설정 정보 파티션 표현식을 지정하는 방법 (# alter-how-to-specify-part-expr).

The query is replicated - it deletes data on all replicas.

#### DROP DETACHED PARTITION \ | PART {# alter_drop-detached}

```sql
ALTER TABLE table_name DROP DETACHED PARTITION | PART partition_expr
```

지정된 부분 또는 지정된 파티션의 모든 부분을`detached`.
섹션에서 파티션 식 설정에 대한 자세한 파티션 식의 지정 방법 (# alter-how-to-specify-part-expr).

#### ATTACH PARTITION \ | PART {# alter_attach-partition}

```sql
ALTER TABLE table_name ATTACH PARTITION | PART partition_expr
```

테이블에 데이터를 추가합니다. `detached` 디렉토리. 전체 파티션 또는 다른 부분의 데이터를 추가 할 수 있습니다. 예 :

```sql
ALTER TABLE visits ATTACH PARTITION 201901;
ALTER TABLE visits ATTACH PART 201901_2_2_0;
```

섹션에서 파티션 식 설정에 대한 자세한 파티션 식의 지정 방법 (# alter-how-to-specify-part-expr).

이 쿼리는 복제됩니다. 복제 - 초기 검사가 있는지 여부의 데이터를`detached` 디렉토리. 데이터가 존재하는 경우, 쿼리는 그 무결성을 확인합니다. 모두가 올바른 경우 쿼리는 데이터를 테이블에 추가합니다. 다른 모든 복제본을 다운로드에서 데이터 복제 - 초기입니다.

그래서 데이터를 넣을 수 있습니다`detached` 디렉토리를 하나의 복제에두고`ALTER ... ATTACH` 모든 복제 테이블에 쿼리.

#### ATTACH PARTITION FROM {# alter_attach-partition-from}

```sql
ALTER TABLE table2 ATTACH PARTITION partition_expr FROM table1
```

이 쿼리는 데이터 파티션을`table1`에`table2`에 데이터를 추가합니다. `table2` 데이터는 삭제되지 않는 것에주의하십시오`table1`.

쿼리를 성공적으로 실행하려면 다음 조건을 충족해야합니다 :

- 두 테이블을 만들 때 필요한 구조입니다.
- 두 테이블을 만들 때 필요한 분할.

#### REPLACE PARTITION {# alter_replace-partition}

```sql
ALTER TABLE table2 REPLACE PARTITION partition_expr FROM table1
```

이 쿼리는 데이터 파티션을`table1`에`table2` 기존의 파티션을 대체`table2` 데이터는 삭제되지 않는 것에주의하십시오`table1`.

쿼리를 성공적으로 실행하려면 다음 조건을 충족해야합니다 :

- 두 테이블을 만들 때 필요한 구조입니다.
- 두 테이블을 만들 때 필요한 분할.

#### MOVE PARTITION TO TABLE {# alter_move_to_table-partition}

```sql
ALTER TABLE table_source MOVE PARTITION partition_expr TO TABLE table_dest
```

이 쿼리는 데이터 파티션을`table_source`에`table_dest`에서 데이터를 삭제하면`table_source`.

쿼리를 성공적으로 실행하려면 다음 조건을 충족해야합니다 :

- 두 테이블을 만들 때 필요한 구조입니다.
- 두 테이블을 만들 때 필요한 분할.
- 두 테이블과 동일해야한다면 엔진입니다. (복제 또는 비 복제)
- 두 테이블을 만들 때 필요한 가끔 있습니다.

#### CLEAR COLUMN IN PARTITION {# alter_clear-column-partition}

```sql
ALTER TABLE table_name CLEAR COLUMN column_name IN PARTITION partition_expr
```

재설정 값으로 지정된 컬럼이있었습니다. 만약`DEFAULT` 절이 결정된 테이블을 만들 때이 쿼리는 지정된 기본값으로 열 값을 설정합니다.

예 :

```sql
ALTER TABLE visits CLEAR COLUMN hour in PARTITION 201902
```

#### FREEZE PARTITION {# alter_freeze-partition}

```sql
ALTER TABLE table_name FREEZE [PARTITION partition_expr]
```

이 쿼리를 만들고 지역의 백업 지정된 파티션 만약`PARTITION` 조항을 생략하여 쿼리를 작성하고 백업 분할합니다.

!!! note "주"
    전체 백업 절차는 서버를 중지하지 않고 실행됩니다.

오래된 스타일의 테이블의 경우, 파티션 이름의 접두사를 지정할 수 있습니다 (예를 들어, '2019') 쿼리를 만들고 백업을위한 모든 해당 격벽 섹션에서 파티션 식의 설정 정보 파티션 표현식 지정 방법 (# alter-how-to-specify-part-expr).

실행시 데이터 스냅 샷의 경우 쿼리는 테이블 데이터에 대한 하드 링크를 만듭니다. Hardlinks에 설치되어있는 디렉토리`/ var / lib / clickhouse / shadow / N / ...`여기에서 :

-`/ var / lib / clickhouse /`설정에 지정된 작업 ClickHouse 디렉토리입니다.
-`N`의 증분 수 있습니다.

!!! note "주"
    를 사용하는 경우 테이블의 데이터 저장 용 디스크 세트 (../../ engines / table-engines / mergetree-family / mergetree.md # table_engine-mergetree-multiple-volumes)는`shadow / N` 디렉토리가 표시 될 때마다 디스크 데이터를 저장하는 부품과 함께에 따르면`PARTITION` 식.

백업의 내부와 같은 디렉토리 구조가 만들어집니다`/ var / lib / clickhouse /`쿼리 실행 'chmod'모든 파일의 금지에 씁니다.

백업을 만든 후 데이터를 복사하려면`/ var / lib / clickhouse / shadow /`로컬 서버에서 삭제합니다. 또한`ALTER t FREEZE PARTITION` 쿼리는 복제되지 않습니다. 하는 지역의 백업 현지 서버입니다.

쿼리를 백업 세트에서 첫 때문에 기다리고 있기 때문에, 현재의 쿼리에 대응하는 테이블을 완성했다.

`ALTER TABLE t FREEZE PARTITION` 복사 전용 데이터가없는 테이블 메타 데이터를 지칭합니다. 백업 테이블 메타 데이터 복사 파일`/ var / lib / clickhouse / metadata / database / table.sql`

백업에서 데이터를 복원하려면 :

1. 테이블이 존재하지 않는 경우 테이블을 만듭니다. 쿼리를 표시하려면을 사용합니다. sql 파일 (대체`ATTACH` 그래서`CREATE`).
2. 데이터를 복사합니다`data / database / table /`백업의 디렉토리`/ var / lib / clickhouse / data / database / table / detached /`디렉토리.
3. 달려라. `ALTER TABLE t ATTACH PARTITION` 테이블에 데이터를 추가하는 쿼리.

복원에서 백업을 필요로하지 않는 정지 서버에 복사합니다.

백업 및 데이터 복원에 대한 자세한 내용은 데이터 다시 아 (../../ operations / backup.md) 섹션

#### CLEAR INDEX IN PARTITION {# alter_clear-index-partition}

```sql
ALTER TABLE table_name CLEAR INDEX index_name IN PARTITION partition_expr
```

쿼리는 다음과 같이 작동합니다`CLEAR COLUMN` 그러나 열 데이터가 아닌 인덱스를 재설정합니다.

#### FETCH PARTITION {# alter_fetch-partition}

```sql
ALTER TABLE table_name FETCH PARTITION partition_expr FROM 'path-in-zookeeper'
```

다운로드 파티션에서 다른 서버입니다. 이 쿼리만을 재현합니다.

쿼리는 다음 작업을 수행합니다 :

1. 다운로드 파티션에서 지정된 더 - 셔.에서 'path-in-zookeeper'ZooKeeper에 샤드 경로를 지정해야합니다.
2. 다음 쿼리는 다운로드 한 데이터를`detached` 디렉토리`table_name` 테이블. 사용하는 [ATTACH PARTITION \ | PART (# alter_attach-partition) 테이블에 데이터를 추가하는 쿼리.

예를 들면 :

```sql
ALTER TABLE users FETCH PARTITION 201902 FROM '/ clickhouse / tables / 01-01 / visits';
ALTER TABLE users ATTACH PARTITION 201902;
```

또한 :

- 그`ALTER ... FETCH PARTITION` 쿼리는 복제되지 않습니다. 그것은 분할을 놓습니다`detached` 디렉토리의 현지 서버입니다.
- 그`ALTER TABLE ... ATTACH` 쿼리가 복제됩니다. 모든 복제본에 데이터를 추가합니다. 데이터는 복제 중 하나에 추가됩니다. `detached` 디렉토리 및 다른 사람에 - 인접한 복제에서.

다운로드하기 전에 시스템 여부를 선택하면 파티션이 존재하면 테이블 구조. 가장 적절한 복제는 정상적인 복제에서 자동으로 선택됩니다.

쿼리는 호출되지만`ALTER TABLE` 테이블 구조를 변경하지 않고 테이블에서 사용 가능한 데이터를 즉시 변경할 수 없습니다.

#### MOVE PARTITION \ | PART {# alter_move-partition}

파티션 또한`MergeTree`- 엔진 테이블. 볼 다중 블록 장치를위한 데이터 저장 (../../ engines / table-engines / mergetree-family / mergetree.md # table_engine-mergetree-multiple-volumes).

```sql
ALTER TABLE table_name MOVE PARTITION | PART partition_expr TO DISK | VOLUME 'disk_name'
```

그`ALTER TABLE t MOVE` 쿼리 :

- 재현이 다른 복제본에서 보관.
- 지정된 디스크 또는 볼류 스토리지 정책에 지정된 데이터 이동의 조건을 적용 할 수없는 경우 Query는 오류를 반환합니다.
- 복귀 할 수있는 오류의 경우 데이터의 이동에 이행하고있는 배경에는 동시`ALTER TABLE t MOVE` 쿼리 또는 백그라운드 데이터 병합의 결과입니다. 이 경우 사용자는 추가 작업을 수행하지 마십시오.

예 :

```sql
ALTER TABLE hits MOVE PART '20190301_14343_16206_438'TO VOLUME 'slow'
ALTER TABLE hits MOVE PARTITION '2019-09-01'TO DISK 'fast_ssd'
```

#### 파티션 식의 설정 방법 {# alter-how-to-specify-part-expr}

파티션 표현식을 지정하려면`ALTER ... PARTITION` 다르게 검색어 :

-의 값으로`partition` 열`system.parts` 테이블. 예를 들어,`ALTER TABLE visits DETACH PARTITION 201901`.
- 테이블 열에서 식으로. 상수와 상수 식을 지원하고 있습니다. 예를 들어,`ALTER TABLE visits DETACH PARTITION toYYYYMM (toDate ( '2019-01-25'))`.
- 파티션 ID를 사용합니다. Partition ID는 파일 시스템 및 ZooKeeper 파티션의 이름으로 사용되는 파티션의 문자열 식별자 (가능하면 인간이 읽을 수있는)입니다. 파티션 ID는`PARTITION ID` 한 따 quotes로 둘러싸인 어구. 예를 들어,`ALTER TABLE visits DETACH PARTITION ID '201901'`.
-에서 ALTER ATTACH PART (# alter_attach-partition) 및 DROP DETACHED PART (# alter_drop-detached) 부품의 이름을 지정하려면 문자열 리터럴을 사용합니다. `name` 열 시스템 detached_parts (../../ operations / system-tables.md # system_tables-detached_parts) 테이블. 예를 들어,`ALTER TABLE visits ATTACH PART '201901_1_1_0'`.

이용 따옴표를 지정하면 파티션의 입구는 눈길 벽면 녹화를 표현. 예를 들어,`String` 그 이름을 인용 부호로 지정해야합니다 (` '`)를 위해`Date`와`Int *`형태 따옴표는 필요하지 않습니다.

오래된 스타일의 테이블의 경우 파티션을 숫자로 지정할 수 있습니다`201901` 또는 문자열` '201901'` 새로운 스타일의 테이블의 구문은 형태에 따라서 더 엄격합니다 (값의 입력 형식 파서와 마찬가지로).

위의 모든 규칙은 OPTIMIZE (misc.md # misc_operations-optimize) 쿼리. 를 지정하는 경우에만 분배시 최적화, 비 나누어 진 테이블 세트의 표현`PARTITION tuple ()`예 :

```sql
OPTIMIZE TABLE table_not_partitioned PARTITION tuple () FINAL;
```

예`ALTER ... PARTITION` 쿼리 테스트에서 나타납니다 [`00502_custom_partitioning_local` (https://github.com/ClickHouse/ClickHouse/blob/master/tests/queries/0_stateless/00502_custom_partitioning_local.sql)과 [`00502_custom_partitioning_replicated_zookeeper` (https://github.com/ClickHouse/ClickHouse/blob/master/tests/queries/0_stateless/00502_custom_partitioning_replicated_zookeeper.sql).

### 테이블 TTL 개입 {# manipulations-with-table-ttl}

변경할 수 있습니다 테이블 TTL (../../ engines / table-engines / mergetree-family / mergetree.md # mergetree-table-ttl) 다음 양식의 요청에 :

```sql
ALTER TABLE table-name MODIFY TTL ttl-expression
```

### ALTER 쿼리의 동시성 {# synchronicity-of-alter-queries}

복제 불가능한 테이블의 경우 모든`ALTER` 쿼리는 동 기적으로 실행됩니다. 위한 replicatable 테이블, 쿼리 만 추가 지시를 위해 적절한 조치를`ZooKeeper` 그리고 액션 자체는 최대한 빨리 실행됩니다. 그러나 쿼리는 모든 복제본에서 이러한 작업이 완료 될 때까지 대기 할 수 있습니다.

때문에`ALTER ... ATTACH | DETACH | DROP` 쿼리를 사용할 수 있습니다`replication_alter_partitions_sync` 대기를 설정하는 설정.
가능한 값 :`0` - do not wait;`1` - only wait for own execution (default);`2` - wait for all.

### 돌연변이 {# alter-mutations}

돌연변이는 테이블의 행을 변경하거나 삭제할 수 ALTER query variant입니다. 표준과는 대조적으로`UPDATE`와`DELETE` 포인트 데이터의 변경, 돌연변이를 목적으로 한 쿼리는 테이블에서 많은 행을 변경하는 무거운 작업을 목적으로하고 있습니다. 를 위해 지원되는`MergeTree` 가족의 테이블 엔진 등 엔진의 복제입니다.

기존 테이블은 그대로 (변환은 필요하지 않습니다) 돌연변이의 준비가되어 있지만, 첫 번째 돌연변이가 테이블에 적용되면 해당 메타 데이터 형식

현재 사용 가능한 명령 :

```sql
ALTER TABLE [db] table DELETE WHERE filter_expr
```

그`filter_expr` 형식이어야합니다`UInt8`이 쿼리는이식이 0이 아닌 값을 취하는 테이블의 행을 삭제합니다.

```sql
ALTER TABLE [db] table UPDATE column1 = expr1 [...] WHERE filter_expr
```

그`filter_expr` 형식이어야합니다`UInt8`이 쿼리는 지정된 열의 값을, 지정된 열의 행 해당 표현식의 값으로 업데이트합니다. `filter_expr` 0이 아닌 값을 취합니다. 값 열 형식으로 캐스팅됩니다. `CAST` 운영자 기본 키 또는 파티션 키의 계산에 사용되는 열의 업데이트는 지원되지 않습니다.

```sql
ALTER TABLE [db] table MATERIALIZE INDEX name IN PARTITION partition_name
```

쿼리를 재건 보조 지수`name` 파티션`partition_name`.

하나의 쿼리를 포함 할 수 여러 명령을 쉼표로 구분되어 있습니다.

\ * MergeTree 테이블의 경우 돌연변이는 데이터 부분 전체를 다시 작성하여 실행됩니다. 원자 수 없다 - 부품 준비되어 있 자마자 변이시킨 부품에 대한 대체, a`SELECT` 돌연변이 중에 실행을 시작한 쿼리는 이미 변이 부분의 데이터와 아직 변이 않은 부분의 데이터가 표시됩니다.

돌연변이는 완전히 그 작성 순서에 따라 순서가 그 순서로 각 부분에 적용된다. 돌연변이가 전송되기 전에 테이블에 삽입 된 데이터는 변이 된 이후에 삽입 된 데이터는 변이되지 않습니다. 돌연변이는 삽입을 결코 차단하지 않도록주의하십시오.

돌연변이 쿼리는 돌연변이 항목이 추가 된 직후에 반환됩니다 (복제 된 테이블이 ZooKeeper에 복제되지 않은 테이블이 파일 시스템). 돌연변이 체의 집행을 비동기 이용 시스템의 개요를 설정합니다. 당신이 사용할 수 돌연변이의 진행을 추적하기 위해 [`system.mutations` (../../ operations / system-tables.md # system_tables-mutations) 테이블. 성공적으로 전송 된 돌연변이는 ClickHouse 서버가 다시 시작될 때 실행을 계속합니다. 그것은 제출 된 후 돌연변이를 롤백하는 방법은 아니지만, 돌연변이가 어떤 이유로 갇혀 있다면 그것은 [`KILL MUTATION` (misc.md # kill-mutation) 쿼리.

완료 한 돌연변이의 항목은 즉시 삭제되지 않습니다 (저장 된 항목의 수는`finished_mutations_to_keep` 스토리지 엔진 오래된 돌연변이 항목은 삭제됩니다.

## ALTER USER {# alter-user-statement}

ClickHouse 사용자 계정 변경.

### 구문 {# alter-user-syntax}

```sql
ALTER USER [IF EXISTS] name [ON CLUSTER cluster_name]
    [RENAME TO new_name]
    [IDENTIFIED [WITH {PLAINTEXT_PASSWORD | SHA256_PASSWORD | DOUBLE_SHA1_PASSWORD} BY { 'password'| 'hash'}]
    [ADD | DROP] HOST {LOCAL | NAME 'name'| REGEXP 'name_regexp'| IP 'address'| LIKE 'pattern'} [...] | ANY | NONE]
    [DEFAULT ROLE role [...] | ALL | ALL EXCEPT role [...]
    [SETTINGS variable [= value] [MIN [=] min_value] [MAX [=] max_value] [READONLY | WRITABLE] | PROFILE 'profile_name'] [...]
```

### 설명 {# alter-user-dscr}

사용하려면`ALTER USER` 당신은, ALTER USER] (grant.md # grant-access-management) 특권

### 예 {# alter-user-examples}

부여 된 역할을 기본값으로 설정한다 :

```sql
ALTER USER user DEFAULT ROLE role1, role2
```

역할이 이전에 사용자에게 부여되지 않은 경우 ClickHouse는 예외를 throw합니다.

부여 된 모든 역할을 default로 설정합니다 :

```sql
ALTER USER user DEFAULT ROLE ALL
```

미래 사용자에게 역할이 부여되면 자동으로 기본 설정됩니다.

세트의 부여의 역할을 기본 제외`role1`와`role2` :

```sql
ALTER USER user DEFAULT ROLE ALL EXCEPT role1, role2
```

## ALTER ROLE {# alter-role-statement}

역할을 변경합니다.

### 구문 {# alter-role-syntax}

```sql
ALTER ROLE [IF EXISTS] name [ON CLUSTER cluster_name]
    [RENAME TO new_name]
    [SETTINGS variable [= value] [MIN [=] min_value] [MAX [=] max_value] [READONLY | WRITABLE] | PROFILE 'profile_name'] [...]
```

## ALTER ROW POLICY {# alter-row-policy-statement}

행 정책을 변경합니다.

### 구문 {# alter-row-policy-syntax}

```sql
ALTER [ROW] POLICY [IF EXISTS] name [ON CLUSTER cluster_name] ON [database] table
    [RENAME TO new_name]
    [AS {PERMISSIVE | RESTRICTIVE}]
    [FOR SELECT]
    [USING {condition | NONE}] [...]
    [TO {role [...] | ALL | ALL EXCEPT role [...]}]
```

## ALTER QUOTA {# alter-quota-statement}

할당량을 변경합니다.

### 구문 {# alter-quota-syntax}

```sql
ALTER QUOTA [IF EXISTS] name [ON CLUSTER cluster_name]
    [RENAME TO new_name]
    [KEYED BY { 'none'| 'user name'| 'ip address'| 'client key'| 'client key or user name'| 'client key or ip address'}]
    [FOR [RANDOMIZED] INTERVAL number {SECOND | MINUTE | HOUR | DAY | WEEK | MONTH | QUARTER | YEAR}
        {MAX {{QUERIES | ERRORS | RESULT ROWS | RESULT BYTES | READ ROWS | READ BYTES | EXECUTION TIME} = number} [...] |
        NO LIMITS | TRACKING ONLY} [...]
    [TO {role [...] | ALL | ALL EXCEPT role [...]}]
```

## ALTER SETTINGS PROFILE {# alter-settings-profile-statement}

할당량을 변경합니다.

### 구문 {# alter-settings-profile-syntax}

```sql
ALTER SETTINGS PROFILE [IF EXISTS] name [ON CLUSTER cluster_name]
    [RENAME TO new_name]
    [SETTINGS variable [= value] [MIN [=] min_value] [MAX [=] max_value] [READONLY | WRITABLE] | INHERIT 'profile_name'] [...]
```

원본 기사 (https://clickhouse.tech/docs/en/query_language/alter/) <! - hide ->
