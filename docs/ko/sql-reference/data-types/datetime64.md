---
machine_translated : true
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd
toc_priority : 49
toc_title : DateTime64
---

# Datetime64 {# data_type-datetime64}

정의 된 초 이하의 정밀도에서 캘린더의 날짜와 시간으로 나타낼 수있는 인스턴트 시간에 저장할 수 있습니다

눈금의 크기 (정밀) : 10 <sup> - 정밀 </ sup> 초

구문 :

```sql
DateTime64 (precision [timezone)
```

내부적으로 데이터를 'ticks'에포크 시작 (1970-01-01 00 : 00 : 00UTC) 이후 Int64로. 눈금의 해상도는 정확도 매개 변수에 의해 결정됩니다. 또한`DateTime64` 형은 전체 열에서 같은 시간대를 저장할 수 있습니다. `DateTime64` 형의 값은 텍스트 형식으로 표시됩니다 문자열로 지정된 값이 어떻게 해석되는지 ( '2020-01-01 05 : 00 : 01.000'). 시간대는 테이블의 행 (또는 resultset)에 저장되지 않지만 열 메타 데이터에 저장됩니다. 자세한 내용은를 참조하십시오. [DateTime (datetime.md).

## 예 {#examples}

** 1. ** 테이블 만들기`DateTime64`- 문자열을 입력하고 거기에 데이터를 삽입하기 :

```sql
CREATE TABLE dt
(
    `timestamp` DateTime64 (3, 'Europe / Moscow')
    `event_id` UInt8
)
ENGINE = TinyLog
```

```sql
INSERT INTO dt Values ​​(1546300800000 1), ( '2019-01-01 00:00:00', 2)
```

```sql
SELECT * FROM dt
```

```text
┌───────────────timestamp─┬─event_id─┐
│ 2019-01-01 03 : 00 : 00.000 │ 1 │
│ 2019-01-01 00 : 00 : 00.000 │ 2 │
└─────────────────────────┴──────────┘
```

- Datetime을 정수로 삽입 할 경우 적절하게 조정 된 Unix 타임 스탬프 (UTC)로 처리됩니다. `1546300800000` (정확도 3)을 나타냅니다` '2019-01-01 00 : 00 : 00'` UTC 그러나`timestamp` 열은`Europe / Moscow` (UTC + 3) 시간대가 지정되어 경우 문자열로 출력하면 값은 다음과 같이 표시됩니다` '2019-01-01 03 : 00 : 00'`
- 문자열 값을 datetime으로 삽입하면 열 시간대에있는 것으로 간주됩니다. ` '2019-01-01 00 : 00 : 00'` 인으로 처리됩니다`Europe / Moscow` 시간대로 저장`1546290000000`.

** 2 ** 필터링`DateTime64` 값

```sql
SELECT * FROM dt WHERE timestamp = toDateTime64 ( '2019-01-01 00:00:00', 3 'Europe / Moscow')
```

```text
┌───────────────timestamp─┬─event_id─┐
│ 2019-01-01 00 : 00 : 00.000 │ 2 │
└─────────────────────────┴──────────┘
```

달리`DateTime``DateTime64` 값은 변환되지 않습니다`String` 자동으로

** 3 ** A 시간대의 취득`DateTime64`- 유형 값 :

```sql
SELECT toDateTime64 (now (), 3 'Europe / Moscow') AS column, toTypeName (column) AS x
```

```text
┌──────────────────column─┬─x────────────────────────── ────┐
│ 2019-10-16 04 : 12 : 04.000 │ DateTime64 (3, 'Europe / Moscow') │
└─────────────────────────┴─────────────────────── ─────────┘
```

** 4 ** 시간대 변환

```sql
SELECT
toDateTime64 (timestamp 3 'Europe / London') as lon_time,
toDateTime64 (timestamp 3 'Europe / Moscow') as mos_time
FROM dt
```

```text
┌───────────────lon_time──┬────────────────mos_time─┐
│ 2019-01-01 00 : 00 : 00.000 │ 2019-01-01 03 : 00 : 00.000 │
│ 2018-12-31 21 : 00 : 00.000 │ 2019-01-01 00 : 00 : 00.000 │
└─────────────────────────┴─────────────────────── ──┘
```

##도 참조. {# see-also}

- 형식 변환 함수 (../../ sql-reference / functions / type-conversion-functions.md)
- 날짜 및 시간을 조작하는 함수 (../../ sql-reference / functions / date-time-functions.md)
- [배열을 조작하기위한 함수 (../../ sql-reference / functions / array-functions.md)
- 그`date_time_input_format` 설정 (../../ operations / settings / settings.md # settings-date_time_input_format)
- 그`timezone` 서버 구성 매개 변수 (../../ operations / server-configuration-parameters / settings.md # server_configuration_parameters-timezone)
- 날짜 및 시간을 조작하는 연산자 (../../ sql-reference / operators / index.md # operators-datetime)
- [`Date` 데이터 형식 (date.md)
- [`DateTime` 데이터 형식 (datetime.md)
