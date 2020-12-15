---
machine_translated : true
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd
toc_priority : 28
toc_title : "\ u8996 \ u754C \ u9762"
---

# 외부 공급 {# visual-interfaces-from-third-party-developers}

## 오픈 소스 {# open-source}

### Tabix {#tabix}

의 ClickHouse위한 Web 인터페이스 [Tabix (https://github.com/tabixio/tabix) 프로젝트

특징 :

- 추가 소프트웨어를 설치할 필요없이 브라우저에서 직접 ClickHouse에서 작동합니다.
- 구문 강조와 쿼리 편집기.
- 명령 자동 완성.
- 도구에 대한 그래픽 분석 쿼리를 실행합니다.
- 색상 옵션.

[Tabix 문서 (https://tabix.io/doc/).

### 하우스옷뿌 {#houseops}

[하우스옷뿌 (https://github.com/HouseOps/HouseOps) OSX, Linux 및 Windows 용 UI / IDE입니다.

특징 :

- 구문 강조와 쿼리 빌더. 테이블 또는 JSON 뷰에서 응답을 표시합니다.
- CSV 또는 JSON으로 내보내기 쿼리 결과.
- 설명 된 프로세스의 목록입니다. 쓰기 모드. 정지 능력 (`KILL`) 과정.
- 데이터베이스의 모든 테이블과 열에 추가 정보가 표시됩니다.
- 열 크기의 빠른 볼 수 있습니다.
- 서버 구성.

다음과 같은 기능의 개발이 계획되어 있습니다 :

- 데이터베이스 관리.
- 사용자 관리.
- 실시간 데이터 분석.
- 클러스터 모니터링.
- 클러스터 관리.
- 복제 된 테이블과 카프카 테이블의 감시.

### 등대 {#lighthouse}

[등대 (https://github.com/VKCOM/lighthouse) ClickHouse위한 가벼운 web 인터페이스입니다.

특징 :

- 테이블 목록 필터링 메타 데이터를 지칭합니다.
- 테이블의 미리보기 및 필터입니다.
- 읽기 전용 쿼리를 실행합니다.

### 레닷슈 {#redash}

[레닷슈 (https://github.com/getredash/redash) 멜위한 플랫폼의 데이터를 시각화한다.

지원 다수의 데이터 소스를 포함 ClickHouse, Redash 참여할 수있는 결과 쿼리에서 데이터 소스에 대한 최종 데이터 세트이다.

특징 :

- 쿼리의 강력한 편집기.
- 탐색기
- 다양한 형태의 데이터를 표현할 수있는 시각화 도구.

### DBeaver {#dbeaver}

[DBeaver (https://dbeaver.io/) - 범용 데스크탑 데이터베이스 클라이언트 ClickHouse입니다.

특징 :

- 구문 강조와 자동 완성하여 쿼리 개발.
- 테이블 리필 및 메타 데이터를 검색 할
- 테이블 데이터 미리보기.
- 전문 검색.

### clickhouse-cli {# clickhouse-cli}

[clickhouse-cli (https://github.com/hatarist/clickhouse-cli) Python3로 작성된 ClickHouse 대체 명령 줄 클라이언트입니다.

특징 :

- 자동 완성.
- 쿼리 및 데이터 출력 구문 강조.
- 데이터 출력을위한 호출기 지원.
- 사용자 정의 PostgreSQL과 같은 명령.

### 클릭 하우스 - 후라메구라후 {# clickhouse-flamegraph}

[클릭 하우스 - 후라메구라후 (https://github.com/Slach/clickhouse-flamegraph)이 도구는`system.trace_log`로 [후라메구라후 (http://www.brendangregg.com/flamegraphs.html).

### 클릭 하우스 - 뿌란타무 {# clickhouse-plantuml}

[틱 하우스 - 뿌란타무 (https://pypi.org/project/clickhouse-plantuml/) 생성하는 스크립트입니다 [뿌란타무 (https://plantuml.com/) 테이블의 스키마 그림.

## 상업 {#commercial}

### DataGrip {#datagrip}

[DataGrip (https://www.jetbrains.com/datagrip/) ClickHouse 전용의 지원을 가진 JetBrains의 데이터베이스 IDE입니다. PyCharm, IntelliJ IDEA, GoLand, PhpStorm 등 다른 IntelliJ 기반 도구도 포함되어 있습니다.

특징 :

- 매우 빠른 코드 완성.
- ClickHouse 구문 강조.
- 중첩 된 열 테이블 엔진 등 ClickHouse 고유의 기능을 지원합니다.
- 데이터 편집기.
- 리팩토링.
- 검색 및 탐색.

### Yandex 데이터 렌즈 {# yandex-datalens}

[Yandex 데이터 렌즈 (https://cloud.yandex.ru/services/datalens) 데이터 시각화 및 분석 서비스입니다.

특징 :

- 간단한 막대 그래프에서 복잡한 대시 보드까지 다양한 시각화가 가능합니다.
- 대시 보드를 게시 할 수 있습니다.
- ClickHouse를 포함한 여러 데이터 소스를 지원합니다.
- ClickHouse에 따른 구체화 된 데이터 저장.

데이터 렌즈는 자유를 위해 유효한 (https://cloud.yandex.com/docs/datalens/pricing) 상업적인 사용의 낮은 에너지 프로젝트를 위해.

- [DataLens 문서 (https://cloud.yandex.com/docs/datalens/).
- [츄토리 (https://cloud.yandex.com/docs/solutions/datalens/data-from-ch-visualization)에서 시각화 데이터에서 ClickHouse 데이터베이스입니다.

### Holistics 소프트웨어 {# holistics-software}

[론적 (https://www.holistics.io/) 전체 스택 데이터 플랫폼 비즈니스 인 도구입니다.

특징 :

- 자동 메일, Slack이나 구글 시트 일정.
- Visualizations 버전 관리, 자동 완성, 재사용 가능한 쿼리 구성 요소와 동적 필터 및 SQL 편집기.
- Iframe에 의한 보고서 및 대시 보드의 내장 분석.
- 데이터 준비 및 ETL 기능.
- SQL 데이터 모델링 지원을위한 관계형 매핑 데이터입니다.

### 미인 {#looker}

[미인] (https://looker.com)는 데이터 플랫폼 비즈니스 인 도구를 지원 50+ 데이터베이스의 방언을 포함 ClickHouse. Looker는 SaaS 플랫폼으로 사용할 수있는 셀프 호스팅입니다. 사용자가 사용할 수 Looker있는 경우에는 vpn 클라이언트 직접 탐색 데이터의 구축 시각화 및 대시 보드, 일정, 識農 공간 관리에 대해 배우고 있습니다. Looker 도구를 포함하는 챕터에서는이 기능의 다른 응용 프로그램 및 API
통합 데이터를 다른 응용 프로그램

특징 :

- LookML을 사용한 간단하고 민첩한 개발
    데이터 모델링 (https://looker.com/platform/data-modeling) 보고서 작성자와 최종 사용자를 지원한다.
- 구경꾼에 의한 강력한 워크 플로우 통합 데이터 작업 (https://looker.com/platform/actions).

[Looker에서 ClickHouse을 설정하는 방법. (https://docs.looker.com/setup-and-management/database-config/clickhouse)

원본 기사 (https://clickhouse.tech/docs/en/interfaces/third-party/gui/) <! - hide ->
