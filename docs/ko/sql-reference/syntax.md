---
machine_translated : true
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd
toc_priority : 31
toc_title : "\ u69CB \ u6587"
---

# 구문 {#syntax}

시스템 파서는 전체 SQL 파서 (재귀 하강 파서)와 데이터 형식 파서 (고속 스트림 파서)의 두 종류가 있습니다.
를 제외한 모든 경우에`INSERT` 전체 SQL 파서 만 사용됩니다.
그`INSERT` 쿼리를 모두 사용 파서 :

```sql
INSERT INTO t VALUES (1, 'Hello, world'), (2, 'abc'), (3, 'def')
```

그`INSERT INTO t VALUES` 조각은 완전한 파서에 의해 분석 된 데이터는`(1, 'Hello, world'), (2, 'abc'), (3, 'def')`고속 스트림 파서에 의해 해석 됩니다. 또한 데이터의 전체 파서를 선택하려면 input_format_values_interpret_expressions] (../ operations / settings / settings.md # settings-input_format_values_interpret_expressions) 설정. 때`input_format_values_interpret_expressions = 1`, ClickHouse 먼저 고속 스트림 파서 값을 분석하려고합니다. 실패한 경우 ClickHouse는 데이터에 대한 완벽한 파서를 사용하여 SQL처럼 취급 식 (# syntax-expressions).

데이터의 형식은 선택 사항입니다. 쿼리가 수신되면, 서버는 다음의 값을 계산하지 않습니다 [max_query_size] (../ operations / settings / settings.md # settings-max_query_size) 요청 바이트의 RAM (기본적으로 1MB)이고 나머지는 스트림 분석됩니다.
이를 방지하려면 문제의 크기`INSERT` 쿼리.

를 사용하는 경우`Values` 포맷`INSERT` 이것은 데이터가 a의 식과 동일하게 해석되는 것처럼 보일지도 모릅니다`SELECT` 쿼리 이것은 사실이 아닙니다. 그`Values` 형식은 훨씬 제한되어 있습니다.

이 기사의 나머지 부분은 완전한 파서를 커버합니다. 포맷 파서의 자세한 내용은 [형식] (../ interfaces / formats.md) 섹션

## 공간 {#spaces}

구문 구성 (쿼리의 시작과 종료를 포함) 사이에는 임의의 수의 공백 기호가 있습니다. 공백은 공백, 탭, 줄 바꿈, CR 및 폼 피드가 있습니다.

## 코멘트 {#comments}

ClickHouse 지원 중 하나의 SQL 형은 C 스타일의 코멘트.
SQL 스타일의 주석으로 시작`--` 그리고 행의 끝 이후의 공간으로 이동합니다`--` 생략 할 수있다.
C 스타일은`/ *`에`* /`그리고 여러 줄에 수 공간도 필요 없습니다.

## 키워드 {# syntax-keywords}

키워드는 대소 문자를 구분하지 않습니다 :

- SQL 표준. 예를 들어,`SELECT``select`와`SeLeCt` 모두 유효합니다.
- 몇 가지 일반적인 DBMS (MySQL이나 Postgres)에서 구현. 예를 들어,`DateTime`과 동일합니다`datetime`.

데이터 형명으로 대문자와 소문자가 구별 될지 여부는`system.data_type_families` 테이블.

표준 SQL과는 대조적으로, 다른 모든 키워드 (함수 이름을 포함)은 다음과 같습니다 ** 대소 문자를 구별 **.

키워드는 예약되어 있지 않습니다. 를 사용하는 경우 식별자 (# syntax-identifiers) 키워드와 같은 이름을 큰 따옴표 또는 역 따옴표로 묶습니다. 예를 들어, 다음 쿼리입니다`SELECT "FROM"FROM table_name` 테이블의 경우 유효합니다`table_name` 이름을 가진 열이 있습니다` "FROM"`.

## 식별자 {# syntax-identifiers}

식별자는 :

- 클러스터 데이터베이스, 테이블, 파티션 컬럼 이름이되어 버립니다
- 기능.
- 데이터 형식입니다.
- 수식 별칭 (# syntax-expression_aliases).

식별자 인용하는 것은 비 인용됩니다. 후자가 바람직하다.

비 인용 식별자와 일치해야한다 regex`^ [a-zA-Z _] [0-9a-zA-Z _] * $`와 동일 할 수 없습니다 [키워드] (# syntax-keywords) 예 : `x, _1, X_y__Z123_.`

키워드와 동일한 식별자를 사용하거나 식별자에 다른 기호를 사용하는 경우 큰 따옴표 또는 역 따옴표를 사용하여 따옴표로 묶습니다. ` "id"````id```.

## 리터럴 {#literals}

숫자, 문자열, 복합 및`NULL` 리터럴.

### 수치 {#numeric}

숫자 리터럴을 해석하려합니다 :

- 먼저 64 비트 부호있는 숫자로, 스토루투루 (https://en.cppreference.com/w/cpp/string/byte/strtoul) 기능.
- 실패했을 경우는 64 비트 부호없는 숫자로, strtoll (https://en.cppreference.com/w/cpp/string/byte/strtol) 기능.
- 실패했을 경우는 부동 소수점 숫자로 [strtod (https://en.cppreference.com/w/cpp/string/byte/strtof) 기능.
- 그렇지 않으면 오류를 반환합니다.

리터럴 값은 값이 들어간다 최소의 형태를 가지고 있습니다.
예를 들어, 1은 다음과 같이 해석됩니다`UInt8` 그러나 256은 다음과 같이 해석됩니다`UInt16`. 자세한 내용은 데이터 형식 (../ sql-reference / data-types / index .md).

예 :`1`,`18446744073709551615``0xDEADBEEF``01`,`0.1`,`1e100``-1e-100`,`inf``nan`.

### 문자열 {# syntax-string-literal}

작은 따 quotes 문자열 리터럴 만 지원됩니다. 둘러싸인 문자는 백 슬래시 이스케이프 수 있습니다. 다음의 이스케이프 시퀀스에 해당하는 특수한 값 :`\ b`,`\ f`,`\ r``\ n``\ t``\ 0``\ a``\ v` `\ xHH`. 그렇지 않으면 이스케이프 시퀀스 형식으로`\ c` 여기서`c`는 임의의 문자이며,`c`. 즉, 시퀀스를 사용할 수 있습니다`\ '`과 `\\`이 값은 문자열 (../ sql-reference / data-types / string.md) 타입.

문자열 리터럴은 적어도` '`와`\`단일 인수 quotes은 작은 따 quote 리터럴에서 벗어날 수`'It \ 's'`와`'It''s'` 같다.

### 화합물 {#compound}

배열은 대괄호로 구성됩니다`[1, 2, 3]`. Nuples는 괄호로 구성되어 있습니다`(1, 'Hello, world!', 2)`.
기술적으로는 이러한 리터럴 대신 각각 배열 생성 연산자와 튜플 생성 연산자가있는 식입니다.
배열은 적어도 하나의 항목으로 구성되어 쌍은 적어도 두 항목을 가질 필요가 있습니다.
튜플이 나타날 다른 경우가 있습니다`IN` a 절`SELECT` 쿼리. 쿼리 결과는 쌍을 포함 할 수 있지만 쌍을 데이터베이스에 저장할 수 없습니다 [메모리] (../ engines / table-engines / special / memory.md) 엔진).

### NULL {# null-literal}

값이 누락 된 것을 나타냅니다.

저장하기 위해`NULL` 테이블 필드에서는 [Null 가능 (../ sql-reference / data-types / nullable.md) 타입.

데이터 형식 (입력 또는 출력)에 따라)`NULL` 다른 표현을 가지고 있어도 좋다. 자세한 내용은 다음 문서를 참조하십시오 데이터 형식 (../ interfaces / formats.md # formats).

처리에 많은 뉘앙스가 있습니다`NULL` 예 비교의 인수 중 적어도 하나가 다음과 같이되어 있으면`NULL`이 작업의 결과도`NULL` 곱하기 덧셈 및 기타 연산에 대해서도 마찬가지입니다. 자세한 내용은 각 작업 문서를 참조하십시오.

쿼리는 다음을 확인할 수 있습니다`NULL`를 사용하여 IS NULL (operators / index.md # operator-is-null) 및 IS NOT NULL (operators / index.md) 연산자와 관련 함수`isNull`와`isNotNull`.

## 함수 {#functions}

함수 호출 인수 목록 (비어있는 경우도 있습니다)을 괄호로 묶은 식별자 같이 쓰여져 있습니다. 표준 SQL과는 대조적으로, 하늘의 인수 목록에도 대괄호가 필요합니다. 예 :`now ()`.
정규 및 집계 함수가 있습니다 (섹션을 참조 "Aggregate functions") 일부 집계 함수를 포함 할 수 두 목록의 인수 트에 고정하여 사용합니다. 예 :`quantile (0.9) (x)`. 이러한 집계 함수는 "parametric"함수와 첫 번째 목록의 인수가 불려갑니다 "parameters"파라미터가없는 집계 함수의 구문은 일반적인 함수와 동일합니다 .

## 연산자 {#operators}

연산자는 쿼리 구문 분석하는 동안 우선 순위와 연상을 고려하여 해당 함수로 변환됩니다.
예를 들어, 다음 수식은`1 + 2 * 3 + 4`로 변환됩니다`plus (plus (1, multiply (2, 3)), 4)`.

## 데이터 유형 및 데이터베이스의 테이블 엔진 {# data_types-and-database-table-engines}

데이터 형식과 테이블 엔진`CREATE` 쿼리는 식별자 또는 함수와 같은 방식으로 작성됩니다. 즉, 괄호 안에 인수 목록을 포함하거나 포함되지 않을 수 있습니다. 자세한 내용은 "Data types" "Table engines"와 "CREATE".

## 식 별칭 {# syntax-expression_aliases}

별명은 쿼리 식의 사용자 정의 이름입니다.

```sql
expr AS alias
```

-`AS` - The keyword for defining aliases. You can define the alias for a table name or a column name in a`SELECT`을 사용하지 않고 어구`AS` 키워드.

        For example,`SELECT table_name_alias.column_name FROM table_name table_name_alias`.

        In the [CAST] (sql_reference / functions / type_conversion_functions.md # type_conversion_function-cast) function, the`AS` keyword has another meaning. See the description of the function.

-`expr` - Any expression supported by ClickHouse.

        For example,`SELECT column_name * 2 AS double FROM some_table`.

-`alias` - Name for`expr` 별칭은 식별자 (# syntax-identifiers) 구문.

        For example,`SELECT "table t".column_name FROM table_name AS "table t"`.

### 사용상의주의 {# notes-on-usage}

별칭은 쿼리 또는 하위 쿼리의 글로벌이며 어떤 식 쿼리의 어떤 부분에서 별칭을 정의 할 수 있습니다. 예를 들어,`SELECT (1 AS n) + 2, n`.

별칭은 하위 쿼리 및 하위 쿼리 사이에서는 표시되지 않습니다. 예를 들어, 쿼리가 실행되는 동안`SELECT (SELECT sum (ba) + num FROM b) - aa AS num FROM a` ClickHouse는 예외를 생성합니다`Unknown identifier : num`.

결과 열에 별칭이 정의되어있는 경우`SELECT` 하위 절은 이러한 열은 외부 쿼리에 표시됩니다. 예를 들어,`SELECT n + m FROM (SELECT 1 AS n 2 AS m)`.

열 또는 테이블 이름과 같은 별명에주의하십시오. 다음의 예를 생각해 봅시다 :

```sql
CREATE TABLE t
(
    a Int,
    b Int
)
ENGINE = TinyLog ()
```

```sql
SELECT
    argMax (a, b)
    sum (b) AS b
FROM t
```

```text
Received exception from server (version 18.14.17) :
Code : 184. DB :: Exception : Received from localhost : 9000, 127.0.0.1 : DB :: Exception : Aggregate function sum (b) is found inside another aggregate function in query.
```

이 예에서는 테이블을 선언했습니다`t` 열이있는`b` 다음 데이터를 선택할 때`sum (b) AS b` 일명 이라며 별칭은 글로벌 ClickHouse 대체되는 리터럴` b` 식으로는`argMax (a, b)`식을 사용하여`sum (b)`이 치환에 의해 예외가 발생했습니다.

## 별표 {#asterisk}

에서`SELECT` 쿼리 별표로 대체 다른 아이콘으로 표시됩니다. 자세한 내용은 "SELECT".

## 식 {# syntax-expressions}

표현식은 함수 식별자, 리터럴, 연산자의 적용, 괄호 안의 수식 하위 쿼리 또는 별표입니다. 별명을 포함 할 수 있습니다.
식 목록은 쉼표로 구분 된 하나 이상의 식입니다.
함수 및 연산자는 인수로 식을 가질 수 있습니다.

원본 기사 (https://clickhouse.tech/docs/en/sql_reference/syntax/) <! - hide ->
