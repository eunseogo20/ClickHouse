---
machine_translated : true
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd
toc_priority : 39
toc_title : "\ u65E5 \ u4ED8 \ u3068 \ u6642 \ u523B \ u306E \ u64CD \ u4F5C"
---

# 날짜와 시간을 조작하는 함수 {# functions-for-working-with-dates-and-times}

시간대 지원

시간대에 논리적으로 사용되는 날짜와 시간을 조작하기위한 모든 함수는 옵션 시간대 인수를 받아 들일 수 있습니다. 예 : 아시아 / 예 카테 린 부르크. 이 경우 로컬 (기본)의 시간대가 아니라 지정된 시간대를 사용합니다.

```sql
SELECT
    toDateTime ( '2016-06-15 23:00:00') AS time,
    toDate (time) AS date_local,
    toDate (time 'Asia / Yekaterinburg') AS date_yekat,
    toString (time 'US / Samoa') AS time_samoa
```

```text
┌────────────────time─┬─date_local─┬─date_yekat─┬─time_samoa──────────┐
│ 2016-06-15 23:00:00 │ 2016-06-15 │ 2016-06-16 │ 2016-06-15 09:00:00 │
└─────────────────────┴────────────┴────────────┴─ ────────────────────┘
```

UTC와 시간이 서로 다른 시간대 만 지원됩니다.

## 비트 팀 영역 {#totimezone}

시간 또는 날짜와 시간을 지정한 시간대로 변환합니다.

## toYear {#toyear}

날짜 또는 날짜와 시간을 연 번호 (AD)를 포함 UInt16 번호로 변환합니다.

## 토쿠아타 {#toquarter}

Date 또는 date with time을 분기 번호를 포함 UInt8 번호로 변환합니다.

## 토몬스 {#tomonth}

Date 또는 date with time을 달 번호 (1-12)를 포함 UInt8 번호로 변환합니다.

## 오늘의 연도 {#todayofyear}

날짜 또는 날짜와 시간을 연 날의 번호를 포함 UInt16 번호 (1-366)으로 변환합니다.

## 오늘 달 {#todayofmonth}

날짜 또는 날짜와 시간을 월 일 번호를 포함 UInt8 번호 (1-31)로 변환합니다.

## 오늘의 주 {#todayofweek}

Date 또는 date with time을 요일 번호를 포함 UInt8 번호로 변환합니다 (월요일은 1, 일요일은 7).

## toHour {#tohour}

시간을 가진 날짜를 24 시간 (0-23)의 시간을 포함 UInt8 숫자로 변환합니다.
This function assumes that if clocks are moved ahead, it is by one hour and occurs at 2 am, and if clocks are moved back, it is by one hour and occurs at 3 am (which is not always true - even in Moscow the clocks were twice changed at a different time).

## 토미누테 {#tominute}

시간을 가진 날짜 시간 분의 수를 포함 UInt8 번호 (0-59)로 변환합니다.

## toSecond {#tosecond}

날짜와 시간을 분 (0-59)의 초를 포함 UInt8 숫자로 변환합니다.
윤초는 고려되지 않습니다.

## toUnixTimestamp {# to-unix-timestamp}

DateTime 인수의 경우 값을 내부의 숫자 표현 (Unix 타임 스탬프)로 변환합니다.
문자열 인수의 경우 시간대에 따라 문자열에서 datetime을 분석 (옵션의 두번째 인수 서버의 시간대가 기본적으로 사용됩니다) 해당 unix 터
Date 인수의 경우 : 동작은 지정되어 있지 않습니다.

** 구문 **

```sql
toUnixTimestamp (datetime)
toUnixTimestamp (str, timezone)
```

** 반환 값 **

- Unix 타임 스탬프를 반환한다.

유형 :`UInt32`.

** 예 **

쿼리 :

```sql
SELECT toUnixTimestamp ( '2017-11-05 08:07:47', 'Asia / Tokyo') AS unix_timestamp
```

결과 :

```text
┌─unix_timestamp─┐
│ 1509836867 │
└────────────────┘
```

## 비트 시작 {#tostartofyear}

