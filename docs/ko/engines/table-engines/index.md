---
machine_translated : true
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd
toc_folder_title : "\ u8868 \ u30A8 \ u30F3 \ u30B8 \ u30F3"
toc_priority : 26
toc_title : "\ u306F \ u3058 \ u3081 \ u306B"
---

# 테이블 엔진 {#table_engines}

테이블 엔진 형식 표합니다. :

- 데이터의 저장 방법 및 위치 데이터의 기입 처 및 데이터의 읽기 처.
- 지원되는 쿼리와 방법.
- 동시 데이터 액세스.
- 인덱스가 존재하는 경우에 사용합니다.
- 멀티 스레드 요청 실행이 가능한지.
- 데이터 복제 매개 변수.

## 엔진 가족 {# engine-families}

### 메루게쯔리 {#mergetree}

고부하 작업을위한 가장 보편적 인 기능 테이블 엔진. 본 물건의 공유에 의한 이러한 엔진은 신속하게 데이터를 삽입 및 후속 백그라운드 데이터를 처리합니다. `MergeTree` 가족 엔진 지원 데이터 복제 (복제 \ * (mergetree-family / replication.md # table_engines-replication) 버전의 엔진) 분할 및 기타 기능에 해당하지 않는 기타 엔진입니다.

가족 엔진 :

- [메루게쯔리 (mergetree-family / mergetree.md # mergetree)
- 바꾸기 병합 트리 (mergetree-family / replacingmergetree.md # replacingmergetree)
- [서밍 마 게임 트리 (mergetree-family / summingmergetree.md # summingmergetree)
- [AggregatingMergeTree (mergetree-family / aggregatingmergetree.md # aggregatingmergetree)
- [축소 병합 트리 (mergetree-family / collapsingmergetree.md # table_engine-collapsingmergetree)
- 버전 관리 코라뿌신구 마 겟 리 (mergetree-family / versionedcollapsingmergetree.md # versionedcollapsingmergetree)
- [GraphiteMergeTree (mergetree-family / graphitemergetree.md # graphitemergetree)

### 로그 {#log}

경량 [엔진] (log-family / index.md) 최소한의 기능을 사용합니다. 많은 작은 테이블 (최대 약 10,000 행)을 신속하게 쓰기, 나중에 전체적으로로드해야하는 경우에 가장 효과적입니다.

가족 엔진 :

- [TinyLog (log-family / tinylog.md # tinylog)
- 스트립 로그 (log-family / stripelog.md # stripelog)
- 로그 (log-family / log.md # log)

### 통합 엔진 {# integration-engines}

엔진 용 응용 프로그램 데이터 저장 및 처리 시스템.

가족 엔진 :

- [카프카] (integrations / kafka.md # kafka)
- [MySQL] (integrations / mysql.md # mysql)
- [ODBC] (integrations / odbc.md # table-engine-odbc)
- [JDBC] (integrations / jdbc.md # table-engine-jdbc)
- HDFS (integrations / hdfs.md # hdfs)

### 특수 엔진 {# special-engines}

가족 엔진 :

- 분산 (special / distributed.md # distributed)
- 실현보기 (special / materializedview.md # materializedview)
- [사전] (special / dictionary.md # dictionary)
- \ Merge \ (special / merge.md # merge
- [파일] (special / file.md # file)
- [Null] (special / null.md # null)
- [세트] (special / set.md # set)
- [참여] (special / join.md # join)
- [URL] (special / url.md # table_engines-url)
- [표시] (special / view.md # table_engines-view)
- [메모리] (special / memory.md # memory)
- 버퍼 (special / buffer.md # buffer)

## 가상 열 {# table_engines-virtual_columns}

가상 열은 엔진의 소스 코드에 정의되어있는 정수 테이블 엔진 특성입니다.

가상 컬럼을 지정해야하지 않습니다. `CREATE TABLE` 당신이 그들을 볼 수 없습니다`SHOW CREATE TABLE`와`DESCRIBE TABLE` 쿼리 결과. 가상 열은 읽기 전용이기 때문에 가상 컬럼에 데이터를 삽입 할 수 없습니다.

가상 열에서 데이터를 선택하려면 가상 열 이름을 지정해야합니다. `SELECT` 쿼리. `SELECT *`가상 열에서 값을 반환하지 않습니다.

테이블 가상 열 중 하나와 같은 이름의 열이있는 테이블을 만들 때 가상 컬럼에 액세스 할 수 없습니다. 이것은 권장하지 않습니다. 충돌을 피하기 위해 가상 열 이름에는 일반적으로 밑줄이 표시됩니다.

원본 기사 (https://clickhouse.tech/docs/en/operations/table_engines/) <! - hide ->
