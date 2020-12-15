---
machine_translated : true
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd
toc_priority : 44
toc_title : "\ u30E1 \ u30E2 \ u30EA"
---

# 메모리 {#memory}

메모리 엔진은 압축되지 않은 형식으로 RAM에 데이터를 저장합니다. 데이터는 읽을 때 수신되는 것과 동일한 형식으로 저장됩니다. 즉,이 표에서 독서는 완전 무료입니다.
동시 데이터 액세스가 동기화됩니다. 잠금은 짧고 읽기 및 쓰기 작업은 서로 차단하지 않습니다.
인덱스는 지원되지 않습니다. 읽기는 병렬 처리됩니다.
간단한 쿼리는 디스크에서 읽을 데이터의 압축 또는 역 직렬화가되지 않으므로 최대 생산성 (10GB / s)에 도달합니다. (종종 MergeTree 엔진의 생산성은 거의만큼 높은 점에 유의하십시오.)
서버를 다시 시작하면 테이블에서 데이터가 사라지고 테이블이 비어 있습니다.
일반적으로이 테이블 엔진의 사용은 정당화되지 않습니다. 그러나 테스트와 상대적으로 적은 행 (최대 약 100,000,000)에 최대 속도가 필요한 작업에 사용할 수 있습니다.

메모리의 엔진을 사용하는 시스템 임시 테이블의 외부 쿼리 데이터 항목을 참조하십시오 "External data for processing a query"글로벌 구현 내용은 섹션을 참조 "IN operators").

원본 기사 (https://clickhouse.tech/docs/en/operations/table_engines/memory/) <! - hide ->
