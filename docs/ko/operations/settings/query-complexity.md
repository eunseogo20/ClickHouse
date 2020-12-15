---
machine_translated : true
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd
toc_priority : 59
toc_title : "\ u30AF \ u30A8 \ u30EA \ u306E \ u8907 \ u96D1 \ u3055 \ u306E \ u5236 \ u9650"
---

# 쿼리 복잡성의 제한 {# restrictions-on-query-complexity}

쿼리의 복잡성에 대한 제한은 설정의 일부입니다.
이들을 더 안전한 실행의 사용자 인터페이스입니다.
거의 모든 제한이 적용됩니다`SELECT` 분산 쿼리 처리는 각 서버에 개별적으로 제한이 적용됩니다.

ClickHouse은 각 행이 아닌 데이터 부분의 한계를 확인합니다. 이것은 데이터 부분의 크기 제한을 초과 할 수 있다는 것을 의미합니다.

제한 "maximum amount of something"값 0을 취할 수 있습니다. "unrestricted"
대부분의 제한은 'overflow_mode'설정 한도를 초과 할 때 무엇을 해야할지를 의미합니다.
의 값 :`throw` 또는`break` 집계 제한 (group_by_overflow_mode)도 값이 있습니다`any`.

`throw` - Throw an exception (default).

`break` - Stop executing the query and return the partial result, as if the source data ran out.

`any (only for group_by_overflow_mode)`- Continuing aggregation for the keys that got into the set, but do not add new keys to the set.

## max_memory_usage {#settings_max_memory_usage}

단일 서버에서 쿼리를 실행하는 데 사용하는 RAM의 최대량.

기본 구성 파일은 최대 10GB입니다.

이 설정은 사용 가능한 메모리의 용량과 컴퓨터의 메모리의 총 용량은 고려되지 않습니다.
이 제한은 단일 서버에서 단일 쿼리에 적용됩니다.
다음을 사용할 수 있습니다`SHOW PROCESSLIST` 각 쿼리의 현재의 메모리 사용량을 확인합니다.
또한 피크 메모리 소비는 각 쿼리에 대해 추적 로그에 기록됩니다.

특정 집계 함수에 대한 내용은 메모리 사용량 모니터링되지 않습니다.

집계 함수의 상태에 대해 메모리 사용량이 완전히 추적되지 않습니다`min``max``any``anyLast``argMin``argMax`에서`String`와`Array` 인수.

메모리 소비도 매개 변수에 의해 제한됩니다`max_memory_usage_for_user`와`max_memory_usage_for_all_queries`.

## max_memory_usage_for_user {# max-memory-usage-for-user}

단일 서버에서 사용자의 쿼리를 실행하는 데 사용하는 RAM의 최대량.

