---
machine_translated : true
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd
toc_priority : 62
toc_title : "\ u5730 \ u7406 \ u5EA7 \ u6A19 \ u306E \ u64CD \ u4F5C"
---

# 지리 좌표를 조작하는 함수 {# functions-for-working-with-geographical-coordinates}

## 그레이트 서클 거리 {#greatcircledistance}

를 사용하여 지구 표면의 두 지점 사이의 거리를 계산합니다 [대원의 공식 (https://en.wikipedia.org/wiki/Great-circle_distance).

```sql
greatCircleDistance (lon1Deg, lat1Deg, lon2Deg, lat2Deg)
```

** 입력 매개 변수 **

-`lon1Deg` - Longitude of the first point in degrees. Range :`[-180 °, 180 °]`.
-`lat1Deg` - Latitude of the first point in degrees. Range :`[-90 °, 90 °]`.
-`lon2Deg` - Longitude of the second point in degrees. Range :`[-180 °, 180 °]`.
-`lat2Deg` - Latitude of the second point in degrees. Range :`[-90 °, 90 °]`.

양수 값은 북위와 동경에 대응하고, 음수 값은 남위과 서경에 대응합니다.

** 반환 값 **

미터 단위로 지구 표면의 두 지점 사이의 거리.

입력 매개 변수 값이 범위 외에있는 경우에 예외를 생성합니다.

** 예 **

```sql
SELECT greatCircleDistance (55.755831, 37.617673, -55.755831, -37.617673)
```

```text
┌─greatCircleDistance (55.755831, 37.617673, -55.755831, -37.617673) ─┐
│ 14132374.194975413 │
└───────────────────────────────────────────────── ──────────────────┘
```

## 포인트 네 입술 {#pointinellipses}

점이 적어도 하나의 타원에 속하는지 여부를 확인합니다.
좌표는 데카르트 좌표계는 기하학적입니다.

```sql
pointInEllipses (x, y x₀, y₀, a₀, b₀, ..., xₙ, yₙ, aₙ, bₙ)
```

** 입력 매개 변수 **

-`x, y` - Coordinates of a point on the plane.
-`xᵢ, yᵢ` - Coordinates of the center of the`i`- 번째 줄임표.
-`aᵢ, bᵢ` - Axes of the`i`-x, y 좌표의 단위 번째 줄임표.

입력 매개 변수`2 + 4⋅n` 여기에서`n` 타원의 수입니다.

** 반환 값 **

`1` 점이 타원의 적어도 하나의 내부에있는 경우;`0` 그렇지 않은 경우.

** 예 **

```sql
SELECT pointInEllipses (10., 10., 10., 9.1, 1, 0.9999)
```

```text
┌─pointInEllipses (10., 10., 10., 9.1, 1, 0.9999) ─┐
│ 1 │
└───────────────────────────────────────────────── ┘
```

## 포인트 다각형 {#pointinpolygon}

포인트가 평면상의 폴리곤에 속하는지 여부를 확인합니다.

```sql
pointInPolygon ((x, y), (a, b), (c, d) ..., ...)
```

** 입력 값 **

-`(x, y)`- Coordinates of a point on the plane. Data type - 튜플 (../../ sql-reference / data-types / tuple.md) - A tuple of two numbers.
-`[(a, b), (c, d) ...]`- Polygon vertices. Data type - [배열] (../../ sql-reference / data-types / array.md) 각 정점 좌표 쌍으로 표시됩니다`(a, b)`정점은 시계 방향 또는 반 시계 방향으로 순서대로 지정해야합니다. 정점의 최소 숫자는 3입니다. 다각형은 일정해야합니다.
-이 기능에도 대응하고 다각적 形穴 아키 (자르기 부문)이 경우 함수의 추가 인수를 사용하여 잘린 부분을 정의하는 다각형을 추가합니다. 이 기능은 지원하지 않는 비 단에 연결 다각형.

** 반환 값 **

`1` 포인트가 다각형의 내부에있는 경우`0` 그렇지 않은 경우.
포인트가 다각형 경계에있는 경우, 함수는 0 또는 1을 반환합니다.

** 예 **

```sql
SELECT pointInPolygon ((3. 3.), (6, 0), (8, 4), (5, 8), (0, 2)) AS res
```

```text
┌─res─┐
│ 1 │
└─────┘
```

## geohashEncode {#geohashencode}

위도와 경도를 지오 해시 문자열로 인코딩합니다. http://geohash.org/,https://en.wikipedia.org/wiki/Geohash).

```sql
geohashEncode (longitude, latitude, precision)
```

** 입력 값 **

- 경도 - 인코딩하는 좌표의 경도 부분. 범위의 부유`[-180 °, 180 °]`
- 위도 - 인코딩 좌표 위도 부분. 범위의 부유`[-90 °, 90 °]`
- 정밀도 - 옵션에서 결과 인코딩 된 문자열의 길이. `12` 범위의 정수`[1, 12]`.보다 작은 값`1` 또는 더 큰`12`로 변환됩니다. `12`.

** 반환 값 **

- 영숫자`String` 코딩 좌표 (base32 인코딩 알파벳의 수정 버전이 사용됩니다).

** 예 **

```sql
SELECT geohashEncode (-5.60302734375, 42.593994140625 0) AS res
```

```text
┌─res──────────┐
│ ezs42d000000 │
└──────────────┘
```

## geohashDecode {#geohashdecode}

지오 해시로 인코딩 된 문자열을 경도와 위도로 디코딩합니다.

** 입력 값 **

- 인코딩 문자열 - 지오 해시 인코딩 문자열.

** 반환 값 **

- (경도, 위도) -2- 튜플`Float64` 위도와 경도 값.

** 예 **

```sql
SELECT geohashDecode ( 'ezs42') AS res
```

```text
┌─res─────────────────────────────┐
│ (-5.60302734375,42.60498046875) │
└─────────────────────────────────┘
```

## geoToH3 {# geotoh3}

쯔づゥ쯔. [H3] (https://uber.github.io/h3/#/documentation/overview/introduction) 포인트 지수`(lon, lat)`지정 결정으로.

[H3] (https://uber.github.io/h3/#/documentation/overview/introduction) 지구의 표면이 육각형 타일로 분할 된 지리적 색인 시스템입니다. 이 시스템은 계층 적이며, 즉 최상위 각 육각형은 7 개로 분할 할 수 있지만, 더 작은 것들입니다.

이 인덱스는 주로 버킷 장소 및 기타 지리 공간 데이터 조작에 사용됩니다.

** 구문 **

```sql
geoToH3 (lon, lat, resolution)
```

** 파라미터 **

-`lon` - Longitude : Type : Float64 (../../ sql-reference / data-types / float.md).
-`lat` - Latitude : Type : Float64 (../../ sql-reference / data-types / float.md).
-`resolution` - Index resolution. Range :`[0, 15]`. 유형 : UInt8 (../../ sql-reference / data-types / int-uint.md).

** 반환 값 **

- 육각형의 인덱스 번호.
- 오류의 경우는 0.

유형 :`UInt64`.

** 예 **

쿼리 :

```sql
SELECT geoToH3 (37.79506683, 55.71290588 15) as h3Index
```

결과 :

```text
┌────────────h3Index─┐
│ 644325524701193974 │
└────────────────────┘
```

## geohashesInBox {#geohashesinbox}

지정된 상자 내부에 있으며, 지정된 상자의 경계와 교차하는, 지정된 정밀도의 지오 해시 인코딩 된 문자열의 배열을 반환합니다.

** 입력 값 **

- longitudie_min- 최소의 경도 범위의 부동 소수점 값`[-180 °, 180 °]`
- latitude_min- 최소 위도 범위의 부동 소수점 값`[-90 °, 90 °]`
- 세로 방향 max- 최대 경도 범위의 부동 소수점 값`[-180 °, 180 °]`
- latitude_max- 최대 위도 범위의 부동 소수점 값`[-90 °, 90 °]`
- 정밀도 - 지오 해시 정밀도`UInt8` 범위 내`[1, 12]`

모든 좌표 매개 변수는 동일한 유형이어야합니다. `Float32` 또는`Float64`.

** 반환 값 **

- 정밀도의 배열 - 제공 된 영역을 커버하는 지오 해시 상자에 긴 문자열, 당신은 항목의 순서에 의존해서는 없습니다.
- \ [\] - 하늘의 배열의 경우 * 분 * 값 * 위도 *와 * 경도 * 대응하는보다 작지 않은 * 최대 * 값.

결과 배열이 10'000'000 항목 이상의 경우 함수에서 예외가 발생한다는 점에 유의하십시오.

** 예 **

```sql
SELECT geohashesInBox (24.48, 40.56, 24.785, 40.81, 4) AS thasos
```

```text
┌─thasos──────────────────────────────────────┐
│ [ 'sx1q', 'sx1r', 'sx32', 'sx1w', 'sx1x', 'sx38'] │
└─────────────────────────────────────────────┘
```

## h3GetBaseCell {# h3getbasecell}

인덱스의 기본 휴대폰 번호를 반환합니다.

** 구문 **

```sql
h3GetBaseCell (index)
```

** 파라미터 **

-`index` - Hexagon index number : Type : UInt64 (../../ sql-reference / data-types / int-uint.md).

** 반환 값 **

- 육각형 기반의 셀 번호. 유형 : UInt8 (../../ sql-reference / data-types / int-uint.md).

** 예 **

쿼리 :

```sql
SELECT h3GetBaseCell (612916788725809151) as basecell
```

결과 :

```text
┌─basecell─┐
│ 12 │
└──────────┘
```

## h3HexAreaM2 {# h3hexaream2}

주어진 해상도에서 평방 미터의 평균 육각형 면적.

** 구문 **

```sql
h3HexAreaM2 (resolution)
```

** 파라미터 **

-`resolution` - Index resolution. Range :`[0, 15]`. 유형 : UInt8 (../../ sql-reference / data-types / int-uint.md).

** 반환 값 **

- Area in m². Type : Float64 (../../ sql-reference / data-types / float.md).

** 예 **

쿼리 :

```sql
SELECT h3HexAreaM2 (13) as area
```

결과 :

```text
┌─area─┐
│ 43.9 │
└──────┘
```

## h3IndexesAreNeighbors {# h3indexesareneighbors}

지정된 H3index이 neighbor인지 여부를 반환합니다.

** 구문 **

```sql
h3IndexesAreNeighbors (index1, index2)
```

** 파라미터 **

-`index1` - Hexagon index number : Type : UInt64 (../../ sql-reference / data-types / int-uint.md).
-`index2` - Hexagon index number : Type : UInt64 (../../ sql-reference / data-types / int-uint.md).

** 반환 값 **

- 쯔づゥ쯔. `1` 인덱스가 근방의 경우`0` 그렇지 않으면 유형 : UInt8 (../../ sql-reference / data-types / int-uint.md).

** 예 **

쿼리 :

```sql
SELECT h3IndexesAreNeighbors (617420388351344639, 617420388352655359) AS n
```

결과 :

```text
┌─n─┐
│ 1 │
└───┘
```

## h3ToChildren {# h3tochildren}

지정된 인덱스의 자식 인덱스를 가지는 배열을 돌려줍니다.

** 구문 **

```sql
h3ToChildren (index, resolution)
```

** 파라미터 **

-`index` - Hexagon index number : Type : UInt64 (../../ sql-reference / data-types / int-uint.md).
-`resolution` - Index resolution. Range :`[0, 15]`. 유형 : UInt8 (../../ sql-reference / data-types / int-uint.md).

** 반환 값 **

- 아이 h3 인덱스를 가지는 배열. 형식의 배열 : UInt64 (../../ sql-reference / data-types / int-uint.md).

** 예 **

쿼리 :

```sql
SELECT h3ToChildren (599405990164561919 6) AS children
```

결과 :

```text
┌─children─────────────────────────────────────────────── ────────────────────────────────────────────────── ──────────────────────────────┐
│ [603909588852408319,603909588986626047,603909589120843775,603909589255061503,603909589389279231,603909589523496959,603909589657714687] │
└───────────────────────────────────────────────── ────────────────────────────────────────────────── ─────────────────────────────────────┘
```

## h3ToParent {# h3toparent}

지정된 인덱스를 포함하는 부모 (더 가혹한) 인덱스를 돌려줍니다.

** 구문 **

```sql
h3ToParent (index, resolution)
```

** 파라미터 **

-`index` - Hexagon index number : Type : UInt64 (../../ sql-reference / data-types / int-uint.md).
-`resolution` - Index resolution. Range :`[0, 15]`. 유형 : UInt8 (../../ sql-reference / data-types / int-uint.md).

** 반환 값 **

- 부모 H3 인덱스. 유형 : UInt64 (../../ sql-reference / data-types / int-uint.md).

** 예 **

쿼리 :

```sql
SELECT h3ToParent (599405990164561919 3) as parent
```

결과 :

```text
┌─────────────parent─┐
│ 590398848891879423 │
└────────────────────┘
```

## h3ToString {# h3tostring}

인덱스 H3Index 표현을 문자열 표현으로 변환합니다.

```sql
h3ToString (index)
```

** 파라미터 **

-`index` - Hexagon index number : Type : UInt64 (../../ sql-reference / data-types / int-uint.md).

** 반환 값 **

- H3 인덱스의 문자열 표현. 유형 : 문자열 (../../ sql-reference / data-types / string.md).

** 예 **

쿼리 :

```sql
SELECT h3ToString (617420388352917503) as h3_string
```

결과 :

```text
┌─h3_string───────┐
│ 89184926cdbffff │
└─────────────────┘
```

## stringToH3 {# stringtoh3}

캐릭터 라인 표현을 H3Index (UInt64) 표현으로 변환합니다.

```sql
stringToH3 (index_str)
```

** 파라미터 **

-`index_str` - String representation of the H3 index : Type : [문자열] (../../ sql-reference / data-types / string.md).

** 반환 값 **

- 육각형의 인덱스 번호. 오류의 경우는 0을 돌려줍니다. 유형 : UInt64 (../../ sql-reference / data-types / int-uint.md).

** 예 **

쿼리 :

```sql
SELECT stringToH3 ( '89184926cc3ffff') as index
```

결과 :

```text
┌──────────────index─┐
│ 617420388351344639 │
└────────────────────┘
```

## h3GetResolution {# h3getresolution}

인덱스의 해상도를 돌려줍니다.

** 구문 **

```sql
h3GetResolution (index)
```

** 파라미터 **

-`index` - Hexagon index number : Type : UInt64 (../../ sql-reference / data-types / int-uint.md).

** 반환 값 **

- 인덱스 해결책. 범위 :`[0, 15]`. 유형 : UInt8 (../../ sql-reference / data-types / int-uint.md).

** 예 **

쿼리 :

```sql
SELECT h3GetResolution (617420388352917503) as res
```

결과 :

```text
┌─res─┐
│ 9 │
└─────┘
```

원본 기사 (https://clickhouse.tech/docs/en/query_language/functions/geo/) <! - hide ->
