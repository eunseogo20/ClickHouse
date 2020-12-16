---
machine_translated : true
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd
toc_priority : 56
toc_title : "JSON \ u3067 \ u306E \ u4F5C \ u696D"
---

# JSON을 조작하는 함수 {# functions-for-working-with-json}

Yandex 때문에. Metrica, JSON에서 보낸 사용자로 세션 매개 변수. 이 JSON을 조작하기위한 특별한 함수가 몇 가지 있습니다. (대부분의 경우 Json은 또한 전처리 된 결과 값은 처리 된 형식에서 다른 컬럼에 넣을 수 있습니다.) 이러한 함수는 모두 JSON이 무엇인지에 대한 강한 전제에 근거하고 있습니다 만 가능한 한 적게하고 일을 끝내려고합니다.

다음과 같은 가정을합니다 :

1. 필드 이름 (함수의 인자)는 정수가 아니면 안됩니다.
2. 필드 이름은 어떻게 든 json으로 인코딩됩니다. 예를 들면 :`visitParamHas ( '{ "abc": "def"}', 'abc') = 1`하지만`visitParamHas ( '{'\\ u0061 \\ u0062 \\ u0063 ":"def "} ','abc ') = 0`
3. 필드는 임의의 중첩 수준에서 무차별 적으로 검색됩니다. 여러 일치하는 필드가 있으면 첫 번째 출현이 사용됩니다.
4. JSON으로 문자열 리터럴의 외부에는 공백이 없습니다.

## visitParamHas (빠라무스 이름) {# visitparamhasparams-name}

이 있는지 여부를 확인합니다. 'name'이름

## 비지트 매개 엑스트라 쿠츄 포인트 (params, name) {# visitparamextractuintparams-name}

이름 필드의 값을 UInt64을 분석합니다 'name'이것이 문자열 필드의 경우, 캐릭터 라인의 선두로부터 수치를 분석하려고합니다. 필드가 존재하지 않거나 존재하지만 수치가 포함되지 않은 경우는 0을 돌려줍니다.

## 비지트 매개 쿠스토 편 하니까 트 (params, name) {# visitparamextractintparams-name}

Int64와 동일합니다.

## 매개 변수) {# visitparamextractfloatparams-name}

Float64과 같습니다.

## 비지트 매개 쿠스토 락토 부울 (params, name) {# visitparamextractboolparams-name}

참 / 거짓 값을 분석합니다. 결과는 UInt8입니다.

## 비지트 매개 쿠스토 락토 로우 (params, name) {# visitparamextractrawparams-name}

구분자가있는 필드의 값을 돌려줍니다.

예 :

```sql
visitParamExtractRaw ( '{ "abc": "\\ n \\ u0000"}', 'abc') = ' "\\ n \\ u0000"'
visitParamExtractRaw ( '{ "abc": { "def": [1,2,3]}}', 'abc') = '{ "def": [1,2,3]}'
```

## 매개 변수를 지정합니다) {# visitparamextractstringparams-name}

큰 따옴표로 문자열을 분석합니다. 값은 이스케이프되어 있지 않습니다. 이스케이프 해제에 실패했을 경우 빈 문자열을 반환합니다.

예 :

```sql
visitParamExtractString ( '{ "abc": "\\ n \\ u0000"}', 'abc') = '\ n \ 0'
visitParamExtractString ( '{ "abc": "\\ u263a"}', 'abc') = '☺'
visitParamExtractString ( '{ "abc": "\\ u263'} ','abc ') =' '
visitParamExtractString ( '{ "abc": "hello}', 'abc') = ''
```

현재이 형식의 코드 포인트는 지원되지 않습니다`\ uXXXX \ uYYYY` 그것은 기본 다국어 평면에서하지 않습니다 (UTF-8 대신 CESU-8로 변환됩니다).

