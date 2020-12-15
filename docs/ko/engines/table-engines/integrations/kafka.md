---
machine_translated : true
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd
toc_priority : 32
toc_title : "\ u30AB \ u30D5 \ u30AB"
---

# 카프카 {#kafka}

이 엔진은 [아파치 - 카프카 (http://kafka.apache.org/).

카프카는 당신을 수 있습니다 :

- 데이터 흐름을 게시하거나 구독한다.
- 정리 - 내결함성 보관합니다.
- 스트림이 사용 가능 해지면 처리합니다.

## 테이블 만들기 {# table_engine-kafka-creating a-table}

```sql
CREATE TABLE [IF NOT EXISTS] [db] table_name [ON CLUSTER cluster]
(
    name1 [type1] [DEFAULT | MATERIALIZED | ALIAS expr1,
    name2 [type2] [DEFAULT | MATERIALIZED | ALIAS expr2]
    ...
) ENGINE = Kafka ()
SETTINGS
    kafka_broker_list = 'host : port',
    kafka_topic_list = 'topic1, topic2 ...'
    kafka_group_name = 'group_name'
    kafka_format = 'data_format'[,]
    [kafka_row_delimiter = 'delimiter_symbol']
    [kafka_schema = '']
    [kafka_num_consumers = N,]
    [kafka_max_block_size = 0]
    [kafka_skip_broken_messages = N,]
    [kafka_commit_every_batch = 0]
```

필수 매개 변수 :

-`kafka_broker_list` - A comma-separated list of brokers (for example,`localhost : 9092`).
-`kafka_topic_list` - A list of Kafka topics.
-`kafka_group_name` - A group of Kafka consumers. Reading margins are tracked for each group separately. If you do not want messages to be duplicated in the cluster, use the same group name everywhere.
-`kafka_format` - Message format. Uses the same notation as the SQL`FORMAT` 기능과 같은`JSONEachRow`. 자세한 내용은을 참조하십시오 형식 (../../../ interfaces /formats.md) 섹션

어떤 변수 :

-`kafka_row_delimiter` - Delimiter character, which ends the message.
-`kafka_schema` - Parameter that must be used if the format requires a schema definition. For example, Cap'N Proto (https://capnproto.org/) 스키마 파일의 경로 및 경로 이름이 필요합니다` schema.capnp : Message` 개체
-`kafka_num_consumers` - The number of consumers per table : Default :`1`. 지정 앞으로도 많은 소비자의 경우 처리량의 소비가 부족합니다. 총 소비자를 초과 할 수 없습니다 파티션 수의 문제에서 하나 뿐인 소비자 할당 할 수 있었다.
-`kafka_max_block_size` - 폴링의 최대 배치 크기 (메시지) (기본 :`max_block_size`).
-`kafka_skip_broken_messages` - Kafka message parser tolerance to schema-incompatible messages per block : Default :`0` 만약`kafka_skip_broken_messages = N` 후 엔진이 생략 * N * 해석 할 수없는 Kafka 메시지 (메시지는 데이터의 행과 동일) .
-`kafka_commit_every_batch` 시점마다 소비되고 취급 배치 대신 하나의 커밋 전체를 블록 (기본 :`0`).

예 :

```sql
  CREATE TABLE queue (
    timestamp UInt64,
    level String,
    message String
  ) ENGINE = Kafka ( 'localhost : 9092', 'topic', 'group1', 'JSONEachRow');

  SELECT * FROM queue LIMIT 5;

  CREATE TABLE queue2 (
    timestamp UInt64,
    level String,
    message String
  ) ENGINE = Kafka SETTINGS kafka_broker_list = 'localhost : 9092'
                            kafka_topic_list = 'topic'
                            kafka_group_name = 'group1'
                            kafka_format = 'JSONEachRow'
                            kafka_num_consumers = 4;

  CREATE TABLE queue2 (
    timestamp UInt64,
    level String,
    message String
  ) ENGINE = Kafka ( 'localhost : 9092', 'topic', 'group1')
              SETTINGS kafka_format = 'JSONEachRow'
                       kafka_num_consumers = 4;
```

<details markdown = "1">

<summary> 추천되고 있지 않습니다 법률 테이블을 만들 </ summary>

!!! attention "주의"
    용도로는 사용하지 마십시오 방법으로 새 ​​프로젝트 가능하면 이전 프로젝트를 위의 방법으로 전환합니다.

```sql
Kafka (kafka_broker_list, kafka_topic_list, kafka_group_name, kafka_format
      [, kafka_row_delimiter, kafka_schema, kafka_num_consumers, kafka_skip_broken_messages)
```

</ details>

## 설명 {#description}

전달 된 메시지는 자동으로 추적에서 각각의 메시지 그룹에서 계산합니다. 데이터를 두번 취득하려면 다른 그룹 이름을 가진 테이블의 복사본을 만듭니다.

그룹은 유연하고 클러스터에서 동기화됩니다. 예를 들어 10 테마의 5 권의 테이블에 클러스터에서는 그 사본을 취득 2입니다. 매수가 변경되면 항목은 자동으로 복사간에 재배포됩니다. 더 읽기 것으로 http://kafka.apache.org/intro.

`SELECT` 특히 도움이 메시지를 읽고 (비 디버그)는 각각의 메시지에서만 읽을 수 있습니다. 에서는보다 실질적인 창출 순간 스레드를 제공합니다. 이렇게하려면 :

1. 엔진을 사용하여 Kafka 소비자를 만들고 데이터 스트림으로 간주합니다.
2. 원하는 구조를 가진 테이블을 만듭니다.
3. 엔진에서 데이터를 변환하여 이전에 만든 테이블에 저장할 실현보기를 만듭니다.

때`MATERIALIZED VIEW` 입력 엔진에서 데이터를 수집하고보고 할 수 있습니다. 그러면 kafka의 메시지를 지속적으로 받고, 필요한 형식으로 변환 할 수 있습니다`SELECT`.
하나의 카프카 테이블은 당신이 원하는만큼의 실현 전망을 가질 수 그들이 직접 카프카 테이블에서 데이터를 읽지 마라

예 :

```sql
  CREATE TABLE queue (
    timestamp UInt64,
    level String,
    message String
  ) ENGINE = Kafka ( 'localhost : 9092', 'topic', 'group1', 'JSONEachRow');

  CREATE TABLE daily (
    day Date,
    level String,
    total UInt64
  ) ENGINE = SummingMergeTree (day (day, level), 8192);

  CREATE MATERIALIZED VIEW consumer TO daily
    AS SELECT toDate (toDateTime (timestamp)) AS day, level, count () as total
    FROM queue GROUP BY day, level;

  SELECT level, sum (total) FROM daily GROUP BY level;
```

성능을 향상시키기 위해 수신 된 메시지는 max_insert_block_size (../../../ operations / server-configuration-parameters / settings.md # settings-max_insert_block_size) 블록이 내부에 형성되어 있지 않은 경우 [stream_flush_interval_ms (../../../ operations / server-configuration-parameters / settings.md) 밀리 초 데이터는 관계없이 블록의 무결성 테이블에 플래시됩니다.

요청을받은 화제의 데이터 변경 변환 로직 분리를 실현보기 :

```sql
  DETACH TABLE consumer;
  ATTACH TABLE consumer;
```

를 사용하여 대상 테이블을 변경하는 경우`ALTER` 자료보기를 해제하면 대상 테이블 및 뷰의 데이터 사이의 불일치를 피할 수 있습니다.

## 설정 {#configuration}

GraphiteMergeTree와 마찬가지로 Kafka 엔진은 ClickHouse 설정 파일을 사용하여 확장 구성을 지원합니다. 사용할 수있는 설정 키는 다음 두입니다. (`kafka`)와 항목 수준 (`kafka_ *`) 먼저 글로벌 구성이 적용되어 다음 항목 수준의 구성이 적용됩니다 (있는 경우).

```xml
  <! - Global configuration options for all tables of Kafka engine type ->
  <kafka>
    <debug> cgrp </ debug>
    <auto_offset_reset> smallest </ auto_offset_reset>
  </ kafka>

  <! - Configuration specific for topic "logs"->
  <kafka_logs>
    <retry_backoff_ms> 250 </ retry_backoff_ms>
    <fetch_min_bytes> 100000 </ fetch_min_bytes>
  </ kafka_logs>
```

가능한 구성 옵션 목록은 [librdkafka 설정 참조 (https://github.com/edenhill/librdkafka/blob/master/CONFIGURATION.md) 밑줄을 사용하는 (`_`) ClickHouse 설정 점 대신. 예를 들어,`check.crcs = true`합니다`<check_crcs> true </ check_crcs>`.

## 가상 열 {# virtual-columns}

-`_topic` - Kafka topic.
-`_key` - Key of the message.
-`_offset` - Offset of the message.
-`_timestamp` - Timestamp of the message.
-`_partition` - Partition of Kafka topic.

** 참고하십시오. **

- 가상 열 (../ index.md # table_engines-virtual_columns)

원본 기사 (https://clickhouse.tech/docs/en/operations/table_engines/kafka/) <! - hide ->
