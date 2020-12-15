---
machine_translated : true
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd
toc_priority : 36
toc_title : "\ u30DE \ u30FC \ u30B8"
---

# 병합 {#merge}

그`Merge` 엔진 (과 혼동하지 않도록`MergeTree`)을 포함하지 않는 데이터 자체가 있기 때문에, 다른 테이블을 동시에
읽기는 자동으로 병렬 처리됩니다. 테이블에 대한 쓰기는 지원되지 않습니다. 읽을 때 실제로 읽고있는 테이블의 인덱스가 존재하는 경우에 사용됩니다.
그`Merge` 데이터베이스 이름과 테이블의 정규 표현식입니다.

예 :

```sql
Merge (hits '^ WatchLog')
```

데이터는 테이블에서 읽혀집니다`hits` 정규 표현에 일치하는 이름을 가진 데이터베이스 '`^ WatchLog`'.

데이터베이스 이름 대신에 문자열을 반환 상수 표현식을 사용할 수 있습니다. 예를 들어,`currentDatabase ()`.

Regular expressions - [re2 (https://github.com/google/re2) (PCRE의 일부를 지원), 대문자와 소문자를 구별합니다.
정규 표현식에서 기호의 탈출에 관한주의 "match"섹션

읽을 테이블을 선택할 때`Merge` 정규식과 일치해도 테이블 자체는 선택되지 않습니다. 이것은 루프를 방지합니다.
두를 만들 수 있습니다`Merge` 무한 서로 데이터를로드 할 테이블이지만, 이것은 좋은 생각이 아닙니다.

사용하는 전형적인 방법`Merge` 엔진은 많은 것을 사용하기 때문입니다`TinyLog` 단일 테이블을 가진 것처럼 테이블.

예 2 :

오래된 테이블 (WatchLog_old)가 데이터를 새 테이블 (WatchLog_new)로 이동하지 않고 파티션을 변경하기로했다고합시다.

```sql
CREATE TABLE WatchLog_old (date Date, UserId Int64, EventType String, Cnt UInt64)
ENGINE = MergeTree (date (UserId, EventType), 8192);
INSERT INTO WatchLog_old VALUES ( '2018-01-01'1 'hit', 3);

CREATE TABLE WatchLog_new (date Date, UserId Int64, EventType String, Cnt UInt64)
ENGINE = MergeTree PARTITION BY date ORDER BY (UserId, EventType) SETTINGS index_granularity = 8192;
INSERT INTO WatchLog_new VALUES ( '2018-01-02'2 'hit', 3);

CREATE TABLE WatchLog as WatchLog_old ENGINE = Merge (currentDatabase () '^ WatchLog');

SELECT *
FROM WatchLog
```

```text
┌───────date─┬─UserId─┬─EventType─┬─Cnt─┐
│ 2018-01-01 │ 1 │ hit │ 3 │
└────────────┴────────┴───────────┴─────┘
┌───────date─┬─UserId─┬─EventType─┬─Cnt─┐
│ 2018-01-02 │ 2 │ hit │ 3 │
└────────────┴────────┴───────────┴─────┘
```

## 가상 열 {# virtual-columns}

-`_table` - Contains the name of the table from which data was read : Type : [문자열] (../../../ sql-reference / data-types / string.md).

    상수 조건은 다음과 같이 설정할 수 있습니다`_table`에서`WHERE / PREWHERE` 절 (예를 들어,`WHERE _table = 'xyz'`)이 경우 읽기 작업은 조건이 켜진 테이블에 대해서만 실행 됩니다`_table` 만족하고 있기 때문에`_table` 열은 인덱스의 역할을합니다.

** 참고하십시오. **

- 가상 열 (index.md # table_engines-virtual_columns)

원본 기사 (https://clickhouse.tech/docs/en/operations/table_engines/merge/) <! - hide ->
