---
machine_translated : true
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd
toc_priority : 44
toc_title : "\ u8F9E \ u66F8 \ u30AD \ u30FC \ u3068 \ u30D5 \ u30A3 \ u30FC \ u30EB \ u30C9"
---

# 사전 키 필드 {# dictionary-key-and-fields}

그`<structure>`어구 쿼리에서 사용할 수있는 사전 키와 필드를 설명합니다.

XML 설명 :

```xml
<dictionary>
    <structure>
        <id>
            <name> Id </ name>
        </ id>

        <attribute>
            <! - Attribute parameters ->
        </ attribute>

        ...

    </ structure>
</ dictionary>
```

속성은 요소에 설명되어 있습니다 :

-`<id>`- 키 열 (external-dicts-dict-structure.md # ext_dict_structure-key).
-`<attribute>`- 데이터 열 (external-dicts-dict-structure.md # ext_dict_structure-attributes) 여러 속성이있을 수 있습니다.

DDL 쿼리 :

```sql
CREATE DICTIONARY dict_name (
    Id UInt64,
    - attributes
)
PRIMARY KEY Id
...
```

속성은 쿼리 본문에 설명되어 있습니다 :

-`PRIMARY KEY` - 키 열 (external-dicts-dict-structure.md # ext_dict_structure-key)
-`AttrName AttrType` - 데이터 열 (external-dicts-dict-structure.md # ext_dict_structure-attributes) 여러 속성이있을 수 있습니다.

## 키 {# ext_dict_structure-key}

ClickHouse는 다음과 같은 종류의 키를 지원하고 있습니다 :

- 숫자 키. `UInt64`.으로 정의된다. `<id>`태그 또는 사용`PRIMARY KEY` 키워드.
- 복합 키. 다른 형태의 값을 설정합니다. 태그로 정의`<key>`또는`PRIMARY KEY` 키워드.

Xml 구조를 포함 할 수 있습니다`<id>`또는`<key>`. DDL- 쿼리는 단 하나를 포함해야합니다`PRIMARY KEY`.

!!! warning "경고"
    키 속성이라고 할 수 없습니다.

### 숫자 키 {# ext_dict-numeric-key}

유형 :`UInt64`.

설정 예 :

```xml
<id>
    <name> Id </ name>
</ id>
```

설정 필드 :

-`name` - The name of the column with keys.

DDL 쿼리의 경우 :

```sql
CREATE DICTIONARY (
    Id UInt64,
    ...
)
PRIMARY KEY Id
...
```

-`PRIMARY KEY` - The name of the column with keys.

### 복합 키 {# composite-key}

키는 a 일 수 있습니다`tuple` 필드의 모든 유형에서. 그 레이아웃 (external-dicts-dict-layout.md)이 경우`complex_key_hashed` 또는`complex_key_cache`.

!!! tip "팁"
    복합 키는 단일 요소로 구성 할 수 있습니다. 이는 예를 들어 문자열을 키로 사용 할 수 있습니다.

키 구조는 요소로 설정됩니다`<key>`키 필드는 사전과 같은 형식으로 지정됩니다 [속성] (external-dicts-dict-structure.md) 예 :

```xml
<structure>
    <key>
        <attribute>
            <name> field1 </ name>
            <type> String </ type>
        </ attribute>
        <attribute>
            <name> field2 </ name>
            <type> UInt32 </ type>
        </ attribute>
        ...
    </ key>
...
```

또는

```sql
CREATE DICTIONARY (
    field1 String,
    field2 String
    ...
)
PRIMARY KEY field1, field2
...
```

검색어에`dictGet *`함수는 튜플이 키로 전달됩니다. 예 :`dictGetString ( 'dict_name', 'attr_name', tuple ( 'string for field1', num_for_field2))`.

## 특성 {# ext_dict_structure-attributes}

설정 예 :

```xml
<structure>
    ...
    <attribute>
        <name> Name </ name>
        <type> ClickHouseDataType </ type>
        <null_value> </ null_value>
        <expression> rand64 () </ expression>
        <hierarchical> true </ hierarchical>
        <injective> true </ injective>
        <is_object_id> true </ is_object_id>
    </ attribute>
</ structure>
```

또는

```sql
CREATE DICTIONARY somename (
    Name ClickHouseDataType DEFAULT ''EXPRESSION rand64 () HIERARCHICAL INJECTIVE IS_OBJECT_ID
)
```

설정 필드 :

| 태그 | 설명 | 필수 |
| ------------------------------------------------- ----- | -------------------------------------------- -------------------------------------------------- -------------------------------------------------- -------------------------------------------------- -------------------------------------------------- -------------------------------------------------- ------------------------------------ | ---------- |
|`name` | 열 이름. | 예. |
|`type` | ClickHouse 데이터 형식입니다. <br/> ClickHouse는 dictionary에서 지정된 데이터 형에 값을 캐스트하려고합니다. 예를 들어, MySQL 필드가`TEXT``VARCHAR` 또는`BLOB` MySQL 소스 테이블은 다음과 같이 업로드 할 수 있습니다`String` 클릭 하우스에서 <br/> [Null 가능 (../ .. /../sql-reference/data-types/nullable.md) 지원되지 않습니다. | 예. |
|`null_value` | 기존 요소 이외의 기본값입니다. <br/>이 예에서는 빈 문자열입니다. 사용할 수 없습니다`NULL`이 분야에서. | 예. |
|`expression` | 식 (../../ syntax.md # syntax-expressions) 그 ClickHouse 값에 대해 실행됩니다. <br/> 식 원격 SQL 데이터베이스 열 이름을 지정할 수 있습니다. 따라서 원격 열 별칭을 만드는 데 사용할 수 있습니다. <br/> <br/> 기본값 : 식 없음. | 아니오. |
| <a name="hierarchical-dict-attr"> </a>`hierarchical` | 만약`true`이 속성은 현재의 키의 상위 키 값이 포함됩니다. 보는 계층 사전 (external-dicts-dict-hierarchical.md) <br/> <br/> 기본값 :`false`. | 아니오. |
|`injective` |이 플래그는`id -> attribute` 이미지는 [injective (https://en.wikipedia.org/wiki/Injective_function) <br/> 만약`true`, ClickHouse은 후 자동 에 넣을 수 있습니다`GROUP BY` 절 인젝션을 사용하여 사전에 요청합니다. 일반적으로 이러한 요청의 양을 크게 줄일 수 있습니다. <br/> <br/> 기본값 :`false`. | 아니오. |
|`is_object_id` | 쿼리가 MongoDB 문서에 대해 수행되는지 여부를 나타내는 플래그`ObjectID`. <br/> <br/> 기본값 :`false`. | 아니오. |

##도 참조. {# see-also}

- 외부 사전을 조작하기위한 함수 (../../../ sql-reference / functions / ext-dict-functions.md).

원본 기사 (https://clickhouse.tech/docs/en/query_language/dicts/external_dicts_dict_structure/) <! - hide ->
