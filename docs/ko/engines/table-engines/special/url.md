--- 
machine_translated : true 
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd 
toc_priority : 41 
toc_title : URL 
--- 

# URL (형식) {# table_engines-url} 

원격 HTTP / HTTPS 서버에서 데이터를 관리합니다. 이 엔진은 마찬가지입니다 
에 [파일] (file.md) 엔진 

## ClickHouse 서버에서 엔진의 사용 {# using the-engine-in-the-clickhouse-server} 

그`format` ClickHouse가 사용할 수있는 것 없으면 안됩니다 
`SELECT` 쿼리 및 필요에 따라`INSERTs` 지원되는 형식의 전체 목록은 
[형식] (../../../ interfaces / formats.md #formats). 

그`URL` Uniform Resource Locator의 구조에 준수해야합니다. 지정된 URL은 서버를 가리켜 야합니다 
HTTP 또는 HTTPS를 사용합니다. 이것은 필요하지 않습니다 
서버의 응답을 얻기 위해 추가 헤더. 

`INSERT`와`SELECT` 질문에`POST`와`GET` 요청
각각. 처리를위한`POST` 요청은 원격 서버가 
[청크 분할 전송 인코딩 (https://en.wikipedia.org/wiki/Chunked_transfer_encoding) 

HTTP GET 리디렉션 호프의 최대 수를 제한하려면 max_http_get_redirects] (. ./../../operations/settings/settings.md#setting-max_http_get_redirects) 설정. 

** 예 ** 

** 1 ** 만들기`url_engine_table` 서버에서 테이블 : 

```sql 
CREATE TABLE url_engine_table (word String, value UInt64) 
ENGINE = URL ( 'http://127.0.0.1:12345/ 'CSV) 
``` 

** 2 ** 표준 python3 도구를 사용하여 기본적인 HTTP 서버를 만들고 
그것을 시작 : 

```python3 
from http.server import BaseHTTPRequestHandler, HTTPServer 

class CSVHTTPServer (BaseHTTPRequestHandler ) : 
    def do_GET (self) : 
        self.send_response (200) 
        self.send_header ( 'Content-type', 'text / csv') 
        self.end_headers ()

        self.wfile.write (bytes ( 'Hello, 1 \ nWorld 2 \ n', "utf-8")) 

if __name__ == "__main__": 
    server_address = ( '127.0.0.1', 12345) 
    HTTPServer (server_address, CSVHTTPServer) .serve_forever () 
``` 

```bash 
$ python3 server.py 
``` 

** 3 ** 요청 데이터 : 

```sql 
SELECT * FROM url_engine_table 
``` 

```text 
┌─word── ┬─value─┐ 
│ Hello │ 1 │ 
│ World │ 2 │ 
└───────┴───────┘ 
``` 

## 실시 내용 {# details-of-implementation} 

- 읽고 쓸 수 병렬 
- 대응하지 않습니다 : 
    -`ALTER`와`SELECT ... SAMPLE` 작전이다SAMPLE` 작전이다 
    - 인덱스.
    - 복제. 

원본 기사 (https://clickhouse.tech/docs/en/operations/table_engines/url/) <! - hide ->
