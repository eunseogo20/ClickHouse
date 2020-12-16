---
machine_translated : true
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd
toc_priority : 39
toc_title : "\ u5185 \ u90E8 \ u8F9E \ u66F8"
---

# 내부 사전 {#internal_dicts}

ClickHouse에는 지오베스를 조작하는 기본 기능이 포함되어 있습니다.

이렇게하면 :

- 지역의 ID를 사용하여 원하는 언어로 그 이름을 가져옵니다.
- 지역의 ID를 사용하여 도시, 지역, 연방 지구, 국가, 또는 대륙의 ID를 가져옵니다.
- 특정 지역이 다른 지역의 일부인지 여부를 확인합니다.
- 부모 영역의 체인을 가져옵니다.

모든 기능 지원 "translocality"지역의 소유권에 관한 다른 관점을 동시에 사용하는 능력. 자세한 내용은 "Functions for working with Yandex.Metrica dictionaries"

기본 패키지는 내부 사전은 비활성화되어 있습니다.
따라서 strncasecmp 매개 변수`path_to_regions_hierarchy_file`와`path_to_regions_names_files` 서버 설정 파일.

Geobase는 텍스트 파일에서로드됩니다.

을 넣어`regions_hierarchy * .txt` 파일`path_to_regions_hierarchy_file` 디렉토리. 이 구성 매개 변수는`regions_hierarchy.txt` 파일 (기본 지역 계층) 및 기타 파일 (`regions_hierarchy_ua.txt`) 같은 디렉토리에 있어야합니다.

을 넣어`regions_names _ *. txt` 파일`path_to_regions_names_files` 디렉토리.

를 만들 수 있습니다 이러한 파일. 파일 형식은 다음과 같습니다 :

`regions_hierarchy * .txt` : TabSeparated (헤더없이) 열 :

- 지역 ID (`UInt32`)
- 부모 지역 ID (`UInt32`)
- 지역 유형 (`UInt8`) : 1 대륙 3 국 4 연방 지구 5 지역 6 도시.
- 인구 (`UInt32`) - optional column

`regions_names _ *. txt` : TabSeparated (헤더없이) 열 :

- 지역 ID (`UInt32`)
- 지역 이름 (`String`) - Can not contain tabs or line feeds, even escaped ones.

평면 어레이는 RAM에 저장하는 데 사용됩니다. 따라서 Id 백만을 초과해서는 없습니다.

사전은 서버를 다시 시작하지 않고 업데이트 할 수 있습니다. 그러나 사용 가능한 사전 설정은 업데이트되지 않습니다.
업데이트의 경우 파일의 변경 시간이 체크됩니다. 파일이 변경된 경우에는 사전이 업데이트됩니다.
변경 사항을 확인하는 간격은`builtin_dictionaries_reload_interval` 매개 변수.
사전 업데이트 (처음 사용할 때로드 제외) 쿼리를 차단하지 않습니다. 업데이트 중 쿼리는 이전 버전의 사전을 사용합니다. 업데이트 중 오류가 발생하면 오류는 서버 로그에 기록되고 쿼리는 이전 버전의 사전을 계속 사용합니다.

Geobase에서 사전을 정기적으로 업데이트하는 것이 좋습니다. 업데이트 중 새 파일을 생성하고 다른 위치에 씁니다. 모든 준비가되면 서버가 사용하는 파일의 이름을 변경합니다.

OS 식별자와 Yandex를 조작하는 기능도 있습니다. Metrica의 수색 엔진, 그러나 그들은 사용될 아니다.

원본 기사 (https://clickhouse.tech/docs/en/query_language/dicts/internal_dicts/) <! - hide ->
