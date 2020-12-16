---
machine_translated : true
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd
toc_priority : 50
toc_title : Enum
---

# Enum {#enum}

명명 된 값으로 구성된 열거 형.

이름 값으로 선언 된` 'string'= integer` 쌍. ClickHouse는 숫자 만 저장하지만, 이름에 의한 값의 작업을 지원합니다.

ClickHouse 지원 :

- 8 비트`Enum`. 그것은에 열거 된 256까지의 값을 포함 할 수 있습니다`[-128, 127]`범위
- 16 비트`Enum`. 그것은에 열거 된 65536까지의 값을 포함 할 수 있습니다`[-32768 32767]`범위

ClickHouse은 자동으로 다음 유형을 선택합니다`Enum` 데이터가 삽입 될 때. 또한`Enum8` 또는`Enum16` 스토리지의 크기를 확인하는 타입.

## 예 {# usage-examples}

여기에서는`Enum8 ( 'hello'= 1 'world'= 2)`유형 열 :

```sql
CREATE TABLE t_enum
(
    x Enum ( 'hello'= 1 'world'= 2)
)
ENGINE = TinyLog
```

열`x` 형 정의에 나열된 값만을 저장할 수 있습니다 :` 'hello'` 또는`'world'` 다른 값을 저장하려고하면 ClickHouse는 예외를 발생시킵니다. 따라서 8 비트 크기`Enum` 자동으로 선택됩니다.

```sql
INSERT INTO t_enum VALUES ( 'hello') ( 'world') ( 'hello')
```

```text
Ok.
```

```sql
INSERT INTO t_enum values ​​( 'a')
```

```text
Exception on client :
Code : 49. DB :: Exception : Unknown element 'a'for type Enum ( 'hello'= 1 'world'= 2)
```

테이블에서 데이터를 조회하면 ClickHouse 문자열 값을`Enum`.

```sql
SELECT * FROM t_enum
```

```text
┌─x─────┐
│ hello │
│ world │
│ hello │
└───────┘
```

행의 등가 인 수치를 볼 필요가있는 경우는 다음과 같이 캐스팅해야합니다`Enum` 정수 값.

```sql
SELECT CAST (x 'Int8') FROM t_enum
```

```text
┌─CAST (x 'Int8') ─┐
│ 1 │
│ 2 │
│ 1 │
└─────────────────┘
```

쿼리에서 Enum 값을 만들려면 다음의 것도 사용해야합니다`CAST`.

```sql
SELECT toTypeName (CAST ( 'a', 'Enum (\'a \ '= 1, \'b \ '= 2)'))
```

```text
┌─toTypeName (CAST ( 'a', 'Enum (\'a \ '= 1, \'b \ '= 2)')) ─┐
│ Enum8 ( 'a'= 1, 'b'= 2) │
└───────────────────────────────────────────────── ────┘
```

## 일반적인 규칙과 사용법 {# general-rules-and-usage}

각 값은 범위의 값이 할당됩니다`-128 ... 127` 위해`Enum8` 또는 범위`-32768 ... 32767` 위해`Enum16` 모든 문자열과 숫자 는 달라야합니다. 빈 문자열을 사용할 수 있습니다. 이 형태가 (테이블 정의에서) 지정되어있는 경우, 수치는 임의의 순서로 할 수 있습니다. 그러나 순서는 중요하지 않습니다.

문자열도 숫자도 없다. `Enum` 수 있습니다 [NULL] (../../ sql-reference / syntax.md).

제거`Enum`에 포함 할 수 있습니다 [Null 가능 (nullable.md) 타입. 그래서 쿼리를 사용하여 테이블을 만들려면

```sql
CREATE TABLE t_enum_nullable
(
    x Nullable (Enum8 ( 'hello'= 1 'world'= 2))
)
ENGINE = TinyLog
```

있는 어플` 'hello'`와`'world'`하지만`NULL`처럼.

```sql
INSERT INTO t_enum_nullable Values ​​( 'hello') ( 'world'), (NULL)
```

RAM은`Enum` 열은 다음과 같이 저장됩니다`Int8` 또는`Int16` 해당하는 숫자.

텍스트 형식으로 가져올 경우 ClickHouse 값을 문자열로 해석하고 열거 형의 값의 집합에서 해당하는 문자열을 검색합니다. 없는 경우는 예외가 슬로우됩니다. 텍스트 형식으로 가져 오면 문자열을로드하고 해당하는 숫자가 검색됩니다. 없으면 예외가 throw됩니다.
텍스트 형식으로 쓸 경우, 그 값을 해당 문자열로 씁니다. 열 데이터에 가비지 (유효한 세트 이외의 수치)가 포함되어있는 경우는 예외가 슬로우됩니다. 바이너리 형식으로 읽고 쓰는 경우 Int8 및 Int16 데이터 형과 같은 방식으로 작동합니다.
암시 적 기본값은 숫자가 가장 작은 값입니다.

중`ORDER BY``GROUP BY``IN``DISTINCT` 등등, 열거 해당하는 숫자처럼 작동합니다. 예를 들어, ORDER BY는 수치로 정렬합니다. 항등 연산자 비교 연산자는 열거 형은 기본 수치와 동일하게 작동합니다.

열거 형의 값을 수치와 비교 할 수 없습니다. 열거 형 상수 문자열과 비교할 수 있습니다. 비교 대상 문자열이 열거 형의 유효한 값이 아닌 경우는 예외가 슬로우됩니다. IN 연산자는 왼쪽 열거 형과 오른쪽의 문자열의 집합에서 지원됩니다. 문자열은 해당 열거 형의 값입니다.

Most numeric and string operations are not defined for Enum values, eg adding a number to an Enum or concatenating a string to an Enum.
그러나 Enum은 자연스러운 것이 있습니다`toString` 문자열 값을 반환하는 함수.

열거 형 값은 다음 식을 사용하여 숫자 형으로 변환 할 수 있습니다`toT` 여기서 T는 숫자입니다. T가 열거 형의 기본 숫자 형식에 해당하는 경우,이 변환은 제로 비용입니다.
열거 형은 값 세트 만 변경되는 경우 ALTER를 사용하여 비용없이 변경할 수 있습니다. ALTER를 사용하여 열거 형 멤버를 추가 및 제거 할 수 있습니다 (삭제 된 값이 테이블에서 사용되지 않는 경우에만 삭제가 안전합니다). 세이프 가드로 이전에 정의 된 열거 형 멤버의 값을 변경하면 예외가 throw됩니다.

ALTER를 사용하면 Int8을 Int16로 변경하는 것과 같이 Enum8을 Enum16로 변경하거나 그 반대의 경우도 가능합니다.

원본 기사 (https://clickhouse.tech/docs/en/data_types/enum/) <! - hide ->
