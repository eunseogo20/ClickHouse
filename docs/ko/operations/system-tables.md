--- 
machine_translated : true 
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd 
toc_priority : 52 
toc_title : "\ u30B7 \ u30B9 \ u30C6 \ u30E0 \ u8868" 
--- 

# 시스템 테이블 {# system-tables} 

시스템 테이블은 시스템의 일부 기능을 구현 하거나 시스템의 동작 정보에 대한 액세스를 제공하는 데 사용됩니다. 
시스템 테이블을 삭제할 수 없습니다 (단, DETACH을 수행 할 수 있습니다). 
시스템 테이블이없는 파일 데이터의 하드 디스크 또는 파일 및 메타 데이터를 지칭합니다. 서버가 시작될 때 모든 시스템 테이블을 만듭니다. 
시스템 테이블은 읽기 전용입니다. 
그들은에 위치하고 있습니다 'system'데이터베이스. 

## 시스템 asynchronous_metrics {# system_tables-asynchronous_metrics} 

백그라운드에서 주기적으로 계산되는 지표가 포함되어 있습니다. 예를 들어, 사용중인 RAM의 크기. 

열 : 
 
-`metric` (문자열] (../ sql-reference / data-types / string.md)) - Metric name.
-`value` (Float64] (../ sql-reference / data-types / float.md)) - Metric value. 

** 예 ** 

```sql
SELECT * FROM system.asynchronous_metrics LIMIT 10 
``` 

```text 
┌─metric──────────────────────────────── ──┬──────value─┐ 
│ jemalloc.background_thread.run_interval │ 0 │ 
│ jemalloc.background_thread.num_runs │ 0 │ 
│ jemalloc.background_thread.num_threads │ 0 │ 
│ jemalloc.retained │ 422551552 │ 
│ jemalloc.mapped │ 1682989056 │ 
│ jemalloc.resident │ 1656446976 │  
│ jemalloc .metadata_thp │ 0 │
│ jemalloc.metadata │ 10226856 │ 
│ UncompressedCacheCells │ 0 │ 
│ MarkCacheFiles │ 0 │
└─────────────────────────────────────────┴─────── ─────┘ 
``` 

** 참조. ** 

