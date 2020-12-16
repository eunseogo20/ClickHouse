---
machine_translated : true
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd
toc_priority : 41
toc_title : "\ u6587 \ u5B57 \ u5217 \ u3092 \ u691C \ u7D22 \ u3059 \ u308B \ u5834 \ u5408"
---

# 문자열을 검색하는 함수 {# functions-for-searching-strings}

이러한 모든 함수는 기본적으로 대소 문자를 구분합니다. 있는 variant에 대한 대소 문자를 구별하지 않습니다.

## 위치 (건초 더미 바늘), 위치 (건초 더미 바늘) {#position}

문자열에서 발견 된 부분 문자열의 위치 (바이트 단위)를 1부터 반환합니다.

작품이 문자열이 포함되는 세트를 나타내는 바이트의 단일 바이트 인코딩됩니다. 이 가정이 충족되지 않고 문자를 단일 바이트로 표현할 수없는 경우, 함수는 예외를 throw하지 않고 예기치 않은 결과를 반환합니다. 문자를 두 바이트로 표현할 수있는 경우는 두 바이트 등을 사용합니다.

대문자와 소문자를 구별하지 않는 검색에서는 다음 함수를 사용합니다 [포지션 커스 인센티브 (# positioncaseinsensitive).

** 구문 **

```sql
position (haystack, needle [, start_pos)
```

일명`locate (haystack, needle [, start_pos)`.

** 파라미터 **

-`haystack` - string, in which substring will to be searched [문자열] (../ syntax.md # syntax-string-literal).
-`needle` - substring to be searched [문자열] (../ syntax.md # syntax-string-literal).
-`start_pos` - Optional parameter, position of the first character in the string to start search [UInt (../../ sql-reference / data-types / int-uint.md)

** 반환 값 **

- 부분 문자열이 발견 된 경우, 시작 위치 (1에서 계산)를 바이트 단위로 지정합니다.
- 부분 문자열이 발견되지 않았던 경우는 0.

유형 :`Integer`.

** 예 **

문구 "Hello, world!"를 포함하는 구성을 나타내는 바이트의 단일 바이트 인코딩됩니다. 이 함수는 예상 된 결과를 반환합니다 :

쿼리 :

```sql
SELECT position ( 'Hello, world!', '!')
```

결과 :

```text
┌─position ( 'Hello, world!', '!') ─┐
│ 13 │
└────────────────────────────────┘
```

러시아어 동일한 어구는 단일 바이트로 표현할 수없는 문자가 포함되어 있습니다. 이 함수는 예기치 않은 결과를 반환합니다 (사용 [positionUTF8 (# positionutf8) 멀티 바이트 인코딩 텍스트 함수) :

쿼리 :

```sql
SELECT position ( 'Привет, мир!', '!')
```

결과 :

```text
┌─position ( 'Привет, мир!', '!') ─┐
│ 21 │
└───────────────────────────────┘
```

## 포지션 커스 인센티브 {#positioncaseinsensitive}

같은 [위치] (# position) 문자열에서 발견 된 부분 문자열의 위치 (바이트 단위)를 1부터 반환합니다. 대문자와 소문자를 구별하지 않는 검색이 함수를 사용합니다.

작품이 문자열이 포함되는 세트를 나타내는 바이트의 단일 바이트 인코딩됩니다. 이 가정이 충족되지 않고 문자를 단일 바이트로 표현할 수없는 경우, 함수는 예외를 throw하지 않고 예기치 않은 결과를 반환합니다. 문자를 두 바이트로 표현할 수있는 경우는 두 바이트 등을 사용합니다.

** 구문 **

```sql
positionCaseInsensitive (haystack, needle [, start_pos)
```

** 파라미터 **

-`haystack` - string, in which substring will to be searched [문자열] (../ syntax.md # syntax-string-literal).
-`needle` - substring to be searched [문자열] (../ syntax.md # syntax-string-literal).
-`start_pos` - Optional parameter, position of the first character in the string to start search [UInt (../../ sql-reference / data-types / int-uint.md)

** 반환 값 **

- 부분 문자열이 발견 된 경우, 시작 위치 (1에서 계산)를 바이트 단위로 지정합니다.
- 부분 문자열이 발견되지 않았던 경우는 0.

유형 :`Integer`.

** 예 **

쿼리 :

```sql
SELECT positionCaseInsensitive ( 'Hello, world!', 'hello')
```

결과 :

```text
┌─positionCaseInsensitive ( 'Hello, world!', 'hello') ─┐
│ 1 │
└───────────────────────────────────────────────── ──┘
```

## positionUTF8 {# positionutf8}

문자열에서 발견 된 부분 문자열의 위치 (Unicode 포인트)를 1부터 반환합니다.

작품이 문자열이 포함되는 세트를 나타내는 바이트 UTF-8로 encode됩니다. 이 가정이 충족되지 않을 경우, 함수는 예외를 throw하지 않고 예기치 않은 결과를 반환합니다. 문자가 두 Unicode 포인트를 사용하여 나타낼 수 있다면 그것은처럼 두를 사용합니다.

대문자와 소문자를 구별하지 않는 검색에서는 다음 함수를 사용합니다 [positionCaseInsensitiveUTF8 (# positioncaseinsensitiveutf8).

** 구문 **

```sql
positionUTF8 (haystack, needle [, start_pos)
```

** 파라미터 **

-`haystack` - string, in which substring will to be searched [문자열] (../ syntax.md # syntax-string-literal).
-`needle` - substring to be searched [문자열] (../ syntax.md # syntax-string-literal).
-`start_pos` - Optional parameter, position of the first character in the string to start search [UInt (../../ sql-reference / data-types / int-uint.md)

** 반환 값 **

- 부분 문자열이 발견되면 Unicode 포인트의 시작 위치 (1에서 계산).
- 부분 문자열이 발견되지 않았던 경우는 0.

유형 :`Integer`.

** 예 **

문구 "Hello, world!"러시아어의 Unicode 포인트를 나타내는 단일 지점에서 인코딩됩니다. 이 함수는 예상 된 결과를 반환합니다 :

쿼리 :

```sql
SELECT positionUTF8 ( 'Привет, мир!', '!')
```

결과 :

```text
┌─positionUTF8 ( 'Привет, мир!', '!') ─┐
│ 12 │
└───────────────────────────────────┘
```

문구 "Salut, étudiante!"여기에서 문자`é` 한점을 사용하여 표현 할 수 있습니다 (`U + 00E9`) 또는 두 점 (`U + 0065U + 0301`) 함수는 예기치 않은 결과를 반환 수 있습니다 :

편지의 쿼리`é` 이것은 하나의 Unicode 포인트로 표시됩니다`U + 00E9` :

```sql
SELECT positionUTF8 ( 'Salut, étudiante!', '!')
```

결과 :

```text
┌─positionUTF8 ( 'Salut, étudiante!', '!') ─┐
│ 17 │
└────────────────────────────────────────┘
```

편지의 쿼리`é` 이것은 두 Unicode 포인트로 표시됩니다`U + 0065U + 0301` :

```sql
SELECT positionUTF8 ( 'Salut, étudiante!', '!')
```

결과 :

```text
┌─positionUTF8 ( 'Salut, étudiante!', '!') ─┐
│ 18 │
└────────────────────────────────────────┘
```

## positionCaseInsensitiveUTF8 {# positioncaseinsensitiveutf8}

과 동일 positionUTF8 (# positionutf8) 그러나, 대문자와 소문자는 구별되지 않습니다. 문자열에서 발견 된 부분 문자열의 위치 (Unicode 포인트)를 1부터 반환합니다.

작품이 문자열이 포함되는 세트를 나타내는 바이트 UTF-8로 encode됩니다. 이 가정이 충족되지 않을 경우, 함수는 예외를 throw하지 않고 예기치 않은 결과를 반환합니다. 문자가 두 Unicode 포인트를 사용하여 나타낼 수 있다면 그것은처럼 두를 사용합니다.

** 구문 **

```sql
positionCaseInsensitiveUTF8 (haystack, needle [, start_pos)
```

** 파라미터 **

-`haystack` - string, in which substring will to be searched [문자열] (../ syntax.md # syntax-string-literal).
-`needle` - substring to be searched [문자열] (../ syntax.md # syntax-string-literal).
-`start_pos` - Optional parameter, position of the first character in the string to start search [UInt (../../ sql-reference / data-types / int-uint.md)

** 반환 값 **

- 부분 문자열이 발견되면 Unicode 포인트의 시작 위치 (1에서 계산).
- 부분 문자열이 발견되지 않았던 경우는 0.

유형 :`Integer`.

** 예 **

쿼리 :

```sql
SELECT positionCaseInsensitiveUTF8 ( 'Привет, мир!', 'Мир')
```

결과 :

```text
┌─positionCaseInsensitiveUTF8 ( 'Привет, мир!', 'Мир') ─┐
│ 9 │
└───────────────────────────────────────────────── ───┘
```

## 멀티 검색 알 포지션 {#multisearchallpositions}

같은 [위치] (string-search-functions.md # position)하지만 돌아`Array` 문자열 내에서 발견 된 해당 부분 문자열의 위치 (바이트 단위). 위치는 1부터 시작 인덱스입니다.

검색을 실시해, 배열의 바이트를 존중하지 않고 문자열 인코딩과 일치.

- 대소 문자를 구별하지 않는 ASCII 검색에서는 다음 함수를 사용합니다`multiSearchAllPositionsCaseInsensitive`.
- UTF-8로 검색하려면 다음 함수를 사용합니다 [multiSearchAllPositionsUTF8 (# multiSearchAllPositionsUTF8).
- 대소 문자를 구별하지 않는 UTF-8 검색은 함수 multiSearchAllPositionsCaseInsensitiveutf8을 사용합니다.

** 구문 **

```sql
multiSearchAllPositions (haystack, needle1, needle2, ..., needlen)
```

** 파라미터 **

-`haystack` - string, in which substring will to be searched [문자열] (../ syntax.md # syntax-string-literal).
-`needle` - substring to be searched [문자열] (../ syntax.md # syntax-string-literal).

** 반환 값 **

- 해당 부분 문자열이 발견 된 경우는 1부터 시작합니다.

** 예 **

쿼리 :

```sql
SELECT multiSearchAllPositions ( 'Hello, World!', 'hello', '!', 'world'])
```

결과 :

```text
┌─multiSearchAllPositions ( 'Hello, World!', 'hello', '!', 'world') ─┐
│ [0,13,0] │
└───────────────────────────────────────────────── ──────────────────┘
```

## multiSearchAllPositionsUTF8 {# multiSearchAllPositionsUTF8}

볼`multiSearchAllPositions`.

## 멀티 검색 퍼스트 포지션 (헤이 스택, \ 바늘 <sub> 1 </ sub> 바늘 <sub> 2 </ sub>, ..., needle <sub> n </ sub> \]) {#multisearchfirstposition}

같은`position`하지만 문자열의 왼쪽 끝의 오프셋을 돌려줍니다`haystack` 그것은 바늘의 일부와 일치합니다.

대문자와 소문자를 구별하지 않는 검색 또는 / 및 UTF-8 형식의 경우 함수를 사용합니다`multiSearchFirstPositionCaseInsensitive, multiSearchFirstPositionUTF8, multiSearchFirstPositionCaseInsensitiveUTF8`.

## 멀티 검색 퍼스트 인덱스 (haystack \ [needle <sub> 1 </ sub> 바늘 <sub> 2 </ sub>, ..., needle <sub> n </ sub> \]) {# multisearchfirstindexhaystack-needle1 -needle2-needlen}

인덱스를 돌려`i` 가장 왼쪽에있는 바늘 1부터 시작 <sub> 나는 </ sub> 문자열에서`haystack` 그렇지 않으면 0입니다.

대문자와 소문자를 구별하지 않는 검색 또는 / 및 UTF-8 형식의 경우 함수를 사용합니다`multiSearchFirstIndexCaseInsensitive, multiSearchFirstIndexUTF8, multiSearchFirstIndexCaseInsensitiveUTF8`.

## 멀티 검색 충전기 니 (건초, \ 바늘 <sub> 1 </ sub> 바늘 <sub> 2 </ sub>, ..., needle <sub> n </ sub> \]) {# function-multisearchany}

1을 반환합니다. <sub> 나는 </ sub> 문자열과 일치합니다`haystack` 그렇지 않으면 0입니다.

대문자와 소문자를 구별하지 않는 검색 또는 / 및 UTF-8 형식의 경우 함수를 사용합니다`multiSearchAnyCaseInsensitive, multiSearchAnyUTF8, multiSearchAnyCaseInsensitiveUTF8`.

!!! note "주"
    모든`multiSearch *`기능 바늘의 수는 2 이하이어야합니다 <sup> 8 </ sup> 구현 사양을위한.

## 경기 (동음이의) {# matchhaystack-pattern}

문자열이`pattern` 정규식. A`re2` 정규식. 그 [구문] (https://github.com/google/re2/wiki/Syntax)의`re2` 정규 표현식은 Perl 정규식 구문보다 제한되어 있습니다.

일치하지 않는 경우는 0, 일치하면 1을 반환합니다.

백 슬래시는 (`\`)는 정규식에서 탈출에 사용됩니다. 문자열 리터럴 탈출에 동일한 기호가 사용됩니다. 따라서 정규 표현식의 기호를 탈출하려면 문자열 리터럴에 두 백 슬래시 (\\)를 기술 할 필요가 있습니다.

정규 표현식은 문자열이 바이트 세트 인 것처럼 작동합니다. 정규식에는 null 바이트를 포함 할 수 없습니다.
문자열의 부분 문자열을 검색하는 패턴의 경우, LIKE 또는를 사용하는 것이 좋다 'position'그들은 훨씬 빠르게 작동하기 때문에.

## 멀티 매치 충전기 니 (동음 <sub> 1 </ sub> 패턴 <sub> 2 </ sub>, ..., pattern <sub> n </ sub> \]) {# multimatchanyhaystack-pattern1-pattern2- patternn}

같은`match` 그러나 정규식이 일치하지 않는 경우는 0을 반환하고 패턴이 일치하는 경우는 1을 돌려줍니다. 그것은 사용합니다 [hyperscan (https://github.com/intel/hyperscan) 도서관 패턴이 문자열의 부분 문자열을 검색하려면 다음을 사용하는 것이 좋습니다`multiSearchAny` 훨씬 고속으로 동작하기 때문에.

!!! note "주"
    하나의 길이`haystack` 문자열은 2 미만이어야합니다 <sup> 32 </ sup> 그렇지 않으면 예외가 throw됩니다. 이 제한은 hyperscan API 위해 이루어집니다.

## multiMatchAnyIndex (헤이 스택 \ 패턴 <sub> 1 </ sub> 패턴 <sub> 2 </ sub>, ..., pattern <sub> n </ sub> \]) {# multimatchanyindexhaystack-pattern1-pattern2 -patternn}

같은`multiMatchAny` 그러나 건초 더미에 일치하는 임의의 인덱스를 돌려줍니다.

## multiMatchAllIndices (건초 더미 \ 패턴 <sub> 1 </ sub> 패턴 <sub> 2 </ sub>, ..., pattern <sub> n </ sub> \]) {# multimatchallindiceshaystack-pattern1- pattern2-patternn}

같은`multiMatchAny` 그러나 건초 더미에 일치하는 모든 지표의 배열을 임의의 순서로 돌려줍니다.

## 멀티 후지 매치 충전기 니 (건초, 거리, \ 패턴 <sub> 1 </ sub> 패턴 <sub> 2 </ sub>, ..., pattern <sub> n </ sub> \]) {#multifuzzymatchanyhaystack -distance-pattern1-pattern2-patternn}

같은`multiMatchAny` 그러나 상수의 건초에 일치하는 패턴이 있으면 1을 반환합니다 [거리를 편집 (https://en.wikipedia.org/wiki/Edit_distance)이 기능은 실험 모드이기도 매우 느려질 수 있습니다. 자세한 내용은 hyperscan 문서 (https://intel.github.io/hyperscan/dev-reference/compilation.html#approximate-matching).

## multiFuzzyMatchAnyIndex (건초 더미, 거리, \ 패턴 <sub> 1 </ sub> 패턴 <sub> 2 </ sub>, ..., pattern <sub> n </ sub> \]) {# multifuzzymatchanyindexhaystack- distance-pattern1-pattern2-patternn}

같은`multiFuzzyMatchAny` 그러나 일정한 편집 거리의 건초 더미에 일치하는 인덱스를 돌려줍니다.

## multiFuzzyMatchAllIndices (건초 더미, 거리, \ 패턴 <sub> 1 </ sub> 패턴 <sub> 2 </ sub>, ..., pattern <sub> n </ sub> \]) {# multifuzzymatchallindiceshaystack- distance-pattern1-pattern2-patternn}

같은`multiFuzzyMatchAny`, but는 일정한 편집 거리의 건초 더미에 일치하는 임의의 순서로 모든 인덱스의 배열을 돌려줍니다.

!!! note "주"
    `multiFuzzyMatch *`함수는 UTF-8 정규 표현식을 지원하지 않고 하이퍼 스캔 제한으로 인해 이러한 식은 바이트로 처리됩니다.

!!! note "주"
    Hyperscan을 사용하는 모든 기능을 해제하려면 설정을 사용합니다`SET allow_hyperscan = 0;`.

## 추출 (건초, 패턴) {# extracthaystack-pattern}

정규식을 사용하여 문자열의 조각을 추출합니다. 만약 'haystack'일치하지 않는 'pattern'정규 표현식은 빈 문자열이 반환됩니다. 정규 표현식에 서브 패턴이 포함되지 않은 경우는 전체 정규 표현식에 일치하는 조각을 가지고 있습니다. 그렇지 않으면 첫 번째 서브 패턴과 일치하는 조각을 가지고 있습니다.

## extractAll (건초 더미 패턴) {# extractallhaystack-pattern}

정규식을 사용하여 문자열의 모든 조각을 추출합니다. 만약 'haystack'일치하지 않는 'pattern'정규 표현식은 빈 문자열이 반환됩니다. 정규식에 일치하는 모든 문자열의 배열을 돌려줍니다. 일반적으로, 동작은 'extract'함수 (첫 번째 서브 패턴을 가지고 하위 패턴이없는 경우는 전체 식을 취합니다).

## like (건초 더미 패턴) 건초 더미 같은 패턴 연산자 {# function-like}

문자열이 간단한 정규식과 일치하는지 여부를 확인합니다.
정규 표현식 메타 기호를 포함 할 수 있습니다`%`와`_`.

`%`임의의 바이트 수 (제로 문자 포함)를 보여줍니다.

`_` 모든 바이트를 나타냅니다.

백 슬래시를 사용하는 (`\`) 메타 기호를 탈출하기 위해. 설명의 탈출에 관한 경고를 참조하십시오. 'match'기능.

다음과 같은 정규 표현식의 경우`% needle %`코드는 더 적합하며만큼 빠르게 작동합니다`position` 기능.
다른 정규식의 경우 코드는 'match'기능.

## notLike (haystack, pattern) haystack NOT LIKE pattern 연산자 {# function-notlike}

같은 것으로 'like'그러나 부정적인.

## 그램 디스턴스 (동음이의) {# ngramdistancehaystack-needle}

동안 4 그램의 거리를 계산합니다`haystack`와`needle` : counts the symmetric difference between two multisets of 4-grams and normalizes it by the sum of their cardinalities. Returns float number from 0 to 1 - the closer to zero, the more strings are similar to each other. If the constant`needle` 또는`haystack` 32Kb를 초과하는 경우에는 예외를 throw합니다. 비 상수의 몇 가지 경우`haystack` 또는`needle` 문자열은 32Kb를 넘어 거리는 항상 하나입니다.

대문자와 소문자를 구별하지 않는 검색 및 UTF-8 형식의 경우 함수를 사용합니다`ngramDistanceCaseInsensitive, ngramDistanceUTF8, ngramDistanceCaseInsensitiveUTF8`.

## 그램 검색 (동음이의) {# ngramsearchhaystack-needle}

같은`ngramDistance` 그러나 사이의 비대칭 차이를 계산합니다`needle`와`haystack` - the number of n-grams from needle minus the common number of n-grams normalized by the number of`needle` n 그램 가까운 수록 가능성이 높아집니다`needle`에있는`haystack`. 모호한 문자열 검색에 도움이됩니다.

대문자와 소문자를 구별하지 않는 검색 및 UTF-8 형식의 경우 함수를 사용합니다`ngramSearchCaseInsensitive, ngramSearchUTF8, ngramSearchCaseInsensitiveUTF8`.

!!! note "주"
    For UTF-8 case we use 3-gram distance. All these are not perfectly fair n-gram distances. We use 2-byte hashes to hash n-grams and then calculate the (non-) symmetric difference between these hash tables - collisions may occur. With UTF-8 case-insensitive format we do not use fair`tolower` function - we zero the 5-th bit (starting from zero) of each codepoint byte and first bit of zeroth byte if bytes more than one - this works for Latin and mostly for all Cyrillic letters.

원본 기사 (https://clickhouse.tech/docs/en/query_language/functions/string_search_functions/) <! - hide ->
