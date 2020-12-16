--- 
machine_translated : true 
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd 
toc_priority : 40 
toc_title : "\ u5916 \ u90E8 \ u30C7 \ u30A3 \ u30AF \ u30B7 \ u30E7 \ u30CA \ u30EA \ u306E \ u69CB \ u6210" 
--- 

# 외부 사전 구성 {# dicts-external-dicts-dict} 

사전이 xml 파일을 사용하여 구성되는 경우, 사전의 구성은 다음 구조를 갖습니다 : 

```xml 
<dictionary> 
    <name> dict_name </ name> 

    <structure > 
      <! - Complex key configuration -> 
    </ structure> 

    <source> 
      <! - source configuration -> 
    </ source> 

    <layout> 
      <! - Memory layout configuration -> 
    </ layout>

    <lifetime>
      <! - Lifetime of dictionary in memory -> 
    </ lifetime> 
</ dictionary> 
``` 

해당하는 DDL- 쿼리 (../../ statements / create.md # create-dictionary-query) 다음 구조를 갖습니다 : 

```sql 
CREATE DICTIONARY dict_name 
( 
    ... - attributes 
) 
PRIMARY kEY ... - complex or single key configuration 
SOURCE (...) - Source configuration 
LAYOUT (...) - - memory layout configuration 
LIFETIME (...) - Lifetime of dictionary in memory 
``` 

-`name` - the identifier that can be used to access the dictionary . Use the characters`[a-zA-Z0-9 _ \ - ]`. 
- [소스] (external-dicts-dict-sources.md) - Source of the dictionary.
- [레이아웃] (external-dicts-dict-layout.md) - Dictionary layout in memory. 
- [구조] (external-dicts-dict-structure.md) - Structure of the dictionary. A key and attributes that can be retrieved by this key. 
- [생애] (external-dicts-dict-lifetime.md) - Frequency of dictionary updates. 

원본 기사 (https://clickhouse.tech/docs/en/query_language/dicts/external_dicts_dict/) <! - hide ->
