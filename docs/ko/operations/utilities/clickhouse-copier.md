--- 
machine_translated : true 
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd 
toc_priority : 59 
toc_title : "\ u30AF \ u30EA \ u30C3 \ u30AF \ u30CF \ u30A6 \ u30B9- \ u8907 \ u5199 \ u6A5F" 
--- 

# 클릭 하우스 - 복사기 {# clickhouse- copier} 

복사 데이터에서 테이블을 하나 클러스터 테이블의 다른 동 클러스터 

여러 실행할 수 있습니다`clickhouse-copier` 인스턴스가 다른 서버를 할 일입니다. ZooKeeper는 프로세스의 동기화에 사용됩니다. 

시작 후`clickhouse-copier` : 

- ZooKeeper에 연결하고 수신합니다 : 

    - 작업 사본. 
    - 복사 작업의 상태. 

-이 작업을 수행합니다. 

    각 실행중인 프로세스는 "closest"더 - 셔 소스 클러스터의 데이터 대상 클러스터 resharding 경우 데이터가 필요합니다. 

`clickhouse-copier` ZooKeeper의 변경을 추적하고 그 자리에서 그들을 적용합니다. 

네트워크 호랑이 피`clickhouse-copier` 소스 데이터가 배치되어있는 동일한 서버.

## 클릭 하우스 복사기 실행 {# running-clickhouse-copier} 

이 유틸 : 

```bash 
$ clickhouse-copier copier --daemon --config zookeeper.xml --task-path / task / path --base- dir / path / to / dir 
``` 

파라미터 : 

-`daemon` - Starts`clickhouse-copier` 데몬 모드. 
-`config` - The path to the`zookeeper.xml` ZooKeeper 연결을위한 파라미터를 가지는 파일. 
-`task-path` - The path to the ZooKeeper node. This node is used for syncing`clickhouse-copier` 프로세스 및 작업 저장. 작업은`$ task-path / description`. 
-`task-file` - Optional path to file with task configuration for initial upload to ZooKeeper . 
-`task-upload-force` - Force upload`task-file` 노드가 이미 존재도.
-`base-dir` - The path to logs and auxiliary files. When it starts`clickhouse-copier` 만들기`clickhouse-copier_YYYYMMHHSS_ <PID>`서브 디렉토리`$ base - dir`이 매개 변수를 생략하면 디렉토리 다음 위치에 만들어집니다`clickhouse-copier` 발매되었다. 

## 사육사의 형식. xml {# format-of-zookeeper-xml} 

```xml 
<yandex> 
    <logger> 
        <level> trace </ level> 
        <size> 100M </ size> 
        <count> 3 </ count> 
    </ logger> 

    < zookeeper> 
        <node index = "1"> 
            <host> 127.0.0.1 </ host> 
            <port> 2181 </ port> 
        </ node> 
    </ zookeeper> 
</ yandex>


    <! - Configuration of clusters as in an ordinary server config -> 
    <remote_servers> 
        <source_cluster> 
            <shard> 
                <internal_replication> false </ internal_replication> 
                    <replica> 
                        <host> 127.0.0.1 </ host> 
                        <port> 9000 </ port> 
                    </ replica> 
            </ shard> 
            ... 
        </ source_cluster> 

        <destination_cluster> 
        ... 
        </ destination_cluster> 
    </ remote_servers> 
 
    <!- How many simultaneously active workers are possible. If you run more workers superfluous workers will sleep ->
    <max_workers> 2 </ max_workers>

    <! - Setting used to fetch (pull) data from source cluster tables ->
    <settings_pull> 
        <readonly> 1 </ readonly> 
    </ settings_pull> 

    <! - Setting used to insert (push) data to destination cluster tables -> 
    <settings_push> 
        <readonly> 0 </ readonly> 
    </ settings_push> 

    < ! - Common setting for fetch (pull) and insert (push) operations. Also, copier process context uses it. 
         They are overlaid by <settings_pull /> and <settings_push /> respectively. -> 
    <settings> 
        <connect_timeout> 3 </ connect_timeout> 
        <! - Sync insert is set forcibly, leave it here just in case -> 
        <insert_distributed_sync> 1 </ insert_distributed_sync> 
    </ settings> 

    <! - Copying tasks description. 
         You could specify several table task in the same task description (in the same ZooKeeper node), they will be performed 
         sequentially. 
    -> 
    <tables> 
        <! - A table task, copies one table -> 
        <table_hits> 
            <! - Source cluster name (from <remote_servers /> section) and tables in it that should be copied -> 
            <cluster_pull> source_cluster </ cluster_pull> 
            <database_pull> test </ database_pull> 
            < table_pull> hits </ table_pull> 

            <! - Destination cluster name and tables in which the data should be inserted ->
            <cluster_push> destination_cluster </ cluster_push> 
            <database_push> test </ database_push> 
            <table_push> hits2 </ table_push> 

            <! - Engine of destination tables. 
                 If destination tables have not be created, workers create them using columns definition from source tables and engine 
                 definition from here. 

                 NOTE : If the first worker starts insert data and detects that destination partition is not empty then the partition will 
                 be dropped and refilled, take it into account if you already have some data in destination tables . you could directly
                 specify partitions that should be copied in <enabled_partitions />, they should be in quoted format like partition column of 
                 system.parts table. 
            -> 
            <engine> 
            ENGINE = ReplicatedMergeTree ( '/ clickhouse / tables / {cluster} / {shard} / hits2 ''{replica} ') 
            PARTITION BY toMonday (date) 
            ORDER BY (CounterID, EventDate) 
            </ engine> 

            <! - Sharding key used to insert data to destination cluster -> 
            <sharding_key> jumpConsistentHash (intHash64 ( UserID), 2) </ sharding_key> 

            <! - Optional expression that filter data while pull them from source servers ->
            <where_condition> CounterID! = 0 </ where_condition> 

            <! - This section specifies partitions that should be copied, other partition will be ignored. 
                 Partition names should have the same format as 
                 partition column of system.parts table (ie a quoted text ) 
                 Since partition key of source and destination cluster could be different ,  
                 these partition names specify destination partitions.

                 NOTE : In spite of this section is optional (if it is not specified, all partitions will be copied), 
                 it is strictly recommended to specify them explicitly. 
                 If you already have some ready partitions on destination cluster they 
                 will be removed at the start of the copying since they will be interpeted 
                 as unfinished data from the previous copying !!! 
            -> 
            <enabled_partitions> 
                <partition> '2018-02-26'</ partition> 
                <partition> '2018-03-05'</ partition> 
                ... 
            </ enabled_partitions> 
        </ table_hits > 

        <! - Next table to copy. It is not copied until previous table is copying ->
        </ table_visits>
        ... 
        </ table_visits> 
        ... 
    </ tables> 
</ yandex> 
```
 
`clickhouse-copier`의 변경을 추적 하다합니다`/ task / path / description` 그리고 그 자리에서 그들을 적용합니다. 예를 들어, 다음의 값을 변경하면`max_workers` 작업을 실행하는 프로세스의 숫자도 변경됩니다.

원본 기사 (https://clickhouse.tech/docs/en/operations/utils/clickhouse-copier/) <! - hide ->
