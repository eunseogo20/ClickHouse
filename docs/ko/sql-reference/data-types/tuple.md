--- 
machine_translated : true 
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd 
toc_priority : 53 
toc_title : Tuple (T1, T2, ...) 
--- 

# Tuple (t1, T2, ...) {# tuplet1-t2} 

요소의 튜플 각 개인을 가진 [유형] (index.md # data_types) 

쌍은 일시적인 열 그룹화하는 데 사용됩니다. 열은 in식이 쿼리에서 사용되는 lambda 함수의 특정 파라미터를 지정할 때 그룹화 할 수 있습니다. 자세한 내용은 [연산자] (../../ sql-reference / operators / in.md) 및 고차 함수 (../../ sql-reference / functions / higher-order-functions. md) 

튜플은 쿼리의 결과입니다. 이 경우, JSON 이외의 텍스트 형식의 경우, 값은 대괄호로 쉼표 구분됩니다. JSON 형식의 튜플은 배열로 출력됩니다 (대괄호로 묶습니다). 

## 튜플 만들기 {# creating a-tuple} 

함수를 사용하여 튜플을 만들 수 있습니다 : 

```sql 
tuple (T1, T2, ...) 
``` 

튜플의 작성 예 : 
 
```sql
SELECT tuple (1 'a') AS x, toTypeName (x) 
``` 

```text 
┌─x───────┬─toTypeName (tuple (1 'a')) ─┐ 
│ ( 1 'a') │ Tuple (UInt8, String) │ 
└─────────┴───────────────────────── ──┘ 
``` 

## 데이터 형의 조작 {# working-with-data-types} 

즉시 튜플을 만들 때 ClickHouse은 자동으로 각 인수의 형태를 인수 값을 저장할 수있는 형태의 최소치로 감지합니다. 인수가 NULL (../../ sql-reference / syntax.md # null-literal) 튜플 요소의 형태는 [Null 가능 (nullable.md) 

자동 데이터 형 감지 예 : 

```sql 
SELECT tuple (1, NULL) AS x, toTypeName (x) 
``` 

```text 
┌─x────────┬─toTypeName (tuple (1, NULL)) ──────┐ 
│ (1, NULL) │ Tuple (UInt8, Nullable (Nothing)) │ 
└──────────┴───────────────────── ────────────┘ 
```

원본 기사 (https://clickhouse.tech/docs/en/data_types/tuple/) <! - hide ->
