---
machine_translated : true
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd
toc_priority : 66
toc_title : "\ u305D \ u306E \ u4ED6"
---

# 기타 기능 {# other-functions}

## 호스트 이름 () {#hostname}

이 함수가 실행 된 호스트 이름을 가진 문자열을 반환합니다. 분산 처리의 경우 함수가 원격 서버에서 실행되는 경우, 이것은 원격 서버 호스트의 이름입니다.

## getMacro {#getmacro}

에서 명명 된 값을 가져옵니다 매크로 (../../ operations / server-configuration-parameters / settings.md # macros) 서버 구성 섹션.

** 구문 **

```sql
getMacro (name);
```

** 파라미터 **

-`name` - Name to retrieve from the`macros` 섹션 [문자열] (../../ sql-reference / data-types / string.md # string).

** 반환 값 **

- 지정된 매크로의 값.

유형 : 문자열 (../../ sql-reference / data-types / string.md).

** 예 **

예`macros` 서버 설정 파일의 섹션 :

```xml
<macros>
    <test> Value </ test>
</ macros>
```

쿼리 :

```sql
SELECT getMacro ( 'test');
```

결과 :

```text
┌─getMacro ( 'test') ─┐
│ Value │
└──────────────────┘
```

같은 값을 구할 다른 방법 :

```sql
SELECT * FROM system.macros
WHERE macro = 'test';
```

```text
┌─macro─┬─substitution─┐
│ test │ Value │
└───────┴──────────────┘
```

## FQDN {#fqdn}

정규화 된 도메인 이름을 반환합니다.

** 구문 **

```sql
fqdn ();
```

이 함수는 대소 문자를 구별하지 않습니다.

** 반환 값 **

- 정규화 된 도메인 이름을 가진 문자열.

유형 :`String`.

** 예 **

쿼리 :

```sql
SELECT FQDN ();
```

결과 :

```text
┌─FQDN () ──────────────────────────┐
│ clickhouse.ru-central1.internal │
└─────────────────────────────────┘
```

## 기본 이름 {#basename}

마지막 슬래시 또는 백 슬래시 뒤에 문자열의 끝 부분을 추출합니다. 이 함수는 경로에서 파일 이름을 추출하기 위해 일반적으로 사용됩니다.

```sql
basename (expr)
```

** 파라미터 **

-`expr` - Expression resulting in a [문자열] (../../ sql-reference / data-types / string.md) 유형 값. 결과 값은 모든 백 슬래시를 이스케이프해야합니다.

** 반환 값 **

다음을 포함하는 문자열 :

- 마지막 슬래시 또는 백 슬래시 뒤에 문자열의 끝 부분.

        If the input string contains a path ending with slash or backslash, for example,`/`or`c : \`the function returns an empty string.

- 슬래시 또는 백 슬래시가없는 경우 원래 문자열.

** 예 **

```sql
SELECT 'some / long / path / to / file'AS a, basename (a)
```

```text
┌─a──────────────────────┬─basename ( 'some \\ long \\ path \\ to \\ file') ─┐
│ some \ long \ path \ to \ file │ file │
└────────────────────────┴──────────────────────── ────────────────┘
```

```sql
SELECT 'some \\ long \\ path \\ to \\ file'AS a, basename (a)
```

```text
┌─a──────────────────────┬─basename ( 'some \\ long \\ path \\ to \\ file') ─┐
│ some \ long \ path \ to \ file │ file │
└────────────────────────┴──────────────────────── ────────────────┘
```

```sql
SELECT 'some-file-name'AS a, basename (a)
```

```text
┌─a──────────────┬─basename ( 'some-file-name') ─┐
│ some-file-name │ some-file-name │
└────────────────┴────────────────────────────┘
```

## visibleWidth (x) {#visiblewidthx}

텍스트 (탭으로 구분)로 콘솔에 값을 출력 할 때의 대략적인 폭을 계산합니다.
이 함수는 시스템이 Pretty 형식을 구현하는 데 사용됩니다.

`NULL`에 해당하는 문자열로 표현된다. `NULL`에서`Pretty` 포맷.

```sql
SELECT visibleWidth (NULL)
```

```text
┌─visibleWidth (NULL) ─┐
│ 4 │
└────────────────────┘
```

## 토타이뿌 이름 (x) {#totypenamex}

전달 된 인수의 형명을 포함하는 문자열을 반환합니다.

만약`NULL` 함수에 입력으로 전달되면`Nullable (Nothing)`내부에 해당하는 형식`NULL` ClickHouse에서의 표현.

## 블록 크기 () {# function-blocksize}

블록의 크기를 가져옵니다.
ClickHouse에서는 쿼리는 항상 블록 (열 부분 집합)에서 실행됩니다. 이 함수는 호출 된 블록의 크기를 얻을 수 있습니다.

## 구체화 된 u (x) {#materializex}

상수를 하나의 값만을 포함한 전체 라인으로 변환합니다.
ClickHouse는 전체 열과 상수는 메모리에서 다르게 표현됩니다. 함수는 정수 인수와 일반 인수 (다른 코드가 실행된다)는 다른 동작을하지만 결과는 거의 항상 동일합니다. 이 기능은 디버깅을위한

## ignore (...) {#ignore}

받아 인수를 포함한`NULL` 항상 0을 반환합니다.
그러나 인수는 아직 평가되고 있습니다. 이 벤치 마크에 사용할 수 있습니다.

## 슬립 (초) {#sleepseconds}

잠 'seconds'각 데이터 블록 (초). 정수 또는 부동 소수점 숫자를 지정할 수 있습니다.

## sleepEachRow (초) {#sleepeachrowseconds}

잠 'seconds'행 초. 정수 또는 부동 소수점 숫자를 지정할 수 있습니다.

## currentDatabase () {#currentdatabase}

현재 데이터베이스의 이름을 돌려줍니다.
이 함수는 데이터베이스를 지정해야 CREATE TABLE 쿼리 테이블 엔진 매개 변수로 사용할 수 있습니다.

## currentUser () {# other-function-currentuser}

현재 사용자의 로그인을 반환합니다. 사용자의 로그인, 그 시작 된 쿼리는 distibuted 쿼리의 경우에 반환됩니다.

```sql
SELECT currentUser ();
```

일명`user ()``USER ()`.

** 반환 값 **

- 현재 사용자의 로그인.
- 분할 된 쿼리의 경우 쿼리를 시작한 사용자의 로그인.

유형 :`String`.

** 예 **

쿼리 :

```sql
SELECT currentUser ();
```

결과 :

```text
┌─currentUser () ─┐
│ default │
└───────────────┘
```

## isConstant {# is-constant}

인수가 상수 표현식 여부를 확인합니다.

A constant expression means an expression whose resulting value is known at the query analysis (ie before execution). For example, expressions over [리터럴] (../ syntax.md # literals)는 상수 식입니다.

이 기능은 개발 응용 프로그램의 디버깅 및 데모

** 구문 **

```sql
isConstant (x)
```

** 파라미터 **

-`x` - Expression to check.

** 반환 값 **

-`1` -`x` 정수입니다.
-`0` -`x`는 상수가 없습니다.

유형 : UInt8] (../ data-types / int-uint.md).

** 예 **

쿼리 :

```sql
SELECT isConstant (x + 1) FROM (SELECT 43 AS x)
```

결과 :

```text
┌─isConstant (plus (x, 1)) ─┐
│ 1 │
└────────────────────────┘
```

쿼리 :

```sql
WITH 3.14 AS pi SELECT isConstant (cos (pi))
```

결과 :

```text
┌─isConstant (cos (pi)) ─┐
│ 1 │
└─────────────────────┘
```

쿼리 :

```sql
SELECT isConstant (number) FROM numbers (1)
```

결과 :

```text
┌─isConstant (number) ─┐
│ 0 │
└────────────────────┘
```

## isFinite (x) {#isfinitex}

Float32과 Float64을 받아 인수가 무한대가 아닌 NaN가 아닌 경우 UInt8을 1, 그렇지 않으면 0을 반환합니다.

## 이신 필 나이트 (x) {#isinfinitex}

Float32과 Float64을 받아 인수가 무한대의 경우는 UInt8을 1, 그렇지 않으면 0을 반환합니다. NaN이면 0이 반환된다는 점에 유의하십시오.

## ifNotFinite {#ifnotfinite}

부동 소수점 값이 유한 여부를 확인합니다.

** 구문 **

    ifNotFinite (x, y)

** 파라미터 **

-`x` - Value to be checked for infinity. Type : 플로트 \ * (../../ sql-reference / data-types / float.md).
-`y` - Fallback value : Type : 플로트 \ * (../../ sql-reference / data-types / float.md).

** 반환 값 **

-`x` 만약`x`는 유한하다.
-`y` 만약`x` 유한이 아니다.

** 예 **

쿼리 :

    SELECT 1/0 as infimum, ifNotFinite (infimum 42)

결과 :

    ┌─infimum─┬─ifNotFinite (divide (1, 0), 42) ─┐
    │ inf │ 42 │
    └─────────┴───────────────────────────────┘

같은 결과를 얻으려면 다음과 같이합니다 [삼항 연산자 (conditional-functions.md # ternary-operator) :`isFinite (x)? x : y`.

## 이스난 (x) {#isnanx}

Float32과 Float64을 받아 인수가 NaN의 경우는 UInt8가 1, 그렇지 않으면 0을 반환합니다.

## hasColumnInTable (\ 'hostname'\ 'username'\ 'password'\ '\'\ ''database ','table ','column ') {# hascolumnintablehostname-username-password-database- table-column}

데이터베이스 이름, 테이블 이름, 열 이름의 상수 문자열을 받아들입니다. 열이있는 경우는 UInt8 상수 식 1, 그렇지 않으면 0을 반환합니다. Hostname 매개 변수가 설정되어있는 경우, 검사는 원격 서버에서 실행됩니다.
테이블이 존재하지 않는 경우,이 함수는 예외를 throw합니다.
중첩 된 데이터 구조 요소의 경우, 함수는 열이 있는지 확인합니다. 중첩 된 데이터 구조 자체의 경우, 함수는 0을 반환합니다.

## 바 {# function-bar}

Unicode 아트 그림을 만들 수 있습니다.

`bar (x, min, max width)`폭에 비례 한 밴드를 그립니다`(x - min)`와 같은`width` 때 문자`x = max`.

파라미터 :

-`x` - Size to display.
-`min, max` - Integer constants. The value must fit in`Int64`.
-`width` - Constant, positive integer, can be fractional.

밴드는 기호의 여덟째 정확하게 그려져 있습니다.

예 :

```sql
SELECT
    toHour (EventTime) AS h,
    count () AS c,
    bar (c, 0, 600000, 20) AS bar
FROM test.hits
GROUP BY h
ORDER BY h ASC
```

```text
┌──h─┬──────c─┬─bar────────────────┐
│ 0 │ 292907 │ █████████▋ │
│ 1 │ 180563 │ ██████ │
│ 2 │ 114861 │ ███▋ │
│ 3 │ 85069 │ ██▋ │
│ 4 │ 68543 │ ██▎ │
│ 5 │ 78116 │ ██▌ │
│ 6 │ 113474 │ ███▋ │
│ 7 │ 170678 │ █████▋ │
│ 8 │ 278380 │ █████████▎ │
│ 9 │ 391053 │ █████████████ │
│ 10 │ 457681 │ ███████████████▎ │
│ 11 │ 493667 │ ████████████████▍ │
│ 12 │ 509641 │ ████████████████▊ │
│ 13 │ 522947 │ █████████████████▍ │
│ 14 │ 539954 │ █████████████████▊ │
│ 15 │ 528460 │ █████████████████▌ │
│ 16 │ 539201 │ █████████████████▊ │
│ 17 │ 523539 │ █████████████████▍ │
│ 18 │ 506467 │ ████████████████▊ │
│ 19 │ 520915 │ █████████████████▎ │
│ 20 │ 521665 │ █████████████████▍ │
│ 21 │ 542078 │ ██████████████████ │
│ 22 │ 493642 │ ████████████████▍ │
│ 23 │ 400397 │ █████████████▎ │
└────┴────────┴────────────────────┘
```

## 변환 {#transform}

몇 가지 요소와 다른 요소로 명시 적으로 정의 된 매핑에 따라 값을 변환합니다.
아 특유의 변형 기능 :

### transform (x, array_from, array_to 기본) {# transformx-array-from-array-to-default}

`x` - What to transform.

`array_from` - Constant array of values ​​for converting.

`array_to` - Constant array of values ​​to convert the values ​​in 'from'에.

`default` - Which value to use if 'x'값 중과도 같지 않다. 'from'.

`array_from`와`array_to` - Arrays of the same size.

유형 :

`transform (T Array (T) Array (U) U) -> U`

`T`와`U` 숫자, 문자열 또는 날짜 또는 DateTime 형식을 지정할 수 있습니다.
같은 문자가 표시된 경우 (T 또는 U) 숫자 형의 경우, 이들은 일치하는 형태가 아닌 일반적인 형태를 가지는 형태 일 가능성이 있습니다.
예를 들어, 첫 번째 인수는 Int64 형에서 두 번째 인수는 배열 (UInt16) 형입니다.

만약 'x'값은 요소 중 하나와 동일합니다. 'array_from'이것은 배열에서 기존 요소 (즉, 같은 번호가 붙어있다)을 돌려줍니다. 'array_to'배열 그렇지 않으면 'default'여러 일치하는 요소가있는 경우 'array_from'일치하지만 중 하나를 반환합니다.

예 :

```sql
SELECT
    transform (SearchEngineID, 2, 3, 'Yandex', 'Google', 'Other') AS title,
    count () AS c
FROM test.hits
WHERE SearchEngineID! = 0
GROUP BY title
ORDER BY c DESC
```

```text
┌─title─────┬──────c─┐
│ Yandex │ 498635 │
│ Google │ 229872 │
│ Other │ 104472 │
└───────────┴────────┘
```

### 변환 (x, array_from, array_to) {# transformx-array-from-array-to}

첫 번째 변형과는 달리, 'default'인수는 생략된다.
만약 'x'값은 요소 중 하나와 동일합니다. 'array_from'이것은 배열과 일치하는 요소 (즉, 같은 번호가 붙어있다)을 돌려줍니다. 'array_to'배열 그렇지 않으면 'x'.

유형 :

`transform (T Array (T) Array (T)) -> T`

예 :

```sql
SELECT
    transform (domain (Referer), 'yandex.ru', 'google.ru', 'vk.com', 'www.yandex', 'example.com']) AS s,
    count () AS c
FROM test.hits
GROUP BY domain (Referer)
ORDER BY count () DESC
LIMIT 10
```

```text
┌─s──────────────┬───────c─┐
│ │ 2906259 │
│ www.yandex │ 867767 │
│ ███████.ru │ 313599 │
│ mail.yandex.ru │ 107147 │
│ ██████.ru │ 100355 │
│ █████████.ru │ 65040 │
│ news.yandex.ru │ 64515 │
│ ██████.net │ 59141 │
│ example.com │ 57316 │
└────────────────┴─────────┘
```

## formatReadableSize (x) {#formatreadablesizex}

크기 (바이트)를 받아들입니다. 접미사 (kib, MiB 등)을 가진 반올림 사이즈를 돌려줍니다. ) 문자열로.

예 :

```sql
SELECT
    arrayJoin ([1, 1024, 1024 * 1024, 192851925) AS filesize_bytes,
    formatReadableSize (filesize_bytes) AS filesize
```

```text
┌─filesize_bytes─┬─filesize───┐
│ 1 │ 1.00 B │
│ 1024 │ 1.00 KiB │
│ 1048576 │ 1.00 MiB │
│ 192851925 │ 183.92 MiB │
└────────────────┴────────────┘
```

## 최소 (a, b) {# leasta-b}

A와 b의 최소값을 반환합니다.

## 그레이 테스트 (a, b) {# greatesta-b}

A와 b의 최대 값을 반환합니다.

## 가동 시간 () {#uptime}

서버 가동 시간을 초 단위로 반환합니다.

## 버전 () {#version}

서버 버전을 문자열로 반환합니다.

## 시간대 () {#timezone}

서버의 시간대를 반환합니다.

## 블록 번호 {#blocknumber}

행이 데이터 블록의 순서 번호를 돌려줍니다.

## rowNumberInBlock {# function-rownumberinblock}

데이터 블록의 행의 서수를 반환합니다. 다른 데이터 블록은 항상 다시 계산됩니다.

## 로우 넘버 인 블록 () {#rownumberinallblocks}

데이터 블록의 행의 서수를 반환합니다. 이 기능만을 고려한 영향 데이터 블록입니다.

## 이웃 {#neighbor}

지정된 열의 현재 행의 앞이나 뒤에 오는 지정된 오프셋 행에 대한 액세스를 제공하는 윈도우 함수.

** 구문 **

```sql
neighbor (column, offset [, default_value)
```

함수의 결과는 영향을받는 데이터 블록과 블록의 데이터의 순서에 따라 달라집니다.
ORDER BY를 사용하여 하위 쿼리를 만들고 하위의 외부에서 함수를 호출 할 때 예상되는 결과를 얻을 수 있습니다.

** 파라미터 **

-`column` - A column name or scalar expression.
-`offset` - The number of rows forwards or backwards from the current row of`column` [Int64] (../../ sql-reference / data-types / int-uint.md).
-`default_value` - Optional. The value to be returned if offset goes beyond the scope of the block. Type of data blocks affected.

** 반환 값 **

- 값`column`에서`offset` 현재 행에서의 거리 if`offset` 값이 블록 경계 밖에서는 없습니다.
- 기본값`column` 만약`offset` 값은 블록 경계에서 제외됩니다. 만약`default_value`가 주어지면 그것이 사용됩니다.

유형 : 유형의 데이터 블록의 영향을받지 또는 기본값 유형입니다.

** 예 **

쿼리 :

```sql
SELECT number, neighbor (number 2) FROM system.numbers LIMIT 10;
```

결과 :

```text
┌─number─┬─neighbor (number 2) ─┐
│ 0 │ 2 │
│ 1 │ 3 │
│ 2 │ 4 │
│ 3 │ 5 │
│ 4 │ 6 │
│ 5 │ 7 │
│ 6 │ 8 │
│ 7 │ 9 │
│ 8 │ 0 │
│ 9 │ 0 │
└────────┴─────────────────────┘
```

쿼리 :

```sql
SELECT number, neighbor (number 2, 999) FROM system.numbers LIMIT 10;
```

결과 :

```text
┌─number─┬─neighbor (number 2, 999) ─┐
│ 0 │ 2 │
│ 1 │ 3 │
│ 2 │ 4 │
│ 3 │ 5 │
│ 4 │ 6 │
│ 5 │ 7 │
│ 6 │ 8 │
│ 7 │ 9 │
│ 8 │ 999 │
│ 9 │ 999 │
└────────┴──────────────────────────┘
```

이 함수를 사용하여 전년 대비 메트릭 값을 계산할 수 있습니다 :

쿼리 :

```sql
WITH toDate ( '2018-01-01') AS start_date
SELECT
    toStartOfMonth (start_date + (number * 32)) AS month,
    toInt32 (month) 100 % AS money,
    neighbor (money -12) AS prev_year,
    round (prev_year / money 2) AS year_over_year
FROM numbers (16)
```

결과 :

```text
┌──────month─┬─money─┬─prev_year─┬─year_over_year─┐
│ 2018-01-01 │ 32 │ 0 │ 0 │
│ 2018-02-01 │ 63 │ 0 │ 0 │
│ 2018-03-01 │ 91 │ 0 │ 0 │
│ 2018-04-01 │ 22 │ 0 │ 0 │
│ 2018-05-01 │ 52 │ 0 │ 0 │
│ 2018-06-01 │ 83 │ 0 │ 0 │
│ 2018-07-01 │ 13 │ 0 │ 0 │
│ 2018-08-01 │ 44 │ 0 │ 0 │
│ 2018-09-01 │ 75 │ 0 │ 0 │
│ 2018-10-01 │ 5 │ 0 │ 0 │
│ 2018-11-01 │ 36 │ 0 │ 0 │
│ 2018-12-01 │ 66 │ 0 │ 0 │
│ 2019-01-01 │ 97 │ 32 │ 0.33 │
│ 2019-02-01 │ 28 │ 63 │ 2.25 │
│ 2019-03-01 │ 56 │ 91 │ 1.62 │
│ 2019-04-01 │ 87 │ 22 │ 0.25 │
└────────────┴───────┴───────────┴──────────────── ┘
```

## runningDifference (x) {# other_functions-runningdifference}

Calculates the difference between successive row values ​​in the data block.
첫 번째 줄에는 0을 반환 후속 행마다 이전 행과의 차이를 반환합니다.

함수의 결과는 영향을받는 데이터 블록과 블록의 데이터의 순서에 따라 달라집니다.
ORDER BY를 사용하여 하위 쿼리를 만들고 하위의 외부에서 함수를 호출 할 때 예상되는 결과를 얻을 수 있습니다.

예 :

```sql
SELECT
    EventID,
    EventTime,
    runningDifference (EventTime) AS delta
FROM
(
    SELECT
        EventID,
        EventTime
    FROM events
    WHERE EventDate = '2016-11-24'
    ORDER BY EventTime ASC
    LIMIT 5
)
```

```text
┌─EventID─┬───────────EventTime─┬─delta─┐
│ 1106 │ 2016-11-24 00:00:04 │ 0 │
│ 1107 │ 2016-11-24 00:00:05 │ 1 │
│ 1108 │ 2016-11-24 00:00:05 │ 0 │
│ 1109 │ 2016-11-24 00:00:09 │ 4 │
│ 1110 │ 2016-11-24 00:00:10 │ 1 │
└─────────┴─────────────────────┴───────┘
```

주의 - 블록 크기는 결과에 영향을줍니다. 각각의 새로운 블록에서는`runningDifference` 상태가 재설정됩니다.

```sql
SELECT
    number,
    runningDifference (number + 1) AS diff
FROM numbers (100000)
WHERE diff! = 1
```

```text
┌─number─┬─diff─┐
│ 0 │ 0 │
└────────┴──────┘
┌─number─┬─diff─┐
│ 65536 │ 0 │
└────────┴──────┘
```

```sql
set max_block_size = 100000 - default value is 65536!

SELECT
    number,
    runningDifference (number + 1) AS diff
FROM numbers (100000)
WHERE diff! = 1
```

```text
┌─number─┬─diff─┐
│ 0 │ 0 │
└────────┴──────┘
```

## runningDifferenceStartingWithFirstvalue {#runningdifferencestartingwithfirstvalue}

마찬가지로 [runningDifference (./ other-functions.md # other_functions-runningdifference) 차이는 첫 번째 행의 값이며, 첫 번째 행의 값을 반환 이후의 각 행은 이전 행의 차이 를 반환합니다.

## 맥 남 트 문자열 (num) {#macnumtostringnum}

UInt64 번호를 받아들입니다. 빅 endian의 MAC 주소로 해석합니다. 해당 MAC 주소가 포함 된 문자열을 AA : BB : CC : DD : EE : FF (십육 진수 콜론 수치) 형식으로 반환합니다.

## 맥 문자열 베트남 (s) {#macstringtonums}

MACNumToString의 역함수. MAC 주소의 형식이 잘못된 경우는 0을 돌려줍니다.

## 맥 문자열 투이 (s) {#macstringtoouis}

AA : BB : CC : DD : EE : FF (십육 진수 콜론 숫자) 형식으로 MAC 주소를 받아들입니다. Uint64 번호로 처음 세 옥텟을 반환합니다. MAC 주소의 형식이 잘못된 경우는 0을 돌려줍니다.

## getSizeOfEnumType {#getsizeofenumtype}

필드의 수를 돌려줍니다. [Enum (../../ sql-reference / data-types / enum.md).

```sql
getSizeOfEnumType (value)
```

** 파라미터 : **

-`value` - Value of type`Enum`.

** 반환 값 **

- 필드의 수`Enum` 입력 값.
- 형식화되지 않은 경우, 예외가 슬로우됩니다`Enum`.

** 예 **

```sql
SELECT getSizeOfEnumType (CAST ( 'a'AS Enum8 ( 'a'= 1, 'b'= 2))) AS x
```

```text
┌─x─┐
│ 2 │
└───┘
```

## blockSerializedSize {#blockserializedsize}

압축을 고려하지 않고 디스크의 사이즈를 돌려줍니다.

```sql
blockSerializedSize (value [, value [, ...]])
```

** 파라미터 : **

-`value` - Any value.

** 반환 값 **

- 값의 블록 (압축 없음)를 위해 디스크에 기입 해지는 바이트 수.

** 예 **

```sql
SELECT blockSerializedSize (maxState (1)) as x
```

```text
┌─x─┐
│ 2 │
└───┘
```

## toColumnTypeName {#tocolumntypename}

RAM의 열의 데이터 형식을 나타내는 클래스의 이름을 돌려줍니다.

```sql
toColumnTypeName (value)
```

** 파라미터 : **

-`value` - Any type of value.

** 반환 값 **

- 나타내는 데 사용되는 클래스의 이름을 가진 문자열`value` RAM의 데이터 형식입니다.

** 차이의 예`toTypeName 'and'toColumnTypeName` **

```sql
SELECT toTypeName (CAST ( '2018-01-01 01:02:03'AS DateTime))
```

```text
┌─toTypeName (CAST ( '2018-01-01 01:02:03', 'DateTime')) ─┐
│ DateTime │
└───────────────────────────────────────────────── ────┘
```

```sql
SELECT toColumnTypeName (CAST ( '2018-01-01 01:02:03'AS DateTime))
```

```text
┌─toColumnTypeName (CAST ( '2018-01-01 01:02:03', 'DateTime')) ─┐
│ Const (UInt32) │
└───────────────────────────────────────────────── ──────────┘
```

이 예에서는`DateTime` 데이`Const (UInt32)`.

## dumpColumnStructure {#dumpcolumnstructure}

RAM의 데이터 구조에 대한 자세한 설명을 출력합니다

```sql
dumpColumnStructure (value)
```

** 파라미터 : **

-`value` - Any type of value.

** 반환 값 **

- 나타내는 데 사용되는 구조를 기술하는 캐릭터 라인`value` RAM의 데이터 형식입니다.

** 예 **

```sql
SELECT dumpColumnStructure (CAST ( '2018-01-01 01:02:03', 'DateTime'))
```

```text
┌─dumpColumnStructure (CAST ( '2018-01-01 01:02:03', 'DateTime')) ─┐
│ DateTime, Const (size = 1, UInt32 (size = 1)) │
└───────────────────────────────────────────────── ─────────────┘
```

## defaultValueOfArgumentType {#defaultvalueofargumenttype}

데이터 형식의 기본 값을 출력합니다.

사용자가 설정 한 사용자 지정 열의 기본값은 포함되지 않습니다.

```sql
defaultValueOfArgumentType (expression)
```

** 파라미터 : **

-`expression` - Arbitrary type of value or an expression that results in a value of an arbitrary type.

** 반환 값 **

-`0` 숫자 위해.
- 문자열의 경우 빈 문자열입니다.
-`ᴺᵁᴸᴸ` 위해 [Null 가능 (../../ sql-reference / data-types / nullable.md).

** 예 **

```sql
SELECT defaultValueOfArgumentType (CAST (1 AS Int8))
```

```text
┌─defaultValueOfArgumentType (CAST (1 'Int8')) ─┐
│ 0 │
└─────────────────────────────────────────────┘
```

```sql
SELECT defaultValueOfArgumentType (CAST (1 AS Nullable (Int8)))
```

```text
┌─defaultValueOfArgumentType (CAST (1 'Nullable (Int8)')) ─┐
│ ᴺᵁᴸᴸ │
└───────────────────────────────────────────────── ──────┘
```

## 복제 {# other-functions-replicate}

단일 값을 가지는 배열을 만듭니다.

내부 구현을 위해 사용되는 [배열 조인 (array-join.md # functions_arrayjoin).

```sql
SELECT replicate (x, arr);
```

** 파라미터 : **

-`arr` - Original array. ClickHouse creates a new array of the same length as the original and fills it with the value`x`.
-`x` - The value that the resulting array will be filled with.

** 반환 값 **

값이 채워진 배열`x`.

유형 :`Array`.

** 예 **

쿼리 :

```sql
SELECT replicate (1, 'a', 'b', 'c'])
```

결과 :

```text
┌─replicate (1, 'a', 'b', 'c']) ─┐
│ [1,1,1] │
└───────────────────────────────┘
```

## filesystemAvailable {#filesystemavailable}

환불액의 남은 공간 파일 시스템의 파일의 데이터베이스가 있습니다. 이것은 항상 총 공간보다 작습니다 (filesystemFree (# filesystemfree)) 일부 공간은 OS에 예약되어 있기 때문이다.

** 구문 **

```sql
filesystemAvailable ()
```

** 반환 값 **

- 바이트 단위에서 사용 가능한 나머지 공간의 양.

유형 : UInt64 (../../ sql-reference / data-types / int-uint.md).

** 예 **

쿼리 :

```sql
SELECT formatReadableSize (filesystemAvailable ()) AS "Available space"toTypeName (filesystemAvailable ()) AS "Type";
```

결과 :

```text
┌─Available space─┬─Type───┐
│ 30.75 GiB │ UInt64 │
└─────────────────┴────────┘
```

## filesystemFree {#filesystemfree}

데이터베이스 파일이있는 파일 시스템 공간의 총량을 반환합니다. 도 참조. `filesystemAvailable`

** 구문 **

```sql
filesystemFree ()
```

** 반환 값 **

- 바이트 단위 공간의 양.

유형 : UInt64 (../../ sql-reference / data-types / int-uint.md).

** 예 **

쿼리 :

```sql
SELECT formatReadableSize (filesystemFree ()) AS "Free space"toTypeName (filesystemFree ()) AS "Type";
```

결과 :

```text
┌─Free space─┬─Type───┐
│ 32.39 GiB │ UInt64 │
└────────────┴────────┘
```

## filesystemCapacity {#filesystemcapacity}

파일 시스템의 용량을 바이트 단위로 돌려줍니다. 평가를 위해, 경로 (../../ operations / server-configuration-parameters / settings.md # server_configuration_parameters-path) 데이터 디렉토리

** 구문 **

```sql
filesystemCapacity ()
```

** 반환 값 **

- 바이트 단위의 파일 시스템 용량 정보.

유형 : UInt64 (../../ sql-reference / data-types / int-uint.md).

** 예 **

쿼리 :

```sql
SELECT formatReadableSize (filesystemCapacity ()) AS "Capacity"toTypeName (filesystemCapacity ()) AS "Type"
```

결과 :

```text
┌─Capacity──┬─Type───┐
│ 39.32 GiB │ UInt64 │
└───────────┴────────┘
```

## finalizeAggregation {# function-finalizeaggregation}

집계 함수의 상태를 취합니다. 집계 결과 (최종 상태)를 돌려줍니다.

## runningAccumulate {# function-runningaccumulate}

집계 함수의 상태를 가지고 값을 가지는 라인을 돌려 처음부터 현재 행에 블록 행 세트를 위해 이러한 상태의 축적의 결과입니다.
예를 들어, 집계 함수의 상태를 그대로 (예 runningAccumulate (uniqState (UserID))) 블록의 각 행에 대해 모든 이전의 행과 현재 행의 상태 병합시에 집계 함수의 결과를 반환합니다.
따라서 함수의 결과는 데이터 블록의 분할 및 블록의 데이터 순서에 의존한다.

## joinGet {#joinget}

이 함수를 사용하면 a와 같은 방법으로 테이블에서 데이터를 추출 할 수 있습니다 [사전] (../../ sql-reference / dictionaries / index.md).

데이터 검색 [참여] (../../ engines / table-engines / special / join.md # creating a-table) 지정된 조인 키를 사용하는 테이블.

지원뿐만 아니라 테이블에서 생성 된`ENGINE = Join (ANY, LEFT <join_keys>)`계산서.

** 구문 **

```sql
joinGet (join_storage_table_name`value_column`, join_keys)
```

** 파라미터 **

-`join_storage_table_name` - an 식별자 (../ syntax.md # syntax-identifiers) 검색이 실행되는 위치를 나타냅니다. 식별자는 기본 데이터베이스에서 검색됩니다 (매개 변수를`default_database` 설정 파일). 기본 데이터베이스를 덮어 쓰려면`USE db_name` 또는 지정 데이터베이스의 테이블 분리기`db_name.db_table` 예를 참조하십시오.
-`value_column` - name of the column of the table that contains required data.
-`join_keys` - list of keys.

** 반환 값 **

키의 목록에 해당하는 값의리스트를 돌려줍니다.

소스 테이블에 어떤 것들이 존재하지 않는 경우`0` 또는`null`에 따라 반환됩니다 [join_use_nulls (../../ operations / settings / settings.md # join_use_nulls) 설정.

자세한 내용`join_use_nulls`에서 결합 작업 (../../ engines / table-engines / special / join.md).

** 예 **

입력 테이블 :

```sql
CREATE DATABASE db_test
CREATE TABLE db_test.id_val (`id` UInt32`val` UInt32) ENGINE = Join (ANY, LEFT, id) SETTINGS join_use_nulls = 1
INSERT INTO db_test.id_val VALUES (1,11) (2,12) (4,13)
```

```text
┌─id─┬─val─┐
│ 4 │ 13 │
│ 2 │ 12 │
│ 1 │ 11 │
└────┴─────┘
```

쿼리 :

```sql
SELECT joinGet (db_test.id_val 'val', toUInt32 (number)) from numbers (4) SETTINGS join_use_nulls = 1
```

결과 :

```text
┌─joinGet (db_test.id_val 'val', toUInt32 (number)) ─┐
│ 0 │
│ 11 │
│ 12 │
│ 0 │
└───────────────────────────────────────────────── ─┘
```

## modelEvaluate (model_name ...) {# function-modelevaluate}

외부 모델의 평가
모델명과 모델 인수를받습니다. Float64를 돌려줍니다.

## throwIf (x \, custom_message \]) {# throwifx-custom-message}

인수가 제로가 아닌 경우는 예외를 throw합니다.
custom_message- 선택적 매개 변수입니다.

```sql
SELECT throwIf (number = 3 'Too many') FROM numbers (10);
```

```text
↙ Progress : 0.00 rows 0.00 B (0.00 rows / s. 0.00 B / s) Received exception from server (version 19.14.1) :
Code : 395. DB :: Exception : Received from localhost : 9000 : DB :: Exception : Too many.
```

## id {#identity}

인수로 사용 된 것과 동일한 값을 반환합니다. 인덱스를 사용하여 취소 전체 검색 쿼리 성능을 얻을 수 있습니다. 쿼리를 분석하여 인덱스를 사용할 수있는 경우 분석기는 내부를 볼 수 없다`identity` 기능.

** 구문 **

```sql
identity (x)
```

** 예 **

쿼리 :

```sql
SELECT identity (42)
```

결과 :

```text
┌─identity (42) ─┐
│ 42 │
└──────────────┘
```

## randomPrintableASCII {#randomascii}

임의의 집합을 가진 문자열을 생성합니다 [ASCII] (https://en.wikipedia.org/wiki/ASCII#Printable_characters) 인쇄 가능한 문자.

** 구문 **

```sql
randomPrintableASCII (length)
```

** 파라미터 **

-`length` - Resulting string length. Positive integer.

        If you pass`length <0`, behavior of the function is undefined.

** 반환 값 **

- 임의의 집합을 가진 문자열 [ASCII] (https://en.wikipedia.org/wiki/ASCII#Printable_characters) 인쇄 가능한 문자.

유형 : 문자열 (../../ sql-reference / data-types / string.md)

** 예 **

```sql
SELECT number, randomPrintableASCII (30) as str, length (str) FROM system.numbers LIMIT 3
```

```text
┌─number─┬─str────────────────────────────┬─length (randomPrintableASCII (30)) ─┐
│ 0 │ SuiCOSTvC0csfABSw = UcSzp2.`rv8x │ 30 │
│ 1 │ 1Ag NlJ & RCN : *> HVPG; PE-nO "SUFD │ 30 │
│ 2 │ / "+ <"wUTh : = LjJ Vm! c & hI * m # XTfzz │ 30 │
└────────┴────────────────────────────────┴─────── ───────────────────────────┘
```

원본 기사 (https://clickhouse.tech/docs/en/query_language/functions/other_functions/) <! - hide ->
