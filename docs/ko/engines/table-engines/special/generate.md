---
machine_translated : true
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd
toc_priority : 46
toc_title : GenerateRandom
---

# Generaterandom {# table_engines-generate}

의 GenerateRandom 테이블 엔진 생산 임의의 데이터가 주어진 테이블의 스키마.

사용 예 :

- 재현 가능한 큰 테이블을 채우는 테스트의 사용.
- 파 징 테스트 임의 입력을 생성합니다.

## ClickHouse 서버에서 사용 {# usage-in-clickhouse-server}

```sql
ENGINE = GenerateRandom (random_seed, max_string_length, max_array_length)
```

그`max_array_length`와`max_string_length` 매개 변수 최대 길이의 모든
생성 된 데이터에 대응하는 배열과 문자열.

Generate table engine 지원 만`SELECT` 쿼리.

그것은 모든 데이터 형식 (../../../ sql-reference / data-types / index.md)를 제외하고 테이블에 저장할 수있는`LowCardinality`와`AggregateFunction`.

** 예 **

** 1 ** 설치`generate_engine_table` 테이블 :

```sql
CREATE TABLE generate_engine_table (name String, value UInt32) ENGINE = GenerateRandom (1, 5, 3)
```

** 2 ** 데이터 조회 :

```sql
SELECT * FROM generate_engine_table LIMIT 3
```

```text
┌─name─┬──────value─┐
│ c4xJ │ 1412771199 │
│ r │ 1791099446 │
│ 7 # $ │ 124312908 │
└──────┴────────────┘
```

## 실시 내용 {# details-of-implementation}

- 지원하지 않습니다 :
    -`ALTER`
    -`SELECT ... SAMPLE`
    -`INSERT`
    - 지수
    - 복제

원본 기사 (https://clickhouse.tech/docs/en/operations/table_engines/generate/) <! - hide ->