날짜 또는 날짜를 연도의 첫날 자릅니다.
날짜를 반환합니다.

## 비트 시작 휘 서 올해 {#tostartofisoyear}

날짜 또는 날짜를 iso 년 첫날 자릅니다.
날짜를 반환합니다.

## toStartOfQuarter {#tostartofquarter}

날짜 또는 분기의 첫날에 자릅니다.
분기의 첫 날은 1 월, 1 월, 1 월 또는 1 월 중 하나입니다.
날짜를 반환합니다.

## 비트 시작 몽스 {#tostartofmonth}

날짜 또는 날짜를 달 첫날에 자릅니다.
날짜를 반환합니다.

!!! attention "주의"
    잘못된 날짜를 해석하는 동작은, 구현 고유합니다. ClickHouse 제로의 날짜를 반환하거나 예외를 던지거나 "natural"오버플로

## 토몬데이 {#tomonday}

날짜 또는 날짜를 시간과 함께 가장 가까운 월요일에 자릅니다.
날짜를 반환합니다.

## 비트 시작 수수료 크 (t \ [모드 \]) {#tostartofweektmode}

날짜 또는 시간의 날짜를 가장 가까운 일요일 또는 월요일에 모드로 자릅니다.
날짜를 반환합니다.
Mode 인수는 toWeek ()의 ​​mode 인수와 동일하게 작동합니다. 단일 인수 구문은 모드 값 0이 사용됩니다.

## 비트 스타트 프 데이 {#tostartofday}

날짜를 시간과 함께 하루의 시작까지 자릅니다.

## 비트 시작 바보 {#tostartofhour}

날짜와 시간을 시간의 시작까지 자릅니다.

## 비트 시작 후미 뉴트 {#tostartofminute}

날짜와 시간을 분 시작까지 자릅니다.

## 비트 시작 사무실 부미 뉴트 {#tostartoffiveminute}

날짜와 시간을 자릅니다.

## toStartOfTenMinutes {#tostartoftenminutes}

날짜와 시간을 버리고 충분한 간격의 시작까지 자릅니다.

## 비트 시작 피프 텐 미 뉴트 {#tostartoffifteenminutes}

날짜를 시간과 함께 버리고 충분한 간격의 시작까지 자릅니다.

## 비트 스타트 오프 간격 (time_or_data 구간 x 단위 \, time_zone \]) {# tostartofintervaltime-or-data-interval-x-unit-time-zone}

이것은 명명 된 다른 함수의 일반화입니다`toStartOf *`예
`toStartOfInterval (t, INTERVAL 1 year)`와 같은을 반환합니다`toStartOfYear (t)`
`toStartOfInterval (t, INTERVAL 1 month)`와 같은을 반환합니다`toStartOfMonth (t)`
`toStartOfInterval (t, INTERVAL 1 day)`와 같은을 반환합니다`toStartOfDay (t)`
`toStartOfInterval (t, INTERVAL 15 minute)`와 같은을 반환합니다`toStartOfFifteenMinutes (t)`등.

## 토타이무 {#totime}

시간을 유지하면서 시간을 가진 날짜를 특정 고정 날짜로 변환합니다.

## toRelativeYearNum {#torelativeyearnum}

시간 또는 날짜가 날짜를 과거의 특정 고정 지점에서 시작되는 연도의 번호로 변환합니다.

## toRelativeQuarterNum {#torelativequarternum}

시간 또는 날짜가 날짜를 과거의 특정 고정 지점에서 시작하는 분기의 숫자로 변환합니다.

## 트레 라 티브 몽스 눔 {#torelativemonthnum}

시간 또는 날짜가 날짜를 과거의 특정 고정 지점에서 시작되는 달의 번호로 변환합니다.

## toRelativeWeekNum {#torelativeweeknum}

시간 또는 날짜가 날짜를 과거의 특정 고정 지점에서 시작 주 번호로 변환합니다.

## toRelativeDayNum {#torelativedaynum}

시간 또는 날짜가 날짜를 과거의 특정 고정 지점에서 시작 날짜의 숫자로 변환합니다.

## toRelativeHourNum {#torelativehournum}

