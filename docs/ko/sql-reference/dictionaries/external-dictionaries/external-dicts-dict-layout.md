---
machine_translated : true
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd
toc_priority : 41
toc_title : "\ u30E1 \ u30E2 \ u30EA \ u3078 \ u306E \ u8F9E \ u66F8 \ u306E \ u683C \ u7D0D"
---

# 메모리에 사전에 저장 {# dicts-external-dicts-dict-layout}

사전을 메모리에 저장하려면 여러 가지 방법이 있습니다.

우리는 추천합니다 [평면 (# flat), 해시 (# dicts-external_dicts_dict_layout-hashed) 및 complex_key_hashed (# complex-key-hashed) 최적의 성능을 제공하는지 어느 것이.

캐시 권장되지 않는 일이 될 수있는 성능과 어려움의 선정에 최적의 파라미터. 섹션의 자세히보기 "캐시] (# cache)".

하는 방법은 몇 가지 있지만, 이번에는 개선 사전 성능 :

- 나중에 사전을 조작하는 함수를 호출합니다`GROUP BY`.
- 추출 특성을 injective로 표시합니다. 다른 속성 값이 다른 키에 해당하는 경우, 속성은 injective이라고합니다. 그래서 때`GROUP BY` 키는 속성 값을 가져 오는 함수를 사용하면이 함수는 자동으로`GROUP BY`.

ClickHouse는 사전의 오류에 대한 예외를 생성합니다. 오류의 예 :

- 사용중인 사전을 읽을 수 없습니다.
- 쿼리 오류`cached` 사전.

외부 사전의 목록과 그 상태는`system.dictionaries` 테이블.

설정은 다음과 같습니다 :

```xml
<yandex>
    <dictionary>
        ...
        <layout>
            <layout_type>
                <! - layout settings ->
            </ layout_type>
        </ layout>
        ...
    </ dictionary>
</ yandex>
```

해당하는 DDL- 쿼리 (../../ statements / create.md # create-dictionary-query) :

```sql
CREATE DICTIONARY (...)
...
LAYOUT (LAYOUT_TYPE (param value)) - layout settings
...
```

## 사전을 메모리에 저장하는 방법 {# ways-to-store-dictionaries-in-memory}

- 평면 (# flat)
- 해시 (# dicts-external_dicts_dict_layout-hashed)
- [sparse_hashed (# dicts-external_dicts_dict_layout-sparse_hashed)
- 캐시 (# cache)
- [직접] (# direct)
- [range_hashed (# range-hashed)
- [complex_key_hashed (# complex-key-hashed)
- [complex_key_cache (# complex-key-cache)
- [ip_trie (# ip-trie)

### 평면 {#flat}

사전은 완전히 평면 배열의 형태로 메모리에 저장됩니다. 사전은 얼마나 많은 메모리를 사용하고 있습니까? 금액은 최대의 키의 크기에 비례합니다 (사용되는 공간).

사전 키에는`UInt64` 유형과 값은 500,000으로 제한되어 있습니다. 사전을 만들 때 큰 키가 발견 된 경우 ClickHouse는 예외를 throw하고 사전을 작성하지 않습니다.

모든 종류의 근원 대응하고 있습니다. 업데이트시에는 파일 또는 테이블의 데이터가 전체로로드됩니다.

이 방법은 최고 성능에서 사용할 수있는 모든 방법을 포함하는 사전입니다.

설정 예 :

```xml
<layout>
  <flat />
</ layout>
```

또는

```sql
LAYOUT (FLAT ())
```

### 해시 {# dicts-external_dicts_dict_layout-hashed}

사전 해시 테이블의 형태로 메모리에 완전히 저장됩니다. 사전에 실제로 어떤 식별자를 가지는 임의의 수의 요소를 포함 할 수 있으며, 키의 수는 수백만 항목에 도달 할 수 있습니다.

모든 종류의 근원 대응하고 있습니다. 업데이트시에는 파일 또는 테이블의 데이터가 전체로로드됩니다.

설정 예 :

```xml
<layout>
  <hashed />
</ layout>
```

또는

```sql
LAYOUT (HASHED ())
```

### sparse_hashed {# dicts-external_dicts_dict_layout-sparse_hashed}

유사한`hashed`이 사용 메모리 찬 이상의 CPU입니다.

설정 예 :

```xml
<layout>
  <sparse_hashed />
</ layout>
```

```sql
LAYOUT (SPARSE_HASHED ())
```

### complex_key_hashed {# complex-key-hashed}

이 유형의 저장은 퇴비와 사용을 위해입니다 키 (external-dicts-dict-structure.md)와 유사한`hashed`.

설정 예 :

```xml
<layout>
  <complex_key_hashed />
</ layout>
```

```sql
LAYOUT (COMPLEX_KEY_HASHED ())
```

### range_hashed {# range-hashed}

사전은 범위와 해당 값의 정렬 된 배열을 가지는 해시 테이블 형식으로 메모리에 저장됩니다.

이 스토리지 메소드는 해시 처리와 마찬가지로 작동 키 이외에 날짜 / 시간 (임의의 숫자) 범위를 사용할 수 있습니다.

예 :이 표에는 각 광고주 할인이 다음 형식으로 포함되어 있습니다 :

```text
+ --------- | ------------- | ------------- | ------ +
| advertiser id | discount start date | discount end date | amount |
+ =============== + ===================== + =========== ======== + ======== +
| 123 | 2015-01-01 | 2015-01-15 | 0.15 |
+ --------- | ------------- | ------------- | ------ +
| 123 | 2015-01-16 | 2015-01-31 | 0.25 |
+ --------- | ------------- | ------------- | ------ +
| 456 | 2015-01-01 | 2015-01-15 | 0.05 |
+ --------- | ------------- | ------------- | ------ +
```

날짜 범위의 샘플을 사용하려면`range_min`와`range_max` 요소 구조 (external-dicts-dict-structure.md). 이러한 요소의 요소가 포함되어해야`name` 하면`type` (만약`type` 지정되어 있지 않은 경우, 디폴트의 형태는 use-Date됩니다). `type` 모든 숫자 형식 (Date / DateTime / UInt64 / Int32 / others)를 지정할 수 있습니다.

예 :

```xml
<structure>
    <id>
        <name> Id </ name>
    </ id>
    <range_min>
        <name> first </ name>
        <type> Date </ type>
    </ range_min>
    <range_max>
        <name> last </ name>
        <type> Date </ type>
    </ range_max>
    ...
```

또는

```sql
CREATE DICTIONARY somedict (
    id UInt64,
    first Date,
    last Date
)
PRIMARY KEY id
LAYOUT (RANGE_HASHED ())
RANGE (MIN first MAX last)
```

이 사전을 사용하려면 추가 인수를 전달해야합니다. `dictGetT` 범위가 선택되는 함수 :

```sql
dictGetT ( 'dict_name', 'attr_name', id, date)
```

이 함수는 지정된 값을 반환합니다`id`s 및 전달 된 날짜를 포함한 날짜 범위.

알고리즘의 세부 사항 :

- 만약`id`가 없거나 범위가 없습니다. `id` 사전의 기본값을 반환합니다.
- 중복 범위가있는 경우 any를 사용할 수 있습니다.
- 범위 구분 기호가`NULL` 또는 잘못된 날짜 (1900-01-01 또는 2039-01-01 등) 범위는 열려 있습니다. 범위는 양쪽으로 열 수있다.

설정 예 :

```xml
<yandex>
        <dictionary>

                ...

                <layout>
                        <range_hashed />
                </ layout>

                <structure>
                        <id>
                                <name> Abcdef </ name>
                        </ id>
                        <range_min>
                                <name> StartTimeStamp </ name>
                                <type> UInt64 </ type>
                        </ range_min>
                        <range_max>
                                <name> EndTimeStamp </ name>
                                <type> UInt64 </ type>
                        </ range_max>
                        <attribute>
                                <name> XXXType </ name>
                                <type> String </ type>
                                <null_value />
                        </ attribute>
                </ structure>

        </ dictionary>
</ yandex>
```

또는

```sql
CREATE DICTIONARY somedict (
    Abcdef UInt64,
    StartTimeStamp UInt64,
    EndTimeStamp UInt64,
    XXXType String DEFAULT ''
)
PRIMARY KEY Abcdef
RANGE (MIN StartTimeStamp MAX EndTimeStamp)
```

### 캐시 {#cache}

사전은 고정 된 수의 셀을 가진 캐시에 저장됩니다. 이러한 세포를 포함한 사용 빈도의 높은 있습니다.

사전을 검색 할 때 먼저 캐시가 검색됩니다. 각 데이터 블록에 대해 캐시에 없거나 오래된 모든 키가 소스에서 요구됩니다. `SELECT attrs ... FROM db.table WHERE id IN (k1, k2, ...)`. 수신 된 데이터는 캐시에 기록됩니다.

캐시 사전 만료 [생애] (external-dicts-dict-lifetime.md) 캐시의 데이터 설정이 가능합니다. 더 많은 시간이`lifetime` 셀에 데이터를로드 한 후 경과 한 경우 셀의 값은 사용되지 않고 다음 사용할 필요가있을 때 회수됩니다.
이것은 사전을 저장하는 모든 방법 중 가장 효과가 없습니다. 캐시의 속도는 올바른 설정과 사용 시나리오에 강하게 의존합니다. 캐시 유형 사전은 적중률이 충분히 높은 (권장 99 % 이상) 경우에만 제대로 작동합니다. 평균 적중률을 표시 할 수 있습니다`system.dictionaries` 테이블.

캐시 빠호마`LIMIT` 및 외부 사전을 사용하여 함수를 호출합니다.

지원 [소스] (external-dicts-dict-sources.md) : MySQL, ClickHouse 실행 파일, HTTP.

설정 예 :

```xml
<layout>
    <cache>
        <! - The size of the cache, in number of cells. Rounded up to a power of two ->
        <size_in_cells> 1000000000 </ size_in_cells>
    </ cache>
</ layout>
```

또는

```sql
LAYOUT (CACHE (SIZE_IN_CELLS 1000000000))
```

설정하는 데 충분한 크기의 캐시 크기입니다. 당신은 세포의 수를 선택하기 위해 실험해야합니다 :

1. 값을 설정합니다.
2. 주행 쿼리까지 캐시를 완전히.
3.를 사용하여 메모리 사용량을 평가하는`system.dictionaries` 테이블.
4. 필요한 메모리 소비에 도달 할 때까지 셀 수를 증감합니다.

!!! warning "경고"
    ClickHouse를 소스로 사용하지 마십시오.

### complex_key_cache {# complex-key-cache}

이 유형의 저장은 퇴비와 사용을 위해입니다 키 (external-dicts-dict-structure.md)와 유사한`cache`.

### 직접 {#direct}

사전은 메모리에 저장되지 않고 요청을 처리하는 동안 소스에 직접 이동합니다.

사전 키에는`UInt64` 타입.

모든 유형의 소스 (external-dicts-dict-sources.md) 로컬 파일

설정 예 :

```xml
<layout>
  <direct />
</ layout>
```

또는

```sql
LAYOUT (DIRECT ())
```

### ip_trie {# ip-trie}

이 유형의 저장 매핑하는 네트워크 접두사 (IP 주소에 대한 메타 데이터 등의 ASN.

예 : 테이블을 포함하는 네트워크 접두사 및 그 대응으로 수 및 국가 코드 :

```text
  + ----------- | ----- | ------ +
  | prefix | asn | cca2 |
  + ================= + ======= + ======== +
  | 202.79.32.0/20 | 17501 | NP |
  + ----------- | ----- | ------ +
  | 2620 : 0 : 870 :: / 48 | 3856 | US |
  + ----------- | ----- | ------ +
  | 2a02 : 6b8 : 1 :: / 48 | 13238 | RU |
  + ----------- | ----- | ------ +
  | 2001 : db8 :: / 32 | 65536 | ZZ |
  + ----------- | ----- | ------ +
```

이 유형의 레이아웃을 사용하는 경우, 구조에 복합 키가 필요합니다.

예 :

```xml
<structure>
    <key>
        <attribute>
            <name> prefix </ name>
            <type> String </ type>
        </ attribute>
    </ key>
    <attribute>
            <name> asn </ name>
            <type> UInt32 </ type>
            <null_value />
    </ attribute>
    <attribute>
            <name> cca2 </ name>
            <type> String </ type>
            <null_value> ?? </ null_value>
    </ attribute>
    ...
```

또는

```sql
CREATE DICTIONARY somedict (
    prefix String,
    asn UInt32,
    cca2 String DEFAULT '??'
)
PRIMARY KEY prefix
```

키는 허가 된 IP 접두사를 포함하고있는 문자열 속성 만 필요합니다. 다른 유형은 지원되지 않겠습니까?

쿼리는 같은 함수를 사용해야합니다 (`dictGetT` 튜플 포함) 복합 키를 가진 사전 대해 :

```sql
dictGetT ( 'dict_name', 'attr_name', tuple (ip))
```

이 함수는`UInt32` IPv4의 경우, 또는`FixedString (16)`IPv6의 경우 :

```sql
dictGetString ( 'prefix', 'asn', tuple (IPv6StringToNum ( '2001 : db8 :: 1')))
```

다른 유형은 지원되지 않겠습니까? 이 함수는이 IP 주소에 대응하는 접두사의 속성을 반환합니다. 중복 접두사가있는 경우, 가장 특정 접두사가 반환됩니다.

데이터는`trie` 그것은 RAM에 완전하게 들어갈 필요가 있습니다.

원본 기사 (https://clickhouse.tech/docs/en/query_language/dicts/external_dicts_dict_layout/) <! - hide ->
