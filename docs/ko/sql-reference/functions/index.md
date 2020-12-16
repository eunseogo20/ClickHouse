---
machine_translated : true
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd
toc_folder_title : "\ u95A2 \ u6570"
toc_priority : 32
toc_title : "\ u306F \ u3058 \ u3081 \ u306B"
---

# 함수 {#functions}

일반 함수 (그들은 단순히 호출됩니다) - 함수의 적어도 \ * 두 종류가 있습니다 "functions") and aggregate functions. These are completely different concepts. Regular functions work as if they are applied to each row separately (for each row, the result of the function does not depend on the other rows) Aggregate functions accumulate a set of values ​​from various rows (ie they depend on the entire set of rows).

이 절에서는 정상적인 기능에 대해 설명한다. 에 대한 집계 함수의 항을 참조하십시오 "Aggregate functions"

\ * - 기능의 세 번째 유형이 있습니다 'arrayJoin'테이블 함수는 따로 언급 할 수 있습니다. \ *

## 강력한 타이핑 {# strong-typing}

표준 SQL과는 대조적으로, ClickHouse는 강력한 형식이 있습니다. 즉, 유형간에 암시 적 변환되지 않습니다. 각 함수는 특정 형식 세트에 대해 작동합니다. 즉, 형식 변환 함수를 사용할 필요가있을 수 있습니다.

## 공통 부분 식 제거 {# common-subexpression-elimination}

같은 AST (동일한 레코드 또는 구문 분석 결과)를 가진 쿼리에있는 모든 식은 같은 값을 가지는 것으로 간주됩니다. 이런 식 연결되고, 한 번 실행됩니다. 동일한 하위도이 방법으로 삭제됩니다.

## 결과의 유형 {# types-of-results}

모든 함수는 결과로 단일 값을 반환합니다 (여러 값이 아닌 제로 값이 없습니다). 결과 형식은 일반적으로 값이 아닌 인수의 형태에 의해서만 정의됩니다. 예외는 tupleElement 함수 (aN 연산자)와 toFixedString 함수입니다.

## 상수 {#constants}

간단하게하기 위해 특정 함수는 여러 인수의 상수에서만 작동 할 수 있습니다. 예를 들어, LIKE 연산자의 오른쪽 인자는 정수가 아니면 안됩니다.
거의 모든 함수는 정수 인수의 정수를 돌려줍니다. 예외는 난수를 생성하는 함수입니다.
그 'now'함수는 다른 시간에 실행 된 쿼리에 대해 다른 값을 반환하지만 상수는 단일 쿼리에서만 중요하기 때문에 결과는 정수로 간주됩니다.
상수 식은 상수로도 간주됩니다 (예를 들어, LIKE 연산자의 오른쪽 반은 여러 상수에서 구성 할 수 있습니다).

함수는 상수와 상수가 아닌 인수를 위해 다른 방법으로 구현할 수 있습니다 (다른 코드가 실행됩니다). 그러나 정수와 같은 값만을 포함하는 진정한 열의 결과는 서로 일치해야합니다.

## 널 처리 {# null-processing}

함수의 동작은 다음과 같습니다 :

- 함수의 인수 중 적어도 하나가`NULL` 함수의 결과도 있습니다`NULL`.
- 각 함수의 설명에 별도로 지정되는 특수한 동작. ClickHouse 소스 코드에서는 이러한 함수는`UseDefaultImplementationForNulls = false`.

## 불변성 {#constancy}

Functions can not change the values ​​of their arguments - any changes are returned as the result. Thus, the result of calculating separate functions does not depend on the order in which the functions are written in the query.

## 오류 처리 {# error-handling}

데이터가 잘못된 경우 일부 함수는 예외를 throw 할 수 있습니다. 이 경우 쿼리가 취소되고 오류 텍스트를 클라이언트에 반환됩니다. 분산 처리의 경우 하나의 서버에서 예외가 발생하면 다른 서버도 쿼리를 중지하려고합니다.

## 인수 식의 평가 {# evaluation-of-argument-expressions}

거의 모든 프로그래밍 언어 중 하나의 인수가 평가된다. 이것은 일반적으로 연산자 &&``||`, and`?`.
그러나 ClickHouse는 함수 (연산자)의 인수는 항상 평가됩니다. 이것은 각 행을 별도로 계산하는 것이 아니라 열 부분이 한꺼번에 평가되기 때문입니다.

## 분산 쿼리 처리를위한 함수의 실행 {# performing-functions-for-distributed-query-processing}

분산 쿼리 처리의 경우 원격 서버에서 많은 단계의 쿼리 처리가 실행되고, 나머지 스테이지 (중간 결과와 이후의 모든 병합)은 리쿠에

즉, 다른 서버에서 기능을 수행 할 수 있습니다.
예를 들어, 쿼리는`SELECT f (sum (g (x))) FROM distributed_table GROUP BY h (y)`

- 만약`distributed_table` 적어도 두 조각을 가지고 기능 'g'와 'h'는 원격 서버에서 실행되는 'f'요청한 서버에서 실행됩니다.
- 만약`distributed_table` 하나 뿐인 조각 모두 가지고 있습니다 'f', 'g', and 'h'기능이 샤드 서버에서 실행됩니다.

일반적으로 함수의 결과는 실행되는 서버에 의존하지 않습니다. 그러나 때로는 이것이 중요합니다.
예를 들어 사전을 조작하는 함수는 그들이 실행되는 서버에 존재하는 사전을 사용합니다.
또 다른 예는`hostName` 실행하는 서버의 이름을 반환하는 함수`GROUP BY` a의 서버가`SELECT` 쿼리.

쿼리의 함수가 요청 서버에서 실행되고 있지만, 원격 서버에서 실행해야하는 경우 요청자 서버에 랩 할 수 있습니다. 'any'함수를 집계하거나 키에 추가합니다`GROUP BY`.

원본 기사 (https://clickhouse.tech/docs/en/query_language/functions/) <! - hide ->
