---
machine_translated : true
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd
toc_priority : 41
toc_title : "Float32 \ u3001Float64"
---

# Float32, Float64 {# float32-float64}

[부동 소수점 (https://en.wikipedia.org/wiki/IEEE_754).

형식은 C 타입과 동일합니다 :

-`Float32` -`float`
-`Float64` -`double`

가능한 정수 형식으로 데이터를 저장하는 것이 좋습니다. 예를 들어, 고정 정밀도의 수치를 금액이나 페이지의 로딩 시간 등의 정수 값에 밀리 초 단위로 변환합니다.

## 부동 소수점 숫자의 사용 {# using-floating-point-numbers}

- 부동 소수점을 사용하여 계산에서 반올림 오류가 발생할 수 있습니다.

<! - ->

```sql
SELECT 1 - 0.9
```

```text
┌───────minus (1, 0.9) ─┐
│ 0.09999999999999998 │
└─────────────────────┘
```

- 계산 결과는 계산 방법 (컴퓨터 시스템의 프로세서 유형 및 아키텍처)에 의존한다.
- 부동 소수점 계산은 무한대 등의 수치가 생성 될 수 있습니다 (`Inf`)과 "not-a-number"(`NaN`). 이것은 계산 결과를 처리 할 때 고려해야 수 있습니다.
- 텍스트에서 부동 소수점 숫자를 분석 할 경우 결과가 가장 가까운 시스템 표현 가능한 숫자가 아닐 수 있습니다.

## NaN 및 Inf {# data_type-float-nan-inf}

표준 SQL과는 대조적으로, ClickHouse는 부동 소수점 아래의 카테고리를 지원하고 있습니다 :

-`Inf` - Infinity.

<! - ->

```sql
SELECT 0.5 / 0
```

```text
┌─divide (0.5, 0) ─┐
│ inf │
└────────────────┘
```

-`-Inf` - Negative infinity.

<! - ->

```sql
SELECT -0.5 / 0
```

```text
┌─divide (-0.5, 0) ─┐
│ -inf │
└─────────────────┘
```

-`NaN` - Not a number.

<! - ->

```sql
SELECT 0 / 0
```

```text
┌─divide (0, 0) ─┐
│ nan │
└──────────────┘
```

    See the rules for`NaN` sorting in the section [ORDER BY clause] (../ sql_reference / statements / select / order-by.md).

원본 기사 (https://clickhouse.tech/docs/en/data_types/float/) <! - hide ->
