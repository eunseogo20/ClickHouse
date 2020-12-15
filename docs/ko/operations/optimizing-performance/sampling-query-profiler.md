---
machine_translated : true
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd
toc_priority : 54
toc_title : "Query \ u30D7 \ u30ED \ u30D5 \ u30A1 \ u30A4 \ u30EA \ u30F3 \ u30B0"
---

# 샘플링 크로 파일러 {# sampling-query-profiler}

ClickHouse 운전 샘플링 프로파일 러의 분석 쿼리를 실행합니다. Profiler를 사용하면 쿼리 실행 중에 가장 자주 사용되는 소스 코드 루틴을 찾을 수 있습니다. CPU 시간과 대기 시간을 포함 벽시계 시간을 추적 할 수 있습니다.

Profiler를 사용하려면 :

- 설치 [trace_log] (../ server-configuration-parameters / settings.md # server_configuration_parameters-trace_log) 서버 구성 섹션.

    이 섹션에서는 [trace_log (../../ operations / system-tables.md # system_tables-trace_log) 프로파일 기능의 결과를 포함하는 시스템 테이블. 이것은 기본적으로 구성되어 있습니다. 이 테이블의 데이터는 실행중인 서버에만 유효합니다. 후 서버를 다시 시작 ClickHouse없는 깨끗한 테이블에 저장된 가상 메모리 주소를 사용할 수 있습니다.

- 설치 [query_profiler_cpu_time_period_ns] (../ settings / settings.md # query_profiler_cpu_time_period_ns) 또는 query_profiler_real_time_period_ns] (../ settings / settings.md # query_profiler_real_time_period_ns) 설정. 설정을 모두 동시에 사용할 수 있습니다.

    이러한 설정을 허용하는 설정 프로파일 러 타이머 이들은 세션 설정이기 때문에 서버 전체 개별 사용자 또는 사용자 프로필 대화식 세션 및 개별 쿼리마다 다른 샘플링 주파수

기본 샘플링 주파수는 샘플과 CPU, 리얼 타이머가 활성화되어 있습니다. 이 주파수에 따라 ClickHouse cluster에 대한 충분한 정보를 수집 할 수 있습니다. 동시에이 자주 작업 할 때 profiler는 ClickHouse server의 성능에 영향을주지 않습니다. 이 필요한 경우 프로파일마다 별도의 쿼리를 이용하도록하고 높은 샘플링 주파수입니다.

분석에는`trace_log` 시스템 테이블 :

- 설치`clickhouse-common-static-dbg` 패키지. 더보기 [DEB 패키지 (../../ getting-started / install.md # install-from-deb-packages).

-에 의한 반성 함수를 허용한다. [allow_introspection_functions] (../ settings / settings.md # settings-allow_introspection_functions) 설정.

    보안상의 이유로, introspection 기능은 기본적으로 비활성화되어 있습니다.

- 사용하는`addressToLine``addressToSymbol`와`demangle` [내관 함수 (../../ sql-reference / functions / introspection.md) ClickHouse 코드에서 함수 이름과 위치를 얻으려면. 일부 쿼리의 프로필을 얻으려면`trace_log` 테이블. 개별 함수 또는 스택 트레이스 전체에서 데이터를 집계 할 수 있습니다.

시각화 할 필요가있는 경우`trace_log` 정보, 시도 [후라메구라후 (../../ interfaces / third-party / gui / # clickhouse-flamegraph) 및 속도 범위 (https://github.com / laplab / clickhouse-speedscope).

## 예 {#example}

이 예제에서는 :

- 필터링`trace_log` 쿼리 식별자와 현재의 날짜 별 데이터.

- 스택 트레이스에 의한 집계.

- 반성 함수를 사용하여, 우리는 보고서를 가져옵니다 :

    - 기호와 해당 소스 코드 함수의 이름입니다.
    -이 함수의 소스 코드 위치.

<! - ->

```sql
SELECT
    count ()
    arrayStringConcat (arrayMap (x -> concat (demangle (addressToSymbol (x)) '\ n', addressToLine (x)), trace), '\ n') AS sym
FROM system.trace_log
WHERE (query_id = 'ebca3574-ad0a-400a-9cbc-dca382f5998c') AND (event_date = today ())
GROUP BY trace
ORDER BY count () DESC
LIMIT 10
```

```text
{% include "examples / sampling_query_profiler_result.txt"%}
```
