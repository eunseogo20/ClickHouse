---
machine_translated : true
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd
toc_priority : 44
toc_title : "\ u8981 \ u4EF6"
---

# 요구 사항 {#requirements}

## CPU {#cpu}

빌드 된 deb 패키지에서 설치하려면 X86_64 아키텍처의 CPU를 사용하고 SSE4.2 명령어를 지원합니다. 달리기 ClickHouse 프로세서를 지원하지 않는 SSE4.2 て AArch64는 PowerPC64LE 건축 협력하여 추진하는 것이 필요하며 ClickHouse에서.

ClickHouse을 구현 한 병렬 데이터 처리 - 가능한 모든 하드웨어 정보를 제공합니다. 프로세서를 선택할 때, ClickHouse가 다수의 코어를 가지는 구성에보다 효율적으로 동작하지만, 코어가 적고 클럭 속도가 높은 구성에서는 at보다 낮은 클 예를 들어, 16 2600MHz의 코어는 8 3600MHz의 코어 보다 바람직합니다.

사용하는 것이 좋습니다 ** 터보 부스트 **와 ** 하이퍼 스레드 ** 기술 상당한 성능 향상을 그림은 전형적인 부하도 커지고있었습니다.

## RAM {#ram}

하찮은 쿼리를 실행하려면 최소 4GB의 RAM을 사용하는 것이 좋습니다. ClickHouse 서버는 훨씬 적은 양의 RAM에서 실행할 수있는 쿼리를 처리하기 위해 메모리가 필요합니다.

필요한 RAM 용량은 다음과 같습니다 :

- 쿼리의 복잡성.
- 쿼리에서 처리되는 데이터의 양.

RAM의 필요량을 계산하려면 다음과 같은 임시 데이터의 크기를 추정해야합니다 [GROUP BY] (../ sql-reference / statements / select / group-by.md # select-group- by-clause) [DISTINCT] (../ sql-reference / statements / select / distinct.md # select-distinct) [JOIN] (../ sql-reference / statements / select / join.md # select- join) 그리고 당신이 사용하는 다른 작업.

ClickHouse는 임시 데이터에 외부 메모리를 사용할 수 있습니다. 보는 외부 메모리 GROUP BY] (../ sql-reference / statements / select / group-by.md # select-group-by-in-external-memory) 자세한 내용은.

## Swap 파일 {# swap-file}

운영 환경의 스왑 파일을 해제합니다.

## Storage 서브 시스템 {# storage-subsystem}

ClickHouse를 설치하려면 2GB의 디스크 여유 공간이 필요합니다.

데이터에 필요한 스토리지 용량은 별도로 계산해야합니다. 평가는 :

- 데이터 량의 추정.

    데이터의 샘플을 가져 와서 거기에서 행의 평균 크기를 얻을 수 있습니다. 다음 값을 저장하려는 행 수를 곱합니다.

- 데이터 압축 계수.

    데이터 압축 계수를 추정하려면 데이터 샘플을 ClickHouse에로드 데이터의 실제 크기와 저장되는 테이블의 크기를 비교합니다. 예를 들어, clickstream 데이터는 일반적으로 6-10 회 압축됩니다.

저장할 데이터의 최종 볼륨을 계산하려면 추정 데이터 볼륨에 압축 계수를 적용합니다. 여러 복제본에 데이터를 저장하는 경우에는 추정 볼륨 복제본의 수를 곱합니다.

## 인터넷 {#network}

가능하면 10G 이상의 네트워크를 사용하십시오.

대량의 중간 데이터를 포함하는 분산 쿼리를 처리하는 네트워크 대역폭이 중요합니다. 또한 네트워크 속도에 영향을 복제 과정.

## 소프트 {#software}

ClickHouse의 개발을 중심으로 Linux의 가족 시스템입니다. 권장되는 Linux 배포 뷸 그`tzdata` 패키지

ClickHouse 제의 수, 기타 업무 시스템. 세부 사항을 참조하십시오 [소개] (../ getting-started / index.md) 문서의 섹션.
