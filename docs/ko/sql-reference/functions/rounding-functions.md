---
machine_translated : true
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd
toc_priority : 45
toc_title : "\ u4E38 \ u3081"
---

# 반올림 함수 {# rounding-functions}

## 층 (x \, N \]) {# floorx-n}

다음의 최대 라운드 수를 돌려줍니다`x` 라운드 수는 1 / 10N의 배수 또는 1 / 10N이 정확하지 않은 경우 적절한 데이터 형식의 가장 가까운 수치입니다.
'N'정수, 선택적 매개 변수입니다. 이것은 정수로 반올림을 의미합니다.
'N'부의 경우가 있습니다.

예 :`floor (123.45, 1) = 123.4, floor (123.45 -1) = 120.`

`x` 임의의 숫자입니다. 결과는 같은 형태의 숫자입니다.
정수 인수의 경우, 음수 마는 말이 있습니다`N` 값 (음수가 아닌 경우`N` 함수는 아무것도하지 않습니다).
반올림에 의해 오버플로가 발생하는 경우 (예를 들어, floor (-128 -1)) 구현 고유의 결과가 반환됩니다.

## ceil (x \, N \), ceiling (x \, N \]) {# ceilx-n-ceilingx-n}

이상의 작은 둥근 수를 반환합니다`x` 다른 모든 방법은 그것과 동일합니다`floor` 함수 (위 참조).

## trunc (x \, N \), truncate (x \, N \]) {# truncx-n-truncatex-n}

절대치가 다음의 최대 절대 값을 가진 둥근 수를 반환합니다`x` 's. In every other way, it is the same as the 'floor'함수 (위 참조).

## round (x \, N \]) {# rounding_functions-round}

지정된 소수 자릿수로 반올림합니다.

이 함수는 지정된 순서에 가장 가까운 번호를 반환합니다. 지정된 수치가 주위의 숫자에 해당하는 거리를 가지는 경우,이 함수는 부동 소수점 형에 벙커의 반올림을 사용하여 다른 숫자 형식에 대해서는 처음부터

```sql
round (expression [, decimal_places)
```

** 파라미터 : **

