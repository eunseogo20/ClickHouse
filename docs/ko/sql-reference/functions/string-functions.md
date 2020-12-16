---
machine_translated : true
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd
toc_priority : 40
toc_title : "\ u6587 \ u5B57 \ u5217 \ u306E \ u64CD \ u4F5C"
---

# 문자열을 조작하는 함수 {# functions-for-working-with-strings}

## 하늘 {#empty}

빈 문자열의 경우 1 비어 있지 않은 문자열의 경우는 0을 돌려줍니다.
결과 형식은 UInt8입니다.
문자열이 공백 또는 null 바이트라도 적어도 하나의 바이트가 포함되어있는 경우 문자열은 비어 있지 간주됩니다.
이 함수는 배열에 대해서도 작동합니다.

## 노텐뿌티 {#notempty}

빈 문자열의 경우는 0, 비어 있지 않은 문자열의 경우 1을 반환합니다.
결과 형식은 UInt8입니다.
이 함수는 배열에 대해서도 작동합니다.

## 길이 {#length}

문자열의 길이를 바이트 단위로 돌려줍니다 (문자 대신 코드 포인트는 없습니다).
결과 형식은 UInt64입니다.
이 함수는 배열에 대해서도 작동합니다.

## lengthUTF8 {# lengthutf8}

문자열에 UTF-8로 인코딩 된 텍스트를 구성하는 바이트의 세트가 포함되어 있다고 가정하여 Unicode 코드 포인트 (문자가 아닌) 문자열의 길이를 반환이 가정이 충족되지 않으면, 결과가 반환됩니다 (예외는 throw되지 않습니다).
결과 형식은 UInt64입니다.

## char_length, CHAR_LENGTH {# char-length}

문자열에 UTF-8로 인코딩 된 텍스트를 구성하는 바이트의 세트가 포함되어 있다고 가정하여 Unicode 코드 포인트 (문자가 아닌) 문자열의 길이를 반환이 가정이 충족되지 않으면, 결과가 반환됩니다 (예외는 throw되지 않습니다).
결과 형식은 UInt64입니다.

## character_length, CHARACTER_LENGTH {# character-length}

문자열에 UTF-8로 인코딩 된 텍스트를 구성하는 바이트의 세트가 포함되어 있다고 가정하여 Unicode 코드 포인트 (문자가 아닌) 문자열의 길이를 반환이 가정이 충족되지 않으면, 결과가 반환됩니다 (예외는 throw되지 않습니다).
결과 형식은 UInt64입니다.

## lower, lcase {#lower}

문자열의 ASCII 라틴 문자 기호를 소문자로 변환합니다.

## 어퍼, ucase {#upper}

문자열의 ASCII 라틴 문자 기호를 대문자로 변환합니다.

## lowerUTF8 {# lowerutf8}

문자열에 UTF-8로 인코딩 된 텍스트를 구성하는 바이트의 세트가 포함되어 있다고 가정하여 문자열을 소문자로 변환합니다.
그것은 언어를 감지하지 않습니다. 따라서 터키에 결과가 정확하게 맞다.
UTF-8 바이트 - 시퀀스의 길이가 코드 - 포인트의 대문자와 소문자 다를이 코드 - 포인트의 결과가 정확하지 않을 수 있습니다.
문자열 UTF-8 이외의 바이트 세트가 포함되어있는 경우, 동작은 정의되지 않습니다.

## upperUTF8 {# upperutf8}

문자열에 UTF-8로 인코딩 된 텍스트를 구성하는 바이트의 세트가 포함되어 있다고 가정하여 문자열을 대문자로 변환합니다.
그것은 언어를 감지하지 않습니다. 따라서 터키에 결과가 정확하게 맞다.
UTF-8 바이트 - 시퀀스의 길이가 코드 - 포인트의 대문자와 소문자 다를이 코드 - 포인트의 결과가 정확하지 않을 수 있습니다.
문자열 UTF-8 이외의 바이트 세트가 포함되어있는 경우, 동작은 정의되지 않습니다.

## isValidUTF8 {# isvalidutf8}

