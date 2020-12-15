---
machine_translated : true
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd
---

# 설정 {#settings}

## distributed_product_mode {# distributed-product-mode}

동작을 변경합니다 분산 하위 (../../ sql-reference / operators / in.md).

ClickHouse applies this setting when the query contains the product of distributed tables, ie when the query for a distributed table contains a non-GLOBAL subquery for the distributed table.

제한 :

- IN 및 JOIN 하위 쿼리에만 적용됩니다.
- FROM 절에 여러 샤드를 포함한 분산 테이블을 사용하는 경우에만.
- 하위 쿼리가 여러 샤드를 포함한 분산 테이블에 관련된 경우.
- 테이블 값은 사용되지 않습니다 [원격] (../../ sql-reference / table-functions / remote.md) 기능.

가능한 값 :

-`deny` - Default value. Prohibits using these types of subqueries (returns the "Double-distributed in / JOIN subqueries is denied"예외).
-`local` - Replaces the database and table in the subquery with local ones for the destination server (shard), leaving the normal`IN` /`JOIN.`
-`global` - Replaces the`IN` /`JOIN` 쿼리`GLOBAL IN` /`GLOBAL JOIN.`
-`allow` - Allows the use of these types of subqueries.

## enable_optimize_predicate_expression {# enable-optimize-predicate-expression}

술어 푸시 다운을 선택하는`SELECT` 쿼리.

술어 푸시 다운

가능한 값 :

- 0 - Disabled.
- 1 - Enabled.

기본값 : 1.

사용법

다음 쿼리를 검토합니다 :

1.`SELECT count () FROM test_table WHERE date = '2018-10-10'`
2.`SELECT count () FROM (SELECT * FROM test_table) WHERE date = '2018-10-10'`

만약`enable_optimize_predicate_expression = 1` ClickHouse이 적용되므로 이러한 쿼리의 실행 시간은 동일합니다`WHERE` 그것을 처리 할 때 하위 쿼리.

만약`enable_optimize_predicate_expression = 0` 다음 두 번째 쿼리의 실행 시간이 훨씬 길어집니다. `WHERE` 절은 하위 쿼리의 종료 후 모든 데이터에 적용됩니다.

## 대체 _to_stale_replicas_for_distributed_queries {# settings-fallback_to_stale_replicas_for_distributed_queries}

업데이트 된 데이터를 사용할 수없는 경우 쿼리를 이전 복제에 적용합니다. 볼 복제 (../../ engines / table-engines / mergetree-family / replication.md).

ClickHouse 테이블이 이전 복제에서 가장 관련성이 높은 것을 선택합니다.

실행시 사용`SELECT` 복제 된 테이블을 가리키는 분산 테이블에서.

기본적으로는 1 (유효)입니다.

## force_index_by_date {# settings-force_index_by_date}

인덱스 날짜에 사용할 수없는 경우 쿼리 실행을 비활성화합니다.

MergeTree 패밀리 테이블에서 작동합니다.

