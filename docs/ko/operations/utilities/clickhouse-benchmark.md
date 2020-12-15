--- 
machine_translated : true 
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd 
toc_priority : 61 
toc_title : "clickhouse- \ u30D9 \ u30F3 \ u30C1 \ u30DE \ u30FC \ u30AF" 
--- 

# clickhouse- 벤치 마크 {# clickhouse-benchmark} 

ClickHouse 서버에 연결하여 지정 쿼리를 반복적으로 전송합니다. 

구문 : 

```bash 
$ echo "single query"| clickhouse-benchmark [keys] 
``` 

또는 

```bash 
$ clickhouse-benchmark [keys] <<< "single query" 
``` 

일련의 쿼리를 보낼 당신은 텍스트 파일을 만들고 각 쿼리를이 파일의 각 문자열에 배치합니다. 예 : 

```sql 
SELECT * FROM system.numbers LIMIT 10000000 
SELECT 1 
``` 

다음이 파일을 표준 입력으로 전달합니다. `clickhouse-benchmark`.

```bash 
clickhouse-benchmark [keys] <queries_file 
``` 

## 키 {# clickhouse-benchmark-keys} 

-`-c N``--concurrency = N` - Number of queries that`clickhouse-benchmark` 동시에 보냅니다. 기본값 : 1. 
-`-d N``--delay = N` - Interval in seconds between intermediate reports (set 0 to disable reports). Default value : 1. 
-`-h WORD``--host = WORD` - Server host. Default value :`localhost`. 위해 비교 모드 (# clickhouse-benchmark-comparison-mode) 이상을 사용할 수 있습니다`-h` 열쇠 다 
-`-p N``--port = N `- Server port. Default value : 9000. For the 비교 모드 (# clickhouse-benchmark-comparison-mode) 이상을 사용할 수 있습니다`-p` 열쇠 다 
-`-s``--secure` - Using TLS connection. 
-`-i N``--iterations = N `- Total number of queries. Default value : 0.
-`-r``--randomize` - Random order of queries execution if there is more then one input query . 
-`-t N``--timelimit = N` - Time limit in seconds`clickhouse-benchmark `지정된 시간 제한에 도달하면 쿼리 전송을 중지합니다. 기본값 : 0 (제한 시간 해제). 
-`--confidence = N` - Level of confidence for T-test. Possible values : 0 (80 %), 1 (90 %), 2 (95 %), 3 (98 %), 4 (99 %), 5 (99.5 %). Default value : 5. In the 비교 모드 (# clickhouse-benchmark-comparison-mode)`clickhouse-benchmark`을 실행합니다. [독립적 인 두 샘플 학생 t 테스트 (https://en.wikipedia.org/wiki/Student%27s_t-test#Independent_two-sample_t-test)이 분포가 선택된 신뢰도와 다르지 않다 여부 를 판정합니다. 
-`--cumulative` - Printing cumulative data instead of data per interval. 
-`--database = DATABASE_NAME` - ClickHouse database name. Default value :`default`.
-`--json = FILEPATH` - JSON output. When the key is set`clickhouse-benchmark` 지정된 JSON 파일에 보고서를 출력합니다. 
-`--user = USERNAME` - ClickHouse user name. Default value :`default`.
-`--password = PSWD` - ClickHouse user password. Default value : empty string. 
-`--stacktrace` - Stack traces output. When the key is set`clickhouse-bencmark` 출력 스택 추적 예외를 throw했습니다 . 
-`--stage = WORD` - Query processing stage at server. ClickHouse stops query processing and returns answer to`clickhouse-benchmark` 지정된 단계에서. 가능한 값 :`complete``fetch_columns``with_mergeable_state` 기본값 :`complete`. 
-`--help` - Shows the help message. 

당신은 몇 가지를 적용하려면 [설정] (../ ../operations/settings/index.md) 쿼리의 경우 키로 전달`- <session setting name> = SETTING_VALUE` 예`--max_memory_usage = 1048576`. 

## 출력 {# clickhouse-benchmark -output} 

기본적으로`clickhouse-benchmark` 각 보고서`--delay` 간격 

보고서의 예 : 

```text
Queries executed : 10. 

localhost : 9000 queries 10, QPS : 6.772, RPS : 67904487.440, MiB / s : 518.070, result RPS : 67721584.984, result MiB / s : 516.675. 

0.000 % 0.145 sec. 
10.000 % 0.146 sec. 
20.000 % 0.146 sec. 
30.000 % 0.146 sec. 
40.000 % 0.147 sec. 
50.000 % 0.148 sec. 
60.000 % 0.148 sec. 
70.000 % 0.148 sec. 
80.000 % 0.149 sec. 
90.000 % 0.150 sec. 
95.000 % 0.150 sec. 
99.000 % 0.150 sec. 
99.900 % 0.150 sec. 
99.990 % 0.150 sec. 
``` 

이 보고서는 : 

- 쿼리 수`Queries executed :`필드 

- 상태 문자열을 포함 (순서대로) : 

    - ClickHouse 서버의 엔드 포인트.
    - 처리 된 쿼리 수. 
    - QPS : QPS :에서 지정된 기간에 초당 실행되는 쿼리 서버 수`--delay` 인수. 
    - RPS : 서버가 지정된 기간에 초당 읽은 행 수`--delay` 인수. 
    - MiB / s : 지정된 기간에 초당 읽은 mebibytes 서버의 수`--delay` 인수. 
    - 결과 RPS : 서버에 의해 지정된 기간 내에 쿼리의 결과에 대해 초당 얼마나 많은 행이 배치되어 있습니까`--delay` 인수. 
    - 쿼리의 결과에 대해 서버에 의해 지정된 기간 내에 초당 메비 바이트 수`--delay` 인수. 

- 쿼리 실행 시간 백분위 수. 

## 비교 모드 {# clickhouse-benchmark-comparison-mode} 

`clickhouse-benchmark` 통 차 도구 조카 "통"도구 채권 츠 즈ュ도구 하나 개 

비교 모드를 사용하려면 두 서버의 끝점 를`--host``--port` 열쇠 다 인수 목록의 위치와 일치하는 키는 최초의`--host` 첫 번째 것과 일치합니다`--port` 등. `clickhouse-benchmark` 두 서버에 연결하고 쿼리를 보냅니다. 무작위로 선택된 서버에 보낼 각 쿼리. 결과는 각 서버마다 개별적으로 표시됩니다. 

## 예 {# clickhouse-benchmark-example}

```bash 
$ echo "SELECT * FROM system.numbers LIMIT 10000000 OFFSET 10000000"| clickhouse-benchmark -i 10 
``` 

```text 
Loaded 1 queries. 

Queries executed : 6. 

localhost : 9000 queries 6 QPS : 6.153, RPS : 123398340.957, MiB / s : 941.455, result RPS : 61532982.200, result MiB / s : 469.459. 

0.000 % 0.159 sec. 
10.000 % 0.159 sec. 
20.000 % 0.159 sec. 
30.000 % 0.160 sec. 
40.000 % 0.160 sec. 
50.000 % 0.162 sec. 
60.000 % 0.164 sec. 
70.000 % 0.165 sec. 
80.000 % 0.166 sec. 
90.000 % 0.166 sec. 
95.000 % 0.167 sec. 
99.000 % 0.167 sec. 
99.900 % 0.167 sec.
99.990 % 0.167 sec.


 
Queries executed : 10.

localhost : 9000 queries 10, QPS : 6.082, RPS : 121959604.568, MiB / s : 930.478, result RPS : 60815551.642, result MiB / s : 463.986. 

0.000 % 0.159 sec. 
10.000 % 0.159 sec. 
20.000 % 0.160 sec. 
30.000 % 0.163 sec. 
40.000 % 0.164 sec. 
50.000 % 0.165 sec. 
60.000 % 0.166 sec. 
70.000 % 0.166 sec. 
80.000 % 0.167 sec. 
90.000 % 0.167 sec. 
95.000 % 0.170 sec. 
99.000 % 0.172 sec. 
99.900 % 0.172 sec. 
99.990 % 0.172 sec. 
```