바이트 세트가 유효한 UTF-8 인코딩의 경우는 1을 반환하고, 그렇지 않으면 0을 반환합니다.

## toValidUTF8 {# tovalidutf8}

잘못된 UTF-8 문자를``(U + FFFD) 문자. 행에서 실행되는 모든 잘못된 문자는 하나의 대체 문자로 축소됩니다.

```sql
toValidUTF8 (input_string)
```

파라미터 :

- input_string - Any set of bytes represented as the 문자열 (../../ sql-reference / data-types / string.md) 데이터 형 객체.

반환 값 : 유효한 UTF-8 문자열.

** 예 **

```sql
SELECT toValidUTF8 ( '\ x61 \ xF0 \ x80 \ x80 \ x80b')
```

```text
┌─toValidUTF8 ( 'ab') ─┐
│ ab │
└───────────────────────┘
```

## 반복 {#repeat}

지정한 횟수만큼 문자열을 반복 복제 된 값을 하나의 문자열로 연결합니다.

** 구문 **

```sql
repeat (s, n)
```

** 파라미터 **

-`s` - The string to repeat [문자열] (../../ sql-reference / data-types / string.md).
-`n` - The number of times to repeat the string [UInt (../../ sql-reference / data-types / int-uint.md).

** 반환 값 **

문자열을 포함하는 단일 문자열`s` 반복`n` 타임즈 만약`n` \ <1 함수는 빈 문자열을 반환합니다.

유형 :`String`.

** 예 **

쿼리 :

```sql
SELECT repeat ( 'abc', 10)
```

결과 :

```text
┌─repeat ( 'abc', 10) ──────────────┐
│ abcabcabcabcabcabcabcabcabcabc │
└────────────────────────────────┘
```

## 역 {#reverse}

문자열 (바이트 순서로) 반전됩니다.

## reverseUTF8 {# reverseutf8}

문자열 UTF-8 텍스트를 나타내는 바이트의 세트가 포함되어 있다고 가정하여 Unicode 코드 포인트의 순서를 반전합니다. 그렇지 않으면, 뭔가 다른 일을합니다 (예외는 throw되지 않습니다).

## format (pattern, s0, s1, ...) {#format}

인수에 나열된 문자열 상수 패턴을 서식합니다. `pattern` 단순화 된 Python 형식의 패턴입니다. 형식 문자열 "replacement fields"중괄호로 둘러싸`{}`중괄호에 포함되지 않은 것은 리터럴 텍스트로 간주 그대로 출력에 복사됩니다. 리터럴 텍스트에 중괄호 문자를 포함 할 필요가있는 경우는 배가하여 탈출 있습니다 :`{{ '{{'}}`와`{{ '}}'}}`필드 이름은 수치 (제로로부터 시작된다) 또는 빈 (결과의 수치로 취급됩니다)을 지정할 수 있습니다.

```sql
SELECT format ( '{1} {0} {1}', 'World', 'Hello')
```

```text
┌─format ( '{1} {0} {1}', 'World', 'Hello') ─┐
│ Hello World Hello │
└─────────────────────────────────────────┘
```

```sql
SELECT format ( '{} {}', 'Hello', 'World')
```

```text
┌─format ( '{} {}', 'Hello', 'World') ─┐
│ Hello World │
└───────────────────────────────────┘
```

## 콩카 {#concat}

인수에 나열되어있는 문자열을 구분 기호없이 연결합니다.

** 구문 **

```sql
concat (s1, s2, ...)
```

** 파라미터 **

String 형 또는 FixedString 형의 값.

** 반환 값 **

인수를 연결 한 결과 문자열을 반환합니다.

인수 값 중 하나가`NULL``concat` 쯔づゥ쯔. `NULL`.

** 예 **

쿼리 :

```sql
SELECT concat ( 'Hello', 'World!')
```

결과 :

```text
┌─concat ( 'Hello', 'World!') ─┐
│ Hello, World! │
└─────────────────────────────┘
```

## concatAssumeInjective {#concatassumeinjective}

같은 [콩카 (# concat) 차이는 다음을 확인해야한다는 것입니다`concat (s1, s2, ...) → sn`은 injective이며, GROUP BY 최적화에 사용됩니다 있습니다.

함수의 이름은 다음과 같습니다 "injective"인수 다른 값에 대해 항상 다른 결과를 돌려주는 경우. 즉 다른 인수가없는 수익률 동일한 결과입니다.

** 구문 **

```sql
concatAssumeInjective (s1, s2, ...)
```

** 파라미터 **

String 형 또는 FixedString 형의 값.

** 반환 값 **

인수를 연결 한 결과 문자열을 반환합니다.

인수 값 중 하나가`NULL``concatAssumeInjective` 쯔づゥ쯔. `NULL`.

** 예 **

입력 테이블 :

```sql
CREATE TABLE key_val (`key1` String`key2` String`value` UInt32) ENGINE = TinyLog;
INSERT INTO key_val VALUES ( 'Hello', 'World', 1), ( 'Hello', 'World', 2), ( 'Hello', 'World!', 3), ( 'Hello', ' , World! ', 2);
SELECT * from key_val;
```

```text
┌─key1────┬─key2─────┬─value─┐
│ Hello, │ World │ 1 │
│ Hello, │ World │ 2 │
│ Hello, │ World! │ 3 │
│ Hello │, World! │ 2 │
└─────────┴──────────┴───────┘
```

쿼리 :

```sql
SELECT concat (key1, key2), sum (value) FROM key_val GROUP BY concatAssumeInjective (key1, key2)
```

결과 :

```text
┌─concat (key1, key2) ─┬─sum (value) ─┐
│ Hello, World! │ 3 │
│ Hello, World! │ 2 │
│ Hello, World │ 3 │
└────────────────────┴────────────┘
```

## substring (s, offset, length), mid (s, offset, length) substr (s, offset, length) {#substring}

바이트로 시작하는 부분 문자열을 반환합니다. 'offset'인 인덱스 'length'바이트. 문자 산출 처음부터 하나로서 표준 SQL) 그 'offset'과 'length'인수는 정수 여야합니다.

## substringUTF8 (s, 오프셋, 길이) {# substringutf8}

같은 'substring'하지만 Unicode 코드 포인트의 경우. 작품이 문자열이 포함되는 세트를 나타내는 바이트 UTF-8로 encode됩니다. 이 가정이 충족되지 않으면 결과가 반환됩니다 (예외는 throw되지 않습니다).

## appendTrailingCharIfAbsent (s, c) {#appendtrailingcharifabsent}

만약 's'문자열은 하늘이 아니라 'c'마지막 문자를 추가합니다. 'c'마지막 문자.

## convertCharset (s, from, to) {#convertcharset}

캐릭터 라인을 돌려 's'그것은에서 코딩에서 변환 된 'from'인코딩에 'to'.

## base64Encode (s) {# base64encode}

인코딩 's'base64로 문자열

## base64Decode (s) {# base64decode}

Base64 인코딩 된 문자열을 디코딩 's'원래 문자열. 장애가 발생한 경우에는 예외를 발생시킵니다.

## tryBase64Decode (s) {# trybase64decode}

Base64Decode와 비슷하지만, 오류의 경우 빈 문자열이 반환됩니다.

## endsWith (s 접미사) {#endswith}

지정된 접미 말로 끝날지 어떨지를 돌려줍니다. 문자열이 지정된 접미사로 끝나는 경우 1을 반환하고, 그렇지 않으면 0을 반환합니다.

## startsWith (str, 접두사) {#startswith}

문자열이 지정된 접두사로 시작 될지 어떨지는 1을 돌려줍니다.

```sql
SELECT startsWith ( 'Spider-Man', 'Spi');
```

** 반환 값 **

- 문자열이 지정된 접두어로 시작되는 경우는 1.
- 문자열이 지정된 접두사로 시작되지 않으면 0.

** 예 **

쿼리 :

```sql
SELECT startsWith ( 'Hello, world!', 'He');
```

결과 :

```text
┌─startsWith ( 'Hello, world!', 'He') ─┐
│ 1 │
└───────────────────────────────────┘
```

## 트림 {#trim}

지정된 모든 문자를 문자열의 시작 또는 끝 부분에서 삭제합니다.
기본적으로 문자열의 끝에서 공통의 공백 (ASCII 문자 32)가 연속해서 출현하는 모든 것을 제거합니다.

** 구문 **

```sql
trim ([[LEADING | TRAILING | BOTH] trim_character FROM] input_string)
```

** 파라미터 **

-`trim_character` - specified characters for trim [문자열] (../../ sql-reference / data-types / string.md).
-`input_string` - string for trim [문자열] (../../ sql-reference / data-types / string.md).

** 반환 값 **

선행 및 (또는) 끝에 지정된 문자가 포함 된 문자열.

유형 :`String`.

** 예 **

쿼리 :

```sql
SELECT trim (BOTH '()'FROM '(Hello, world!)')
```

결과 :

```text
┌─trim (BOTH '()'FROM '(Hello, world!)') ─┐
│ Hello, world! │
└───────────────────────────────────────────────┘
```

## 토리무레후토 {#trimleft}

문자열의 처음부터 연속해서 출현하는 공통의 공백 (ASCII 문자 32)를 제거합니다. 다른 종류의 공백 (탭 끊기지 않는 공백 등)는 삭제되지 않습니다. ).

** 구문 **

```sql
trimLeft (input_string)
```

일명`ltrim (input_string)`.

** 파라미터 **

-`input_string` - string to trim [문자열] (../../ sql-reference / data-types / string.md).

** 반환 값 **

일반적인 빈 선두를 가지지 않는 문자열.

유형 :`String`.

** 예 **

쿼리 :

```sql
SELECT trimLeft ( 'Hello, world!')
```

결과 :

```text
┌─trimLeft ( 'Hello, world!') ─┐
│ Hello, world! │
└─────────────────────────────────────┘
```

## 트림 라이트 {#trimright}

문자열의 끝에서 연속해서 출현하는 모든 공통 공백 (ASCII 문자 32)를 제거합니다. 다른 종류의 공백 (탭 끊기지 않는 공백 등)는 삭제되지 않습니다. ).

