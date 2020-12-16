---
machine_translated : true
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd
toc_priority : 34
toc_title : INSERT INTO
---

## INSERT {#insert}

데이터 추가.

기본적인 쿼리 형식 :

```sql
INSERT INTO [db] table [(c1, c2, c3) VALUES (v11, v12, v13) (v21, v22, v23) ...
```

쿼리는 삽입 할 열 목록을 지정할 수 있습니다`[(c1, c2, c3)`이 경우 나머지 열은 :

- 계산 된 값`DEFAULT` 테이블 정의에서 지정된 표현식입니다.
- 0과 빈 문자열`DEFAULT` 식 정의되어 있지 않습니다.

만약 [strict_insert_defaults = 1] (../../ operations / settings / settings.md)이없는 열`DEFAULT` 정의 된 기재되어 있는지를 반환합니다.

데이터는 임의의 장소에서 INSERT에 전달할 수 있습니다 [형식] (../../ interfaces / formats.md # formats) ClickHouse 지원하고 있습니다. 이 형식은 쿼리에서 명시 적으로 지정해야합니다 :

```sql
INSERT INTO [db] table [(c1, c2, c3) FORMAT format_name data_set
```

For example, the following query format is identical to the basic version of INSERT ... VALUES :

```sql
INSERT INTO [db] table [(c1, c2, c3) FORMAT Values ​​(v11, v12, v13) (v21, v22, v23) ...
```

ClickHouse 데이터 앞에 공백과 줄 바꿈이있는 경우를 모두 삭제합니다. 쿼리를 만들 때 쿼리 연산자 뒤에 새 행에 데이터를 넣을 것을 권장합니다 (데이터가 공백으로 시작하는 경우 중요합니다.)

예 :

```sql
INSERT INTO t FORMAT TabSeparated
11 Hello, world!
22 Qwerty
```

삽입 할 수 있습니다. 데이터에서 다른 쿼리의 명령 줄 클라이언트 HTTP 인터페이스. 자세한 내용은 "[인터] (../../ interfaces / index.md # interfaces)".

### 제약 {#constraints}

테이블 제약 (create.md # constraints), their expressions will be checked for each row of inserted data. If any of those constraints is not satisfied - server will raise an exception containing constraint name and expression, the query will be stopped .

###의 결과를 삽입하는`SELECT` {# insert_query_insert-select}

```sql
INSERT INTO [db] table [(c1, c2, c3)] SELECT ...
```

열은 SELECT 절 내의 위치에 따라 맵됩니다. 그러나 SELECT 표현식과 INSERT 테이블의 이름은 다를 수 있습니다. 필요에 따라 형식 캐스팅이 진행된다.

값 이외의 데이터 형식에서는 다음과 같은 식으로 값을 설정할 수 없습니다`now ()``1 + 2`, 등등. Values ​​형식으로 식의 사용은 제한되지만,이 경우는 비효율적 인 코드를 실행하는 데 사용되기 때문에이 방법은 권장되지 않습니다.

다른 쿼리를 데이터 요소에 대응하지 않는`UPDATE``DELETE``REPLACE``MERGE``UPSERT``INSERT UPDATE`.
그러나 오래된 데이터를 삭제하려면`ALTER TABLE ... DROP PARTITION`.

`FORMAT` 다음의 경우 쿼리의 마지막 절을 지정해야합니다`SELECT` 절은 테이블 함수를 포함 입력 ()] (../ table-functions / input.md).

### 성능 {# performance-considerations}

`INSERT` 입력 데이터를 기본 키에 정렬 파티션 키에 분할합니다. 경우에 데이터를 삽입 복수의 분할은 한 번에 크게 저감 할 수있는`INSERT` 쿼리. 이를 방지하려면 :

- 한 번에 100,000 행 등 꽤 큰 배치에서 데이터를 추가합니다.
- 그룹에 의한 데이터 파티션 키 업로드 전에에서 ClickHouse.

성능이 저하되지 않는 경우 :

- 데이터는 실시간으로 추가됩니다.
- 업로드 된 데이터는 일반적으로 정렬되었습니다.

원본 기사 (https://clickhouse.tech/docs/en/query_language/insert_into/) <! - hide ->
