---
machine_translated : true
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd
toc_priority : 51
toc_title : "\ u64EC \ u4F3C \ u4E71 \ u6570 \ u306E \ u751F \ u6210"
---

# 의사 난수를 생성하는 함수 {# functions-for-generating-pseudo-random-numbers}

의사 난수의 비 암호화 발생기가 사용된다.

모든 기능을 수용 제로의 인수 또는 하나의 인수.
인수가 전달 된 경우 어떤 형태로 할 수 있으며, 그 값은 아무것도 사용되지 않습니다.
이 인수의 유일한 목적은 동일한 함수의 두 가지 인스턴스가 다른 난수를 가진 다른 열을 반환하도록 공통 부분 식의 제거를 방지하는 것입니다.

## 랜드 {#rand}

모든 UInt32 형의 수치에 균등하게 분포하는 의사 임의의 UInt32 값을 반환합니다.
선형 합동 생성기를 사용합니다.

## rand64 {# rand64}

모든 UInt64 형의 수치에 균등하게 분포하는 의사 임의의 UInt64 값을 반환합니다.
선형 합동 생성기를 사용합니다.

## 랜드 한결 {#randconstant}

임의의 값을 가지는 정수 열을 생성합니다.

** 구문 **

```sql
randConstant ([x])
```

** 파라미터 **

-`x` - 식 (../ syntax.md # syntax-expressions) 어떤 결과 다음 해당 데이터 형식 (../ data-types / index.md # data_types) 결과 값 는 파기되지만, 식 자체가 바이 패스에 사용되는 경우는 삭제됩니다 공통 부분 식 제거 (index.md # common-subexpression-elimination) 함수가 하나의 쿼리에서 여러 번 호출 경우. 모든 매개 변수.

** 반환 값 **

- 의사 난수.

유형 : UInt32] (../ data-types / int-uint.md).

** 예 **

쿼리 :

```sql
SELECT rand (), rand (1), rand (number) randConstant () randConstant (1) randConstant (number)
FROM numbers (3)
```

결과 :

```text
┌─────rand () ─┬────rand (1) ─┬─rand (number) ─┬─randConstant () ─┬─randConstant (1) ─┬─randConstant (number) ─┐
│ 3047369878 │ 4132449925 │ 4044508545 │ 2740811946 │ 4229401477 │ 1924032898 │
│ 2938880146 │ 1267722397 │ 4154983056 │ 2740811946 │ 4229401477 │ 1924032898 │
│ 956619638 │ 4238287282 │ 1104342490 │ 2740811946 │ 4229401477 │ 1924032898 │
└────────────┴────────────┴──────────────┴──────── ────────┴─────────────────┴──────────────────────┘
```

원본 기사 (https://clickhouse.tech/docs/en/query_language/functions/random_functions/) <! - hide ->
