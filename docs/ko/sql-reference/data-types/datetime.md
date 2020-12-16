---
machine_translated : true
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd
toc_priority : 48
toc_title : DateTime
---

# Datetime {# data_type-datetime}

달력의 날짜와 하루의 시간으로 표현 될 수 시간 내에 인스턴트을 저장할 수 있습니다.

구문 :

```sql
DateTime (timezone)
```

지원되는 값의 범위 : \ [1970-01-01 00:00:00, 2105-12-31 23 : 59 : 59 \].

해상도 : 1 초.

## 사용상의주의 {# usage-remarks}

시간의 포인트는 a로 저장됩니다 [Unix 타임 스탬프 (https://en.wikipedia.org/wiki/Unix_time) 시간대 또는 일광 절약 시간에 관계없이. 또한`DateTime` 형은 전체 열에서 같은 시간대를 저장할 수 있습니다. `DateTime` 형의 값은 텍스트 형식으로 표시됩니다 문자열로 지정된 값이 어떻게 해석되는지 ( '2020-01-01 05:00:01'). 시간대는 테이블의 행 (또는 resultset)에 저장되지 않지만 열 메타 데이터에 저장됩니다.
지원되는 시간대 목록은 [IANA 시간대 데이터베이스 (https://www.iana.org/time-zones).
그`tzdata` 패키지 포함 [IANA 시간대 데이터베이스 (https://www.iana.org/time-zones) 시스템에 설치되어 있어야합니다. 사용하는`timedatectl list-timezones` 로컬 시스템이 알려진 시간대를 나열하는 명령입니다.

시간대를 명시 적으로 설정 할 수 있습니다`DateTime`- 테이블을 만들 때 열을 입력합니다. 시간대가 설정되어 있지 않은 경우, ClickHouse는 시간대 (../../ operations / server-configuration-parameters / settings.md # server_configuration_parameters-timezone) ClickHouse 서버 시작시 서버 설정 또는 운영 체제 설정 매개 변수.

그 [clickhouse- 클라이언트 (../../ interfaces / cli.md) 데이터 형의 초기화시 시간대가 명시 적으로 설정되어 있지 않으면 기본적으로 서버 시간대를 적용합니다. 클라이언트의 시간대를 사용하려면`clickhouse-client`와`--use_client_time_zone` 매개 변수.

ClickHouse 다음의 값을 출력합니다`YYYY-MM-DD hh : mm : ss` 기본 텍스트 형식. 출력을 변경하려면 formatDateTime (../../ sql-reference / functions / date-time-functions.md # formatdatetime) 기능.

ClickHouse에 데이터를 삽입 할 때 데이터의 값에 따라 날짜와 시간 문자열의 다른 형식을 사용할 수 있습니다. [date_time_input_format (../../ operations / settings / settings.md # settings-date_time_input_format) 설정.

## 예 {#examples}

** 1. ** 테이블 만들기`DateTime`- 문자열을 입력하고 거기에 데이터를 삽입하기 :

```sql
CREATE TABLE dt
(
    `timestamp` DateTime ( 'Europe / Moscow')
    `event_id` UInt8
)
ENGINE = TinyLog;
```

```sql
INSERT INTO dt Values ​​(1546300800 1), ( '2019-01-01 00:00:00', 2);
```

```sql
SELECT * FROM dt;
```

```text
┌───────────timestamp─┬─event_id─┐
│ 2019-01-01 03:00:00 │ 1 │
│ 2019-01-01 00:00:00 │ 2 │
└─────────────────────┴──────────┘
```

- Datetime을 정수로 삽입 할 경우, Unix Timestamp (UTC)로 처리됩니다. `1546300800`을 나타내는` '2019-01-01 00 : 00 : 00'` UTC 그러나`timestamp` 열은`Europe / Moscow` (UTC + 3) 시간대가 지정되어있는 경우 문자열로 출력 그러면 값은 다음과 같이 표시됩니다` '2019-01-01 03 : 00 : 00'`
- 문자열 값을 datetime으로 삽입하면 열 시간대에있는 것으로 간주됩니다. ` '2019-01-01 00 : 00 : 00'` 인으로 처리됩니다`Europe / Moscow` 시간대로 저장`1546290000`.

** 2 ** 필터링`DateTime` 값

```sql
SELECT * FROM dt WHERE timestamp = toDateTime ( '2019-01-01 00:00:00', 'Europe / Moscow')
```

```text
┌───────────timestamp─┬─event_id─┐
│ 2019-01-01 00:00:00 │ 2 │
└─────────────────────┴──────────┘
```

`DateTime` 열의 값은 문자열 값을 사용하여 필터링 할 수 있습니다. `WHERE` 단정하다. 로 변환됩니다`DateTime` 자동으로 :

```sql
SELECT * FROM dt WHERE timestamp = '2019-01-01 00:00:00'
```

```text
┌───────────timestamp─┬─event_id─┐
│ 2019-01-01 03:00:00 │ 1 │
└─────────────────────┴──────────┘
```

** 3 ** A 시간대의 취득`DateTime`- 유형 열 :

```sql
SELECT toDateTime (now (), 'Europe / Moscow') AS column, toTypeName (column) AS x
```

```text
┌──────────────column─┬─x─────────────────────────┐
│ 2019-10-16 04:12:04 │ DateTime ( 'Europe / Moscow') │
└─────────────────────┴─────────────────────────── ┘
```

** 4 ** 시간대 변환

```sql
SELECT
toDateTime (timestamp, 'Europe / London') as lon_time,
toDateTime (timestamp, 'Europe / Moscow') as mos_time
FROM dt
```

```text
┌───────────lon_time──┬────────────mos_time─┐
│ 2019-01-01 00:00:00 │ 2019-01-01 03:00:00 │
│ 2018-12-31 21:00:00 │ 2019-01-01 00:00:00 │
└─────────────────────┴─────────────────────┘
```

##도 참조. {# see-also}

- 형식 변환 함수 (../../ sql-reference / functions / type-conversion-functions.md)
- 날짜 및 시간을 조작하는 함수 (../../ sql-reference / functions / date-time-functions.md)
- [배열을 조작하기위한 함수 (../../ sql-reference / functions / array-functions.md)
- 그`date_time_input_format` 설정 (../../ operations / settings / settings.md # settings-date_time_input_format)
- 그`timezone` 서버 구성 매개 변수 (../../ operations / server-configuration-parameters / settings.md # server_configuration_parameters-timezone)
- 날짜 및 시간을 조작하는 연산자 (../../ sql-reference / operators / index.md # operators-datetime)
- 그`Date` 데이터 형식 (date.md)

원본 기사 (https://clickhouse.tech/docs/en/data_types/datetime/) <! - hide ->