기본값은 설정. h (https://github.com/ClickHouse/ClickHouse/blob/master/src/Core/Settings.h#L288) 기본적으로 금액 제한되지 않습니다 (`max_memory_usage_for_user = 0`).

설명도 참조하십시오 [max_memory_usage (# settings_max_memory_usage).

## max_memory_usage_for_all_queries {# max-memory-usage-for-all-queries}

단일 서버에서 모든 쿼리를 실행하는 데 사용하는 RAM의 최대량.

기본값은 설정. h (https://github.com/ClickHouse/ClickHouse/blob/master/src/Core/Settings.h#L289) 기본적으로 금액 제한되지 않습니다 (`max_memory_usage_for_all_queries = 0`).

설명도 참조하십시오 [max_memory_usage (# settings_max_memory_usage).

## max_rows_to_read {# max-rows-to-read}

다음의 제한은 각 블록 (각 행이 아니라)에서 확인할 수 있습니다. 즉, 제한은 조금 손상 될 수 있습니다.
여러 스레드에서 쿼리를 실행하면 다음과 같은 제한은 각 스레드에 개별적으로 적용됩니다.

쿼리 실행시 테이블에서 읽을 수있는 최대 행수.

## max_bytes_to_read {# max-bytes-to-read}

쿼리 실행시 테이블에서 읽을 수있는 최대 바이트 수 (비 압축 데이터).

## read_overflow_mode {# read-overflow-mode}

로드되는 데이터 양이 어느 한도를 초과 한 경우의 대처 방법 : 'throw'또는 'break'. 기본적으로 throw.

## max_rows_to_group_by {# settings-max-rows-to-group-by}

집계에서받은 고유 키의 최대 수. 이 설정을 사용하면 집계시 메모리 사용량을 제한 할 수 있습니다.

## group_by_overflow_mode {# group-by-overflow-mode}

집계 고유 키의 수가 제한을 초과 한 경우의 대처 방법 : 'throw', 'break'또는 'any'. 기본적으로 throw.
를 사용하여 'any'value는 GROUP BY의 근사치를 실행할 수 있습니다. 이 근사치의 품질은 데이터의 통계적 특성에 따라 달라집니다.

## max_bytes_before_external_group_by {# settings-max_bytes_before_external_group_by}

실행을 활성화하거나 비활성화합니다. `GROUP BY` 외부 메모리에 어구. 보는 외부 메모리 GROUP BY (../../ sql-reference / statements / select / group-by.md # select-group-by-in-external-memory).

가능한 값 :

- 싱글로 사용할 수있는 RAM의 최대 볼륨 (바이트) GROUP BY (../../ sql-reference / statements / select / group-by.md # select-group-by-clause) 작전이다
- 0 -`GROUP BY` 외부 메모리로 사용할 수 없습니다.

기본값은 0입니다.

## max_rows_to_sort {# max-rows-to-sort}

정렬 전의 최대 행 수입니다. 이에 따라 정렬시 메모리 소비를 제한 할 수 있습니다.

## max_bytes_to_sort {# max-bytes-to-sort}

정렬 이전 최대 바이트 수.

## sort_overflow_mode {# sort-overflow-mode}

정렬 전에받은 행수가 어느 한도를 초과 한 경우의 대처 방법 : 'throw'또는 'break'. 기본적으로 throw.

## max_result_rows {# setting-max_result_rows}

결과 행 수를 제한합니다. 또한 체크 서브 쿼리는 windows 응용 프로그램을 실행할 때 부품의 분산을 반환합니다.

## max_result_bytes {# max-result-bytes}

결과의 바이트 수를 제한합니다. 이전 설정과 동일합니다.

## result_overflow_mode {# result-overflow-mode}

결과의 양을 제한 중 하나를 초과하는 경우의 해결 방법 : 'throw'또는 'break'. 기본적으로 throw.

를 사용하여 'break'LIMIT의 사용과 비슷합니다. `Break` 블록 수준에서만 실행을 중단합니다. 이것은 반환되는 행의 양이 [max_result_rows (# setting-max_result_rows)의 배수 [max_block_size (settings.md # setting-max_block_size) 그리고 의존하는 [max_threads (settings.md # settings-max_threads).

예 :

```sql
SET max_threads = 3, max_block_size = 3333;
SET max_result_rows = 3334, result_overflow_mode = 'break';

SELECT *
FROM numbers_mt (100000)
FORMAT Null;
```

결과 :

```text
6666 rows in set ...
```

## max_execution_time {# max-execution-time}

쿼리의 최대 실행 시간을 초 단위로 지정합니다.
현재 정렬 스테이지 중 하나 또는 집계 함수의 병합 및 종료시에는 확인하지 않습니다.

## timeout_overflow_mode {# timeout-overflow-mode}

쿼리가 실행되는 시간보다 긴 경우의 대처 방법 'max_execution_time': 'throw'또는 'break'. 기본적으로 throw.

## min_execution_speed {# min-execution-speed}

초당 행 단위의 최소 실행 속도. 모든 데이터 블록에서 'timeout_before_checking_execution_speed'만료됩니다. 실행 속도가 낮은 경우는 예외가 슬로우됩니다.

## min_execution_speed_bytes {# min-execution-speed-bytes}

초당 최소 실행 바이트 수. 모든 데이터 블록에서 'timeout_before_checking_execution_speed'만료됩니다. 실행 속도가 낮은 경우는 예외가 슬로우됩니다.

## max_execution_speed {# max-execution-speed}

초당 실행 최대 행 수. 모든 데이터 블록에서 'timeout_before_checking_execution_speed'만료됩니다. 실행 속도가 높은 경우는 실행 속도가 느려집니다.

## max_execution_speed_bytes {# max-execution-speed-bytes}

초당 실행 바이트의 최대 값. 모든 데이터 블록에서 'timeout_before_checking_execution_speed'만료됩니다. 실행 속도가 높은 경우는 실행 속도가 느려집니다.

## timeout_before_checking_execution_speed {# timeout-before-checking-execution-speed}

실행 속도가 너무 많이 들어 있지 않은지 확인합니다 'min_execution_speed'), 지정된 시간 (초) 경과 후.

## max_columns_to_read {# max-columns-to-read}

단일 쿼리의 테이블에서 읽을 수있는 열의 최대 수. 쿼리에서 더 많은 열을 읽을 필요가있는 경우는 예외가 슬로우됩니다.

## max_temporary_columns {# max-temporary-columns}

상수 문자열을 포함하는 쿼리를 실행할 때 동시에 RAM에 보관해야하는 임시 열의 최대 수. 이것은보다 많은 임시 열이 있으면 예외가 throw됩니다.

## max_temporary_non_const_columns {# max-temporary-non-const-columns}

같은 것으로 'max_temporary_columns'그러나 상수 열을 세어 않고.
정수열 쿼리를 실행할 때 꽤 자주 형성되지만, 계산 자원은 거의 제로입니다.

## max_subquery_depth {# max-subquery-depth}

하위 쿼리의 최대 중첩 깊이. 하위 쿼리가 깊은 경우는 예외가 슬로우됩니다. 기본적으로 100입니다.

## max_pipeline_depth {# max-pipeline-depth}

최대 파이프 라인 깊이. 쿼리 처리 중에 각 데이터 블록이 처리하는 변환 수에 해당합니다. 단일 서버 내에서 계산됩니다. 파이프 라인의 깊이가 큰 경우는 예외가 슬로우됩니다. 기본적으로 1000입니다.

## max_ast_depth {# max-ast-depth}

쿼리 구문 트리의 최대 중첩 깊이. 초과하면 예외가 throw됩니다.
현재 분석 중에 확인되지 않고 쿼리 분석 후에 만 ​​확인됩니다. 즉, 해석 중에 너무 깊이 구문 트리를 만들 수 있지만 쿼리가 실패합니다. 기본적으로 1000입니다.

## max_ast_elements {# max-ast-elements}

쿼리 구문 트리 내의 요소의 최대 수. 초과하면 예외가 throw됩니다.
이전 설정과 마찬가지로 쿼리를 분석 한 후에 만 ​​확인됩니다. 기본적으로 50,000입니다.

## max_rows_in_set {# max-rows-in-set}

하위 쿼리에서 생성 된 IN 절에서 데이터 - 세트의 최대 행수.

## max_bytes_in_set {# max-bytes-in-set}

하위 쿼리에서 생성 된 IN 절 세트로 사용되는 최대 바이트 수 (비 압축 데이터).

## set_overflow_mode {# set-overflow-mode}

데이터의 양이 어느 한도를 초과 한 경우의 대처 방법 : 'throw'또는 'break'. 기본적으로 throw.

## max_rows_in_distinct {# max-rows-in-distinct}

DISTINCT를 사용하는 경우의 최대 행수.

## max_bytes_in_distinct {# max-bytes-in-distinct}

DISTINCT를 사용할 때 해시 테이블에서 사용되는 최대 바이트 수.

## distinct_overflow_mode {# distinct-overflow-mode}

데이터의 양이 어느 한도를 초과 한 경우의 대처 방법 : 'throw'또는 'break'. 기본적으로 throw.

## max_rows_to_transfer {# max-rows-to-transfer}

글로벌 IN을 사용하면 원격 서버에 전달하거나 임시 테이블에 저장할 수있는 최대 행 수입니다.

## max_bytes_to_transfer {# max-bytes-to-transfer}

글로벌 IN을 사용하면 원격 서버에 전달하거나 임시 테이블에 저장할 수있는 최대 바이트 수 (비 압축 데이터).

## transfer_overflow_mode {# transfer-overflow-mode}

데이터의 양이 어느 한도를 초과 한 경우의 대처 방법 : 'throw'또는 'break'. 기본적으로 throw.

## max_rows_in_join {# settings-max_rows_in_join}

테이블을 조인 할 때 사용되는 해시 테이블의 행 수를 제한합니다.

이 설정은 다음에 적용됩니다 [SELECT ... JOIN (../../ sql-reference / statements / select / join.md # select-join) 조작 및 참여 (../../ engines / table-engines / special / join.md) 테이블 엔진.

쿼리에 여러 조인이 포함되어있는 경우 ClickHouse이 설정에서 중간 결과를 모두 체크합니다.

ClickHouse 제한에 도달하면 다양한 작업을 계속할 수 있습니다. 사용하는 [join_overflow_mode (# settings-join_overflow_mode) 액션을 선택하는 설정.

가능한 값 :

- 양의 정수.
- 0 - Unlimited number of rows.

기본값은 0입니다.

## max_bytes_in_join {# settings-max_bytes_in_join}

제한 크기를 바이트 해시 테이블이 참가합니다.

이 설정은 다음에 적용됩니다 [SELECT ... JOIN (../../ sql-reference / statements / select / join.md # select-join) 조작 및 결합 테이블 엔진 (../../ engines / table-engines / special / join.md).

쿼리에 조인이 포함되어있는 경우 ClickHouse는 중간 결과에 대해이 설정을 확인합니다.

ClickHouse 제한에 도달하면 다양한 작업을 계속할 수 있습니다. 사용 [join_overflow_mode (# settings-join_overflow_mode) 액션을 선택하는 설정.

가능한 값 :

- 양의 정수.
- 0 - Memory control is disabled.

기본값은 0입니다.

## join_overflow_mode {# settings-join_overflow_mode}

다음 중 하나의 결합 제한에 도달하면 ClickHouse가 수행 할 작업을 정의합니다 :

- [max_bytes_in_join (# settings-max_bytes_in_join)
- [max_rows_in_join (# settings-max_rows_in_join)

가능한 값 :

-`THROW` - ClickHouse throws an exception and breaks operation.
-`BREAK` - ClickHouse breaks operation and does not throw an exception.

기본값 :`THROW`.

** 참고하십시오. **

- [JOIN 절 (../../ sql-reference / statements / select / join.md # select-join)
- 조인 테이블 엔진 (../../ engines / table-engines / special / join.md)

## max_partitions_per_insert_block {# max-partitions-per-insert-block}

단일 삽입 블록 파티션의 최대 수를 제한합니다.

- 양의 정수.
- 0 - Unlimited number of partitions.

기본값은 100입니다.

** 자세한 **

를 삽입 할 때 데이터 ClickHouse 계산 파티션 수에 삽입됩니다. 파티션의 수가`max_partitions_per_insert_block`, ClickHouse 다음 텍스트에서 예외를 throw합니다 :

> "Too many partitions for single INSERT block (more than"+ toString (max_parts) + "). The limit is controlled by 'max_partitions_per_insert_block'setting. A large number of partitions is a common misconception. It will lead to severe negative performance impact , including slow server startup, slow INSERT queries and slow SELECT queries. Recommended total number of partitions for a table is under 1000..10000. Please note, that partitioning is not intended to speed up SELECT queries (ORDER BY key is sufficient to make range queries fast) Partitions are intended for data manipulation (DROP PARTITION, etc). "

원본 기사 (https://clickhouse.tech/docs/en/operations/settings/query_complexity/) <! - hide ->
