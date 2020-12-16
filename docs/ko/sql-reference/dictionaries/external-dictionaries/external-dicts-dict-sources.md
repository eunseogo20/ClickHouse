--- 
machine_translated : true 
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd 
toc_priority : 43 
toc_title : "\ u5916 \ u90E8 \ u8F9E \ u66F8 \ u306E \ u30BD \ u30FC \ u30B9" 
--- 

# 외부 사전의 소스 {# dicts-external-dicts-dict- sources} 

외부 사전은 다양한 소스에서 연결할 수 있습니다. 

사전 xml-file을 사용하여 구성되어있는 경우 구성은 다음과 같습니다 : 

```xml 
<yandex> 
  <dictionary> 
    ... 
    <source> 
      <source_type> 
        <! - Source configuration - -> 
      </ source_type> 
    </ source> 
    ... 
  </ dictionary> 
  ... 
</ yandex> 
```

의 경우 [DDL- 쿼리 (../../ statements / create.md # create-dictionary-query), 동일한 구성은 다음과 같습니다 : 

```sql 
CREATE DICTIONARY dict_name (...) 
. .. 
SOURCE (SOURCE_TYPE (param1 val1 ... paramN valN)) - Source configuration 
... 
``` 

소스`source` 섹션 

소스 유형의 경우 [로컬] (# dicts-external_dicts_dict_sources-local_file), 실행 파일 (# dicts-external_dicts_dict_sources-executable), HTTP (s) (# dicts-external_dicts_dict_sources-http), 클릭 하우스 (# dicts-external_dicts_dict_sources-clickhouse) 
모든 설정은 유효하다 : 

```xml 
< source> 
  <file> 
    <path> /opt/dictionaries/os.tsv </ path> 
    <format> TabSeparated </ format> 
  </ file> 
  <settings>
      <format_csv_allow_single_quotes> 0 </ format_csv_allow_single_quotes> 
  </ settings> 
</ source> 
``` 

또는 

```sql 
SOURCE (FILE (path '/opt/dictionaries/os.tsv'format 'TabSeparated')) 
SETTINGS (format_csv_allow_single_quotes = 0 ) 
``` 

소스의 종류 (`source_type`) : 

- 로컬 (# dicts-external_dicts_dict_sources-local_file) 
- [실행] (# dicts-external_dicts_dict_sources-executable) 
- [HTTP (s) (# dicts- external_dicts_dict_sources-http) 
- DBMS 
    - ODBC (# dicts-external_dicts_dict_sources-odbc) 
    - [MySQL] (# dicts-external_dicts_dict_sources-mysql) 
    - 클릭 하우스 (# dicts-external_dicts_dict_sources-clickhouse) 
    - [mongoDB] (# dicts-external_dicts_dict_sources-mongodb) 
    - [Redis (# dicts-external_dicts_dict_sources-redis) 

## 로컬 {# dicts-external_dicts_dict_sources-local_file} 

설정 예 : 

```xml 
<source> 
  <file> 
    <path > /opt/dictionaries/os.tsv </ path> 
    <format> TabSeparated </ format> 
  </ file> 
</ source> 
``` 

또는 

```sql 
sOURCE (fILE (path '/ opt / dictionaries / os. tsv 'format'TabSeparated ')) 
``` 

필드의 설정 : 

-`path` - the absolute path to the file. 
-`format` - the file format. All the formats described in "[형식] (../. ./../interfaces/formats.md#formats) "지원됩니다.

## 실행 파일 {# dicts-external_dicts_dict_sources-executable}

실행 파일을 조작하려면 사전을 메모리에 저장하는 방법] (external-dicts-dict-layout.md) 사전이 다음을 사용하여 저장되어있는 경우`cache`와`complex_key_cache`, ClickHouse 는 실행 파일의 STDIN에 요청을 전송하여 필요한 키를 요구합니다. 기타 ClickHouse 시작 실행 파일을 취급하고 그 출력으로 사전 데이터입니다. 

설정 예 : 

```xml 
<source> 
    <executable> 
        <command> cat /opt/dictionaries/os.tsv </ command> 
        <format> TabSeparated </ format> 
    </ executable> 
</ source> 
``` 

또는 

` ``sql 
SOURCE (eXECUTABLE (command 'cat /opt/dictionaries/os.tsv'format 'TabSeparated')) 
``` 

필드의 설정 : 

-`command` - the absolute path to the executable file, or the file name ( if the program directory is written to`PATH`). 
-`format` - The file format. All the formats described in "[형식] (. ./../../interfaces/formats.md#formats) "지원됩니다. 

## Http (s) {# dicts-external_dicts_dict_sources-http} 

HTTP 서버에서의 작업은 다음과 같이 달라집니다 사전을 메모리에 저장하는 방법] (external-dicts-dict-layout.md) 사전이 다음을 사용하여 저장되어있는 경우`cache`와`complex_key_cache` 트를 전송하여 필요한 키를 요구합니다. `POST` 방법. 

설정 예 : 

```xml 
<source> 
    <http> 
        <url> http : // [:: 1] /os.tsv </ url> 
        <format> TabSeparated </ format> 
        <credentials> 
            <user> user </ user> 
            <password> password < 
        </ credentials> 
        <headers> 
            <header> 
                <name> API-KEY </ name>
            </ header> 
        </ headers> 
    </ http> 
</ source> 
``` 

또는 

```sql 
SOURCE (HTTP ( 
    url 'http://[::1]/os.tsv' 
    format 'TabSeparated' 
    credentials ( user 'user'password 'password') 
    headers (header (name 'API-kEY'value 'key')) 
)) 
``` 

ClickHouse가 HTTPS 리소스에 액세스하려면 다음을 수행해야합니다 [openSSL 설정 (../../../ operations / server-configuration-parameters / settings.md # server_configuration_parameters-openssl) 서버 구성. 

필드의 설정 : 

-`url` - The source URL.  
-`format` - The file format. All the formats described in "[형식] (../../ ..
-`credentials` - Basic HTTP authentication. Optional parameter.
    -`password` - Password required for the authentication. 
-`headers` - All custom HTTP headers entries used for the HTTP request . Optional parameter. 
    -`header` - Single HTTP header entry. 
    -`name` - Identifiant name used for the header send on the request. 
    -`value` - Value set for a specific identifiant name. 

## ODBC {# dicts-external_dicts_dict_sources-odbc} 

이 메소드를 사용하여 ODBC 드라이버를 가진 데이터베이스에 연결할 수 있습니다. 

설정 예 : 

```xml 
<source> 
    <odbc> 
        <db> DatabaseName </ db> 
        <table> ShemaName.TableName </ table>
        <connection_string> DSN = some_parameters </ connection_string> 
        <invalidate_query> SQL_QUERY </ invalidate_query> 
    </ odbc> 
</ source> 
``` 

또는 

```sql 
SOURCE (ODBC ( 
    db 'DatabaseName' 
    table 'SchemaName.TableName' 
    connection_string ' DSN = some_parameters ' 
    invalidate_query'SQL_QUERY ' 
)) 
``` 

필드의 설정 : 

-`db` - name of the database. Omit it if the database name is set in the `<connection_string>`변수. 
-`table` - Name of the table and schema if exists. 
-`connection_string` - Connection string. 
-`invalidate_query` - Query for checking the dictionary status. Optional parameter. Read more in the section 사전 업데이트 (external-dicts-dict-lifetime.md) 

ClickHouse는 ODBC-driver에서 인용 기호를 받고 쿼리의 모든 설정을 driver에 인용하기 위하여 데이터베이스 내 테이블 이름의 대소 문자에 따라 테이블 이름을 

Oracle을 사용할 때 인코딩에 문제가있는 경우 해당 부분을 참조하십시오 [fAQ] (../../../ faq / general .md # oracle-odbc-encodings) 기사이다 

### ODBC 사전 기능의 알려진 취약점 {# known-vulnerability-of-the-odbc-dictionary-functionality} 

!!! attention "주의"
    ODBC 드라이버 연결 매개 변수 데이터베이스에 연결하는 경우`Servername` 대체 가능하다. 이 경우 값은`USERNAME`와`PASSWORD`에서`odbc.ini` 원격 서버에 전송되고 침해 될 가능성이 있습니다. 

** 안전하지 않은 사용 예 ** 

PostgreSQL 용 unixODBC를 설정합시다. 내용`/ etc / odbc.ini` : 

```text 
[gregtest] 
Ubuntu OS. 
Driver = /usr/lib/psqlodbca.so
Servername = localhost 
PORT = 5432 
DATABASE = test_db 
#OPTION = 3 
USERNAME = test 
PASSWORD = test 
``` 

다음에, 다음과 같은 쿼리를 만들 때 

```sql 
SELECT * FROM odbc ( 'DSN = gregtest; Servername = some-server.com ','test_db '); 
``` 

ODBC 드라이버는 다음의 값을 보냅니다`USERNAME`와`PASSWORD`에서`odbc.ini`에`some-server.com`. 

## # Postgresql의 접속 예 {# example-of-connecting-postgresql} 


Unixodbc과 POSTGRESQL 용 ODBC 드라이버 설치 : 

```bash 
$ sudo apt-get install -y unixodbc odbcinst odbc-postgresql 
``` 

설정`/ etc / odbc .ini` (또는`~ / .odbc.ini`) : 

```text 
    [DEFAULT] 
    Driver = myconnection 

    [myconnection]
    Description = PostgreSQL connection to my_db 
    Driver = PostgreSQL Unicode 
    Database = my_db 
    Servername = 127.0.0.1 
    UserName = username 
    Password = password 
    Port = 5432 
    Protocol = 9.3 
    ReadOnly = No 
                <! - You can specify the following parameters in connection_string : -> 
    RowVersioning = No 
    ShowSystemTables = No
    ConnSettings = 
``` 

ClickHouse의 사전 구성 : 

```xml 
<yandex> 
    <dictionary> 
        <name> table_name </ name> 
        <source> 
            <odbc> 
                <! - DSN = myconnection; UID = username; PWD = password; HOST = 127.0.0.1; PORT = 5432; DATABASE = my_db -> 
                <connection_string> DSN = myconnection </ connection_string> 
                <table> postgresql_table </ table> 
            </ odbc> 
        </ source> 
        <lifetime> 
            <min> 300 < / min> 
            <max> 360 </ max> 
        </ lifetime> 
        <layout>
            <hashed /> 
        </ layout> 
        <structure> 
            <id> 
                <name> id </ name> 
            </ id> 
            <attribute> 
                <name> some_column </ name>
                <type> UInt64 </ type> 
                <null_value> 0 </ null_value>
            </ attribute> 
        </ structure> 
    </ dictionary> 
</ yandex> 
``` 

또는 

```sql 
CREATE DICTIONARY table_name ( 
    id UInt64, 
    some_column UInt64 DEFAULT 0 
) 
PRIMARY KEY id 
SOURCE (ODBC (connection_string 'DSN = myconnection'table 'postgresql_table')) 
LAYOUT (HASHED ()) 
LIFETIME (MIN 300 MAX 360) 
``` 
 
편집이 필요한 경우가 있습니다` odbc.ini` 드라이버를 사용하여 라이브러리의 전체 경로를 지정하려면`DRIVER = / usr / local / lib / psqlodbcw.so`.

### mS sQL server의 연결 예 {# example-of-connecting-ms-sql-server} 

Ubuntu OS. 

드라이버 설치 : 

```bash 
$ sudo apt-get install tdsodbc freetds-bin sqsh 
``` 

드라이버 설정 : 

```bash 
    $ cat /etc/freetds/freetds.conf 
    ...

    [MSSQL] 
    host = 192.168.56.101 
    port = 1433 
    tds version = 7.0 
    client charset = UTF-8 

    $ cat /etc/odbcinst.ini 
    ... 

    [FreeTDS] 
    Description = FreeTDS 
    Driver = / usr / lib / x86_64-linux-gnu /odbc/libtdsodbc.so  
    Setup = /usr/lib/x86_64-linux-gnu/odbc/libtdsS.so 
    FileUsage = 1
    UsageCount = 5 

    $ cat ~ / .odbc.ini 
    ... 

    [MSSQL] 
    Description = FreeTDS 
    Driver = FreeTDS 
    Servername = MSSQL 
    Database = test 
    UID = test 
    PWD = test 
    Port = 1433 
``` 

ClickHouse에서 사전 구성 : 

```xml
<yandex> 
    <dictionary> 
        <name> test </ name> 
        <source> 
            <odbc> 
                <table> dict </ table> 
                <connection_string> DSN = MSSQL; UID = test; PWD = test </ connection_string> 
            </ odbc> 
        </ source> 
            <attribute> 
                <name> s </ name> 
                <type> String </ type>

        <lifetime>
            <min> 300 </ min> 
            <max> 360 </ max> 
        </ lifetime> 

        <layout> 
            <flat /> 
        </ layout> 

        <structure> 
            <id> 
                <name> k </ name> 
            </ id> 
                < null_value> </ null_value> 
            </ attribute> 
        </ structure> 
    </ dictionary> 
</ yandex> 
``` 

또는 

```sql 
CREATE dICTIONARY test ( 
    k UInt64, 
    s String DEFAULT '' 
) 
PRIMARY KEY k 
SOURCE (ODBC ( table 'dict'connection_string 'DSN = MSSQL; UID = test; PWD = test')) 
LAYOUT (FLAT ())
LIFETIME (MIN 300 MAX 360) 
``` 

## DBMS {#dbms} 

### Mysql {# dicts-external_dicts_dict_sources-mysql} 

설정 예 : 

```xml 
<source> 
  <mysql> 
      <port> 3306 </ port> 
      <user> clickhouse </ user> 
      <password> qwerty </ password> 
      <replica>  
          <host> example01-1 </ host>
          <priority> 1 </ priority> 
      </ replica> 
      <replica> 
          <host> example01-2 </ host> 
          <priority> 1 </ priority> 
      </ replica> 
      <db> db_name </ db> 
      <table> table_name </ table> 
      <where> id = 10 </ where>
      <invalidate_query> SQL_QUERY </ invalidate_query> 
  </ mysql> 
</ source>
``` 

또는 

```sql 
SOURCE (MYSQL ( 
    port 3306 
    user 'clickhouse' 
    password 'qwerty' 
    replica (host 'example01-1'priority 1) 
    replica (host 'example01-2'priority 1) 
    db 'db_name' 
    table ' table_name ' 
    where'id = 10 '  
    invalidate_query 'SQL_QUERY'
)) 
``` 

필드의 설정 : 

-`port` - the port on the MySQL server. You can specify it for all replicas, or for each one individually (inside`<replica>`) . 

-`user` - Name of the MySQL user. You can specify it for all replicas, or for each one individually (inside`<replica>`).
 
-`password` - Password of the MySQL user. You can specify it for all replicas, or for each one individually (inside`<replica>`) 

-`replica` - Section of replica configurations. There can be multiple sections. 

        -`replica / host` - The MySQL host. 
        -`replica / priority` - the replica priority. When attempting to connect, ClickHouse traverses the replicas in order of priority. the lower the number, the higher the priority. 

-`db` - Name of the database. 

-` table` - Name of the table.

-`where` - The selection criteria. The syntax for conditions is the same as for `WHERE` MySQL의 어구 예를 들어,`id> 10 AND id <20`. 모든 매개 변수. 

-`invalidate_query` - Query for checking the dictionary status. Optional parameter. Read more in the section 사전 업데이트 (external-dicts-dict-lifetime.md) 

MySQL은 소켓을 통해 로컬 호스트에 연결할 수 있습니다. 이렇게하기 위해서는`host`와`socket`. 

설정 예 : 

```xml 
<source> 
  <mysql> 
      <host> localhost </ host> 
      <socket> /path/to/socket/file.sock </ socket> 
      <user> clickhouse </ user> 
      <password> qwerty </ password> 
      <db> db_name </ db> 
      <table> table_name </ table> 
      <where> id = 10 </ where>
  </ mysql> 
</ source> 
``` 

또는

    user 'clickhouse' 
    password 'qwerty' 
    db 'db_name' 
    table 'table_name' 
    where 'id = 10' 
    invalidate_query 'SQL_QUERY' 
)) 
``` 

### 클릭 하우스 {# dicts-external_dicts_dict_sources-clickhouse} 

설정 예 : 

``` xml 
<source> 
    <clickhouse> 
        <host> example01-01-1 </ host> 
        <port> 9000 </ port> 
        <user> default </ user> 
        <password> </ password> 
        <db> default </ db> 
        <table> ids </ table> 
        <where> id = 10 </ where> 
    </ clickhouse>
</ source> 
``` 

또는 

```sql 
SOURCE (CLICKHOUSE (
    host 'example01-01-1' 
    port 9000 
    user 'default' 
    password '' 
    db 'default' 
    table 'ids' 
    where 'id = 10' 
)) 
``` 

필드의 설정 : 

-`host` - The ClickHouse host. If it is a local host, the query is processed without any network activity. To improve fault tolerance, you can create a [분산] (../../../ engines / table-engines / special / distributed.md) 테이블 후속 구성에 그것을 입력합니다. 
-`port` - The port on the ClickHouse server. 
-`user` - Name of the ClickHouse user. 
-`password` - Password of the ClickHouse user. 
-`db` - Name of the database.  
-`table` - Name of the table.
-`where` - The selection criteria. May be omitted. 
-`invalidate_query` - Query for checking the dictionary status. Optional parameter. Read more in the section 사전 업데이트 (external-dicts-dict-lifetime.md). 

### mongodb {# dicts-external_dicts_dict_sources-mongodb} 

설정 예 : 

```xml 
<source> 
    <mongodb> 
        <host> localhost </ host> 
        <port> 27017 </ port> 
        <user> </ user> 
        <password > </ password> 
        <db> test </ db> 
        <collection> dictionary_source </ collection> 
    </ mongodb> 
</ source>
``` 

또는 

```sql 
SOURCE (MONGO ( 
    host 'localhost' 
    port 27017
    user ' 
    password '' 
    db 'test'
    collection'dictionary_source ' 
)) 
``` 

필드의 설정 : 

-`host` - The MongoDB host. 
-`port` - The port on the MongoDB server. 
-`user` - Name of the MongoDB user. 
-` password` - Password of the MongoDB user. 
-`db` - Name of the database. 
-`collection` - Name of the collection. 

### redis {# dicts-external_dicts_dict_sources-redis} 

설정 예 : 

```xml 
<source > 
    <redis> 
        <host> localhost </ host> 
        <port> 6379 </ port> 
        <storage_type> simple </ storage_type> 
        <db_index> 0 </ db_index> 
    </ redis>
</ source> 
```

또는 
 
```sql
SOURCE (REDIS ( 
    host 'localhost' 
    port 6379 
    storage_type 'simple' 
    db_index 0 
)) 
``` 

필드의 설정 : 

-`host` - The Redis host. 
-`port` - The port on the Redis server. 
-`storage_type `- The structure of internal Redis storage using for work with keys `simple` 간단한 근원을위한 해시 된 단일 키 소스,`hash_map` 두 키를 가진 해시 소스 용입니다. 距源및 캐시 소스의 복잡한 키 지원하지 않습니다. 선택적이며, 기본값은`simple`. 
-`db_index` - The specific numeric index of Redis logical database. May be omitted, default value is 0. 

원래 기사 (https://clickhouse.tech/docs / en / query_language / dicts / external_dicts_dict_sources /) <! - hide ->
