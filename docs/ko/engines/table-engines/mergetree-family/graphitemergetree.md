--- 
machine_translated : true 
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd 
toc_priority : 38 
toc_title : GraphiteMergeTree 
--- 

# GraphiteMergeTree {#graphitemergetree} 

이 엔진) 흑연 (http://graphite.readthedocs.io/en/latest/index.html) 데이터 이것은 Graphite 데이터 저장소로 ClickHouse을 사용하려는 개발자에게 도움이 될 수 없습니다. 

를 사용할 수 있습니다 ClickHouse 테이블 엔진 흑연의 데이터가 필요없는 장소 rollup이 필요한 경우 rollup을 사용`GraphiteMergeTree` 엔진은 저장량을 줄이고 흑연에서 조회의 효율을 높여줍니다. 

엔진은 속성을 메루게쯔리 (mergetree.md) 

## 테이블 만들기 {# creating-table} 

```sql 
CREATE TABLE [IF NOT EXISTS] [db] table_name [ON CLUSTER cluster] 
( 
    Path String, 
    Time DateTime, 
    Value <Numeric_type> 
    Version <Numeric_type> 
    ... 
) ENGINE = GraphiteMergeTree (config_section) 
[PARTITION BY expr] 
[ORDER BY expr] 
[SAMPLE BY expr] 
[SETTINGS name = value, ...] 
``` 

에 대한 자세한 설명을 참조 제발 [cREATE tABLE (../../../ sql-reference / statements / create.md # create-table-query) 쿼리. 

흑연 데이터 테이블에는 다음 데이터의 다음의 열이 필요합니다 : 

- 밀리 규격명 (흑연 센서) 데이터 형식 :`String`. 

- 메트릭을 측정하는 시간. 데이터 형식 :`DateTime`. 

- 메트릭 값. 데이터 형식 : 임의의 숫자. 

- 지표의 버전. 데이터 형식 : 임의의 숫자. 

    ClickHouse 버전이 동일한 경우 가장 높은 버전 또는 마지막에 쓰여진 행을 저장합니다. 다른 행은 데이터 파트 병합 중에 삭제됩니다. 

이러한 열 이름은 롤업 구성에서 설정해야합니다. 

** GraphiteMergeTree 파라미터 **

-`config_section` - Name of the section in the configuration file, where are the rules of rollup set. 
    ...

** 쿼리 절 ** 

을 만들 때`GraphiteMergeTree` 테이블 같은 [어구] (mergetree.md # table_engine-mergetree-creating a-table)을 만들 때처럼 필요합니다. `MergeTree` 테이블. 

<details markdown = "1"> 

<summary> 추천되고 있지 않습니다 법률 테이블을 만들 </ summary> 

!!! attention "주의" 
    가능하면 이전 프로젝트를 위의 방법으로 전환하십시오. 

```sql 
CREATE TABLE [IF NOT EXISTS] [db] table_name [ON CLUSTER cluster] 
( 
    EventDate Date, 
    Path String, 
    Time DateTime, 
    Value <Numeric_type> 
    Version <Numeric_type> 
) ENGINE [=] GraphiteMergeTree (date-column [, sampling_expression] (primary, key), index_granularity, config_section) 
``` 

다음을 제외한 모든 매개 변수`config_section`와 같은 의미를 가진`MergeTree`.
 
-`config_section` - Name of the section in the configuration file, where are the rules of rollup set. 

</ details> 

## 롤업 구성 {# rollup-configuration} 

롤업 설정은 graphite_rollup (../../../ operations / server-configuration-parameters / settings.md # server_configuration_parameters-graphite) 서버 구성 매개 변수입니다. 매개 변수 이름은 선택 사항입니다. 여러 구성을 만들고 다른 테이블에 사용할 수 있습니다. 

롤업 구성 구조 : 

      required-columns 
      patterns 

### 필요한 열 {# required-columns} 

-`path_column_name` - The name of the column storing the metric name (Graphite sensor) Default value :`Path`. 
-`time_column_name` - The name of the column storing the time of measuring the metric . Default value :`Time`. 
-`value_column_name` - The name of the column storing the value of the metric at the time set in`time_column_name` 기본값 :`Value`.
-`version_column_name` - The name of the column storing the version of the metric . Default value :`Timestamp`. 

### 패턴 {#patterns} 

의 구조`patterns` 섹션 : 

```text 
pattern 
    regexp 
    function 
pattern 
    regexp 
    age + precision 
    ... 
pattern 
    regexp 
    function 
    age + precision 
    ... 
pattern 
    ...  
default
    function 
    age + precision 
    ... 
``` 

!!! warning "주의" 
    패턴은 엄격하게 주문해야 : 

      1. Patterns without`function` or`retention`. 
      1. Patterns with both`function` and`retention`.
      1. Pattern`default`. 

행을 처리 할 때 ClickHouse는`pattern` 섹션 각각의`pattern` (포함`default`) 섹션에는`function` 집계 매개 변수`retention` 변수 또는 둘 다. 메트릭 이름이`regexp`,에서의 규칙`pattern` 섹션 (또는 섹션)이 적용됩니다. `default` 섹션을 사용합니다. 

필드`pattern`와`default` 섹션 : 

-`regexp`- A pattern for the metric name. 
-`age` - The minimum age of the data in seconds. 
        <regexp> click_cost </ regexp> 
        <function> 
-`precision`- How precisely to define the age of the data in seconds. Should be a divisor for 86400 (seconds in a day).
-`function` - The name of the aggregating function to apply to data whose age falls within the range `[age, age + precision]`. 

### 설정 예 {# configuration-example} 

```xml 
<graphite_rollup> 
    < version_column_name> Version </ version_column_name> 
    <pattern> 
        <retention> 
            <age> 0 </ age> 
            <precision> 5 </ precision> 
        </ retention> 
        <retention> 
            <age> 86400 </ age> 
            <precision> 60 </ precision> 
        </ retention> 
    </ pattern> 
    <default> 
        <function> max </ function>
        <retention> 
            <age> 0 </ age>
            <precision> 60 </ precision> 
        </ retention> 
        <retention> 
            <age> 3600 </ age> 
            <precision> 300 </ precision> 
        </ retention> 
        <retention> 
            <age> 86400 </ age> 
            <precision> 3600 </ precision> 
        </ retention> 
    </ default> 
</ graphite_rollup> 
```

원본 기사 (https://clickhouse.tech/docs/en/operations/table_engines/graphitemergetree/) <! - hide ->
