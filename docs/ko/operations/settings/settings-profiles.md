---
machine_translated : true
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd
toc_priority : 61
toc_title : "\ u8A2D \ u5B9A \ u30D7 \ u30ED \ u30D5 \ u30A1 \ u30A4 \ u30EB"
---

# 설정 프로파일 {# settings-profiles}

설정 프로파일은 동일한 이름으로 그룹화 된 설정의 집합입니다.

!!! note "정보"
    ClickHouse 또한 지원합니다 [SQL 기반 워크 플로 (../ access-rights.md # access-control) 설정 프로파일을 관리한다. 권장합니다.

프로필 상관 없음 가질 수 있습니다. 프로필 상관 없음 가질 수 있습니다. 다른 사용자에게 동일한 프로파일을 지정할 수 있습니다. 가장 중요한 것은이 쓸 설정 프로필`readonly = 1` 읽기 전용 액세스를 보장합니다.

설정 프로파일은 서로 상속 할 수 있습니다. 상속을 사용하려면 하나 이상을 지정합니다`profile` 프로필에 나열된 다른 설정의 이전 설정. 있는 설정이 다른 프로파일에 정의되어있는 경우, 정의 된 최신의 설정이 사용됩니다.

프로필의 모든 설정을 적용하려면`profile` 설정.

예 :

설치`web` 프로필

```sql
SET profile = 'web'
```

설정 프로파일에서 선언 된 사용자의 config 파일입니다. 이것은 일반적입니다`users.xml`.

예 :

```xml
<! - Settings profiles ->
<profiles>
    <! - Default settings ->
    <default>
        <! - The maximum number of threads when running a single query ->
        <max_threads> 8 </ max_threads>
    </ default>

    <! - Settings for quries from the user interface ->
    <web>
        <max_rows_to_read> 1000000000 </ max_rows_to_read>
        <max_bytes_to_read> 100000000000 </ max_bytes_to_read>

        <max_rows_to_group_by> 1000000 </ max_rows_to_group_by>
        <group_by_overflow_mode> any </ group_by_overflow_mode>

        <max_rows_to_sort> 1000000 </ max_rows_to_sort>
        <max_bytes_to_sort> 1000000000 </ max_bytes_to_sort>

        <max_result_rows> 100000 </ max_result_rows>
        <max_result_bytes> 100000000 </ max_result_bytes>
        <result_overflow_mode> break </ result_overflow_mode>

        <max_execution_time> 600 </ max_execution_time>
        <min_execution_speed> 1000000 </ min_execution_speed>
        <timeout_before_checking_execution_speed> 15 </ timeout_before_checking_execution_speed>

        <max_columns_to_read> 25 </ max_columns_to_read>
        <max_temporary_columns> 100 </ max_temporary_columns>
        <max_temporary_non_const_columns> 50 </ max_temporary_non_const_columns>

        <max_subquery_depth> 2 </ max_subquery_depth>
        <max_pipeline_depth> 25 </ max_pipeline_depth>
        <max_ast_depth> 50 </ max_ast_depth>
        <max_ast_elements> 100 </ max_ast_elements>

        <readonly> 1 </ readonly>
    </ web>
</ profiles>
```

이 예에서는`default`와`web`.

그`default` 프로필에 특별한 목적이 있습니다. 즉,`default` 옵션의 설정 기본값을 설정합니다.

그`web` 프로필은 보통의 프로필입니다. `SET` 쿼리 또는 HTTP 쿼리 URL 매개 변수를 사용한다.

원본 기사 (https://clickhouse.tech/docs/en/operations/settings/settings_profiles/) <! - hide ->
