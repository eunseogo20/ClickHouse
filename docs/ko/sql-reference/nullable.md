--- 
machine_translated : true 
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd 
toc_priority : 54 
toc_title : "Null \ u53EF \ u80FD" 
--- 

# Nullable (형명) {# data_type-nullable} 

있는 특별한 마커 (NULL] (../../ sql-reference / syntax.md))를 나타낸다. "missing value"와 함께 정상치를 허용하는`TypeName` 예`Nullable (Int8)`유형 열이 포함될 수 있습니다`Int8` 값을 입력하고 값이없는 행에 저장됩니다`NULL`. 

때문에`TypeName` 복합 데이터 형식을 사용할 수 없습니다 [배열] (array.md) 및 튜플 (tuple.md) 복합 데이터 유형은`Nullable` 다음과 같은 형식의 값`Array (Nullable ( Int8))`. 

A`Nullable` type 필드 수없는 포함 된 테이블을 만듭니다. 

`NULL`의 기본값입니다`Nullable` ClickHouse 서버 구성에서 특히 지정이없는 한 입력합니다. 

## 스토리지 기능 {# storage-features}

저장하려면`Nullable` 테이블의 열에 값을 입력하면 ClickHouse 다른 파일을 사용합니다`NULL` 값을 가진 보통의 파일 이외에 마스크. 마스크 파일의 항목은 ClickHouse가`NULL` 테이블 행마다 해당 데이터 유형의 기본값. 추가 파일을 위해`Nullable` column 비슷한 보통 사람과 비교하여 추가 저장 공간을 소비합니다. 

!!! info "주" 
    를 사용하여`Nullable` 대부분의 경우 성능에 영향을 미칩니다. 

## 예 {# usage-example} 

```sql 
CREATE TABLE t_null (x Int8, y Nullable (Int8)) ENGINE TinyLog 
``` 

```sql 
INSERT INTO t_null VALUES (1, NULL), (2, 3 ) 
``` 

```sql 
SELECT x + y FROM t_null 
``` 

```text 
┌─plus (x, y) ─┐ 
│ ᴺᵁᴸᴸ │ 
│ 5 │ 
└──────────── ┘ 
``` 

원래 기사 (https://clickhouse.tech/docs/en/data_types/nullable/) <! - hide ->
