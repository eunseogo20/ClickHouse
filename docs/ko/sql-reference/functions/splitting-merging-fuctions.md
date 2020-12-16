---
machine_translated : true
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd
toc_priority : 47
toc_title : "\ u6587 \ u5B57 \ u5217 \ u3068 \ u914D \ u5217 \ u306E \ u5206 \ u5272 \ u3068 \ u30DE \ u30FC \
  \ u30B8 "
---

# 문자열 배열을 분할 및 결합하기위한 함수 {# functions-for-splitting-and-merging-strings-and-arrays}

## splitByChar (세퍼레이터, s) {# splitbycharseparator - s}

문자열을 지정한 문자로 구분 된 부분 문자열로 분할합니다. 상수 문자열을 사용합니다`separator` 이것은 정확히 하나의 문자로 구성됩니다.
선택한 부분 문자열의 배열을 돌려줍니다. 문자열의 시작 또는 끝 부분에 구분 기호가 있으면 이상의 연속 된 구분 문자가있는 경우는 빈 문자열을 선택할 수 있습니다.

** 구문 **

```sql
splitByChar (<separator>, <s>)
```

** 파라미터 **

-`separator` - The separator which should contain exactly one character [문자열] (../../ sql-reference / data-types / string.md).
-`s` - The string to split [문자열] (../../ sql-reference / data-types / string.md).

** 반환 값) **

선택한 부분 문자열의 배열을 돌려줍니다. 빈 부분 문자열은 다음의 경우에 선택할 수 있습니다 :

- 구분 기호는 문자열의 시작 또는 끝 부분에 있습니다;
- 여러 개의 연속 구분 문자가 있습니다;
- 원래 문자열`s` 비어 있습니다.

유형 : 배열 (../../ sql-reference / data-types / array.md)의 문자열 (../../ sql-reference / data-types / string.md).

** 예 **

```sql
SELECT splitByChar ( '', '1,2,3, abcde')
```

```text
┌─splitByChar ( '', '1,2,3, abcde') ─┐
│ [ '1', '2', '3', 'abcde'] │
└─────────────────────────────────┘
```

## splitByString (세퍼레이터, s) {# splitbystringseparator - s}

문자열을 문자열로 구분 된 부분 문자열로 분할합니다. 상수 문자열을 사용합니다`separator` 구분 기호로 여러 문자를 지정합니다. 문자열의 경우`separator` 비어 있으면 문자열을 나눕니다`s` 단일 문자의 배열.

** 구문 **

```sql
splitByString (<separator>, <s>)
```

** 파라미터 **

-`separator` - The separator [문자열] (../../ sql-reference / data-types / string.md).
-`s` - The string to split [문자열] (../../ sql-reference / data-types / string.md).

** 반환 값) **

선택한 부분 문자열의 배열을 돌려줍니다. 빈 부분 문자열은 다음의 경우에 선택할 수 있습니다 :

유형 : 배열 (../../ sql-reference / data-types / array.md)의 문자열 (../../ sql-reference / data-types / string.md).

- 하늘이 아닌 분리 문자는 문자열의 시작 또는 끝 부분에 있습니다;
- 연속 비어 있지 않은 구분자가 다수 있습니다;
- 원래 문자열`s` 구분자가 비어 있지 않은 사이가 비어 있습니다.

** 예 **

```sql
SELECT splitByString ( '', '1, 2 3, 4, 5 abcde')
```

```text
┌─splitByString ( '', '1, 2 3, 4, 5 abcde') ─┐
│ [ '1', '2 세', '4,5', 'abcde'] │
└───────────────────────────────────────────┘
```

```sql
SELECT splitByString ( '', 'abcde')
```

```text
┌─splitByString ( '', 'abcde') ─┐
│ [ 'a', 'b', 'c', 'd', 'e'] │
└────────────────────────────┘
```

## arrayStringConcat (arr \ 구분 기호 \]) {# arraystringconcatarr-separator}

배열에리스트 된 문자열을 구분자로 연결합니다. 'separator'는 선택적 매개 변수입니다 : 상수 문자열에서 기본적으로 빈 문자열로 설정됩니다.
문자열을 반환합니다.

## 알파 도현 스 (동음이의) {#alphatokenss}

Az 또는 AZ 범위에서 연속적인 바이트의 문자열을 선택합니다.

** 예 **

```sql
SELECT alphaTokens ( 'abca1abc')
```

```text
┌─alphaTokens ( 'abca1abc') ─┐
│ [ 'abca', 'abc'] │
└─────────────────────────┘
```

원본 기사 (https://clickhouse.tech/docs/en/query_language/functions/splitting_merging_functions/) <! - hide ->
