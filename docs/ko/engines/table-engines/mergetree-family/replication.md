---
machine_translated : true
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd
toc_priority : 31
toc_title : "\ u30C7 \ u30FC \ u30BF \ u8907 \ u88FD"
---

# 데이터 복제 {# table_engines-replication}

복제가 지원되는 유일한위한 테이블 MergeTree 가족 :

- 복제 병합 트리
- 복제 된 서밍 병합 트리
- 복제 리플 레이스 병합 트리
- 복제 된 집합 병합 트리
- 복제 코라뿌신구 병합 트리
- ReplicatedVersionedCollapsingMergetree
- 복제 낙서 병합 트리

복제 작품의 수준을 별도의 테이블이 아니라 전체 서버입니다. 서버에서 매장도 복제, 비 복제 테이블에서 동시에 실시합니다.

복제 샤드에 의존하지 않습니다. 각 샤드는 자신의 독립적 인 복제가 있습니다.

압축 된 데이터`INSERT`와`ALTER` 쿼리를 복제 (자세한 내용은 문서에 [ALTER] (../../../ sql-reference / statements / alter.md # query_language_queries_alter)).

`CREATE``DROP``ATTACH``DETACH`와`RENAME` 쿼리는 단일 서버에서 실행되고 복제되지 않습니다 :

- 그`CREATE TABLE` 쿼리는 쿼리가 실행되는 서버에 새 복제 가능한 테이블을 만듭니다. 이 테이블이 이미 다른 서버를 추가 새로운 복제.
- 그`DROP TABLE` 쿼리는 쿼리가 실행되는 서버의 복제본을 제거합니다.
- 그`RENAME` query는 복제 중 하나의 테이블의 이름을 변경합니다. 즉, 복제 테이블에서 다른 이름의 다른 복제본.

ClickHouse 사용 [아파치 사육사 (https://zookeeper.apache.org) 복제 메타 정보를 저장하기 위하여. ZooKeeper 버전 3.4.5 이상을 사용합니다.

복제를 사용하려면 [사육사 (../../../ operations / server-configuration-parameters / settings.md # server-settings_zookeeper) 서버 구성 섹션.

!!! attention "주의"
    보안 설정을 무시하지 마십시오 클릭 하우스는`digest` [ACL 체계 (https://zookeeper.apache.org/doc/current/zookeeperProgrammers.html#sc_ZooKeeperAccessControl) ZooKeeper 보안 서브 시스템.

ZooKeeper 클러스터의 주소를 설정하는 방법 :

```xml
<zookeeper>
    <node index = "1">
        <host> example1 </ host>
        <port> 2181 </ port>
    </ node>
    <node index = "2">
        <host> example2 </ host>
        <port> 2181 </ port>
    </ node>
    <node index = "3">
        <host> example3 </ host>
        <port> 2181 </ port>
    </ node>
</ zookeeper>
```

기존 ZooKeeper 클러스터를 지정하면 시스템은 클러스터의 디렉토리를 자신의 데이터로 사용합니다 (복제 가능한 테이블을 만들 때

경우 사육사 설정 구성 파일을 만들어 올려 않아서 재현 테이블 및 기존 복제 테이블 읽기 전용입니다.

사육사로 사용되지 않습니다`SELECT` 복제 성능`SELECT` 또한 쿼리는 비 복제 테이블의 경우와 마찬가지로 빠르게 실행됩니다. 때 쿼리에 배포 재현 테이블 ClickHouse 행동 제어 설정 max_replica_delay_for_distributed_queries (../../../ operations / settings / settings.md # settings-max_replica_delay_for_distributed_queries) 및 대체 _to_stale_replicas_for_distributed_queries] (../ ../../operations/settings/settings.md#settings-fallback_to_stale_replicas_for_distributed_queries).

각각을위한`INSERT` 쿼리 계약시에 응모를 추가 사육사를 무 거래 등 (보다 정확하게는, 이것은 삽입 된 데이터의 각 블록에 대한 것입니다.`max_insert_block_size = 1048576` 행) 이것은` INSERT` 비 복제 테이블과 비교합니다. 그러나 권장 사항에 따라 데이터를 여러 배치에 삽입 할 경우`INSERT` 초당 그것은 문제를 만들 수 없습니다. 하나의 ZooKeeper 클러스터를 조정하는 데 사용되는 ClickHouse 클러스터 전체의 총 수백입니다`INSERTs` 초당 데이터 삽입 처리량 (초 당 행)는 복제되지 않은 데이터의 경우만큼 높아 있습니다.

아주 큰 클러스터의 경우 다른 샤드 다른 ZooKeeper 클러스터를 사용할 수 있습니다. 그러나 이것은 Yandex에 필요한 증명되어 있지 않습니다. 메트 개 클러스터 (약 300 대).

복제는 비동기 다중 마스터입니다. `INSERT` 쿼리 (그리고`ALTER`) 사용 가능한 모든 서버에 보낼 수 있습니다. 데이터는 쿼리가 실행되는 서버에 삽입되어 다른 서버로 복사됩니다. 그래서 비동기이기 때문에 최근에는 삽입 된 데이터가 표시되고 다른 복제본과 대기 ​​시간을 제로로하기로 복제의 일부를 사용할 수없는 경우 데이터가 사용 가능하게되었을 때 기록됩니다 . 복제가 사용 가능한 경우, 대기 시간은 압축 된 데이터 블록을 네트워크를 통해 전송하는 데 걸리는 시간입니다.

기본적으로 삽입 쿼리는 하나의 복제에서 데이터의 쓰기 확인을 기다립니다. 데이터를 얻을 수없는 경우에는 성공한 기술에 대해 복제 서버의이 복제가 소멸하고 저장된 데이터는 손실됩니다. 여러 복제에서 데이터 쓰기 확인의 취득을 활성화하려면`insert_quorum` 옵션

각 데이터 블록은 원자 적으로 기록됩니다. 삽입 쿼리는 다음 블록으로 분할됩니다`max_insert_block_size = 1048576` 행. 즉,`INSERT` 쿼리는 1048576 줄 미만이 원자 적으로 만들어집니다.

데이터 블록은 중복 제거됩니다. 같은 데이터 블록 (같은 순서로 같은 행을 포함 동일한 크기의 데이터 블록)의 여러 쓰기의 경우 블록은 한 번만 기록됩니다. 그 이유는 클라이언트 응용 프로그램이 데이터를 DB에 기록 된 여부를 모르는 경우 네트워크 오류가 발생한 경우입니다. `INSERT` 쿼리하는 것만으로 간단하게 반복합니다. 어떤 복제 삽입이 동일한 데이터 전송되었는지는 관계 없습니다. `INSERTs` 멱등 법칙이다. 중복 제거 압축 파라미터의 제어 [merge_tree (../../../ operations / server-configuration-parameters / settings.md # server_configuration_parameters-merge_tree) 서버 설정.

복제시 원본 데이터의 삽입에 전송됩니다. 새로운 데이터 변환 (병합)은 모든 복제에서 같은 방법으로 조정되고 실행됩니다. 즉, 복제가 다른 데이터 센터에있는 경우 복제가 제대로 작동합니다. 복제의 주요 목적은 다른 데이터 센터에 데이터를 복제하는 것입니다. )

동일한 데이터의 임의의 수의 복제본을 가질 수 있습니다. Yandex.Metrica는 프로덕션 환경에서 이중 복사를 사용합니다. 각 서버는 RAID-5 또는 RAID-6를 사용하고 경우에 따라서는 RAID-10을 사용합니다. 이것은 비교적 안정적 유용한 솔루션입니다.

시스템은 복제 데이터 동기화를 모니터링하고 장애 발생 후 복구 할 수 있습니다. 장애 조치는 자동 (데이터의 사소한 차이의 경우) 또는 반자동 (데이터 다름이 너무 많으면 구성 오류를 나타낼 수 있습니다)입니다.

## 복제 테이블 만들기 {# creating-replicated-tables}

그`Replicated` 테이블 엔진 이름에 접두어가 추가됩니다. 예를 들면 :`ReplicatedMergeTree`.

** 복제 \ * MergeTree 파라미터 **

-`zoo_path` - The path to the table in ZooKeeper.
-`replica_name` - The replica name in ZooKeeper.

예 :

```sql
CREATE TABLE table_name
(
    EventDate DateTime,
    CounterID UInt32,
    UserID UInt32
) ENGINE = ReplicatedMergeTree ( '/ clickhouse / tables / {layer} - {shard} / table_name', '{replica}')
PARTITION BY toYYYYMM (EventDate)
ORDER BY (CounterID, EventDate, intHash32 (UserID))
SAMPLE BY intHash32 (UserID)
```

<details markdown = "1">

<summary> 비추천 구문의 예 </ summary>

```sql
CREATE TABLE table_name
(
    EventDate DateTime,
    CounterID UInt32,
    UserID UInt32
) ENGINE = ReplicatedMergeTree ( '/ clickhouse / tables / {layer} - {shard} / table_name', '{replica}', EventDate, intHash32 (UserID) (CounterID, EventDate, intHash32 (UserID) EventTime), 8192)
```

</ details>

그 예로는 이러한 매개 변수를 포함 할 수 換巻 와서하실 바디입니다. 대체 값은 'macros'설정 파일의 섹션. 예 :

```xml
<macros>
    <layer> 05 </ layer>
    <shard> 02 </ shard>
    <replica> example05-02-1.yandex.ru </ replica>
</ macros>
```

표 사육 관련해서는 작동하지 않지만 미래에 재현합니다. 테이블 다른 자료는 다르다.
이 경우 경로는 다음 부분으로 구성됩니다 :

`/ clickhouse / tables /`일반적인 접두사입니다. 의 사용을 권장 버리는 것입니다.

`{layer} - {shard}`샤드 식별자입니다. 이 예에서는 Yandex 때문에 두 부분으로 구성되어 있습니다. Metrica 클러스터의 사용 인터넷에서 파일 전송 sharding 대부분의 작업에서는 {shard} 대체만을 남길 수 있습니다.

`table_name` ZooKeeper의 테이블 노드 이름입니다. 테이블 이름과 일치하는 것이 좋습니다. 이것은 테이블 이름과는 대조적으로, 이름 변경 쿼리 후 변경되지 않기 때문에 명시 적으로 정의되어 있습니다.
* HINT * : 데이터베이스 이름을 추가 할 수 있습니다`table_name`처럼. 예를 들어`db_name.table_name`

복제의 이름을 식별 복제와 같다. 여기에는 예와 같이 서버 이름을 사용할 수 있습니다. 이름은 각 샤드에서 고유해야합니다.

대체를 사용하는 대신 매개 변수를 명시 적으로 정의 할 수 있습니다. 이것은 테스트와 작은 클러스터 구성에 유용합니다. 그러나 분산 DDL 쿼리는 사용할 수 없습니다 (`ON CLUSTER`)이 있다면.

대규모 클러스터에서 작업하는 경우 오류의 가능성을 줄이기 위해 대체를 사용하는 것이 좋습니다.

실행`CREATE TABLE` 각 복제본의 쿼리. 이 쿼리를 복제 테이블을 추가하고 새로운 복제는 기존합니다.

테이블에 다른 복제 데이터가 이미 포함되어있는 후 새 복제본을 추가하면 쿼리 실행 후 데이터가 다른 복제본에서 새 복제 즉, 새로운 복제가 다른 복제와 동기화합니다.

복제를 삭제하려면`DROP TABLE`. However, only one replica is deleted - the one that resides on the server where you run the query.

## 장애 복구 후 {# recovery-after-failures}

경우 사육사가 불가능한 경우 서버는 복제 테이블 스위치 읽기 전용 모드입니다. 시스템은 정기적으로 ZooKeeper에 연결을 시도합니다.

사육사가 동안 사용할 수없는 경우`INSERT` 또는 ZooKeeper과의 대화시 오류가 발생하면 예외가 throw됩니다.

ZooKeeper에 연결하면 로컬 파일 시스템의 데이터 세트가 예상되는 데이터 세트와 일치하는지 여부를 확인합니다 (ZooKeeper는이 정보를 저장하는 경우는 경미한 불일치 해소 동기화 데이터 복제 .

시스템이 손상된 데이터 부분 (파일 크기가 잘못) 또는 인식 할 수없는 부분 (파일 시스템에 기록되어 있지만, ZooKeeper에 기록 된`detached` 하위 디렉토리 (삭제되지 않습니다) 다른 부분이 복사본에서 복제.

ClickHouse은 대량의 데이터를 자동으로 삭제하는 등의 파괴적인 작업을 수행하지 않습니다.

서버가 시작할 때 나타납니다 (또는 새로 설립 세션과 사육사)에서만 검사의 양과 크기의 모든 파일입니다. 파일 크기는 일치하지만 도중에 바이트가 변경된 경우이를 즉시 감지되지 않고 데이터를 읽으려고 할 때 만 검색됩니다. `SELECT` 쿼리. 쿼리에서 예외가 발생하면서 비 매칭 체크섬은 크기에 압축 된 블록입니다. 이 경우, 데이터 부분은 검증 큐에 추가되고 필요에 따라 복제 복사됩니다.

경우에는 현지의 데이터 세트가 달라 너무 취해 안전기구를 시작합니다. 서버는이 로그에 입력하고 실행을 거부합니다. 그 이유는 샤드의 복제본이 다른 샤드의 복제본과 같이 잘못 구성된 경우 등이 케이스가 구성 오류를 나타낼 수 있기 때문입니다. 그러나 임계 값이기구의 설정 상당히 낮고, 이런 상황이 일어날에서 실패를 회복했습니다. 이 경우 데이터는 반 자동으로 복원됩니다. "pushing a button".

복구를 시작하려면 노드를 만듭니다`/ path_to_table / replica_name / flags / force_restore_data`에서 사육사와 콘텐츠 또는 명령을 실행하여 복원 모든 복제 테이블 :

```bash
sudo -u clickhouse touch / var / lib / clickhouse / flags / force_restore_data
```

그런 다음 서버를 다시 시작합니다. 시작할 때 서버는 이러한 플래그를 제거하고 복구를 시작합니다.

## 완전한 데이터 손실 후 회복 {# recovery-after-complete-data-loss}

모든 데이터와 메타 데이터 사라지면 서버에는 다음과 같이하십시오 부흥 :

1. ClickHouse를 서버에 설치합니다. 샤드 식별자와 복제를 사용하는 경우는 설정 파일에서 바꾸기를 제대로 정의합니다.
2. 서버에서 수동으로 복제해야되지 않은 복제 테이블이있는 경우 복제에서 데이터를 복사합니다 (디렉토리). `/ var / lib / clickhouse / data / db_name / table_name /`).
3. 테이블 정의의 복사`/ var / lib / clickhouse / metadata /`복제본에서. 샤드 식별자 또는 복제본 식별자가 테이블 정의에서 명시 적으로 정의되어있는 경우,이 복제에 대응하도록 수정합니다. (또는 서버를 시작하고 모든`ATTACH TABLE`에 있었을 쿼리 .sql 파일`/ var / lib / clickhouse / metadata /`)
4. 복구를 시작하려면 ZooKeeper 노드를 만듭니다`/ path_to_table / replica_name / flags / force_restore_data` 명령을 실행하여 복제 된 테이블을 복원하고 있습니다 :`sudo -u clickhouse touch / var / lib / clickhouse / flags / force_restore_data`

그런 다음 서버를 시작합니다 (이미 실행중인 경우 다시 시작합니다). 데이터 다운로드의 복제.

대체 복구 옵션은 제거 정보가 손실 된 복제본에서 사육사 (`/ path_to_table / replica_name`)에 설명 된대로 복제본을 다시 만듭니다 "복제 테이블 만들기 (# creating-replicated-tables ) ".

복구시 네트워크 대역폭에 제한은 없습니다. 한 번에 다수의 복제본을 복원하려면이 점에주의하십시오.

## MergeTree에서 ReplicatedMergeTree로 변환 {# converting-from-mergetree-to-replicatedmergetree}

우리는이 용어를 사용하는`MergeTree` 모든 테이블 엔진을 보려면`MergeTree family`의 경우와 동일합니다. `ReplicatedMergeTree`.

당신이 가지고 있던 경우`MergeTree` 수동으로 복제 된 테이블은 복제 된 테이블로 변환 할 수 있습니다. 이미 대량의 데이터를 수집하는 경우에는 이렇게 필요가 있을지도 모릅니다. `MergeTree` 복제를 활성화합니다.

다양한 복제 데이터가 다른 경우 먼저 동기화하거나이 데이터를 제외한 모든 복제본에서 제거합니다.

기존 MergeTree 테이블의 이름을 변경 한 다음`ReplicatedMergeTree` 오래된 이름의 테이블.
데이터 이전 테이블에서`detached` 하위 디렉토리에있는 내용을 새로운 테이블 데이터 (`/ var / lib / clickhouse / data / db_name / table_name /`).
그 후, 실행`ALTER TABLE ATTACH PARTITION` 이러한 데이터 파트를 작업 세트에 추가 복제본 중 하나.

## ReplicatedMergeTree에서 MergeTree로 변환 {# converting-from-replicatedmergetree-to-mergetree}

다른 이름 MergeTree 테이블을 만듭니다. 디렉토리에서 모든 데이터를 이동합니다. `ReplicatedMergeTree` 테이블 데이터를 새 테이블의 데이터 디렉토리입니다. 그 후, 삭제`ReplicatedMergeTree` 서버를 표하고 다시 시작합니다.

당신이 제거하려면`ReplicatedMergeTree` 서버를 시작하지 않는 테이블 :

- 대응하는 것을 제거하는`.sql` 파일의 메타 데이터 디렉토리 (`/ var / lib / clickhouse / metadata /`).
- ZooKeeper에서 해당 경로를 삭제합니다 (`/ path_to_table / replica_name`).

그런 다음 서버를 시작하고`MergeTree` 데이터를 해당 디렉토리로 이동하고 서버를 다시 시작합니다.

## Zookeeper 클러스터의 메타 데이터가 손실되거나 손상된 경우 복구 {# recovery-when-metadata-in-the-zookeeper-cluster-is-lost-or-damaged}

ZooKeeper의 데이터가 손실되거나 손상된 경우에는 위와 같이 재생되지 않는 테이블로 이동하여 데이터를 저장할 수 있습니다.

원본 기사 (https://clickhouse.tech/docs/en/operations/table_engines/replication/) <! - hide ->
