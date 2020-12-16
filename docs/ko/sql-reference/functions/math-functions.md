---
machine_translated : true
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd
toc_priority : 44
toc_title : "\ u6570 \ u5B66"
---

# 수학 함수 {# mathematical-functions}

모든 함수는 Float64 번호를 반환합니다. 결과의 정확도는 가능한 최대 정밀도에 가까운이지만, 결과는 해당 실수에 가장 가까운 기계 표현할 수있는 수와 일치하지 않을 수 있습니다.

## e () {#e}

수치 e에 가까운 Float64 번호를 반환합니다.

## pi () {#pi}

Returns a Float64 number that is close to the number π.

## exp (x) {#expx}

수치 인수를 인수 지수에 가까운 Float64 번호를 반환합니다.

## 로그 (x), ln (x) {# logx-lnx}

수치 인수를 받아, 인수의 자연 로그에 가까운 Float64 수를 돌려줍니다.

## exp2 (x) {# exp2x}

수치 인수를 받아, Float64를 x의 제곱에 가까운 2에 가까운 수치를 돌려줍니다.

## log2 (x) {# log2x}

수치 인수를 인수 바이너리 로그에 가까운 Float64 값을 반환합니다.

## exp10 (x) {# exp10x}

수치 인수를 받아, Float64의 x의 제곱에 가까운 10의 수치를 돌려줍니다.

## log10 (x) {# log10x}

수치 인수를 인수 소수 대수에 가까운 Float64 값을 반환합니다.

## sqrt (x) {#sqrtx}

수치 인수를 받아, 인수의 제곱근에 가까운 Float64 번호를 반환합니다.

## cbrt (x) {#cbrtx}

수치 인수를 인수 입방 뿌리에 가까운 Float64 번호를 반환합니다.

## erf (x) {#erfx}

만약 'x'부가 아닌 경우`erf (x / σ√2)`확률 변수가 표준 편차를 가진 정규 분포를 갖는 확률 'σ'더 기대치에서 분리 된 값을 취합니다 'x' .

예 (세 시그마 법칙) :

```sql
SELECT erf (3 / sqrt (2))
```

```text
┌─erf (divide (3 sqrt (2))) ─┐
│ 0.9973002039367398 │
└─────────────────────────┘
```

## erfc (x) {#erfcx}

수치 인수를 받아, Float64의 1-erf (x)에 가까운 수치를 돌려줍니다. 'x'값.

## lgamma (x) {#lgammax}

감마 함수의 로그.

## tgamma (x) {#tgammax}

감마 함수

## sin (x) {#sinx}

사인.

## cos (x) {#cosx}

코사인

## 탄 (x) {#tanx}

접선.

## asin (x) {#asinx}

아크 사인.

## acos (x) {#acosx}

아크 코사인.

## 아탄 (x) {#atanx}

호 탄젠트.

## pow (x, y), power (x, y) {# powx-y-powerx-y}

Y의 제곱에 가까운 Float64의 값을 돌려줍니다.

## intExp2 {# intexp2}

수치 인수를 받아, x의 제곱에 가까운 UInt64 값을 반환합니다.

## intExp10 {# intexp10}

수치 인수를 받아, x의 제곱에 10에 가까운 UInt64 숫자를 반환합니다.

원본 기사 (https://clickhouse.tech/docs/en/query_language/functions/math_functions/) <! - hide ->
