---
machine_translated : true
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd
toc_priority : 48
toc_title : "\ u30D3 \ u30C3 \ u30C8"
---

# 비트 함수 {# bit-functions}

비트 함수는 UInt8, UInt16, UInt32, UInt64, Int8, Int16, Int32, Int64, Float32, Float64 중 하나의 형태의 쌍에 대해서도 작동합니다.

결과 형식은 인수의 최대 비트와 동일한 비트를 가지는 정수입니다. 인수 중 적어도 하나가 서명되어있는 경우, 결과는 부호있는 번호입니다. 인수가 부동 소수점의 경우, Int64로 변환됩니다.

## 尾端 (a, b) {# bitanda-b}

## 비터 (a, b) {# bitora-b}

## bitXor (a, b) {# bitxora-b}

## bitNot (a) {#bitnota}

## 비트 시프트 레프트 (a, b) {# bitshiftlefta-b}

## 비트 시프트 라이트 (a, b) {# bitshiftrighta-b}

## 비트로 세로 레프트 (a, b) {# bitrotatelefta-b}

## 비트 로터 라이트 (a, b) {# bitrotaterighta-b}

## bitTest {#bittest}

사이의 임의의 정수로 환산하고 있습니다. [이진] (https://en.wikipedia.org/wiki/Binary_number), 지정된 위치에있는 비트의 값을 돌려줍니다. 카운트 다운은 오른쪽에서 왼쪽으로 0부터 시작합니다.

** 구문 **

```sql
SELECT bitTest (number, index)
```

** 파라미터 **

-`number` - integer number.
-`index` - position of bit.

** 반환 값 **

지정된 위치에있는 bit의 값을 돌려줍니다.

유형 :`UInt8`.

** 예 **

예를 들어, 기수 43-2 (이진수)의 수치 시스템은 101011입니다.

쿼리 :

```sql
SELECT bitTest (43, 1)
```

결과 :

```text
┌─bitTest (43, 1) ─┐
│ 1 │
└────────────────┘
```

다른 예 :

쿼리 :

```sql
SELECT bitTest (43, 2)
```

결과 :

```text
┌─bitTest (43, 2) ─┐
│ 0 │
└────────────────┘
```

## 빗테 등급 {#bittestall}

결과를 반환합니다 논리 결합 (https://en.wikipedia.org/wiki/Logical_conjunction) 지정된 위치에있는 모든 비트 (and 연산자). 카운트 다운은 오른쪽에서 왼쪽으로 0부터 시작합니다.

비트 연산을위한 결합 :

0 AND 0 = 0

0 AND 1 = 0

1 AND 0 = 0

1 AND 1 = 1

** 구문 **

```sql
SELECT bitTestAll (number, index1, index2, index3, index4 ...)
```

** 파라미터 **

-`number` - integer number.
-`index1``index2``index3``index4` - positions of bit. For example, for set of positions (`index1``index2``index3``index4`)는 모든 위치 이 사실 인 경우에만 true입니다 (`index1` ⋀`index2`, ⋀`index3` ⋀`index4`).

** 반환 값 **

논리 결합의 결과를 반환합니다.

유형 :`UInt8`.

** 예 **

예를 들어, 기수 43-2 (이진수)의 수치 시스템은 101011입니다.

쿼리 :

```sql
SELECT bitTestAll (43, 0, 1, 3, 5)
```

결과 :

```text
┌─bitTestAll (43, 0, 1, 3, 5) ─┐
│ 1 │
└────────────────────────────┘
```

다른 예 :

쿼리 :

```sql
SELECT bitTestAll (43, 0, 1, 3, 5, 2)
```

결과 :

```text
┌─bitTestAll (43, 0, 1, 3, 5, 2) ─┐
│ 0 │
└───────────────────────────────┘
```

## 빗테스타니 {#bittestany}

결과를 반환합니다 [논리합 (https://en.wikipedia.org/wiki/Logical_disjunction) 지정된 위치에있는 모든 비트 (또는 운영자). 카운트 다운은 오른쪽에서 왼쪽으로 0부터 시작합니다.

비트 연산의 분리 :

0 OR 0 = 0

0 OR 1 = 1

1 OR 0 = 1

1 OR 1 = 1

** 구문 **

```sql
SELECT bitTestAny (number, index1, index2, index3, index4 ...)
```

** 파라미터 **

-`number` - integer number.
-`index1``index2``index3``index4` - positions of bit.

** 반환 값 **

논리 해석의 결과를 반환합니다.

유형 :`UInt8`.

** 예 **

예를 들어, 기수 43-2 (이진수)의 수치 시스템은 101011입니다.

쿼리 :

```sql
SELECT bitTestAny (43, 0, 2)
```

결과 :

```text
┌─bitTestAny (43, 0, 2) ─┐
│ 1 │
└──────────────────────┘
```

다른 예 :

쿼리 :

```sql
SELECT bitTestAny (43, 4, 2)
```

결과 :

```text
┌─bitTestAny (43, 4, 2) ─┐
│ 0 │
└──────────────────────┘
```

## 비트 {#bitcount}

숫자 이진 표현 하나에 설정된 비트 수를 계산합니다.

** 구문 **

```sql
bitCount (x)
```

** 파라미터 **

-`x` - 정수 (../../ sql-reference / data-types / int-uint.md) 또는 부동 소수점 (../../ sql-reference / data-types / float.md) 번호이 함수는 메모리의 값 표현을 사용합니다. 부동 소수점을 지원합니다.

** 반환 값 **

- 입력 번호 중 하나에 설정된 비트 수.

이 함수는 입력 값을 더 큰 형식으로 변환하지 않습니다 (부호 확장 (https://en.wikipedia.org/wiki/Sign_extension)). 예를 들어,`bitCount (toUInt8 (-1)) = 8` .

유형 :`UInt8`.

** 예 **

예를 들어, 숫자 333를 취한다. 이진 표현 : 00000000101001101.

쿼리 :

```sql
SELECT bitCount (333)
```

결과 :

```text
┌─bitCount (333) ─┐
│ 5 │
└───────────────┘
```

원본 기사 (https://clickhouse.tech/docs/en/query_language/functions/bit_functions/) <! - hide ->
