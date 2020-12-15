---
machine_translated : true
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd
toc_folder_title : "\ u30ED \ u30B0 \ u30D5 \ u30A1 \ u30DF \ u30EA"
toc_priority : 29
toc_title : "\ u306F \ u3058 \ u3081 \ u306B"
---

# 로그 엔진 가족 {# log-engine-family}

이 엔진은 많은 작은 테이블 (최대 10,000 행)을 신속하게 쓰기, 나중에 전체적으로로드해야하는 시나리오 용으로 개발되었습니다.

가족 엔진 :

- 스트립 로그 (stripelog.md)
- 로그 (log.md)
- [TinyLog (tinylog.md)

## 공통 속성 {# common-properties}

엔진 :

- 디스크에 데이터를 저장합니다.

- 쓸 때 파일의 끝에 데이터를 추가합니다.

- 동시 데이터 액세스 지원 잠금.

    중`INSERT` 쿼리 테이블이 잠겨 다른 질문을 읽기 및 쓰기 데이터의 두 테이블을 만드는 데이터 쓰기 쿼리가없는 경우에는 임의의 수의 데이터 읽기 쿼리를 동시에 실행할 수 있습니다.

- 지원하지 않는 [돌연변이] (../../../ sql-reference / statements / alter.md # alter-mutations) 작전이다

- 색인을 지원하지 않습니다.

    즉`SELECT` 데이터 범위 질의가 효율적으로하지 않습니다.

- 작성하지는 않습니다 데이터를 원자 적으로.

    취득 할 수있는 테이블 데이터가 손상된 경우도 찢어 쓰기 작업은 예를 들어, 이상 서버를 종료했습니다.

## 차이 {#differences}

그`TinyLog` 엔진은 가족의 가장 간단하고 가장 나쁜 기능성 및 가장 낮은 효율을 제공한다. 그`TinyLog` 엔진을 지원하지 않는 병렬 데이터의 읽기 의한 다중 스레드). 로 데이터를로드하는 대신 각 엔진의 가족을 지원하는 병렬 読みで의 사용이 거의 동일했다 기술자로서의`Log` 엔진은 각 열을 별도의 파일로 저장할 수 있습니다. 간단한 낮은 에너지 시나리오에서 사용합니다.

그`Log`와`StripeLog` 엔진 지원 병렬 데이터입니다. 로드 할 때 데이터 ClickHouse 사용 다중 스레드). 각 스레드 프로세스 별 데이터 블록입니다. 그`Log` 엔진은 테이블의 각 열에 대해 별도의 파일을 사용합니다. `StripeLog` 모든 데이터 파일입니다. 그 결과`StripeLog` 엔진은 운영`Log` 엔진은 데이터를 읽을 때 고성능을 제공한다.

원본 기사 (https://clickhouse.tech/docs/en/operations/table_engines/log_family/) <! - hide ->
