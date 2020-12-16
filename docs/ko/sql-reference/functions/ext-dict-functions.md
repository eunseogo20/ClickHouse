--- 
machine_translated : true 
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd 
toc_priority : 58 
toc_title : "\ u5916 \ u90E8 \ u8F9E \ u66F8 \ u306E \ u64CD \ u4F5C" 
--- 

# 외부 사전을 조작하는 함수 {#ext_dict_functions} 

정보의 연결 또는 설정 외부 사전 참조 외부 사전 (../../ sql-reference / dictionaries / external-dictionaries / external-dicts.md) 

## dictGet {#dictget} 

외부 사전에서 값을 가져옵니다. 

```sql 
dictGet ( 'dict_name', 'attr_name', id_expr) 
dictGetOrDefault ( 'dict_name', 'attr_name', id_expr, default_value_expr) 
``` 

** 파라미터 ** 

-`dict_name` - Name of the dictionary [문자 문자열 리터럴] (../ syntax.md # syntax-string-literal).
-`attr_name` - Name of the column of the dictionary [문자열 리터럴] (../ syntax.md # syntax-string-literal) 
-`id_expr` - Key value. 식 (../ syntax. md # syntax-expressions) a를 반환 [UInt64 (../../ sql-reference / data-types / int-uint.md) 또는 튜플 (../../ sql-reference / data- types / tuple.md) - 사전 구성에 따라 값을 입력합니다. 
-`default_value_expr` - Value returned if the dictionary does not contain a row with the `id_expr` 키 식 (../ syntax.md # syntax-expressions) 데이터 형의 값을 돌려줍니다. `attr_name` 속성. 

** 반환 값 ** 

- ClickHouse이 속성을 제대로 해석 한 경우, 속성의 데이터 형식 (../../ sql-reference / dictionaries / external-dictionaries / external-dicts-dict-structure.md # ext_dict_structure-attributes) 함수는 해당 사전 속성 값을 반환합니다`id_expr`. 

- 키가없는 경우 해당`id_expr`, 사전에서는 :

        -`dictGet` returns the content of the`<null_value>`element specified for the attribute in the dictionary configuration . 
        -`dictGetOrDefault` returns the value passed as the`default_value_expr` parameter. 

ClickHouse는 특성 값을 해석 할 수없는 경우 또는 값이 속성 데이터 유형과 일치하지 않는 경우에 예외를 throw합니다. 

** 예 ** 

텍스트 파일 만들기`ext-dict-text.csv` 다음을 포함 : 

```text 
1,1 
2,2 
``` 

최초의 열은 다음과 같습니다`id` 두 번째 열은`c1`. 

외부 사전 구성 : 

```xml 
<yandex> 
    <dictionary> 
        <name> ext-dict-test </ name> 
        <source> 
            <file> 
                <path> / path-to / ext-dict -test.csv </ path> 
                <format> CSV <
            </ file> 
        </ source> 
        <layout>  
            <flat /> 
        </ layout>
                <name> id </ name> 
            </ id> 
            <attribute> 
                <name> c1 </ name> 
                <type> UInt32 </ type> 
                <null_value> </ null_value> 
            < / attribute> 
        </ structure> 
        <lifetime> 0 </ lifetime> 
    </ dictionary> 
</ yandex> 
``` 

쿼리 : 

```sql 
SELECT 
    dictGetOrDefault ( 'ext-dict-test', 'c1'number +1, toUInt32 (number * 10)) AS val,
    toTypeName (val) AS type 
FROM system.numbers 
LIMIT 3 
``` 

```text
┌─val─┬─type───┐ 
│ 1 │ UInt32 │ 
│ 2 │ UInt32 │ 
│ 20 │ UInt32 │
└─────┴────────┘ 
``` 

** 참조. ** 

- 외부 사전 (../../ sql-reference / dictionaries / external-dictionaries / external-dicts.md) 

## 디쿠타스 {#dicthas} 

키가 사전에 있는지 여부를 확인합니다. 

```sql 
dictHas ( 'dict_name', id_expr) 
``` 

** 파라미터 ** 

-`dict_name` - Name of the dictionary [문자열 리터럴] (../ syntax.md # syntax-string-literal). 
-`id_expr` - Key value. 식 (../ syntax.md # syntax-expressions) a를 반환 [UInt64 (../../ sql-reference / data-types / int-uint.md) - 유형 값. 

** 반환 값 ** 

- 키가 없으면 0. 
- 1 키가 있다면. 

유형 :`UInt8`. 

## dictGetHierarchy {#dictgethierarchy}

키의 모든 부모를 포함한 배열을 만듭니다. 계층 사전 (../../ sql-reference / dictionaries / external-dictionaries / external-dicts-dict-hierarchical.md) 

** 구문 ** 

```sql 
dictGetHierarchy ( 'dict_name', key) 
` `` 

** 파라미터 ** 

-`dict_name` - Name of the dictionary [문자열 리터럴] (../ syntax.md # syntax-string-literal) 
-`key` - Key value. 식 (. ./syntax.md#syntax-expressions) a를 반환 [UInt64 (../../ sql-reference / data-types / int-uint.md) - 유형 값. 

** 반환 값 ** 

- 키 부모. 

유형 : 배열 (UInt64) (../../ sql-reference / data-types / array.md) 

## 지쿠찌신 {#dictisin} 

사전의 계층 체인 전반에 걸쳐 키의 조상을 확인합니다. 

```sql 
dictIsIn ( 'dict_name', child_id_expr, ancestor_id_expr) 
``` 

** 파라미터 **

-`dict_name` - Name of the dictionary [문자열 리터럴] (../ syntax.md # syntax-string-literal) 
-`child_id_expr` - Key to be checked. 식 (../ syntax.md # syntax-expressions) a를 반환 [UInt64 (../../ sql-reference / data-types / int-uint.md) - 유형 값. 
-`ancestor_id_expr` - Alleged ancestor of the`child_id_expr` 키 식 (../ syntax.md # syntax-expressions) a를 반환 [UInt64 (../../ sql-reference / data-types / int -uint.md) - 유형 값. 

** 반환 값 ** 

- 0의 경우`child_id_expr`의 자식이 아닙니다`ancestor_id_expr`. 
- 1의 경우`child_id_expr`의 아들 인`ancestor_id_expr` 또는`child_id_expr`는`ancestor_id_expr`. 

유형 :`UInt8`. 

## 기타 기능 {# ext_dict_functions-other} 

ClickHouse는 사전 구성에 관계없이 사전 속성 값을 특정 데이터 형식으로 변환하는 특수 기능을 지원합니다. 

함수 :

-`dictGetInt8``dictGetInt16``dictGetInt32``dictGetInt64` 
-`dictGetUInt8``dictGetUInt16``dictGetUInt32``dictGetUInt64` 
-`dictGetFloat32``dictGetFloat64` 
-`dictGetDate` 
-`dictGetDateTime` 
-` dictGetUUID` 
-`dictGetString` 

이 함수는 모든`OrDefault` 수정 예를 들어,`dictGetDateOrDefault`. 

구문 : 

```sql 
dictGet Type ( 'dict_name', 'attr_name', id_expr) 
dictGet [Type] OrDefault ( ' dict_name ','attr_name ', id_expr, default_value_expr) 
``` 

** 파라미터 ** 
 
-`dict_name` - Name of the dictionary [문자열 리터럴] (../ syntax.md # syntax-string-literal).
-`attr_name` - Name of the column of the dictionary [문자열 리터럴] (../ syntax.md # syntax-string-literal ). 
-`id_expr` - Key value. 식 (../ syntax. md # syntax-expressions) a를 반환 [UInt64 (../../ sql-reference / data-types / int-uint.md) - 유형 값. 
-`default_value_expr` - Value which is returned if the dictionary does not contain a row with the `id_expr` 키 식 (../ syntax.md # syntax-expressions) 데이터 형에 설정된 값을 반환`attr_name `속성. 

** 반환 값 ** 

- ClickHouse이 속성을 제대로 해석 한 경우, 속성의 데이터 형식 (../../ sql-reference / dictionaries / external-dictionaries / external-dicts-dict-structure.md # ext_dict_structure-attributes) 함수는 해당 사전 속성 값을 반환합니다`id_expr`. 

- 요청이 없을 경우`id_expr` 사전에서 :

        -`dictGet [Type]`returns the content of the`<null_value>`element specified for the attribute in the dictionary configuration . 
        -`dictGet [Type] OrDefault` returns the value passed as the`default_value_expr` parameter. 

ClickHouse 특성 값을 해석 할 수없는 경우 또는 값이 속성 데이터 유형과 일치하지 않는 경우에 예외를 throw합니다. 

원본 기사 (https://clickhouse.tech/docs/en/query_language/functions/ext_dict_functions/) <! - hide ->
