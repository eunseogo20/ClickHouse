---
machine_translated : true
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd
toc_folder_title : "\ u8A2D \ u5B9A"
toc_priority : 55
toc_title : "\ u306F \ u3058 \ u3081 \ u306B"
---

# 설정 {# session-settings-intro}

여러 있다는 것입니다 모든 설정은이 섹션에서 설명하는 문서

설정 레이어로 구성되기 때문에 이후의 각 레이어는 이전의 설정을 다시 정의합니다.

우선 순위의 순서로 설정하는 방법 :

- 설정`users.xml` 서버 구성 파일.

    요소로 설정`<profiles>`.

- 세션 설정.

    보내는`SET setting = value` 대화 모드로 ClickHouse 콘솔 클라이언트에서.
    마찬가지로, Http 프로토콜 ClickHouse 세션을 사용할 수 있습니다. 이렇게하려면 다음을 지정해야합니다`session_id` HTTP 매개 변수.

- 쿼리 설정.

    - ClickHouse console 클라이언트를 비대화 모드로 부팅 할 때 startup 매개 변수를 설정합니다`--setting = value`.
    - HTTP API를 사용하는 경우 CGI 매개 변수를 전달합니다 (`URL? setting_1 = value & setting_2 = value ...`).

서버 설정 파일에서만 할 수있는 설정은이 섹션에서 설명되지 않습니다.

원본 기사 (https://clickhouse.tech/docs/en/operations/settings/) <! - hide ->