시간 또는 날짜가 날짜를 과거의 특정 고정 지점에서 시작 시간 수로 변환합니다.

## 트레 라 티브 민호 텐 {#torelativeminutenum}

시간 또는 날짜가 날짜를 과거의 특정 고정 지점에서 시작하는 분의 숫자로 변환합니다.

## toRelativeSecondNum {#torelativesecondnum}

시간 또는 날짜가 날짜를 과거의 특정 고정 지점에서 시작 초에 변환합니다.

## toISOYear {#toisoyear}

Date 또는 date with time을 ISO 년 번호를 포함 UInt16 번호로 변환합니다.

## toISOWeek {#toisoweek}

날짜 또는 날짜와 시간을 ISO 주를 포함 UInt8 번호로 변환합니다.

## 토위쿠 (날짜 \ [모드 \]) {#toweekdatemode}

이 함수는 date 또는 datetime 주 번호를 반환합니다. 두 인수 형식의 toWeek ()를 사용하면 주 일요일 또는 월요일 또는 반환 값이 0에서 53 또는 1에서 53의 범위인지 여부를 지정할 수 있습니다. mode 매개 변수를 생략하면 기본 모드는 0이됩니다.
`toISOWeek ()`와 동일하게 호환 함수`toWeek (date, 3)`.
다음 표는 mode 인수의 작동 방법을 보여줍니다.

| 모드 | 주 첫날 | 범위 | Week 1 is the first week ... |
| -------- | -------------- | ------ | ------------------ ---------- |
| 0 | 일요일 | 0-53 | 올해의 일요일과 |
| 1 | 월요일 | 0-53 | 올해는 4 일 이상 |
| 2 | 일요일 | 1-53 | 올해의 일요일과 |
| 3 | 월요일 | 1-53 | 올해는 4 일 이상 |
| 4 | 일요일 | 0-53 | 올해는 4 일 이상 |
| 5 | 월요일 | 0-53 | 올해의 월요일과 |
| 6 | 일요일 | 1-53 | 올해는 4 일 이상 |
| 7 | 월요일 | 1-53 | 올해의 월요일과 |
| 8 | 일요일 | 1-53 | 1을 포함 |
| 9 | 월요일 | 1-53 | 1을 포함 |

의미있는 모드 값의 경우 "with 4 or more days this year"주 ISO8601 : 1988에 따라 번호가 매겨집니다 :

- 1 월을 포함 주가 새해에 4 일 이상 있다면, 그것은 1 주이다.

- 그렇지 않으면은 전년의 마지막 주이며, 다음 주 1 주입니다.

의미있는 모드 값의 경우 "contains January 1", 1 월을 포함 주 1 주이다. 비록 그것이 하루 만 포함되어 있어도 그 주를 포함하는 새해 일수는 문제가 없습니다.

```sql
toWeek (date [, mode] [, Timezone)
```

** 파라미터 **

