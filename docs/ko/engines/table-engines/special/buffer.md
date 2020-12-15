---
machine_translated : true
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd
toc_priority : 45
toc_title : "\ u30D0 \ u30C3 \ u30D5 \ u30A1"
---

# 버퍼 {#buffer}

버퍼의 데이터를 기록 RAM 정기적으로 플러싱에서 별표와 같다. 읽기 동작 중에 데이터 버퍼와 다른 테이블에서 동시에 읽습니다.

```sql
Buffer (database table, num_layers, min_time, max_time, min_rows, max_rows, min_bytes, max_bytes)
```

엔진 변수 :

-`database` - Database name. Instead of the database name, you can use a constant expression that returns a string.
-`table` - Table to flush data to.
-`num_layers` - Parallelism layer. Physically, the table will be represented as`num_layers` 독립적 인 완충. 권장 값 : 16.
-`min_time``max_time``min_rows``max_rows``min_bytes`, and`max_bytes` - Conditions for flushing data from the buffer.

데이터는 버퍼에서 플래시되며 대상 테이블에 기록됩니다. `min *`조건 또는 적어도 하나`max *`조건이 충족된다.

-`min_time``max_time` - Condition for the time in seconds from the moment of the first write to the buffer.
-`min_rows``max_rows` - ​​Condition for the number of rows in the buffer.
-`min_bytes``max_bytes` - Condition for the number of bytes in the buffer.

쓰기 작업 동안 데이터는 a에 삽입됩니다`num_layers` 랜덤 버퍼의 수. 또는 삽입하는 데이터 부분이 충분히 큰 (큰) 경우`max_rows` 또는`max_bytes`) 그것은 버퍼를 생략하여 대상 테이블에 직접 기록됩니다.

데이터를 플래시하는 조건은`num_layers` 버퍼 예를 들어,`num_layers = 16`와`max_bytes = 100000000` 최대 RAM 사용량은 1.6GB입니다.

예 :

```sql
CREATE TABLE merge.hits_buffer AS merge.hits ENGINE = Buffer (merge, hits, 16, 10, 100, 10000, 1000000, 10000000, 100000000)
```

작성 'merge.hits_buffer'와 동일한 구조의 테이블 'merge.hits'그리고 버퍼 엔진을 사용합니다. 이 테이블에 기록 할 때 데이터는 RAM에 버퍼되고 나중에 'merge.hits'테이블. 16 버퍼가 생성됩니다. 각각의 데이터는 100 초가 경과 한 경우 또는 백만 행이 작성된 경우 또는 100MB의 데이터가 기록 된 경우 또는 10 초가 경과하여 10,000 개의 행과 10 예를 들어, 단 하나의 행이 기록되어있는 경우 100 초 후에는 무슨 일이 있어도 플래시됩니다. 그러나 많은 행이 기록 된 경우 데이터는 빨리 플래시됩니다.

DROP TABLE 또는 DETACH TABLE을 사용하여 서버를 중지하면 버퍼 데이터를 대상 테이블에 플래시됩니다.

데이터베이스 이름과 테이블 이름은 작은 따 quotation에서 빈 문자열을 설정할 수 있습니다. 이것은 대상 테이블이 없음을 나타냅니다. 이 경우 데이터 플래시 조건에 도달하면 버퍼는 단순히 지워집니다. 이것은 데이터 창을 메모리에 유지하는 경우에 유용합니다.

버퍼 테이블에서 읽을 때 데이터는 버퍼 및 대상 테이블 모두에서 처리됩니다 (있는 경우).
버퍼 테이블은 인덱스를 지원하지 않습니다. 즉, 버퍼의 데이터는 완전히 스캔됩니다. (하위 테이블의 데이터는 지원하는 인덱스가 사용됩니다.)

버퍼 테이블의 열 집합이 하위 테이블의 C 럼 세트와 일치하지 않는 경우 두 테이블에 존재하는 컬럼의 서브 세트가 삽입됩니다.

버퍼 테이블의 열 중 하나와 하위 테이블 형식이 일치하지 않는 경우, 서버 로그에 오류 메시지가 입력 버퍼가 지워집니다.
버퍼가 플러시 된 경우 하위 테이블이 존재하지 않는 경우에도 같은 일이 일어납니다.

하위 테이블과 버퍼 테이블에 대해 ALTER를 수행 할 필요가있는 경우 먼저 버퍼 테이블을 삭제하고 하위 테이블에 대해 ALTER를 수행하고 백업

서버가 비정상적으로 다시 시작되면 버퍼의 데이터는 손실됩니다.

마지막 샘플의 제대로 작동하지 않기 때문에 버퍼입니다. 이러한 조건은 대상 테이블로 전달되지만 버퍼의 데이터 처리에 사용되지 않습니다. 이러한 특징이 필요만을 사용하는 것이 좋습니다 버리는 버퍼 테이블을 쓰기, 읽기의 목적지 표에 나타낸다.

를 추가 할 때 데이터를 버퍼의 버퍼가 잠겨 있습니다. 이렇게하면 테이블에서 읽기 작업이 동시에 실행되는 경우 지연이 발생합니다.

버퍼 테이블에 삽입되는 데이터는 하위 테이블에서 다른 순서와 다른 블록 될 수 있습니다. 따라서 CollapsingMergeTree 제대로 쓰기 위해 버퍼 테이블을 사용하는 것은 어렵습니다. 문제를 해결하려면 다음을 설정할 수 있습니다 'num_layers'1.

의 경우 먼저 테이블이 그대로 재현되어 기대의 특징을 재현 테이블이 잃어버린 쓰기 버퍼의 표에 나타내었다. 행 순서와 데이터 부분의 크기를 임의로 변경되면 데이터 중복 제거가 작동을 종료합니다. 'exactly once'쓰기 재현합니다.

이러한 단점이 있기 때문에 드물게 버퍼 테이블의 사용을 권장하지 않습니다.

버퍼 테이블은 단위 시간에 걸쳐 다수의 서버에서 다수의 삽입을 수신하고 삽입하기 전에 데이터를 버퍼링 할 수없는 경우에 사용됩니다.

버퍼 테이블도 한 번에 데이터 행을 삽입하는 것은 의미가 없습니다. 이는 초당 수천 라인의 속도가 생성되지만 더 큰 데이터 블록을 삽입하면 초당 백만 행 이상의 속도가 생성됩니다 (섹션을 참조 "Performance").

원본 기사 (https://clickhouse.tech/docs/en/operations/table_engines/buffer/) <! - hide ->
