--- 
machine_translated : true 
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd 
toc_priority : 57 
toc_title : "\ u30B5 \ u30FC \ u30D0 \ u30FC \ u8A2D \ u5B9A" 
--- 

# 서버 설정 {# server-settings} 

## builtin_dictionaries_reload_interval {# builtin-dictionaries-reload -interval} 

내장 사전을 다시로드 할 때까지의 초 단위의 간격. 

ClickHouse가 x 초마다 내장 된 사전을 다시로드합니다. 이렇게하면 사전 편집 할 수 있습니다 "on the fly"서버를 다시 시작하지 않고. 

기본값은 3600입니다. 

** 예 ** 

```xml 
<builtin_dictionaries_reload_interval> 3600 </ builtin_dictionaries_reload_interval> 
``` 

## 압축 {# server-settings-compression} 

데이터 압축 설정 메루게쯔리 (../../ engines / table-engines /mergetree-family/mergetree.md)- 엔진 테이블.

!!! warning "경고" 
    ClickHouse의 사용을 시작한지 얼마 안된 경우에는 사용하지 마십시오. 

구성 템플릿 : 

```xml 
<compression> 
    <case> 
      <min_part_size> ... </ min_part_size> 
      <min_part_size_ratio> ... </ min_part_size_ratio> 
      <method> ... </ method> 
    </ case> 
    ... 
</ compression> 
``` 

`<case>`필드 : 

-`min_part_size` - the minimum size of a data part. 
-`min_part_size_ratio` - the ratio of the data part size to the table size . 
-`method` - Compression method. Acceptable values :`lz4` 또는`zstd`. 

여러 설정이 가능합니다`<case>`섹션 

조건을 충족 한 경우 조치 :


데이터 부품의 조건이 충족되지 않은 경우 ClickHouse는`lz4` 압축. 

** 예 ** 

```xml 
<compression incl = "clickhouse_compression"> 
    <case> 
        <min_part_size> 10000000000 </ min_part_size> 
        <min_part_size_ratio> 0.01 </ min_part_size_ratio> 
        <method> zstd </ method> 
    </ case> 
</ compression> 
``` 

## default_database {# default-database} 

기본 데이터베이스입니다. 

