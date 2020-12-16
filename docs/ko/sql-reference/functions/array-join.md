---
machine_translated : true
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd
toc_priority : 61
toc_title : "\ u30A2 \ u30EC \ u30A4 \ u30B8 \ u30E7 \ u30A4 \ u30F3"
---

# arrayJoin 함수 {#functions_arrayjoin}

이것은 매우 드문 기능입니다.

일반 함수는 행 세트를 변경하는 것이 아니라 각 행 (map)의 값을 변경하면됩니다.
집계 함수는 행 세트를 압축합니다 (fold 또는 reduce).
그 'arrayJoin'함수는 각 행을 가지고 행 세트를 생성합니다 (전개).

이 함수는 배열을 인수로 배열 내의 요소 수에 소스 행을 여러 행에 전파합니다.
이 함수가 적용되는 열의 값을 제외한 열의 모든 값은 단순히 복사됩니다.

쿼리는 여러`arrayJoin` 기능. 이 경우 변환은 여러 번 실행됩니다.

SELECT 쿼리의 배열 조인 구문에주의하십시오.

예 :

```sql
SELECT arrayJoin (1, 2, 3] AS src) AS dst 'Hello', src
```

```text
┌─dst─┬─ \ 'Hello \'─┬─src─────┐
│ 1 │ Hello │ [1,2,3] │
│ 2 │ Hello │ [1,2,3] │
│ 3 │ Hello │ [1,2,3] │
└─────┴───────────┴─────────┘
```

원본 기사 (https://clickhouse.tech/docs/en/query_language/functions/array_join/) <! - hide ->
