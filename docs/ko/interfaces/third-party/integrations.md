---
machine_translated : true
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd
toc_priority : 27
toc_title : "\ u7D71 \ u5408"
---

# 타사 개발자의 통합 라이브러리 {# integration-libraries-from-third-party-developers}

!!! warning "포기"
    Yandex는 ** 없다 ** 다음 도구와 라이브러리를 유지하고 그 품질을 보장하기위한 광범위한 테스트를 실시하지 않습니다.

## 인프라 제품 {# infrastructure-products}

- 관계형 데이터베이스 관리 시스템
    - [MySQL] (https://www.mysql.com)
        - [mysql2ch (https://github.com/long2ice/mysql2ch)
        - [ProxySQL (https://github.com/sysown/proxysql/wiki/ClickHouse-Support)
        - [clickhouse-mysql- 데이터 판독기 (https://github.com/Altinity/clickhouse-mysql-data-reader)
        - [horgh- 복제기 (https://github.com/larsnovikov/horgh-replicator)
    - [PostgreSQL] (https://www.postgresql.org)
        - [clickhousedb_fdw (https://github.com/Percona-Lab/clickhousedb_fdw)
        - [인휘 clickhouse_fdw (https://github.com/Infinidat/infi.clickhouse_fdw) (용도 [인휘 clickhouse_orm (https://github.com/Infinidat/infi.clickhouse_orm))
        - [pg2ch (https://github.com/mkabilov/pg2ch)
        - [clickhouse_fdw (https://github.com/adjust/clickhouse_fdw)
    - [MSSQL] (https://en.wikipedia.org/wiki/Microsoft_SQL_Server)
        - [ClickHouseMigrator (https://github.com/zlzforever/ClickHouseMigrator)
- 메시지 큐
    - [카프카] (https://kafka.apache.org)
        - [clickhouse_sinker (https://github.com/housepower/clickhouse_sinker) (용도 [Go 클라이언트 (https://github.com/ClickHouse/clickhouse-go/))
        - [stream-loader-clickhouse (https://github.com/adform/stream-loader)
- 스트림 처리
    - [후린쿠 (https://flink.apache.org)
        - [후린쿠 - 클릭 하우스 - 싱크 (https://github.com/ivi-ru/flink-clickhouse-sink)
- 객체 저장
    - [S3] (https://en.wikipedia.org/wiki/Amazon_S3)
        - [clickhouse- 백업 (https://github.com/AlexAkulov/clickhouse-backup)
- 용기의 협주
    - [Kubernetes (https://kubernetes.io)
        - [clickhouse- 연산자 (https://github.com/Altinity/clickhouse-operator)
- 구성 관리
    - [인형] (https://puppet.com)
        - 이노 게임스 / 클릭 하우스 (https://forge.puppet.com/innogames/clickhouse)
        - [mfedotov / 클릭 하우스 (https://forge.puppet.com/mfedotov/clickhouse)
- 감시
    - 흑연 (https://graphiteapp.org)
        - [구라화우스 (https://github.com/yandex/graphouse)
        - [카본 클릭 하우스 (https://github.com/lomik/carbon-clickhouse) +
        - [흑연 - 클릭 (https://github.com/lomik/graphite-clickhouse)
        - 흑연 -ch- 최적화 (https://github.com/innogames/graphite-ch-optimizer) -staled 분할을 최대한 활용하는 [\ * GraphiteMergeTree (../../ engines / table -engines / mergetree-family / graphitemergetree.md # graphitemergetree)에서 규칙의 경우 롤업 구성 (../../ engines / table-engines / mergetree-family / graphitemergetree.md # rollup-configuration) 응용할 수 있습니다
    - [구라화나 (https://grafana.com/)
        - [클릭 하우스 - 구라화나 (https://github.com/Vertamedia/clickhouse-grafana)
    - [프로 메테우스] (https://prometheus.io/)
        - [clickhouse_exporter (https://github.com/f1yegor/clickhouse_exporter)
        - [파티 하우스 (https://github.com/Percona-Lab/PromHouse)
        - [clickhouse_exporter (https://github.com/hot-wifi/clickhouse_exporter) (용도 [Go 클라이언트 (https://github.com/kshvakov/clickhouse/))
    - [나기오스 (https://www.nagios.org/)
        - [check_clickhouse (https://github.com/exogroup/check_clickhouse/)
        - [check_clickhouse.py (https://github.com/innogames/igmonplugins/blob/master/src/check_clickhouse.py)
    - [Zabbix (https://www.zabbix.com)
        - [clickhouse-zabbix- 템플릿 (https://github.com/Altinity/clickhouse-zabbix-template)
    - [Sematext (https://sematext.com/)
        - [clickhouse 통합 (https://github.com/sematext/sematext-agent-integrations/tree/master/clickhouse)
- 로깅
    - [rsyslog (https://www.rsyslog.com/)
        - [옴므 클릭 하우스 (https://www.rsyslog.com/doc/master/configuration/modules/omclickhouse.html)
    - [후루엔토 (https://www.fluentd.org)
        - 로그 하우스 (https://github.com/flant/loghouse) (을 위해 [Kubernetes (https://kubernetes.io))
    - [logagent (https://www.sematext.com/logagent)
        - [logagent output-plugin-clickhouse (https://sematext.com/docs/logagent/output-plugin-clickhouse/)
- Geo
    - [MaxMind (https://dev.maxmind.com/geoip/)
        - [클릭 하우스 -maxmind-geoip (https://github.com/AlexeyKupershtokh/clickhouse-maxmind-geoip)

## 프로그래밍 {# programming-language-ecosystems}

- Python
    - [SQLAlchemy (https://www.sqlalchemy.org)
        - [sqlalchemy- 클릭 하우스 (https://github.com/cloudflare/sqlalchemy-clickhouse) (용도 [인휘 clickhouse_orm (https://github.com/Infinidat/infi.clickhouse_orm))
    - [팬더] (https://pandas.pydata.org)
        - [팬더 하우스 (https://github.com/kszucs/pandahouse)
- PHP
    - [교리] (https://www.doctrine-project.org/)
        - [dbal- 클릭 하우스 (https://packagist.org/packages/friendsofdoctrine/dbal-clickhouse)
- R
    - [dplyr (https://db.rstudio.com/dplyr/)
        - [RClickHouse (https://github.com/IMSMWU/RClickHouse) (용도 클릭 하우스 -cpp (https://github.com/artpaul/clickhouse-cpp))
- Java
    - [Hadoop] (http://hadoop.apache.org)
        - [clickhouse-hdfs-loader] (https://github.com/jaykelin/clickhouse-hdfs-loader) (용도 [JDBC] (../../ sql-reference / table-functions / jdbc.md))
- Scala
    - [아카] (https://akka.io)
        - [clickhouse-scala- 클라이언트 (https://github.com/crobox/clickhouse-scala-client)
- C #
    - [ADO.NET] (https://docs.microsoft.com/en-us/dotnet/framework/data/adonet/ado-net-overview)
        - [클릭 하우스 Ado (https://github.com/killwort/ClickHouse-Net)
        - [클릭 하우스 클라이] (https://github.com/DarkWanderer/ClickHouse.Client)
        - [ClickHouse.Net (https://github.com/ilyabreev/ClickHouse.Net)
        - [ClickHouse.Net.Migrations (https://github.com/ilyabreev/ClickHouse.Net.Migrations)
- 엘릭서
    - [Ecto (https://github.com/elixir-ecto/ecto)
        - [clickhouse_ecto (https://github.com/appodeal/clickhouse_ecto)
- Ruby
    - [Ruby on rails (https://rubyonrails.org/)
        - [activecube (https://github.com/bitquery/activecube)
        - [ActiveRecord] (https://github.com/PNixx/clickhouse-activerecord) 
    - [GraphQL (https://github.com/graphql)
        - [activecube-graphql (https://github.com/bitquery/activecube-graphql)

원본 기사 (https://clickhouse.tech/docs/en/interfaces/third-party/integrations/) <! - hide ->
