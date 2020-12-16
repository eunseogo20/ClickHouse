---
machine_translated : true
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd
toc_priority : 42
toc_title : "\ u5C0F \ u6570 \ u70B9"
---

# Decimal (P, S), Decimal32 (S) Decimal64 (S) Decimal128 (S) {# decimalp-s-decimal32s-decimal64s-decimal128s}

덧셈, 뺄셈 및 곱셈 연산 동안 정밀도를 유지하는 부호있는 고정 소수점 숫자입니다. 나눗셈의 경우 최하위 자리수는 파기됩니다 (반올림하지 않음).

## 매개 변수 {#parameters}

- P- 정도. 유효 범위 : \ [1 : 38 \]. 소수 자릿수를 결정합니다 (분수 포함).
- S 스케일 유효 범위 : \ 0 : P \]. 소수 자릿수를 지정합니다.

P에 의존하는 파라미터 값 Decimal (P, S)는 :
-P from \ [1 : 9 \] - For Decimal32 (S)
-P from \ [10:18 \] - For Decimal64 (S)
-P from \ [19:38 \] - For Decimal128 (S)

## 소수점 이하의 값의 범위 {# decimal-value-ranges}

- Decimal32 (S) - (-1 \ * 10 ^ (9 - S) 1 \ * 10 ^ (9-S))
- Decimal64 (S) - (-1 \ * 10 ^ (18 - S) 1 \ * 10 ^ (18-S))
- Decimal128 (S) - (-1 \ * 10 ^ (38 - S) 1 \ * 10 ^ (38-S))

예를 들어, Decimal32 (4)는 -99999.9999에서 99999.9999까지 0.0001 단계의 숫자를 포함 할 수 있습니다.

## 내부 표현 {# internal-representation}

회사 데이터로 표현 일반적 서명 정수를 각각의 비트 폭입니다. 메모리에 저장할 수있는 실제 값의 범위는 위의 지정보다 조금 더 큰 문자열에서 변환시에만 체크됩니다.

현대의 CPU는 128 비트 정수를 기본적으로 지원하지 않기 때문에 Decimal128 연산 에뮬레이트됩니다. 따라서 Decimal128는 Decimal32 / Decimal64보다 훨씬 느리게 작동합니다.

## 조작과 결과의 종류 {# operations-and-result-type}

Decimal 이진 연산은 더 넓은 결과의 형태가됩니다 (인수의 순서는 임의입니다).

-`Decimal64 (S1) <op> Decimal32 (S2) -> Decimal64 (S)`
-`Decimal128 (S1) <op> Decimal32 (S2) -> Decimal128 (S)`
-`Decimal128 (S1) <op> Decimal64 (S2) -> Decimal128 (S)`

스케일의 규칙 :

- 덧셈, 뺄셈 : S = max (S1, S2).
- multuply : S = S1 + S2.
- 나누기 : S = S1.

Decimal 및 정수 사이의 비슷한 연산의 경우, 결과는 인수와 같은 크기의 Decimal입니다.

Decimal 및 Float32 / Float64 사이의 연산은 정의되어 있지 않습니다. 필요한 경우 toDecimal32, toDecimal64, toDecimal128 또는 toFloat32, toFloat64 내장을 사용하여 인수 중 하나를 명시 적으로 캐스팅 할 수 있습니다. 결과는 정밀도가 없어 형식 변환은 계산 비용이 많이 드는 작업임을주의하십시오.

Decimal의 일부 기능은 Float64 (var 나 stddev 등)로 결과를 반환합니다. 그러면 Float64 입력 및 Decimal 입력이 같은 값으로 다른 결과를 초래할 수 있습니다.

## 오버플로 검사 {# overflow-checks}

중간 계산은 소수, 정수로 넘쳐 가지가 일어난다. 소수의 과도한 숫자는 삭제됩니다 (반올림하지 않음). 정수 부분의 숫자가 과도하면 예외가 발생합니다.

```sql
SELECT toDecimal32 (2, 4) AS x, x / 3
```

```text
┌──────x─┬─divide (toDecimal32 (2, 4) 3) ─┐
│ 2.0000 │ 0.6666 │
└────────┴──────────────────────────────┘
```

```sql
SELECT toDecimal32 (4.2, 8) AS x x * x
```

```text
DB :: Exception : Scale is out of bounds.
```

```sql
SELECT toDecimal32 (4.2, 8) AS x 6 * x
```

```text
DB :: Exception : Decimal math overflow.
```

오버플로 검사가 업무에 감속했다. 이 알려진 수 없습니다이 비활성화 체크`decimal_check_overflow` 설정. 때 체크를 해제 오버 플로우가 발생, 결과는 올바르지 :

```sql
SET decimal_check_overflow = 0;
SELECT toDecimal32 (4.2, 8) AS x 6 * x
```

```text
┌──────────x─┬─multiply (6 toDecimal32 (4.2, 8)) ─┐
│ 4.20000000 │ -17.74967296 │
└────────────┴──────────────────────────────────┘
```

오버플로 검사가 일어날뿐만 아니라 연산 업무는 물론, 가치와의 비교 :

```sql
SELECT toDecimal32 (1 8) <100
```

```text
DB :: Exception : Can not compare.
```

원본 기사 (https://clickhouse.tech/docs/en/data_types/decimal/) <! - hide ->
