--- 
machine_translated : true 
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd 
toc_priority : 46 
toc_title : "\ u30C8 \ u30E9 \ u30D6 \ u30EB \ u30B7" 
--- 

# 제점 {#troubleshooting} 

- [설치] (# troubleshooting-installation-errors) 
- [ 서버에 연결 (# troubleshooting-accepts-no-connections) 
- 쿼리 처리 (# troubleshooting-does-not-process-queries) 
- 쿼리 처리의 효율성 (# troubleshooting-too-slow) 

## 설치 {# troubleshooting-installation-errors} 

### Apt-get은 clickHouse 저장소에서 Deb 패키지를 검색 할 수 없습니다 {# you-can not-get-deb-packages-from-clickhouse-repository-with-apt-get}
 
- 방화벽 설정
- 할 수없는 경우 액세스 저장소에 대한 어떤 이유로 다운로드 패키지에 명시된 [소개] (../ getting-started / index.md) 기사와를 사용하여 수동으로 설치`sudo dpkg -i <packages >`명령 또한 필요합니다`tzdata` 패키지. 

## 서버에 접속 {# troubleshooting-accepts-no-connections} 

가능한 문제 : 

- 서버가 실행되고 있지 않습니다. 
- 예기치 않은 또는 잘못된 구성 매개 변수. 

### 서버 실행 중에 {# server-is-not-running} 

** 서버가 runnnig 여부를 확인하는 ** 

명령 : 

```bash 
$ sudo service clickhouse-server status 
``` 

서버가 실행되고 않은 경우, 다음 명령을 실행합니다 : 

```bash 
$ sudo service clickhouse-server start 
``` 

** 로그 확인 ** 

메인 로그`clickhouse-server` 인`/ var / log / clickhouse-server / clickhouse-server.log` 기본적으로. 

서버가 정상적으로 시작하면 문자열이 나타납니다 :

-`<Information> Application : starting up.` - Server started. 
-`<Information> Application : Ready for connections.` - Server is running and ready for connections. 

만약`clickhouse-server` 설정 오류 때문에 실패했습니다 . `<Error>`오류에 대한 설명이 포함 된 문자열. 예 : 

```text 
2019.01.11 15 : 23 : 25.549505 [45] {} <Error> ExternalDictionaries : Failed reloading 'event2id'external dictionary : Poco :: Exception. Code : 1000, e.code () = 111, e .displayText () = Connection refused, e.what () = Connection refused 
``` 

파일의 마지막에 오류가 표시되지 않는 경우는 문자열로 시작하는 전체 파일을 검사합니다 : 

```text 
<Information> Application : starting up. 
``` 

당신이 두 번째 인스턴스를 시작하려고하면`clickhouse-server` 서버에는 다음 로그가 나타납니다 :
 
```text
Stop SignalListener thread ``` 
** 시스템을 참조하십시오. d 로그 **
 
2019.01.11 15 : 25 : 11.156686 [1] {} <Debug> Application : Uninitializing subsystem : Logging subsystem

 
에서 유용한 정보를 찾을 수없는 경우`clickhouse-server` 로그 또는 로그가 없으면 볼 수 있습니다`system .d` 명령을 사용하여 로그 : 

```bash 
$ sudo journalctl -u clickhouse-server 
``` 

** clickhouse-server를 대화 모드로 부팅 ** 

```bash 
$ sudo -u clickhouse / usr / bin / clickhouse-server --config-file /etc/clickhouse-server/config.xml 
``` 

이 프레임이 모드에서는`clickhouse-server` 판화의 모든 이벤트 메시지입니다. 

### 구성 매개 변수 {# configuration-parameters} 

체크 : 

- 독 노동자의 설정. 

    Docker에서 Clickhouse를 IPv6 네트워크에서 실행하는 경우 다음 사항을 확인하십시오`network = host` 설정되어 있습니다. 

- 엔드 포인트 설정.

    체크 [listen_host (server-configuration-parameters / settings.md # server_configuration_parameters-listen_host) 및 tcp_port (server-configuration-parameters / settings.md # server_configuration_parameters-tcp_port) 설정. 

    ClickHouse server는 기본적으로 전용 localhost 연결을 허용합니다. 

- HTTP 프로토콜 설정. 

    HTTP API의 프로토콜 설정을 확인합니다. 

- 안전한 연결 설정. 

    체크 : 

    - 그 [tcp_port_secure (server-configuration-parameters / settings.md # server_configuration_parameters-tcp_port_secure) 설정. 
    - 설정 [SSL 셀틱스 (server-configuration-parameters / settings.md # server_configuration_parameters-openssl) 

    적절한 매개 변수를 연결 예를 들어,`port_secure` 변수와`clickhouse_client`. 

- 사용자 설정. 

    키노 다른 사용자 이름과 암호입니다.

## 쿼리 처리 {# troubleshooting-does-not-process-queries} 

ClickHouse 쿼리를 처리 할 수없는 경우, 클라이언트 오류의 설명을 보냅니다. 에서`clickhouse-client` 콘솔에 오류 설명이 표시됩니다. HTTP 인터페이스를 사용하는 경우 ClickHouse는 응답 본문에 오류의 설명을 보냅니다. 예 : 

```bash 
$ curl 'http : // localhost : 8123 /'--data-binary "SELECT a" 
Code : 47, e.displayText () = DB :: Exception : Unknown identifier a. Note that there are no tables (FROM clause) in your query, context : required_names : 'a'source_tables : table_aliases : private_aliases : column_aliases : public_columns : 'a'masked_columns : array_join_columns : source_columns :, e.what () = DB :: Exception 
`` ` 

당신이 시작하면`clickhouse-client`와`stack-trace` 매개 변수 ClickHouse 오류에 대한 설명을 포함하는 서버 스택 추적을 반환합니다.

또는 메시지가 손상 연결합니다. 이 경우 쿼리를 반복 할 수 있습니다. 쿼리를 실행할 때마다 연결이 끊어진 경우, 서버 로그에서 오류를 확인합니다. 

## 쿼리 처리의 효율 {# troubleshooting-too-slow} 

ClickHouse 동작이 너무 느린 경우 쿼리 서버 자원과 네트워크의 부하를 프로파일해야합니다. 

Clickhouse-benchmark 유틸리티 초당 처리 된 쿼리 수, 초당 처리 된 행 수 및 쿼리 처리 시간 백분위 수가 표시됩니다.
