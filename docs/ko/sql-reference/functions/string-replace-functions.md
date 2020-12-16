---
machine_translated : true
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd
toc_priority : 42
toc_title : "\ u6587 \ u5B57 \ u5217 \ u3067 \ u7F6E \ u63DB \ u3059 \ u308B \ u5834 \ u5408"
---

# 문자열 찾기 및 바꾸기위한 함수 {# functions-for-searching-and-replacing-in-strings}

## replaceOne (건초, 패턴, 교체) {# replaceonehaystack-pattern-replacement}

그것이 존재하는 경우는 처음 외관을 바꿉니다. 'pattern'하위 문자열 'haystack'와 'replacement'부분 문자열.
이후 'pattern'와 'replacement'상수이어야합니다.

## replaceAll (건초, 패턴, 교체) replace (건초, 패턴, 교체) {# replaceallhaystack-pattern-replacement-replacehaystack-pattern-replacement}

모든 사건을 바꿉니다. 'pattern'하위 문자열 'haystack'와 'replacement'부분 문자열.

## replaceRegexpOne (건초, 패턴, 교체) {# replaceregexponehaystack-pattern-replacement}

를 사용하여 교체 'pattern'정규 표현식. Re2 정규식.
존재하는 경우는 처음 외관 만 바꿉니다.
패턴은 다음과 같이 지정할 수 있습니다 'replacement'이 퍼터`\ 0- \ 9`.
대체`\ 0` 전체 정규 표현식을 포함합니다. 대체`\ 1- \ 9` 서브 패턴에 대응하는 numbers.To 사용하는`\`템플릿`\`.
또한 문자열 리터럴에는 추가 이스케이프가 필요합니다.

예 1 날짜를 미국 형식으로 변환 :

```sql
SELECT DISTINCT
    EventDate,
    replaceRegexpOne (toString (EventDate) '(\\ d {4}) - (\\ d {2}) - (\\ d {2})', '\\ 2 / \\ 3 / \\ 1') AS res
FROM test.hits
LIMIT 7
FORMAT TabSeparated
```

```text
2014-03-17 03/17/2014
2014-03-18 03/18/2014
2014-03-19 03/19/2014
2014-03-20 03/20/2014
2014-03-21 03/21/2014
2014-03-22 03/22/2014
2014-03-23 ​​03/23/2014
```

예 2. 문자열을 열 번 복사 :

```sql
SELECT replaceRegexpOne ( 'Hello, World!', '*', '\\ 0 \\ 0 \\ 0 \\ 0 \\ 0 \\ 0 \\ 0 \\ 0 \\ 0 \\ 0') AS res
```

```text
┌─res─────────────────────────────────────────────── ────────────────────────────────────────────────── ───────────────────────────────┐
│ Hello, World! Hello, World! Hello, World! Hello, World! Hello, World! Hello, World! Hello, World! Hello, World! Hello, World! Hello, World! │
└───────────────────────────────────────────────── ────────────────────────────────────────────────── ─────────────────────────────────┘
```

## replaceRegexpAll (건초 더미, 패턴, 교체) {# replaceregexpallhaystack-pattern-replacement}

이것은 동일한 작업을 수행하지만 모든 사건을 바꿉니다. 예 :

```sql
SELECT replaceRegexpAll ( 'Hello, World!', '.', '\\ 0 \\ 0') AS res
```

```text
┌─res────────────────────────┐
│ HHeelllloo ,, WWoorrlldd! │
└────────────────────────────┘
```

예외로 정규식이 빈 부분 문자열에 대해 작동하는 경우 대체가 여러 번 발생하지 않습니다.
예 :

```sql
SELECT replaceRegexpAll ( 'Hello, World!', '^', 'here :') AS res
```

```text
┌─res─────────────────┐
│ here : Hello, World! │
└─────────────────────┘
```

## regexpQuoteMeta (s) {#regexpquotemetas}

함수는 문자열의 몇 가지 미리 정의 된 문자 앞에 백 슬래시를 추가합니다.
미리 정의 된 문자 : '0', '\\', '\ |' '(' ')', '^', '$', '.', '\' '\' ','? ''\ * ','+ ','{ ',' ','- '.
이 구현은 re2 : RE2 :: QuoteMeta과는 약간 다릅니다. 0 바이트는 00 대신 \\ 0으로 이스케이프 필요한 문자 만 이스케이프됩니다.
자세한 내용은 링크를 참조하십시오 : RE2 (https://github.com/google/re2/blob/master/re2/re2.cc#L473)

원본 기사 (https://clickhouse.tech/docs/en/query_language/functions/string_replace_functions/) <! - hide ->