** 구문 **

```sql
trimRight (input_string)
```

일명`rtrim (input_string)`.

** 파라미터 **

-`input_string` - string to trim [문자열] (../../ sql-reference / data-types / string.md).

** 반환 값 **

공통의 공백을 끝에 붙여 않은 문자열.

유형 :`String`.

** 예 **

쿼리 :

```sql
SELECT trimRight ( 'Hello, world!')
```

결과 :

```text
┌─trimRight ( 'Hello, world!') ─┐
│ Hello, world! │
└──────────────────────────────────────┘
```

## 토린보스 {#trimboth}

문자열의 끝에서 공통의 공백 (ASCII 문자 32)가 연속해서 출현하는 모든 것을 제거합니다. 다른 종류의 공백 (탭 끊기지 않는 공백 등)는 삭제되지 않습니다. ).

** 구문 **

```sql
trimBoth (input_string)
```

일명`trim (input_string)`.

** 파라미터 **

-`input_string` - string to trim [문자열] (../../ sql-reference / data-types / string.md).

** 반환 값 **

일반적인 공백 문자의 시작과 끝이없는 문자열.

유형 :`String`.

** 예 **

쿼리 :

```sql
SELECT trimBoth ( 'Hello, world!')
```

결과 :

```text
┌─trimBoth ( 'Hello, world!') ─┐
│ Hello, world! │
└─────────────────────────────────────┘
```

## CRC32 (s) {# crc32}

CRC32-IEEE802.3 다항식 및 초기 값을 사용하여 문자열의 CRC32 체크섬을 반환합니다`0xffffffff` (zlib 구현).

결과 형식은 UInt32입니다.

## CRC32IEEE (s) {# crc32ieee}

CRC32-IEEE802.3 다항식을 사용하여 문자열의 CRC32 체크섬을 반환합니다.

결과 형식은 UInt32입니다.

## CRC64 (s) {# crc64}

CRC64-ECMA 다항식을 사용하여 문자열의 CRC64 체크섬을 반환합니다.

결과 형식은 UInt64입니다.

원본 기사 (https://clickhouse.tech/docs/en/query_language/functions/string_functions/) <! - hide ->
