---
machine_translated : true
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd
toc_priority : 32
toc_title : "\ u30B9 \ u30C8 \ u30EA \ u30C3 \ u30D7 \ u30ED \ u30B0"
---

# 스트립 로그 {#stripelog}

이 엔진은 로그 엔진 계열에 속한다. 로그 엔진의 일반적인 속성과 그 차이를 참조하십시오 로그 엔진 가족 (index.md) 기사이다

소량의 데이터 (10,000 행 미만)에서 다수의 테이블을 작성할 필요가있는 경우에이 엔진을 사용합니다.

## 테이블 만들기 {# table_engines-stripelog-creating a-table}

```sql
CREATE TABLE [IF NOT EXISTS] [db] table_name [ON CLUSTER cluster]
(
    column1_name [type1] [DEFAULT | MATERIALIZED | ALIAS expr1,
    column2_name [type2] [DEFAULT | MATERIALIZED | ALIAS expr2]
    ...
) ENGINE = StripeLog
```

자세한 설명을보세요 [CREATE TABLE (../../../ sql-reference / statements / create.md # create-table-query) 쿼리.

## 데이터 쓰기 {# table_engines-stripelog-writing-the-data}

그`StripeLog` engine은 모든 열을 하나의 파일에 저장합니다. 각각을위한`INSERT` query, ClickHouse 데이터 블록을 테이블 파일의 마지막에 추가 열을 하나씩 씁니다.

각 테이블 ClickHouse에 기록되는 파일 :

-`data.bin` - Data file.
-`index.mrk` - File with marks. Marks contain offsets for each column of each data block inserted.

그`StripeLog` 엔진은 지원하지 않습니다`ALTER UPDATE`와`ALTER DELETE` 작전이다

## 데이터 읽기 {# table_engines-stripelog-reading-the-data}

파일을 마크 ClickHouse를 병렬로 데이터입니다. 이것은`SELECT` query는 예측할 수없는 순서로 행을 반환합니다. 사용하는`ORDER BY` 행을 정렬하는 어구.

## 예 {# table_engines-stripelog-example-of-use}

테이블 만들기 :

```sql
CREATE TABLE stripe_log_table
(
    timestamp DateTime,
    message_type String,
    message String
)
ENGINE = StripeLog
```

데이터 삽입 :

```sql
INSERT INTO stripe_log_table VALUES (now (), 'REGULAR', 'The first regular message')
INSERT INTO stripe_log_table VALUES (now (), 'REGULAR', 'The second regular message'), (now (), 'WARNING', 'The first warning message')
```

우리는 두 가지를 사용`INSERT` 데이터 블록을 생성하는 쿼리`data.bin` 파일

ClickHouse 이용은 여러 스레드 선택시의 데이터입니다. 각 스레드는 별도의 데이터 블록을 읽고 종료시에 결과 행을 개별적으로 반환합니다 그 결과 출력의 행 블록의 순서는 대부분의 경우 입력의 동일한 블록의 순서와 일치하지 않습니다. 예를 들면 :

```sql
SELECT * FROM stripe_log_table
```

```text
┌───────────timestamp─┬─message_type─┬─message────────────────────┐
│ 2019-01-18 14:27:32 │ REGULAR │ The second regular message │
│ 2019-01-18 14:34:53 │ WARNING │ The first warning message │
└─────────────────────┴──────────────┴──────────── ────────────────┘
┌───────────timestamp─┬─message_type─┬─message───────────────────┐
│ 2019-01-18 14:23:43 │ REGULAR │ The first regular message │
└─────────────────────┴──────────────┴──────────── ───────────────┘
```

결과 정렬 (기본적으로 오름차순) :

```sql
SELECT * FROM stripe_log_table ORDER BY timestamp
```

```text
┌───────────timestamp─┬─message_type─┬─message────────────────────┐
│ 2019-01-18 14:23:43 │ REGULAR │ The first regular message │
│ 2019-01-18 14:27:32 │ REGULAR │ The second regular message │
│ 2019-01-18 14:34:53 │ WARNING │ The first warning message │
└─────────────────────┴──────────────┴──────────── ────────────────┘
```

원본 기사 (https://clickhouse.tech/docs/en/operations/table_engines/stripelog/) <! - hide ->