다음의 기능은 다음과 같습니다 [simdjson (https://github.com/lemire/simdjson)보다 복잡한 JSON 분석 요구 사항을 위해 설계되어 있습니다. 위의 가정 2는 아직 적용됩니다.

## isValidJSON (json) {#isvalidjsonjson}

전달 된 문자열이 유효한 json임을 확인합니다.

예 :

```sql
SELECT isValidJSON ( '{'a ': "hello", "b": -100, 200.0, 300]}') = 1
SELECT isValidJSON ( 'not a json') = 0
```

## JSONHas (json \, indices_or_keys \] ...) {# jsonhasjson-indices-or-keys}

값이 JSON 문서에 존재하는 경우,`1` 반환됩니다.

값이 존재하지 않는 경우`0` 반환됩니다.

예 :

```sql
SELECT JSONHas ( '{'a ': "hello", "b": -100, 200.0, 300]}', 'b') = 1
SELECT JSONHas ( '{'a ': "hello", "b": -100, 200.0, 300]}', 'b', 4) = 0
```

`indices_or_keys` 제로 이상의 인수 목록은 각 문자열 또는 정수 중 하나입니다.

- String = 키
- 양수 = 처음부터 n 번째 멤버 / 키에 액세스합니다.
- 음의 정수 = 마지막에서 n 번째 멤버 / 키에 액세스합니다.

요소의 최소 인덱스는 1입니다. 따라서 요소 0은 존재하지 않습니다.

정수를 사용하여 JSON 배열과 JSON 객체 모두에 액세스 할 수 있습니다.

예를 들면 :

```sql
SELECT JSONExtractKey ( '{'a ': "hello", "b": -100, 200.0, 300]}', 1) = 'a'
SELECT JSONExtractKey ( '{'a ': "hello", "b": -100, 200.0, 300]}', 2) = 'b'
SELECT JSONExtractKey ( '{'a ': "hello", "b": -100, 200.0, 300]}'-1) = 'b'
SELECT JSONExtractKey ( '{'a ': "hello", "b": -100, 200.0, 300]}', -2) = 'a'
SELECT JSONExtractString ( '{'a ': "hello", "b": -100, 200.0, 300]}', 1) = 'hello'
```

## JSONLength (json \, indices_or_keys \] ...) {# jsonlengthjson-indices-or-keys}

JSON 배열 또는 JSON 개체의 길이를 돌려줍니다.

값이 존재하지 않거나 형식이 잘못된 경우`0` 반환됩니다.

예 :

```sql
SELECT JSONLength ( '{'a ': "hello", "b": -100, 200.0, 300]}', 'b') = 3
SELECT JSONLength ( '{'a ': "hello", "b": -100, 200.0, 300]}') = 2
```

## JSONType (json \, indices_or_keys \] ...) {# jsontypejson-indices-or-keys}

JSON 값의 형태를 돌려줍니다.

값이 존재하지 않는 경우`Null` 반환됩니다.

예 :

```sql
SELECT JSONType ( '{'a ': "hello", "b": -100, 200.0, 300]}') = 'Object'
SELECT JSONType ( '{'a ': "hello", "b": -100, 200.0, 300]}' 'a') = 'String'
SELECT JSONType ( '{'a ': "hello", "b": -100, 200.0, 300]}', 'b') = 'Array'
```

## JSONExtractUInt (json \, indices_or_keys \] ...) {# jsonextractuintjson-indices-or-keys}

## JSONExtractInt (json \, indices_or_keys \] ...) {# jsonextractintjson-indices-or-keys}

## JSONExtractFloat (json \, indices_or_keys \] ...) {# jsonextractfloatjson-indices-or-keys}

## JSONExtractBool (json \, indices_or_keys \] ...) {# jsonextractbooljson-indices-or-keys}

JSON을 분석하여 값을 추출합니다. 이러한 기능과 유사한`visitParam` 기능.

값이 존재하지 않거나 형식이 잘못된 경우`0` 반환됩니다.

예 :

```sql
SELECT JSONExtractInt ( '{'a ': "hello", "b": -100, 200.0, 300]}', 'b', 1) = -100
SELECT JSONExtractFloat ( '{'a ': "hello", "b": -100, 200.0, 300]}', 'b', 2) = 200.0
SELECT JSONExtractUInt ( '{'a ': "hello", "b": -100, 200.0, 300]}', 'b', -1) = 300
```

## JSONExtractString (json \, indices_or_keys \] ...) {# jsonextractstringjson-indices-or-keys}

JSON을 분석하여 문자열을 추출합니다. 이 함수는 다음과 같습니다`visitParamExtractString` 기능.

값이 존재하지 않거나 형식이 잘못된 경우 빈 문자열이 반환됩니다.

값은 이스케이프되어 있지 않습니다. 이스케이프 해제에 실패했을 경우 빈 문자열을 반환합니다.

예 :

```sql
SELECT JSONExtractString ( '{'a ': "hello", "b": -100, 200.0, 300]}' 'a') = 'hello'
SELECT JSONExtractString ( '{ "abc": "\\ n \\ u0000"}', 'abc') = '\ n \ 0'
SELECT JSONExtractString ( '{ "abc": "\\ u263a"}', 'abc') = '☺'
SELECT JSONExtractString ( '{ "abc": "\\ u263'} ','abc ') =' '
SELECT JSONExtractString ( '{ "abc": "hello}', 'abc') = ''
```

## JSONExtract (json \, indices_or_keys ... \, Return_type) {# jsonextractjson-indices-or-keys-return-type}

JSON을 분석하고 지정된 ClickHouse 데이터 형의 값을 추출합니다.

이것은 이전의 것들의 일반화입니다`JSONExtract <type>`기능.
즉
`JSONExtract (... 'String')`와 동일을 반환합니다`JSONExtractString ()`
`JSONExtract (... 'Float64')`와 동일을 반환합니다`JSONExtractFloat ()`.

예 :

```sql
SELECT JSONExtract ( '{'a ': "hello", "b": -100, 200.0, 300]}' 'Tuple (String Array (Float64))') = ( 'hello', - 100,200,300] )
SELECT JSONExtract ( '{'a ': "hello", "b": -100, 200.0, 300]}' 'Tuple (b Array (Float64), a String)') = (-100,200,300, ' hello ')
SELECT JSONExtract ( '{'a ': "hello", "b": -100, 200.0, 300]}', 'b', 'Array (Nullable (Int8))') = -100, NULL, NULL ]
SELECT JSONExtract ( '{'a ': "hello", "b": -100, 200.0, 300]}', 'b', 4 'Nullable (Int64)') = NULL
SELECT JSONExtract ( '{ "passed": true}' 'passed', 'UInt8') = 1
SELECT JSONExtract ( '{ "day": "Thursday"}', 'day', 'Enum8 (\'Sunday \ '= 0 \'Monday \ '= 1, \'Tuesday \ '= 2 \'Wednesday \ '= 3 \'Thursday \ '= 4 \'Friday \ '= 5, \'Saturday \ '= 6)') = 'Thursday'
SELECT JSONExtract ( '{ "day": 5}', 'day', 'Enum8 (\'Sunday \ '= 0 \'Monday \ '= 1, \'Tuesday \ '= 2 \'Wednesday \ '= 3 \ 'Thursday \'= 4 \ 'Friday \'= 5, \ 'Saturday \'= 6) ') ='Friday '
```

## JSONExtractKeysAndValues ​​(json \, indices_or_keys ... \, Value_type) {# jsonextractkeysandvaluesjson-indices-or-keys-value-type}

값이 지정된 ClickHouse 데이터 형식 인 JSON에서 키 - 값 쌍을 분석합니다.

예 :

```sql
SELECT JSONExtractKeysAndValues ​​( '{ "x": { "a": 5, "b": 7 "c": 11}}', 'x', 'Int8') = [( 'a', 5), ( 'b', 7) ( 'c', 11)
```

## JSONExtractRaw (json \, indices_or_keys \] ...) {# jsonextractrawjson-indices-or-keys}

JSON의 일부를 분석되지 않은 문자열로 반환합니다.

부품이 없거나 형식이 잘못된 경우 빈 문자열이 반환됩니다.

예 :

```sql
SELECT JSONExtractRaw ( '{'a ': "hello", "b": -100, 200.0, 300]}', 'b') = '[-100, 200.0, 300]'
```

## JSONExtractArrayRaw (json \, indices_or_keys ... \]) {# jsonextractarrayrawjson-indices-or-keys}

JSON 배열의 요소를 가진 배열을 돌려줍니다.

부품이 없거나 배열이 아닌 경우는 하늘의 배열이 반환됩니다.

예 :

```sql
SELECT JSONExtractArrayRaw ( '{'a ': "hello", "b": -100, 200.0 "hello"]}', 'b') = '-100', '200.0', ' "hello"' ] '
```

## JSONExtractKeysAndValuesRaw {# json-extract-keys-and-values-raw}

JSON 개체에서 원시 데이터를 추출합니다.

** 구문 **

```sql
JSONExtractKeysAndValuesRaw (json [, p, a, t, h)
```

** 파라미터 **

-`json` - [문자열] (../ data-types / string.md) 유효한 JSON에서.
-`p, a, t, h` - Comma-separated indices or keys that specify the path to the inner field in a nested JSON object. Each argument can be either a [문자열] (../ data-types / string .md) 또는 키로 필드를 얻으려면 [정수] (../ data-types / int-uint.md) N 번째 필드를 얻으려면 (1 인덱싱 된 음의 정수는 마지막부터 시작합니다). 설정되어 있지 않은 경우, JSON 전체가 최상위 개체로 해석됩니다. 모든 매개 변수.

** 반환 값 **

-와 배열`( 'key', 'value')`튜플 두 튜플 멤버는 문자열입니다.
- 요청한 오브젝트가 존재하지 않거나 입력 JSON가 무효 인 경우는 하늘의 배열.

유형 : 배열 (../ data-types / array.md) (튜플 (../ data-types / tuple.md) ([문자열] (../ data-types / string.md) [문자열] (../ data-types / string.md)).

** 예 **

쿼리 :

```sql
SELECT JSONExtractKeysAndValuesRaw ( '{'a ': -100, 200.0, "b": { "c": { "d": "hello", "f": "world"}}}')
```

결과 :

```text
┌─JSONExtractKeysAndValuesRaw ( '{'a ': -100, 200.0, "b": { "c": { "d": "hello", "f": "world"}}}') ─┐
│ [( 'a', '[- 100,200]') ( 'b', '{ "c": { "d": "hello", "f": "world"}}') │
└───────────────────────────────────────────────── ─────────────────────────────────────────────┘
```

쿼리 :

```sql
SELECT JSONExtractKeysAndValuesRaw ( '{'a ': -100, 200.0, "b": { "c": { "d": "hello", "f": "world"}}}', 'b')
```

결과 :

```text
┌─JSONExtractKeysAndValuesRaw ( '{'a ': -100, 200.0, "b": { "c": { "d": "hello", "f": "world"}}}', 'b' ) ─┐
│ [( 'c', '{'d ': "hello", "f": "world"}') │
└───────────────────────────────────────────────── ────────────────────────────────────────────────── ┘
```

쿼리 :

```sql
SELECT JSONExtractKeysAndValuesRaw ( '{'a ': -100, 200.0, "b": { "c": { "d": "hello", "f": "world"}}}'-1 ' c ')
```

결과 :

```text
┌─JSONExtractKeysAndValuesRaw ( '{'a ': -100, 200.0, "b": { "c": { "d": "hello", "f": "world"}}}', -1, 'c') ─┐
│ [( 'd', ' "hello"') ( 'f' ' "world"') │
└───────────────────────────────────────────────── ────────────────────────────────────────────────── ────┘
```

원본 기사 (https://clickhouse.tech/docs/en/query_language/functions/json_functions/) <! - hide ->
