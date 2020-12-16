---
machine_translated : true
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd
toc_priority : 50
toc_title : "\ u30CF \ u30C3 \ u30B7 \ u30E5"
---

# 해시 함수 {# hash-functions}

해시 함수는 요소의 결정적 pseudo 유사 난수 셔플에 사용할 수 있습니다.

## halfMD5 {# hash-functions-halfmd5}

[해석] (../../ sql-reference / functions / type-conversion-functions.md # type_conversion_functions-reinterpretAsString) 모든 입력 매개 변수를 문자열로 계산하고 MD5 (https : // en. wikipedia.org/wiki/MD5) 각각의 해시 값. 다음 해시를 결합하고 결과 문자열의 해시의 첫 8 바이트를 가지고 가고 그들을 다음과 같이 해석합니다`UInt64` 빅 endian의 바이트 순서.

```sql
halfMD5 (par1, ...)
```

이 기능은 비교적 느린 (프로세서 코어 당 초당 5 만개의 짧은 문자열).
사용을 고려합니다. [sipHash64 (# hash_functions-siphash64) 대신 함수.

** 파라미터 **

이 함수는 가변 수의 입력 매개 변수를받습니다. 매개 변수는 다음 중 하나입니다 [해당 데이터 형식 (../../ sql-reference / data-types / index.md).

** 반환 값 **

A [UInt64 (../../ sql-reference / data-types / int-uint.md) 데이터 형식의 해시 값.

** 예 **

```sql
SELECT halfMD5 (array ( 'e', ​​'x', 'a') 'mple', 10, toDateTime ( '2019-06-15 23:00:00')) AS halfMD5hash, toTypeName (halfMD5hash) AS type
```

```text
┌────────halfMD5hash─┬─type───┐
│ 186182704141653334 │ UInt64 │
└────────────────────┴────────┘
```

## MD5 {# hash_functions-md5}

문자열에서 MD5를 계산하고 결과 바이트 세트를 FixedString (16)로서 돌려줍니다.
특히 MD5를 필요로하지 않지만, 괜찮은 암호화 128 비트 해시가 필요한 경우 'sipHash128'대신 함수.
Md5sum 유틸리티의 출력과 같은 결과를 얻고 싶다면, lower (hex (MD5 (s)))를 사용합니다.

## sipHash64 {# hash_functions-siphash64}

64 비트를 생성하는 [사이화슈 (https://131002.net/siphash/) 해시 값.

```sql
sipHash64 (par1, ...)
```

이것은 암호화 해시 함수입니다. 그것은 더 적어도 세 배 빠르게 작동합니다 [MD5] (# hash_functions-md5) 기능.

함수 [해석] (../../ sql-reference / functions / type-conversion-functions.md # type_conversion_functions-reinterpretAsString) 모든 입력 매개 변수를 문자열로 계산하고 각각의 해시 값을 계산합니다. 다음의 알고리즘으로 해시를 결합합니다 :

1. 모든 입력 매개 변수를 해시 한 후 함수는 해시의 배열을 가져옵니다.
2. 함수는 첫 번째와 두 번째 요소를 가지고 그 배열의 해시를 계산합니다.
3. 다음 함수는 이전 단계에서 계산 된 해시 값과 초기 해시 배열의 세 번째 요소를 가지고 그 배열의 해시를 계산합니다.
4. 이전 단계는 초기 해시 배열의 나머지 모든 요소에 대해 반복됩니다.

** 파라미터 **

이 함수는 가변 수의 입력 매개 변수를받습니다. 매개 변수는 다음 중 하나입니다 [해당 데이터 형식 (../../ sql-reference / data-types / index.md).

** 반환 값 **

A [UInt64 (../../ sql-reference / data-types / int-uint.md) 데이터 형식의 해시 값.

** 예 **

```sql
SELECT sipHash64 (array ( 'e', ​​'x', 'a') 'mple', 10, toDateTime ( '2019-06-15 23:00:00')) AS SipHash, toTypeName (SipHash) AS type
```

```text
┌──────────────SipHash─┬─type───┐
│ 13726873534472839665 │ UInt64 │
└──────────────────────┴────────┘
```

## sipHash128 {# hash_functions-siphash128}

문자열에서 SipHash을 계산합니다.
문자열 인수를받습니다. FixedString (16)를 돌려줍니다.
SipHash64과는 달리, 최종 xor 접는 상태는 128 비트까지 밖에 행해지 지 않는다.

## 시티 해시 64 {# cityhash64}

64 비트를 생성하는 [시티 해시 (https://github.com/google/cityhash) 해시 값.

```sql
cityHash64 (par1, ...)
```

이것은 빠른 비 암호화 해시 함수입니다. 문자열 매개 변수는 CityHash 알고리즘을 사용하여 다른 데이터 형식의 매개 변수는 구현 고유의 고속 비 암호화 해시 함수를 사용합니다. 이 함수는 최종 결과를 얻기 위해 CityHash combinator를 사용합니다.

** 파라미터 **

이 함수는 가변 수의 입력 매개 변수를받습니다. 매개 변수는 다음 중 하나입니다 [해당 데이터 형식 (../../ sql-reference / data-types / index.md).

** 반환 값 **

A [UInt64 (../../ sql-reference / data-types / int-uint.md) 데이터 형식의 해시 값.

** 예 **

호출 예 :

```sql
SELECT cityHash64 (array ( 'e', ​​'x', 'a') 'mple', 10, toDateTime ( '2019-06-15 23:00:00')) AS CityHash, toTypeName (CityHash) AS type
```

```text
┌─────────────CityHash─┬─type───┐
│ 12072650598913549138 │ UInt64 │
└──────────────────────┴────────┘
```

다음 예제에서는 행의 순서까지의 정밀도로 전체 테이블의 체크섬을 계산하는 방법을 보여줍니다.

```sql
SELECT groupBitXor (cityHash64 (*)) FROM table
```

## intHash32 {# inthash32}

모든 유형의 정수에서 32 비트 해시 코드를 계산합니다.
이것은 수치의 평균 품질의 비교적 빠른 비 암호화 해시 함수입니다.

## intHash64 {# inthash64}

모든 유형의 정수에서 64 비트 해시 코드를 계산합니다.
그것은 intHash32보다 빠르게 작동합니다. 평균 품질.

## SHA1 {# sha1}

## SHA224 {# sha224}

## SHA256 {# sha256}

문자열에서 SHA-1, SHA-224 또는 SHA-256을 계산하고 결과 바이트 세트를 FixedString (20) FixedString (28) 또는 FixedString (32)로서 돌려줍니다.
이 기능은 매우 천천히 작동합니다 (SHA-1은 프로세서 코어 초당 약 5 만 짧은 문자열을 처리하지만, SHA-224 및 SHA-256은 약 2.2 만 짧은 문자열을 처리
이 함수는 특정 해쉬 함수가 필요 선택할 수없는 경우에만 사용하는 것이 좋습니다.
이러한 경우에도 SELECT에 적용하는 것이 아니라 함수를 오프라인으로 적용하고 테이블에 삽입 할 때 값을 미리 계산하는 것이 좋습니다.

## URLHash (url \, N \]) {# urlhashurl-n}

어떤 타입의 정규화를 사용하여 URL에서 가져온 문자열에 대한 빠르고 괜찮은 품질의 비 암호화 해시 함수.
`URLHash (s)`- Calculates a hash from a string without one of the trailing symbols`/``?`또는`#`마지막으로 존재하는 경우.
`URLHash (s, N)`- Calculates a hash from a string up to the N level in the URL hierarchy, without one of the trailing symbols`/``?`또는`#`마지막으로 존재하는 경우.
수준은 URLHierarchy과 같습니다. 이 기능은 Yandex에 고유의 것입니다. 메틀리카

## farnhash64 {# farmhash64}

64 비트를 생성하는 팜 해시 (https://github.com/google/farmhash) 해시 값.

```sql
farmHash64 (par1, ...)
```

이 함수는`Hash64` 모두의 방법 사용 가능한 방법 (https://github.com/google/farmhash/blob/master/src/farmhash.h).

** 파라미터 **

이 함수는 가변 수의 입력 매개 변수를받습니다. 매개 변수는 다음 중 하나입니다 [해당 데이터 형식 (../../ sql-reference / data-types / index.md).

** 반환 값 **

A [UInt64 (../../ sql-reference / data-types / int-uint.md) 데이터 형식의 해시 값.

** 예 **

```sql
SELECT farmHash64 (array ( 'e', ​​'x', 'a') 'mple', 10, toDateTime ( '2019-06-15 23:00:00')) AS FarmHash, toTypeName (FarmHash) AS type
```

```text
┌─────────────FarmHash─┬─type───┐
│ 17790458267262532859 │ UInt64 │
└──────────────────────┴────────┘
```

## javaHash {# hash_functions-javahash}

계산 [JavaHash (http://hg.openjdk.java.net/jdk8u/jdk8u/jdk/file/478a4add975b/src/share/classes/java/lang/String.java#l1452) 문자열에서. 이 해시 함수는 고속에서도 좋은 품질도 없습니다. 이것을 사용하는 유일한 이유는이 알고리즘이 이미 다른 시스템에서 사용되며, 동일한 결과를 계산해야하는 경우입니다.

** 구문 **

```sql
SELECT javaHash ( '');
```

** 반환 값 **

A`Int32` 데이터 형의 해시 값.

** 예 **

쿼리 :

```sql
SELECT javaHash ( 'Hello, world!');
```

결과 :

```text
┌─javaHash ( 'Hello, world!') ─┐
│ -1880044555 │
└───────────────────────────┘
```

## javaHashUTF16LE {# javahashutf16le}

계산 [JavaHash (http://hg.openjdk.java.net/jdk8u/jdk8u/jdk/file/478a4add975b/src/share/classes/java/lang/String.java#l1452) 문자열에서 UTF-16LE 인코딩 문자열을 나타내는 바이트가 포함되어 있다고 가정합니다.

** 구문 **

```sql
javaHashUTF16LE (stringUtf16le)
```

** 파라미터 **

-`stringUtf16le` - a string in UTF-16LE encoding.

** 반환 값 **

A`Int32` 데이터 형의 해시 값.

** 예 **

UTF-16LE 인코딩 문자열에서 쿼리를 수정합니다.

쿼리 :

```sql
SELECT javaHashUTF16LE (convertCharset ( 'test', 'utf-8', 'utf-16le'))
```

결과 :

```text
┌─javaHashUTF16LE (convertCharset ( 'test', 'utf-8', 'utf-16le')) ─┐
│ 3556498 │
└───────────────────────────────────────────────── ─────────────┘
```

## hiveHash {# hash-functions-hivehash}

계산`HiveHash` 문자열에서.

```sql
SELECT hiveHash ( '');
```

이것은 단지입니다 [JavaHash (# hash_functions-javahash) 제로 아웃 부호 비트를 가진다. 이 함수는 [Apache 하이브 (https://en.wikipedia.org/wiki/Apache_Hive) 3.0 이전 버전의 경우. 이 해시 함수는 고속에서도 좋은 품질도 없습니다. 이것을 사용하는 유일한 이유는이 알고리즘이 이미 다른 시스템에서 사용되며, 동일한 결과를 계산해야하는 경우입니다.

** 반환 값 **

A`Int32` 데이터 형의 해시 값.

유형 :`hiveHash`.

** 예 **

쿼리 :

```sql
SELECT hiveHash ( 'Hello, world!');
```

결과 :

```text
┌─hiveHash ( 'Hello, world!') ─┐
│ 267439093 │
└───────────────────────────┘
```

## metroHash64 {# metrohash64}

64 비트를 생성하는 [메토로하슈 (http://www.jandrewrogers.com/2015/05/27/metrohash/) 해시 값.

```sql
metroHash64 (par1, ...)
```

** 파라미터 **

이 함수는 가변 수의 입력 매개 변수를받습니다. 매개 변수는 다음 중 하나입니다 [해당 데이터 형식 (../../ sql-reference / data-types / index.md).

** 반환 값 **

A [UInt64 (../../ sql-reference / data-types / int-uint.md) 데이터 형식의 해시 값.

** 예 **

```sql
SELECT metroHash64 (array ( 'e', ​​'x', 'a') 'mple', 10, toDateTime ( '2019-06-15 23:00:00')) AS MetroHash, toTypeName (MetroHash) AS type
```

```text
┌────────────MetroHash─┬─type───┐
│ 14235658766382344533 │ UInt64 │
└──────────────────────┴────────┘
```

## jumpConsistentHash {#jumpconsistenthash}

JumpConsistentHash을 계산하면 UInt64를 형성합니다.
UInt64 형의 키와 버킷의 수입니다. Int32를 반환합니다.
자세한 내용은 링크를 참조하십시오 : JumpConsistentHash (https://arxiv.org/pdf/1406.2294.pdf)

## murmurHash2_32, murmurHash2_64 {# murmurhash2-32-murmurhash2-64}

를 생성한다. [짹짹 2] (https://github.com/aappleby/smhasher) 해시 값.

```sql
murmurHash2_32 (par1, ...)
murmurHash2_64 (par1, ...)
```

** 파라미터 **

두 함수는 가변 수의 입력 매개 변수를 취합니다. 매개 변수는 다음 중 하나입니다 [해당 데이터 형식 (../../ sql-reference / data-types / index.md).

** 반환 값 **

- 그`murmurHash2_32` 함수는 해시 값을 반환합니다. [UInt32 (../../ sql-reference / data-types / int-uint.md) 데이터 형식입니다.
- 그`murmurHash2_64` 함수는 해시 값을 반환합니다. [UInt64 (../../ sql-reference / data-types / int-uint.md) 데이터 형식입니다.

** 예 **

```sql
SELECT murmurHash2_64 (array ( 'e', ​​'x', 'a') 'mple', 10, toDateTime ( '2019-06-15 23:00:00')) AS MurmurHash2, toTypeName (MurmurHash2) AS type
```

```text
┌──────────MurmurHash2─┬─type───┐
│ 11832096901709403633 │ UInt64 │
└──────────────────────┴────────┘
```

## gccMurmurHash {#gccmurmurhash}

64 비트의 계산 [짹짹 2] (https://github.com/aappleby/smhasher) 같은 해시 시드를 사용하는 해시 값 [gcc (https://github.com/gcc-mirror/gcc/blob/41d6b10e96a1de98e90a7c0378437c3255814b16 /libstdc%2B%2B-v3/include/bits/functional_hash.h#L191). 이것은 CLang와 GCC 빌드 사이에 이식 가능합니다.

** 구문 **

```sql
gccMurmurHash (par1, ...);
```

** 파라미터 **

-`par1 ...`- A variable number of parameters that can be any of the [해당 데이터 형식 (../../ sql-reference / data-types / index.md # data_types).

** 반환 값 **

- 계산 된 해시 값.

유형 : UInt64 (../../ sql-reference / data-types / int-uint.md).

** 예 **

쿼리 :

```sql
SELECT
    gccMurmurHash (1, 2, 3) AS res1,
    gccMurmurHash (( 'a', 1, 2, 3, 4, (4, 'foo', 'bar', 1 (1, 2)))) AS res2
```

결과 :

```text
┌─────────────────res1─┬────────────────res2─┐
│ 12384823029245979431 │ 1188926775431157506 │
└──────────────────────┴─────────────────────┘
```

## murmurHash3_32, murmurHash3_64 {# murmurhash3-32-murmurhash3-64}

를 생성한다. [마무루하슈 3 세 (https://github.com/aappleby/smhasher) 해시 값.

```sql
murmurHash3_32 (par1, ...)
murmurHash3_64 (par1, ...)
```

** 파라미터 **

두 함수는 가변 수의 입력 매개 변수를 취합니다. 매개 변수는 다음 중 하나입니다 [해당 데이터 형식 (../../ sql-reference / data-types / index.md).

** 반환 값 **

- 그`murmurHash3_32` 함수는 a를 반환합니다 [UInt32 (../../ sql-reference / data-types / int-uint.md) 데이터 형식의 해시 값.
- 그`murmurHash3_64` 함수는 a를 반환합니다 [UInt64 (../../ sql-reference / data-types / int-uint.md) 데이터 형식의 해시 값.

** 예 **

```sql
SELECT murmurHash3_32 (array ( 'e', ​​'x', 'a') 'mple', 10, toDateTime ( '2019-06-15 23:00:00')) AS MurmurHash3, toTypeName (MurmurHash3) AS type
```

```text
┌─MurmurHash3─┬─type───┐
│ 2152717 │ UInt32 │
└─────────────┴────────┘
```

## 짹짹 3_128 {# murmurhash3-128}

128 비트를 생성하는 [마무루하슈 3 세 (https://github.com/aappleby/smhasher) 해시 값.

```sql
murmurHash3_128 (expr)
```

** 파라미터 **

-`expr` - 식 (../ syntax.md # syntax-expressions) a를 반환 문자열 (../../ sql-reference / data-types / string.md) - 유형 값.

** 반환 값 **

A [FixedString (16) (../../ sql-reference / data-types / fixedstring.md) 데이터 형식의 해시 값.

** 예 **

```sql
SELECT murmurHash3_128 ( 'example_string') AS MurmurHash3, toTypeName (MurmurHash3) AS type
```

```text
┌─MurmurHash3──────┬─type────────────┐
│ 6? 1? 4 "S5KT ~ q │ FixedString (16) │
└──────────────────┴─────────────────┘
```

## xxHash32, xxHash64 {# hash-functions-xxhash32}

계산`xxHash` 문자열에서. 이것은 두 가지 맛, 32 및 64 비트에서 제안되고 있습니다.

```sql
SELECT xxHash32 ( '');

OR

SELECT xxHash64 ( '');
```

** 반환 값 **

A`Uint32` 또는`Uint64` 데이터 형의 해시 값.

유형 :`xxHash`.

** 예 **

쿼리 :

```sql
SELECT xxHash32 ( 'Hello, world!');
```

결과 :

```text
┌─xxHash32 ( 'Hello, world!') ─┐
│ 834093149 │
└───────────────────────────┘
```

** 참고하십시오. **

- [xxHash (http://cyan4973.github.io/xxHash/).

원본 기사 (https://clickhouse.tech/docs/en/query_language/functions/hash_functions/) <! - hide ->
