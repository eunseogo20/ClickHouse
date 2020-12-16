---
machine_translated : true
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd
toc_priority : 49
toc_title : "\ u30D3 \ u30C3 \ u30C8 \ u30DE"
---

# 비트 맵 함수 {# bitmap-functions}

And, or xor, not 등의 수식 계산을 사용하면서 새로운 비트 맵 또는 기수를 돌려주는 것입니다.

비트 맵 객체를 만드는 방법에는 2 가지가 있습니다. 하나는 -State를 가지는 집계 함수 groupBitmap 의해 만들어진 또 하나는 Array Object 의해 구축됩니다. 하지만 변환 비트 맵 객체 배열의 객체입니다.

RoaringBitmap 비트 맵 객체의 실제 저장중인 데이터 구조에 싸여있다. 기수가 32 이하인 경우 Set objet가 사용됩니다. 기수가 32보다 큰 경우는 RoaringBitmap 개체를 사용합니다. 따라서 낮은 기수 세트의 저장이 빨라집니다.

RoaringBitmap 대한 자세한 내용은 다음을 참조하십시오 : 크로 링 (https://github.com/RoaringBitmap/CRoaring).

## bitmapBuild {# bitmap_functions-bitmapbuild}

부호없는 정수 배열에서 비트 맵을 만듭니다.

```sql
bitmapBuild (array)
```

** 파라미터 **

-`array` - unsigned integer array.

** 예 **

```sql
SELECT bitmapBuild (1, 2, 3, 4, 5]) AS res, toTypeName (res)
```

```text
┌─res─┬─toTypeName (bitmapBuild (1, 2, 3, 4, 5])) ─────┐
│ ???? │ AggregateFunction (groupBitmap, UInt8) │
└─────┴─────────────────────────────────────────── ───┘
```

## bitmapToArray {#bitmaptoarray}

비트 맵을 정수 배열로 변환합니다.

```sql
bitmapToArray (bitmap)
```

** 파라미터 **

-`bitmap` - bitmap object.

** 예 **

```sql
SELECT bitmapToArray (bitmapBuild (1, 2, 3, 4, 5])) AS res
```

```text
┌─res─────────┐
│ [1,2,3,4,5] │
└─────────────┘
```

## bitmapSubsetInRange {# bitmap-functions-bitmapsubsetinrange}

지정된 범위의 하위 집합을 반환합니다 (range_end은 포함되지 않습니다).

```sql
bitmapSubsetInRange (bitmap, range_start, range_end)
```

** 파라미터 **

-`bitmap` - 비트 맵 (# bitmap_functions-bitmapbuild).
-`range_start` - range start point : Type : UInt32 (../../ sql-reference / data-types / int-uint.md).
-`range_end` - range end point (excluded) Type : UInt32 (../../ sql-reference / data-types / int-uint.md).

** 예 **

```sql
SELECT bitmapToArray (bitmapSubsetInRange (bitmapBuild (0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20, 21,22,23,24,25,26,27,28,29,30,31,32,33,100,200,500), toUInt32 (30) toUInt32 (200))) AS res
```

```text
┌─res───────────────┐
│ [30,31,32,33,100] │
└───────────────────┘
```

## bitmapSubsetLimit {#bitmapsubsetlimit}

비트 맵의 ​​부분 집합을 만듭니다. `range_start`와`cardinality_limit`.

** 구문 **

```sql
bitmapSubsetLimit (bitmap, range_start, cardinality_limit)
```

** 파라미터 **

-`bitmap` - 비트 맵 (# bitmap_functions-bitmapbuild).
-`range_start` - The subset starting point : Type : UInt32 (../../ sql-reference / data-types / int-uint.md).
-`cardinality_limit` - The subset cardinality upper limit : Type : UInt32 (../../ sql-reference / data-types / int-uint.md).

** 반환 값 **

일부.

유형 :`Bitmap object`.

** 예 **

쿼리 :

```sql
SELECT bitmapToArray (bitmapSubsetLimit (bitmapBuild (0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20, 21,22,23,24,25,26,27,28,29,30,31,32,33,100,200,500), toUInt32 (30) toUInt32 (200))) AS res
```

결과 :

```text
┌─res───────────────────────┐
│ [30,31,32,33,100,200,500] │
└───────────────────────────┘
```

## bitmapContains {# bitmap_functions-bitmapcontains}

여부를 확인합니다 비트 맵을 포함하는 요소입니다.

```sql
bitmapContains (haystack, needle)
```

** 파라미터 **

-`haystack` - 비트 맵 (# bitmap_functions-bitmapbuild) 함수가 검색하는 장소.
-`needle` - Value that the function searches. Type : UInt32 (../../ sql-reference / data-types / int-uint.md).

** 반환 값 **

- 0 - If`haystack` 포함하지 않는`needle`.
- 1 - If`haystack` 포함한`needle`.

유형 :`UInt8`.

** 예 **

```sql
SELECT bitmapContains (bitmapBuild (1,5,7,9), toUInt32 (9)) AS res
```

```text
┌─res─┐
│ 1 │
└─────┘
```

## bitmapHasAny {#bitmaphasany}

여부를 판단 두개의 비트 맵하고있는 것으로 교차점에있다.

```sql
bitmapHasAny (bitmap1, bitmap2)
```

당신이 그것을 확신하는 경우`bitmap2` 엄격하게 하나의 요소가 포함되어 있습니다. [bitmapContains (# bitmap_functions-bitmapcontains) 기능. 이것은 더 효율적으로 작동합니다.

** 파라미터 **

-`bitmap *`- bitmap object.

** 반환 값 **

-`1`, if`bitmap1`와`bitmap2` 적어도 하나의 같은 요소가 있습니다.
-`0` 그렇지 않으면

** 예 **

```sql
SELECT bitmapHasAny (bitmapBuild ([1,2,3]) bitmapBuild ([3,4,5])) AS res
```

```text
┌─res─┐
│ 1 │
└─────┘
```

## bitmapHasAll {#bitmaphasall}

유사한`hasAll (array, array)`반환 값이 1이면 첫 번째 비트 맵을 포함한 모든 요소는 0입니다.
두 번째 인수가 빈 비트 맵의 ​​경우는 1을 돌려줍니다.

```sql
bitmapHasAll (bitmap, bitmap)
```

** 파라미터 **

-`bitmap` - bitmap object.

** 예 **

```sql
SELECT bitmapHasAll (bitmapBuild ([1,2,3]) bitmapBuild ([3,4,5])) AS res
```

```text
┌─res─┐
│ 0 │
└─────┘
```

## bitmapCardinality {#bitmapcardinality}

UInt64 형의 비트 맵 기수를 다시 실행합니다.

```sql
bitmapCardinality (bitmap)
```

** 파라미터 **

-`bitmap` - bitmap object.

** 예 **

```sql
SELECT bitmapCardinality (bitmapBuild (1, 2, 3, 4, 5])) AS res
```

```text
┌─res─┐
│ 5 │
└─────┘
```

## bitmapMin {#bitmapmin}

세트의 UInt64 형의 최소값을 다시 실행하여 설정이 비어 있으면 UINT32_MAX.

    bitmapMin (bitmap)

** 파라미터 **

-`bitmap` - bitmap object.

** 예 **

```sql
SELECT bitmapMin (bitmapBuild (1, 2, 3, 4, 5])) AS res
```

    ┌─res─┐
    │ 1 │
    └─────┘

## bitmapMax {#bitmapmax}

세트의 UInt64 형의 최대 값을 다시 시도 세트가 하늘의 경우는 0을 다시 시도합니다.

    bitmapMax (bitmap)

** 파라미터 **

-`bitmap` - bitmap object.

** 예 **

```sql
SELECT bitmapMax (bitmapBuild (1, 2, 3, 4, 5])) AS res
```

    ┌─res─┐
    │ 5 │
    └─────┘

## bitmapTransform {#bitmaptransform}

비트 맵의 ​​값의 배열을 다른 값의 배열로 변환하면 결과는 새로운 비트 맵입니다.

    bitmapTransform (bitmap, from_array, to_array)

** 파라미터 **

-`bitmap` - bitmap object.
-`from_array` - UInt32 array. For idx in range \ 0 from_array.size ()) if bitmap contains from_array \ [idx \, then replace it with to_array \ [idx \]. Note that the result depends on array ordering if there are common elements between from_array and to_array.
-`to_array` - UInt32 array, its size shall be the same to from_array.

** 예 **

```sql
SELECT bitmapToArray (bitmapTransform (bitmapBuild (1, 2, 3, 4, 5, 6, 7, 8, 9, 10), cast (5,999,2] as Array (UInt32)), cast (2,888,20 ] as Array (UInt32)))) aS res
```

    ┌─res───────────────────┐
    │ [1,3,4,6,7,8,9,10,20] │
    └───────────────────────┘

## bitmapAnd {#bitmapand}

두 비트 맵과 계산 결과는 새로운 비트 맵입니다.

```sql
bitmapAnd (bitmap, bitmap)
```

** 파라미터 **

-`bitmap` - bitmap object.

** 예 **

```sql
SELECT bitmapToArray (bitmapAnd (bitmapBuild ([1,2,3]) bitmapBuild ([3,4,5]))) AS res
```

```text
┌─res─┐
│ [3] │
└─────┘
```

## bitmapOr {#bitmapor}

결과는 새로운 비트 맵입니다.

```sql
bitmapOr (bitmap, bitmap)
```

** 파라미터 **

-`bitmap` - bitmap object.

** 예 **

```sql
SELECT bitmapToArray (bitmapOr (bitmapBuild ([1,2,3]) bitmapBuild ([3,4,5]))) AS res
```

```text
┌─res─────────┐
│ [1,2,3,4,5] │
└─────────────┘
```

## bitmapXor {#bitmapxor}

두 비트 맵 xor 계산 결과는 새로운 비트 맵입니다.

```sql
bitmapXor (bitmap, bitmap)
```

** 파라미터 **

-`bitmap` - bitmap object.

** 예 **

```sql
SELECT bitmapToArray (bitmapXor (bitmapBuild ([1,2,3]) bitmapBuild ([3,4,5]))) AS res
```

```text
┌─res───────┐
│ [1,2,4,5] │
└───────────┘
```

## bitmapAndnot {#bitmapandnot}

두 비트 맵 andnot 계산 결과는 새로운 비트 맵입니다.

```sql
bitmapAndnot (bitmap, bitmap)
```

** 파라미터 **

-`bitmap` - bitmap object.

** 예 **

```sql
SELECT bitmapToArray (bitmapAndnot (bitmapBuild ([1,2,3]) bitmapBuild ([3,4,5]))) AS res
```

```text
┌─res───┐
│ [1,2] │
└───────┘
```

## bitmapAndCardinality {#bitmapandcardinality}

Uint64 형의 기수를 돌려줍니다.

```sql
bitmapAndCardinality (bitmap, bitmap)
```

** 파라미터 **

-`bitmap` - bitmap object.

** 예 **

```sql
SELECT bitmapAndCardinality (bitmapBuild ([1,2,3]) bitmapBuild ([3,4,5])) AS res;
```

```text
┌─res─┐
│ 1 │
└─────┘
```

## bitmapOrCardinality {#bitmaporcardinality}

Uint64 형의 기수를 돌려줍니다.

```sql
bitmapOrCardinality (bitmap, bitmap)
```

** 파라미터 **

-`bitmap` - bitmap object.

** 예 **

```sql
SELECT bitmapOrCardinality (bitmapBuild ([1,2,3]) bitmapBuild ([3,4,5])) AS res;
```

```text
┌─res─┐
│ 5 │
└─────┘
```

## bitmapXorCardinality {#bitmapxorcardinality}

Uint64 형의 기수를 돌려줍니다.

```sql
bitmapXorCardinality (bitmap, bitmap)
```

** 파라미터 **

-`bitmap` - bitmap object.

** 예 **

```sql
SELECT bitmapXorCardinality (bitmapBuild ([1,2,3]) bitmapBuild ([3,4,5])) AS res;
```

```text
┌─res─┐
│ 4 │
└─────┘
```

## bitmapAndnotCardinality {#bitmapandnotcardinality}

Uint64 형의 기수를 돌려줍니다.

```sql
bitmapAndnotCardinality (bitmap, bitmap)
```

** 파라미터 **

-`bitmap` - bitmap object.

** 예 **

```sql
SELECT bitmapAndnotCardinality (bitmapBuild ([1,2,3]) bitmapBuild ([3,4,5])) AS res;
```

```text
┌─res─┐
│ 2 │
└─────┘
```

원본 기사 (https://clickhouse.tech/docs/en/query_language/functions/bitmap_functions/) <! - hide ->
