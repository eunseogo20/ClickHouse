---
machine_translated : true
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd
toc_priority : 52
toc_title : "\ u30A8 \ u30F3 \ u30B3 \ u30FC \ u30C9"
---

# 인코딩 기능 {# encoding-functions}

## char {#char}

전달 된 인수의 개수로 길이의 스트링을 돌려 각 바이트는 대응하는 인수의 값을 갖습니다. 수치 형의 복수의 인수를받습니다. 인수의 값이 UInt8 데이터 타입의 범위이면 반올림 및 오버플로 가능성이있다 UInt8로 변환됩니다.

** 구문 **

```sql
char (number_1, number_2, ..., number_n]);
```

** 파라미터 **

-`number_1, number_2, ..., number_n` - Numerical arguments interpreted as integers. Types : [Int] (../../ sql-reference / data-types / int-uint.md), 플로트 ( ../../sql-reference/data-types/float.md).

** 반환 값 **

- 지정된 바이트 문자열.

유형 :`String`.

** 예 **

쿼리 :

```sql
SELECT char (104.1, 101, 108.9, 108.9, 111) AS hello
```

결과 :

```text
┌─hello─┐
│ hello │
└───────┘
```

를 구축 할 수 있습니다 문자열의 인코딩에 대응하는 바이트까지입니다. UTF-8의 예는 다음과 같습니다 :

쿼리 :

```sql
SELECT char (0xD0, 0xBF, 0xD1, 0x80, 0xD0, 0xB8, 0xD0, 0xB2, 0xD0, 0xB5, 0xD1, 0x82) AS hello;
```

결과 :

```text
┌─hello──┐
│ привет │
└────────┘
```

쿼리 :

```sql
SELECT char (0xE4, 0xBD, 0xA0, 0xE5, 0xA5, 0xBD) AS hello;
```

결과 :

```text
┌─hello─┐
│ 你好 │
└───────┘
```

## hex {#hex}

인수의 16 진 표현을 포함한 문자열을 반환합니다.

** 구문 **

```sql
hex (arg)
```

함수는 대문자를 사용하고 있습니다`AF` 그리고 접두사를 사용하지 않는 (같은`0x`) 또는 접미사 (같은`h`).

정수 인수의 경우는 여섯 자리를 출력합니다 ( "nibbles") 최상위에서 최하위로 (빅 엔디안 또는 "human readable"순서). 이것은 최상위 비 제로 바이트 (선두의 제로 바이트는 생략합니다)에서 시작하지만, 첫 번째 숫자가 0이도 항상 모든 바이트의 두 자릿수를

예 :

** 예 **

쿼리 :

```sql
SELECT hex (1);
```

결과 :

```text
01
```

형태의 값`Date`와`DateTime` 해당하는 정수 (Date의 경우 신기원으로부터의 일수, DateTime의 경우 Unix Timestamp 값)로 포맷됩니다.

때문에`String`와`FixedString` 모든 바이트는 단순히 이진수로 부호화된다. 0 바이트는 생략되지 않습니다.

부동 소수점 형과 Decimal 형의 값은 메모리의 표현으로 인코딩됩니다. 지원에서도 조금 엔디, 건축, 그 부호화 된 때문에 조금 엔디입니다. ※ 맨 / 후행 0 바이트는 생략되지 않습니다.

** 파라미터 **

-`arg` - A value to convert to hexadecimal. Types : [문자열] (../../ sql-reference / data-types / string.md), UInt (../../ sql- reference / data-types / int-uint.md), 플로트 (../../ sql-reference / data-types / float.md), 소수 (../../ sql-reference / data-types / decimal.md), 날짜 (../../ sql-reference / data-types / date.md) 또는 DateTime (../../ sql-reference / data-types / datetime.md).

** 반환 값 **

- 인수의 16 진수 표현을 가지는 문자열.

유형 :`String`.

** 예 **

쿼리 :

```sql
SELECT hex (toFloat32 (number)) as hex_presentation FROM numbers (15, 2);
```

결과 :

```text
┌─hex_presentation─┐
│ 00007041 │
│ 00008041 │
└──────────────────┘
```

쿼리 :

```sql
SELECT hex (toFloat64 (number)) as hex_presentation FROM numbers (15, 2);
```

결과 :

```text
┌─hex_presentation─┐
│ 0000000000002E40 │
│ 0000000000003040 │
└──────────────────┘
```

## unhex (str) {#unhexstr}

임의의 수의 16 진수가 포함 된 문자열을 받고 해당 바이트를 포함하는 문자열을 반환합니다. 대문자와 소문자를 모두 지원하고 있습니다 aF 진행의 자리수는 짝수 여야는 없습니다. 홀수의 경우 마지막 자리는 00-0F 바이트의 최하위 반으로 해석됩니다. 인수 문자열에 열 여섯 진수 이외의 자릿수가 포함되어있는 경우, 구현 정의의 결과를 반환합니다 (예외는 throw되지 않습니다).
결과를 숫자로 변환하려면 'reverse'와 'reinterpretAsType'기능.

## UUIDStringToNum (str) {#uuidstringtonumstr}

형식으로 36 문자가 포함 된 문자열을 받아들입니다`123e4567-e89b-12d3-a456-426655440000`이를 FixedString (16)의 바이트 집합으로 반환한다.

## UUIDNumToString (str) {#uuidnumtostringstr}

FixedString (16) 값을받습니다. 36 문자가 포함 된 문자열을 텍스트 형식으로 반환합니다.

## 비트 마스크 트 목록 (num) {#bitmasktolistnum}

정수를받습니다. 소스 번호를 합계하는 두 제곱의 목록을 포함하는 문자열을 반환합니다. 이들은 텍스트 공백없이 오름차순으로 쉼표로 구분합니다.

## 비트 마스크 비트 어레이 (num) {#bitmasktoarraynum}

정수를받습니다. 총 된 때 소스 번호를 합계하는 두 지수의 목록을 포함 UInt64 값의 배열을 돌려줍니다. 배열의 값은 오름차순입니다.

원본 기사 (https://clickhouse.tech/docs/en/query_language/functions/encoding_functions/) <! - hide ->
