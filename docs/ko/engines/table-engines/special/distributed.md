---
machine_translated : true
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd
toc_priority : 33
toc_title : "\ u5206 \ u6563"
---

# 분산 {#distributed}

** 분산 엔진을 갖춘 테이블은 데이터 자체를 저장하지 않습니다 ** 그러나 여러 서버에 분산 쿼리 처리를 허용합니다.
읽기는 자동으로 병렬 처리됩니다. 읽는 동안 원격 서버의 테이블 인덱스가 존재하는 경우에 사용됩니다.

분산 엔지니어링 :

- 서버의 설정 파일의 클러스터 이름

- 원격 데이터베이스의 이름

- 원격 테이블의 이름

- (옵션) sharding 키

- (필요한 경우) 정책 이름은 비동기 전송 임시 파일을 저장하는 데 사용됩니다

    도 참조. :

    -`insert_distributed_sync` 설정
    - [메루게쯔리] (../ mergetree-family / mergetree.md # table_engine-mergetree-multiple-volumes) 예를 들어

예 :

```sql
Distributed (logs, default, hits [, sharding_key [, policy_name]])
```

모든 서버에서 데이터를 읽습니다. 'logs'기본에서 클러스터. 히트 테이블에 위치마다 서버 클러스터
데이터는 읽기뿐만 아니라 원격 서버에서 부분적으로 처리됩니다 (가능한 한).
예를 들어, GROUP BY를 사용한 쿼리의 경우 원격 서버에서 데이터가 집계 된 집계 함수의 중간 상태가 요청하는 서버로 전송됩니다. 그런 다음 데이터는 또한 집계됩니다.

데이터베이스 이름 대신에 문자열을 반환 상수 표현식을 사용할 수 있습니다. 예를 들어, currentDatabase ()입니다.

logs - The cluster name in the server 's config file.

클러스터가 세트 ようなこ :

```xml
<remote_servers>
    <logs>
        <shard>
            <! - Optional. Shard weight when writing data : Default : 1. ->
            <weight> 1 </ weight>
            <! - Optional. Whether to write data to just one of the replicas. Default : false (write data to all replicas) ->
            <internal_replication> false </ internal_replication>
            <replica>
                <host> example01-01-1 </ host>
                <port> 9000 </ port>
            </ replica>
            <replica>
                <host> example01-01-2 </ host>
                <port> 9000 </ port>
            </ replica>
        </ shard>
        <shard>
            <weight> 2 </ weight>
            <internal_replication> false </ internal_replication>
            <replica>
                <host> example01-02-1 </ host>
                <port> 9000 </ port>
            </ replica>
            <replica>
                <host> example01-02-2 </ host>
                <secure> 1 </ secure>
                <port> 9440 </ port>
            </ replica>
        </ shard>
    </ logs>
</ remote_servers>
```

여기서 클러스터는 이름으로 정의됩니다 'logs'이 두 조각으로 구성되어 각각 두 개의 복제가 포함되어 있습니다.
샤드는 데이터의 다른 부분을 포함하는 서버를 말합니다 (모든 데이터를 읽고, 모든 샤드에 액세스해야합니다).
복제 서버를 복제합니다 (모든 데이터를 읽고, 복제 중 하나의 데이터에 액세스 할 수 있습니다).

클러스터의 이름 등을 포함하지 마십시오 없습니다.

매개 변수`host``port` 및 필요에 따라`user``password``secure``compression` 서버마다 지정됩니다 :
-`host` - The address of the remote server. You can use either the domain or the IPv4 or IPv6 address. If you specify the domain, the server makes a DNS request when it starts, and the result is stored as long as the server is running. If the DNS request fails, the server does not start. If you change the DNS record, restart the server.
-`port` - The TCP port for messenger activity ( 'tcp_port'설정은 보통 9000로 설정되어 있습니다). Http_port과 혼동하지 마십시오.
-`user` - Name of the user for connecting to a remote server. Default value : default. This user must have access to connect to the specified server. Access is configured in the users.xml file. For more information, see the section [권한] (../../../ operations / access-rights.md).
-`password` - The password for connecting to a remote server (not masked) Default value : empty string.
-`secure` - 연결에 ssl을 사용합니다. `port` = 9440. 서버가 수신하는`<tcp_port_secure> 9440 </ tcp_port_secure>`올바른 인증서를 가지고 있습니다.
-`compression` - 데이터 압축을 사용합니다. 기본값은 true.

When specifying replicas, one of the available replicas will be selected for each of the shards when reading. You can configure the algorithm for load balancing (the preference for which replica to access) - see the [load_balancing (../../ ../operations/settings/settings.md#settings-load_balancing) 설정.
서버와의 연결이 설정되지 않은 경우, 짧은 시간에 연결하려고합니다. 연결에 실패하면 모든 복제본에 대해 다음 복제가 선택됩니다. 모든 복제본에서 연결 시도가 실패하면 그 시도는 같은 방법으로 여러 번 반복됩니다.
원격 서버는 연결을 받아 들일 수 없지만 작동하지 않거나 불충분하게 작동 할 수 있습니다.

어느 샤드 만 지정할 수 있습니다 (이 경우 쿼리 처리 분산이 아니라 원격으로 불리는해야합니다). 각 샤드는 하나에서 임의의 수의 복제본을 지정할 수 있습니다. 샤드마다 다른 수의 복제본을 지정할 수 있습니다.

설정은 필요한 수의 클러스터를 지정할 수 있습니다.

클러스터를 표시하려면 'system.clusters'테이블.

분산 형 엔진 기능함으로써 사회와 클러스터처럼 현지 서버입니다. 서버 설정 파일에 설정을 기록해야합니다 (모든 클러스터 서버에서 더 우수합니다).

The Distributed engine requires writing clusters to the config file. Clusters from the config file are updated on the fly, without restarting the server. If you need to send a query to an unknown set of shards and replicas each time, you do not need to create a Distributed table - use the 'remote'대신 테이블 함수. 섹션을 참조 테이블 함수 (../../../ sql-reference / table-functions / index.md).

클러스터에 데이터를 기록하는 방법은 두 가지가 있습니다 :

먼저 어떤 서버에 어떤 데이터를 쓸 것인지를 정의하고 각 샤드에서 직접 쓸 수 있습니다. 즉, 분산 테이블 테이블에 삽입을 실행합니다 "looks at"이는 해당 영역의 요구 사항에 대한 자명하지 않을 수있는 샤드 구성표를 사용할 수 있기 때문에 가장 유연한 솔루션입니다. 데이터는 완전히 독립적으로 다른 샤드에 쓸 수 있기 때문에 이것은 최적의 솔루션입니다.

다음 분산 테이블에 INSERT를 수행 할 수 있습니다. 이 경우, 테이블은 삽입 된 데이터를 서버 자체에 분산합니다. 분산 테이블에 쓰려면 샤드 키 세트 (마지막 매개 변수)가 필요합니다. 또한 샤드가 하나 밖에없는 경우,이 경우는 아무것도 의미하지 않기 때문에 샤드 키를 지정하지 않고 쓰기 작업이 작동합니다.

각 샤드는 설정 파일에 정의 된 가중치를 설정할 수 있습니다. 기본적으로 무게는 무게와 같습니다. 데이터는 샤드의 크기에 비례하는 양 샤드에 분산됩니다. 예를 들어, 샤드이 두 가지가 처음 샤드가 9 가중치를 가지고 다음 샤드가 10의 가중치를 가지는 경우, 첫 번째 샤드는 행의 9/19 부분에 전송 된 다음 셔

각 샤드는 'internal_replication'설정 파일에 정의 된 매개 변수입니다.

이 매개 변수를 'true'쓰기 작업은 첫 번째 정상적인 복제를 선택하고 해당 데이터를 씁니다. 분산 테이블의 경우이 대안을 사용합니다 "looks at"복제 된 테이블. 즉, 데이터가 기록되는 테이블 자체를 복제하는 경우.

로 설정되어있는 경우 'false'데이터는 모든 복제본에 기록됩니다. 본질적으로 이것은 분산 테이블이 데이터 자체를 복제하는 것을 의미합니다. 이것은 복제 된 테이블을 사용하는 것보다 나쁜 것입니다.

데이터 행이 전송되는 샤드를 선택하기 위해 샤드식이 분석되고 나머지 샤드의 총 중량으로 나눈됩니다. 이 줄은 나머지 절반의 간격에 해당하는 샤드로 전송됩니다. 'prev_weight'에 'prev_weights + weight'여기 'prev_weights'는 최소 수를 가진 칩의 총 무게입니다. 'weight'이 칩의 무게입니다. 예를 들어, 샤드이 두 가지가 처음 샤드의 무게가 9에서 다음 샤드 가중치가 10 인 경우, 행은 범위 \ [0,9)의 나머지 샤드의 첫 번째 샤드에 전송되고

샤드 식에는 정수를 반환하는 정수 및 테이블 열의 임의의 식을 사용할 수 있습니다. 예를 들어, 다음 식을 사용할 수 있습니다 'rand ()'데이터의 무작위 분포의 경우 또는 'UserID'사용자 ID를 나눌 수에서 나머지 의한 분배를 위해 (단일 사용자의 데이터는 단일 샤드에 존재하고 사용자에 의한 실행과 결합을 단순화). 하나의 열이 충분히 고르게 분산되지 않은 경우, 해시 함수 intHash64 (UserID)에 싸여 있습니다.

간단한 알림에서 한정시 sharding 거를 항상 적합합니다. 이것은 데이터의 중형 및 대형 볼륨 (서버의 수십) 대신 데이터의 매우 큰 볼륨 (서버의 수백 이상)을 위해 작동합니다. 후자의 경우, 분산 테이블의 항목을 사용하는 것이 아니라, 주제 영역에서 필요한 샤드 구성표를 사용합니다.

SELECT queries are sent to all the shards and work regardless of how data is distributed across the shards (they can be distributed completely randomly). When you add a new shard, you do not have to transfer the old data to it. You can write new data with a heavier weight - the data will be distributed slightly unevenly, but queries will work correctly and efficiently.

다음의 경우 샤드 방식에 대해 걱정할 필요가 있습니다 :

- 특정 키에 의한 데이터의 결합 (IN 또는 JOIN)을 필요로하는 쿼리가 사용됩니다. 이 키에 의해 데이터가 샤드되는 경우 GLOBAL IN 또는 GLOBAL JOIN 대신 local IN 또는 JOIN을 사용할 수 있습니다.
- 다수의 서버가 다수의 작은 쿼리 (개별 클라이언트 - 웹 사이트, 광고주 또는 파트너 쿼리)에서 사용됩니다 (수백 이상). 작은 쿼리가 전체 클러스터에 영향을주지 않도록하려면 단일 샤드에 하나의 클라이언트의 데이터를 검색 할 말이있다 또한 우리는 Yandex 때문에 그랬던 것처럼. Metrica는 bi 수준의 샤드를 설정할 수 있습니다 : 전체 클러스터를 다음과 같이 나눕니다 "layers"여기에서 레이어는 여러 샤드로 구성됩니다. 단일 클라이언트의 데이터는 단일 레이어에 있습니다 만, 필요에 따라 샤드를 레이어에 추가 할 수있는 데이터는 무작위로 배포됩니다. 분산 테이블은 계층마다 만들어 글로벌 쿼리에 대한 단일 공유 분산 테이블이 만들어집니다.

데이터는 비동기 적으로 기록됩니다. 테이블에 삽입하면 데이터 블록은 로컬 파일 시스템에 기록됩니다. 데이터는 가능한 빨리 백그라운드에서 원격 서버로 전송됩니다. 데이터를 전송하기위한 기간은 [distributed_directory_monitor_sleep_time_ms (../../../ operations / settings / settings.md # distributed_directory_monitor_sleep_time_ms) 및 distributed_directory_monitor_max_sleep_time_ms (../../../ operations / settings / settings.md # distributed_directory_monitor_max_sleep_time_ms) 설정. 그`Distributed` 엔진은 삽입 된 데이터가있는 파일을 따로 보내지 만, distributed_directory_monitor_batch_inserts (../../../ operations / settings / settings.md # distributed_directory_monitor_batch_inserts) 설정. 이 설정의 개선에 클러스터의 성능을 더욱 활용 지역의 서버 및 네트워크 자원입니다. 확인 둡시다 여부의 데이터가 전송되는 체크리스트 파일 (데이터 또는 사이에 -를 끼운)는 테이블 디렉토리 :`/ var / lib / clickhouse / data / database / table /`.

분산 테이블에 삽입 후 서버가 존재하지 않는 경우 또는 장치 오류 후 등에 거친 재부팅 된 경우 삽입 된 데이터가 손실 될 테이블 디렉토리에서 손상된 데이터 부분이 발견되면 해당 부분은 'broken'사용되지 않습니다.

Max_parallel_replicas 옵션을 사용하면 단일 샤드의 모든 복제본에서 쿼리 처리가 병렬 처리됩니다. 자세한 내용은 [max_parallel_replicas (../../../ operations / settings / settings.md # settings-max_parallel_replicas).

## 가상 열 {# virtual-columns}

-`_shard_num` - Contains the`shard_num` (에서`system.clusters`) 유형 : UInt32 (../../../ sql-reference / data-types / int-uint.md).

!!! note "주"
    이후 [`remote` (../../../ sql-reference / table-functions / remote.md) /`cluster` 테이블 함수는 내부적으로 동일한 분산 엔진의 임시 인스턴스를 만듭니다` _shard_num` 너무 거기에 사용할 수 있습니다.

** 참고하십시오. **

- 가상 열 (index.md # table_engines-virtual_columns)

원본 기사 (https://clickhouse.tech/docs/en/operations/table_engines/distributed/) <! - hide ->
