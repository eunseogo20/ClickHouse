---
machine_translated : true
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd
toc_priority : 45
toc_title : "\ u76E3 \ u8996"
---

# 감시 {#monitoring}

모니터링 할 수 있습니다 :

- 하드웨어 자원의 이용.
- ClickHouse 서버 지표.

## 리소스 사용률 {# resource-utilization}

ClickHouse 하드웨어 자원의 상태를 혼자 감시하지 않습니다.

모니터를 설치하는 것을 권장합니다 :

- 프로세서의 부하 및 온도.

    다음을 사용할 수 있습니다 [dmesg (https://en.wikipedia.org/wiki/Dmesg), 터보 스타트 (https://www.linux.org/docs/man8/turbostat.html) 또는 다른 악기.

- 스토리지 시스템, RAM, 네트워크 이용.

## ClickHouse 서버 지표 {# clickhouse-server-metrics}

ClickHouse server는 자기 상태 모니터링을위한 계측기가 포함되어 있습니다.

추적 서버의 이벤트 서버를 이용입니다. 를 참조하십시오. [로거] (server-configuration-parameters / settings.md # server_configuration_parameters-logger) 설정 파일의 섹션.

클릭 하우스 수집 :

- 다른 인식의 서버가 어떻게 이용 계산 자원입니다.
- 쿼리 처리에 관한 일반적인 통계.

메트릭은 다음과 같습니다. 시스템 메트릭 (../ operations / system-tables.md # system_tables-metrics) 시스템 이벤트 (../ operations / system-tables.md # system_tables-events), and 시스템 asynchronous_metrics (.. /operations/system-tables.md#system_tables-asynchronous_metrics) 테이블

를 설정할 수 ClickHouse 수출 지표에 [흑연 (https://github.com/graphite-project)를 참조하십시오. [흑연 부 (server-configuration-parameters / settings.md # server_configuration_parameters-graphite) ClickHouse 서버 설정 파일. 지표 내보내기를 구성하기 전에 공식적으로 따르 Graphite를 설정해야합니다 [안내] (https://graphite.readthedocs.io/en/latest/install.html).

를 설정할 수 ClickHouse 수출 지표 [프로 메테우스] (https://prometheus.io)를 참조하십시오. [프로 메테우스 절 (server-configuration-parameters / settings.md # server_configuration_parameters-prometheus) ClickHouse 서버 설정 파일. 메트릭 내보내기를 구성하기 전에 공식적으로 따르 Prometheus을 설정하십시오 [안내] (https://prometheus.io/docs/prometheus/latest/installation/).

또한 HTTP API를 사용하여 서버의 가용성을 모니터링 할 수 있습니다. 보내는`HTTP GET` 요청 대상`/ ping` 서버가 사용 가능한 경우는 다음과 같이 응답합니다`200 OK`.

모니터링 서버 클러스터 구성 설정하십시오 [max_replica_delay_for_distributed_queries (settings / settings.md # settings-max_replica_delay_for_distributed_queries) 매개 변수 및 HTTP 리소스 사용`/ replicas_status`.에 요청`/ replicas_status` 쯔づゥ쯔. `200 OK` 복제가 사용 가능하고, 다른 복제보다 늦게 않은 경우. 복제가 지연하면 다음과 같이됩니다`503 HTTP_SERVICE_UNAVAILABLE` 격차에 대한 정보와.