-`expression` - A number to be rounded. Can be any 식 (../ syntax.md # syntax-expressions) 값을 반환 데이터 형식 (../../ sql-reference / data-types /index.md#data_types).
-`decimal-places` - An integer value.
    - 만약`decimal-places> 0` 다음 함수는 값을 소수점 오른쪽에 반올림합니다.
    - 만약`decimal-places <0` 다음이 함수는 값을 소수점 왼쪽으로 반올림합니다.
    - 만약`decimal-places = 0` 다음이 함수는 값을 정수로 반올림합니다. 이 경우 인수는 생략 할 수 있습니다.

** 반환 값 : **

입력 번호와 동일한 유형의 반올림 한 숫자.

### 예 {#examples}

** 예 **

```sql
SELECT number / 2 AS x round (x) FROM system.numbers LIMIT 3
```

```text
┌───x─┬─round (divide (number 2)) ─┐
│ 0 │ 0 │
│ 0.5 │ 0 │
│ 1 │ 1 │
└─────┴──────────────────────────┘
```

** 반올림 예 **

가장 가까운 수로 반올림합니다.

```text
round (3.2, 0) = 3
round (4.1267, 2) = 4.13
round (22, -1) = 20
round (467, -2) = 500
round (-467 -2) = -500
```

벙커 반올림.

```text
round (3.5) = 4
round (4.5) = 4
round (3.55, 1) = 3.6
round (3.65, 1) = 3.6
```

** 참고하십시오. **

- [라운드 벙커 (# roundbankers)

## 라운드 벙커 {#roundbankers}

숫자를 지정한 소수점 자리로 반올림합니다.

- 반올림 수가 두 숫자 사이에있는 경우, 함수는 은행가의 반올림을 사용합니다.

        Banker 's rounding is a method of rounding fractional numbers. When the rounding number is halfway between two numbers, it 's rounded to the nearest even digit at the specified decimal position. For example : 3.5 rounds up to 4, 2.5 rounds down to 2.

        It 's the default rounding method for floating point numbers defined in [IEEE 754] (https://en.wikipedia.org/wiki/IEEE_754#Roundings_to_nearest). The [round (# rounding_functions-round) function performs the same rounding for floating point numbers. The`roundBankers` function also rounds integers the same way, for example,`roundBankers (45, -1) = 40`.

- 그렇지 않으면 함수는 숫자를 가장 가까운 정수로 반올림합니다.

은행가의 반올림을 사용하면 반올림 수치가 이러한 수치의 합계 또는 감산 결과에 미치는 영향을 줄일 수 있습니다.

예를 들어, 반올림이 다른 합계 1.5,2.5,3.5,4.5 등입니다 :

- 반올림 없슴 : 1.5 + 2.5 + 3.5 + 4.5 = 12.
- 벙커 반올림 : 2 ​​+ 2 + 4 + 4 = 12.
- 가장 가까운 정수로 반올림 : 2 ​​+ 3 + 4 + 5 = 14.

** 구문 **

```sql
roundBankers (expression [, decimal_places)
```

** 파라미터 **

-`expression` - A number to be rounded. Can be any 식 (../ syntax.md # syntax-expressions) 값을 반환 데이터 형식 (../../ sql-reference / data-types /index.md#data_types).
-`decimal-places` - Decimal places. An integer number.
    -`decimal-places> 0` - The function rounds the number to the given position right of the decimal point. Example :`roundBankers (3.55, 1) = 3.6`.
    -`decimal-places <0` - The function rounds the number to the given position left of the decimal point. Example :`roundBankers (24.55 -1) = 20`.
    -`decimal-places = 0` - The function rounds the number to an integer. In this case the argument can be omitted. Example :`roundBankers (2.5) = 2`.

** 반환 값 **

벙커의 둥근 법에 의해 반올림 한 값.

### 예 {# examples-1}

** 예 **

쿼리 :

```sql
 SELECT number / 2 AS x, roundBankers (x, 0) AS b fROM system.numbers limit 10
```

결과 :

```text
┌───x─┬─b─┐
│ 0 │ 0 │
│ 0.5 │ 0 │
│ 1 │ 1 │
│ 1.5 │ 2 │
│ 2 │ 2 │
│ 2.5 │ 2 │
│ 3 │ 3 │
│ 3.5 │ 4 │
│ 4 │ 4 │
│ 4.5 │ 4 │
└─────┴───┘
```

** 은행의 반올림 예 **

```text
roundBankers (0.4) = 0
roundBankers (-3.5) = -4
roundBankers (4.5) = 4
roundBankers (3.55, 1) = 3.6
roundBankers (3.65, 1) = 3.6
roundBankers (10.35, 1) = 10.4
roundBankers (10.755, 2) = 11,76
```

** 참고하십시오. **

- [원] (# rounding_functions-round)

## roundToExp2 (num) {# roundtoexp2num}

번호를 받아들입니다. 숫자가 하나보다 작은 경우는 0을 돌려줍니다. 그렇지 않으면 숫자를 가장 가까운 곳 (부가 아닌 전체) 차수까지 자릅니다.

## roundDuration (num) {#rounddurationnum}

번호를 받아들입니다. 숫자가 하나보다 작은 경우는 0을 돌려줍니다. 그렇지 않으면 숫자를 세트 수치에 자릅니다 : 1, 10, 30, 60, 120, 180, 240, 300, 600, 1200, 1800, 3600, 7200, 18000, 36000.이 기능은 Yandex에 고유의 것입니다. Metrica와 세션의 길이에 대한 보고서의 구현에 사용됩니다.

## roundAge (num) {#roundagenum}

번호를 받아들입니다. 수치가 18 미만의 경우는 0을 돌려줍니다. 그렇지 않으면 숫자를 세트 수치에 자릅니다 : 18, 25, 35, 45, 55.이 기능은 Yandex에 고유의 것입니다. 사용자 연령에 대한 보고서를 구현하는 데 사용됩니다.

## 라운드 다운 (num, arr) {# rounddownnum-arr}

수치를 받고 지정된 배열의 요소에 자릅니다. 값이 최소한 계보다 작은 경우는 최소한 세계가 반환됩니다.

원본 기사 (https://clickhouse.tech/docs/en/query_language/functions/rounding_functions/) <! - hide ->
