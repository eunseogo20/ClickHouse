---
machine_translated : true
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd
toc_priority : 39
toc_title : "\ u4E00 \ u822C \ u7684 \ u306A \ u8AAC \ u660E"
---

# 외부 사전 {# dicts-external-dicts}

다양한 데이터 소스에서 자신의 사전을 추가 할 수 있습니다. 사전 데이터 소스에 로컬 텍스트 또는 실행 파일, HTTP 리소스 또는 다른 DBMS를 사용할 수 있습니다. 자세한 내용은 "외부 사전의 원본 (external-dicts-dict-sources.md)".

클릭 하우스 :

- 전체 또는 부분적으로 RAM에 사전을 저장합니다.
- 사전을 정기적으로 업데이트하고 결손치를 동적으로로드합니다. 즉, 사전 동적으로로드 할 수 있습니다.
- Xml 파일에서 외부 사전을 만들 수 있습니다. [DDL 쿼리 (../../ statements / create.md # create-dictionary-query).

외부 사전의 구성은 하나 이상의 xml 파일로 저장할 수 있습니다. 설정 경로는 [dictionaries_config (../../../ operations / server-configuration-parameters / settings.md # server_configuration_parameters-dictionaries_config) 매개 변수.

사전 서버를 시작하거나 처음 사용할 때로드 할 수 있습니다. [dictionaries_lazy_load (../../../ operations / server-configuration-parameters / settings.md # server_configuration_parameters-dictionaries_lazy_load) 설정.

그 [사전] (../../../ operations / system-tables.md # system_tables-dictionaries) 시스템 테이블에 대한 정보가 포함되어 사전에 설정됩니다. 각 사전은 거기에 있습니다 :

- 사전 상태.
- 구성 매개 변수.
- 인식과 같은 양의 메모리 할당을위한 사전은 다수의 쿼리에서 사전에 성공한 것으로 간주됩니다.

사전 구성 파일의 형식은 다음과 같습니다 :

```xml
<yandex>
    <comment> An optional element with any content. Ignored by the ClickHouse server. </ comment>

    <! - Optional element. File name with substitutions ->
    <include_from> /etc/metrika.xml </ include_from>


    <dictionary>
        <! - Dictionary configuration ->
        <! - There can be any number of <dictionary> sections in the configuration file ->
    </ dictionary>

</ yandex>
```

당신은 할 수 설정 (external-dicts-dict.md) 같은 파일 내의 임의의 수의 사전.

[사전 DDL 쿼리 (../../ statements / create.md # create-dictionary-query) 서버 구성에 추가 레코드가 필요하지 않습니다. 이 일을 사전으로 일류의 몸처럼 테이블이나 뷰.

!!! attention "주의"
    작은 사전의 값을 변환하려면 다음과 같이 설명합니다`SELECT` 쿼리 (참조 [변환] (../../../ sql-reference / functions / other-functions.md) 기능) . 이 기능은 외부 사전과는 관계 없습니다.

##도 참조. {# ext-dicts-see-also}

- 외부 사전 구성 (external-dicts-dict.md)
- 메모리에 사전 저장 (external-dicts-dict-layout.md)
- 사전 업데이트 (external-dicts-dict-lifetime.md)
- 외부 사전의 원본 (external-dicts-dict-sources.md)
- 사전 키와 필드 (external-dicts-dict-structure.md)
- 외부 사전을 조작하기위한 함수 (../../../ sql-reference / functions / ext-dict-functions.md)

원본 기사 (https://clickhouse.tech/docs/en/query_language/dicts/external_dicts/) <! - hide ->
