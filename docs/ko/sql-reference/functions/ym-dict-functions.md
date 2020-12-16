---
machine_translated : true
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd
toc_priority : 59
toc_title : "Yandex \ u3067 \ u306E \ u4F5C \ u696D \ u3002 \ u30E1 \ u30C8 \ u30EA \ u30AB \ u8F9E \ u66F8"
---

# Yandex에서 작업하기위한 기능. 메틀리카 사전 {# functions-for-working-with-yandex-metrica-dictionaries}

다음과 같은 기능을 작동하려면 서버 설정에서 모든 Yandex을 취득하기위한 경로와 주소를 지정해야합니다. 메틀리카 사전. 사전은 이러한 함수의 최초의 호출시에로드됩니다. 참조 목록을로드 할 수없는 경우는 예외가 슬로우됩니다.

에 대한 정보 목록의 항목을 참조하십시오 "Dictionaries"

## 여러 지오베스 {# multiple-geobases}

ClickHouse은 특정 지역이 속한 국가의 다양한 관점을 지원하기 위해 여러 대안 지오베스 (지역 계층)을 동시에 사용하는 것을 지원하고 있습니다.

그 'clickhouse-server'config는 지역 계층을 가진 파일을 지정합니다 ::`<path_to_regions_hierarchy_file> /opt/geo/regions_hierarchy.txt </ path_to_regions_hierarchy_file>`

이 파일 이외에도 이름에 _ 기호와 접미사가 추가 된 근처의 파일도 검색합니다 (파일 확장자 앞에).
예를 들어, 다음 파일을 찾습니다`/ opt / geo / regions_hierarchy_ua.txt` 존재하는 경우.

`ua` 사전 키라고합니다. 접미사가없는 사전의 경우 키는 빈 문자열입니다.

모든 사전은 실행시에 다시로드됩니다 (builtin_dictionaries_reload_interval 구성 매개 변수에 정의 된 바와 같이 일정한 초마다 또는 기본적으로 시간에 한 번). 그러나 사용 가능한 사전 목록은 서버를 시작할 때 한 번에 정의됩니다.

All functions for working with regions have an optional argument at the end - the dictionary key. It is referred to as the geobase.
예 :

```sql
regionToCountry (RegionID) - Uses the default dictionary : /opt/geo/regions_hierarchy.txt
regionToCountry (RegionID '') - Uses the default dictionary : /opt/geo/regions_hierarchy.txt
regionToCountry (RegionID 'ua') - Uses the dictionary for the 'ua'key : /opt/geo/regions_hierarchy_ua.txt
```

### 지역 트 시티 (id \, geobase \]) {# regiontocityid-geobase}

Accepts a UInt32 number - the region ID from the Yandex geobase. If this region is a city or part of a city, it returns the region ID for the appropriate city. Otherwise, returns 0.

### 레기온 트 아래아 (id \, geobase \]) {# regiontoareaid-geobase}

영역을 영역으로 변환합니다 (지오베스 유형 5). 다른 모든 방법으로이 함수는 다음과 같이 동일합니다 'regionToCity'.

```sql
SELECT DISTINCT regionToName (regionToArea (toUInt32 (number) 'ua'))
FROM system.numbers
LIMIT 15
```

```text
┌─regionToName (regionToArea (toUInt32 (number) \ 'ua \')) ─┐
│ │
│ Moscow and Moscow region │
│ St. Petersburg and Leningrad region │
│ Belgorod region │
│ Ivanovsk region │
│ Kaluga region │
│ Kostroma region │
│ Kursk region │
│ Lipetsk region │
│ Orlov region │
│ Ryazan region │
│ Smolensk region │
│ Tambov region │
│ Tver region │
│ Tula region │
└───────────────────────────────────────────────── ─────┘
```

### 레지옹 트 디스트릭트 (id \, geobase \]) {# regiontodistrictid-geobase}

지역 연방 구 (지오베스 유형 4)로 변환합니다. 다른 모든 방법으로이 함수는 다음과 같이 동일합니다 'regionToCity'.