-`date` - Date or DateTime.
-`mode` - Optional parameter, Range of values ​​is \ [0,9 \, default is 0.
-`Timezone` - Optional parameter, it behaves like any other conversion function.

** 예 **

```sql
SELECT toDate ( '2016-12-27') AS date, toWeek (date) AS week0, toWeek (date 1) AS week1, toWeek (date 9) AS week9;
```

```text
┌───────date─┬─week0─┬─week1─┬─week9─┐
│ 2016-12-27 │ 52 │ 52 │ 1 │
└────────────┴───────┴───────┴───────┘
```

## toYearWeek (날짜 \ [모드 \]) {#toyearweekdatemode}

날짜의 연도와 주를 반환합니다. 결과 연도는 년의 처음이자 마지막 주 date 인수 연도와 다를 수 있습니다.

Mode 인수는 toWeek ()의 ​​mode 인수와 동일하게 작동합니다. 단일 인수 구문은 모드 값 0이 사용됩니다.

`toISOYear ()`와 동일하게 호환 함수`intDiv (toYearWeek (date, 3) 100)`.

** 예 **

```sql
SELECT toDate ( '2016-12-27') AS date, toYearWeek (date) AS yearWeek0, toYearWeek (date 1) AS yearWeek1, toYearWeek (date 9) AS yearWeek9;
```

```text
┌───────date─┬─yearWeek0─┬─yearWeek1─┬─yearWeek9─┐
│ 2016-12-27 │ 201652 │ 201652 │ 201701 │
└────────────┴───────────┴───────────┴───────────┘
```

## 자 {#now}

제로의 인수를 요청 실행 중의 순간에 현재 시간을 반환합니다.
이 함수는 요청이 완료되기까지 시간이 걸린 경우에도 정수를 돌려줍니다.

## 오늘 {#today}

제로의 인수를 요청 실행 중의 순간에 현재 날짜를 반환합니다.
같은 'toDate (now ())'.

## 어제 {#yesterday}

제로의 인수를 요청 실행 중 잠시 어제의 날짜를 반환합니다.
같은 'today () - 1'.

## 시간 슬롯 {#timeslot}

시간을 절반의 시간에 반올림합니다.
이 기능은 Yandex에 고유의 것입니다. Metrica는 추적 태그가 단일 사용자 연속 된 페이지 뷰를 표시하는 경우 세션을 두 세션으로 나누어하기위한 최소 시간이기 때문에이 즉, 튜플 (태그 ID, 사용자 ID 및 타임 슬롯 )를 사용하여 해당 세션에 포함 된 페이지 뷰를 검색 할 수 있습니다.

## 토이야이무 {#toyyyymm}

날짜 또는 날짜와 시간을 해와 달의 번호 (YYYY \ * 100 + MM)를 포함 UInt32 번호로 변환합니다.

## 토이야이무 {#toyyyymmdd}

날짜 또는 날짜와 시간을 해와 달의 번호 (YYYY \ * 10000 + MM \ * 100 + DD)를 포함 UInt32 번호로 변환합니다.

## 토이야이무 {#toyyyymmddhhmmss}

날짜 또는 날짜와 시간을 해와 달의 번호 (YYYY \ * 10000000000 + MM \ * 100000000 + DD \ * 1000000 + hh \ * 10000 + mm \ * 100 + ss)를 포함 UInt64 번호로 변환합니다.

## addYears, addMonths, addWeeks, addDays, addHours, addMinutes, addSeconds, addQuarters {# addyears-addmonths-addweeks-adddays-addhours-addminutes-addseconds-addquarters}

함수는 날짜 / 날짜 시간 날짜 / 날짜 시간 간격을 추가하고 날짜 / 날짜와 시간을 반환합니다. 예를 들면 :

```sql
WITH
    toDate ( '2018-01-01') AS date,
    toDateTime ( '2018-01-01 00:00:00') AS date_time
SELECT
    addYears (date, 1) AS add_years_with_date,
    addYears (date_time 1) AS add_years_with_date_time
```

```text
┌─add_years_with_date─┬─add_years_with_date_time─┐
│ 2019-01-01 │ 2019-01-01 00:00:00 │
└─────────────────────┴──────────────────────────┘
```

## subtruttyears, subtrutmonths, subtrutweeks, subtrutdays, subtrutthours, subtrutminutes, subtrutseconds, subtrutquarters {# subtractyears-subtractmonths-subtractweeks-subtractdays-subtracthours-subtractminutes-subtractseconds-subtractquarters}

함수 Date / DateTime 간격을 Date / DateTime으로 빼서 Date / DateTime을 반환합니다. 예를 들면 :

```sql
WITH
    toDate ( '2019-01-01') AS date,
    toDateTime ( '2019-01-01 00:00:00') AS date_time
SELECT
    subtractYears (date, 1) AS subtract_years_with_date,
    subtractYears (date_time 1) AS subtract_years_with_date_time
```

```text
┌─subtract_years_with_date─┬─subtract_years_with_date_time─┐
│ 2018-01-01 │ 2018-01-01 00:00:00 │
└──────────────────────────┴────────────────────── ─────────┘
```

## dateDiff {#datediff}

Date 값 또는 DateTime 값의 차이를 반환합니다.

** 구문 **

```sql
dateDiff ( 'unit', startdate, enddate [timezone)
```

** 파라미터 **

-`unit` - Time unit, in which the returned value is expressed [문자열] (../ syntax.md # syntax-string-literal).

        Supported values ​​:

        | unit |
        | ---- |
        | second |
        | minute |
        | hour |
        | day |
        | week |
        | month |
        | quarter |
        | year |

-`startdate` - The first time value to compare [날짜] (../../ sql-reference / data-types / date.md) 또는 DateTime (../../ sql-reference / data -types / datetime.md).

-`enddate` - The second time value to compare [날짜] (../../ sql-reference / data-types / date.md) 또는 DateTime (../../ sql-reference / data -types / datetime.md).

-`timezone` - Optional parameter. If specified, it is applied to both`startdate`와`enddate`. 지정되어 있지 않은 경우,`startdate`와`enddate` 사용됩니다. 그들이 동일하지 않은 경우, 결과는 미지정입니다.

** 반환 값 **

차이`startdate`와`enddate`로 표현된다. `unit`.

유형 :`int`.

** 예 **

쿼리 :

```sql
SELECT dateDiff ( 'hour', toDateTime ( '2018-01-01 22:00:00') toDateTime ( '2018-01-02 23:00:00'));
```

결과 :

```text
┌─dateDiff ( 'hour', toDateTime ( '2018-01-01 22:00:00') toDateTime ( '2018-01-02 23:00:00')) ─┐
│ 25 │
└───────────────────────────────────────────────── ───────────────────────────────────────┘
```

## 시간 슬롯 (StartTime, Duration, \, Size \]) {# timeslotsstarttime-duration-size}

시작 시간 간격의 경우 'StartTime'그리고 계속하려면 'Duration'이이 구간의 점으로 구성된 시간 순간의 배열을 돌려줍니다. 'Size'초. 'Size'상수 UInt32에서 기본적으로 1800으로 설정됩니다.
예를 들어,`timeSlots (toDateTime ( '2012-01-01 12:20:00'), 600) = [toDateTime ( '2012-01-01 12:00:00') toDateTime ( '2012-01-01 12 : 30 : 00 ')`.
이것은 해당 세션에서 페이지 뷰를 검색하는 데 필요합니다.

## formatDateTime (시간 Format \ 시간대 \]) {#formatdatetime}

Function formats a Time according given Format string. NB : Format is a constant expression, eg you can not have multiple formats for single result column.

지원되는 형식 수정 자 :
( "Example"열에 시간 서식의 결과가 나타납니다`2018-01-02 22 : 33 : 44`)

| 수식 | 설명 | 예 |
| -------- | ---------------------------------------- --------- | ------------ |
| % C | 년을 100으로 나누어 정수로 자릅니다 (00-99) | 20 |
| % d | 날짜를, 제로팟도 (01-31) | 02 |
| % D | % M / % d / % y에 해당하는 짧은 MM / DD / YY 날짜 | 01/02/18 |
| % e | 날짜를, 공간 가득 (1-31) | 2 |
| % F | % Y- % m- % d에 해당합니다 | 2018-01-02 |
| % H | 24 시간 형식 (00-23시) | 22 |
| % I | 12 시간 형식 (01-12) | 10 |
| % j | 설날 (001-366) | 002 |
| % m | 십진수로 달 (01-12) | 01 |
| % M | 분 (00-59) | 33 |
| % n | 개행 문자 ( ") | |
| % p | AM 또는 PM 지정 | PM |
| % R | 24 시간 HH : MM 시간, % H : % M에 해당 | 22:33 |
| % S | 두 번째 (00-59) | 44 |
| % t | 가로 탭 문자 ( ') | |
| % T | ISO8601 시간 형식 (HH : MM : SS) % H : % M : % S에 해당합니다 | 22:33:44 |
| % u | ISO8601 평일 as 번호 월요일 as1 (1-7) | 2 |
| % V | ISO8601 주 번호 (01-53) | 01 |
| % w | 요일을 십진수로 일요일을 0 (0-6)합니다) | 2 |
| % y | 년 마지막 두 자리 (00-99) | 18 |
| % Y | 년 | 2018 |
| %% | % 기호 | % |

원본 기사 (https://clickhouse.tech/docs/en/query_language/functions/date_time_functions/) <! - hide ->
