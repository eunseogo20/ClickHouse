---
machine_translated : true
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd
toc_priority : 40
toc_title : "\ u53C2 \ u52A0"
---

# 참여 {#join}

에서 사용하기 위해 준비 된 데이터 구조 [JOIN] (../../../ sql-reference / statements / select / join.md # select-join) 작전이다

## 테이블 만들기 {# creating a-table}

```sql
CREATE TABLE [IF NOT EXISTS] [db] table_name [ON CLUSTER cluster]
(
    name1 [type1] [DEFAULT | MATERIALIZED | ALIAS expr1] [TTL expr1,
    name2 [type2] [DEFAULT | MATERIALIZED | ALIAS expr2] [TTL expr2]
) ENGINE = Join (join_strictness, join_type, k1 [, k2, ...])
```

자세한 설명을보세요 [CREATE TABLE (../../../ sql-reference / statements / create.md # create-table-query) 쿼리.

** 엔진 변수 **

-`join_strictness` - [엄격하게 결합 (../../../ sql-reference / statements / select / join.md # select-join-types).
-`join_type` - 결합 유형 (../../../ sql-reference / statements / select / join.md # select-join-types).
-`k1 [, k2, ...]`- Key columns from the`USING` 절은`JOIN` 작업으로 이루어진다.

입력`join_strictness`와`join_type` 인용 부호가없는 매개 변수`Join (ANY, LEFT, col1)`.도 일치해야한다`JOIN` 테이블이 사용되는 작업. 매개 변수가 일치하지 않는 경우 ClickHouse는 예외를 throw하지 않고 잘못된 데이터를 반환 할 수 있습니다.

## 테이블의 사용법 {# table-usage}

### 예 {#example}

왼쪽 테이블 만들기 :

```sql
CREATE TABLE id_val (`id` UInt32`val` UInt32) ENGINE = TinyLog
```

```sql
INSERT INTO id_val VALUES (1,11) (2,12) (3,13)
```

오른쪽 만들기`Join` 테이블 :

```sql
CREATE TABLE id_val_join (`id` UInt32`val` UInt8) ENGINE = Join (ANY, LEFT, id)
```

```sql
INSERT INTO id_val_join VALUES (1,21) (1,22) (3,23)
```

테이블의 결합 :

```sql
SELECT * FROM id_val ANY LEFT JOIN id_val_join USING (id) SETTINGS join_use_nulls = 1
```

```text
┌─id─┬─val─┬─id_val_join.val─┐
│ 1 │ 11 │ 21 │
│ 2 │ 12 │ ᴺᵁᴸᴸ │
│ 3 │ 13 │ 23 │
└────┴─────┴─────────────────┘
```

다른 방법으로,`Join` 결합 키 값을 지정하는 테이블 :

```sql
SELECT joinGet ( 'id_val_join', 'val', toUInt32 (1))
```

```text
┌─joinGet ( 'id_val_join', 'val', toUInt32 (1)) ─┐
│ 21 │
└────────────────────────────────────────────┘
```

### 데이터 선택 및 삽입 {# selecting-and-inserting-data}

다음을 사용할 수 있습니다`INSERT`에 데이터를 추가하는 쿼리`Join`- 엔진 테이블. 테이블이 작성된 경우`ANY` 엄격 중복 키의 데이터는 무시됩니다. 하면`ALL` 경직은 모든 ​​행이 추가됩니다.

을 수행 할 수 없습니다. `SELECT` 테이블에서 직접 쿼리. 대신 다음 방법 중 하나를 사용합니다 :

- A의 오른쪽에 테이블을 놓으십시오`JOIN` 절.
- 콜 [joinGet (../../../ sql-reference / functions / other-functions.md # joinget) 사전과 같은 방법으로 테이블에서 데이터를 추출 할 수있는 기능입니다.

### 제한 사항 및 설정 {# join-limitations-and-settings}

테이블을 만들 때 다음과 같은 설정이 적용됩니다 :

- [join_use_nulls (../../../ operations / settings / settings.md # join_use_nulls)
- [max_rows_in_join (../../../ operations / settings / query-complexity.md # settings-max_rows_in_join)
- [max_bytes_in_join (../../../ operations / settings / query-complexity.md # settings-max_bytes_in_join)
- [join_overflow_mode (../../../ operations / settings / query-complexity.md # settings-join_overflow_mode)
- [join_any_take_last_row (../../../ operations / settings / settings.md # settings-join_any_take_last_row)

그`Join`- 엔진 테이블은 사용할 수 없습니다`GLOBAL JOIN` 작전이다

그`Join`- 엔진은 사용을 허용 [join_use_nulls (../../../ operations / settings / settings.md # join_use_nulls) 설정`CREATE TABLE` 계산서. 및 SELECT (../../../ sql-reference / statements / select / index.md) 쿼리를 사용할 수있는`join_use_nulls`도. 당신이 다른 경우`join_use_nulls` 설정할 수있는 오류에 합류했다. 그것은 결합의 종류에 따라 달라집니다. 사용할 때 [joinGet (../../../ sql-reference / functions / other-functions.md # joinget) 함수 당신은 동일을 사용해야합니다`join_use_nulls` 설정`CRATE TABLE`과 `SELECT` 계산서.

## 데이터 저장 {# data-storage}

`Join` 테이블 데이터는 항상 RAM에 있습니다. 를 삽입 할 때 열 표 ClickHouse에 쓰기 데이터 블록의 디렉토리의 디스크 수 있도록 복원 된 서버가 다시 시작하게됩니다.

경우 서버가 다시 시작 오류, 데이터 블록의 디스크가 있습니다. 이 경우 손상된 데이터가 포함 된 파일을 수동으로 삭제해야하는 경우가 있습니다.

원본 기사 (https://clickhouse.tech/docs/en/operations/table_engines/join/) <! - hide ->
