---
machine_translated : true
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd
toc_priority : 49
toc_title : "\ u30C7 \ u30FC \ u30BF \ u30D0 \ u30C3 \ u30AF \ u30A2"
---

# 데이터 백 아 {# data-backup}

하면서 복제 (../ engines / table-engines / mergetree-family / replication.md) provides protection from hardware failures, it does not protect against human errors : accidental deletion of data, deletion of the wrong table or a table on the wrong cluster, and software bugs that result in incorrect data processing or data corruption. In many cases mistakes like these will affect all replicas. ClickHouse has built-in safeguards to prevent some types of mistakes - for example, by default [50Gb 이상의 데이터를 포함 MergeTree 같은 엔진에서 테이블을 제거 할 수 없습니다 (https://github.com/ClickHouse/ClickHouse/blob/v18.14.18-stable/programs/server/config.xml#L322-L330 ) 그러나 이러한 보장 조치가 커버하지 않는 모든 경우에 해결.

인간의 오류를 효과적으로 줄이기 위해 데이터의 백업 및 복원을위한 전략을 신중하게 준비해야합니다 ** 사전 **.

각각의 회사는 이용 가능한 리소스 및 비즈니스 요구 사항이 다르기 때문에 모든 상황에 맞는 ClickHouse 백업 및 복원을위한 보편적 인 솔루션 무엇이 데이터의 기가 바이트를 위해 일할 가능성이 수십 페타 바이트 위해 작동하지 않습니다. 자신의 장점과 단점을 가지고 다양한 가능한 방법이 있습니다 만, 이것은 다음에서 설명합니다. 에서 사용하게하면 좋을 것입니다 몇 개의 방법으로 그것을 보완하기 위해 님의 여러가지 문제가 있었다.

!!! note "주"
    당신이 뭔가를 백업하고 그것을 복원하려고 한 적이 있다면, 기회는 당신이 실제로 그것을 필요로 할 때 복원이 제대로 작동 따라서 어떤 백업 방법을 선택해서 복원 프로세스를 자동화하고, 여분의 ClickHouse 클러스터에서 정기적으로 연습하십시오.

## 소스 데이터를 다른 위치에 중복 {# duplicating-source-data-somewhere-else}

종종 ClickHouse에 포함 된 데이터는 다음과 같은 몇 가지 영구 큐를 통해 전달됩니다 [아파치 - 카프카 (https://kafka.apache.org).이 경우 ClickHouse에 기록 된 있는 동안 동일한 데이터 스트림을 읽고 어느 콜드 스토리지에 저장하는 추가의 구독자 세트를 구성 할 대부분의 기업은 이미 몇 가지 기본 권장 냉장 창고를 가지고 있습니다. [HDFS (https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html).

## 파일 시스템 {# filesystem-snapshots}

일부 지역의 파일 시스템을 스냅 샷 기능 (예를 들어, ZFS (https://en.wikipedia.org/wiki/ZFS)하지만 라이브 쿼리를 제공하기 위해 최선의 선택이 아닐 수도 있습니다. 가능한 해결책은 이런 종류의 파일 시스템에 추가 복제본을 생성하고이를 분산 (../ engines / table-engines / special / distributed.md) 다음과 같은 목적으로 사용되는 테이블`SELECT `쿼리 스냅 샷 등을 복제하는 것이어야한다 쿼리가 데이터를 변경한다. 보너스 파트로서 이러한 복제가 특별한 하드웨어 구성에 따라 디스크에 포함 된 서버 비용 효과적입니다.

## 클릭 하우스 - 복사기 {# clickhouse-copier}

[클릭 하우스 - 복사기 (utilities / clickhouse-copier.md) 페타 바이트 크기의 테이블을 다시 샤드하기 위해 처음 만들어진 다목적 공구는있다. ClickHouse 테이블과 클러스터간에 데이터를 안정적으로 복사하는 백업 및 복원의 목적으로도 사용할 수 있습니다.

데이터의 작은 볼륨에 대한 간단한`INSERT INTO ... SELECT ...`원격 테이블을 작업하고 있습니다.

## 부품 개입 {# manipulations-with-parts}

ClickHouse은`ALTER TABLE ... FREEZE PARTITION ...`쿼리를 복사 테이블 분할. 이것은 하드 링크를 사용하여 구현된다. `/ var / lib / clickhouse / shadow /`그것은 일반적으로 오래된 데이터를위한 추가 디스크 공간을 소비하지 않습니다. 생성 된 파일의 복사는 ClickHouse server에 의해 처리되지 않기 때문에, 거기 남길 수 있습니다 : 추가 외부 시스템을 필요로하지 않는 간단한 백업이 때문에 원격에서 다른 위치로 복사 한 후 로컬 복사본을 삭제 하는 것이 좋습니다. 분산 파일 시스템과 오브젝트 스토어는 아직 이에 대한 좋은 옵션이지만, 충분한 용량을 가진 일반 첨부 파일 서버뿐만 아니라 작품 [rsync (https://en.wikipedia.org/wiki/Rsync)).

파티션 작업과 관련된 쿼리에 대한 자세한 내용은 문서의 변경] (../ sql-reference / statements / alter.md # alter_manipulations-with-partitions).

타사 도구를 자동화하는 이런 방법 : clickhouse- 백업 (https://github.com/AlexAkulov/clickhouse-backup).

원본 기사 (https://clickhouse.tech/docs/en/operations/backup/) <! - hide ->
