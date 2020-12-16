---
machine_translated : true
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd
toc_priority : 35
toc_title : "\ u7B97 \ u8853"
---

# 산술 함수 {# arithmetic-functions}

모든 산술 함수 결과의 형태는 그러한 형태가있는 경우, 결과가 들어간다 최소의 숫자로 계산됩니다. 최소 비트 수, 서명 여부 및 부동 소수점 숫자에 따라 동시에 취득됩니다. 충분한 비트가없는 경우에는 가장 높은 비트 타입을 가지고 있습니다.

예 :

```sql
SELECT toTypeName (0) toTypeName (0 + 0) toTypeName (0 + 0 + 0) toTypeName (0 + 0 + 0 + 0)
```

```text
┌─toTypeName (0) ─┬─toTypeName (plus (0, 0)) ─┬─toTypeName (plus (plus (0, 0), 0)) ─┬─toTypeName (plus (plus (plus (0, 0) 0), 0)) ─┐
│ UInt8 │ UInt16 │ UInt32 │ UInt64 │
└───────────────┴────────────────────────┴──────── ─────────────────────────┴──────────────────────── ──────────────────┘
```

산술 함수는 UInt8, UInt16, UInt32, UInt64, Int8, Int16, Int32, Int64, Float32, Float64 중 하나의 형태의 쌍에 대해서도 작동합니다.

오버 플로우는 C ++과 같은 방법으로 생성됩니다.

## 플러스 (a, b) a + b 연산자 {# plusa-bab-operator}

수치의 합계를 계산합니다.
또한 날짜 또는 날짜와 시간을 가지는 정수를 추가 할 수 있습니다. 날짜의 경우 정수를 추가하는 것은 해당 일자를 추가하는 것을 의미합니다. 날짜와 시간의 경우 해당 초를 합산을 의미합니다.

## 마이너스 (a, b), ab 연산자 {# minusa-bab-operator}

차이를 계산합니다. 결과는 항상 서명됩니다.

You can also calculate integer numbers from a date or date with time. The idea is the same - see above for 'plus'.

## 곱하기 (a, b) a \ * b 연산자 {# multiplya-bab-operator}

숫자를 곱합니다.

## 나누기 (a, b) a / b 연산자 {# dividea-bab-operator}

수치의 몫을 계산합니다. 결과 형식은 항상 부동 소수점 형식입니다.
정수 나누기는 없습니다. 정수 나누기의 경우는 'intDiv'기능.
제로로 나누면 'inf', '-inf'또는 'nan'.

## intDiv (a, b) {# intdiva-b}

수치의 몫을 계산합니다. 정수로 분할하고 (절대 값) 반올림합니다.
예외는 0으로 나누기 할 때, 또는 최소 음수를 마이너스로 나누면 때에 슬로우됩니다.

## intDivOrZero (a, b) {# intdivorzeroa-b}

과는 다른 'intDiv'제로 나누기 할 때, 또는 최소 음수를 마이너스로 나누면 때 제로를 돌려줍니다.

## modulo (a, b) a % b 연산자 {# moduloa-bab-operator}

나누기 후 나머지를 계산합니다.
인수가 부동 소수점 숫자의 경우 소수점 부분을 제거하여 정수로 미리 변환됩니다.
나머지는 C ++와 같은 의미로 가져옵니다. 잘라 나누기는 음수로 사용됩니다.
예외는 0으로 나누기 할 때, 또는 최소 음수를 마이너스로 나누면 때에 슬로우됩니다.

## moduloOrZero (a, b) {# moduloorzeroa-b}

과는 다른 'modulo'제수가 0 인 경우에 0을 반환한다는 점에서.

## 부정 (a) - 연산자 {# negatea-a-operator}

반대 부호로 값을 계산합니다. 결과는 항상 서명됩니다.

## abs (a) {# arithm_func-abs}

수치 (a)의 절대 값을 산출한다. 즉, a \ <0의 경우 -a를 돌려줍니다. 부호있는 정수의 경우, 부호없는 숫자를 반환합니다.

## gcd (a, b) {# gcda-b}

수치의 최대 공약수를 반환합니다.
예외는 0으로 나누기 할 때, 또는 최소 음수를 마이너스로 나누면 때에 슬로우됩니다.

## lcm (a, b) {# lcma-b}

수치의 최소 공배수를 반환합니다.
예외는 0으로 나누기 할 때, 또는 최소 음수를 마이너스로 나누면 때에 슬로우됩니다.

원본 기사 (https://clickhouse.tech/docs/en/query_language/functions/arithmetic_functions/) <! - hide ->