데이터베이스의 목록을 얻으려면, SHOW DATABASES (../../ sql-reference / statements / show.md # show-databases) 쿼리. 

** 예 ** 

```xml 
<default_database> default </ default_database>

 
기본 설정 프로파일. 

설정 프로파일의 파일에서 지정된 매개 변수`user_config`.

** 예 ** 

```xml 
<default_profile> default </ default_profile> 
``` 

## dictionaries_config {# server_configuration_parameters-dictionaries_config} 

외부 사전 설정 파일의 경로. 

경로 : 

- 절대 경로 또는 서버 설정 파일에 대한 상대 경로를 지정합니다. 
- 경로를 포함 할 수 와일드 카드 \ *와?. 

참조. "외부 사전 (../../ sql-reference / dictionaries / external-dictionaries / external-dicts.md)" 

** 예 ** 

```xml 
<dictionaries_config> * _ dictionary.xml </ dictionaries_config> 
``` 

## dictionaries_lazy_load {# server_configuration_parameters-dictionaries_lazy_load} 

사전의 지연로드. 

만약`true` 다음 각 사전은 처음 사용할 때 작성됩니다. 사전의 작성에 실패했을 경우, 사전을 사용했다 함수는 예외를 throw합니다. 

만약`false` 모든 사전은 서버가 시작될 때 생성되고 오류가있는 경우 서버가 종료됩니다.

기본값은 다음과 같습니다`true`. 

** 예 ** 

```xml 
<dictionaries_lazy_load> true </ dictionaries_lazy_load> 
``` 

## format_schema_path {# server_configuration_parameters-format_schema_path} 

입력 데이터의 스키마를 포함하는 디렉토리 경로 . [CapnProto (../../ interfaces / formats.md # capnproto) 형식. 

** 예 ** 

```xml 
  <! - Directory containing schema files for various input formats -> 
  <format_schema_path> format_schemas / </ format_schema_path> 
``` 

## 흑연 {# server_configuration_parameters-graphite} 

데이터의 대상 [흑연 (https://github.com/graphite-project) 

설정 : 

- host - The Graphite server.
- port - The port on the Graphite server.  
- interval - The interval for sending, in seconds.
- timeout - The timeout for sending data, in seconds. 
- root_path - Prefix for keys. 
- metrics - Sending data from the 시스템 메트릭 (../../ operations / system -tables.md # system_tables-metrics) 테이블. 
- events - Sending deltas data accumulated for the time period from the 시스템 이벤트 (../../ operations / system-tables.md # system_tables-events) 테이블. 
- events_cumulative - Sending cumulative data from the 시스템 이벤트 (../../ operations / system-tables.md # system_tables-events) 테이블. 
- asynchronous_metrics - Sending data from the 시스템 asynchronous_metrics (../../ operations / system-tables.md # system_tables-asynchronous_metrics) 테이블. 

여러 설정이 가능합니다`<graphite>`어구. 예를 들어, 다른 간격으로 다른 데이터를 전송하기 위해 이것을 사용할 수 있습니다. 

** 예 **
 
```xml 
<graphite> 
    <host> localhost </ host> 
    <port> 42000 </ port> 
    <timeout> 0.1 </ timeout> 
    <interval> 60 </ interval> 
    <root_path> one_min </ root_path> 
    <metrics> true </ metrics> 
    <events> true </ events> 
    <events_cumulative> false </ events_cumulative> 
    <asynchronous_metrics> true </ asynchronous_metrics> 
</ graphite> 
``` 

## graphite_rollup {# server_configuration_parameters-graphite-rollup} 

흑연의 데이터를 얇게하기 설정. 

자세한 내용은을 참조하십시오 [GraphiteMergeTree (../../ engines / table-engines / mergetree-family / graphitemergetree.md) 

** 예 ** 

```xml 
<
        <retention> 
            <age> 0 </ age> 
            <precision> 60 </ precision> 
        </ retention> 
        <retention> 
            <age> 3600 </ age> 
            <precision> 300 </ precision> 
        </ retention> 
        <retention> 
            < age> 86400 </ age> 
            <precision> 3600 </ precision> 
        </ retention> 
    </ default> 
</ graphite_rollup_example> 
``` 

## http_port / https_port {# http-porthttps-port} 

HTTP를 통해 서버에 연결 위한 포트. 

만약`https_port` 지정되는 [openSSL (# server_configuration_parameters-openssl) 설정해야합니다. 

만약`http_port`가 지정되어있는 경우에는 OpenSSL 설정이 설정되어 있어도 무시됩니다. 

** 예 **

```xml
<https_port> 9999 </ https_port> 
``` 

## http_server_default_response {# server_configuration_parameters-http_server_default_response} 

ClickHouse HTTP (s) 서버에 액세스 할 때 기본적으로 표시되는 페이지입니다. 
기본값은 다음과 같습니다 "Ok"(마지막에 개행이 있습니다) 

** 예 ** 

열`https : // tabix.io /`액세스시`http : // localhost : http_port`. 

```xml 
<http_server_default_response> 
  <! [CDATA [<html ng-app = "SMI2"> <head> <base href = "http://ui.tabix.io/"> </ head> <body> <div ui-view = ""class = "content-ui"> </ div> <script src = "http://loader.tabix.io/master.js"> </ script> </ body> </ html>]]> 
</ http_server_default_response>



자세한 내용은 "설정 파일 (../ configuration-files.md # configuration_files)" 

** 예 ** 

```xml 
<include_from> /etc/metrica.xml </ include_from> 
``` 

## interserver_http_port {# interserver-http-port} 

ClickHouse 서버간에 데이터를 교환하는 포트. 

** 예 ** 

```xml 
<interserver_http_port> 9009 </ interserver_http_port> 
``` 

## interserver_http_host {# interserver-http-host} 

이 서버에 액세스하는 다른 서버가 사용할 수있는 호스트 이름입니다. 

생략 된 경우, 이것은`hostname-f` 명령 

특정 네트워크 

** 예 ** 

```xml 
<interserver_http_host> example.yandex.ru </ interserver_http_host> 
``` 

## interserver_http_credentials {# server-settings-interserver -http-credentials}

인증시 사용되는 사용자 이름과 암호 복제 (../../ engines / table-engines / mergetree-family / replication.md) 복제 된 \ * 엔진. 이러한 자격 증명은 복제 사이의 통신에만 사용되며 ClickHouse 클라이언트의 자격 증명과는 무관합니다. 서버의 체크 이러한 자격의 연결은 복제와 같은 자격을 연결하면 다른 복제 그래서, 이러한 자격을 설정 같은 모든 복제본 클러스터 
기본적으로 인증은 사용되지 않습니다. 

이 섹션에서는 다음 매개 변수 : 

-`user` - username. 
-`password` - password. 

** 예 ** 

```xml 
<interserver_http_credentials> 
    <user> admin </ user> 
    <password> 222 </ password> 
< / interserver_http_credentials> 
``` 

## keep_alive_timeout {# keep-alive-timeout} 

ClickHouse가 연결을 닫기 전에 들어오는 요청을 대기하는 시간 (초). 기본값은 3 초입니다. 

** 예 ** 

```xml 
<keep_alive_timeout> 3 </ keep_alive_timeout> 
```

## listen_host {# server_configuration_parameters-listen_host} 

요청하는 호스트에 대한 제한. 원한다면 서버의 응답을하고 있지만, 그들을 지정`:`. 

예 : 

```xml 
<listen_host> :: 1 </ listen_host> 
<listen_host> 127.0.0.1 </ listen_host> 
``` 

## 로거 {# server_configuration_parameters-logger} 

로그 설정. 

키 : 

- level - Logging level. Acceptable values :`trace``debug``information``warning``error`. 
- log - The log file. Contains all the entries according to`level`. 
- errorlog - Error log file. 
- size - size of the file. Applies to`log`와`errorlog` 파일이 도달하면`size`, ClickHouse 보관하고 그것의 이름을 변경하고 해당 위치에 새 로그 파일을 만듭니다. 
- count - The number of archived log files that ClickHouse stores .

** 예 ** 

```xml 
<logger> 
    <level> trace </ level> 
    <log> /var/log/clickhouse-server/clickhouse-server.log </ log> 
    <errorlog> / var / log / clickhouse -server / clickhouse-server.err.log </ errorlog> 
    <size> 1000M </ size> 
    <count> 10 </ count> 
</ logger> 
``` 

Syslog에 쓰기도 지원되고 있습니다. 설정 예 : 

```xml 
<logger> 
    <use_syslog> 1 </ use_syslog> 
    <syslog> 
        <address> syslog.remote : 10514 </ address> 
        <hostname> myhost.local </ hostname> 
        <facility> LOG_LOCAL6 </ facility> 
        <format> syslog </ format>


- use_syslog - Required setting if you want to write to the syslog . 
- address - The host \ [: port \] of syslogd. If omitted, the local daemon is used. 
- hostname - Optional. The name of the host that logs are sent from. 
- facility - [Syslog 기능 키워드 (https://en.wikipedia.org/wiki/Syslog#Facility) 대문자는 "LOG_"접두사 : (`LOG_USER``LOG_DAEMON``LOG_LOCAL3`라는 같이). 
    기본값 :`LOG_USER` 만약`address` 지정되는`LOG_DAEMON otherwise.` 
- format - Message format. Possible values :`bsd`와`syslog.` 

## 매크로 {#macros} 

복제 된 테이블의 매개 변수 대체 . 

하여야 생략 할 수 복제의 테이블은 사용하고 있지 않습니다. 

자세한 내용은 "복제 테이블 만들기 (../../ engines / table-engines / mergetree-family / replication.md)" 

** 예 ** 
 
```xml
<macros incl = "macros"optional = "true" /> 
``` 

## mark_cache_size {# server-mark-cache-size} 

테이블 엔진이 사용하는 마크 캐시의 대략적인 크기 (바이트 단위) 메루게쯔리 ( ../../engines/table-engines/mergetree-family/mergetree.md) 가족이다 

캐시 공유 서버 메모리가 할당됩니다. 캐시 크기는 적어도 5368709120이어야합니다. 

** 예 ** 

```xml 
<mark_cache_size> 5368709120 </ mark_cache_size> 
``` 

## max_concurrent_queries {# max-concurrent-queries} 

동시에 처리되는 요청의 최대 수. 

** 예 ** 

```xml 
<max_concurrent_queries> 100 </ max_concurrent_queries> 
``` 

## max_connections {# max-connections} 

들어오는 연결의 최대 수. 

** 예 ** 

```xml 
<max_connections> 4096 </ max_connections>
 
## max_open_files {# max-open-files}
 
열려있는 파일의 최대 수. 

기본적으로`maximum`. 

이 옵션을 Mac OS X에서 사용하는 것이 좋습니다. `getrlimit ()`함수는 잘못된 값을 반환합니다. 

** 예 ** 

```xml 
<max_open_files> 262144 </ max_open_files> 
``` 

## max_table_size_to_drop {# max-table-size-to-drop} 

테이블의 삭제에 대한 제한. 

크기가 메루게쯔리 (../../ engines / table-engines / mergetree-family / mergetree.md) 테이블 초과`max_table_size_to_drop` (바이트 단위) 드롭 쿼리를 사용하여 제거 할 수 없습니다. 

ClickHouse 서버를 다시 시작하지 않고 테이블을 제거 할 필요가있는 경우에는`<clickhouse-path> / flags / force_drop_table` DROP 쿼리 파일을 실행합니다. 

기본값 : 50GB. 

값 0은 제한없이 모든 테이블을 삭제할 수 있음을 의미합니다. 

** 예 ** 

```xml 
<max_table_size_to_drop> 0 </ max_table_size_to_drop> 
```

## merge_tree {# server_configuration_parameters-merge_tree} 

테이블 조정 [메루게쯔리 (../../ engines / table-engines / mergetree-family / mergetree.md). 

자세한 내용은 MergeTreeSettings를 참조하십시오. h 헤더 파일. 

** 예 ** 

```xml 
<merge_tree> 
    <max_suspicious_broken_parts> 5 </ max_suspicious_broken_parts> 
</ merge_tree> 
``` 

## openSSL {# server_configuration_parameters-openssl} 

SSL 클라이언트 / 서버 구성. 

SSL 지원은 다음에 의해 제공됩니다`libpoco` 도서관 사용자 인터페이스는 파일에 [SSLManager.h (https://github.com/ClickHouse-Extras/poco/blob/master/NetSSL_OpenSSL/include/Poco/Net /SSLManager.h) 

서버 / 클라이언트 설정 키 :

- privateKeyFile - The path to the file with the secret key of the PEM certificate . The file may contain a key and certificate at the same time . 
- certificateFile - The path to the client / server certificate file in PEM format. You can omit it if`privateKeyFile` 인증서가 포함되어 있습니다. 
- caConfig - The path to the file or directory that contains trusted root certificates . 
- verificationMode - The method for checking the node 's certificates. Details are in the description of the [맥락] (https://github.com/ClickHouse-Extras/ poco / blob / master / NetSSL_OpenSSL / include / Poco / Net / Context.h) 클래스 가능한 값 :`none``relaxed``strict``once`.  
- verificationDepth - the maximum length of the verification chain. Verification will fail if the certificate chain length exceeds the set value .
- loadDefaultCAFile - Indicates that built-in CA certificates for OpenSSL will be used. Acceptable values :`true``false`. \ | 
- cipherList - Supported OpenSSL encryptions. For example :`ALL :! ADH :! LOW :! EXP :! MD5 : @ STRENGTH`. 
- cacheSessions - Enables or disables caching sessions. Must be used in combination with`sessionIdContext`. 허용치 :`true``false`. 
- sessionIdContext - A unique set of random characters that the server appends to each generated identifier. the length of the string must not exceed`SSL_MAX_SSL_SESSION_ID_LENGTH`이 매개 변수는 항상 좋습니다 문제를 피하게 될 것이다 두 경우 서버의 캐시 세션이 클라이언트의 요청은 캐시 기본값 값 :`$ {application.name}`. 
- sessionCacheSize - the maximum number of sessions that the server caches . Default value : 1024 \ * 20 0 - Unlimited sessions.
- sessionTimeout - Time for caching the session on the server.  
- extendedVerification - Automatically extended verification of certificates after the session ends. Acceptable values :`true``false`.
- requireTLSv1 - Require a TLSv1 connection. Acceptable values :`true``false`. 
- requireTLSv1_1 - Require a TLSv1.1 connection. Acceptable values :`true`,`false. `. 
- requireTLSv1 - Require a TLSv1.2 connection. Acceptable values :`true``false`. 
- fips - Activates OpenSSL FIPS mode. Supported if the library 's OpenSSL version supports FIPS . 
- privateKeyPassphraseHandler - Class (privateKeyPassphraseHandler subclass) that requests the passphrase for accessing the private key. For example :`<privateKeyPassphraseHandler>``<name> KeyFileHandler </ name>``<options> <password> test </ password> </ options>``</ privateKeyPassphraseHandler >`. 
- invalidCertificateHandler - Class (a subclass of CertificateHandler) for verifying invalid certificates. For example :`<invalidCertificateHandler> <name> ConsoleCertificateHandler </ name> </ invalidCertificateHandler>`. 
- disableProtocols - Protocols that are not allowed to use. 
- preferServerCiphers - Preferred server ciphers on the client. 

** 설정 예 : ** 

```xml 
<openSSL>
    <server>  
        <! - openssl req -subj "/ CN = localhost"-new -newkey rsa : 2048 -days 365 -nodes -x509 -keyout / etc / clickhouse-server / server.key -out /etc/clickhouse-server/server.crt -> 
        <certificateFile> /etc/clickhouse-server/server.crt </ certificateFile> 
        <privateKeyFile> / etc / clickhouse-server / server .key </ privateKeyFile>
        <! - openssl dhparam -out /etc/clickhouse-server/dhparam.pem 4096 -> 
        <dhParamsFile> /etc/clickhouse-server/dhparam.pem </ dhParamsFile> 
        <verificationMode> none </ verificationMode> 
        <loadDefaultCAFile> true </ loadDefaultCAFile> 
        <cacheSessions> true </ cacheSessions> 
        <disableProtocols> sslv2, sslv3 </ disableProtocols> 
        <preferServerCiphers> true </ preferServerCiphers> 
    </ server> 
    <client> 
        <loadDefaultCAFile> true </ loadDefaultCAFile> 
        < cacheSessions> true </ cacheSessions> 
        <disableProtocols> sslv2, sslv3 </ disableProtocols> 
        <preferServerCiphers> true </ preferServerCiphers>
        <! - Use for self-signed : <verificationMode> none </ verificationMode> -> 
        <invalidCertificateHandler> 
            <! - Use for self-signed : <name> AcceptCertificateHandler </ name> -> 
            <name> RejectCertificateHandler < / name> 
        </ invalidCertificateHandler> 
    </ client> 
</ openSSL> 
``` 

## part_log {# server_configuration_parameters-part-log} 

연관된 이벤트 로깅 [메루게쯔리 (../../ engines / table- engines / mergetree-family / mergetree.md). 예를 들어, 데이터 추가 및 병합 등입니다. 사용할 수있는 로그를 통합 알고리즘과 비교합니다. 병합 프로세스를 시각화 할 수 있습니다. 

쿼리는 시스템 part_log (../../ operations / system-tables. md # system_tables-part-log) 다른 파일이 아닌 테이블. 이 테이블의 이름은`table` 매개 변수 (아래 참조). 

다음 매개 변수 설정 로깅 :

-`database` - Name of the database.
-`table` - Name of the system table. 
-`partition_by` - Sets a 사용자 정의 분할 키 (../../ engines / table-engines / mergetree-family / custom-partitioning-key.md) 
- `flush_interval_milliseconds` - Interval for flushing data from the buffer in memory to the table . 

** 예 ** 

```xml 
<part_log> 
    <database> system </ database> 
    <table> part_log </ table> 
    <partition_by> toMonday ( event_date) </ partition_by> 
    <flush_interval_milliseconds> 7500 </ flush_interval_milliseconds> 
</ part_log> 
``` 

## 경로 {# server_configuration_parameters-path} 

데이터를 포함하는 디렉토리 경로. 

!!! note "주" 
    후행 슬래시가 필요합니다. 

** 예 **

```xml 
<
``` 

## 프로 메테우스 {# server_configuration_parameters-prometheus} 

된다고 지표 데이터 공개 [프로 메테우스] (https://prometheus.io) 

설정 : 

-`endpoint` - HTTP endpoint for scraping metrics by prometheus server. Start from ' / '. 
-`port` - Port for`endpoint`. 
-`metrics` - Flag that sets to expose metrics from the 시스템 메트릭 (../ system-tables.md # system_tables-metrics) 테이블. 
-`events` - Flag that sets to expose metrics from the [시스템 이벤트] (../ system-tables.md # system_tables-events) 테이블. 
-`asynchronous_metrics` - Flag that sets to expose current metrics values from the 시스템 asynchronous_metrics] (../ system-tables.md # system_tables-asynchronous_metrics) 테이블. 

** 예 ** 
 
```xml 
 <prometheus>
        <endpoint> / metrics </ endpoint>
        <port> 8001 </ port> 
        <metrics> true </ metrics> 
        <events> true </ events> 
        <asynchronous_metrics> true </ asynchronous_metrics> 
    </ prometheus> 
``` 

## query_log {# server_configuration_parameters-query-log} 

쿼리를 기록하기위한 설정 log_queries = 1] (../ settings / settings.md) 설정. 

쿼리는 시스템 query_log (../../ operations / system-tables.md # system_tables-query_log) 다른 파일이 아닌 테이블. 테이블의 이름을 변경 할 수 있습니다. `table` 매개 변수 (아래 참조). 

다음 매개 변수 설정 로그 : 
 
-`database` - Name of the database. 
-`table` - Name of the system table the queries will be logged in .
-`partition_by` - Sets a 사용자 정의 분할 키 (../../ engines / table-engines / mergetree-family / custom-partitioning-key.md) 테이블을 위해. 
-`flush_interval_milliseconds` - Interval for flushing data from the buffer in memory to the table .

테이블이 존재하지 않는 경우 ClickHouse 그것을 만듭니다. ClickHouse 서버가 업데이트 된 경우 쿼리 로그의 구조가 변경된 경우 기존 구조 테이블의 이름이 변경되고 새 테이블이 자동으로 작성됩니다. 

** 예 ** 

```xml 
<query_log> 
    <database> system </ database> 
    <table> query_log </ table> 
    <partition_by> toMonday (event_date) </ partition_by> 
    <flush_interval_milliseconds> 7500 </ flush_interval_milliseconds> 
</ query_log > 
``` 
 
## query_thread_log {# server_configuration_parameters-query-thread-log}

쿼리 스레드를 기록하기위한 설정 log_query_threads = 1] (../ settings / settings.md # settings-log-query-threads) 설정. 

쿼리는 시스템 query_thread_log (../../ operations / system-tables.md # system_tables-query-thread-log) 다른 파일이 아닌 테이블. 테이블의 이름을 변경 할 수 있습니다. `table` 매개 변수 (아래 참조). 

다음 매개 변수 설정 로깅 :

-`table` - Name of the system table the queries will be logged in . 
-`partition_by` - Sets a 사용자 정의 분할 키 (../../ engines / table-engines / mergetree-family / custom-partitioning- key.md) 시스템 테이블의 경우. 
-`flush_interval_milliseconds` - Interval for flushing data from the buffer in memory to the table . 

테이블이 존재하지 않는 경우 ClickHouse 그것을 만듭니다. ClickHouse 서버 업데이트시 쿼리 스레드 로그의 구조가 변경된 경우 기존 구조 테이블의 이름이 변경되고 새 테이블이 자동으로 작성됩니다. 

** 예 ** 

```xml 
<query_thread_log>
    <database> system </ database> 
    <table> query_thread_log </ table> 
    <partition_by> toMonday (event_date) </ partition_by> 
    <flush_interval_milliseconds> 7500 </ flush_interval_milliseconds> 
</ query_thread_log> 
``` 

## trace_log {# server_configuration_parameters-trace_log }
-`flush_interval_milliseconds` - Interval for flushing data from the buffer in memory to the table .

설정 [trace_log (../../ operations / system-tables.md # system_tables-trace_log) 시스템 테이블 조작. 

매개 변수 : 

-`database` - Database for storing a table. 
-`table` - Table name. 
-`partition_by` - 사용자 지정 분할 키 (../../ engines / table-engines / mergetree-family / custom- partitioning-key.md) 시스템 테이블의 경우. 

기본 서버 구성 파일`config.xml` 다음 설정 섹션이 있습니다 : 

```xml 
<trace_log> 
    <database> system </ database> 
    <table> trace_log </ table> 
    <partition_by> toYYYYMM (event_date) </ partition_by > 
    <flush_interval_milliseconds> 7500 </ flush_interval_milliseconds> 
</ trace_log> 
``` 

## query_masking_rules {# query-masking-rules} 

`system.query_log``system.text_log``system.processes` 클라이언트로 전송 된 로그 . 이는 
SQL 쿼리에서 기밀 데이터 유출 (이름, 이메일, 개인 등 
로그에 식별자 또는 신용 카드 번호). 

** 예 ** 

```xml 
<query_masking_rules> 
    <rule> 
        <name> hide SSN </ name> 
        <regexp>
        <replace> 000-00-0000 </ replace> 
    </ rule> 
</ query_masking_rules> 
``` 

설정 필드 : 
-`name` - 규칙 이름 (옵션) 
-`regexp` -RE2 호환 정규 표현식 (필수) 
-`replace` - 민감한 데이터의 대체 문자열 (옵션, 기본값은 - 여섯 별표) 

마스킹 규칙은 전체 쿼리에 적용됩니다 (잘못된 형식 / 해석 불가능한 쿼리에서 기밀 데이터가 유출되는 것을 방지하기 ). 

`system.events` 테이블 카운터`QueryMaskingRulesMatch`하고 전체 마스킹 규칙. 

분산 쿼리의 경우 각 서버를 개별적으로 구성해야합니다. 
노드는 마스크없이 저장됩니다. 

## remote_servers {# server-settings-remote-servers}

에 의해 사용되는 클러스터 구성 분산 (../../ engines / table-engines / special / distributed.md) 테이블 엔진과`cluster` 테이블 함수. 

** 예 ** 

```xml 
<remote_servers incl = "clickhouse_remote_servers"/> 
``` 

값에 대해`incl` 속성은 절을 참조하십시오 "설정 파일 (../ configuration-files .md # configuration_files) " 

** 참조. ** 

- [skip_unavailable_shards] (../ settings / settings.md # settings-skip_unavailable_shards) 

## 시간대 {# server_configuration_parameters-timezone} 

서버의 시간대. 

UTC 시간대 또는 지리적 위치 (예 : Africa / Abidjan)의 IANA 식별자로 지정합니다. 

시간대는 DateTime 필드를 텍스트 형식 (화면 또는 파일에 출력)에 출력 할 때, 그리고 문자열에서 DateTime을받을 때 문자열과 DateTime 형식 사이 또한 시간대는 입력 매개 변수로 시간대 을받지 못한 경우 시간과 날짜를 다루는 함수에서 사용됩니다. 

** 예 ** 

```xml
<timezone> Europe / Moscow </ timezone> 
``` 

## tcp_port {# server_configuration_parameters-tcp_port} 

TCP 프로토콜을 통해 클라이언트와 통신하는 포트입니다. 

** 예 ** 

```xml 
<tcp_port> 9000 </ tcp_port> 
```

## tcp_port_secure {# server_configuration_parameters-tcp_port_secure} 

클라이언트와의 안전한 통신을위한 TCP 포트. 그것을 사용하여 OpenSSL (# server_configuration_parameters-openssl) 설정. 

** 가능한 값 ** 

양의 정수. 

** 기본값 ** 

```xml 
<tcp_port_secure> 9440 </ tcp_port_secure> 
``` 

## mysql_port {# server_configuration_parameters-mysql_port} 

MySQL 프로토콜 

** 가능한 값 ** 

양의 정수. 

예 

```xml 
<mysql_port> 9004 </ mysql_port> 
``` 

## tmp_path {# server-settings-tmp_path} 

대규모 쿼리를 처리하기위한 임시 데이터 경로. 

!!! note "주" 
    후행 슬래시가 필요합니다. 

** 예 ** 

```xml 
<tmp_path> / var / lib / clickhouse / tmp / </ tmp_path> 
```
 
## tmp_policy {# server-settings-tmp-policy} 

정책에서`storage_configuration`] (../. ./engines/table-engines/mergetree-family/mergetree.md#table_engine-mergetree-multiple-volumes) 임시 파일을 저장한다. 
설정되어 있지 않은 경우 [`tmp_path` (# server-settings-tmp_path) 그렇지 않으면 무시됩니다. 

!!! note "주" 
    -`move_factor` 무시된다 
-`keep_free_space_bytes` 무시된다 
-`max_data_part_size_bytes` 무시되는 
하여야한다 동일한 수량의 정책 

## uncompressed_cache_size {# server-settings-uncompressed_cache_size}

테이블 엔진을 사용하는 비 압축 데이터의 캐시 크기 (바이트) 메루게쯔리 (../../ engines / table-engines / mergetree-family / mergetree.md) 
 
서버의 공유 캐시가 하나 있습니다. 메모리가 할당됩니다. 캐시가 사용되는 것은 [use_uncompressed_cache] (../ settings / settings.md # setting-use_uncompressed_cache) 유효합니다.

비 압축 캐시는 개별 사건에서 매우 짧은 쿼리에서 유리합니다. 

** 예 ** 

```xml 
<uncompressed_cache_size> 8589934592 </ uncompressed_cache_size> 
``` 

## user_files_path {# server_configuration_parameters-user_files_path} 

유 테이블 함수에서 사용됩니다 파일 () (../../ sql- reference / table-functions / file.md) 

** 예 ** 

```xml 
<user_files_path> / var / lib / clickhouse / user_files / </ user_files_path> 
``` 

## users_config {# users-config} 

다음 파일 경로 : 

- 사용자 구성. 
- 권한. 
- 설정 프로필. 
- 할당량 설정. 

** 예 ** 

```xml 
<users_config> users.xml </ users_config> 
``` 

## 사육사 {# server-settings_zookeeper}

ClickHouse와 상호 작용할 수 있도록하는 설정이 포함되어 있습니다. [사육사 (http://zookeeper.apache.org/) 클러스터. 

ClickHouse 용 사육사 저장 메타 데이터 복제 사용시 재현합니다. 경우 복제 테이블을 사용하지 않기 때문에이 매개 변수를 생략 할 수 있습니다. 

이 섹션에서는 다음 매개 변수 : 

-`node` - ZooKeeper endpoint. You can set multiple endpoints. 

    예를 들면 : 

<! - -> 

```xml 
    <node index = "1"> 
        <host> example_host </ host> 
        <port> 2181 </ port> 
    </ node> 
``` 

      The`index` attribute specifies the node order when trying to connect to the ZooKeeper cluster . 
 
-`session_timeout` - Maximum timeout for the client session in milliseconds.
-`root` - The [znode (http : //zookeeper.apache .org / doc / r3.5.5 / zookeeperOver.html # nodes + and + ephemeral + nodes)이 ClickHouse 서버에서 사용되는 znode의 루트로 사용됩니다. 어떤.
-`identity` - User and password, that can be required by ZooKeeper to give access to requested znodes . Optional. 

** 설정 예 ** 

```xml 
<zookeeper> 
    <node> 
        <host> example1 </ host> 
        <port > 2181 </ port> 
    </ node> 
    <node> 
        <host> example2 </ host> 
        <port> 2181 </ port> 
    </ node> 
    <session_timeout_ms> 30000 </ session_timeout_ms> 
    <operation_timeout_ms> 10000 </ operation_timeout_ms> 
    < !
/ identity> </ zookeeper> 
``` 
** 참조. **


- 복제 (../../ engines / table-engines / mergetree-family / replication.md) 
- [ZooKeeper 프로그래머 가이드 (http://zookeeper.apache.org/doc/current/zookeeperProgrammers.html) 

## use_minimalistic_part_header_in_zookeeper {# server-settings-use_minimalistic_part_header_in_zookeeper} 

ZooKeeper의 데이터 부분 헤더의 저장 방법. 

이 설정은`MergeTree` 가족이다 지정할 수 있습니다 : 

- 글로벌 [merge_tree (# server_configuration_parameters-merge_tree) 섹션`config.xml` 파일 

    ClickHouse는 서버에있는 모든 테이블의 설정을 사용합니다. 설정은 언제든지 변경할 수 있습니다. 기존의 테이블은 설정이 변경 될 때 동작을 변경합니다. 

- 각 테이블을 위해. 

    테이블을 만들 때, 해당하는 엔진 설정 (../../ engines / table-engines / mergetree-family / mergetree.md # table_engine-mergetree-creating a-table)이 설정을 가진 기존 테이블의 동작은 전역 설정이 변경 되어도 변경되지 않습니다. 

** 가능한 값 **

- 0 - Functionality is turned off. 
- 1 - Functionality is turned on. 

만약`use_minimalistic_part_header_in_zookeeper = 1` 후 복제 (../../ engines / table-engines / mergetree-family / replication.md) 테이블은 단일 데이터 부품의 헤더를 압축 저장합니다`znode` 테이블에 많은 열이 포함되어있는 경우이 저장 방법은 Zookeeper에 저장되는 데이터의 양을 크게 줄일 수 있습니다. 

!!! attention "주의" 
    신청 후`use_minimalistic_part_header_in_zookeeper = 1` ClickHouse 서버에만이 설정을 지원하지 않는 버전으로 다운 그레이드 할 수 없습니다. 그러자 업그레이드시주의 ClickHouse 서버 클러스터 업의 모든 서버입니다. ClickHouse의 새로운 버전을 테스트하려면 테스트 환경 또는 클러스터의 몇몇 서버에서 테스트하는 것이 안전합니다.  
## disable_internal_dns_cache {# server-settings-disable-internal-dns-cache}

      Data part headers already stored with this setting can not be restored to their previous (non-compact) representation. 

** 기본값 : ** 0. 


내부 DNS 캐시를 비활성화합니다. 시스템의 작동 ClickHouse 위해 추천되는 
자주 변경되는 인프라 등의 Kubernetes. 

** 기본값 : ** 0. 

## dns_cache_update_period {# server-settings-dns-cache-update-period} 

ClickHouse 내부 DNS 캐시 에 저장되어있는 IP 주소의 갱신 기간 (초)입니다. 
업데이트는 다른 시스템 스레드에서 비동기 적으로 실행됩니다. 

** 기본값 ** : 15. 

## access_control_path {#access_control_path} 

빠호루다이 ClickHouse 서버 점포 사용자 역할 구성에서 만든 SQL 명령. 

기본값 :`/ var / lib / clickhouse / access /`. 

** 참조 . ** 

- 액세스 제어 및 계정 관리 (../ access-rights.md # access-control)

원본 기사 (https://clickhouse.tech/docs/en/operations/server_configuration_parameters/settings/) <! - hide ->
