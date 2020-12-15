--- 
machine_translated : true 
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd 
toc_priority : 37 
toc_title : "\ u30D5 \ u30A1 \ u30A4 \ u30EB" 
--- 

# 파일 {# table_engines-file} 

파일에 테이블 엔진의 데이터 파일을 사용하거나 [파일 
형식 (../../../ interfaces / formats.md # formats) (TabSeparated, Native 등). 

예 : 

- ClickHouse에서 파일에 데이터 내보내기. 
- 한 형식에서 다른 형식으로 데이터를 변환합니다. 
- 데이터 업데이트 ClickHouse 통해 편집 파일 디스크. 

## ClickHouse 서버에서 사용 {# usage-in-clickhouse-server} 

```sql 
File (Format) 
``` 

그`Format` 매개 변수를 지정하거나 파일의 파일 형식 실행하려면  
`SELECT` 이 형식은 입력과 실행을 위해 지원해야합니다
`INSERT` queries - for output. the available formats are listed in the
[형식] (../../../ interfaces / formats.md # formats) 섹션 

ClickHouse는 파일 시스템의 경로를`File`.에 정의 된 폴더를 사용합니다 경로 (../. ./../operations/server-configuration-parameters/settings.md) 서버 구성 설정. 

를 사용하여 테이블을 만들 때`File (Format)`에서 빈 하위 디렉토리 및 폴더에 정리 하였다. 데이터가 테이블에 기록 된`data.Format` 그 서브 

문의 수동으로 작성이 하위 폴더 나 파일 서버의 파일 시스템으로 [ATTACH (../../../ sql-reference / statements / misc. md) 일치하는 이름으로 정보를 나타내므로, 그 파일에서 데이터를 조회 할 수 있습니다. 

!!! warning "경고" 
    ClickHouse 그런 파일에 대한 외부의 변경 사항을 추적하지 않기 때문에이 기능에주의하십시오. ClickHouse과 ClickHouse 외부에서 동시 쓰기의 결과는 정의되지 않습니다. 

** 예 ** 

** 1 ** 설치`file_engine_table` 테이블 : 
 
```sql
CREATE TABLE file_engine_table (name String, value UInt32) ENGINE = File (TabSeparated) 
```
 
기본적으로 ClickHouse 폴더를 만듭니다`/ var / lib / clickhouse / data / default / file_engine_table`. 

** 2 ** 수동으로 작성`/ var / lib / clickhouse / data / default / file_engine_table / data.TabSeparated` 포함 : 

```bash 
$ cat data.TabSeparated 
one 1 
two 2 
``` 

** 3 ** 데이터 조회 : 

```sql 
SELECT * FROM file_engine_table 
``` 

```text 
┌─name─┬─value─┐ 
│ one │ 1 │ 
│ two │ 2 │ 
└──────┴───────┘ 
``` 

## ClickHouse 에서의 사용 - 로컬 {# usage-in-clickhouse-local}
 
에서 [통 차 도구 조카 "도구 채권 쯔つ케 (../../../ operations / utilities / clickhouse-local.md) 파일 엔진은 다음 이외에`Format`. 기본 입출력 스트림 다음과 같은 숫자 또는 인간이 읽을 이름으로 지정할 수 있습니다`0` 또는`stdin``1` 또는`stdout`. 
** 예 **
 
```bash 
$ echo -e "1,2 \ n3,4" | clickhouse-local -q "CREATE TABLE table (a Int64, b Int64) ENGINE = File (CSV, stdin); SELECT a, b FROM table; DROP TABLE table" 
``` 

## 실시 내용 {# details-of- implementation} 

- 여러`SELECT` 쿼리는 동시에 실행할 수 있지만`INSERT` 쿼리는 서로 기다립니다. 
- 새로운 파일을 생성하는`INSERT` 쿼리 
- 파일이 존재하는 경우,`INSERT` 새로운 값을 추가 합니다. 
- 해당하지 않습니다 : 
    -`ALTER` 
    -`SELECT ... SAMPLE` 
    - 지수 
    - 복제 

원본 기사 (https://clickhouse.tech/docs/en/operations/table_engines/file/ ) <! - hide ->
