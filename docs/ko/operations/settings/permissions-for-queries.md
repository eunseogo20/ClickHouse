---
machine_translated : true
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd
toc_priority : 58
toc_title : "\ u30AF \ u30A8 \ u30EA \ u306E \ u6A29 \ u9650"
---

# 쿼리 권한 {#permissions_for_queries}

문의 ClickHouse 크게 여러 유형 :

1. 데이터를 읽어 들이기위한 쿼리 :`SELECT``SHOW``DESCRIBE``EXISTS`.
2. 데이터 쿼리 작성 :`INSERT``OPTIMIZE`.
3. 설정 변경 쿼리 :`SET``USE`.
4. DDL (https://en.wikipedia.org/wiki/Data_definition_language) 쿼리 :`CREATE``ALTER``RENAME``ATTACH``DETACH``DROP``TRUNCATE`.
5.`KILL QUERY`.

다음 설정은 쿼리의 종류에 따라 사용자 권한을 조정합니다 :

- [읽기 전용] (# settings_readonly) - Restricts permissions for all types of queries except DDL queries.
- [allow_ddl (# settings_allow_ddl) - Restricts permissions for DDL queries.

`KILL QUERY` 모든 설정에서 실행할 수 있습니다.

## 읽기 전용 {#settings_readonly}

제한 권한 읽기 데이터 쓰기 데이터와 설정의 변경입니다.

쿼리 형태로 분할하는 방법을 참조하십시오 [위] (# permissions_for_queries).

가능한 값 :

- 0 - All queries are allowed.
- 1 - Only read data queries are allowed.
- 2 - Read data and change settings queries are allowed.

설정 후`readonly = 1` 사용자가 변경할 수 없습니다`readonly`와`allow_ddl` 현재 세션의 설정.

를 사용하는 경우`GET` 방법 [HTTP 인터페이스 (../../ interfaces / http.md)`readonly = 1` 자동으로 설정됩니다. 데이터를 변경하려면`POST` 방법.

설정`readonly = 1` 사용자가 모든 설정을 변경하는 것을 금지합니다. 사용자를 차단하는 방법이 있습니다
에서의 변경은 특정 설정의 세부 설정 제약 조건 (constraints-on-settings.md).

기본값 : 0

## allow_ddl {#settings_allow_ddl}

허용 또는 거부 [DDL] (https://en.wikipedia.org/wiki/Data_definition_language) 쿼리.

쿼리 형태로 분할하는 방법을 참조하십시오 [위] (# permissions_for_queries).

가능한 값 :

- 0 - DDL queries are not allowed.
- 1 - DDL queries are allowed.

실행할 수없는`SET allow_ddl = 1` 만약`allow_ddl = 0` 현재 세션의 경우.

기본값 : 1

원본 기사 (https://clickhouse.tech/docs/en/operations/settings/permissions_for_queries/) <! - hide ->
