--- 
machine_translated : true 
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd 
toc_priority : 61 
toc_title : "\ u9593 \ u9694" 
--- 

# 간격 {# data-type-interval} 

시간과 날짜 간격을 나타내는 데이터 형의 패밀리. 결과 유형 [INTERVAL (../../../ sql-reference / operators / index.md # operator-interval) 오퍼레이터 

!!! warning "경고" 
    `Interval` 데이터 형의 값은 테이블에 저장 수 없습니다. 

구조 : 

- 부호없는 정수 값으로 시간 간격. 
- 간격 타입. 

지원되는 : 

-`SECOND` 
-`MINUTE` 
-`HOUR` 
-`DAY` 
-`WEEK` 
-`MONTH` 
-`QUARTER` 
-`YEAR` 

간격 유형에 대해 별도의 데이터 유형이 있습니다. 예를 들어,`DAY` 구간은`IntervalDay` 데이터 형식 :
 
```sql
SELECT toTypeName (INTERVAL 4 DAY) 
``` 

```text 
┌─toTypeName (toIntervalDay (4)) ─┐ 
│ IntervalDay │ 
└───────────────────── ─────────┘ 
``` 

## 사용상의주의 {# data-type-interval-usage-remarks} 

다음을 사용할 수 있습니다`Interval`- 산술 값을 날짜 (. ./../../sql-reference/data-types/date.md) 및 DateTime (../../../ sql-reference / data-types / datetime.md) - 값을 입력 합니다. 예를 들어, 현재 시간으로 4 일을 추가 할 수 있습니다 : 

```sql 
SELECT now () as current_date_time, current_date_time + INTERVAL 4 DAY 
``` 

```text 
┌───current_date_time─┬─plus (now () toIntervalDay (4)) ─┐ 
│ 2019-10-23 10:58:45 │ 2019-10-27 10:58:45 │ 
└─────────────────── ──┴───────────────────────────────┘ 
```

다른 유형의 간격은 결합 할 수 없습니다. 다음과 같은 간격은 사용할 수 없습니다`4 DAY 1 HOUR`. 간격은 간격 등 간격의 최소 단위보다 작은 단위 또는 동일한 단위로 지정합니다`1 day and an hour` 간격은 다음과 같이 표현할 수 하다합니다`25 HOUR` 또는`90000 SECOND`. 

다음 작업에서 산술 연산을 수행 할 수 없습니다`Interval`- 값을 입력합니다. 결과적으로 다른 유형의 간격을 값에 추가 할 수 있습니다. `Date` 또는`DateTime` 데이터 형식입니다. 예 : 

```sql 
SELECT now () AS current_date_time, current_date_time + INTERVAL 4 DAY + INTERVAL 3 HOUR 
``` 

```text 
┌───current_date_time─┬─plus (plus (now (), toIntervalDay (4)) , toIntervalHour (3)) ─┐ 
│ 2019-10-23 11:16:28 │ 2019-10-27 14:16:28 │ 
└────────────────── ───┴────────────────────────────────────────────── ──────────┘ 
``` 

다음 쿼리에서는 예외가 발생합니다.
 
```sql
select now () AS current_date_time, current_date_time + (INTERVAL 4 DAY + INTERVAL 3 HOUR) 
``` 

```text 
Received exception from server (version 19.14.1) : 
Code : 43. DB :: Exception : Received from localhost : 9000 : DB :: Exception : Wrong argument types for function plus : if one argument is Interval, then another must be Date or DateTime .. 
``` 

## 참조. {# see-also} 

- [INTERVAL (../../../ sql-reference / operators / index.md # operator-interval) 연산자 
- [toInterval (../../../ sql-reference / functions / type-conversion-functions.md # function-tointerval) 형식 변환 함수