- 모니터 (monitoring.md) - Base concepts of ClickHouse monitoring. 
- 시스템 메트릭 (# system_tables-metrics) - Contains instantly calculated metrics. 
- [시스템 이벤트] (# system_tables-events) - Contains a number of events that have occurred. 
- 시스템 metric_log (# system_tables-metric_log) - Contains a history of metrics values from tables`system.metrics` и`system.events`. 

## 시스템 클러스터 {# system-clusters} 

에 대한 정보가 포함 된 클러스터 구성 파일을 서버입니다. 

열 : 

-`cluster` (String) - The cluster name.  
-`shard_num` (UInt32) - The shard number in the cluster, starting from 1.
-`shard_weight` (UInt32) - The relative weight of the shard when writing data .
-`replica_num` (UInt32) - The replica number in the shard, starting from 1. 
-`host_name` (String) - The host name, as specified in the config. 
-`host_address` (String) - The host IP address obtained from DNS. 
-`port` (UInt16) - the port to use for connecting to the server . 
-`user` (String) - the name of the user for connecting to the server . 
-`errors_count` (UInt32) -이 호스트 이 복제에 도달하지 못한 횟수입니다. 
-`estimated_recovery_time` (UInt32) - 복제 실패 횟수가 0이되고, 정상으로 돌아올 때까지의 시간. 
** 참고하십시오. **

주의하십시오`errors_count` 클러스터에 대한 쿼리마다 한 번 updated되지만`estimated_recovery_time` 주문형 다시 계산됩니다. 그래서 제로가 아닌 경우가 있을지도 모릅니다`errors_count`과 제로`estimated_recovery_time` 다음 쿼리는 제로가됩니다`errors_count` 오류가없는 것처럼 replica를 사용하여보십시오. 


- 분산 테이블 엔진 (../ engines / table-engines / special / distributed.md) 
- [distributed_replica_error_cap 설정 (settings / settings.md # settings-distributed_replica_error_cap) 
- [distributed_replica_error_half_life 설정 (settings / settings.md # settings-distributed_replica_error_half_life) 

## 시스템 열 {# system-columns} 

모든 테이블의 열에 관한 정보를 포함합니다. 

이 테이블을 사용하면 다음과 같은 정보를 얻을 수 있습니다 [DESCRIBE TABLE] (../ sql-reference / statements / misc.md # misc-describe-table) 쿼리가 한 번에 여러 테이블을 위해 . 

그`system.columns` 테이블을 포함하는 다음 컬럼의 컬럼 유형은 브래킷) :

-`database` (String) - Database name. 
-`table` (String) - Table name.  
-`name` (String) - Column name : 
-`type` (String) - Column type.
-`default_kind` (String) - Expression type (`DEFAULT``MATERIALIZED``ALIAS` 기본값의 경우) 정의되어 있지 않으면 빈 문자열. 
-`default_expression` (String) - Expression for the default value, or an empty string if it is not defined . 
-`data_compressed_bytes` (UInt64) - The size of compressed data, in bytes. 
-`data_uncompressed_bytes` (UInt64) - The size of decompressed data, in bytes. 
-`marks_bytes` (UInt64) - the size of marks, in bytes. 
-`comment` (String) - Comment on the column, or an empty string if it is not defined . 
-`is_in_partition_key `(UInt8) - Flag that indicates whether the column is in the partition expression . 
-`is_in_sorting_key `(UInt8) - Flag that indicates whether the column is in the sorting key expression . 
-`is_in_primary_key` (UInt8) - Flag that indicates whether the column is in the primary key expression .
-`is_in_sampling_key` (UInt8) - Flag that indicates whether the column is in the sampling key expression . 

## 시스템 참여자 {# system-contributors} 

를 포함한 정보 제공자가 보유하고 있습니다. 무작위 순서로 모든 constributors. 순서는 쿼리 실행시 랜덤입니다. 

열 : 

-`name` (String) - Contributor (author) name from git log. 

** 예 ** 

```sql 
SELECT * FROM system.contributors LIMIT 10 
``` 

```text 
┌─name──── ─────────┐ 
│ Olga Khvostikova │ 
│ Max Vetrov │ 
│ LiuYangkuan │ 
│ svladykin │ 
│ zamulla │  
│ Šimon Podlipský │
│ BayoNet │ 
│ Ilya Khomutov │ 
│ Amy Krishnevsky │ 
│ Loud_Scream │ 
└──────── ──────────┘
``` 

테이블에서 자신을 알고, 쿼리를 사용합니다 : 

```sql 
SELECT * FROM system.contributors WHERE name = 'Olga Khvostikova' 
``` 

```text 
┌─name──── ─────────┐ 
│ Olga Khvostikova │ 
└──────────────────┘ 
``` 

## 시스템 데이터 {# system-databases} 

이 테이블을 포함하는 단일 문자열 열라고 'name'- the name of a database. 
각 데이터베이스 서버에 대해 알고 있고 해당 항목 표에 나타낸다. 
이 시스템 테이블은`SHOW DATABASES` 쿼리. 

## 시스템 detached_parts {# system_tables-detached_parts}

에 대한 정보가 포함 된 외부 [메루게쯔리] (../ engines / table-engines / mergetree-family / mergetree.md) 테이블이`reason` column 부품이 분리 된 이유를 지정합니다. 사용자가 제거 된 부품의 경우, 그 이유는 비어 있습니다. 이러한 부품은, ALTER TABLE ATTACH PARTITION \ | PART] (../ sql-reference / statements / alter.md # alter_attach-partition) 명령 기타 열 설명은 시스템 부품 (# system_tables- parts) 부품 이름이 잘못된 경우 일부 컬럼의 값은 다음과 같습니다`NULL` 이런 부분은 다음으로 삭제할 수 있습니다 [ALTER TABLE DROP DETACHED PART] (../ sql -reference / statements / alter.md # alter_drop-detached) 

## 시스템 사전 {# system_tables-dictionaries} 

에 대한 정보가 포함 된 외부 사전] (../ sql-reference / dictionaries / external-dictionaries / external -dicts.md) 

열 : 
 
-`database` (문자열] (../ sql-reference / data-types / string.md)) - Name of the database containing the dictionary created by DDL query . Empty string for other dictionaries.
-`name` (문자열] (../ sql-reference / data-types / string.md)) - 사전 이름 (../ sql-reference / dictionaries / external -dictionaries / external-dicts-dict.md). 
-`status` (Enum8] (../ sql-reference / data-types / enum.md)) - Dictionary status. Possible values : 
    -`NOT_LOADED` - Dictionary was not loaded because it was not used . 
    -`LOADED` - dictionary loaded successfully. 
    -`FAILED` - Unable to load the dictionary as a result of an error . 
    -`LOADING` - dictionary is loading now. 
    -`LOADED_AND_RELOADING` - dictionary is loaded successfully, and is being reloaded right now (frequent reasons : [sYSTEM rELOAD dICTIONARY (../ sql-reference / statements / system.md # query_language-system-reload-dictionary) 쿼리 시간 제한, 사전 설정이 변경되었습니다).
    -`FAILED_AND_RELOADING` - Could not load the dictionary as a result of an error and is loading now .  
-`origin` (문자열] (../ sql-reference / data-types / string.md)) - Path to the configuration file that describes the dictionary .
-`type` (문자열] (../ sql-reference / data-types / string.md)) - Type of a dictionary allocation [메모리에 사전 저장] (../ sql-reference / dictionaries / external-dictionaries / external-dicts-dict-layout.md) 
-`key` - [키 유형] (../ sql-reference / dictionaries / external-dictionaries / external-dicts-dict-structure.md # ext_dict_structure-key) : 숫자 키 (UInt64] (../ sql-reference / data-types / int-uint.md # uint -ranges)) or Сomposite key ([문자열] (../ sql-reference / data-types / string.md)) - form "(type 1, type 2, ..., type n)".
-`attribute.names` ([배열] (../ sql-reference / data-types / array.md) ([문자열] (../ sql-reference / data-types / string.md))) - Array of [속성 이름] (../ sql-reference / dictionaries / external-dictionaries / external-dicts-dict-structure.md # ext_dict_structure-attributes) 사전에 의해 제공됩니다.  
-`hit_rate` (Float64] (../ sql -reference / data-types / float.md)) - For cache dictionaries, the percentage of uses for which the value was in the cache .
-`attribute.types` ([배열] (../ sql-reference / data-types / array.md) ([문자열] (../ sql-reference / data-types / string.md))) - Corresponding array of [속성 유형] (../ sql-reference / dictionaries / external-dictionaries / external-dicts-dict-structure.md # ext_dict_structure-attributes) 그것은 사전에 의해 제공됩니다.
-`bytes_allocated` (UInt64] (../ sql-reference / data-types / int-uint.md # uint-ranges)) - Amount of RAM allocated for the dictionary. 
-`query_count` (UInt64] (. ./sql-reference/data-types/int-uint.md#uint-ranges)) - Number of queries since the dictionary was loaded or since the last successful reboot . 
-`element_count` (UInt64] (../ sql -reference / data-types / int-uint.md # uint-ranges)) - Number of items stored in the dictionary. 
-`load_factor` (Float64] (../ sql-reference / data-types / float.md )) - percentage filled in the dictionary (for a hashed dictionary, the percentage filled in the hash table) 
-`source` (문자열] (../ sql-reference / data-types / string.md)) - Text describing the 데이터 원본 (../ sql-reference / dictionaries / external-dictionaries / external-dicts-dict-sources.md) 사전을 위해. 
-`lifetime_min` (UInt64] (. ./sql-reference/data-types/int-uint.md#uint-ranges)) - Minimum [생애] (../ sql-reference / dictionaries / external-dictionaries / external-dicts-dict-lifetime.md) 그 후, ClickHouse는 사전을 다시로드하려고합니다 (만약`invalidate_query` 그것이 변경된 경우에만 설정되어 있습니다). 초 단위로 설정합니다. 
-`lifetime_max` (UInt64] (../ sql-reference / data-types / int-uint.md # uint-ranges)) - Maximum [생애] (../ sql-reference / dictionaries / external-dictionaries / external-dicts-dict-lifetime.md) 이후 ClickHouse는 사전을 다시로드하려고합니다 (만약`invalidate_query` 그것이 변경된 경우에만 설정되어 있습니다). 초 단위로 설정합니다. 
-`loading_start_time` (DateTime] (../ sql-reference / data-types / datetime.md)) - Start time for loading the dictionary.
-`last_successful_update_time` (DateTime] (../ sql-reference / data-types / datetime.md)) - End time for loading or updating the dictionary. Helps to monitor some troubles with external sources and investigate causes . 
    `value_expression` String DEFAULT 'xxx'EXPRESSION 'toString (127 * 172)'
-`loading_duration` (Float32] (../ sql-reference / data-types / float.md)) - Duration of a dictionary loading. 
-`last_exception` (문자열] (../ sql-reference / data -types / string.md)) - Text of the error that occurs when creating or reloading the dictionary if the dictionary could not be created . 

** 예 ** 

사전을 설정합니다. 

```sql 
CREATE DICTIONARY dictdb.dict 
( 
    `key` Int64 DEFAULT -1, 
    `value_default` String DEFAULT 'world' 
) 
PRIMARY KEY key 
SOURCE (CLICKHOUSE (HOST 'localhost'PORT 9000 USER 'default'TABLE 'dicttbl'DB 'dictdb')) 
LIFETIME (MIN 0 MAX 1) 
LAYOUT (FLAT ()) 
``` 

사전로드되어 있는지 확인합니다. 

```sql 
SELECT * FROM system.dictionaries 
``` 

```text 
┌─database─┬─name─┬─status─┬─origin──────┬─type─┬─key────┬─attribute.names────── ────────────────┬─attribute.types─────┬─bytes_allocated─┬─query_count─┬─hit_rate─┬─element_count─┬─────── ────load_factor─┬─source─────────────────────┬─lifetime_min─┬─lifetime_max─┬──loading_start_time─┌──last_successful_update_time─┬─ ─────loading_duration─┬─last_exception─┐
│ dictdb │ dict │ LOADED │ dictdb.dict │ Flat │ UInt64 │ [ 'value_default', 'value_expression'] │ [ 'String', 'String'] │ 74032 │ 0 │ 1 │ 1 │ 0.0004887585532746823 │ ClickHouse : dictdb.dicttbl │ 0 │ 1 │ 2020-03-04 04:17:34 │ 2020-03-04 04:30:34 │ 0.002 │ │ 
└──────────┴──────┴─ ───────┴─────────────┴──────┴────────┴──────────── ──────────────────────────┴─────────────────────┴─ ────────────────┴─────────────┴──────────┴──────── ───────┴───────────────────────┴────────────────── ──────────┴──────────────┴──────────────┴───────── ────────────┴──────────────────────────────┘────── ─────────────────┴────────────────┘ 
``` 

## 시스템 이벤트 {# system_tables-events}
 
시스템에서 발생한 이벤트의 수에 대한 정보가 포함되어 있습니다. 예를 들어, 테이블에서 얼마나 많은`SELECT` ClickHouse 서버가 시작한 쿼리가 처리되었습니다. 

열 : 

-`event` (문자열] (../ sql-reference / data-types / string.md)) - Event name. 
-`value` (UInt64] (../ sql-reference / data- types / int-uint.md)) - Number of events occurred. 
-`description` (문자열] (../ sql-reference / data-types / string.md)) - Event description.

** 예 ** 

```sql 
SELECT * FROM system.events LIMIT 5 
``` 

```text  
┌─event──────── ─────────────────────────┬─value─┬─description────────────────── ────────────────────────────────────────────────── ────────────────────────────────────────────────── ────────────────────────────────────────────────── ────────────────────────────────────────────────── ──────────────────────┐
│ Query │ 12 │ Number of queries to be interpreted and potentially executed . Does not include queries that failed to parse or were rejected due to AST size limits, quota limits or limits on the number of simultaneously running queries . May include internal queries initiated by ClickHouse itself. Does not count subqueries. │ 
│ SelectQuery │ 8 │ Same as Query, but only for SELECT queries. │
│ FileOpen │ 73 │ Number of files opened. │ 
│ ReadBufferFromFileDescriptorRead │ 155 │ Number of reads (read / pread) from a file descriptor. Does not include sockets. │
│ ReadBufferFromFileDescriptorReadBytes │ 9931 │ Number of bytes read from file descriptors. If the file is compressed, this will show the compressed data size. │ 
└───────────────────── ──────────────────┴───────┴─────────────────────── ────────────────────────────────────────────────── ────────────────────────────────────────────────── ────────────────────────────────────────────────── ────────────────────────────────────────────────── ─────────────────────────────┘ 
``` 

** 참조. ** 

- [시스템 asynchronous_metrics (# system_tables-asynchronous_metrics) - Contains periodically calculated metrics.
- 시스템 메트릭 (# system_tables-metrics) - Contains instantly calculated metrics. 
- 시스템 metric_log (# system_tables-metric_log) - Contains a history of metrics values from tables`system.metrics` и`system.events`. 
- 모니터 (monitoring.md) - Base concepts of ClickHouse monitoring. 

## 시스템 함수 {# system-functions} 

표준 및 집계 함수에 대한 정보가 포함되어 있습니다. 

열 : 
 
-`name` (`String`) - The name of the function.
-`is_aggregate` (`UInt8`) - Whether the function is aggregate. 

## 시스템 graphite_retentions {# system-graphite-retentions} 

매개 [graphite_rollup (server-configuration-parameters / settings.md # server_configuration_parameters-graphite) 테이블 에서 사용되는 [\ * GraphiteMergeTree] (../ engines / table-engines / mergetree-family / graphitemergetree.md) 엔진이다 

열 :
 
-`config_name` (문자열) -`graphite_rollup` 매개 변수 이름. 
-`regexp` (String) - 메트릭 이름의 패턴. 
-`function` (String) - 집계 함수의 이름입니다. 
-`age` (UInt64) - 데이터의 최소 연령을 초 단위로 나타냅니다. 
-`precision` (UInt64) - 데이터의 나이를 초 단위로 정확하게 정의하는 방법. 
-`priority` (UInt16) - 패턴의 우선 순위. 
-`is_default` (UInt8) - 패턴이 기본 여부. 
-`Tables.database` (Array (String)) - 데이터베이스 테이블 이름의 배열. `config_name` 매개 변수.
-`Tables.table` (Array (String)) - 테이블 이름의 배열`config_name` 매개 변수. 

## 시스템 병합 {# system-merges} 

MergeTree 가족의 테이블에서 현재 처리중인 병합 및 파트의 변이에 대한 정보가 포함되어 있습니다. 

열 : 

-`database` (String) - The name of the database the table is in . 
-`total_size_marks` (UInt64) - The total number of marks in the merged parts . 
-`table` (String) - Table name. 
-`elapsed` (Float64) - The time elapsed (in seconds) since the merge started .
-`progress` (Float64) - The percentage of completed work from 0 to 1 
-`num_parts` (UInt64) - The number of pieces to be merged. 
-`result_part_name` (String) - The name of the part that will be formed as the result of merging. 
-`is_mutation` (UInt8) -1이 과정이 부분 돌연변이 인 경우. 
-`total_size_bytes_compressed` (UInt64) - The total size of the compressed data in the merged chunks . 
-`bytes_read_uncompressed` (UInt64) - Number of bytes read, uncompressed. 
-`rows_read` (UInt64) - Number of rows read. 
-` bytes_written_uncompressed` (UInt64) - Number of bytes written, uncompressed. 
-`rows_written` (UInt64) - Number of rows written. 

## 시스템 메트릭 {# system_tables-metrics}

즉시 계산할 수 메트릭 또는 현재의 값이 포함됩니다. 예를 들어, 동시에 처리 된 쿼리 수와 현재 복제 지연 등입니다. 이 테이블은 항상 최신입니다. 

열 : 

-`metric` (문자열] (../ sql-reference / data-types / string.md)) - Metric name. 
-`value` (Int64] (../ sql-reference / data- types / int-uint.md)) - Metric value. 
-`description` (문자열] (../ sql-reference / data-types / string.md)) - Metric description. 

지원되는 지표 목록 은 다음과 같습니다 [src / Common / CurrentMetrics. cpp] (https://github.com/ClickHouse/ClickHouse/blob/master/src/Common/CurrentMetrics.cpp) ClickHouse 소스 파일. 

** 예 ** 

```sql 
SELECT * FROM system.metrics LIMIT 10 
``` 

```text
┌─metric─────────────────────┬─value─┬─description─────────────────── ────────────────────────────────────────────────── ────────────────────────────────────────────────── ────────────────────────────────────────────────── ─────────────┐  
│ Query │ 1 │ Number of executing queries │
│ Merge │ 0 │ Number of executing background merges │ 
│ PartMutation │ 0 │ Number of mutations (ALTER DELETE / UPDATE) │
│ ReplicatedFetch │ 0 │ Number of data parts being fetched from replicas │  
│ ReplicatedSend │ 0 │ Number of data parts being sent to replicas │
│ ReplicatedChecks │ 0 │ Number of data parts checking for consistency │ 
│ BackgroundPoolTask │ 0 │ Number of active tasks in BackgroundProcessingPool (merges, mutations, fetches, or replication queue bookkeeping) │
│ BackgroundSchedulePoolTask │ 0 │ Number of active tasks in BackgroundSchedulePool. This pool is used for periodic ReplicatedMergeTree tasks, like cleaning old data parts, altering data parts, replica re-initialization, etc. │  
│ DiskSpaceReservedForMerge │ 0 │ Disk space reserved for currently running background merges . It is slightly more than the total size of currently merging parts . │
│ DistributedSend │ 0 │ Number of connections to remote servers sending data that was INSERTed into Distributed tables. Both synchronous and asynchronous mode. │ 
└────────────────────────────┴──── ───┴────────────────────────────────────────────── ────────────────────────────────────────────────── ────────────────────────────────────────────────── ────────────────────────────────────────────────┘ 
` `` 

** 참조. **
 
- [시스템 asynchronous_metrics (# system_tables-asynchronous_metrics) - Contains periodically calculated metrics. 
- [시스템 이벤트] (# system_tables-events) - Contains a number of events that occurred. 
- 시스템 metric_log (# system_tables-metric_log) - Contains a history of metrics values from tables`system.metrics` и`system.events`. 
- 모니터 (monitoring.md) - Base concepts of ClickHouse monitoring. 

## 시스템 metric_log {# system_tables-metric_log} 

을 포함 내역 통계 값에서 테이블`system.metrics`와`system.events` 정기적으로 디스크에 플래시. 
메트릭 기록 수집을 활성화하려면`system.metric_log` 만든`/ etc / clickhouse-server / config.d / metric_log.xml` 다음 내용으로 : 

```xml 
<yandex> 
    <metric_log> 
        <database> system </ database> 
        <table> metric_log </ table> 
        <flush_interval_milliseconds> 7500 </ flush_interval_milliseconds> 
        <collect_interval_milliseconds> 1000 </ collect_interval_milliseconds> 
    </ metric_log> 
</ yandex>


SELECT * FROM system.metric_log LIMIT 1 FORMAT Vertical; 
``` 

```text 
Row 1 : 
────── 
event_date : 2020-02-18  
event_time : 2020-02-18 07:15:33 
milliseconds : 554 
ProfileEvent_Query : 0 
ProfileEvent_SelectQuery : 0
ProfileEvent_InsertQuery : 0 
ProfileEvent_FileOpen : 0 
ProfileEvent_Seek : 0 
ProfileEvent_ReadBufferFromFileDescriptorRead : 1 
ProfileEvent_ReadBufferFromFileDescriptorReadFailed : 0 
ProfileEvent_ReadBufferFromFileDescriptorReadBytes : 0 
ProfileEvent_WriteBufferFromFileDescriptorWrite : 1 
ProfileEvent_WriteBufferFromFileDescriptorWriteFailed : 0
ProfileEvent_WriteBufferFromFileDescriptorWriteBytes : 56 
...  
CurrentMetric_Query : 0 
CurrentMetric_Merge : 0
CurrentMetric_PartMutation : 0 
CurrentMetric_ReplicatedFetch : 0 
CurrentMetric_ReplicatedSend : 0 
CurrentMetric_ReplicatedChecks : 0 
... 
``` 

** 참조. ** 

- [시스템 asynchronous_metrics (# system_tables-asynchronous_metrics) - Contains periodically calculated metrics. 
- [시스템 이벤트] (# system_tables-events) - Contains a number of events that occurred. 
- 시스템 메트릭 (# system_tables-metrics) - Contains instantly calculated metrics. 
- 모니터 (monitoring.md) - Base concepts of ClickHouse monitoring.
 
## 시스템 숫자 {# system-numbers} 

이 테이블을 한 UInt64 열 이름 'number'제로로부터 시작되는 거의 모든 자연수가 포함되어 있습니다.
이 테이블은 테스트를 위해, 또는 무력 검색을 할 필요가있는 경우에 사용할 수 있습니다. 
이 표에서 읽기는 병렬화되지 않습니다. 

## 시스템 numbers_mt {# system-numbers-mt} 

와 같은 'system.numbers'하지만 읽기는 병렬 처리됩니다. 번호는 임의의 순서로 반환 할 수 있습니다. 
테스트에 사용됩니다. 

## 시스템 원 {# system-one} 

이 테이블에는 하나의 행이 포함되어 있습니다. 'dummy'UInt8 값을 포함하는 열 0. 
이 테이블은 SELECT 쿼리에 FROM 절이 지정되지 않은 경우에 사용됩니다. 
이것은 다른 Dbms의 듀얼 테이블과 유사합니다. 

## 시스템 부품 {# system_tables-parts} 

에 대한 정보가 포함되어 부품의 메루게쯔리] (../ engines / table-engines / mergetree-family / mergetree.md) 테이블 

각 행은 하나의 데이터 부분을 설명 합니다. 

열 : 

-`partition` (String) - The partition name. To learn what a partition is, see the description of the [ALTER] (../ sql-reference / statements / alter.md # query_language_queries_alter) 쿼리. 

    형식 :

    -`YYYYMM` 월별 자동 분할의 경우. 
    -`any_string` 수동으로 분할하는 경우. 

-`name` (`String`) - Name of the data part. 

-`active` (`UInt8`) - Flag that indicates whether the data part is active . If a data part is active, it 's used in a table. Otherwise , it 's deleted. Inactive data parts remain after merging. 

-`marks` (`UInt64`) - the number of marks. To get the approximate number of rows in a data part , multiply`marks` 인덱스의 입도 (일반적으로 8192) 로 지정합니다 (이 팁은 적응적인 입도에서는 작동하지 않습니다). 

-`rows` (`UInt64`) - The number of rows. 

-`bytes_on_disk` (`UInt64`) - Total size of all the data part files in bytes .

-`data_compressed_bytes` (`UInt64`) - Total size of compressed data in the data part . All the auxiliary files (for example, files with marks) are not included. 

-`data_uncompressed_bytes` (`UInt64`) - Total size of uncompressed data in the data part. All the auxiliary files (for example, files with marks) are not included. 

-`marks_bytes` (`UInt64`) - the size of the file with marks. 

-`modification_time` (`DateTime`) - the time the directory with the data part was modified . This usually corresponds to the time of data part creation \ | 

-`remove_time` (`DateTime`) - the time when the data part became inactive.

-`refcount` (`UInt32`) - The number of places where the data part is used . A value greater than 2 indicates that the data part is used in queries or merges . 

-`min_date` (`Date`) - The minimum value of the date key in the data part . 

-`max_date` (`Date`) - The maximum value of the date key in the data part . 

-`min_time` (`DateTime`) - The minimum value of the date and time key in the data part. 

-`max_time` (`DateTime`) - the maximum value of the date and time key in the data part . 

-`partition_id` (`String`) - ID of the partition. 

-`min_block_number` ( `UInt64`) - the minimum number of data parts that make up the current part after merging .

-`max_block_number` (`UInt64`) - The maximum number of data parts that make up the current part after merging . 

-`level` (`UInt32`) - Depth of the merge tree. Zero means that the current part was created by insert rather than by merging other parts. 

-`data_version` (`UInt64`) - Number that is used to determine which mutations should be applied to the data part (mutations with a version higher than`data_version`) 

-`primary_key_bytes_in_memory` ( `UInt64`) - The amount of memory (in bytes) used by primary key values. 

-`primary_key_bytes_in_memory_allocated` (`UInt64`) - The amount of memory (in bytes) reserved for primary key values.

-`is_frozen` (`UInt8`) - Flag that shows that a partition data backup exists 1, the backup exists 0, the backup does not exist. For more details, see [FREEZE PARTITION] (../ sql- reference / statements / alter.md # alter_freeze-partition) 

-`database` (`String`) - Name of the database. 

-`table` (`String`) - Name of the table. 

-`engine` (`String` ) - Name of the table engine without parameters. 

-`path` (`String`) - Absolute path to the folder with data part files . 

-`disk` (`String`) - Name of a disk that stores the data part . 

-`hash_of_all_files` (`String`) - [sipHash128] (../ sql-reference / functions / hash-functions.md # hash_functions-siphash128) 압축 된 파일.

-`hash_of_uncompressed_files` (`String`) - [sipHash128] (../ sql-reference / functions / hash-functions.md # hash_functions-siphash128) 비 압축 파일 (표시된 파일, 인덱스 파일 등). 

-`uncompressed_hash_of_compressed_files `(`String`) - [sipHash128] (../ sql-reference / functions / hash-functions.md # hash_functions-siphash128) 그들이 압축되지 않은 것처럼 압축 된 파일의 데이터. 

-`bytes` (`UInt64`) - Alias for`bytes_on_disk`. 

-`marks_size` (`UInt64`) - Alias for`marks_bytes`. 

## 시스템 part_log {# system_tables-part-log} 

그`system.part_log` 테이블이 생성되는 것은, part_log (server-configuration-parameters / settings.md # server_configuration_parameters-part-log) 서버 설정을 지정합니다.

이 테이블에 대한 정보가 포함 된 이벤트가 발생한 데이터 파트 (../ engines / table-engines / mergetree-family / custom-partitioning-key.md)에서 메루게쯔리] (../ engines / table -engines / mergetree-family / mergetree.md) 데이터 추가 및 병합 등의 패밀리 테이블. 

그`system.part_log` 테이블을 포함하는 다음 컬럼 : 

-`event_type` (Enum) - Type of the event that occurred with the data part . Can have one of the following values : 
    -`NEW_PART` - Inserting of a new data part. 
    -`MERGE_PARTS` - Merging of data parts. 
    -`DOWNLOAD_PART` - Downloading a data part. 
    -`REMOVE_PART` - Removing or detaching a data part using [DETACH PARTITION] (../ sql-reference / statements / alter. md # alter_detach-partition) 
    -`MUTATE_PART` - Mutating of a data part.
    -`MOVE_PART` - Moving the data part from the one disk to another one . 
-`size_in_bytes` (UInt64) - Size of the data part in bytes.
-`event_date` (Date) - Event date. 
-`event_time` (DateTime) - Event time. 
-`duration_ms` (UInt64) - Duration. 
-`database` (String) - Name of the database the data part is in . 
-`table` (String) - Name of the table the data part is in . 
-`part_name` (String) - Name of the data part. 
-`partition_id` (String) - ID of the partition that the data part was inserted to. the column takes the 'all'분할이`tuple ()`. 
-`rows` (UInt64) - the number of rows in the data part. 
-`merged_from` (Array (String)) - An array of names of the parts which the current part was made up from (after the merge) 
-`bytes_uncompressed` (UInt64) - Size of uncompressed bytes. 
-`read_rows` (UInt64) - The number of rows was read during the merge . 
-`read_bytes` (UInt64) - the number of bytes was read during the merge . 
-`error` (UInt16) - the code number of the occurred error. 
-`exception` (String) - Text message of the occurred error. 

그 `system.part_log` 테이블은 처음에 데이터를 삽입 한 후에 작성됩니다. `MergeTree` 테이블. 

## 시스템 프로세스 {# system_tables-processes} 

이 시스템 테이블은`SHOW PROCESSLIST` 쿼리. 

열 :
 
-`user` (String) - The user who made the query. Keep in mind that for distributed processing, queries are sent to remote servers under the`default` 사용자. 분야의 사용자 이름에서 특정 쿼리는 쿼리는 쿼리도 시작하고 있습니다.
-`address` (String) - The IP address the request was made from. The same for distributed processing. To track where a distributed query was originally made from , look at`system.processes` 쿼리 요청 서버. 
-`elapsed` (Float64) - The time in seconds since request execution started.  
-`rows_read` (UInt64) - The number of rows read from the table. For distributed processing, on the requestor server, this is the total for all remote servers.
-`bytes_read` (UInt64) - The number of uncompressed bytes read from the table . For distributed processing, on the requestor server, this is the total for all remote servers. 
-`total_rows_approx` (UInt64) - the approximation of the total number of rows that should be read . For distributed processing, on the requestor server, this is the total for all remote servers. It can be updated during request processing, when new sources to process become known. 
-`memory_usage` (UInt64) - Amount of RAM the request uses . It might not include some types of dedicated memory . See the [max_memory_usage] (../ operations / settings / query-complexity.md # settings_max_memory_usage) 설정. 
-`query` (String) - The query text : For`INSERT` 삽입 할 데이터는 포함되지 않습니다. 
-`query_id` (String) - Query ID, if defined. 

## 시스템 text_log {# system_tables-text_log} 

을 포함 로그인 작품의 응모가있었습니다. 로그 수준이 테이블에 한정`text_log.level` 서버 설정. 

열 : 

-`event_date` (`Date`) - 항목의 날짜. 
-`event_time` (`DateTime`) - 항목의 시간.
-`microseconds` (`UInt32`) - 항목의 마이크로 초. 
-`thread_name` (String) - Name of the thread from which the logging was done . 
-`thread_id` (UInt64) - OS thread ID.  
-`level` (`Enum8`) - 엔트리 레벨. 
    -` 'Fatal'= 1`
    -` 'Critical'= 2` 
    -` 'Error'= 3` 
    -` ' Warning '= 4` 
    -`'Notice '= 5` 
    -`'Information '= 6` 
    -`'Debug '= 7` 
    -`'Trace '= 8` 
-`query_id` (`String`) - 쿼리의 ID . 
-`logger_name` (`LowCardinality (String)`) - Name of the logger (ie 
`DDLWorker`) -`message` (`String`) - 메시지 자체. 
-`revision` (`UInt32`) -ClickHouse 수정. 
-`source_file` (`LowCardinality (String)`) - 로깅이 수행 된 소스 파일. 
-`source_line` (`UInt64`) - 로깅이 이루어졌다 소스 라인.

## 시스템 query_log {# system_tables-query_log} 

쿼리 실행에 대한 정보가 포함되어 있습니다. 쿼리에 대해 처리 시작 시간, 처리 시간, 오류 메시지 및 기타 정보를 확인할 수 있습니다. 

!!! note "주"

ClickHouse이 테이블을 만듭니다. [query_log (server-configuration-parameters / settings.md # server_configuration_parameters-query-log) server 매개 변수를 지정합니다. 이 매개 변수는 쿼리가 로그인하는 테이블의 로그 간격이나 이름 등의 로그 규칙을 설정합니다. 

쿼리 로깅을 활성화하려면 [log_queries (settings / settings.md # settings-log-queries) 파라미터는 1. 자세한 내용은 설정 (settings / settings.md) 섹션 

의`system.query_log` 테이블 레지스터의 종류는 문의 : 

1. 클라이언트에 의해 직접 실행 된 초기 쿼리. 
2. 다른 쿼리에서 시작된 아이 쿼리 (분산 쿼리 실행 용). 이러한 유형의 쿼리에 대해서는 부모 쿼리에 대한 정보를`initial_ *`라인. 

열 : 

-`type` (`Enum8`) - Type of event that occurred when executing the query . Values : 
    -` 'QueryStart'= 1` - Successful start of query execution.
-`written_rows` (UInt64) - For`INSERT` 쿼리 기록 된 행의 수입니다. 다른 쿼리의 경우, 열의 값은 0입니다. 
-`written_bytes` (UInt64) - For`INSERT` 쿼리 기록 된 바이트 수. 다른 쿼리의 경우, 열의 값은 0입니다.
-`result_rows` (UInt64) - Number of rows in the result. 
-`result_bytes` (UInt64) - Number of bytes in the result. 
-`memory_usage` (UInt64) - Memory consumption by the query.
-`query` (String) - Query string. 
-`exception` (String) - Exception message. 
-`stack_trace` (String) - Stack trace (a list of methods called before the error occurred ) An empty string, if the query is completed successfully. 
-`is_initial_query` (UInt8) - query type. Possible values : 
    - 1 - query was initiated by the client. 
    - 0 - query was initiated by another query for distributed query execution . 
-`user` (String) - Name of the user who initiated the current query . 
-`query_id` (String) - ID of the query. 
-`address` (IPv6) - IP address that was used to make the query .
-`port` (UInt16) - The client port that was used to make the query . 
-`initial_user` (String) - Name of the user who ran the initial query (for distributed query execution) 
-`initial_query_id` (String) - ID of the initial query (for distributed query execution) 
-`initial_address` (IPv6) - IP address that the parent query was launched from . 
-`initial_port` (UInt16) - the client port that was used to make the parent query . 
-`interface` (UInt8) - Interface that the query was initiated from. Possible values : 
    - 1 - TCP. 
    - 2 - HTTP. 
-`os_user` (String) - OS 's username who runs [clickhouse- 클라이언트 (.. /interfaces/cli.md).
-`client_hostname` (String) - Hostname of the client machine where the [clickhouse- 클라이언트 (../ interfaces / cli.md) 또는 다른 TCP 클라이언트가 실행됩니다. 
-`client_name` (String) - The [clickhouse- 클라이언트 (../ interfaces / cli.md) 또는 다른 TCP 클라이언트 이름.
-`client_revision` (UInt32) - Revision of the [clickhouse- 클라이언트 (../ interfaces / cli.md) 또는 다른 TCP 클라이언트. 
-`client_version_major` (UInt32) - Major version of the [clickhouse- 클라이언트 (../ interfaces / cli.md) 또는 다른 TCP 클라이언트. 
-`client_version_minor` (UInt32) - Minor version of the [clickhouse- 클라이언트 (../ interfaces / cli.md) 또는 다른 TCP 클라이언트. 
-`client_version_patch` (UInt32) - Patch component of the [clickhouse- 클라이언트 (../ interfaces / cli.md) 다른 TCP 클라이언트 버전. 
-`http_method` (UInt8) - HTTP method that initiated the query. Possible values : 
    - 0 - The query was launched from the TCP interface. 
    - 1 -`GET` 방법을 사용 하였다. 
    - 2 -`POST` 방법을 사용 하였다. 
-`http_user_agent` (String) - The`UserAgent` HTTP 요청에서 전달되는 헤더.
-`quota_key` (String) - The "quota key"로 지정된다. 할당량 (quotas.md) 설정 (참조`keyed`) 
-`revision` (UInt32) - ClickHouse revision. 
-`thread_numbers` (Array (UInt32)) - Number of threads that are participating in query execution . 
-` ProfileEvents.Names` (Array (String)) - Counters that measure different metrics. the description of them could be found in the table 시스템 이벤트 (# system_tables-events) 
-`ProfileEvents.Values` (Array (UInt64)) - Values of metrics that are listed in the`ProfileEvents.Names` 라인. 
-`Settings.Names` (Array (String)) - Names of settings that were changed when the client ran the query . To enable logging changes to settings, set the`log_query_settings` 매개 변수는 1.
-`Settings.Values` (Array (String)) - Values of settings that are listed in the`Settings.Names` 라인. 

각각의 쿼리는 하나 또는 두 줄이`query_log` 쿼리의 상태에 따라 테이블 : 

1. 쿼리의 실행이 성공하면 유형 1과 유형 2의 이벤트가 생성됩니다. `type` 열).
2. 쿼리 처리 중 오류가 발생한 경우에는 유형 1과 4의 이벤트가 생성됩니다. 
3. 쿼리를 실행하기 전에 오류가 발생하는 경우 유형 3의 단일 이벤트가 생성됩니다. 

기본적으로 로그는 7.5 초 간격으로 테이블에 추가됩니다. 이 간격은 [query_log (server-configuration-parameters / settings.md # server_configuration_parameters-query-log) 서버 설정 (참조`flush_interval_milliseconds` 변수). 로그를 메모리 버퍼에서 테이블에 강제로 플래시`SYSTEM FLUSH LOGS` 쿼리. 

테이블을 수동으로 삭제하면 그 자리에서 자동으로 생성됩니다. 이전 로그는 모두 삭제됩니다. 

!!! note "주" 
    로그의 보존 기간은 무제한입니다. 로그는 테이블에서 자동으로 삭제되지 않습니다. 오래된 로그 삭제를 스스로 정리해야합니다. 

파티션 키를 지정할 수 있습니다. `system.query_log` 테이블 [query_log (server-configuration-parameters / settings.md # server_configuration_parameters-query-log) 서버 설정 (참조`partition_by` 변수). 

## 시스템 query_thread_log {# system_tables-query-thread-log} 

테이블에 대한 정보가 포함 된 각각의 검색어 실행 스레드.

ClickHouse이 테이블을 만듭니다. [query_thread_log (server-configuration-parameters / settings.md # server_configuration_parameters-query-thread-log) server 매개 변수를 지정합니다. 이 매개 변수는 쿼리가 로그인하는 테이블의 로그 간격이나 이름 등의 로그 규칙을 설정합니다. 

쿼리 로깅을 활성화하려면 [log_query_threads (settings / settings.md # settings-log-query-threads) 파라미터는 1. 자세한 내용은 설정 (settings / settings.md) 섹션 

열 : 

-`event_date` (Date) - the date when the thread has finished execution of the query . 
-`read_bytes` (UInt64) - Number of read bytes. 
-`written_rows` (UInt64) - For` INSERT` 쿼리 기록 된 행의 수입니다. 다른 쿼리의 경우, 열의 값은 0입니다. 
-`event_time` (DateTime) - the date and time when the thread has finished execution of the query.
-`query_start_time` (DateTime) - Start time of query execution. 
-`query_duration_ms` (UInt64) - Duration of query execution. 
-`read_rows` (UInt64) - Number of read rows. 
-`master_thread_id` (UInt64) - OS initial ID of initial thread .
-`written_bytes` (UInt64) - For`INSERT` 쿼리 기록 된 바이트 수. 다른 쿼리의 경우, 열의 값은 0입니다. 
-`memory_usage` (Int64) - The difference between the amount of allocated and freed memory in context of this thread . 
-`peak_memory_usage` (Int64) - The maximum difference between the amount of allocated and freed memory in context of this thread . 
- `thread_name` (String) - Name of the thread. 
-`thread_number` (UInt32) - Internal thread ID. 
-`os_thread_id` (Int32) - OS thread ID. 
    - 1 - query was initiated by the client. 
    - 0 - query was initiated by another query for distributed query execution .
-`query` (String) - Query string. 
-`is_initial_query` (UInt8) - Query type. Possible values : 
-`user` (String) - Name of the user who initiated the current query . 
-`query_id` (String) - ID of the query. 
-`address` (IPv6) - IP address that was used to make the query . 
-`port` (UInt16) - the client port that was used to make the query . 
-`initial_user` (String) - Name of the user who ran the initial query (for distributed query execution) 
-`initial_query_id` (String) - ID of the initial query (for distributed query execution) 
-`initial_address` (IPv6) - IP address that the parent query was launched from. 
-`initial_port` (UInt16) - the client port that was used to make the parent query .
-`interface` (UInt8) - Interface that the query was initiated from. Possible values :  
    - 1 - TCP. 
    - 2 - HTTP.
-`os_user` (String) - OS 's username who runs [clickhouse- 클라이언트 (../ interfaces / cli.md) 
-`client_hostname `(String) - Hostname of the client machine where the [clickhouse- 클라이언트 (../ interfaces / cli.md) 또는 다른 TCP 클라이언트가 실행됩니다. 
-`client_name` (String) - The [clickhouse- 클라이언트 (../ interfaces / cli.md) 또는 다른 TCP 클라이언트 이름. 
-`client_revision` (UInt32) - Revision of the [clickhouse- 클라이언트 (../ interfaces / cli.md) 또는 다른 TCP 클라이언트. 
-`client_version_major` (UInt32) - Major version of the [clickhouse- 클라이언트 (../ interfaces / cli.md) 또는 다른 TCP 클라이언트. 
-`client_version_minor` (UInt32) - Minor version of the [clickhouse- 클라이언트 (../ interfaces / cli.md) 또는 다른 TCP 클라이언트.
-`client_version_patch` (UInt32) - Patch component of the [clickhouse- 클라이언트 (../ interfaces / cli.md) 다른 TCP 클라이언트 버전. 
-`http_method` (UInt8) - HTTP method that initiated the query. Possible values :
    - 0 - The query was launched from the TCP interface. 
    - 1 -`GET` 방법을 사용 하였다. 
    - 2 -`POST` 방법을 사용 하였다. 
-`http_user_agent` (String) - The`UserAgent` HTTP 요청에서 전달되는 헤더. 
-`quota_key` (String) - The "quota key"로 지정된다. 할당량 (quotas.md) 설정 (참조`keyed`) 
-`revision` (UInt32) - ClickHouse revision.  
-`ProfileEvents.Names` (Array (String)) - Counters that measure different metrics for this thread. The description of them could be found in the table 시스템 이벤트 (# system_tables-events)
-`ProfileEvents.Values` (Array (UInt64)) - Values of metrics for this thread that are listed in the`ProfileEvents.Names` 라인. 

기본적으로 로그는 7.5 초 간격으로 테이블에 추가됩니다. 이 간격은 [query_thread_log (server-configuration-parameters / settings.md # server_configuration_parameters-query-thread-log) 서버 설정 (참조`flush_interval_milliseconds` 변수). 로그를 메모리 버퍼에서 테이블에 강제로 플래시`SYSTEM FLUSH LOGS` 쿼리. 

테이블을 수동으로 삭제하면 그 자리에서 자동으로 생성됩니다. 이전 로그는 모두 삭제됩니다.

!!! note "주" 
    로그의 보존 기간은 무제한입니다. 로그는 테이블에서 자동으로 삭제되지 않습니다. 오래된 로그 삭제를 스스로 정리해야합니다. 

파티션 키를 지정할 수 있습니다. `system.query_thread_log` 테이블 [query_thread_log (server-configuration-parameters / settings.md # server_configuration_parameters-query-thread-log) 서버 설정 (참조`partition_by` 변수). 

## 시스템 trace_log {# system_tables-trace_log} 

을 포함 스택 추적의 수집 샘플링 크로 파일러.
 
ClickHouse이 테이블을 만듭니다. [trace_log (server-configuration-parameters / settings.md # server_configuration_parameters-trace_log) 서버 설정이 설정됩니다. 또한, query_profiler_real_time_period_ns (settings / settings.md # query_profiler_real_time_period_ns) 및 query_profiler_cpu_time_period_ns (settings / settings.md # query_profiler_cpu_time_period_ns) 설정은 설정해야합니다. 

로그를 분석하려면`addressToLine``addressToSymbol`와`demangle` 반성 함수.

열 : 

-`event_date` (날짜 (../ sql-reference / data-types / date.md)) - Date of sampling moment. 

-`event_time` (DateTime] (../ sql-reference / data -types / datetime.md)) - Timestamp of the sampling moment. 

-`timestamp_ns` (UInt64] (../ sql-reference / data-types / int-uint.md)) - Timestamp of the sampling moment in nanoseconds . 

-`revision` (UInt32] (../ sql-reference / data-types / int-uint.md)) - clickHouse server build revision.
    -`CPU` CPU 시간을 나타냅니다.

    서버에 연결할 때`clickhouse-client` 같은 문자열을 참조하십시오`Connected to ClickHouse server version 19.18.1 revision 54429.`이 필드에는`revision` 대신`version` 서버. 

-`timer_type` (Enum8] (../ sql-reference / data-types / enum.md)) - Timer type : 

    -`Real` 벽 시계의 시간을 나타냅니다. 

-`thread_number` (UInt32] (../ sql-reference / data-types / int-uint.md)) - Thread identifier. 

-`query_id` (문자열] (../ sql-reference / data- types / string.md)) - query identifier that can be used to get details about a query that was running from the [query_log (# system_tables-query_log) 시스템 테이블. 

-`trace` (배열 (UInt64)] (../ sql-reference / data-types / array.md)) - Stack trace at the moment of sampling. Each element is a virtual memory address inside ClickHouse server process . 

* * 예 ** 

```sql 
SELECT * FROM system.trace_log LIMIT 1 \ G 
``` 

```text 
Row 1 : 
────── 
event_date : 2019-11-15 
event_time : 2019-11-15 15 : 09:38 
revision : 54428 
timer_type : Real 
thread_number : 48 
query_id : acc4d61f-5bd1-4a3e-bc91-2180be37c915 
trace : 94222141367858,94222152240175,94222152325351,94222152329944,94222152330796,94222151449980,94222144088167,94222151682763,94222144088167,94222151682763,94222144088167,94222144058283,94222144059248,94222091840750,94222091842302,94222091831228,94222189631488, 140509950166747,140509942945935] 
``` 

## 시스템 복제 {# system_tables-replicas} 

정보 및 상황을 재현 테이블 거주 지역 서버입니다. 
이 테이블은 모니터링 할 수있다. 테이블이 포함 된 행마다 재현 \ *입니다. 

예 : 

```sql 
SELECT * 
FROM system.replicas 
WHERE table = 'visits' 
FORMAT Vertical 
``` 

```text 
Row 1 : 
────── 
database : merge
table : visits 
engine : ReplicatedCollapsingMergeTree 
is_leader : 1 
can_become_leader : 1 
is_readonly : 0 
is_session_expired : 0 
future_parts : 1 
parts_to_check : 0 
zookeeper_path : / clickhouse / tables / 01-06 / visits 
replica_name : example01-06-1.yandex.ru 
replica_path : / clickhouse / tables / 01-06 / visits / replicas / example01-06-1.yandex.ru 
part_mutations_in_queue : 0 
queue_oldest_time : 2020-02-20 08:34:30 
columns_version : 9
queue_size : 1 
inserts_in_queue : 0 
merges_in_queue : 1 
inserts_oldest_time : 1970-01-01 00:00:00 
merges_oldest_time : 2020-02-20 08:34:30 
part_mutations_oldest_time : 1970-01-01 00:00:00 
oldest_part_to_get : 
oldest_part_to_merge_to : 20200220_20284_20840_7 
oldest_part_to_mutate_to : 
log_max_index : 596273 
log_pointer : 596274 
last_queue_update : 2020-02-20 08:34:32 
absolute_delay : 0 
total_replicas : 2 
active_replicas : 2 
``` 

열 : 

-`database` (`String`) - 데이터베이스 이름 
-`table` (`String`) - 테이블 이름 
-`engine` (`String`) - 테이블 엔진 이름 
-`is_leader` (`UInt8`) - 복제가 리더인지 여부. 
    한 번에 하나의 복제 만이 리더가 될 수 있습니다. 리더는 같이 백그라운드 병합을 선택합니다. 
    쓰기는 리더인지 여부에 관계없이 사용 가능 ZK 세션이있는 모든 복제본에 대해 실행할 수 있습니다. 
-`can_become_leader` (`UInt8`) - 복제가 리더로 선출 할 수 있는지 여부. 
-`is_readonly` (`UInt8`) - 복제가 읽기 전용 모드인지 여부. 
    이 모드는 설정에 ZooKeeper 섹션이 없으면 zookeeper에서 세션을 다시 초기화 할 때, 그리고 ZooKeeper에서 세션을 다시 초기화 할 때 알 수없는 오류가 발생 
-`is_session_expired` (`UInt8`) -ZooKeeper 에 대한 세션이 종료되었습니다. 기본적으로`is_readonly`. 
-`future_parts` (`UInt32`) - 아직 완료되지 않은 삽입 또는 병합의 결과로 나타나는 데이터 부품의 수입니다. 
-`parts_to_check` (`UInt32`) - 검증을위한 큐의 데이터 부분의 수입니다. 손상이 의심되는 경우에는 부품을 검증 큐에 넣습니다. 
-`zookeeper_path` (`String`) -ZooKeeper 테이블 데이터의 경로. 
-`replica_name` (`String`) - 사육사 복제 이름. 같은 테이블의 다른 복제본의 이름은 다릅니다.
-`replica_path` (`String`) -ZooKeeper 복제 데이터의 경로. 연결과 같은 'zookeeper_path / replicas / replica_path'. 
-`columns_version` (`Int32`) - 테이블 구조의 버전 번호. 변경이 실행 된 횟수를 나타냅니다. 경우 복제는 다른 버전에서 일부 복제 씨의 모든 변경 사항은 아직 없다. 
-`queue_size` (`UInt32`) - 대기중인 작업 큐의 크기. 작업은 데이터 블록의 삽입, 병합, 기타 특정 작업이 포함됩니다. 그것은 일반적으로 일치합니다`future_parts`. 
-`inserts_in_queue` (`UInt32`) - 필요한 데이터 블록의 삽입 수. 삽입은 일반적으로 매우 빠르게 복제됩니다. 이 숫자가 크면 뭔가 잘못하고 있다는 것을 의미합니다. 
-`merges_in_queue` (`UInt32`) - 행해지을 기다리는 병합 수입니다. 때로는 병합이 길기 때문에이 값은 오랫동안 제로보다 커질 수 있습니다. 
-`part_mutations_in_queue` (`UInt32`) - 만들어지는 것을 기다리는 돌연변이의 수입니다. 
-`queue_oldest_time` (`DateTime`) -If`queue_size` 0보다 큰 경우 가장 오래된 작업이 언제 큐에 추가되었는지를 나타냅니다. 
-`inserts_oldest_time` (`DateTime`) - 참조`queue_oldest_time`
-`merges_oldest_time` (`DateTime`) - 참조`queue_oldest_time` 
-`part_mutations_oldest_time` (`DateTime`) - 참조`queue_oldest_time` 
 
다음 4 개의 열은 zk의 활성 세션이있는 경우에만 0이 아닌 값 를가집니다.

-`log_max_index` (`UInt64`) - 일반적인 활동 로그의 최대 항목 번호. 
-`log_pointer` (`UInt64`) - 복제가 실행 큐에 복사 한 일반적인 활동 로그의 최대 항목 번호를 더한 것. 만약`log_pointer` 훨씬 작은`log_max_index` 뭔가 이상 
-`last_queue_update` (`DateTime`) - 큐가 마지막 updated 된 때. 
-`absolute_delay` (`UInt64`) - 현재 복제가 가지고있는 초 단위의 큰 지연. 
-`total_replicas` (`UInt8`) -이 테이블의 기존 복제본 수입니다. 
-`active_replicas` (`UInt8`) -ZooKeeper 세션이이 테이블의 복제 수 (즉, 작동하는 복제의 수). 

모든 열을 요청하면 ZooKeeper의 일부를 읽을 줄마다 이루어지기 때문에 테이블은 조금 늦게 작동 할 수 있습니다. 
마지막 4 개의 열 (log_max_index, log_pointer, total_replicas, active_replicas)를 요구하지 않으면 테이블은 즉시 작동합니다.

예를 들어, 다음과 같이 모든 것이 제대로 작동하고 있는지 확인할 수 있습니다 : 

```sql 
SELECT 
    database,  
    table, 
    is_leader,
    is_readonly, 
    is_session_expired, 
    future_parts, 
    parts_to_check, 
    columns_version, 
    queue_size, 
    inserts_in_queue, 
    merges_in_queue, 
    log_max_index, 
    log_pointer, 
    total_replicas, 
    active_replicas 
FROM system.replicas 
WHERE 
       is_readonly 
    OR is_session_expired 
    OR future_parts> 20 
    OR parts_to_check> 10 
    OR queue_size> 20 
    OR inserts_in_queue> 10 
    OR log_max_index - log_pointer> 10
    OR total_replicas <2 
    OR active_replicas <total_replicas 
``` 
 
이 쿼리가 아무것도 돌려주지 않는 경우는 모두 정상임을 의미합니다.

## 시스템 설정 {# system-tables-system-settings} 

현재 유 

열 : 

-`name` (문자열] (../ sql-reference / data-types /string.md)) - Setting name. 
-`value` (문자열] (../ sql-reference / data-types / string.md)) - Setting value. 
-`changed` (UInt8] (. ./sql-reference/data-types/int-uint.md#uint-ranges)) - Shows whether a setting is changed from its default value . 
-`description` (문자열] (../ sql-reference / data-types / string.md)) - Short setting description.
-`min` (Null 가능 (../ sql-reference / data-types / nullable.md) ([문자열] (../ sql-reference / data-types / string.md))) - Minimum value of the setting, if any is set via 제약 (settings / constraints-on-settings.md # constraints-on-settings) 최소값이 설정되어 있지 않은 경우는 다음을 포함합니다 [NULL] (. ./sql-reference/syntax.md#null-literal) 
    -`0` - Current user can change the setting.
-`max` (Null 가능 (../ sql-reference / data-types / nullable.md) ([문자열] (../ sql-reference / data-types / string.md))) - Maximum value of the setting, if any is set via 제약 (settings / constraints-on-settings.md # constraints-on-settings) 최대 값이 설정되어 있지 않은 경우는 다음을 포함합니다 [NULL] (. ./sql-reference/syntax.md#null-literal) 
-`readonly` (UInt8] (../ sql-reference / data-types / int-uint.md # uint-ranges)) - Shows whether the current user can change the setting : 
    -`1` - Current user can not change the setting. 

** 예 ** 

다음 예제는 이름에 포함 된 설정에 대한 정보를 검색하는 방법을 보여줍니다`min_i`. 

```sql 
SELECT * 
FROM system.settings 
WHERE name LIKE '% min_i %' 
``` 

```text
┌─name────────────────────────────────────────┬─value──── ─┬─changed─┬─description────────────────────────────────────────── ────────────────────────────────────────────────── ────────────────────────────────────────────────── ─────────────┬─min──┬─max──┬─readonly─┐ 
│ read_backoff_min_interval_between_events_ms │ 1000 │ 0 │ Settings to reduce the number of threads in case of slow reads . Do not pay attention to the event, if the previous one has passed less than a certain amount of time. │ ᴺᵁᴸᴸ │ ᴺᵁᴸᴸ │ 0 │ 
│ min_insert_block_size_rows │ 1048576 │ 0 │ Squash blocks passed to INSERT query to specified size in rows , if blocks are not big enough. │ ᴺᵁᴸᴸ │ ᴺᵁᴸᴸ │ 0 │
│ min_insert_block_size_bytes │ 268435456 │ 0 │ Squash blocks passed to INSERT query to specified size in bytes , if blocks are not big enough. │ ᴺᵁᴸᴸ │ ᴺᵁᴸᴸ │ 0 │ 
└──────────────── ─────────────────────────────┴───────────┴──────── ─┴──────────────────────────────────────────────── ────────────────────────────────────────────────── ────────────────────────────────────────────────── ───────────────────┴──────┴──────┴──────────┘ 
``` 

사용 `WHERE changed` 예를 들어 검사 할 때 유용합니다 : 

- 구성 파일의 설정이 잘못 읽혀 사용되고 있는지. 
- 현재 세션에서 변경된 설정. 

<! - -> 

```sql 
SELECT * FROM system.settings WHERE changed AND name = 'load_balancing' 
```

** 참고하십시오. ** 

- 설정 (settings / index.md # session-settings-intro) 
- 쿼리 권한 (settings / permissions-for-queries.md # settings_readonly) 
-`value` (String) - Setting value. 
- 설정의 제약 (settings / constraints-on -settings.md)

## 시스템 table_engines {# system.table_engines} 

```text 
┌─name───────────────────┬─value───────┐ 
│ max_threads │ 8 │ 
│ use_uncompressed_cache │ 0 │ 
│ load_balancing │ random │ 
│ max_memory_usage │ 10,000,000,000 │ 
└────────────────────────┴─────── ──────┘ 
``` 

## 시스템 merge_tree_settings {# system-merge_tree_settings} 

설정에 대한 정보가 포함되어 있습니다`MergeTree` 테이블 

열 : 

-`name` (String) - Setting name. 
-`description` ( String) - Setting description. 
-`type` (String) - Setting type (implementation specific string value).
-`changed` (UInt8) - Whether the setting was explicitly defined in the config or explicitly changed . 

## 시스템 table_engines {# system-table-engines} 

을 포함하는 기술의 테이블 엔진을 지원 서버와 그 특징을 지원합니다. 

이 테이블 다음 컬럼의 컬럼 유형은 브래킷) : 

-`name` (String) - The name of table engine. 
-`supports_settings` (UInt8) - Flag that indicates if table engine supports`SETTINGS` 절. 
-`supports_skipping_indices` (UInt8) - Flag that indicates if table engine supports 색인 건너 뛰기 (../ engines / table-engines / mergetree-family / mergetree.md # table_engine-mergetree-data_skipping-indexes).
-`supports_ttl` (UInt8) - Flag that indicates if table engine supports [TTL] (../ engines / table-engines / mergetree-family / mergetree.md # table_engine-mergetree-ttl) 
-`supports_sort_order` (UInt8) - Flag that indicates if table engine supports clauses`PARTITION_BY``PRIMARY_KEY``ORDER_BY`와`SAMPLE_BY`. 
-`supports_replication` (UInt8) - Flag that indicates if table engine supports 데이터 복제 (../ engines / table-engines / mergetree-family / replication.md) 
-`supports_duduplication` (UInt8) - Flag that indicates if table engine supports data deduplication . 

예 : 

```sql 
SELECT * 
FROM system.table_engines 
WHERE name in ( 'Kafka', 'MergeTree', 'ReplicatedCollapsingMergeTree') 
``` 

```text 
┌─name───────────── ─────────────┬─supports_settings─┬─supports_skipping_indices─┬─supports_sort_order─┬─supports_ttl─┬─supports_replication─┬─supports_deduplication─┐
│ Kafka │ 1 │ 0 │ 0 │ 0 │ 0 │ 0 │  
│ MergeTree │ 1 │ 1 │ 1 │ 1 │ 0 │ 0 │
│ ReplicatedCollapsingMergeTree │ 1 │ 1 │ 1 │ 1 │ 1 │ 1 │ 
└─────────────────── ────────────┴───────────────────┴───────────────── ──────────┴─────────────────────┴──────────────┴── ────────────────────┴────────────────────────┘ 
``` 

* * 참조. ** 

- 메루게쯔리 족 쿼리 절 (../ engines / table-engines / mergetree-family / mergetree.md # mergetree-query-clauses) 
- 카프카 [설정] (../ engines / table-engines / integrations / kafka.md # table_engine-kafka-creating a-table)
- 참가 [설정] (../ engines / table-engines / special / join.md # join-limitations-and-settings) 

## 시스템 테이블 {# system-tables} 

를 포함한 메타 데이터는 각 테이블 서버에 알고 있습니다. 분리 된 테이블은`system.tables`. 

이 테이블 다음 컬럼의 컬럼 유형은 브래킷) : 

-`database` (String) - The name of the database the table is in . 

-`name` (String) - Table name. 

-`engine` (String) - Table engine name (without parameters) 

-`is_temporary` (UInt8) - 테이블이 임시 여부를 나타내는 플래그. 

-`data_path` (String) - 파일 시스템의 테이블 데이터의 경로. 

-`metadata_path` (String) - 파일 시스템의 테이블 메타 데이터의 경로. 

-`metadata_modification_time` (DateTime) - 테이블 메타 데이터의 최신 수정 시간. 

-`dependencies_database` (Array (String)) - 데이터베이스 종속성.

-`dependencies_table` (Array (String)) - 테이블의 종속성 (실현보기] (../ engines / table-engines / special / materializedview.md) 현재 테이블을 기반으로 테이블). 

-`create_table_query` (String) - 테이블을 만드는 데 사용 된 쿼리. 

-`engine_full` (String) - 테이블 엔진 매개 변수입니다. 

-`partition_key` (String) - 테이블에서 지정된 파티션 키 식. 

-`sorting_key` (String) - 테이블에서 지정된 정렬 키 표현식. 

-`primary_key` (String) - 테이블에서 지정된 기본 키 식. 

-`sampling_key` (String) - 테이블에서 지정된 샘플링 키 식.

-`storage_policy` (String) - 스토리지 정책 : 

    - [메루게쯔리] (../ engines / table-engines / mergetree-family / mergetree.md # table_engine-mergetree-multiple-volumes) 
    - [분산] (../ engines /table-engines/special/distributed.md#distributed) 

-`total_rows` (Nullable (UInt64)) - 테이블의 정확한 행을 신속히 결정할 경우, 행의 총 수입니다. `Null` (언더 잉을 포함한`Buffer` 표).

-`total_bytes` (Nullable (UInt64)) - 스토리지에 테이블의 정확한 바이트 수를 신속하게 결정할 수 경우 총 바이트 수. `Null` (**없는 ** 기본 저장소 포함). 

    - If the table stores data on disk, returns used space on disk (ie compressed) 
    - 테이블이 메모리에 데이터를 저장하는 경우는 메모리의 사용 바이트 수를 추정을 반환합니다. 

그`system.tables` 테이블로 사용됩니다`SHOW TABLES` 쿼리의 구현. 

## 시스템 사육사 {# system-zookeeper} 

ZooKeeper가 설정되어 있지 않은 경우, 테이블은 존재하지 않습니다. 있는 데이터를 읽어 들여 사육사 클러스터에 정의되어 config.
쿼리는 'path'WHERE 절 동등 조건. 이것은 데이터를 검색하는 아이들을위한 ZooKeeper의 경로입니다. 

쿼리`SELECT * FROM system.zookeeper WHERE path = '/ clickhouse'` 모든 자식의 데이터를 출력합니다. `/ clickhouse` 노드 
모든 루트 노드의 데이터를 출력하려면 path = '/'. 
지정된 경로의 경우 'path'존재하지 않는 경우, 예외가 슬로우됩니다. 

열 : 

-`name` (String) - The name of the node. 
-`path` (String) - The path to the node.  
-`value` (String) - Node value.
-`dataLength` (Int32) - Size of the value. 
-`numChildren` (Int32) - Number of descendants. 
-`czxid` (Int64) - ID of the transaction that created the node. 
-`mzxid` (Int64) - ID of the transaction that last changed the node . 
-`pzxid` (Int64) - ID of the transaction that last deleted or added descendants. 
-`ctime` (DateTime) - Time of node creation. 
-`mtime` (DateTime) - Time of the last modification of the node. 
-`version` (Int32) - node version : the number of times the node was changed. 
-`cversion `(Int32) - Number of added or removed descendants. 
-`aversion` (Int32) - Number of changes to the ACL. 
-`ephemeralOwner` (Int64) - For ephemeral nodes, the ID of the session that owns this node . 

예 : 

```sql 
SELECT * 
FROM system.zookeeper
WHERE path = '/ clickhouse / tables / 01-08 / visits / replicas' 
FORMAT Vertical 
``` 

```text 
Row 1 : 
────── 
name : example01-08-1.yandex.ru 
value :  
czxid : 932998691229 
mzxid : 932998691229
ctime : 2015-03-27 16:49:51 
mtime : 2015-03-27 16:49:51 
version : 0 
cversion : 47 
aversion : 0 
ephemeralOwner : 0 
dataLength : 0 
numChildren : 7 
pzxid : 987021031383 
path : / clickhouse / tables / 01-08 / visits / replicas 

Row 2 : 
────── 
name : example01-08-2.yandex.ru 
value : 
czxid : 933002738135 
mzxid : 933002738135 
ctime : 2015-03-27 16:57:01 
mtime : 2015- 03-27 16:57:01 
version : 0 
cversion : 37 
aversion : 0 
ephemeralOwner : 0 
dataLength : 0 
numChildren : 7
pzxid : 987021252247 
path : / clickhouse / tables / 01-08 / visits / replicas 
``` 

## 시스템 돌연변이 {# system_tables-mutations} 

테이블에 대한 정보가 포함 된 [돌연변이] (../ sql- reference / statements / alter.md # alter-mutations) 병합 트리 테이블과 진행. 각 돌연변이 명령은 한 줄로 표시됩니다. 테이블에는 다음 열이 있습니다 : 

** 데이터 ** ** 테이블 ** - 돌연변이가 적용된 데이터베이스 및 테이블의 이름. 

** mutation_id ** - 돌연변이의 ID입니다. 복제 된 테이블의 경우 이러한 Id는`<table_path_in_zookeeper> / mutations /`사육사 디렉토리. 미 복제 테이블의 경우 Id는 테이블의 데이터 디렉토리에있는 파일 이름에 해당합니다. 

** 명령 ** - 돌연변이 명령 문자열 (후 쿼리 부분`ALTER TABLE [db] table`) 

** create_time ** -이 돌연변이 명령이 실행을 위해 제출 된 때.

** 블록 번호 partition_id ** ** 블록 번호 번호 ** - 중첩 된 열. 파티션 ID와 그 변이에 의해 취득 된 블록 번호 (각 파티션은 파티션의 변이에 의해 취득 된 블록 번호 비 복제 테이블 블록 번호의 모든 칸막이가 하나의 시퀀스입니다.이 없다는 것을 의미 하는 돌연변이 재현 테이블의 열로 확장하고있는 것이 기록하며 단일 블록 번호의 취득에 의한 돌연변이가 원인입니다. 

** parts_to_do ** - 돌연변이가 완료 돌연변이해야 이 데이터 부분의 수입니다. 

** is_done ** - 돌연변이는? 비록`parts_to_do = 0` 변경할 필요가있는 새로운 데이터 파트를 만들 장시간 실행되는 INSERT를 위해 복제 된 테이블 갑자기 변이가 아직 완료되지 않은 가능성이 

일부 부품의 변경에 문제가있는 경우 다음 열에 추가 정보가 포함되어 있습니다 : 

** latest_failed_part ** - 변이 할 수 없었던 최신 부분의 이름. 

** latest_fail_time ** - 최신 조각 돌연변이의 실패의 시간. 

** latest_fail_reason ** - 최신 부품 돌연변이의 오류를 일으킨 예외 메시지. 

## 시스템 디스크 {# system_tables-disks } 

디스 [서버 구성] (../ engines / table-engines / mergetree-family / mergetree.md # table_engine-mergetree-multiple-volumes_configure) 

열 :

-`name` (문자열] (../ sql-reference / data-types / string.md)) - Name of a disk in the server configuration. 
-`path` (문자열] (../ sql -reference / data-types / string.md)) - Path to the mount point in the file system . 
-`free_space` (UInt64] (../ sql-reference / data-types / int-uint.md)) - Free space on disk in bytes. 
-`total_space` (UInt64] (../ sql-reference / data-types / int-uint.md)) - disk volume in bytes. 
-`keep_free_space` (UInt64 ( ../sql-reference/data-types/int-uint.md)) - Amount of disk space that should stay free on disk in bytes . Defined in the`keep_free_space_bytes` 디스크 구성 매개 변수입니다. 

## 시스템 스토리지 정책 {# system_tables-storage_policies} 

스토리지 정책 [서버 구성] (../ engines / table-engines / mergetree-family / mergetree.md # table_engine-mergetree-multiple-volumes_configure).
 
열 : 

-`policy_name` (문자열] (../ sql- reference / data-types / string.md)) - name of the storage policy. 
-`volume_name` (문자열] (../ sql-reference / data-types / string.md)) - Volume name defined in the storage policy. 
-`volume_priority` (UInt64] (../ sql-reference / data-types / int-uint.md)) - Volume order number in the configuration. 
-`disks` (배열 (문자열)] (../sql-reference/data-types/array.md)) - Disk names, defined in the storage policy. 
-`max_data_part_size` (UInt64] (../ sql-reference / data-types / int-uint .md)) - Maximum size of a data part that can be stored on volume disks (0 - no limit).
-`move_factor` (Float64] (../ sql-reference / data-types / float.md)) - Ratio of free disk space. When the ratio exceeds the value of configuration parameter , ClickHouse start to move data to the next volume in order.
 
스토리지 정책 여러 볼륨이 포함되어있는 경우 각 볼륨의 정보가 테이블의 각 행에 저장됩니다.

원본 기사 (https://clickhouse.tech/docs/en/operations/system_tables/) <! - hide ->
