--- 
machine_translated : true 
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd 
toc_priority : 45 
toc_title : "\ u968E \ u5C64 \ u8F9E \ u66F8" 
--- 

# 계층 사전 {# hierarchical-dictionaries} 

ClickHouse 계층 사전을 지원합니다 숫자 키] (external-dicts-dict-structure.md # ext_dict-numeric-key) 

다음 계층 구조를보고하십시오 : 

```text 
0 (Common parent) 
│ 
├── 1 (Russia) 
│ │ 
│ └── 2 (Moscow) 
│ │ 
│ └── 3 (Center) 
│ 
└── 4 (Great Britain) 
    │ 
    └── 5 (London) 
``` 

이 계층은 다음 사전 테이블로 나타낼 수 있습니다. 

| region_id | parent_region | region_name |
| ------------ | ---------------- | -------------- | 
| 1 | 0 | 러시아 | 
| 2 | 1 | 모스크바 | 
| 3 | 2 | 중앙 | 
| 4 | 0 | 영국 | 
| 5 | 4 | 런던 | 

이 테이블 열`parent_region` 여기에는 요소의 가장 가까운 부모의 키가 포함 됩니다. 

클릭 하우스는 계층 구조 (external-dicts-dict-structure.md # hierarchical-dict-attr) 속성 외부 사전 (index.md) 속성. 이 속성을 사용하면 위와 같은 계층 사전을 구성 할 수 있습니다. 

그 [dictGetHierarchy (../../../ sql-reference / functions / ext-dict-functions.md # dictgethierarchy) 함수를 사용하면 요소의 부모 체인을 얻을 수 있습니다. 

이 예에서는 dictionary의 구조는 다음과 같습니다 : 

```xml 
<dictionary> 
    <structure> 
        <id>
            <name> region_id </ name> 
        </ id> 
 
        <
            <name> parent_region </ name> 
            <type> UInt64 </ type> 
            <null_value> 0 </ null_value> 
            <hierarchical> true </ hierarchical> 
        </ attribute> 

        <attribute > 
            <name> region_name </ name> 
            <type> String </ type> 
            <null_value> </ null_value> 
        </ attribute> 

    </ structure> 
</ dictionary> 
``` 

원래 기사 (https : // clickhouse .tech / docs / en / query_language / dicts / external_dicts_dict_hierarchical /) <! - hide ->
