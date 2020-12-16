--- 
machine_translated : true 
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd 
toc_priority : 53 
toc_title : "UUID \ u306E \ u64CD \ u4F5C" 
--- 

# UUID를 조작하는 함수 {# functions-for-working-with-uuid} 

UUID를 조작하는 함수를 다음과 같습니다. 

## generateuidv4 {# uuid-function-generate} 

를 생성한다. [UUID (../../ sql-reference / data-types / uuid.md)의 버전 4 (https://tools.ietf.org/html/rfc4122#section-4.4) 

``` sql 
generateUUIDv4 () 
``` 

** 반환 값 ** 

UUID 형의 값. 

** 예 ** 

이 예에서는 UUID 형의 열이있는 테이블을 만들고 테이블에 값을 삽입하는 방법을 보여줍니다. 

```sql 
CREATE TABLE t_uuid (x UUID) ENGINE = TinyLog 

INSERT INTO t_uuid SELECT generateUUIDv4 ()

SELECT * FROM t_uuid 
``` 

```text 
┌────────────────────────────────────x─ ┐ 
│ f4bf890f-f9dc-4332-ad5c-0c18e73f28e9 │ 
└───────────────────────────────────── ─┘ 
``` 

## 토우이도 (x) {# touuid-x} 

문자열 값을 UUID 형식으로 변환합니다. 

```sql 
toUUID (String) 
``` 

** 반환 값 ** 

UUID 형의 값. 

** 예 ** 

```sql 
SELECT toUUID ( '61f0c404-5cb3-11e7-907b-a6006ad3dba0') AS uuid 
``` 

```text 
┌─────────────── ──────────────────uuid─┐ 
│ 61f0c404-5cb3-11e7-907b-a6006ad3dba0 │ 
└───────────────── ─────────────────────┘ 
``` 

## UUIDStringToNum {#uuidstringtonum}

형식으로 36 문자가 포함 된 문자열을 받아들입니다`xxxxxxxx-xxxxxxxx-xxxx-xxxxxxxxxxxx` 이것을 a 바이트 집합으로 반환합니다 [FixedString (16) (../../ sql-reference / data -types / fixedstring.md) 

```sql 
UUIDStringToNum (String) 
``` 

** 반환 값 ** 

FixedString (16) 

** 예 ** 

```sql 
SELECT 
    '612f3c40-5d3b-217e-707b-6a546a3d7b29 'AS uuid, 
    UUIDStringToNum (uuid) AS bytes 
``` 

```text 
┌─uuid────────────────────────────── ───┬─bytes────────────┐ 
│ 612f3c40-5d3b-217e-707b-6a546a3d7b29 │ a / <@] ;! ~ p {jTj = {) │ 
└──── ──────────────────────────────────┴─────────────── ───┘ 
``` 

## UUIDNumToString {#uuidnumtostring}

받아들이 a [FixedString (16) (../../ sql-reference / data-types / fixedstring.md) 텍스트 형식으로 36 문자가 포함 된 문자열을 반환합니다. 

```sql 
UUIDNumToString (FixedString (16)) 
``` 

** 반환 값 ** 

문자열 

** 예 ** 

```sql 
SELECT 
    'a / <@] ;! ~ p {jTj = {)'AS bytes, 
    UUIDNumToString (toFixedString (bytes 16)) AS uuid 
``` 

```text 
┌─bytes────────────┬─uuid──────────── ─────────────────────┐ 
│ a / <@] ;! ~ p {jTj = {) │ 612f3c40-5d3b-217e-707b-6a546a3d7b29 │ 
└─ ─────────────────┴──────────────────────────────── ──────┘ 
``` 

## 참조. {# see-also} 

- [dictGetUUID (ext-dict-functions.md # ext_dict_functions-other)

원본 기사 (https://clickhouse.tech/docs/en/query_language/functions/uuid_function/) <! - hide ->
