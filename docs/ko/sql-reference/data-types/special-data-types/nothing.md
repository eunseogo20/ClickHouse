--- 
machine_translated : true 
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd 
toc_priority : 60 
toc_title : "\ u4F55 \ u3082 \ u306A \ u3044" 
--- 

# 아무것도 {#nothing} 

이 데이터 형식의 유일한 목적은 값이 예상되지 않는 케이스를 나타내는 것입니다. 그래서 만들 수 없습니다`Nothing` 유형 값. 

예를 들어, 리터럴 [NULL] (../../../ sql-reference / syntax.md # null-literal) 타입의있다`Nullable (Nothing)`자세히보기 [Null 가능 (../ ../../sql-reference/data-types/nullable.md). 

그`Nothing` 형은 빈 배열을 나타내는 데 사용할 수 있습니다 : 

```sql 
SELECT toTypeName (array ()) 
`` ` 

```text 
┌─toTypeName (array ()) ─┐ 
│ array (Nothing) │ 
└─────────────────────┘ 
```

원본 기사 (https://clickhouse.tech/docs/en/data_types/special_data_types/nothing/) <! - hide ->