만약`force_index_by_date = 1`, ClickHouse 쿼리에 데이터 범위를 제한하는 데 사용할 수있는 날짜 키 조건이 있는지 여부를 확인합니다. 적절한 조건이없는 경우는 예외를 throw합니다. 그러나 조건에서 읽을 데이터 양이 감소 여부는 확인하지 않습니다. 예를 들어, 다음의 조건`Date! = '2000-01-01'`테이블의 모든 데이터와 일치하는 경우에도 허용됩니다 (즉, 쿼리를 실행하려면 전체 검사가 필요합니다.) MergeTree 테이블의 데이터 범위에 대한 자세한 내용은 다음을 참조하십시오 [메루게쯔리 (../../ engines / table-engines / mergetree-family / mergetree.md).

## force_primary_key {# force-primary-key}

기본 키에 의해 색인이 불가능한 경우 쿼리 실행을 비활성화합니다.

MergeTree 패밀리 테이블에서 작동합니다.

만약`force_primary_key = 1`, ClickHouse 쿼리에 데이터 범위를 제한하는 데 사용할 수있는 기본 키 조건이 있는지 여부를 확인합니다. 적절한 조건이없는 경우는 예외를 throw합니다. 그러나 조건에서 읽을 데이터 양이 감소 여부는 확인하지 않습니다. MergeTree 테이블의 데이터 범위에 대한 자세한 내용은 다음을 참조하십시오 [메루게쯔리 (../../ engines / table-engines / mergetree-family / mergetree.md).

## format_schema {# format-schema}

이 매개 변수는 다음과 같은 스키마 정의를 필요로하는 형식을 사용하는 경우 유용합니다 [Cap'N Proto (https://capnproto.org/) 또는 뿌로토부후 (https : // developers .google.com / protocol-buffers /) 값은 형식에 따라 달라집니다.

## fsync_metadata {# fsync-metadata}

유효 또는 무효로합니다 [fsync (http://pubs.opengroup.org/onlinepubs/9699919799/functions/fsync.html) 쓸 때`.sql` 파일 기본적으로 활성화되어 있습니다.

것은 없어야한다 무효로 할 수도 있고, 서버는 수백만의 작은 테이블들이 속속 생겨나 고 파괴되었습니다.

## enable_http_compression {# settings-enable_http_compression}

HTTP 요청에 대한 응답 데이터 압축을 활성화하거나 비활성화합니다.

자세한 내용은 HTTP (../../ interfaces / http.md).

가능한 값 :

- 0 - Disabled.
- 1 - Enabled.

기본값은 0입니다.

## http_zlib_compression_level {# settings-http_zlib_compression_level}

HTTP 요청에 대한 응답 데이터 압축 수준을 다음의 경우에 설정합니다 [enable_http_compression = 1] (# settings-enable_http_compression).

가능한 값 : 1에서 9까지의 숫자입니다.

기본값은 3입니다.

## http_native_compression_disable_checksumming_on_decompress {# settings-http_native_compression_disable_checksumming_on_decompress}

클라이언트 ClickHouse 기본 압축 포맷에만 사용됩니다 (`gzip` 또는`deflate`).

자세한 내용은 HTTP (../../ interfaces / http.md).

가능한 값 :

- 0 - Disabled.
- 1 - Enabled.

기본값은 0입니다.

## send_progress_in_http_headers {# settings-send_progress_in_http_headers}

활성화 또는 비활성화합니다`X-ClickHouse-Progress` HTTP 응답 헤더`clickhouse-server` 응답.

자세한 내용은 HTTP (../../ interfaces / http.md).

가능한 값 :

- 0 - Disabled.
- 1 - Enabled.

기본값은 0입니다.

## max_http_get_redirects {# setting-max_http_get_redirects}

HTTP GET 리디렉션 호프의 최대 수를 제한합니다. [URL] (../../ engines / table-engines / special / url.md) - 엔진 테이블. 이 설정은 두 가지 유형의 테이블에 적용됩니다. [CREATE TABLE (../../ sql-reference / statements / create.md # create-table-query) 쿼리에 의해 [url (../../ sql-reference / table-functions / url. md) 테이블 함수.

가능한 값 :

- 임의의 양의 정수 홉.
- 0 - No hops allowed.

기본값은 0입니다.

## input_format_allow_errors_num {# settings-input_format_allow_errors_num}

텍스트 (CSV, TSV 등)에서 읽을 때 허용되는 오류의 최대 수를 설정합니다. ).

기본값은 0입니다.

항상 쌍으로합니다`input_format_allow_errors_ratio`.

행을 읽는 동안 오류가 발생했지만 오류 카운터가 그것보다 작은 경우`input_format_allow_errors_num`, ClickHouse 행을 무시하고 다음 행으로 이동합니다.

두 경우`input_format_allow_errors_num`와`input_format_allow_errors_ratio`를 초과하면 ClickHouse는 예외를 throw합니다.

## input_format_allow_errors_ratio {# settings-input_format_allow_errors_ratio}

텍스트 (CSV, TSV 등)에서 읽을 때 허용되는 최대 오류 비율을 설정합니다. ).
오류의 비율은 0에서 1 사이의 부동 소수점 숫자로 설정됩니다.

기본값은 0입니다.

항상 쌍으로합니다`input_format_allow_errors_num`.

행을 읽는 동안 오류가 발생했지만 오류 카운터가 그것보다 작은 경우`input_format_allow_errors_ratio`, ClickHouse 행을 무시하고 다음 행으로 이동합니다.

두 경우`input_format_allow_errors_num`와`input_format_allow_errors_ratio`를 초과하면 ClickHouse는 예외를 throw합니다.

## input_format_values_interpret_expressions {# settings-input_format_values_interpret_expressions}

를 활성화 또는 비활성화 해의 SQL 파서의 경우 고속 스트림 파서로 구문 분석 데이터입니다. 이 설정은 값 (../../ interfaces / formats.md # data-format-values) 데이터 삽입시의 형식. 구문 분석에 대한 자세한 내용은 다음을 참조하십시오 [구문] (../../ sql-reference / syntax.md) 섹션

가능한 값 :

- 0 - Disabled.

    이 경우 제공해야합니다 형식의 데이터입니다. 를 참조하십시오. [형식] (../../ interfaces / formats.md) 섹션

- 1 - Enabled.

    이 경우 SQL 식을 값으로 사용할 수있는 데이터의 삽입이 방법으로 훨씬 느립니다. 서식 된 데이터 만 삽입하면 ClickHouse는 설정 값이 0 인 것처럼 작동합니다.

기본값 : 1.

사용 예

삽입 [DateTime (../../ sql-reference / data-types / datetime.md) 다른 설정 값을 입력합니다.

```sql
SET input_format_values_interpret_expressions = 0;
INSERT INTO datetime_t VALUES (now ())
```

```text
Exception on client :
Code : 27. DB :: Exception : Can not parse input : expected) before : now ()) : (at row 1)
```

```sql
SET input_format_values_interpret_expressions = 1;
INSERT INTO datetime_t VALUES (now ())
```

```text
Ok.
```

마지막 쿼리는 다음과 동일합니다 :

```sql
SET input_format_values_interpret_expressions = 0;
INSERT INTO datetime_t SELECT now ()
```

```text
Ok.
```

## input_format_values_deduce_templates_of_expressions {# settings-input_format_values_deduce_templates_of_expressions}

SQL 표현식 템플릿 공제를 활성화하거나 비활성화합니다. 값 (../../ interfaces / formats.md # data-format-values) 형식. 이 분석과 해석 표현`Values` 연속 행식이 동일한 구조의 경우 훨씬 빠릅니다. ClickHouse는 식의 템플릿을 추론이 템플릿을 사용하여 다음 줄을 분석하고 성공적으로 분석 된 행의 배치에서 식을 계산하려고합니다.

가능한 값 :

- 0 - Disabled.
- 1 - Enabled.

기본값 : 1.

다음 쿼리의 경우 :

```sql
INSERT INTO test VALUES (lower ( 'Hello')), (lower ( 'world')), (lower ( 'INSERT')) (upper ( 'Values')) ...
```

- 만약`input_format_values_interpret_expressions = 1`과`format_values_deduce_templates_of_expressions = 0` 식은 각 행마다 개별적으로 해석됩니다 (이것은 많은 행이 매우 느립니다).
- 만약`input_format_values_interpret_expressions = 0`과`format_values_deduce_templates_of_expressions = 1` 첫째, 둘째 및 셋째 줄의 수식 템플릿을 사용하여 분석됩니다`lower (String)`그리고 함께 해석되면 forth 행 식은 다른 템플릿으로 해석됩니다 (`upper (String)`).
- 만약`input_format_values_interpret_expressions = 1`과`format_values_deduce_templates_of_expressions = 1` 이전의 경우와 동일하지만 템플릿을 추론 할 수없는 경우는 식을 개별적으로 해석 할 수 있습니다.

## input_format_values_accurate_types_of_literals {# settings-input-format-values-accurate-types-of-literals}

이 설정은 다음의 경우에만 사용됩니다`input_format_values_deduce_templates_of_expressions = 1` 일부의 컬럼 표현식은 동일한 구조를 갖지만, 다른 형태의 숫자 리터럴이 포함되어 있습니다.

```sql
(..., abs (0), ...) - UInt64 literal
(..., abs (3.141592654), ...) - Float64 literal
(..., abs (-1), ...) - Int64 literal
```

가능한 값 :

- 0 - Disabled.

    In this case, ClickHouse may use a more general type for some literals (eg`Float64` 또는`Int64` 대신`UInt64` 위해`42`가 그 원인이 오버 플로우 및 정밀도의 문제입니다.

- 1 - Enabled.

    이 경우 ClickHouse 리터럴의 실제의 형태를 확인하고 대응하는 형태의 수식 템플릿을 사용합니다. 경우에 따라서는`Values`.

기본값 : 1.

## input_format_defaults_for_omitted_fields {# session_settings-input_format_defaults_for_omitted_fields}

실행할 때`INSERT` 생략 된 입력 열의 값을 각 열의 디폴트 값으로 대체합니다. 이 옵션은 JSONEachRow (../../ interfaces / formats.md # jsoneachrow), CSV (../../ interfaces / formats.md # csv) 및 TabSeparated] (../ .. /interfaces/formats.md#tabseparated) 형식.

!!! note "주"
    이 옵션 소비량에 추가로 계산 자원을 서버를 줄일 수있다.

가능한 값 :

- 0 - Disabled.
- 1 - Enabled.

기본값 : 1.

## input_format_tsv_empty_as_default {# settings-input-format-tsv-empty-as-default}

사용하면 TSV 하늘의 입력 필드를 기본값으로 대체합니다. 복잡한 기본 식의 경우`input_format_defaults_for_omitted_fields` 활성화해야합니다.

기본적으로 사용할 수 없습니다.

## input_format_null_as_default {# settings-input-format-null-as-default}

를 활성화하거나 비활성화하고 용의 기본값이 입력 데이터를 포함한`NULL` 그러나 not 해당 열의 데이터 형식`Nullable (T)`(텍스트 입력 형식의 경우).

## input_format_skip_unknown_fields {# settings-input-format-skip-unknown-fields}

추가 데이터 건너 뛰기 삽입을 활성화하거나 비활성화합니다.

쓰기 데이터 ClickHouse가 예외를 throw했을 경우 입력 데이터를 포함 컬럼 특별한 권한이 필요하지 않습니다 사용합니다. 스킵이 활성화되면 ClickHouse 여분의 데이터를 삽입하지 않고 예외를 throw하지 않습니다.

지원 형식 :

- [JSONEachRow (../../ interfaces / formats.md # jsoneachrow)
- [CSVWithNames (../../ interfaces / formats.md # csvwithnames)
- [TabSeparatedWithNames (../../ interfaces / formats.md # tabseparatedwithnames)
- [TSKV (../../ interfaces / formats.md # tskv)

가능한 값 :

- 0 - Disabled.
- 1 - Enabled.

기본값은 0입니다.

## input_format_import_nested_json {# settings-input_format_import_nested_json}

를 활성화하거나 비활성화하고 삽입 JSON 데이터를 중첩 된 개체입니다.

지원 형식 :

- [JSONEachRow (../../ interfaces / formats.md # jsoneachrow)

가능한 값 :

- 0 - Disabled.
- 1 - Enabled.

기본값은 0입니다.

도 참조. :

- [중첩 사용 (../../ interfaces / formats.md # jsoneachrow-nested)과`JSONEachRow` 형식.

## input_format_with_names_use_header {# settings-input-format-with-names-use-header}

데이터 삽입시의 열 순서의 확인을 활성화하거나 비활성화합니다.

삽입의 성능을 향상시키기 위해, 입력 데이터의 열 순서가 대상 테이블과 동일한 것이 확실한 경우이 확인란을 비활성화 할

지원 형식 :

- [CSVWithNames (../../ interfaces / formats.md # csvwithnames)
- [TabSeparatedWithNames (../../ interfaces / formats.md # tabseparatedwithnames)

가능한 값 :

- 0 - Disabled.
- 1 - Enabled.

기본값 : 1.

## date_time_input_format {# settings-date_time_input_format}

날짜와 시간의 텍스트 표현 파서를 선택할 수 있습니다.

이 설정은 다음의 경우에는 적용되지 않습니다 날짜 및 시간 함수 (../../ sql-reference / functions / date-time-functions.md).

가능한 값 :

-` 'best_effort'` - Enables extended parsing.

    ClickHouse 기본적를 분석 할 수 있습니다`YYYY-MM-DD HH : MM : SS` 서식과 모든 [ISO 8601] (https://en.wikipedia.org/wiki/ISO_8601) 날짜 및 시간 형식. 예를 들어,` '2018-06-08T01 : 02 : 03.000Z'`.

-` 'basic'` - Use basic parser.

    ClickHouse은 기본만을 분석 할 수 있습니다`YYYY-MM-DD HH : MM : SS` 형식. 예를 들어,` '2019-08-20 10 : 18 : 56'`.

기본값 :` 'basic'`.

도 참조. :

- [DateTime 데이터 형식입니다. (../../ sql-reference / data-types / datetime.md)
- 날짜 및 시간을 조작하기위한 함수. (../../ sql-reference / functions / date-time-functions.md)

## join_default_strictness {# settings-join_default_strictness}

기본 엄격 성을 조인 절 (../../ sql-reference / statements / select / join.md # select-join).

가능한 값 :

-`ALL` - If the right table has several matching rows, ClickHouse creates a [데카르트 (https://en.wikipedia.org/wiki/Cartesian_product) 일치하는 행. 이것은 정상입니다`JOIN` 표준 SQL에서 작동합니다.
-`ANY` - If the right table has several matching rows, only the first one found is joined. If the right table has only one matching row, the results of`ANY`와`ALL` 같습니다.
-`ASOF` - For joining sequences with an uncertain match.
-`Empty string` - If`ALL` 또는`ANY` 쿼리로 지정되어 있지 않은 경우, ClickHouse는 예외를 throw합니다.

기본값 :`ALL`.

## join_any_take_last_row {# settings-join_any_take_last_row}

결합 작업 동작을`ANY` 경직.

!!! warning "주의"
    이 설정은`JOIN`과 작업 [참여] (../../ engines / table-engines / special / join.md) 엔진 테이블.

가능한 값 :

- 0 - If the right table has more than one matching row, only the first one found is joined.
- 1 - If the right table has more than one matching row, only the last one found is joined.

기본값은 0입니다.

도 참조. :

- [JOIN 절 (../../ sql-reference / statements / select / join.md # select-join)
- 조인 테이블 엔진 (../../ engines / table-engines / special / join.md)
- [join_default_strictness (# settings-join_default_strictness)

## join_use_nulls {#join_use_nulls}

유형을 설정합니다. [JOIN] (../../ sql-reference / statements / select / join.md) 행동. 때 융합 테이블 빈 세포가 나타날 수 있습니다. ClickHouse이 설정에 따라 다른 채 웁니다.

가능한 값 :

- 0 - The empty cells are filled with the default value of the corresponding field type.
- 1 -`JOIN` 표준 SQL과 같은 방식으로 작동합니다. 해당 필드의 형식은 다음과 같이 변환됩니다 [Null 가능 (../../ sql-reference / data-types / nullable.md # data_type-nullable) 빈 셀은 [NULL] (.. /../sql-reference/syntax.md).

기본값은 0입니다.

## max_block_size {# setting-max_block_size}

ClickHouse에서는 데이터 블록 (열 부분 집합)에 의해 처리됩니다. 단일 블록의 내부 처리주기는 충분히 효율적이지만 각 블록에는 현저한 지출 있습니다. 그`max_block_size` 설정은 테이블에서로드 블록의 크기 (행수)의 권장 사항입니다. 블록 크기는 너무 작아서 각 블록의 지출은 아직 눈에 있지만 첫 번째 블록 이후에 완료된 LIMIT 쿼리를 빠르게 처리 할 수 ​​있도록 목표는 여러 스레드에서 많은 열을 추출 할 때 메모리를 소비하는 것을 피하고 적어도 일부 캐시 지역성을 유지하는 것입니다.

기본값은 65,536입니다.

블록의 크기`max_block_size` 있지 않기 때문에로드됩니다. 경우 것은 분명하다하지 않고 데이터를 검색하고 심지어 작은 블록을 처리합니다.

## preferred_block_size_bytes {# preferred-block-size-bytes}

같은 목적을 위해 사용되는`max_block_size` 그러나 블록 내의 행수에 적응하여 권장되는 블록 크기를 바이트 단위로 설정합니다.
그러나 블록 크기는 다음과 같습니다`max_block_size` 행.
기본적으로 1,000,000입니다. MergeTree 엔진에서 읽을 때만 작동합니다.

## merge_tree_min_rows_for_concurrent_read {# setting-merge-tree-min-rows-for-concurrent-read}

파일에서로드되는 행수가 메루게쯔리 (../../ engines / table-engines / mergetree-family / mergetree.md) 테이블 초과`merge_tree_min_rows_for_concurrent_read` 그 ClickHouse하려는 행의 겸직 상황에서 판독이 파일에 여러 개의 스레드).

가능한 값 :

- 임의의 양의 정수.

기본값은 163840입니다.

## merge_tree_min_bytes_for_concurrent_read {# setting-merge-tree-min-bytes-for-concurrent-read}

파일에서 읽을 바이트 수가 메루게쯔리 (../../ engines / table-engines / mergetree-family / mergetree.md) - 엔진 표 초과`merge_tree_min_bytes_for_concurrent_read` 그럼 ClickHouse는이 파일에서 여러 스레드에서 동시에 읽으려고합니다.

가능한 값 :

- 임의의 양의 정수.

기본값은 251658240입니다.

## merge_tree_min_rows_for_seek {# setting-merge-tree-min-rows-for-seek}

하나의 파일에로드되는 두 데이터 블록 사이의 거리가`merge_tree_min_rows_for_seek` 이후 ClickHouse 파일을 검색하지 않고 데이터를 순차적으로 읽습니다.

가능한 값 :

- 임의의 양의 정수.

기본값은 0입니다.

## merge_tree_min_bytes_for_seek {# setting-merge-tree-min-bytes-for-seek}

하나의 파일에로드되는 두 데이터 블록 사이의 거리가`merge_tree_min_bytes_for_seek` 이후 ClickHouse 두 블록을 포함하는 파일의 범위를 순차적으로 읽어 들이기위한 추가 탐색을 피합니다.

가능한 값 :

- 임의의 양의 정수.

기본값은 0입니다.

## merge_tree_coarse_index_granularity {# setting-merge-tree-coarse-index-granularity}

하는 경우 데이터 ClickHouse 검사 데이터 파일입니다. 필요한 키가있는 범위에있는 것을 ClickHouse가 감지하면이 범위를 다음과 같이 나눕니다`merge_tree_coarse_index_granularity` 필요한 키를 재귀 적으로 검색합니다.

가능한 값 :

- 양의 짝수의 정수.

기본값 : 8.

## merge_tree_max_rows_to_use_cache {# setting-merge-tree-max-rows-to-use-cache}

ClickHouse이 더 읽어야한다면`merge_tree_max_rows_to_use_cache`있는 쿼리는 비 압축 블록 캐시를 사용하지 않습니다.

캐시 된 압축 해제 된 블록의 점포 데이터를 추출 써서 있습니다. ClickHouse이 캐시의 고속화 대응 소의 반복합니다. 이 설정은 대량의 데이터를 읽을 쿼리에서 캐시가 손상되는 것을 방지합니다. 그 [uncompressed_cache_size] (../ server-configuration-parameters / settings.md # server-settings-uncompressed_cache_size) 서버 설정은 비 압축 블록의 캐시 크기를 정의합니다.

가능한 값 :

- 임의의 양의 정수.

Default value : 128 ✕ 8192.

## merge_tree_max_bytes_to_use_cache {# setting-merge-tree-max-bytes-to-use-cache}

ClickHouse이 더 읽어야한다면`merge_tree_max_bytes_to_use_cache` 하나의 쿼리는 비 압축 블록 캐시를 사용하지 않습니다.

캐시 된 압축 해제 된 블록의 점포 데이터를 추출 써서 있습니다. ClickHouse이 캐시의 고속화 대응 소의 반복합니다. 이 설정은 대량의 데이터를 읽을 쿼리에서 캐시가 손상되는 것을 방지합니다. 그 [uncompressed_cache_size] (../ server-configuration-parameters / settings.md # server-settings-uncompressed_cache_size) 서버 설정은 비 압축 블록의 캐시 크기를 정의합니다.

가능한 값 :

- 임의의 양의 정수.

기본값 : 2013265920.

## min_bytes_to_use_direct_io {# settings-min-bytes-to-use-direct-io}

스토리지 디스크에 직접 I / O 액세스를 사용하는 데 필요한 최소 데이터 양.

ClickHouse이 설정에서 데이터를 가져 오면 있습니다. 읽을 모든 데이터의 총 저장 용량이 초과 한 경우`min_bytes_to_use_direct_io` 이후 ClickHouse 스토리지 디스크에서 데이터를 읽습니다. `O_DIRECT` 옵션

가능한 값 :

- 0 - Direct I / O is disabled.
- 양의 정수.

기본값은 0입니다.

## log_queries {# settings-log-queries}

쿼리 로그 설정.

이 설정 ClickHouse에 전송 된 쿼리는 [query_log] (../ server-configuration-parameters / settings.md # server_configuration_parameters-query-log) 서버 구성 매개 변수.

예 :

```text
log_queries = 1
```

## log_queries_min_type {# settings-log-queries-min-type}

`query_log` 기록 최소 타입.

가능한 값 :
-`QUERY_START` (`= 1`)
-`QUERY_FINISH` (`= 2`)
-`EXCEPTION_BEFORE_START` (`= 3`)
-`EXCEPTION_WHILE_PROCESSING` (`= 4`)

기본값 :`QUERY_START`.

어떤 엔티 리 가는지를 제한하는 데 사용할 수 있습니다`query_log`거야 흥미로운 만 오차를 이용할 수`EXCEPTION_WHILE_PROCESSING` :

```text
log_queries_min_type = 'EXCEPTION_WHILE_PROCESSING'
```

## log_query_threads {# settings-log-query-threads}

쿼리 스레드 로그 설정.

이 설정 ClickHouse 의해 실행 된 쿼리 스레드는 [query_thread_log] (../ server-configuration-parameters / settings.md # server_configuration_parameters-query-thread-log) 서버 구성 매개 변수.

예 :

```text
log_query_threads = 1
```

## max_insert_block_size {# settings-max_insert_block_size}

테이블에 삽입하기 위해 형성하는 블록의 크기.
이 설정은 서버가 블록을 형성하는 경우에만 적용됩니다.
예를 들어 HTTP 인터페이스를 통해 삽입의 경우, 서버는 데이터 형식을 분석하고 지정된 크기의 블록을 형성합니다.
그러나 clickhouse-client를 사용하면 클라이언트는 데이터 자체를 해석하고 'max_insert_block_size'서버에서의 설정은 삽입 된 블록의 크기에 영향을주지 않습니다.
데이터는 SELECT 후에 형성되는 것과 동일한 블록을 사용하여 삽입되기 때문에 INSERT SELECT를 사용하는 경우에도이 설정은 목적이 아닙니다.

기본값은 1,048,576입니다.

초기 값은`max_block_size`. 그 이유는 특정 테이블 엔진 때문입니다 (`* MergeTree`) 삽입 된 블록마다 디스크에 데이터 부분을 형성합니다. 마찬가지로`* MergeTree` 테이블 데이터를 정렬 할 때 삽입 할거야 충분한 크기의 블록 크기를 선별 데이터에 응용 프로그램입니다.

## min_insert_block_size_rows {# min-insert-block-size-rows}

테이블에 삽입 할 블록의 최소 행수를 설정합니다. `INSERT` 쿼리. 작은 크기의 블록을 없애 들어갑니다.

가능한 값 :

- 양의 정수.
- 0 - Squashing disabled.

기본값은 1048576입니다.

## min_insert_block_size_bytes {# min-insert-block-size-bytes}

테이블에 삽입 할 블록의 최소 바이트 수를 설정합니다. `INSERT` 쿼리. 작은 크기의 블록을 없애 들어갑니다.

가능한 값 :

- 양의 정수.
- 0 - Squashing disabled.

기본값 : 268435456.

## max_replica_delay_for_distributed_queries {# settings-max_replica_delay_for_distributed_queries}

분산 쿼리 지연 복제를 비활성화합니다. 볼 복제 (../../ engines / table-engines / mergetree-family / replication.md).

시간을 초 단위로 설정합니다. 복제가 설정 값보다 늦은 경우이 복제는 사용되지 않습니다.

기본값은 300입니다.

실행시 사용`SELECT` 복제 된 테이블을 가리키는 분산 테이블에서.

## max_threads {# settings-max_threads}

원격 서버에서 데이터를 검색하기위한 스레드를 제외한 쿼리 처리 스레드의 최대 수 'max_distributed_connections'변수).

이 매개 변수에 적용되는 스레드는 그 스레드가 동일한 단계에서 쿼리 처리 파이프 라인.
예를 들어, 테이블에서로드 할 때 함수에서 식을 계산할 수있는 경우는 적어도를 사용하여 WHERE 및 GROUP BY의 사전 집계를 병렬로 사용하여 필 'max_threads'그 스레드의 수 'max_threads' 사용됩니다.

기본값 : 물리적 CPU 코어의 수입니다.

서버에서 동시에 실행되는 SELECT 쿼리가 정상보다 적은 경우에는이 매개 변수를 실제 프로세서 코어 수보다 약간 작은 값으로 설정합니다.

제한으로 인해 신속하게 완료 쿼리의 경우 낮은 값을 설정할 수 있습니다 'max_threads'예 필요한 수의 항목이 모든 블록에 위치하고 max_threads = 8의 경우 8 개의 블록이 검색됩니다 .

작을수록`max_threads` 값은 더 적은 메모리가 소비됩니다.

## max_insert_threads {# settings-max-insert-threads}

실행하는 스레드의 최대 수`INSERT SELECT` 쿼리.

가능한 값 :

- 0 (or 1) -`INSERT SELECT` 병렬 실행되지 않습니다.
- 양의 정수. 1보다 크다.

기본값은 0입니다.

평행`INSERT SELECT` 만 효과가 있습니다. `SELECT` 부품은 병렬로 실행됩니다. [max_threads (# settings-max_threads) 설정.
값을 크게하면 메모리 사용량이 증가합니다.

## max_compress_block_size {# max-compress-block-size}

테이블에 쓸 수있는 압축 이전의 비 압축 데이터 블록의 최대 크기. 기본적으로 1,048,576 (1MiB)입니다. 크기를 작게하면 압축률이 크게 저하 캐시 지역성을 위해 압축 및 압축 해제 속도가 약간 증가 된 메모리 소비가 감소합니다. 일반적으로이 설정을 변경할 이유가 없습니다.

압축 용 블록 (바이트로 구성되는 메모리 덩어리)와 쿼리 처리를위한 블록 (테이블에서 행 세트)을 혼동하지 마십시오.

## min_compress_block_size {# min-compress-block-size}

위해 [메루게쯔리 (../../ engines / table-engines / mergetree-family / mergetree.md) "테이블. 감소를위한 지연 처리 쿼리 블록의 압축을 쓸 때 다음의 마크가 그 크기는 적어도 'min_compress_block_size'기본적으로는 65,536입니다.

압축되지 않은 데이터가 다음의 경우 블록의 실제 크기 'max_compress_block_size'는이 값 이상이며, 하나의 마크 데이터 량 이상이다.

예를 살펴 보자. 가정 'index_granularity'테이블 만들 때 8192로 설정되었습니다.

UInt32 형의 열 (값 당 4 바이트)를 쓰고 있습니다. 8192 행을 쓰면 총 32KB의 데이터가됩니다. Min_compress_block_size = 65,536 때문에 압축 된 블록은 모든 마크에 대해 형성됩니다.

문자열 타입 (값 당 60 바이트의 평균 크기)의 URL 열을 만들고 있습니다. 8192 행을 쓰면 평균은 500KB의 데이터보다 약간 작습니다. 이것은 65,536 이상이기 때문에 각 마크에 압축 된 블록이 형성됩니다. 이 경우 하나의 표시 범위에서 디스크에서 데이터를 읽을 때 불필요한 데이터는 압축되지 않습니다.

일반적으로이 설정을 변경할 이유가 없습니다.

## max_query_size {# settings-max_query_size}

SQL 파서를 사용하여 분석하기 위해 RAM에 적용 할 수있는 최대 쿼리 부분.
INSERT 쿼리는 다른 스트림 파서 (O (1) RAM을 소비한다)에 의해 처리되는 INSERT 데이터도 포함되어 있지만이 제한에 포함되지 않습니다.

기본값 : 256KiB.

## interactive_delay {# interactive-delay}

구간 마이크로 초 단위로 확인하기위한 요청 실행 중지가 전송합니다.

기본값 : 100,000 (취소를 확인하고 진행 상황을 초 단위로 전송합니다).

## connect_timeout, receive_timeout, send_timeout {# connect-timeout-receive-timeout-send-timeout}

클라이언트와의 통신에 사용되는 소켓의 초 단위의 시간.

기본값 : 10,300,300.

## cancel_http_readonly_queries_on_client_close {# cancel-http-readonly-queries-on-client-close}

Cancels HTTP read-only queries (eg SELECT) when a client closes the connection without waiting for the response.

기본값 : 0

## poll_interval {# poll-interval}

지정된 시간 (초) 대기 루프를 잠급니다.

기본값은 10입니다.

## max_distributed_connections {# max-distributed-connections}

단일 분산 테이블에 대한 단일 쿼리의 분산 처리를위한 원격 서버의 최대 동시 연결 수. 클러스터의 서버 수 이상의 값을 설정하는 것이 좋습니다.

기본값 : 1024.

다음 매개 변수는 분산 테이블을 만들 때 (및 서버를 시작할 때)에만 사용되기 때문에 런타임에 변경할 이유가 없습니다.

## distributed_connections_pool_size {# distributed-connections-pool-size}

단일 분산 테이블에 대한 모든 쿼리의 분산 처리를위한 원격 서버의 최대 동시 연결 수. 클러스터의 서버 수 이상의 값을 설정하는 것이 좋습니다.

기본값 : 1024.

## connect_timeout_with_failover_ms {# connect-timeout-with-failover-ms}

분산 테이블 엔진의 원격 서버에 연결하는 제한 시간 (밀리 초). 'shard'와 'replica'섹션은 클러스터 정의에서 사용됩니다.
실패하면 다양한 복제 연결이 시도됩니다.

기본값은 50입니다.

## connections_with_failover_max_tries {# connections-with-failover-max-tries}

분산 테이블 엔진의 각 복제본과의 연결 시도의 최대 수.

기본값은 3입니다.

## 극단적 인 {#extremes}

극단 값 (쿼리 결과의 열의 최소값과 최대 값)을 계산 여부. 0 또는 1을 수락합니다. 기본적으로 0 (해제)입니다.
자세한 내용은 "Extreme values".

## use_uncompressed_cache {# setting-use_uncompressed_cache}

비 압축 블록 캐시를 사용할지 여부. 0 또는 1을 수락합니다. 기본적으로 0 (해제)입니다.
비 압축 캐시 (MergeTree 패밀리 테이블 만)을 사용하면 다수의 짧은 쿼리를 처리 할 때 대기 시간을 대폭 줄이고 처리량을 향상시켜이 설정을 사용자에게 보낼 자주 짧은 있습니다. 또한주의를 기울이십시오 [uncompressed_cache_size] (../ server-configuration-parameters / settings.md # server-settings-uncompressed_cache_size) configuration parameter (only set in the config file) - the size of uncompressed cache blocks. By default , it is 8 GiB. the uncompressed cache is filled in as needed and the least-used data is automatically deleted.

적어도 약간 큰 양의 데이터 (백만 행 이상)를 읽을 쿼리의 경우, 압축되지 않은 캐시는 자동으로 비활성화 정말 작은 쿼리 이것은 유지할 수있는 것을 의미한다 'use_uncompressed_cache'설정 항상 1로 설정합니다.

## replace_running_query {# replace-running-query}

HTTP 인터페이스를 사용하는 경우 'query_id'변수는 전달할 수 있습니다. 이것은 쿼리 식별자로서 기능하는 임의의 문자열입니다.
같은 사용자의 쿼리가 동일한 경우 'query_id'이 시점에서 이미 존재하고 있기 때문에, 동작은 'replace_running_query'매개 변수.

`0` (default) - Throw an exception (do not allow the query to run if a query with the same 'query_id'이미 실행되고 있습니다).

`1` - Cancel the old query and start running the new one.

Yandex.Metrica이 파라미터 세트가 1의 실시를위한 제안에 대한 분할입니다. 다음 문자를 입력 한 후 이전 쿼리가 아직 완료되지 않은 경우 취소해야합니다.

## stream_flush_interval_ms {# stream-flush-interval-ms}

작품의 테이블 스트리밍의 경우 초과 된 경우 또는 스레드를 생성합니다 [max_insert_block_size (# settings-max_insert_block_size) 행.

기본값은 7500입니다.

값이 작을수록 데이터가 테이블에 플래시되는 빈도가 높아집니다. 값을 작게 설정하면 성능이 저하됩니다.

## load_balancing {# settings-load_balancing}

분산 쿼리 처리에 사용되는 복제 선택 알고리즘을 지정합니다.

ClickHouse 대응하여 다음과 같은 알고리즘의 선택의 복제 :

- [랜덤] (# load_balancing-random) (기본적으로)
- 가장 가까운 호스트 이름 (# load_balancing-nearest_hostname)
- [차례로] (# load_balancing-in_order)
- 첫 번째 또는 랜덤 (# load_balancing-first_or_random)

### 랜덤 (기본) {# load_balancing-random}

```sql
load_balancing = random
```

오류 수는 복제본에 대해 계산됩니다. 쿼리는 오류가 가장 적은 복제본으로 전송됩니다.
복제 데이터가 다를 경우 다른 데이터도 검색됩니다.

### 가장 가까운 호스트 이름 {# load_balancing-nearest_hostname}

```sql
load_balancing = nearest_hostname
```

The number of errors is counted for each replica. Every 5 minutes, the number of errors is integrally divided by 2. Thus, the number of errors is calculated for a recent time with exponential smoothing. If there is one replica with a minimal number of errors (ie errors occurred recently on the other replicas), the query is sent to it. If there are multiple replicas with the same minimal number of errors, the query is sent to the replica with a hostname that is most similar to the server 's hostname in the config file (for the number of different characters in identical positions, up to the minimum length of both hostnames).

예를 들어, example01-01-1과 example01-01-2.yandex.ru 한 장소에서 다르지만, example01-01-1과 example01-02-2는 두 장소에서 다르다.
이 방법은 원시적으로 보일지도 모르지만, 네트워크 토폴로지에 대한 외부 데이터를 필요로하지 않고, Ip 주소를 비교할 수 없습니다.

따라서 동일한 복제본이있는 경우는 이름에서 가장 가까운 복제가 우선됩니다.
또한 동일한 서버에 쿼리를 보낼 때 오류가없는 경우 분산 쿼리도 동일한 서버로 이동한다고 가정 할 수 있습니다. 따라서 복제에 다른 데이터가 배치 되어도 쿼리 거의 동일한 결과를 반환합니다.

### 차례로 {# load_balancing-in_order}

```sql
load_balancing = in_order
```

같은 번호의 오류를 가진 복제 구성에서 지정된 순서와 같은 순서로 액세스됩니다.
이 방법은 적절한 복제를 정확하게 파악하고있는 경우에 적합합니다.

### 최초 또는 랜덤 {# load_balancing-first_or_random}

```sql
load_balancing = first_or_random
```

이 알고리즘은 세트의 첫 번째 복제를 선택합니다. 크로스 복제 토폴로지의 설정은 유효하지만 다른 구성에 도움이되지 않습니다.

그`first_or_random` 알고리즘은 문제를 해결합니다`in_order` 알고리즘과`in_order`있는 복제가 다운되면 나머지 복제는 일반적으로 트래픽을 처리하지만 다음의 복제는 이중 부하를 받아 있습니다. 를 사용하는 경우`first_or_random` 알고리즘은 부하는 아직 사용 가능한 복제본간에 균등하게 분산됩니다.

## prefer_localhost_replica {# settings-prefer-localhost-replica}

를 활성화 / 비활성화가 바람직 localhost 복제 처리시에 분포합니다.

가능한 값 :

- 1 - ClickHouse always sends a query to the localhost replica if it exists.
- 0 - ClickHouse uses the balancing strategy specified by the [load_balancing (# settings-load_balancing) 설정.

기본값 : 1.

!!! warning "경고"
    사용하는 경우이 설정을 해제합니다 [max_parallel_replicas (# settings-max_parallel_replicas).

## totals_mode {# totals-mode}

있을 때 합계 계산 방법 및 max_rows_to_group_by 및 group_by_overflow_mode = 'any'존재한다.
섹션을 참조 "WITH TOTALS modifier"

## totals_auto_threshold {# totals-auto-threshold}

임계 값`totals_mode = 'auto'`.
섹션을 참조 "WITH TOTALS modifier"

## max_parallel_replicas {# settings-max_parallel_replicas}

쿼리 실행시 각 샤드 복제의 최대 수.
에 대한 일관성을 다른 부분에 동일한 데이터를 분할)이 옵션으로되어있을 때에 만 샘플링 키를 설정합니다.
복제 지연은 제어되지 않습니다.

## 컴파일 {#compile}

편집입니다. 기본적으로 0 (해제)입니다.

컴파일은 쿼리 처리 파이프 라인의 일부에만 사용됩니다.
이 부분의 파이프 라인에 대한 쿼리를 실행하는 응용하여 배포 짧은 사이클 inlining 집계 기능. 여러 단순 집계 함수를 사용하는 쿼리는 최대의 성능 향상을 볼 수 있습니다. 일반적으로 성능은 경미합니다. 매우 드문 예 늦게 쿼리를 실행합니다.

## min_count_to_compile {# min-count-to-compile}

거나 사람을 잠재적으로 이용 코챤쿠의 코드를 실행하기 전에 작성한다. 기본적으로 3.
For testing, the value can be set to 0 : compilation runs synchronously and the query waits for the end of the compilation process before continuing execution. For all other cases, use values ​​starting with 1. Compilation normally takes about 5-10 seconds .
값이 1 이상이면 컴파일은 다른 스레드에서 비동기 적으로 실행됩니다. 결과는 현재 실행중인 쿼리를 포함하여 준비가되면 즉시 사용됩니다.

컴파일 된 코드는 쿼리에서 사용되는 집계 함수와 GROUP BY 절에있는 키의 종류의 각기 다른 조합이 필요합니다.
The results of the compilation are saved in the build directory in the form of .so files. There is no restriction on the number of compilation results since they do not use very much space. Old results will be used after server restarts, except in the case of a server upgrade - in this case, the old results are deleted.

## output_format_json_quote_64bit_integers {# session_settings-output_format_json_quote_64bit_integers}

값이 true이면 json \ * Int64 및 UInt64 형식 (대부분의 JavaScript 구현과의 호환성을 위해)을 사용할 때 정수가 따옴표로 표시됩니다.

## format_csv_delimiter {# settings-format_csv_delimiter}

CSV 데이터 구분 기호로 해석되는 캐릭터. 기본적으로 구분 문자는``.

## input_format_csv_unquoted_null_literal_as_null {# settings-input_format_csv_unquoted_null_literal_as_null}

CSV 입력 형식의 경우 따옴표없이 분석을 활성화하거나 비활성화합니다`NULL` 리터럴로 (동의어`\ N`).

## output_format_csv_crlf_end_of_line {# settings-output-format-csv-crlf-end-of-line}

CSV는 UNIX 스타일 (LF) 대신 DOS / Windows 스타일의 행 구분 기호 (CRLF)을 사용합니다.

## output_format_tsv_crlf_end_of_line {# settings-output-format-tsv-crlf-end-of-line}

TSV는 UNIX 스타일 (LF) 대신 DOC / Windows 스타일의 행 구분 기호 (CRLF)을 사용합니다.

## insert_quorum {# settings-insert_quorum}

결의의 정족수를 씁니다.

- 만약`insert_quorum <2` 쿼럼 쓰기는 사용할 수 없습니다.
- 만약`insert_quorum> = 2` 쿼럼 쓰기가 활성화됩니다.

기본값은 0입니다.

정족수 쓰기

`INSERT` 승인 ClickHouse 관리를 제대로 쓰기 데이터의`insert_quorum` 사이의 복제`insert_quorum_timeout` 어떤 이유로 든 쓰기가 성공한 복제의 수가`insert_quorum`을 쓰는 것은 실패했다고 ClickHouse을 삭제하려면 삽입 한 블록에서 모든 복제 데이터가 이미 기록되어 있습니다.

쿼럼의 모든 복제본은 일관성이 있습니다. `INSERT` 쿼리. 그`INSERT` 시퀀스는 선형화됩니다.

에서 기록 된 데이터를 읽을 때`insert_quorum`을 사용할 수 있습니다 [select_sequential_consistency (# settings-select_sequential_consistency) 옵션

ClickHouse는 예외를 생성합니다

- 쿼리의 시점에서 사용 가능한 복제본의 수를`insert_quorum`.
- 이전 블록이 아직 삽입되지 않은 경우에 데이터를 쓰려고하면`insert_quorum` 복제. 이러한 상황은 사용자가`INSERT` 전의 것 앞에`insert_quorum` 완료됩니다.

도 참조. :

- [insert_quorum_timeout (# settings-insert_quorum_timeout)
- [select_sequential_consistency (# settings-select_sequential_consistency)

## insert_quorum_timeout {# settings-insert_quorum_timeout}

쓰기 수가 정원 제한 시간을 초 단위로 지정합니다. 시간이 경과 아직 쓰기가 수행되지 않은 경우 ClickHouse는 예외를 생성하고 클라이언트는 동일 또는 다른 복제에 같은 블록 쓰기

기본값은 60 초입니다.

도 참조. :

- [insert_quorum (# settings-insert_quorum)
- [select_sequential_consistency (# settings-select_sequential_consistency)

## select_sequential_consistency {# settings-select_sequential_consistency}

순차 무결성을 활성화 또는 비활성화합니다`SELECT` 쿼리 :

가능한 값 :

- 0 - Disabled.
- 1 - Enabled.

기본값은 0입니다.

사용법

순차 일관성이 활성화되어있는 경우 ClickHouse는 클라이언트가`SELECT` 이전의 모든 데이터를 포함 복제만을 조회합니다`INSERT` 다음에 실행되는 쿼리`insert_quorum` 클라이언트가 부분 복제를 참조 하는 경우 ClickHouse는 예외를 생성합니다. SELECT 쿼리는 복제 쿼럼에 아직 기록되지 않은 데이터는 포함되지 않습니다.

도 참조. :

- [insert_quorum (# settings-insert_quorum)
- [insert_quorum_timeout (# settings-insert_quorum_timeout)

## insert_deduplicate {# settings-insert-deduplicate}

중복 제거 블록을 활성화하거나 비활성화합니다. `INSERT` (복제 된 \ * 테이블의 경우).

가능한 값 :

- 0 - Disabled.
- 1 - Enabled.

기본값 : 1.

기본적으로 블록`INSERT` 문은 중복 제거됩니다 데이터 복제 (../../ engines / table-engines / mergetree-family / replication.md)).

## deduplicate_blocks_in_dependent_materialized_views {# settings-deduplicate-blocks-in-dependent-materialized-views}

를 활성화하거나 비활성화하고 중복 제거 압축을 체크를 실현 의견을받는 데이터의 중복 \ *입니다.

가능한 값 :

      0 - Disabled.
      1 - Enabled.

기본값은 0입니다.

사용법

기본적으로 중복 제거는 실현보기에서는 실행되지 않지만, 소스 테이블의 상류에서 실행됩니다.
소스 테이블의 중복 제거를 위해 삽입 된 블록이 생략 된 경우, 첨부 된 실현보기에는 삽입되지 않습니다. 이 동작은 실현보기의 집계 후 삽입 된 블록이 동일한 소스 테이블에 다른 삽입에서 파생 된 경우 고도로 집계 된 데이터
동시에이 문제 "breaks"`INSERT` 나무 등 성. 만약`INSERT` 메인 테이블에 성공했다고`INSERT` into a materialized view failed (eg because of communication failure with Zookeeper) a client will get an error and can retry the operation. However, the materialized view will not receive the second insert because it will be discarded by deduplication in the main (source) table. The setting`deduplicate_blocks_in_dependent_materialized_views`이 동작을 변경할 수 있습니다. 다시 시도하면 실현보기는 반복 삽입을 받고 중복 제거 체크를 단독으로 실행합니다,
소스 테이블의 검사 결과를 무시하고 첫 번째 실패로 인해 손실 된 행을 삽입합니다.

## max_network_bytes {# settings-max-network-bytes}

쿼리를 실행할 때 네트워크를 통해 송수신되는 데이터의 양 (바이트 단위)를 제한합니다. 이 설정은 개별 쿼리에 대해 적용됩니다.

가능한 값 :

- 양의 정수.
- 0 - Data volume control is disabled.

기본값은 0입니다.

## max_network_bandwidth {# settings-max-network-bandwidth}

네트워크에서의 데이터 교환 속도를 초당 바이트 단위로 제한합니다. 이 설정은 모든 조회에 적용됩니다.

가능한 값 :

- 양의 정수.
- 0 - Bandwidth control is disabled.

기본값은 0입니다.

## max_network_bandwidth_for_user {# settings-max-network-bandwidth-for-user}

네트워크에서의 데이터 교환 속도를 초당 바이트 단위로 제한합니다. 이 설정은 단일 사용자가 동시에 실행하는 모든 쿼리에 적용됩니다.

가능한 값 :

- 양의 정수.
- 0 - Control of the data speed is disabled.

기본값은 0입니다.

## max_network_bandwidth_for_all_users {# settings-max-network-bandwidth-for-all-users}

네트워크를 통해 데이터가 교환되는 속도를 초당 바이트 단위로 제한합니다. 이 설정이 적용되는 모든 동시 주행 문의합니다.

가능한 값 :

- 양의 정수.
- 0 - Control of the data speed is disabled.

기본값은 0입니다.

## count_distinct_implementation {# settings-count_distinct_implementation}

이것은`uniq *`을 실행하는 데 사용해야합니다. [COUNT (DISTINCT ...) (../../ sql-reference / aggregate-functions / reference.md # agg_function-count) 건설.

가능한 값 :

- [uniq (../../ sql-reference / aggregate-functions / reference.md # agg_function-uniq)
- [uniqCombined (../../ sql-reference / aggregate-functions / reference.md # agg_function-uniqcombined)
- [uniqCombined64 (../../ sql-reference / aggregate-functions / reference.md # agg_function-uniqcombined64)
- [uniqHLL12 (../../ sql-reference / aggregate-functions / reference.md # agg_function-uniqhll12)
- [uniqExact (../../ sql-reference / aggregate-functions / reference.md # agg_function-uniqexact)

기본값 :`uniqExact`.

## skip_unavailable_shards {# settings-skip_unavailable_shards}

를 활성화하거나 비활성화합니다 静키의 중요한 조각.

더 - 샤가있는 경우에는 이용할 수없는 그 모든 복제본을 위해 사용할 수 없습니다. 다음의 경우 복제는 사용할 수 없습니다 :

- ClickHouse은 어떤 이유로 복제에 연결할 수 없습니다.

    복제에 연결하면 ClickHouse 몇 가지 시도를 실행합니다. 모든 이들의 시도가 실패하고 복제본 수 있습니다.

- 복제가 DNS로 해결할 수 없습니다.

    복제 호스트 이름을 DNS를 통해 해결할 수없는 경우 다음과 같은 상황을 나타낼 수 있습니다 :

    - 복제 호스트에 DNS 레코드가 없다. 이것은 동적 DNS를 가진 시스템에서 발생할 수 있습니다. [Kubernetes (https://kubernetes.io) 여기서 노드는 다운 타임 동안 해결하지 못하고, 이것은 오류가 아닙니다.

    - 설정 오류. ClickHouse 설정 파일이 포함되어 잘못된 호스트 이름입니다.

가능한 값 :

- 1 - skipping enabled.

    샤드를 사용할 수없는 경우 ClickHouse는 부분적인 데이터를 기반으로 결과를 반환 노드의 가용성 문제는보고되지 않습니다.

- 0 - skipping disabled.

    샤드를 사용할 수없는 경우 ClickHouse는 예외를 throw합니다.

기본값은 0입니다.

## optimize_skip_unused_shards {# settings-optimize_skip_unused_shards}

PREWHERE / WHERE에 샤드 키 조건이있는 SELECT 쿼리의 미사용 샤드 건너 뛰기를 활성화 또는 비활성화합니다 (데이터가 샤드 키에 의해 배포되는

기본값 : 0

## force_optimize_skip_unused_shards {# settings-force_optimize_skip_unused_shards}

를 활성화하거나 비활성화하고 쿼리의 경우 [`optimize_skip_unused_shards` (# settings-optimize_skip_unused_shards) 미사용 샤드를 사용하여 건너 뛸 수 없습니다. 스킵이 불가능하며, 설정이 활성화되어있는 경우는 예외가 슬로우됩니다.

가능한 값 :

- 0- 비활성화 (포기하지)
- 1- 제 쿼리의 경우에만 표는 sharding 키
- 2-를 해제 쿼리에 관계없이 sharding 키 정의 테이블

기본값 : 0

## optimize_throw_if_noop {# setting-optimize_throw_if_noop}

예외의 발생을 유효 또는 무효로합니다. [OPTIMIZE (../../ sql-reference / statements / misc.md # misc_operations-optimize) 쿼리가 병합을 실행하지 못했습니다.

기본적으로`OPTIMIZE` 아무것도하지 않아도 정상으로 돌아갑니다. 이 설정을 사용하면 이러한 상황을 구별하고 예외 메시지에서 이유를 얻을 수 있습니다.

가능한 값 :

- 1 - Throwing an exception is enabled.
- 0 - Throwing an exception is disabled.

기본값은 0입니다.

## 유통 {# settings-distributed_replica_error_half_life}

- 유형 : 초
- 기본값 : 60 초

분산 테이블의 오류를 제로로하는 속도를 제어합니다. 복제가 잠시 사용하지 못하고, 5 개의 오류가 축적되어 distributed_replica_error_half_life이 1 초로 설정되어있는 경우 복제가 마지막 오류의 3 초 후에 정상으로 간주됩니다.

도 참조. :

- 분산 테이블 엔진 (../../ engines / table-engines / special / distributed.md)
- [유통] (# settings-distributed_replica_error_cap)

## 유통 {# settings-distributed_replica_error_cap}

- 형식 : unsigned int
- 기본값 : 1000

각 복제의 오류

도 참조. :

- 분산 테이블 엔진 (../../ engines / table-engines / special / distributed.md)
- [유통] (# settings-distributed_replica_error_half_life)

## distributed_directory_monitor_sleep_time_ms {#distributed_directory_monitor_sleep_time_ms}

기본 구간 분산 (../../ engines / table-engines / special / distributed.md) 데이터를 전송하는 표 엔진. 실제 간격은 오류가 발생한 경우 기하 급수적으로 증가합니다.

가능한 값 :

- 밀리 세컨드의 양의 정수.

기본값은 100 밀리 초입니다.

## distributed_directory_monitor_max_sleep_time_ms {#distributed_directory_monitor_max_sleep_time_ms}

최대 간격 분산 (../../ engines / table-engines / special / distributed.md) 데이터를 전송하는 표 엔진. 구간의 기하 급수적 인 성장을 제한한다. [distributed_directory_monitor_sleep_time_ms (# distributed_directory_monitor_sleep_time_ms) 설정.

가능한 값 :

- 밀리 세컨드의 양의 정수.

기본값 : 30000 밀리 초 (30 초).

## distributed_directory_monitor_batch_inserts {#distributed_directory_monitor_batch_inserts}

삽입 된 데이터의 일괄 전송을 활성화하거나 비활성화합니다.

일괄 전송이 활성화되어있는 경우, 분산 (../../ engines / table-engines / special / distributed.md) 테이블 엔진을 보내 여러 파일 삽입 데이터를 이동하게되어 있습니다 대신 보냅니다. 대량의 개선에 클러스터의 성능을 더 활용하여 서버와 네트워크 자원입니다.

가능한 값 :

- 1 - Enabled.
- 0 - Disabled.

기본값은 0입니다.

## os_thread_priority {# setting-os-thread-priority}

우선 순위를 설정합니다 (니스 (https://en.wikipedia.org/wiki/Nice_ (Unix))) 쿼리를 실행하는 스레드의 경우. OS 스케줄러는 사용 가능한 각 CPU 코어에서 실행되는 다음 스레드를 선택할 때 우선 순위를 고려합니다.

!!! warning "경고"
    이 설정을 사용하려면`CAP_SYS_NICE` 능력. 그`clickhouse-server` 패키지 몇 가지 가상 환경에서는`CAP_SYS_NICE` 능력. 이 경우`clickhouse-server` 시작할 때 이에 대한 메시지를 표시합니다.

가능한 값 :

- 범위의 값을 설정할 수 있습니다`[-20, 19]`.

값이 낮을수록 우선 순위가 높습니다. 낮은 실`nice` 우선 순위 값은 높은 값의 스레드보다 더 자주 실행됩니다. 장시간 실행되는 비대화 형 쿼리에서는 짧은 대화 형 쿼리가 도착했을 때 자원을 신속하게 포기 할 수 있기 때문에 높은 값이 바람직합니다.

기본값은 0입니다.

## query_profiler_real_time_period_ns {#query_profiler_real_time_period_ns}

실제 클럭 타이머 기간을 설정합니다. 쿼리 프로파일 러 (../../ operations / optimizing-performance / sampling-query-profiler.md). 실제 클럭 타이머 카운트 벽 벽시계.

가능한 값 :

- 나노초 단위의 양의 정수입니다.

    권장 값 :

            - 10000000 (100 times a second) nanoseconds and less for single queries.
            - 1000000000 (once a second) for cluster-wide profiling.

- 타이머를 취소하는 경우는 0.

유형 : UInt64 (../../ sql-reference / data-types / int-uint.md).

기본값 : 1000000000 나노초 (초).

도 참조. :

- 시스템 테이블 [trace_log (../../ operations / system-tables.md # system_tables-trace_log)

## query_profiler_cpu_time_period_ns {#query_profiler_cpu_time_period_ns}

CPU의 클럭 타이머 기간을 설정합니다. 쿼리 프로파일 러 (../../ operations / optimizing-performance / sampling-query-profiler.md)이 타이머 카운트 전용 CPU 시간.

가능한 값 :

- 나노초의 양의 정수.

    권장 값 :

            - 10000000 (100 times a second) nanoseconds and more for single queries.
            - 1000000000 (once a second) for cluster-wide profiling.

- 타이머를 취소하는 경우는 0.

유형 : UInt64 (../../ sql-reference / data-types / int-uint.md).

기본값 : 1000000000 나노초.

도 참조. :

- 시스템 테이블 [trace_log (../../ operations / system-tables.md # system_tables-trace_log)

## allow_introspection_functions {# settings-allow_introspection_functions}

비활성화 활성화 [반성 함수 (../../ sql-reference / functions / introspection.md) 쿼리 프로파일.

가능한 값 :

- 1 - Introspection functions enabled.
- 0 - Introspection functions disabled.

기본값은 0입니다.

** 참고하십시오. **

- 샘플링 크로 파일러] (../ optimizing-performance / sampling-query-profiler.md)
- 시스템 테이블 [trace_log (../../ operations / system-tables.md # system_tables-trace_log)

## input_format_parallel_parsing {# input-format-parallel-parsing}

- 유형 : bool
- 기본값 : True

데이터 형식 순서 유지 병렬 분석을 활성화합니다. TSV, TKSV, CSV 및 JSONEachRow 형식에서만 지원됩니다.

## min_chunk_bytes_for_parallel_parsing {# min-chunk-bytes-for-parallel-parsing}

- 형식 : unsigned int
- 기본값 : 1MiB

각 스레드가 병렬로 분석하는 최소 청크 크기를 바이트 단위로 나타냅니다.

## output_format_avro_codec {# settings-output_format_avro_codec}

출력 Avro 파일에 사용하는 압축 코덱을 설정합니다.

유형 : 문자열

가능한 값 :

-`null` - No compression
-`deflate` - Compress with Deflate (zlib)
-`snappy` - Compress with [스너피 (https://google.github.io/snappy/)

기본값 :`snappy` (사용 가능한 경우) 또는`deflate`.

## output_format_avro_sync_interval {# settings-output_format_avro_sync_interval}

출력 Avro 파일 동기화 마커 사이의 최소 데이터 사이즈 (바이트 단위)을 설정합니다.

형식 : unsigned int

사용 가능한 값 : 32 (32 바이트) -1073741824 (1GiB)

기본값 : 32768 (32KiB)

## format_avro_schema_registry_url {# settings-format_avro_schema_registry_url}

사용 Confluent 스키마 레지스트리 URL을 설정합니다 [아브로 합류 (../../ interfaces / formats.md # data-format-avro-confluent) 형식

유형 : URL

기본값 : 빈

## background_pool_size {#background_pool_size}

세트의 스레드를 할 배경 사업의 테이블 엔진 (예를 들어, 합병에 [MergeTree 엔진 (../../ engines / table-engines / mergetree-family / index.md) 테이블). 이 설정은 ClickHouse 서버를 시작할 때 적용되고 사용자 세션을 변경할 수 없습니다. 이 설정을 조정하여 CPU와 디스크의 부하를 관리합니다. 작은 수영장 크기 이하의 CPU와 디스크 자원이 배경 프로세스의 사전 지연이 영향을 쿠에리す.

가능한 값 :

- 임의의 양의 정수.

기본값은 16입니다.

원본 기사 (https://clickhouse.tech/docs/en/operations/settings/settings/) <! - hide ->
