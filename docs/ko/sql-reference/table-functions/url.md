--- 
machine_translated : true 
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd 
toc_priority : 41 
toc_title : url 
--- 

# url {#url} 

`url (URL, format, structure)`-에서 만든 테이블을 반환합니다`URL` 주어진 
` format`와`structure`. 

URL-HTTP 또는 HTTPS 서버 주소. `GET` 및 / 또는`POST` 요청 

형식 - 형식 (../../ interfaces / formats.md # formats) 데이터의. 

structure- 테이블의 구조` 'UserID UInt64, Name String'` 형식. 열 이름과 형식을 결정합니다. 

** 예 ** 

```sql 
- getting the first 3 lines of a table that contains columns of String and UInt32 type from HTTP -server which answers in CSV format. 
SELECT * FROM url ( 'http://127.0.0.1 : 12345 / ', CSV'column1 String, column2 UInt32 ') LIMIT 3
``` 

원래 기사 (https://clickhouse.tech/docs/en/query_language/table_functions/url/) <! - hide ->
