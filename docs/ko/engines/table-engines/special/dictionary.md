--- 
machine_translated : true 
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd 
toc_priority : 35 
toc_title : "\ u8F9E \ u66F8" 
--- 

# 사전 {#dictionary} 

그`Dictionary` 엔진은 표시합니다 [사전] (../../ .. /sql-reference/dictionaries/external-dictionaries/external-dicts.md) ClickHouse 테이블로 데이터입니다. 

예를 들어 사전을 생각하자`products` 다음의 구성 : 

```xml 
<dictionaries> 
<dictionary> 
        <name> products </ name> 
        <source> 
            <odbc> 
                <table> products </ table > 
                <connection_string> DSN = some-db-server </ connection_string> 
            </ odbc>
            <min> 300 </ min> 
            <max> 360 </ max> 
        </ lifetime> 
        <layout> 
            <flat /> 
        </ layout> 
        <structure> 
            <id> 
                <name> product_id </ name> 
            </ id> 
            < attribute> 
                <name> title </ name> 
                <type> String </ type> 
                <null_value> </ null_value> 
            </ attribute> 
        </ structure> 
</ dictionary> 
</ dictionaries> 
``` 

사전 데이터의 조회 :

```sql 
SELECT 
    name, 
    type,  
    key,
    attribute.names,
    attribute.types, 
    bytes_allocated, 
    element_count, 
    source 
FROM system.dictionaries 
WHERE name = 'products' 
``` 

```text 
┌─name─────┬─type─┬─key────┬─attribute.names ─┬─attribute.types─┬─bytes_allocated─┬─element_count─┬─source──────────┐ 
│ products │ Flat │ UInt64 │ [ 'title'] │ [ 'String'] │ 23065376 │ 175032 │ ODBC : .products │ 
└──────────┴──────┴────────┴─────────────── ──┴─────────────────┴─────────────────┴─────────── ────┴─────────────────┘ 
``` 

를 사용할 수 있습니다 [dictGet \ * (../../../ sql- reference / functions / ext-dict-functions.md # ext_dict_functions)이 형식의 사전 데이터를 검색하는 함수입니다.

이보기는 원시 데이터를 취득 할 필요가 있거나`JOIN` 작전이다 이러한 경우는`Dictionary` 사전 데이터를 테이블에 표시하는 엔진. 

구문 : 

```sql 
CREATE TABLE % table_name % (% fields %) engine = Dictionary (% dictionary_name %)` 
``` 

예 : 

```sql 
create table products (product_id UInt64, title String) Engine = Dictionary (products ); 
``` 

      Ok 

테이블에있는 것을보십시오. 

```sql 
select * from products limit 1; 
``` 

```text 
┌────product_id─┬─title───────────┐ 
│ 152689 │ Some item │ 
└─── ────────────┴─────────────────┘ 
``` 

원래 기사 (https://clickhouse.tech/docs/ en / operations / table_engines / dictionary /) <! - hide ->
