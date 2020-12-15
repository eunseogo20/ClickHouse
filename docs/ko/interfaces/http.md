---
machine_translated : true
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd
toc_priority : 19
toc_title : "HTTP \ u30A4 \ u30F3 \ u30BF \ u30FC \ u30D5 \ u30A7"
---

# HTTP 인터페이스 {# http-interface}

HTTP 사이트의 이용 ClickHouse 플랫폼에서 이른바 프로그래밍 언어입니다. Java 나 Perl 쉘 스크립트에서의 작업에 사용합니다. 다른 부문에서는 Http 인터페이스는 Perl, Python, Go에서 사용됩니다. HTTP이

기본적으로 clickhouse-server는 포트 8123에서 HTTP를 수신합니다 (이것은 설정에서 변경할 수 있습니다).

매개 변수없이 GET / request를하면 200 응답 코드에 정의 된 문자열을 반환합니다 [http_server_default_response] (../ operations / server-configuration-parameters / settings.md # server_configuration_parameters-http_server_default_response) 기본값 "Ok"(마지막에 개행이 있습니다)

```bash
$ curl 'http : // localhost : 8123 /'
Ok.
```

건강 체크 스크립트에서 GET / ping 요청을 사용합니다. 이 핸들러는 항상 "Ok"(마지막 줄 바꿈 포함). 버전 18.12.13에서 사용할 수 있습니다.

```bash
$ curl 'http : // localhost : 8123 / ping'
Ok.
```

요청을 URL로 보내는 'query'매개 변수 또는 포스트로. 또는 쿼리의 시작을 'query'매개 변수 및 게시물의 나머지 부분 (이것이 필요한 이유는 나중에 설명합니다). URL의 크기는 16KB로 제한되어 있기 때문에 큰 쿼리를 보낼 때이 점에주의하십시오.

성공하면 200 응답 코드와 결과 응답 본문에 표시됩니다.
오류가 발생하면 응답 본문에 500 응답 코드와 오류 설명 텍스트가 표시됩니다.

GET 메서드를 사용하는 경우, 'readonly'설정되어 있습니다. 즉, 데이터를 변경하는 쿼리는 POST 메서드 만 사용할 수 있습니다. 쿼리 자체는 POST 본문 또는 URL 매개 변수 중 하나를 보낼 수 있습니다.

예 :

```bash
$ curl 'http : // localhost : 8123 /? query = SELECT % 201'
1

$ wget -nv -O- 'http : // localhost : 8123 /? query = SELECT 1'
1

$ echo -ne 'GET /? query = SELECT % 201 HTTP / 1.0 \ r \ n \ r \ n'| nc localhost 8123
HTTP / 1.0 200 OK
Date : Wed, 27 Nov 2019 10:30:18 GMT
Connection : Close
Content-Type : text / tab-separated-values; charset = UTF-8
X-ClickHouse-Server-Display-Name : clickhouse.ru-central1.internal
X-ClickHouse-Query-Id : 5abe861c-239c-467f-b955-8a201abb8b7f
X-ClickHouse-Summary : { "read_rows": "0", "read_bytes": "0", "written_rows": "0", "written_bytes": "0", "total_rows_to_read": "0"}

1
```

보시는 바와 같이 칼은 다소 불편하지만 그 공간하는 URL이 자동으로 이스케이프됩니다.
Wget은 모든 자체를 탈출하지만 keep-alive과 Transfer-Encoding : chunked를 사용하는 경우 HTTP1.1보다 잘 작동하지 않기 때문에 사용하는 것은 권장하지 않습니다.

```bash
$ echo 'SELECT 1'| curl 'http : // localhost : 8123 /'--data-binary @ -
1

$ echo 'SELECT 1'| curl 'http : // localhost : 8123 /? query ='--data-binary @ -
1

$ echo '1'| curl 'http : // localhost : 8123 /? query = SELECT'--data-binary @ -
1
```

쿼리의 일부가 매개 변수로 전송 된 게시물의 일부가 전송되면이 두 데이터 부분 사이에 줄 바꿈을 삽입합니다.
예 (이것은 작동하지 않습니다) :

```bash
$ echo 'ECT 1'| curl 'http : // localhost : 8123 /? query = SEL'--data-binary @ -
Code : 59, e.displayText () = DB :: Exception : Syntax error : failed at position 0 : SEL
ECT 1
, expected One of : SHOW TABLES, SHOW DATABASES, SELECT, INSERT, CREATE ATTACH, RENAME, DROP DETACH, USE, SET OPTIMIZE., e.what () = DB :: Exception
```

기본적으로 데이터는 TabSeparated 형식으로 반환됩니다 (자세한 내용은 "Formats"섹션).
다른 형식을 요구하는 쿼리의 FORMAT 절을 사용합니다.

```bash
$ echo 'SELECT 1 FORMAT Pretty'| curl 'http : // localhost : 8123 /?'--data-binary @ -
┏━━━┓
┃ 1 ┃
┡━━━┩
│ 1 │
└───┘
```

데이터를 보낼 POST 메서드는 INSERT 쿼리에 필요합니다. 이 경우 URL 매개 변수 쿼리의 시작을 설명하고 POST를 사용하여 삽입 할 데이터를 전달할 수 있습니다. 삽입하는 데이터는 예를 들어, MySQL에서 탭으로 구분 된 덤프입니다. 이렇게하여 INSERT 쿼리는 MYSQL에서 LOAD DATA LOCAL INFILE을 바꿉니다.

예 : 테이블 만들기 :

```bash
$ echo 'CREATE TABLE t (a UInt8) ENGINE = Memory'| curl 'http : // localhost : 8123 /'--data-binary @ -
```

데이터 삽입을위한 친숙한 INSERT 쿼리 사용 :

```bash
$ echo 'INSERT INTO t VALUES (1), (2), (3)'| curl 'http : // localhost : 8123 /'--data-binary @ -
```

데이터 쿼리와는 별도로 제출할 수 있습니다 :

```bash
$ echo '(4), (5), (6)'| curl 'http : // localhost : 8123 /? query = INSERT % 20INTO % 20t % 20VALUES'--data-binary @ -
```

모든 데이터 형식을 지정할 수 있습니다. 그 'Values'형식은 INSERT를 t 값에 쓸 때 사용되는 서식과 같습니다 :

```bash
$ echo '(7), (8), (9)'| curl 'http : // localhost : 8123 /? query = INSERT % 20INTO % 20t % 20FORMAT % 20Values'--data-binary @ -
```

탭으로 구분 된 덤프에서 데이터를 삽입하려면 해당 형식을 지정합니다 :

```bash
$ echo -ne '10 \ n11 \ n12 \ n '| curl'http : // localhost : 8123 /? query = INSERT % 20INTO % 20t % 20FORMAT % 20TabSeparated '--data-binary @ -
```

테이블의 내용을 읽는다. 병렬 쿼리 처리에 의해 데이터가 무작위로 출력됩니다 :

```bash
$ curl 'http : // localhost : 8123 /? query = SELECT % 20a % 20FROM % 20t'
7
8
9
10
11
12
1
2
3
4
5
6
```

테이블을 삭제합니다.

```bash
$ echo 'DROP TABLE t'| curl 'http : // localhost : 8123 /'--data-binary @ -
```

데이터 테이블을 반환하지 않는 정상적인 요청의 경우 빈 응답 본문을 반환합니다.

데이터를 전송할 때 내부 ClickHouse 압축 형식을 사용할 수 있습니다. 압축 된 데이터는 비표준 형식이며, 특별한 형식을 사용해야합니다`clickhouse-compressor` 그래서 작동하도록 프로그램 (그것은`clickhouse-client` 패키지). 데이터 삽입의 효율성을 높이기 위해 다음을 사용하여 서버 측의 체크섬 검증을 비활성화 할 수 있습니다 [http_native_compression_disable_checksumming_on_decompress] (../ operations / settings / settings.md # settings-http_native_compression_disable_checksumming_on_decompress) 설정.

지정한 경우`compress = 1` URL은 서버에서 보내는 데이터를 압축합니다.
지정한 경우`decompress = 1`이 URL은 서버는 전달되는 데이터와 동일한 데이터를 압축합니다. `POST` 방법.

또한 사용할 수 있습니다 [HTTP 압축 (https://en.wikipedia.org/wiki/HTTP_compression) 압축을 보내려면`POST` 요청 헤더를 추가합니다`Content-Encoding : compression_method` . ClickHouse가 응답을 압축하려면 다음과 같이 추가해야합니다`Accept-Encoding : compression_method`. ClickHouse 지원`gzip``br`, and`deflate` 압축 방법 (https : // en.wikipedia.org/wiki/HTTP_compression#Content-Encoding_tokens) HTTP 압축을 사용하려면 ClickHouse를 사용해야합니다 [enable_http_compression] (../ operations / settings / settings.md # settings-enable_http_compression) 설정. 데이터 압축 수준은 http_zlib_compression_level (# settings-http_zlib_compression_level) 모든 압축 방법을 설정합니다.

이용할 수 감소 네트워크 통신의 송수신에 대량의 데이터를 덤프하면 즉시 압축됩니다.

압축에 의한 데이터 전송의 예 :

```bash
#Sending data to the server :
$ curl -vsS "http : // localhost : 8123 /? enable_http_compression = 1"-d 'SELECT number FROM system.numbers LIMIT 10'-H 'Accept-Encoding : gzip'

#Sending data to the client :
$ echo "SELECT 1"| gzip -c | curl -sS --data-binary @ - -H 'Content-Encoding : gzip' 'http : // localhost : 8123 /'
```

!!! note "주"
    있는 HTTP 고객이 압축 데이터에서 서버에 의한 기본 (`gzip`와`deflate`) 압축 설정을 올바르게 사용하여 압축 해제 된 데이터를 얻을 수 있습니다.

를 사용 할 수 있습니다 'database'기본 데이터베이스를 지정하는 URL 매개 변수.

```bash
$ echo 'SELECT number FROM numbers LIMIT 10'| curl 'http : // localhost : 8123 /? database = system'--data-binary @ -
0
1
2
3
4
5
6
7
8
9
```

기본적으로 서버 설정에 등록 된 데이터베이스가 기본 데이터베이스로 사용됩니다. 기본적으로이 데이터베이스 'default'또는 테이블 이름 앞에 마침표를 사용하여 데이터베이스를 항상 지정할 수 있습니다.

사용자 이름과 암호는 다음 중 하나의 방법으로 지정할 수 있습니다 :

1. HTTP 기본 인증을 사용합니다. 예 :

<! - ->

```bash
$ echo 'SELECT 1'| curl 'http : // user : password @ localhost : 8123 /'-d @ -
```

1.에서 'user'과 'password'URL 매개 변수. 예 :

<! - ->

```bash
$ echo 'SELECT 1'| curl 'http : // localhost : 8123 /? user = user & password = password'-d @ -
```

1.를 사용하여 'X-ClickHouse-User'와 'X-ClickHouse-Key'헤더. 예 :

<! - ->

```bash
$ echo 'SELECT 1'| curl -H 'X-ClickHouse-User : user'-H 'X-ClickHouse-Key : password'http : // localhost : 8123 / '-d @ -
```

사용자 이름이 지정되어 있지 않은 경우,`default` 이름이 사용됩니다. 암호를 지정하지 않으면 빈 암호가 사용됩니다.
에 사용하실 수 있습니다 URL 매개 변수에서 지정한 설정 처리의 단일 쿼리 또는 전체 프로필을 설정합니다. 예 : http : // localhost : 8123 /? profile = web & max_rows_to_read = 1000000000 & query = SELECT + 1

자세한 내용은을 참조하십시오 [설정] (../ operations / settings / index.md) 섹션

```bash
$ echo 'SELECT number FROM system.numbers LIMIT 10'| curl 'http : // localhost : 8123 /?'--data-binary @ -
0
1
2
3
4
5
6
7
8
9
```

에 대한 정보 기타 매개 변수 섹션을 참조하십시오 "SET".

마찬가지로, Http 프로토콜 ClickHouse 세션을 사용할 수 있습니다. 이렇게하기 위해서는`session_id` 요청 매개 변수를 가져옵니다. 세션 ID로 임의의 문자열을 사용할 수 있습니다. 기본적으로 세션은 비활성 60 초 후에 종료합니다. 이 제한을 변경하려면`default_session_timeout` 서버 구성에서 설정하거나`session_timeout` 요청 매개 변수를 가져옵니다. 세션 상태를 확인하려면`session_check = 1` 매개 변수. 단일 세션에서 한 번에 하나의 쿼리 만 수행 할 수 있습니다.

쿼리의 진행 상황에 대한 정보를받을 수 있습니다`X-ClickHouse-Progress` 응답 헤더. 이렇게하려면 사용합니다 [send_progress_in_http_headers] (../ operations / settings / settings.md # settings-send_progress_in_http_headers) 헤더 시퀀스의 예 :

```text
X-ClickHouse-Progress : { "read_rows": "2752512", "read_bytes": "240570816" "total_rows_to_read": "8880128"}
X-ClickHouse-Progress : { "read_rows": "5439488", "read_bytes": "482285394" "total_rows_to_read": "8880128"}
X-ClickHouse-Progress : { "read_rows": "8783786", "read_bytes": "819092887" "total_rows_to_read": "8880128"}
```

가능한 헤더 항목 :

-`read_rows` - ​​Number of rows read.
-`read_bytes` - Volume of data read in bytes.
-`total_rows_to_read` - Total number of rows to be read.
-`written_rows` - ​​Number of rows written.
-`written_bytes` - Volume of data written in bytes.

HTTP 연결이 끊어져도 요청 실행이 자동으로 중지하지 않습니다. 분석과 데이터의 서식은 서버 측에서 실행되며 네트워크를 사용하면 무효가 될 수 있습니다.
어떤 'query_id'매개 변수는 쿼리 ID (임의의 문자열)로 전달할 수 있습니다. 자세한 내용은 "Settings, replace_running_query"

어떤 'quota_key'매개 변수로 전달할 수 쿼터 키 (전환 문자열) 자세한 내용은 "Quotas"

HTTP 나 자세한 내용은 "External data for query processing".

## 응답 버퍼링 {# response-buffering}

서버 측에서 응답 버퍼링을 활성화 할 수 있습니다. 그`buffer_size`와`wait_end_of_query` URL 매개 변수를 제공합니다.

`buffer_size` 서버 메모리에 버퍼링하는 결과의 바이트 수를 결정합니다. 결과의 본문이 임계 값보다 큰 경우, 버퍼는 HTTP 채널에 기록되고 나머지 데이터는 HTTP 채널로 직접 전송됩니다.

보전의 대응은 버퍼 처리되지만, 설정`wait_end_of_query = 1`이 경우 메모리에 저장되지 않은 데이터는 임시 서버 파일에 버퍼됩니다.

예 :

```bash
$ curl -sS 'http : // localhost : 8123 /? max_result_bytes = 4000000 & buffer_size = 3000000 & wait_end_of_query = 1'-d 'SELECT toUInt8 (number) FROM system.numbers LIMIT 9000000 FORMAT RowBinary'
```

사용 버퍼링 등으로 쿼리 처리 오류가 발생한 후에는 응답 코드, HTTP 헤더가 전송됩니다. 이 상황에서는 응답 본문의 마지막에 오류 메시지가 기록되고 클라이언트 측에서 오류 분석 단계에서만 찾을 수 있습니다.

### 매개 {# cli-queries-with-parameters}

매개 변수를 사용하여 쿼리를 작성하고 해당 HTTP 요청 매개 변수에서 값을 전달할 수 있습니다. 자세한 내용은 CLI 매개 변수를 사용하여 조회 (cli.md # cli-queries-with-parameters).

### 예 {#example}

```bash
$ curl -sS "<address>? param_id = 2 & param_phrase = test"-d "SELECT * FROM table WHERE int_column = {id : UInt8} and string_column = {phrase : String}"
```

## 소정의 HTTP 스 {#predefined_http_interface}

ClickHouse 지 특정 쿼리는 HTTP 인터페이스. 예를 들어, 다음과 같이 테이블에 데이터를 쓸 수 있습니다 :

```bash
$ echo '(4), (5), (6)'| curl 'http : // localhost : 8123 /? query = INSERT % 20INTO % 20t % 20VALUES'--data-binary @ -
```

ClickHouse에 해당하는 소정의 HTTP 인터페이스 수 고마워 통합 제삼자의 리딩 프로젝트 [프로 메테우스 수출 (https://github.com/percona-lab/clickhouse_exporter).

예 :

- 먼저이 부분을 서버 설정 파일에 추가합니다 :

<! - ->

```xml
<http_handlers>
    <rule>
        <url> / predefined_query </ url>
        <methods> POST, GET </ methods>
        <handler>
            <type> predefined_query_handler </ type>
            <query> SELECT * FROM system.metrics LIMIT 5 FORMAT Template SETTINGS format_template_resultset = 'prometheus_template_output_format_resultset', format_template_row = 'prometheus_template_output_format_row', format_template_rows_between_delimiter = '\ n'</ query>
        </ handler>
    </ rule>
    <rule> ... </ rule>
    <rule> ... </ rule>
</ http_handlers>
```

- Prometheus 형식의 데이터 url을 직접 요청할 수있게되었습니다 :

<! - ->

```bash
$ curl -v 'http : // localhost : 8123 / predefined_query'
* Trying : 1 ...
* Connected to localhost (:: 1) port 8123 (# 0)
> GET / predefined_query HTTP / 1.1
> Host : localhost : 8123
> User-Agent : curl / 7.47.0
> Accept : * / *
>
<HTTP / 1.1 200 OK
<Date : Tue, 28 Apr 2020 08:52:56 GMT
<Connection : Keep-Alive
<Content-Type : text / plain; charset = UTF-8
<X-ClickHouse-Server-Display-Name : i-mloy5trc
<Transfer-Encoding : chunked
<X-ClickHouse-Query-Id : 96fe0052-01e6-43ce-b12a-6b7370de6e8a
<X-ClickHouse-Format : Template
<X-ClickHouse-Timezone : Asia / Shanghai
<Keep-Alive : timeout = 3
<X-ClickHouse-Summary : { "read_rows": "0", "read_bytes": "0", "written_rows": "0", "written_bytes": "0", "total_rows_to_read": "0"}
<
# HELP "Query" "Number of executing queries"
# TYPE "Query"counter
"Query"1

# HELP "Merge" "Number of executing background merges"
# TYPE "Merge"counter
"Merge"0

# HELP "PartMutation" "Number of mutations (ALTER DELETE / UPDATE)"
# TYPE "PartMutation"counter
"PartMutation"0

# HELP "ReplicatedFetch" "Number of data parts being fetched from replica"
# TYPE "ReplicatedFetch"counter
"ReplicatedFetch"0

# HELP "ReplicatedSend" "Number of data parts being sent to replicas"
# TYPE "ReplicatedSend"counter
"ReplicatedSend"0

* Connection # 0 to host localhost left intact


* Connection # 0 to host localhost left intact
```

이 예에서 볼 수 있듯이`<http_handlers>`설정으로 구성되어 있습니다. xml 파일과`<http_handlers>`를 포함 할 수 많은`<rule> s`. ClickHouse는 수신 된 HTTP 요청을 사전 정의 된 유형과 일치합니다`<rule>`처음 일치하는 핸들러가 실행됩니다. 일치가 성공하면 ClickHouse 대응하는 사전 정의 된 쿼리를 실행합니다.

> 이제`<rule>`구성 할 수 있습니다`<method>``<headers>``<url>``<handler>`:
>`<method>`HTTP 요청 메소드 부분의 조합을 담당합니다. `<method>`충분히 정의에 부합합니다 [방법] (https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods) HTTP 프로토콜에서. 이 옵션의 구성입니다. 구성 파일에 정의되어 있지 않은 경우, HTTP 요청 메소드 부분과 일치하지 않습니다.
>
>`<url>`HTTP 요청의 url 부분의 조합을 담당합니다. 그것은과 호환됩니다 [RE2 (https://github.com/google/re2) 정규 표현식. 이 옵션의 구성입니다. 구성 파일에 정의되어 있지 않은 경우, HTTP 요청의 url 부분과 일치하지 않습니다.
>
>`<headers>`HTTP 요청 헤더 부분의 조합을 담당합니다. RE2 정규 표현식과 호환됩니다. 이 옵션의 구성입니다. 구성 파일에 정의되어 있지 않은 경우 HTTP 요청 헤더 부분과 일치하지 않습니다.
>
>`<handler>`주요 처리 부품을 포함하고 있습니다. 그럼`<handler>`구성 할 수 있습니다`<type>``<status>``<content_type>``<response_content>``<query>``<query_param_name>`.
> \>`<type>`현재 지원 : ** predefined_query_handler ** ** dynamic_query_handler ** ** 정적 **.
> \>
> \>`<query>`-predefined_query_handler 형으로 사용하여 핸들러가 호출 될 때 쿼리를 실행합니다.
> \>
> \>`<query_param_name>`-dynamic_query_handler 형으로 사용하면 해당 값을 추출하여 실행합니다. `<query_param_name>`HTTP 요청 파라미터의 값.
> \>
> \>`<status>`- 정적 유형, 응답 상태 코드로 사용합니다.
> \>
> \>`<content_type>`- 정적 유형 응답 및 사용 내용 형식 (https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Type).
> \>
> \>`<response_content>`사용 정지형에서 응답이 송신 (발신) 한 컨텐츠를 클라이언트로 사용하는 경우, 접두사 'file : //'또는 'config : //'클라이언트에 보내기 파일 또는 구성에서 내용을 검색합니다.

다음 다른 설정 방법을 설명합니다`<type>`.

## predefined_query_handler {#predefined_query_handler}

`<predefined_query_handler>`설정과 query_params 값 설정을 지원합니다. 설정할 수 있습니다`<query>`유형으로`<predefined_query_handler>`.

`<query>`값은 다음 정의 된 쿼리입니다`<predefined_query_handler>`이것은 Http 요청이 일치 쿼리 결과를 반환 할 때 ClickHouse 의해 실행됩니다. 이것은 필수 구성입니다.

다음의 예는 다음 값을 정의합니다`max_threads`와`max_alter_threads` 설정하고 쿼리 테이블에서 설정 설정합니다.

예 :

```xml
<http_handlers>
    <rule>
        <url> <! [CDATA [/ query_param_with_url / \ w + / (? P <name_1> ^ / +) (/ (? P <name_2> ^ / +))?]]> </ url>
        <method> GET </ method>
        <headers>
            <XXX> TEST_HEADER_VALUE </ XXX>
            <PARAMS_XXX> <! [CDATA [(? P <name_1> ^ / +) (/ (? P <name_2> ^ / +))?]]> </ PARAMS_XXX>
        </ headers>
        <handler>
            <type> predefined_query_handler </ type>
            <query> SELECT value FROM system.settings WHERE name = {name_1 : String} </ query>
            <query> SELECT name, value FROM system.settings WHERE name = {name_2 : String} </ query>
        </ handler>
    </ rule>
</ http_handlers>
```

```bash
$ curl -H 'XXX : TEST_HEADER_VALUE'-H 'PARAMS_XXX : max_threads'http : // localhost : 8123 / query_param_with_url / 1 / max_threads / max_alter_threads? max_threads = 1 & max_alter_threads = 2 '
1
max_alter_threads 2
```

!!! note "주의"
    하나`<predefined_query_handler>`하나만을 지원`<query>`삽입 타입.

## dynamic_query_handler {#dynamic_query_handler}

에서`<dynamic_query_handler>`쿼리는 HTTP 요청 param 형식으로 쓰여져 있습니다. 차이점은`<predefined_query_handler>`쿼리는 설정 파일에 기록됩니다. 설정할 수 있습니다`<query_param_name>`에서`<dynamic_query_handler>`.

클릭 하우스는`<query_param_name>`HTTP 요청의 url 값. 기본값`<query_param_name>`는`/ query`. 이것은 옵션 구성입니다. 설정 파일에 정의가 없으면 param은 전달되지 않습니다.

이 기능을 시험하기 위해이 예제에서는 max_threads과 max_alter_threads 값을 정의하고 설정이 제대로 설정되었는지 여부를 조회합니다.

예 :

```xml
<http_handlers>
    <rule>
    <headers>
        <XXX> TEST_HEADER_VALUE_DYNAMIC </ XXX> </ headers>
    <handler>
        <type> dynamic_query_handler </ type>
        <query_param_name> query_param </ query_param_name>
    </ handler>
    </ rule>
</ http_handlers>
```

```bash
$ curl -H 'XXX : TEST_HEADER_VALUE_DYNAMIC'http : // localhost : 8123 / own? max_threads = 1 & max_alter_threads = 2 & param_name_1 = max_threads & param_name_2 = max_alter_threads & query_param = SELECT % 20name, value % 20FROM % 20system.settings % 20where % 20name 20 % = 20 % % 7Bname_1 : String % 7D % 20OR % 20name % 20 = % 20 % 7Bname_2 : String % 7D '
max_threads 1
max_alter_threads 2
```

## 정적 {#static}

`<static>`돌아갈 [content_type] (https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Type), 상태 (https://developer.mozilla.org / en-US / docs / Web / HTTP / Status) 그리고 response_content. response_content은 지정된 내용을 반환 할 수 있습니다

예 :

메시지를 반환한다.

```xml
<http_handlers>
        <rule>
            <methods> GET </ methods>
            <headers> <XXX> xxx </ XXX> </ headers>
            <url> / hi </ url>
            <handler>
                <type> static </ type>
                <status> 402 </ status>
                <content_type> text / html; charset = UTF-8 </ content_type>
                <response_content> Say Hi! </ response_content>
            </ handler>
        </ rule>
<http_handlers>
```

```bash
$ curl -vv -H 'XXX : xxx' 'http : // localhost : 8123 / hi'
* Trying : 1 ...
* Connected to localhost (:: 1) port 8123 (# 0)
> GET / hi HTTP / 1.1
> Host : localhost : 8123
> User-Agent : curl / 7.47.0
> Accept : * / *
> XXX : xxx
>
<HTTP / 1.1 402 Payment Required
<Date : Wed, 29 Apr 2020 03:51:26 GMT
<Connection : Keep-Alive
<Content-Type : text / html; charset = UTF-8
<Transfer-Encoding : chunked
<Keep-Alive : timeout = 3
<X-ClickHouse-Summary : { "read_rows": "0", "read_bytes": "0", "written_rows": "0", "written_bytes": "0", "total_rows_to_read": "0"}
<
* Connection # 0 to host localhost left intact
Say Hi! %
```

컨텐츠로 구성 보냅니다.

```xml
<get_config_static_handler> <! [CDATA [<html ng-app = "SMI2"> <head> <base href = "http://ui.tabix.io/"> </ head> <body> <div ui-view = ""class = "content-ui"> </ div> <script src = "http://loader.tabix.io/master.js"> </ script> </ body> </ html>]]> </ get_config_static_handler>

<http_handlers>
        <rule>
            <methods> GET </ methods>
            <headers> <XXX> xxx </ XXX> </ headers>
            <url> / get_config_static_handler </ url>
            <handler>
                <type> static </ type>
                <response_content> config : // get_config_static_handler </ response_content>
            </ handler>
        </ rule>
</ http_handlers>
```

```bash
$ curl -v -H 'XXX : xxx' 'http : // localhost : 8123 / get_config_static_handler'
* Trying : 1 ...
* Connected to localhost (:: 1) port 8123 (# 0)
> GET / get_config_static_handler HTTP / 1.1
> Host : localhost : 8123
> User-Agent : curl / 7.47.0
> Accept : * / *
> XXX : xxx
>
<HTTP / 1.1 200 OK
<Date : Wed, 29 Apr 2020 04:01:24 GMT
<Connection : Keep-Alive
<Content-Type : text / plain; charset = UTF-8
<Transfer-Encoding : chunked
<Keep-Alive : timeout = 3
<X-ClickHouse-Summary : { "read_rows": "0", "read_bytes": "0", "written_rows": "0", "written_bytes": "0", "total_rows_to_read": "0"}
<
* Connection # 0 to host localhost left intact
<html ng-app = "SMI2"> <head> <base href = "http://ui.tabix.io/"> </ head> <body> <div ui-view = ""class = "content- ui "> </ div> <script src ="http://loader.tabix.io/master.js "> </ script> </ body> </ html> %
```

클라이언트로 전송하는 파일의 내용을 검색합니다.

```xml
<http_handlers>
        <rule>
            <methods> GET </ methods>
            <headers> <XXX> xxx </ XXX> </ headers>
            <url> / get_absolute_path_static_handler </ url>
            <handler>
                <type> static </ type>
                <content_type> text / html; charset = UTF-8 </ content_type>
                <response_content> file : ///absolute_path_file.html </ response_content>
            </ handler>
        </ rule>
        <rule>
            <methods> GET </ methods>
            <headers> <XXX> xxx </ XXX> </ headers>
            <url> / get_relative_path_static_handler </ url>
            <handler>
                <type> static </ type>
                <content_type> text / html; charset = UTF-8 </ content_type>
                <response_content> file : //./relative_path_file.html </ response_content>
            </ handler>
        </ rule>
</ http_handlers>
```

```bash
$ user_files_path = '/ var / lib / clickhouse / user_files'
$ sudo echo "<html> <body> Relative Path File </ body> </ html>"> $ user_files_path / relative_path_file.html
$ sudo echo "<html> <body> Absolute Path File </ body> </ html>"> $ user_files_path / absolute_path_file.html
$ curl -vv -H 'XXX : xxx' 'http : // localhost : 8123 / get_absolute_path_static_handler'
* Trying : 1 ...
* Connected to localhost (:: 1) port 8123 (# 0)
> GET / get_absolute_path_static_handler HTTP / 1.1
> Host : localhost : 8123
> User-Agent : curl / 7.47.0
> Accept : * / *
> XXX : xxx
>
<HTTP / 1.1 200 OK
<Date : Wed, 29 Apr 2020 04:18:16 GMT
<Connection : Keep-Alive
<Content-Type : text / html; charset = UTF-8
<Transfer-Encoding : chunked
<Keep-Alive : timeout = 3
<X-ClickHouse-Summary : { "read_rows": "0", "read_bytes": "0", "written_rows": "0", "written_bytes": "0", "total_rows_to_read": "0"}
<
<html> <body> Absolute Path File </ body> </ html>
* Connection # 0 to host localhost left intact
$ curl -vv -H 'XXX : xxx' 'http : // localhost : 8123 / get_relative_path_static_handler'
* Trying : 1 ...
* Connected to localhost (:: 1) port 8123 (# 0)
> GET / get_relative_path_static_handler HTTP / 1.1
> Host : localhost : 8123
> User-Agent : curl / 7.47.0
> Accept : * / *
> XXX : xxx
>
<HTTP / 1.1 200 OK
<Date : Wed, 29 Apr 2020 04:18:31 GMT
<Connection : Keep-Alive
<Content-Type : text / html; charset = UTF-8
<Transfer-Encoding : chunked
<Keep-Alive : timeout = 3
<X-ClickHouse-Summary : { "read_rows": "0", "read_bytes": "0", "written_rows": "0", "written_bytes": "0", "total_rows_to_read": "0"}
<
<html> <body> Relative Path File </ body> </ html>
* Connection # 0 to host localhost left intact
```

원본 기사 (https://clickhouse.tech/docs/en/interfaces/http_interface/) <! - hide ->
