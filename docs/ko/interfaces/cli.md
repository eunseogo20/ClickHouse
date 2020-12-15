---
machine_translated : true
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd
toc_priority : 17
toc_title : "\ u30B3 \ u30DE \ u30F3 \ u30C9 \ u884C \ u30AF \ u30E9 \ u30A4 \ u30A2 \ u30F3 \ u30C8"
---

# 명령 행 클라이언트 {# command-line-client}

ClickHouse 스 네이티브 커맨드 라인 클라이언트 :`clickhouse-client`. 클라이언트 자세한 내용은 [설정] (# interfaces_cli_configuration).

[설치] (../ getting-started / index.md) 그리고`clickhouse-client` 패키지 명령으로 실행합니다`clickhouse-client`.

```bash
$ clickhouse-client
ClickHouse client version 19.17.1.1579 (official build).
Connecting to localhost : 9000 as user default.
Connected to ClickHouse server version 19.17.1 revision 54428.

:)
```

다른 클라이언트 및 서버 버전과 호환 일부 기능을 사용할 수없는 오래된 클라이언트. 사용하시는 것을 권장합니다 동일한 버전의 클라이언트 서버 응용 프로그램입니다. 이전 버전의 클라이언트를 사용하려고하면 서버`clickhouse-client` 메시지를 표시한다 :

      ClickHouse client version is older than ClickHouse server. It may lack support for new features.

## 사용법 {#cli_usage}

클라이언트는 대화 형 및 비대화 형 (일괄) 모드에서 사용할 수 있습니다. 배치 모드를 사용하려면 'query'변수 또는 데이터에 보냅니다 'stdin'(이것은 'stdin'은 단말기입니다), 또는 둘 다. HTTP 인터페이스와 마찬가지로, 'query'변수 및 데이터를로 보냅니다 'stdin'는 요청의 연결이다. 'query'매개 변수 줄 바꿈 및 데이터 'stdin'이것은 대규모 INSERT 쿼리에 유용합니다.

클라이언트를 사용하여 데이터를 삽입하는 예 :

```bash
$ echo -ne "1 'some text', '2016-08-14 00 : 00 : 00'\ n2 'some more text', '2016-08-14 00:00:01'"| clickhouse-client --database = test --query = "INSERT INTO test FORMAT CSV";

$ cat << _ EOF | clickhouse-client --database = test --query = "INSERT INTO test FORMAT CSV";
3 'some text', '2016-08-14 00:00:00'
4 'some more text', '2016-08-14 00:00:01'
_EOF

$ cat file.csv | clickhouse-client --database = test --query = "INSERT INTO test FORMAT CSV";
```

배치 모드에서는 기본 데이터 형식은 TabSeparated입니다. 쿼리 FORMAT 절에서 서식을 설정할 수 있습니다.

기본적으로 배치 모드에서만 단일 쿼리를 처리 할 수 ​​있습니다. 여러 쿼리를 만들려면 "script"사용하는`--multiquery` 매개 변수. 따라서 모든 쿼리를 제거를 지원하지 않습니다. 쿼리 결과는 추가의 구분없이 연속적으로 출력됩니다. 마찬가지로 다수의 쿼리를 처리하려면 다음과 같이 실행할 수 있습니다 'clickhouse-client'각 쿼리에 대해. 시작은 수십 밀리 초 걸리는 점에 유의하십시오. 'clickhouse-client'프로그램

대화식 모드에서는 쿼리를 입력 할 수있는 명령 줄을 가져옵니다.

만약 'multiline'지정되지 않은 (기본) : 쿼리를 실행하려면 Enter 키를 누릅니다. 쿼리의 끝에 세미콜론이 필요하지 않습니다. 여러 줄의 쿼리를 입력하려면 백 슬래시를 입력합니다`\`줄 바꿈 전에. Enter 키를 누르면 쿼리의 다음 줄을 입력하도록 요구됩니다.

여러 행이 지정되어있는 경우 쿼리를 실행하려면 세미콜론으로 종료 한 다음 Enter 키를 누릅니다. 입력 한 행의 끝에 세미콜론이 생략되는 경우 쿼리의 다음 줄을 입력하라는 메시지가 표시됩니다.

단일 쿼리 만 실행되므로 세미콜론 뒤에 모두 무시됩니다.

다음을 지정할 수 있습니다`\ G` 세미콜론 대신 또는 그. 이것은 수직 형식을 나타냅니다. 이 형식은 각 값은 별도의 줄에 인쇄됩니다. 이 특별한 기능은 MySQL CLI와의 호환성을 위해 추가되었습니다.

명령 줄에 근거한 'replxx'(유사 'readline'). 즉, 익숙한 키보드 단축키를 사용하여 기록을 유지합니다. 역사에 기록되어 있습니다`~ / .clickhouse-client-history`.

기본적으로 사용되는 형식은 PrettyCompact입니다. 쿼리 FORMAT 절에서 서식을 변경하거나 다음과 같이 지정할 수 있습니다`\ G` 쿼리의 마지막에`--format` 또는`--vertical` 명령 줄 인수 또는 클라이언트 구성 파일의 사용.

클라이언트를 종료하려면 Ctrl + D (또는 Ctrl + C)를 누르거나 쿼리 대신 다음 중 하나를 입력합니다 : "exit", "quit", "logout", "exit;" "quit;" "logout;", "q", "Q", ": q"

이 처리 쿼리 클라이언트를 보여 :

1. (기본적으로) 초당 10 회를 넘지 않도록 업데이트됩니다. 빠른 쿼리의 경우 진행률을 표시 할 시간이없는 경우가 있습니다.
2. 분석 후 디버그 용으로 포맷 된 쿼리.
3. 지정된 형식의 결과.
4. 결과의 행수 경과 된 시간 및 쿼리 처리의 평균 속도.

그러나 서버가 요청을 중단하는 것을 조금 기다려야합니다. 특정 단계에서 쿼리를 취소 할 수 없습니다. 다시 기다리고 Ctrl + C를 누르지 않으면 클라이언트가 종료됩니다.

명령 줄 자세한 내용은 "External data for query processing".

### 매개 {# cli-queries-with-parameters}

를 만들 수 쿼리 매개 변수 및 경로 값에서의 알려진 클라이언트 응용 프로그램.이를 피하기 형식의 쿼리가 특정 동적 가치를 클라이언트 측에서 이루어집니다. 예를 들면 :

```bash
$ clickhouse-client --param_parName = "[1, 2]"-q "SELECT * FROM table WHERE a = {parName : Array (UInt16)}"
```

#### 쿼리 구문 {# cli-queries-with-parameters-syntax}

정상적으로 쿼리 서식하고 응용 프로그램 매개 변수에서 쿼리에 전달되는 값을 다음 형식 중 괄호로 묶습니다 :

```sql
{<name> : <data type>}
```

-`name` - Placeholder identifier. In the console client it should be used in app parameters as`--param_ <name> = value`.
-`data type` - 데이터 형식 (../ sql-reference / data-types / index.md) 응용 프로그램의 매개 변수 값의 예를 들어 다음과 같은 데이터 구조`(integer ( 'string', integer) )`를 가질 수`Tuple (UInt8, Tuple (String, UInt8))`데이터 형 (다른 데이터 형식도 사용할 수 있습니다 정수 (../ sql-reference / data-types / int-uint. md) 유형).

#### 예 {#example}

```bash
$ clickhouse-client --param_tuple_in_tuple = "(10 ( 'dt'10))"-q "SELECT * FROM table WHERE val = {tuple_in_tuple : Tuple (UInt8, Tuple (String, UInt8))}"
```

## 설정 {#interfaces_cli_configuration}

매개 변수를 전달하려면`clickhouse-client` (모든 매개 변수는 기본값이 있습니다) :

- 명령 줄에서

    명령 줄 옵션은 구성 파일의 기본값으로 설정을 덮어 씁니다.

- 설정 파일.

    구성 파일의 설정은 기본값을 재정의합니다.

### 명령 행 {# command-line-options}

-`--host, -h` - The server name 'localhost'기본적으로. 이름 또는 IPv4 또는 IPv6 주소를 사용할 수 있습니다.
-`--port` - The port to connect to. Default value : 9000. Note that the HTTP interface and the native interface use different ports.
-`--user, -u` - The username. Default value : default.
-`--password` - The password. Default value : empty string.
-`--query, -q` - The query to process when using non-interactive mode.
-`--database, -d` - Select the current default database. Default value : the current database from the server settings ( 'default'기본적으로).
-`--multiline, -m` - If specified, allow multiline queries (do not send the query on Enter).
-`--multiquery, -n` - If specified, allow processing multiple queries separated by semicolons.
-`--format, -f` - Use the specified default format to output the result.
-`--vertical, -E` - If specified, use the Vertical format by default to output the result. This is the same as '-format = Vertical'이 형식은 각 값은 별도의 줄에 인쇄됩니다 .
-`--time, -t` - If specified, print the query execution time to 'stderr'비대화 형 모드.
-`--stacktrace` - If specified, also print the stack trace if an exception occurs.
-`--config-file` - The name of the configuration file.
-`--secure` - If specified, will connect to server over secure connection.
-`--param_ <name>`- Value for a [매개 (# cli-queries-with-parameters).

### 설정 파일 {#configuration_files}

`clickhouse-client` 다음 첫 번째 기존 파일을 사용하고 있습니다 :

-로 정의된다. `--config-file` 매개 변수.
-`/ clickhouse-client.xml`
-`~ / .clickhouse-client / config.xml`
-`/ etc / clickhouse-client / config.xml`

설정 파일의 예 :

```xml
<config>
    <user> username </ user>
    <password> password </ password>
    <secure> False </ secure>
</ config>
```

원본 기사 (https://clickhouse.tech/docs/en/interfaces/cli/) <! - hide ->
