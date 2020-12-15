--- 
machine_translated : true 
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd 
toc_priority : 33 
toc_title : "\ u7F6E \ u63DB \ u30DE \ u30FC \ u30B8 \ u30C4 \ u30EA \ u30FC" 
--- 

# 대체 병합 트리 {#replacingmergetree} 

엔진은 [메루게쯔리 (mergetree .md # table_engines-mergetree) 동일한 기본 키 값이 중복 된 항목을 삭제한다는 점에서,보다 정확하게는 동일합니다 [정렬 키 (mergetree.md) 값). 

데이터 중복 제거는 병합 중에 만 발생합니다. 병합은 미지의 시간에 백그라운드에서 발생하기 때문에 계획 할 수 없습니다. 일부 데이터는 처리되지 않은 상태로되는 경우가 있습니다. 예약되지 않은 병합을 실행하려면`OPTIMIZE` 쿼리는 그것을 사용하여 계산되지 않으므로`OPTIMIZE` 쿼리는 대량의 데이터를 읽고 씁니다. 

따라서`ReplacingMergeTree`에 적합한 청산 나와 중복 데이터를 배경으로 저장하기위한 공간을 보장하는 것은 아니지만 중복있다. 

## 테이블 만들기 {# creating a-table} 

```sql  
CREATE TABLE [IF NOT EXISTS] [db] table_name [ON CLUSTER cluster]
( 
    name1 [type1] [DEFAULT | MATERIALIZED | ALIAS expr1, 
    name2 [type2] [DEFAULT | MATERIALIZED | ALIAS expr2, 
    ... 
) ENGINE = ReplacingMergeTree (ver) 
[PARTITION BY expr] 
[ORDER BY expr] 
[ PRIMARY KEY expr] 
[SAMPLE BY expr] 
[SETTINGS name = value, ...] 
``` 

설명 요청 매개 변수의 참조 요청에 대한 설명 (../../../ sql-reference / statements / create. md) 

** ReplacingMergeTree 파라미터 ** 

-`ver` - column with version : Type`UInt *``Date` 또는`DateTime`. 모든 매개 변수. 

    병합시`ReplacingMergeTree` 동일한 기본 키를 가진 모든 행에서 하나만 남기고 있습니다 : 

    - 선택 범위의 마지막에있는 경우`ver` 설정되어 있지 않습니다. 
    - 최대 버전은 다음의 경우`ver` 지정. 

** 쿼리 절 **

을 만들 때`ReplacingMergeTree` 같은 테이블 [어구] (mergetree.md)을 만들 때처럼 필요합니다. `MergeTree` 테이블. 

<details markdown = "1"> 

<summary> 추천되고 있지 않습니다 법률 테이블을 만들 </ summary> 

!!! attention "주의" 
    가능하면 이전 프로젝트를 위의 방법으로 전환하십시오. 

```sql 
CREATE TABLE [IF NOT EXISTS] [db] table_name [ON CLUSTER cluster] 
( 
    name1 [type1] [DEFAULT | MATERIALIZED | ALIAS expr1, 
    name2 [type2] [DEFAULT | MATERIALIZED | ALIAS expr2, 
    ... . 
) ENGINE [=] ReplacingMergeTree (date-column [, sampling_expression] (primary, key), index_granularity [ver) 
``` 

다음을 제외한 모든 매개 변수`ver`와 같은 의미를 가진`MergeTree`. 

- `ver` - 버전 열. 모든 매개 변수. 설명은 위의 텍스트를 참조하십시오. 

</ details>

원본 기사 (https://clickhouse.tech/docs/en/operations/table_engines/replacingmergetree/) <! - hide ->