```sql
SELECT DISTINCT regionToName (regionToDistrict (toUInt32 (number) 'ua'))
FROM system.numbers
LIMIT 15
```

```text
┌─regionToName (regionToDistrict (toUInt32 (number) \ 'ua \')) ─┐
│ │
│ Central federal district │
│ Northwest federal district │
│ South federal district │
│ North Caucases federal district │
│ Privolga federal district │
│ Ural federal district │
│ Siberian federal district │
│ Far East federal district │
│ Scotland │
│ Faroe Islands │
│ Flemish region │
│ Brussels capital region │
│ Wallonia │
│ Federation of Bosnia and Herzegovina │
└───────────────────────────────────────────────── ─────────┘
```

### regionToCountry (id \, geobase \]) {# regiontocountryid-geobase}

지역을 국가로 변환합니다. 다른 모든 방법으로이 함수는 다음과 같이 동일합니다 'regionToCity'.
예 :`regionToCountry (toUInt32 (213)) = 225` 모스크바 (213) 러시아 (225)로 변환합니다.

### 레지옹 트 콘텐츠 (id \, geobase \]) {# regiontocontinentid-geobase}

지역을 대륙으로 변환합니다. 다른 모든 방법으로이 함수는 다음과 같이 동일합니다 'regionToCity'.
예 :`regionToContinent (toUInt32 (213)) = 10001` 모스크바 (213)를 유라시아 (10001)로 변환합니다.

### regionToTopContinent (#regiontotopcontinent) {# regiontotopcontinent-regiontotopcontinent}

지역의 계층 구조에서 가장 높은 대륙을 검색합니다.

** 구문 **

```sql
regionToTopContinent (id [, geobase]);
```

** 파라미터 **

-`id` - Region ID from the Yandex geobase [UInt32 (../../ sql-reference / data-types / int-uint.md).
-`geobase` - Dictionary key. See 다중 지오베스 (# multiple-geobases) [문자열] (../../ sql-reference / data-types / string.md). 어떤.

** 반환 값 **

- 최상위 대륙의 식별자 (후자는 지역 계층을 올라갈 때).
- 없으면 0.

유형 :`UInt32`.

### 지역 지형 퓨 레이션 (id \, geobase \]) {# regiontopopulationid-geobase}

지역의 인구를 가져옵니다.
모집단은 지오베스을 가진 파일에 기록 할 수 있습니다. 섹션을 참조 "External dictionaries"
인구가 지역에 기록되어 있지 않은 경우는 0을 돌려줍니다.
Yandex 지오베스에서는 아이 지역에 인구가 기록되지만 부모 영역에 대해서는 기록되지 않습니다.

### 레기오닌 (lhs, rhs \, geobase \]) {# regioninlhs-rhs-geobase}

A 있는지 확인 'lhs'지역은 a에 속한다 'rhs'지역. UInt8가 속한 경우 1 속하지 않으면 0을 반환합니다.
The relationship is reflexive - any region also belongs to itself.

### 지역 하이 아키텍처 (id \, geobase \]) {# regionhierarchyid-geobase}

Accepts a UInt32 number - the region ID from the Yandex geobase. Returns an array of region IDs consisting of the passed region and all parents along the chain.
예 :`regionHierarchy (toUInt32 (213)) = [213,1,3,225,10001,10000]`.

### 레지옹 베트남 (id \, lang \]) {# regiontonameid-lang}

Accepts a UInt32 number - the region ID from the Yandex geobase. A string with the name of the language can be passed as a second argument. Supported languages ​​are : ru, en, ua, uk, by, kz, tr. If the second argument is omitted, the language 'ru'is used. If the language is not supported, an exception is thrown. Returns a string - the name of the region in the corresponding language. If the region with the specified ID does not exist, an empty string is returned.

`ua`와`uk` 다시 클릭합니다.

원본 기사 (https://clickhouse.tech/docs/en/query_language/functions/ym_dict_functions/) <! - hide ->
