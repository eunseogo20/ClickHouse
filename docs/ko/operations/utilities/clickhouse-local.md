--- 
machine_translated : true 
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd 
toc_priority : 60 
toc_title : "\ uFF82 \ u3064 \ uFF68 \ uFF82 \ u59EA \"\ uFF82 \ u50B5 \ uFF82 \ u3064 \ uFF79 " 
--- 

# 통 차 도구 조카"도구 채권 쯔つ케 {# clickhouse-local} 

그`clickhouse-local` 프로그램으로 실시 고속 처리가 현지의 파일 열기에 맞게 구성되어 있습니다 clickHouse 서버입니다. 

데이터를 수용 나타내는 테이블 쿼리를 이용하여 클릭 하우스 SQL 방언 (../../ sql-reference / index.md) 

`clickhouse-local` ClickHouse server와 같은 코어를 사용하기 때문에 대부분 기능과 동일한 형식과 테이블 엔진 세트를 지원합니다. 

기본적으로`clickhouse-local` 액세스 데이터를 동일한 호스트에 대응하여 탑재 서버 설정을 사용`--config-file` 인수. 

!!! warning "경고" 
    프로덕션 서버의 구성을로드하는 것은 권장하지 않습니다`clickhouse-local` 휴먼 에러의 경우 데이터가 손상 될 수 있기 때문에. 

## 사용법 {#usage} 

기본 사용법 :

```bash 
$ clickhouse-local --structure "table_structure"--input-format "format_of_incoming_data"-q "query" 
``` 

인수 : 

-`-S``--structure` - table structure for input data. 
-`-if``--input-format` - input format`TSV` 기본적으로. 
-`-f``--file` - path to data`stdin` 기본적으로. 
-`-q``--query` - queries to execute with`;`델리 미터로. 
-`-N``--table` - table name where to put output data`table` 기본적으로. 
-`-of``--format``--output-format` - output format`TSV` 기본적으로. 
-`--stacktrace` - whether to dump debug output in case of exception . 
-`--verbose` - more details on query execution. 
-`-s` - disables`stderr` 로깅
-`--config-file` - path to configuration file in same format as for ClickHouse server , by default the configuration empty. 
-`--help` - arguments references for`clickhouse-local`. 

또한 ClickHouse 설정 변수에 대해 인수가 있습니다. `--config-file`. 

## 예 {#examples} 

```bash 
$ echo -e "1,2 \ n3,4"| clickhouse-local -S "a Int64, b Int64"-if "CSV" -q "SELECT * FROM table" 
Read 2 rows, 32.00 B in 0.000 sec., 5182 rows / sec., 80.97 KiB / sec. 
1 2 
3 4 
``` 

이전 예제와 동일합니다 : 

```bash 
$ echo -e "1,2 \ n3,4"| clickhouse-local -q "CREATE tABLE table (a Int64, b Int64) ENGINE = File (CSV, stdin); SELECT a, b FROM table; DROP tABLE table" 
Read 2 rows, 32.00 B in 0.000 sec.,

그런 다음 각 Unix 유저 메모리 사용을 출력하자 : 

```bash 
$ ps aux | tail -n +2 | awk '{printf ( "% s \ t % s \ n", $ 1, $ 4)}' | clickhouse-local -S "user String, mem Float64"-q "SELECT user round (sum (mem), 2) as memTotal FROM table GROUP BY user ORDER BY memTotal DESC FORMAT Pretty " 
``` 

```text 
Read 186 rows 4.15 KiB in 0.035 sec., 5302 rows / sec., 118.34 KiB / sec. 
┏━━━━━━━━━━┳━━━━━━━━━━┓ 
┃ user ┃ memTotal ┃ 
┡ ━━━━━━━━━━╇━━━━━━━━━━┩ 
│ bayonet │ 113.5 │ 
├──────────┼──────────┤ 
│ root │ 8.8 │ 
├──────────┼──────────┤ 
... 
``` 

원래 기사 (https://clickhouse.tech/docs/ en / operations / utils / clickhouse-local /) <! - hide ->
