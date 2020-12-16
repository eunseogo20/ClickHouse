---
machine_translated : true
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd
toc_priority : 37
toc_title : SYSTEM
---

# 시스템 쿼리 {# query-language-system}

- [RELOAD DICTIONARIES (# query_language-system-reload-dictionaries)
- [RELOAD DICTIONARY (# query_language-system-reload-dictionary)
- [DROP DNS CACHE] (# query_language-system-drop-dns-cache)
- [DROP MARK CACHE] (# query_language-system-drop-mark-cache)
- [FLUSH LOGS (# query_language-system-flush_logs)
- [RELOAD CONFIG] (# query_language-system-reload-config)
- [SHUTDOWN] (# query_language-system-shutdown)
- [KILL (# query_language-system-kill)
- [STOP DISTRIBUTED SENDS] (# query_language-system-stop-distributed-sends)
- [FLUSH DISTRIBUTED (# query_language-system-flush-distributed)
- [START DISTRIBUTED SENDS] (# query_language-system-start-distributed-sends)
- [STOP MERGES (# query_language-system-stop-merges)
- [START MERGES (# query_language-system-start-merges)

## RELOAD DICTIONARIES {# query_language-system-reload-dictionaries}

이전에 성공적으로로드 된 모든 사전을 다시로드합니다.
기본적으로 사전은 지연로드됩니다 [dictionaries_lazy_load (../../ operations / server-configuration-parameters / settings.md # server_configuration_parameters-dictionaries_lazy_load)) 따라서 시작할 때 자동으로로드되는 것이 없이 dictGet 함수를 사용하여 첫 번째 액세스시에 초기화되거나 ENGINE = Dictionary를 사용하여 테이블에서 선택됩니다. 그`SYSTEM RELOAD DICTIONARIES` 쿠에바 등의 사전 (로드).
항상 돌아갑니다`Ok.` 사전 업데이트 결과에 상관없이.

## 사전 Dictionary_name를 다시로드 {# query_language-system-reload-dictionary}

사전을 완전히 다시로드합니다`dictionary_name` 사전의 상태에 관계없이 (LOADED / NOT_LOADED / FAILED).
항상 돌아갑니다`Ok.` 사전 업데이트 결과에 상관없이.
사전의 상태는`system.dictionaries` 테이블.

```sql
SELECT name, status FROM system.dictionaries;
```

## DROP DNS CACHE {# query_language-system-drop-dns-cache}

ClickHouse의 내부 DNS 캐시를 재설정합니다. 경우에 따라서는 (이전 ClickHouse 버전의 경우) 인프라를 변경할 때 (다른 ClickHouse 서버 또는 사전에서 사용되는 서버의 IP 주소를 변경

보다 편리한 (자동) 캐시 관리에 대해서는 "disable_internal_dns_cache, dns_cache_update_period 매개 변수"를 참조하십시오.

## DROP MARK CACHE {# query_language-system-drop-mark-cache}

재설정을 표시합니다. ClickHouse 및 성능 시험의 개발에 사용된다.

## FLUSH LOGS {# query_language-system-flush_logs}

Flushes buffers of log messages to system tables (eg system.query_log) Allows you to not wait 7.5 seconds when debugging.

## RELOAD CONFIG {# query_language-system-reload-config}

ClickHouse 구성을 다시로드합니다. 설정이 Zookeeper에 저장되어있는 경우에 사용됩니다.

## SHUTDOWN {# query_language-system-shutdown}

일반적으로 ClickHouse를 종료합니다`service clickhouse-server stop` /`kill {$ pid_clickhouse-server}`)

## KILL {# query_language-system-kill}

클릭 하우스 프로세스를 중지합니다`kill -9 {$ pid_clickhouse-server}`)

## 분산 테이블 관리 {# query-language-system-distributed}

ClickHouse 관리 할 수 ​​있습니다 분산 (../../ engines / table-engines / special / distributed.md) 테이블 사용자가이 테이블에 데이터를 삽입하면 ClickHouse 먼저 클러스터 노드로 전송하는 데이터 큐 를 만들고 그것을 비동기 적으로 보냅니다. 대기열 처리를 관리하려면 STOP DISTRIBUTED SENDS] (# query_language-system-stop-distributed-sends), FLUSH DISTRIBUTED (# query_language-system-flush-distributed), and [START DISTRIBUTED SENDS] (# query_language- system-start-distributed-sends) 쿼리. 분산 데이터를 동기화하여 삽입 할 수 있습니다. `insert_distributed_sync` 설정.

### STOP DISTRIBUTED SENDS {# query_language-system-stop-distributed-sends}

을 해제 한 배경 데이터의 분포를 삽입 할 때 데이터를 배포합니다.

```sql
SYSTEM STOP DISTRIBUTED SENDS [db] <distributed_table_name>
```

### FLUSH DISTRIBUTED {# query_language-system-flush-distributed}

ClickHouse가 강제로 클러스터 노드에 데이터를 동 기적으로 보냅니다. 사용할 수없는 노드가있는 경우 ClickHouse는 예외를 슬로우 쿼리의 실행을 중지합니다. 이것은 모든 노드가 온라인으로 돌아 왔을 때 발생합니다.

```sql
SYSTEM FLUSH DISTRIBUTED [db] <distributed_table_name>
```

### START DISTRIBUTED SENDS {# query_language-system-start-distributed-sends}

배경 데이터의 분포를 삽입 할 때 데이터를 배포합니다.

```sql
SYSTEM START DISTRIBUTED SENDS [db] <distributed_table_name>
```

### STOP MERGES {# query_language-system-stop-merges}

MergeTree 패밀리 테이블의 배경 병합을 중지 할 수 있습니다 :

```sql
SYSTEM STOP MERGES [db] merge_tree_family_table_name]
```

!!! note "주"
    `DETACH / ATTACH` 이전에 모든 MergeTree 테이블에 병합이 정지 된 경우에도 table은 테이블의 배경 병합을 시작합니다.

### START MERGES {# query_language-system-start-merges}

MergeTree 패밀리 테이블의 배경 병합을 시작할 수 있습니다 :

```sql
SYSTEM START MERGES [db] merge_tree_family_table_name]
```

원본 기사 (https://clickhouse.tech/docs/en/query_language/system/) <! - hide ->
