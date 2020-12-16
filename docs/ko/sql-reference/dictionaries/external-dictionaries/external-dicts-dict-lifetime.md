---
machine_translated : true
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd
toc_priority : 42
toc_title : "\ u8F9E \ u66F8 \ u306E \ u66F4 \ u65B0"
---

# 사전의 업데이트 {# dictionary-updates}

ClickHouse 정기적으로 사전을 업데이트합니다. 완전히 다운로드 된 사전의 업데이트 간격 및 캐시 된 사전 비활성화 간격은`<lifetime>`초 태그.

사전 업데이트 (처음 사용을위한로드 제외) 쿼리를 차단하지 않습니다. 갱신시에는 이전 버전의 사전이 사용됩니다. 업데이트 중 오류가 발생하면 오류는 서버 로그에 기록되고 쿼리는 이전 버전의 사전을 계속 사용합니다.

설정 예 :

```xml
<dictionary>
    ...
    <lifetime> 300 </ lifetime>
    ...
</ dictionary>
```

```sql
CREATE DICTIONARY (...)
...
LIFETIME (300)
...
```

설정`<lifetime> 0 </ lifetime>`(`LIFETIME (0)`) 사전의 업데이트를 방지합니다.

그러나 세션의 시간 간격 업그레이드 ClickHouse을 선정하여 무작위 균일하게 시간 이내 인 것으로 밝혀졌다. 이것은 다수의 서버로 업그레이드 할 때 사전 소스의 부하를 분산하기 위해 필요합니다.

설정 예 :

```xml
<dictionary>
    ...
    <lifetime>
        <min> 300 </ min>
        <max> 360 </ max>
    </ lifetime>
    ...
</ dictionary>
```

또는

```sql
LIFETIME (MIN 300 MAX 360)
```

만약`<min> 0 </ min>`와`<max> 0 </ max>`ClickHouse는 시간에 의해 사전을 다시로드하지 않습니다.
이 경우 clickhouse은 사전 설정 파일이 변경된 경우 또는`SYSTEM RELOAD DICTIONARY` 명령이 실행되었다.

업하는 경우에는 사전에 ClickHouse 서버에 적용 다른 논리의 종류에 따라 소스 (external-dicts-dict-sources.md) :

업하는 경우에는 사전에 ClickHouse 서버에 적용 다른 논리의 종류에 따라 소스 (external-dicts-dict-sources.md) :

- 텍스트 파일의 경우 변경 시간을 확인합니다. 시간이 이전에 기록 된 시간과 다를 사전이 업데이트됩니다.
- MyISAM 테이블의 경우 변경 시간은`SHOW TABLE STATUS` 쿼리.
- 다른 소스로부터의 사전은 기본적으로 매번 updated됩니다.

MySQL (InnoDB), ODBC 및 ClickHouse 소스는 매번이 아니라 실제로 변경된 경우에만 사전을 업데이트하는 쿼리를 설정할 수 있습니다. 이렇게하려면 다음과 같이합니다 :

- 사전 테이블에는 소스 데이터의 업데이트 때마다 변경되는 필드가 필요합니다.
- 소스 설정은 변경 필드를 검색하는 쿼리를 지정해야합니다. ClickHouse 서버는 쿼리 결과를 행으로 해석하고이 행이 이전의 상태에 대해 상대적으로 변경된 경우, 사전이 업데이트됩니다. 쿼리를 지정합니다. `<invalidate_query>`의 설정 필드 [소스] (external-dicts-dict-sources.md).

설정 예 :

```xml
<dictionary>
    ...
    <odbc>
      ...
      <invalidate_query> SELECT update_time FROM dictionary_source where id = 1 </ invalidate_query>
    </ odbc>
    ...
</ dictionary>
```

또는

```sql
...
SOURCE (ODBC (... invalidate_query 'SELECT update_time FROM dictionary_source where id = 1'))
...
```

원본 기사 (https://clickhouse.tech/docs/en/query_language/dicts/external_dicts_dict_lifetime/) <! - hide ->
