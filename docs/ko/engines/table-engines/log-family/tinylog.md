---
machine_translated : true
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd
toc_priority : 34
toc_title : TinyLog
---

# TinyLog {#tinylog}

엔진 로그 엔진 제품군에 속합니다. 볼 로그 엔진 가족 (index.md) 로그 엔진의 공통 속성과 그 차이.

이 테이블 엔진은 일반적으로 write-once 메소드에서 사용됩니다. 예를 들어, 다음과 같이합니다`TinyLog`- 작은 ​​일괄 처리되는 중간 데이터 테이블을 입력합니다. 다수의 작은 테이블에 데이터를 저장하는 것은 비효율적입니다.

쿼리는 단일 스트림으로 실행됩니다. 즉,이 엔진은 상대적으로 작은 테이블 (최대 약 1,000,000 행)을 대상으로하고 있습니다. 작은 테이블이 많은 경우는이 테이블 엔진을 사용하는 것이 의미가 있습니다. 로그 (log.md) 엔진 (열어야 적은 파일).

원본 기사 (https://clickhouse.tech/docs/en/operations/table_engines/tinylog/) <! - hide ->
