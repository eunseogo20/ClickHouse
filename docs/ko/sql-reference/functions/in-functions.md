---
machine_translated : true
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd
toc_priority : 60
toc_title : "IN \ u6F14 \ u7B97 \ u5B50 \ u306E \ u5B9F \ u88C5"
---

# IN 연산자를 구현하는 함수 {# functions-for-implementing the-in-operator}

##에서 노찐, 구로바린, 구로바루노찐 {# in-functions}

섹션을 참조 [연산자] (../ operators / in.md # select-in-operators).

## tuple (x, y, ...), operator (x, y, ...) {# tuplex-y-operator-xy}

여러 열을 그룹화 할 수있는 기능입니다.
For columns with the types T1, T2, ..., it returns a Tuple (T1, T2, ...) type tuple containing these columns. There is no cost to execute the function.
튜플은 일반적으로 in 연산자의 인수의 중간 값으로 또는 람다 함수의 파라미터 목록을 작성하는 데 사용됩니다. 튜플을 테이블에 쓸 수 없습니다.

## tupleElement (튜플 n) 연산자 xN {# tupleelementtuple-n-operator-xn}

튜플에서 열을 취득 할 수있는 기능입니다.
'N'1부터 시작하는 열 인덱스입니다. N은 정수가 아니면 안됩니다. 'N'상수이어야합니다. 'N'세트의 크기보다 크지 않다 정확한 postive 정수 여야합니다.
함수를 실행하는 비용은 없습니다.

원본 기사 (https://clickhouse.tech/docs/en/query_language/functions/in_functions/) <! - hide ->
