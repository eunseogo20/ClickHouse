---
machine_translated : true
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd
toc_priority : 38
toc_title : "\ u30BF \ u30A4 \ u30D7 \ u5909 \ u63DB"
---

# 형식 변환 함수 {# type-conversion-functions}

## 숫자 변환 일반적인 문제 {# numeric-conversion-issues}

데이터 형식에서 다른 데이터 형식으로 값을 변환하는 경우, 일반적인 경우에는 데이터 손실로 이어질 위험한 작업임을 기억해야합니다. 큰 데이터 형식에서 작은 데이터 형에 값을 근사하려고하거나 다른 데이터 형식간에 값을 변환 할 경우 데이터 손실이 발생할 수 있습니다.

클릭 하우스는 [C ++ 프로그램과 동일한 동작 (https://en.cppreference.com/w/cpp/language/implicit_conversion).

## 토인토 (8/16/32/64) {# toint8163264}

입력 값을 [Int] (../../ sql-reference / data-types / int-uint.md) 데이터 형식입니다. 이 함수 제품군 :

-`toInt8 (expr)`- Results in the`Int8` 데이터 형식입니다.
-`toInt16 (expr)`- Results in the`Int16` 데이터 형식입니다.
-`toInt32 (expr)`- Results in the`Int32` 데이터 형식입니다.
-`toInt64 (expr)`- Results in the`Int64` 데이터 형식입니다.

** 파라미터 **

-`expr` - 식 (../ syntax.md # syntax-expressions) 숫자 또는 숫자의 십진수 표현을 가지는 문자열을 반환합니다. 수치 이원 표현, 팔 진수, 진수 표현은 지원되지 않습니다. 첫 번째 0이 제거됩니다.

** 반환 값 **

정수`Int8``Int16``Int32` 또는`Int64` 데이터 형식입니다.

함수의 사용 [제로 반올림 (https://en.wikipedia.org/wiki/Rounding#Rounding_towards_zero) 즉, 숫자의 소수 부분의 숫자를 자릅니다.

대한 함수의 행동 [NaN 및 Inf (../../ sql-reference / data-types / float.md # data_type-float-nan-inf) 인수는 정의되지 않습니다. 기억 숫자 변환 문제 (# numeric-conversion-issues) 함수를 사용하는 경우.

** 예 **

```sql
SELECT toInt64 (nan) toInt32 (32) toInt16 ('16 ') toInt8 (8.8)
```

```text
┌─────────toInt64 (nan) ─┬─toInt32 (32) ─┬─toInt16 ( '16') ─┬─toInt8 (8.8) ─┐
│ -9223372036854775808 │ 32 │ 16 │ 8 │
└──────────────────────┴─────────────┴──────────── ───┴─────────────┘
```

## toInt (8/16/32/64) OrZero {# toint8163264orzero}

String 형의 인수를 가져다가 Int로 해석하려고합니다 (8 \ | 16 \ | 32 \ | 64). 실패했을 경우는 0을 돌려줍니다.

** 예 **

```sql
select toInt64OrZero ( '123123') toInt8OrZero ( '123qwe123')
```

```text
┌─toInt64OrZero ( '123123') ─┬─toInt8OrZero ( '123qwe123') ─┐
│ 123123 │ 0 │
└─────────────────────────┴─────────────────────── ────┘
```

## toInt (8/16/32/64) OrNull {# toint8163264ornull}

String 형의 인수를 가져다가 Int로 해석하려고합니다 (8 \ | 16 \ | 32 \ | 64). 실패하면 NULL을 반환합니다.

** 예 **

```sql
select toInt64OrNull ( '123123') toInt8OrNull ( '123qwe123')
```

```text
┌─toInt64OrNull ( '123123') ─┬─toInt8OrNull ( '123qwe123') ─┐
│ 123123 │ ᴺᵁᴸᴸ │
└─────────────────────────┴─────────────────────── ────┘
```

## 투인토 (8/16/32/64) {# touint8163264}

입력 값을 [UInt (../../ sql-reference / data-types / int-uint.md) 데이터 형식입니다. 이 함수 제품군 :

-`toUInt8 (expr)`- Results in the`UInt8` 데이터 형식입니다.
-`toUInt16 (expr)`- Results in the`UInt16` 데이터 형식입니다.
-`toUInt32 (expr)`- Results in the`UInt32` 데이터 형식입니다.
-`toUInt64 (expr)`- Results in the`UInt64` 데이터 형식입니다.

** 파라미터 **

-`expr` - 식 (../ syntax.md # syntax-expressions) 숫자 또는 숫자의 십진수 표현을 가지는 문자열을 반환합니다. 수치 이원 표현, 팔 진수, 진수 표현은 지원되지 않습니다. 첫 번째 0이 제거됩니다.

** 반환 값 **

정수`UInt8``UInt16``UInt32` 또는`UInt64` 데이터 형식입니다.

함수의 사용 [제로 반올림 (https://en.wikipedia.org/wiki/Rounding#Rounding_towards_zero) 즉, 숫자의 소수 부분의 숫자를 자릅니다.

부정적인 함수에 대한 함수의 동작 및 NaN 및 Inf (../../ sql-reference / data-types / float.md # data_type-float-nan-inf) 인수는 정의되지 않습니다. 음수의 문자열을 전달하면 다음과 같이됩니다` '-32'`, ClickHouse는 예외를 발생시킵니다. 기억 숫자 변환 문제 (# numeric-conversion-issues) 함수를 사용하는 경우.

** 예 **

```sql
SELECT toUInt64 (nan) toUInt32 (-32), toUInt16 ('16 ') toUInt8 (8.8)
```

```text
┌───────toUInt64 (nan) ─┬─toUInt32 (-32) ─┬─toUInt16 ( '16') ─┬─toUInt8 (8.8) ─┐
│ 9223372036854775808 │ 4294967264 │ 16 │ 8 │
└─────────────────────┴───────────────┴─────────── ─────┴──────────────┘
```

## 투인토 (8/16/32/64) 오루제로 {# touint8163264orzero}

## 투인토 (8/16/32/64) OrNull {# touint8163264ornull}

## 토후로아 (32/64) {# tofloat3264}

## toFloat (32/64) OrZero {# tofloat3264orzero}

## toFloat (32/64) OrNull {# tofloat3264ornull}

## 東立 (새순 일부러 {#todate}

## 토다테오루제로 {#todateorzero}

## toDateOrNull {#todateornull}

## toDateTime {#todatetime}

## 토다 티 메 올 제로 {#todatetimeorzero}

## toDateTimeOrNull {#todatetimeornull}

## 토데시마루 (32/64/128) {# todecimal3264128}

변환`value`에 소수 (../../ sql-reference / data-types / decimal.md) 정확한 데이터 형`S` 그`value` 수치 또는 문자열을 지정할 수 있습니다. 그`S` (scale) 매개 변수는 소수점 이하 자릿수를 지정합니다.

-`toDecimal32 (value, S)`
-`toDecimal64 (value, S)`
-`toDecimal128 (value, S)`

## toDecimal (32/64/128) OrNull {# todecimal3264128ornull}

입력 문자열을 a로 변환합니다 [Nullable (Decimal (P, S))) (../../ sql-reference / data-types / decimal.md) 데이터 형의 값. 이 제품군의 기능 등 :

-`toDecimal32OrNull (expr, S)`- Results in`Nullable (Decimal32 (S))`데이터 형식입니다.
-`toDecimal64OrNull (expr, S)`- Results in`Nullable (Decimal64 (S))`데이터 형식입니다.
-`toDecimal128OrNull (expr, S)`- Results in`Nullable (Decimal128 (S))`데이터 형식입니다.

이 함수는`toDecimal * ()`당신이 얻을 것을 선호한다면, 함수`NULL` 입력 값의 분석 오류가 발생하면 예외가 아닌 값.

** 파라미터 **

-`expr` - 식 (../ syntax.md # syntax-expressions)의 값을 돌려줍니다. [문자열] (../../ sql-reference / data-types / string.md) 데이터 형식입니다. ClickHouse은 십진수의 텍스트 표현을 상정하고 있습니다. 예를 들어,` '1.111'`.
-`S` - Scale, the number of decimal places in the resulting value.

** 반환 값 **

값`Nullable (Decimal (P, S))`데이터 형식입니다. 이 값을 포함 :

-와 수`S` ClickHouse가 입력 문자열을 숫자로 해석하는 경우 소수점 이하의 자리수.
-`NULL`, ClickHouse가 입력 문자열을 숫자로 해석 할 수없는 경우 또는 입력 번호에 다음이 포함되어있는 경우`S` 소수점 이하의 자리수.

** 예 **

```sql
SELECT toDecimal32OrNull (toString (-1.111), 5) AS val, toTypeName (val)
```

```text
┌──────val─┬─toTypeName (toDecimal32OrNull (toString (-1.111), 5)) ─┐
│ -1.11100 │ Nullable (Decimal (9, 5)) │
└──────────┴────────────────────────────────────── ──────────────┘
```

```sql
SELECT toDecimal32OrNull (toString (-1.111), 2) AS val, toTypeName (val)
```

```text
┌──val─┬─toTypeName (toDecimal32OrNull (toString (-1.111), 2)) ─┐
│ ᴺᵁᴸᴸ │ Nullable (Decimal (9, 2)) │
└──────┴────────────────────────────────────────── ──────────┘
```

## toDecimal (32/64/128) OrZero {# todecimal3264128orzero}

입력 값을 [소수 (P, S) (../../ sql-reference / data-types / decimal.md) 데이터 형식입니다. 이 제품군의 기능 등 :

-`toDecimal32OrZero (expr, S)`- Results in`Decimal32 (S)`데이터 형식입니다.
-`toDecimal64OrZero (expr, S)`- Results in`Decimal64 (S)`데이터 형식입니다.
-`toDecimal128OrZero (expr, S)`- Results in`Decimal128 (S)`데이터 형식입니다.

이 함수는`toDecimal * ()`당신이 얻을 것을 선호한다면, 함수`0` 입력 값의 분석 오류가 발생하면 예외가 아닌 값.

** 파라미터 **

-`expr` - 식 (../ syntax.md # syntax-expressions)의 값을 돌려줍니다. [문자열] (../../ sql-reference / data-types / string.md) 데이터 형식입니다. ClickHouse은 십진수의 텍스트 표현을 상정하고 있습니다. 예를 들어,` '1.111'`.
-`S` - Scale, the number of decimal places in the resulting value.

** 반환 값 **

값`Nullable (Decimal (P, S))`데이터 형식입니다. 이 값을 포함 :

-와 수`S` ClickHouse가 입력 문자열을 숫자로 해석하는 경우 소수점 이하의 자리수.
- 0과 함께`S` ClickHouse가 입력 문자열을 숫자로 해석 할 수없는 경우 또는 입력 번호에 다음이 포함되어있는 경우에는 소수점 이하의 자리수`S` 소수점 이하의 자리수.

** 예 **

```sql
SELECT toDecimal32OrZero (toString (-1.111), 5) AS val, toTypeName (val)
```

```text
┌──────val─┬─toTypeName (toDecimal32OrZero (toString (-1.111), 5)) ─┐
│ -1.11100 │ Decimal (9 5) │
└──────────┴────────────────────────────────────── ──────────────┘
```

```sql
SELECT toDecimal32OrZero (toString (-1.111), 2) AS val, toTypeName (val)
```

```text
┌──val─┬─toTypeName (toDecimal32OrZero (toString (-1.111), 2)) ─┐
│ 0.00 │ Decimal (9, 2) │
└──────┴────────────────────────────────────────── ──────────┘
```

## toString {#tostring}

숫자, 문자열 (단, 고정 문자열이 없습니다), 날짜 및 시간을 가진 날짜 사이의 변환을위한 함수.
이러한 모든 기능을 수용 한 인수.

문자열 사이에서 변환하는 경우, 값은 TabSeparated 형식 (그리고 거의 모든 다른 텍스트 형식)과 같은 규칙을 사용하여 서식 또는 해석됩니다. 문자열을 해석 할 수없는 경우는 예외를 throw 요청이 취소됩니다.

날짜를 숫자로 변환하거나 그 반대의 경우 날짜는 Unix 에포크의 시작부터 일을 지원합니다.
시간이있는 날짜를 숫자로 변환하거나 그 반대의 경우, 시간이있는 날짜는 Unix 에포크의 시작부터 초에 해당합니다.

ToDate / toDateTime 함수의 날짜와 날짜 및 시간 형식은 다음과 같이 정의됩니다 :

```text
YYYY-MM-DD
YYYY-MM-DD hh : mm : ss
```

예외로, uint32, Int32, UInt64 또는 Int64 숫자 형식에서 Date로 변환 할 경우 수치가 65536 이상인 경우 숫자는 Unix 타임 스탬프로 해석됩니다 (날짜가 아니라) 날짜이 지원을위한 공통의 발생 쓰기 'toDate (unix_timestamp)'그렇지 않으면 오류가 발생 더 귀찮은 쓰기가 필요합니다 'toDate (toDateTime (unix_timestamp))'.

날짜와 시간 사이의 변환은 null 시간을 추가하거나 시간을 제거함으로써 자연적인 방법으로 실행됩니다.

숫자 형식 간의 변환은 C ++ 다른 숫자 사이의 할당과 같은 규칙이 사용됩니다.

또한 DateTime 인수의 toString 함수는 타임 존의 이름을 포함한 두 번째 문자열 인수를 취할 수 있습니다. 예 :`Asia / Yekaterinburg`이 경우 시간은 지정된 시간대에 따라 서식됩니다.

```sql
SELECT
    now () AS now_local,
    toString (now (), 'Asia / Yekaterinburg') AS now_yekat
```

```text
┌───────────now_local─┬─now_yekat───────────┐
│ 2016-06-15 00:11:21 │ 2016-06-15 02:11:21 │
└─────────────────────┴─────────────────────┘
```

또한`toUnixTimestamp` 기능.

## toFixedString (s, N) {# tofixedstrings-n}

문자열 인수를 FixedString (N) 형 (고정 길이 N의 문자열)로 변환합니다. N은 정수가 아니면 안됩니다.
문자열의 바이트 수가 N보다 작 으면 오른쪽에 null 바이트가 포함됩니다. 문자열의 바이트 수가 N보다 많은 경우는 예외가 슬로우됩니다.

## 비트 문자열 컷 제로 (s) {#tostringcuttozeros}

문자열 또는 FixedString 인수를받습니다. 발견 된 최초의 0 바이트로 잘린 내용의 문자열을 반환합니다.

예 :

```sql
SELECT toFixedString ( 'foo', 8) AS s, toStringCutToZero (s) AS s_cut
```

```text
┌─s─────────────┬─s_cut─┐
│ foo \ 0 \ 0 \ 0 \ 0 \ 0 │ foo │
└───────────────┴───────┘
```

```sql
SELECT toFixedString ( 'foo \ 0bar', 8) AS s, toStringCutToZero (s) AS s_cut
```

```text
┌─s──────────┬─s_cut─┐
│ foo \ 0bar \ 0 │ foo │
└────────────┴───────┘
```

## 재 해석 (8/16/32/64) {# reinterpretasuint8163264}

## 재 해석 (8/16/32/64) {# reinterpretasint8163264}

## reinterpretAsFloat (32/64) {# reinterpretasfloat3264}

## 재 해석 일 {#reinterpretasdate}

## 데이터의 재 해석 {#reinterpretasdatetime}

이 함수는 문자열을 받아 문자열의 앞에 놓인 바이트를 호스트 순서 (Little Endian)의 값으로 해석합니다. 문자열이 충분히 길지 않은 경우, 함수는 문자열이 null 바이트의 필요한 숫자로 포함 된 것처럼 작동합니다. 문자열이 필요 이상으로 긴 경우 추가 바이트는 무시됩니다. 날짜는 Unix 에포크의 시작부터 일로서 해석되어 시간을 가진 날짜는 Unix 에포크의 시작에서 초로 처리됩니다.

## 재 해석 {# type_conversion_functions-reinterpretAsString}

이 함수는 숫자 또는 날짜 또는 날짜와 시간을 받아 해당 값을 호스트 순서 (Little Endian)으로 나타내는 바이트를 포함하는 문자열을 반환합니다. Null 바이트는 끝에서 삭제됩니다. 예를 들어, UInt32 형의 255 바이트 길이의 문자열입니다.

## reinterpretAsFixedString {#reinterpretasfixedstring}

이 함수는 숫자 또는 날짜 또는 날짜와 시간을 받고 호스트 순서 (Little Endian)에 해당하는 값을 나타내는 바이트를 포함 FixedString를 돌려줍니다. Null 바이트는 끝에서 삭제됩니다. 예를 들어, UInt32 형의 255 바이트 길이의 FixedString입니다.

## 캐스트 (x, T) {# type_conversion_function-cast}

변환 'x'를 't'데이터 형식입니다. 구문 CAST (x AS t)도 지원되고 있습니다.

예 :

```sql
SELECT
    '2016-06-15 23:00:00'AS timestamp,
    CAST (timestamp AS DateTime) AS datetime,
    CAST (timestamp AS Date) AS date,
    CAST (timestamp, 'String') AS string,
    CAST (timestamp 'FixedString (22)') AS fixed_string
```

```text
┌─timestamp───────────┬────────────datetime─┬───────date─┬─string──────── ──────┬─fixed_string──────────────┐
│ 2016-06-15 23:00:00 │ 2016-06-15 23:00:00 │ 2016-06-15 │ 2016-06-15 23:00:00 │ 2016-06-15 23:00:00 \ 0 \ 0 \ 0 │
└─────────────────────┴─────────────────────┴───── ───────┴─────────────────────┴──────────────────── ───────┘
```

FixedString (N)로 변환은 String 형 또는 FixedString (N) 형의 인수에만 작동합니다.

유형 변환 [Null 가능 (../../ sql-reference / data-types / nullable.md) 그리고 뒤는 지원된다. 예 :

```sql
SELECT toTypeName (x) FROM t_null
```

```text
┌─toTypeName (x) ─┐
│ Int8 │
│ Int8 │
└───────────────┘
```

```sql
SELECT toTypeName (CAST (x 'Nullable (UInt16)')) FROM t_null
```

```text
┌─toTypeName (CAST (x 'Nullable (UInt16)')) ─┐
│ Nullable (UInt16) │
│ Nullable (UInt16) │
└─────────────────────────────────────────┘
```

## toInterval (년 / 분기 \ | 월 / 주 \ | 일 \ |시 / 분 / 초) {# function-tointerval}

숫자 인수를 간격 (../../ sql-reference / data-types / special-data-types / interval.md) 데이터 형식입니다.

** 구문 **

```sql
toIntervalSecond (number)
toIntervalMinute (number)
toIntervalHour (number)
toIntervalDay (number)
toIntervalWeek (number)
toIntervalMonth (number)
toIntervalQuarter (number)
toIntervalYear (number)
```

** 파라미터 **

-`number` - Duration of interval. Positive integer number.

** 반환 값 **

- 값`Interval` 데이터 형식입니다.

** 예 **

```sql
WITH
    toDate ( '2019-01-01') AS date,
    INTERVAL 1 WEEK AS interval_week,
    toIntervalWeek (1) AS interval_to_week
SELECT
    date + interval_week,
    date + interval_to_week
```

```text
┌─plus (date, interval_week) ─┬─plus (date, interval_to_week) ─┐
│ 2019-01-08 │ 2019-01-08 │
└───────────────────────────┴───────────────────── ─────────┘
```

## parseDateTimeBestEffort {#parsedatetimebesteffort}

날짜와 시간을 변환합니다. [문자열] (../../ sql-reference / data-types / string.md)로 표현 [DateTime (../../ sql-reference / data-types / datetime.md # data_type- datetime) 데이터 형식입니다.

함수는 분석이다 [ISO 8601] (https://en.wikipedia.org/wiki/ISO_8601), RFC1123-5.2.14RFC-822日付と時刻の仕様 (https://tools.ietf.org/ html / rfc1123 # page-55) ClickHouse의 그리고 몇 가지 다른 날짜 및 시간 형식.

** 구문 **

```sql
parseDateTimeBestEffort (time_string [, time_zone);
```

** 파라미터 **

-`time_string` - String containing a date and time to convert [문자열] (../../ sql-reference / data-types / string.md).
-`time_zone` - Time zone. The function parses`time_string` 시간대에 따르면. [문자열] (../../ sql-reference / data-types / string.md).

** 지원되는 비표준 형식 **

- 9를 포함하는 문자열. .10 자리 [unix 타임 스탬프 (https://en.wikipedia.org/wiki/Unix_time).
- 날짜와 시간 요소를 가진 문자열 :`YYYYMMDDhhmmss``DD / MM / YYYY hh : mm : ss``DD-MM-YY hh : mm``YYYY-MM-DD hh : mm : ss` 등.
- 날짜가 문자열이지만, 시간 구성 요소가 없습니다 :`YYYY``YYYYMM``YYYY * MM``DD / MM / YYYY``DD-MM-YY` 등.
- 날짜 및 시간 문자열 :`DD``DD hh``DD hh : mm`이 경우`YYYY-MM`로 대체됩니다. `2000-01`.
- 날짜와 시간 및 시간대 오프셋 정보를 포함하는 문자열 :`YYYY-MM-DD hh : mm : ss ± h : mm` 등. 예를 들어,`2020-12-12 17:36:00 -5 : 00`.

단락 문자를 가지는 모든 형식에 대한 함수는 달 이름을 성명 또는 월 이름의 첫 세 글자로 나타낸을 분석합니다. 예 :`24 / DEC / 18``24-Dec-18``01-September-2018`.

** 반환 값 **

-`time_string`로 변환됩니다. `DateTime` 데이터 형식입니다.

** 예 **

쿼리 :

```sql
SELECT parseDateTimeBestEffort ('12 / 12 / 2020 12:12:57 ')
AS parseDateTimeBestEffort;
```

결과 :

```text
┌─parseDateTimeBestEffort─┐
│ 2020-12-12 12:12:57 │
└─────────────────────────┘
```

쿼리 :

```sql
SELECT parseDateTimeBestEffort ( 'Sat, 18 Aug 2018 07:22:16 GMT', 'Europe / Moscow')
AS parseDateTimeBestEffort
```

결과 :

```text
┌─parseDateTimeBestEffort─┐
│ 2018-08-18 10:22:16 │
└─────────────────────────┘
```

쿼리 :

```sql
SELECT parseDateTimeBestEffort ( '1284101485')
AS parseDateTimeBestEffort
```

결과 :

```text
┌─parseDateTimeBestEffort─┐
│ 2015-07-07 12:04:41 │
└─────────────────────────┘
```

쿼리 :

```sql
SELECT parseDateTimeBestEffort ( '2018-12-12 10:12:12')
AS parseDateTimeBestEffort
```

결과 :

```text
┌─parseDateTimeBestEffort─┐
│ 2018-12-12 10:12:12 │
└─────────────────────────┘
```

쿼리 :

```sql
SELECT parseDateTimeBestEffort ('10 20:19 ')
```

결과 :

```text
┌─parseDateTimeBestEffort ('10 20:19 ') ─┐
│ 2000-01-10 20:19:00 │
└─────────────────────────────────────┘
```

** 참고하십시오. **

- \ ISO8601에 의한 발표 @xkcd \ (https://xkcd.com/1179/)
- [RFC 1123] (https://tools.ietf.org/html/rfc1123)
- [東立 (새순이다 해 (# todate)
- [toDateTime (# todatetime)

## parseDateTimeBestEffortOrNull {#parsedatetimebesteffortornull}

마찬가지로 [parseDateTimeBestEffort (# parsedatetimebesteffort) 그러나 처리 할 수없는 날짜 형식이 발견되면 null가 돌려 주어집니다.

## parseDateTimeBestEffortOrZero {#parsedatetimebesteffortorzero}

마찬가지로 [parseDateTimeBestEffort (# parsedatetimebesteffort) 그러나 처리 할 수없는 날짜 형식이 검출 된 때 제로 날짜 또는 제로의 날짜와 시간을 반환합니다.

원본 기사 (https://clickhouse.tech/docs/en/query_language/functions/type_conversion_functions/) <! - hide ->
