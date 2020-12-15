---
machine_translated : true
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd
toc_priority : 32
toc_title : "\ u30AB \ u30B9 \ u30BF \ u30E0 \ u5206 \ u5272 \ u30AD \ u30FC"
---

# 사용자 정의 분할 키 {# custom-partitioning-key}

분할은 [메루게쯔리 (mergetree.md) 가족 테이블 (포함 복제 (replication.md) 테이블). [실체화 뷰 (../ special / materializedview.md # materializedview)에 근거 MergeTree 테이블 지원을 나눕니다.

파티션은 지정된 조건에 의한 테이블의 레코드의 논리적 인 조합입니다. 파티션은 월별, 일별 이벤트 유형별 등 어떤 조건으로 설정할 수 있습니다. 각 파티션은 따로 저장되는 간편한 데이터입니다. 액세스시의 데이터 ClickHouse의 최소 부분 집합의 파티션은 가능합니다.

파티션은`PARTITION BY expr` 절 때 테이블 만들기 (mergetree.md # table_engine-mergetree-creating a-table)이 파티션 키에 수 표현에서 테이블 열 수 있습니다. 예를 들어, 지정 프 달의 표현을 사용`toYYYYMM (date_column)`:

```sql
CREATE TABLE visits
(
    VisitDate Date,
    Hour UInt8,
    ClientID UUID
)
ENGINE = MergeTree ()
PARTITION BY toYYYYMM (VisitDate)
ORDER BY Hour;
```

파티션 키는 식의 튜플 할 수 있습니다. [기본 키] (mergetree.md # primary-keys-and-indexes-in-queries)). 예를 들면 :

```sql
ENGINE = ReplicatedCollapsingMergeTree ( '/ clickhouse / tables / name', 'replica1'Sign)
PARTITION BY (toMonday (StartDate), EventType)
ORDER BY (CounterID, StartDate, intHash32 (UserID));
```

이 예제에서는 현재 주에 발생한 이벤트의 종류에 따라 파티션을 설정합니다.

삽입 할 때 새로운 데이터 테이블에 데이터를 저장할 수있는 다른 부품으로 (개) -field-list 차례로 정렬이 유효한 유형을 사용합니다. 삽입 후 10-15 분에서 동일한 파티션 부분이 부분 전체에 병합됩니다.

!!! info "정보"
    병합은 분할 식의 값이 같은 데이터 부분에 대해서만 작동합니다. 즉 ** 따위를 지나치게 세분화 분할 ** (천 약 이상의 파티션). 그렇지 않으면`SELECT` 파일 시스템 및 오픈 파일 기술자에게 부당하게 많은 파일이 있기 때문에 쿼리의 수행이 부족합니다.

사용하는 시스템 부품 (../../../ operations / system-tables.md # system_tables-parts) 테이블 파트와 파티션을 표시하는 표. 예를 들어, 이제 우리가 가지고 있다고 가정하자`visits` 테이블을 분할한다. 는 실행하자`SELECT` 쿼리`system.parts` 테이블 :

```sql
SELECT
    partition,
    name,
    active
FROM system.parts
WHERE table = 'visits'
```

```text
┌─partition─┬─name───────────┬─active─┐
│ 201901 │ 201901_1_3_1 │ 0 │
│ 201901 │ 201901_1_9_2 │ 1 │
│ 201901 │ 201901_8_8_0 │ 0 │
│ 201901 │ 201901_9_9_0 │ 0 │
│ 201902 │ 201902_4_6_1 │ 1 │
│ 201902 │ 201902_10_10_0 │ 1 │
│ 201902 │ 201902_11_11_0 │ 1 │
└───────────┴────────────────┴────────┘
```

그`partition` 열은 파티션의 이름이 포함됩니다. 파티션 예 :`201901`와`201902`이 열의 값을 사용하여 파티션 이름을 지정할 수 있습니다 [ALTER ... PARTITION (# alter_manipulations-with-partitions) 쿼리.

그`name` 컬럼의 이름을 포함하여 파티션의 데이터 부품입니다. 이 열을 사용하여 부품의 이름을 지정할 수 있습니다. [ALTER ATTACH PART (# alter_attach-partition) 쿼리.

첫 번째 부분의 이름을 분해 해 봅시다 :`201901_1_3_1` :

-`201901` 파티션 이름입니다.
-`1` 데이터 블록의 최소 수입니다.
-`3` 데이터 블록의 최대 수입니다.
-`1` 청크 수준 (형성되는 병합 트리의 깊이)입니다.

!!! info "정보"
    구형 테이블의 부분에는 이름이 있습니다 :`20190117_20190123_2_2_0` (최소 날짜 - 최대 일 - 최소 블록 번호 - 최대 블록 번호 - 수준).

그`active` 열은 부품의 상태를 나타냅니다. `1` 활성;`0` 비활성 상태입니다. 비활성 부분은 예를 들어, 더 큰 부분을 병합 한 후에 남는 소스 부분입니다. 손상된 데이터 부분도 비활성으로 표시됩니다.

이 예에서 알 수 있듯이 동일한 파티션에 여러 분리 된 부분이 있습니다 (예를 들어,`201901_1_3_1`와`201901_1_9_2`). 즉,이 부분은 아직 병합되지 않습니다. ClickHouse 데이터의 삽입 된 부분을 정기적으로 병합하고 삽입 약 15 분 후에 병합합니다. 또한 예약되지 않은 병합을 실행하려면 OPTIMIZE (../../../ sql-reference / statements / misc.md # misc_operations-optimize) 쿼리. 예 :

```sql
OPTIMIZE TABLE visits PARTITION 201902;
```

```text
┌─partition─┬─name───────────┬─active─┐
│ 201901 │ 201901_1_3_1 │ 0 │
│ 201901 │ 201901_1_9_2 │ 1 │
│ 201901 │ 201901_8_8_0 │ 0 │
│ 201901 │ 201901_9_9_0 │ 0 │
│ 201902 │ 201902_4_6_1 │ 0 │
│ 201902 │ 201902_4_11_2 │ 1 │
│ 201902 │ 201902_10_10_0 │ 0 │
│ 201902 │ 201902_11_11_0 │ 0 │
└───────────┴────────────────┴────────┘
```

비활성 부분은 병합 후 약 10 분에서 삭제됩니다.

부품 및 파티션 세트를 표시하는 또 다른 방법은 테이블의 디렉토리로 이동합니다 :`/ var / lib / clickhouse / data / <database> / <table> /`예 :

```bash
/ var / lib / clickhouse / data / default / visits $ ls -l
total 40
drwxr-xr-x 2 clickhouse clickhouse 4096 Feb 1 16:48 201901_1_3_1
drwxr-xr-x 2 clickhouse clickhouse 4096 Feb 5 16:17 201901_1_9_2
drwxr-xr-x 2 clickhouse clickhouse 4096 Feb 5 15:52 201901_8_8_0
drwxr-xr-x 2 clickhouse clickhouse 4096 Feb 5 15:52 201901_9_9_0
drwxr-xr-x 2 clickhouse clickhouse 4096 Feb 5 16:17 201902_10_10_0
drwxr-xr-x 2 clickhouse clickhouse 4096 Feb 5 16:17 201902_11_11_0
drwxr-xr-x 2 clickhouse clickhouse 4096 Feb 5 16:19 201902_4_11_2
drwxr-xr-x 2 clickhouse clickhouse 4096 Feb 5 12:09 201902_4_6_1
drwxr-xr-x 2 clickhouse clickhouse 4096 Feb 1 16:48 detached
```

폴더 '201901_1_1_0', '201901_1_7_1'그리고 부품의 디렉토리입니다. 각부에 관한 대응하는 분할 데이터가 포함까지 일정 달의 테이블이 예에서는 분할에 의한 다.

그`detached` 디렉토리에 포함 된 부품의 수 있었는지를 사용하여 테이블 [DETACH (../../../ sql-reference / statements / alter.md # alter_detach-partition) 쿼리. 손상된 부분도 삭제되는 것이 아니라,이 디렉토리로 이동됩니다. 서버는 서버의 부품을 사용하지 않습니다. `detached` directory. You can add, delete or modify the data in this directory at any time - the server will not know about this until you run the [ATTACH (../../../ sql-reference / statements / alter.md # alter_attach-partition) 쿼리.

운영 서버는 파일 시스템의 부품 세트 또는 데이터를 수동으로 변경할 수 없습니다. 비 복제 테이블이를 수행 할 수 있습니다. 서버가 중지되고 있지 권장합니다. 위한 복제 테이블은 부품 세트를 변경할 수 없습니다.

ClickHouse에서는 파티션 삭제, 테이블 사이의 복사 또는 백업 생성 등의 작업을 수행 할 수 있습니다. 섹션의 모든 작업 목록을 참조하십시오 파티션 및 부품의 작업 (../../../ sql-reference / statements / alter.md # alter_manipulations-with-partitions).

원본 기사 (https://clickhouse.tech/docs/en/operations/table_engines/custom_partitioning_key/) <! - hide ->
