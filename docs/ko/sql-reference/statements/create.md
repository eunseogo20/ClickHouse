---
machine_translated : true
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd
toc_priority : 35
toc_title : CREATE
---

# 쿼리 만들기 {# create-queries}

## CREATE DATABASE {# query-language-create-database}

데이터베이스 만들기

```sql
CREATE DATABASE [IF NOT EXISTS] db_name [ON CLUSTER cluster] [ENGINE = engine (...)
```

### 절 {#clauses}

-`IF NOT EXISTS`
    만약`db_name` 데이터베이스가 이미 존재하는 경우, ClickHouse 새 데이터베이스를 작성하지 않고 :

    - 어구가 지정되어있는 경우는 예외를 throw하지 않습니다.
    - 어구가 지정되어 있지 않으면 예외를 throw합니다.

-`ON CLUSTER`
    클릭 하우스는`db_name` 데이터베이스의 모든 서버의 지정된 클러스터

-`ENGINE`

    - [MySQL] (../../ engines / database-engines / mysql.md)
        원격 MySQL 서버에서 데이터를 검색 할 수 있습니다.
        기본적으로 ClickHouse 자신의 것을 사용합니다 데이터베이스 (../../ engines / database-engines / index.md).

## CREATE TABLE {# create-table-query}

그`CREATE TABLE` 쿼리에는 여러 형태가 있습니다.

```sql
CREATE TABLE [IF NOT EXISTS] [db] table_name [ON CLUSTER cluster]
(
    name1 [type1] [DEFAULT | MATERIALIZED | ALIAS expr1] [compression_codec] [TTL expr1,
    name2 [type2] [DEFAULT | MATERIALIZED | ALIAS expr2] [compression_codec] [TTL expr2]
    ...
) ENGINE = engine
```

테이블을 만듭니다 'name'에서 'db'데이터베이스 'db'가 설정되어 있지 않다. 'engine'엔진
테이블의 구조는 열의 설명의 목록입니다. 경우의 지수에 대해서는 엔진으로 표시하고 매개 변수 테이블의 엔진입니다.

열에 대한 설명은 다음과 같습니다`name type` 가장 간단한 경우는. 예 :`RegionID UInt32`.
기본값에 대한 식을 정의 할 수 있습니다 (아래 참조).

```sql
CREATE TABLE [IF NOT EXISTS] [db] table_name AS [db2] name2 [ENGINE = engine]
```

다른 테이블과 동일한 구조의 테이블을 만듭니다. 테이블에 다른 엔진을 지정할 수 있습니다. 엔진이 지정되어 있지 않은 경우, 같은 엔진이 사용됩니다. `db2.name2` 테이블.

```sql
CREATE TABLE [IF NOT EXISTS] [db] table_name AS table_function ()
```

테이블을 만듭니다 구조와 데이터에 의해 반환되는 테이블 함수 (../ table-functions / index.md # table-functions).

```sql
CREATE TABLE [IF NOT EXISTS] [db] table_name ENGINE = engine AS SELECT ...
```

결과 같은 구조를 가진 테이블을 만듭니다. `SELECT` 쿼리는 'engine'엔진 충전에서 데이터를 선택합니다.

모든 경우에서`IF NOT EXISTS` 테이블이 이미 존재하는 경우, 쿼리는 오류를 반환하지 않습니다. 이 경우 쿼리는 아무것도하지 않습니다.

다음에 다른 어구가있을 수도 있습니다. `ENGINE` 쿼리 내의 절. 테이블을 만드는 방법에 대한 자세한 문서를 참조하십시오 [표 엔진 (../../ engines / table-engines / index.md # table_engines).

### 기본값 {# create-default-values}

열에 대한 설명은 다음 중 하나의 방법으로 기본값 식을 지정할 수 있습니다 :`DEFAULT expr``MATERIALIZED expr``ALIAS expr`.
예 :`URLDomain String DEFAULT domain (URL)`.

기본값 표현식이 정의되지 않은 경우 기본값은 수치의 경우는 0, 문자열의 경우 빈 문자열 배열의 경우는 하늘의 배열 및`1970-01-01` 날짜 또는 zero unix timestamp 시간과 날짜를 위해. Null은 지원되지 않습니다.

기본식이 정의되어있는 경우, 열의 형태는 선택 사항입니다. 명시 적으로 정의 된 형식이없는 경우는 기본 식의 형식이 사용됩니다. 예 :`EventDate DEFAULT toDate (EventTime)`- the 'Date'유형 'EventDate'라인.

데이터 유형과 기본식이 명시 적으로 정의되어있는 경우,이 식은 형변환 함수를 사용하여 지정된 형식으로 캐스팅됩니다. 예 :`Hits UInt32 DEFAULT 0`과 같은 것을 의미합니다`Hits UInt32 DEFAULT toUInt32 (0)`.

Default expressions may be defined as an arbitrary expression from table constants and columns. When creating and changing the table structure, it checks that expressions do not contain loops. For INSERT, it checks that expressions are resolvable - that all columns they can be calculated from have been passed.

`DEFAULT expr`

표준 기본값. INSERT 쿼리에 해당하는 열이 지정되어 있지 않으면 해당 식을 계산하여 입력됩니다.

`MATERIALIZED expr`

구체화 된 식. 이러한 열은 항상 계산되기 때문에 INSERT는 지정할 수 없습니다.
열 목록이없는 삽입의 경우이 열은 고려되지 않습니다.
또한 SELECT 쿼리에서 별표를 사용하는 경우이 열은 대체되지 않습니다. 이것은 덤프가 취득한 불변량을 보유하는 것입니다`SELECT *`열 목록을 지정하지 않고 INSERT를 사용하여 테이블에 삽입 할 수 있습니다.

`ALIAS expr`

동의어 이러한 열은 테이블에 전혀 저장되지 않습니다.
또한 SELECT 쿼리에서 별표를 사용하는 경우, 그 값은 대체되지 않습니다.
쿼리 구문 분석하는 동안 별칭을 열기 경우에는 Select에서 사용할 수 있습니다.

ALTER 쿼리를 사용하여 새 열을 추가하는 경우 이러한 열 오래된 데이터는 기록되지 않습니다. 대신, 새로운 열의 값을 가지지 않는 오래된 데이터를 읽을 때 기본적으로식이 그 자리에서 계산됩니다. 그러나 수식을 실행하려면 쿼리에 지정된 않은 다른 열이 필요한 경우,이 열은 또한 읽을 수 있지만 필요한 데이터 블록에 대해

테이블에 새 열을 추가하고 나중에 기본 식을 변경하면 이전 데이터에 사용되는 값이 변경됩니다 (값이 디스크에 저장되지 않은 데이터의 경우). 주의 실행하면 배경이 합병의 데이터 열을 결손의 합류 부에 적혀 합병합니다.

중첩 된 데이터 구조의 요소에 기본값을 설정할 수 없습니다.

### 제약 {#constraints}

열 기술 제약과 함께 정의 할 수 있습니다 :

```sql
CREATE TABLE [IF NOT EXISTS] [db] table_name [ON CLUSTER cluster]
(
    name1 [type1] [DEFAULT | MATERIALIZED | ALIAS expr1] [compression_codec] [TTL expr1,
    ...
    CONSTRAINT constraint_name_1 CHECK boolean_expr_1,
    ...
) ENGINE = engine
```

`boolean_expr_1` 모든 부울 표현식에서 가능합니다. 경우에 제약 조건 정의 테이블의 각 검사마다 행`INSERT` query. If any constraint is not satisfied - server will raise an exception with constraint name and checking expression.

추가 많은 제약이 될 가능성의 성능을 큰`INSERT` 쿼리.

### TTL 식 {# ttl-expression}

값의 저장 시간을 정의합니다. MergeTree-family 테이블에 대해서만 지정할 수 있습니다. 자세한 설명은 열 및 테이블의 TTL (../../ engines / table-engines / mergetree-family / mergetree.md # table_engine-mergetree-ttl).

### 열 압축 코덱 {#codecs}

기본적으로 ClickHouse는`lz4` 압축 방법. 때문에`MergeTree`- 엔진 제품군에서 기본 압축 방법을 변경할 수 있습니다. [압축] (../../ operations / server-configuration-parameters / settings.md # server-settings-compression) 서버 구성 섹션. 또한 압축 방법을 정의 할 수 있습니다. `CREATE TABLE` 쿼리.

```sql
CREATE TABLE codec_example
(
    dt Date CODEC (ZSTD)
    ts DateTime CODEC (LZ4HC)
    float_value Float32 CODEC (NONE)
    double_value Float64 CODEC (LZ4HC (9))
    value Float32 CODEC (Delta, ZSTD)
)
ENGINE = <Engine>
...
```

코덱이 지정되어있는 경우 기본 코덱이 적용되지 않습니다. 코덱은 파이프 라인으로 결합 할 수 있습니다. `CODEC (Delta, ZSTD)`.의 선정과 대형 브릿지 다이오드 코덱 조합합니다 프로젝트 벤치 마크와 마찬가지로 기재된 Altinity [ClickHouse의 효율성을 개선하는 새로운 부호화 (https://www.altinity.com / blog / 2019 / 7 / new-encodings-to-improve-clickhouse) 기사이다

!!! warning "경고"
    수없는 압축 ClickHouse 데이터베이스 파일을 외부의 것들처럼`lz4` 대신 특별한 [clickhouse- 압축기 (https://github.com/ClickHouse/ClickHouse/tree/master/programs/compressor) 유틸리티

압축은 다음 표 엔진에서 지원됩니다 :

- [메루게쯔리 (../../ engines / table-engines / mergetree-family / mergetree.md) 가족이다 지주의 압축 코덱의 선택의 기본 압축 방법에 의한 압축 (../../ operations / server-configuration-parameters / settings.md # server-settings-compression) 설정.
- 로그 (../../ engines / table-engines / log-family / index.md) 가족이다를 사용하여`lz4` 압축 메소드는 디폴트 대응 컬럼 압축 코덱.
- [세트] (../../ engines / table-engines / special / set.md) 기본 압축 만 지원했습니다.
- [참여] (../../ engines / table-engines / special / join.md) 기본 압축 만 지원했습니다.

ClickHouse 지원 공통의 목적 코덱과 전문의 코덱.

#### 특수 코덱 {# create-query-specialized-codecs}

이러한 코덱을주기위한 압축하여 효과적인 이용의 특징 데이터입니다. 이러한 코덱 중 일부는 데이터 자체를 압축하지 않습니다. 대신 일반적인 목적의 코덱을 위해 데이터를 준비하고이 준비가없는 경우보다 압축합니다.

특수 코덱 :

-`Delta (delta_bytes)`- Compression approach in which raw values ​​are replaced by the difference of two neighboring values, except for the first value that stays unchanged. Up to`delta_bytes` 델타 값을 저장하는 데 사용됩니다. `delta_bytes` 원시 값의 최대 크기입니다. 수`delta_bytes` 값 : 1,2,4,8. 기본값`delta_bytes`는`sizeof (type)`2, 4 또는 8 같으면. 다른 모든 경우에, 그것은 1입니다.
-`DoubleDelta` - Calculates delta of deltas and writes it in compact binary form. Optimal compression rates are achieved for monotonic sequences with a constant stride, such as time series data. Can be used with any fixed-width type. Implements the algorithm used in Gorilla TSDB, extending it to support 64 bit types. Uses 1 extra bit for 32-byte deltas : 5-bit prefixes instead of 4-bit prefixes. for additional information, see Compressing Time Stamps in [Gorilla : 빠르고 확장 가능한 메모리의 시계열 데이터베이스 (http://www.vldb.org/pvldb/vol8/p1816-teller.pdf).
-`Gorilla` - Calculates XOR between current and previous value and writes it in compact binary form. Efficient when storing a series of floating point values ​​that change slowly, because the best compression rate is achieved when neighboring values ​​are binary equal. Implements the algorithm used in Gorilla TSDB, extending it to support 64 bit types. For additional information, see Compressing Values ​​in [Gorilla : 빠르고 확장 가능한 메모리의 시계열 데이터베이스 (http://www.vldb.org/pvldb/vol8 /p1816-teller.pdf).
-`T64` - Compression approach that crops unused high bits of values ​​in integer data types (including`Enum``Date`와`DateTime`) 그 알고리즘의 각 단계에서 codec은 64의 값의 블록을 가지고 그들을 64x64 비트 행렬에 넣어 전치하고 사용되지 않는 값의 비트를 잘라 내고 나머지를시 미사용 비트는 압축이 사용되는 데이터 부분 전체의 최대 값과 최소값 사이에서 다르지 않다 비트입니다.

`DoubleDelta`와`Gorilla` Gorilla TSDB는 압축 알고리즘의 구성 요소로서 코덱이 사용되고있다. Gorilla 방법은 타임 스탬프 천천히 변화하는 일련의 값이있는 경우의 시나리오에 효과적입니다. 타임 스탬프는`DoubleDelta` 의해 효과적으로 압축됩니다`Gorilla` 코덱. 예를 들어, 효과적으로 저장된 테이블을 얻으려면 다음의 구성으로 만들 수 있습니다 :

```sql
CREATE TABLE codec_example
(
    timestamp DateTime CODEC (DoubleDelta)
    slow_values ​​Float32 CODEC (Gorilla)
)
ENGINE = MergeTree ()
```

#### 범용 코덱 {# create-query-general-purpose-codecs}

코덱 :

-`NONE` - No compression.
-`LZ4` - Lossless 데이터 압축 (https://github.com/lz4/lz4) 기본적으로 사용됩니다. LZ4 고속 압축을 적용합니다.
-`LZ4HC [(level)`- LZ4 HC (high compression) algorithm with configurable level. Default level : 9. Setting`level <= 0` 기본 수준을 적용합니다. 가능한 수준 : \ 1,12 \]. 권장 레벨 범위 : \ [4,9 \].
-`ZSTD [(level)`- [ZSTD 압축 알고리즘 (https://en.wikipedia.org/wiki/Zstandard) 구성 가능을 사용하여`level`. 가능한 레벨 : \ 1,22 \] . 기본값 : 1.

높은 압축 수준은 일단 압축 반복 압축 등의 비대칭 시나리오에 도움이됩니다. 높은 수준은 더 나은 압축 및 CPU 사용량을 의미합니다.

## 임시 테이블 {# temporary-tables}

ClickHouse는 다음의 특성을 가진 임시 테이블을 지원합니다 :

- 임시 테이블은 연결이 손실 된 경우를 포함하여 세션이 종료 될 때 소멸합니다.
- 임시 테이블은 메모리 엔진만을 사용합니다.
- 임시 테이블에 DB를 지정할 수 없습니다. 데이터베이스 외부에 만들어집니다.
- 모든 클러스터 서버에서 분산 DDL 쿼리를 사용하여 임시 테이블을 만들 수 없습니다`ON CLUSTER`) :이 테이블은 현재 세션에만 존재합니다.
- 임시 테이블의 이름이 다른 테이블과 동일한 쿼리가 DB를 사용하지 않고 테이블 이름을 지정한 경우 임시 테이블이 사용됩니다.
- 분산 쿼리 처리는 쿼리에서 사용되는 임시 테이블이 원격 서버에 전달됩니다.

임시 테이블을 만들려면 다음 구문을 사용합니다 :

```sql
CREATE TEMPORARY TABLE [IF NOT EXISTS] table_name
(
    name1 [type1] [DEFAULT | MATERIALIZED | ALIAS expr1,
    name2 [type2] [DEFAULT | MATERIALIZED | ALIAS expr2]
    ...
)
```

대부분의 경우, 임시 테이블을 수동으로 작성되어 외부 데이터를 이용하기위한 쿼리에 대해 또는 배포`(GLOBAL) IN` 자세한 내용은 해당 항목을 참조하십시오

테이블을 사용하는 것은 가능 [엔진 = 메모리 (../../ engines / table-engines / special / memory.md) 임시 테이블 대신.

## 분산 DDL 쿼리 (ON CLUSTER 절) {# distributed-ddl-queries-on-cluster-clause}

그`CREATE``DROP``ALTER`, and`RENAME` 쿼리 지원의 분산 실행 클러스터
예를 들어, 다음 쿼리는`all_hits``Distributed` 각 호스트 테이블`cluster` :

```sql
CREATE TABLE IF NOT EXISTS all_hits ON CLUSTER cluster (p Date, i Int32) ENGINE = Distributed (cluster, default, hits)
```

이러한 쿼리를 제대로 실행하려면 각 호스트가 동일한 클러스터 정의를 가지고 있어야합니다 (설정 동기화를 쉽게하기 위해 ZooKeeper에서 대체 또한 ZooKeeper 서버에 연결해야합니다.
일부 호스트가 현재 사용할 수없는 경우 로컬 버전의 쿼리는 마지막으로 클러스터의 각 호스트에 구현됩니다. 단일 호스트에서 쿼리를 실행하는 순서는 보장됩니다.

## CREATE VIEW {# create-view}

```sql
CREATE [MATERIALIZED] VIEW [IF NOT EXISTS] [db] table_name [TO [db] name] [ENGINE = engine] [POPULATE] AS SELECT ...
```

뷰를 작성합니다. 일반 및 구체화 : 뷰의 두 종류가 있습니다.

일반 뷰는 데이터를 저장하지 않고 다른 테이블에서 읽기를 실행하면됩니다. 즉 일반 뷰는 저장된 쿼리에 지나지 않습니다. 뷰에서 읽을 때이 저장된 쿼리는 FROM 절 하위 쿼리로 사용됩니다.

예를 들어, 뷰를 작성했다고합니다 :

```sql
CREATE VIEW view AS SELECT ...
```

그리고 쿼리를 썼다 :

```sql
SELECT a, b, c FROM view
```

이 쿼리는 서브 쿼리의 사용과 완전히 동일합니다 :

```sql
SELECT a, b, c FROM (SELECT ...)
```

실현 뷰

실현보기를 만들 때`TO [db]. [table]`you must specify ENGINE - the table engine for storing data.

실현보기를 만들 때`TO [db]. [table]`사용하지 마세요`POPULATE`.

SELECT에서 지정된 테이블에 데이터를 삽입하면 삽입 된 데이터의 일부가이 SELECT 쿼리에 의해 변환 된 결과가 뷰에 삽입됩니다.

POPULATE을 지정하면 기존 테이블 데이터를 작성할 때 뷰에 삽입됩니다. `CREATE TABLE ... AS SELECT ...`. 그렇지 않으면 쿼리를 포함하고 데이터를 삽입하고 표를 작성한 후 작성했다. 뷰를 작성하는 동안 테이블에 삽입 된 데이터는 삽입되지 않기 때문에, POPULATE을 사용하는 것은 권장하지 않습니다.

A`SELECT` 쿼리를 포함 할 수`DISTINCT``GROUP BY``ORDER BY``LIMIT` ... Note that the corresponding conversions are performed independently on each block of inserted data. For example, if`GROUP BY `데이터 삽입시 집계되지만 삽입 된 데이터의 단일 패킷 내에서만 집계됩니다. 데이터는 더 이상 집계되지 않습니다. 예외는 다음과 같이 독립적으로 데이터 집계를 수행하는 엔진을 사용하는 경우입니다`SummingMergeTree`.

실행`ALTER` 실현보기 쿼리는 완전히 개발되지 않아 불편한 경우가 있습니다. 실현보기에서 구성을 사용하는 경우`TO [db] name`, 수`DETACH`보기 실행`ALTER` 대상 테이블의 경우 다음`ATTACH` 이전에 분리 된 (`DETACH`)보기.

뷰의 외관은 일반 테이블과 동일합니다. 예를 들어, 그들은 다음의 결과에 나열됩니다`SHOW TABLES` 쿼리.

뷰를 삭제하는 다른 쿼리가 없습니다. 뷰를 삭제하려면`DROP TABLE`.

## CREATE DICTIONARY {# create-dictionary-query}

```sql
CREATE DICTIONARY [IF NOT EXISTS] [db] dictionary_name [ON CLUSTER cluster]
(
    key1 type1 [DEFAULT | EXPRESSION expr1] [HIERARCHICAL | INJECTIVE | IS_OBJECT_ID,
    key2 type2 [DEFAULT | EXPRESSION expr2] [HIERARCHICAL | INJECTIVE | IS_OBJECT_ID,
    attr1 type2 [DEFAULT | EXPRESSION expr3,
    attr2 type2 [DEFAULT | EXPRESSION expr4]
)
PRIMARY KEY key1, key2
SOURCE (SOURCE_NAME (param1 value1 ... paramN valueN]))
LAYOUT (LAYOUT_NAME (param_name param_value))
LIFETIME ({MIN min_val MAX max_val | max_val})
```

만들기 외부 사전 (../../ sql-reference / dictionaries / external-dictionaries / external-dicts.md) 주어진 구조 (../../ sql-reference / dictionaries / external-dictionaries /external-dicts-dict-structure.md), 소스 (../../ sql-reference / dictionaries / external-dictionaries / external-dicts-dict-sources.md), 레이아웃 (../ ../sql-reference/dictionaries/external-dictionaries/external-dicts-dict-layout.md) 및 평생 (../../ sql-reference / dictionaries / external-dictionaries / external-dicts-dict- lifetime.md).

외부 사전 구조는 속성으로 구성됩니다. 사전 속성은 테이블의 열과 같이 지정됩니다. 필요한 속성 속성은 그 형태뿐 다른 모든 속성은 기본값이 있습니다.

사전에 따라 레이아웃 (../../ sql-reference / dictionaries / external-dictionaries / external-dicts-dict-layout.md) 하나 이상의 속성을 사전 키로 지정할 수 있습니다.

자세한 내용은 외부 사전] (../ dictionaries / external-dictionaries / external-dicts.md) 섹션

## CREATE USER {# create-user-statement}

를 만듭니다. [사용자] (../../ operations / access-rights.md # user-account-management).

### 구문 {# create-user-syntax}

```sql
CREATE USER [IF NOT EXISTS | OR REPLACE] name [ON CLUSTER cluster_name]
    [IDENTIFIED [WITH {NO_PASSWORD | PLAINTEXT_PASSWORD | SHA256_PASSWORD | SHA256_HASH | DOUBLE_SHA1_PASSWORD | DOUBLE_SHA1_HASH} BY { 'password'| 'hash'}]
    [HOST {LOCAL | NAME 'name'| REGEXP 'name_regexp'| IP 'address'| LIKE 'pattern'} [...] | ANY | NONE]
    [DEFAULT ROLE role [...]
    [SETTINGS variable [= value] [MIN [=] min_value] [MAX [=] max_value] [READONLY | WRITABLE] | PROFILE 'profile_name'] [...]
```

#### 식별 정보 {#identification}

사용자 ID에는 여러 방법이 있습니다 :

-`IDENTIFIED WITH no_password`
-`IDENTIFIED WITH plaintext_password BY 'qwerty'`
-`IDENTIFIED WITH sha256_password BY 'qwerty'` 또는`IDENTIFIED BY'password'`
-`IDENTIFIED WITH sha256_hash BY 'hash'`
-`IDENTIFIED WITH double_sha1_password BY 'qwerty'`
-`IDENTIFIED WITH double_sha1_hash BY 'hash'`

#### 사용자 호스트 {# user-host}

사용자 호스트는 ClickHouse 서버에 연결할 수있는 호스트입니다. 호스트를 지정할 수 있습니다. `HOST` 다음 방법으로 쿼리 섹션 :

-`HOST IP 'ip_address_or_subnetwork'` - User can connect to ClickHouse server only from the specified IP address or a 하위 네트워크 (https://en.wikipedia.org/wiki/Subnetwork) 예 :`HOST IP'192.168 .0.0 / 16'``HOST IP '2001 : DB8 :: / 32'`. 사용, 생산을 지정하기 만하면됩니다. `HOST IP` 요소 (IP 주소와 그 마스크)를 사용하고 있기 때문에`host`와`host_regexp` 때문에 다른 대기 시간을 제로로
-`HOST ANY` - User can connect from any location. This is default option.
-`HOST LOCAL` - User can connect only locally.
-`HOST NAME 'fqdn'` - User host can be specified as FQDN. For example,`HOST NAME'mysite.com'`.
-`HOST NAME REGEXP 'regexp'` - You can use [pcre (http://www.pcre.org/) 사용자 호스트를 지정할 때 정규 표현식. 예를 들어,`HOST NAME REGEXP '. * \. mysite \ .com'`.
-`HOST LIKE 'template'` - Allows you use the [LIKE] (../ functions / string-search-functions.md # function-like) 사용자 호스트 필터 연산자. 예를 들어,`HOST LIKE '%'`과 같다. `HOST ANY``HOST LIKE '% .mysite.com'` 모든 호스트를 필터링한다. `mysite.com` 도메인.

Host를 지정하는 다른 방법은 다음과 같습니다`@`사용자 이름 구문입니다. 예 :

-`CREATE USER mira @ '127.0.0.1'` - Equivalent to the`HOST IP` 구문.
-`CREATE USER mira @ 'localhost'` - Equivalent to the`HOST LOCAL` 구문.
-`CREATE USER mira@'192.168. %. % '`- Equivalent to the`HOST LIKE` 구문.

!!! info "경고"
    클릭 하우스`user_name @ 'address'` 전체적으로 사용자 이름으로. 따라서 기술적으로 만들 수 있습니다 여러 사용자`user_name` 그리고 나중에 다른 구조`@`우리는 이렇게하는 것을 권장하지 않습니다.

### 예 {# create-user-examples}

사용자 계정 만들기`mira` 암호로 보호`qwerty` :

```sql
CREATE USER mira HOST IP '127.0.0.1'IDENTIFIED WITH sha256_password BY 'qwerty'
```

`mira` 시작해야 귀하의 응용 프로그램을 호스트에 ClickHouse 서버 운행하고 있습니다.

사용자 계정 만들기`john` 역할을 할당이 역할을 기본으로한다 :

```sql
CREATE USER john DEFAULT ROLE role1, role2
```

사용자 계정 만들기`john` 자신의 미래의 역할을 모두 기본값으로 설정 :

```sql
ALTER USER user DEFAULT ROLE ALL
```

어떤 역할이 할당 될 때`john` 미래에 자동으로 기본 설정됩니다.

사용자 계정 만들기`john` 하 모든 그의 앞으로의 역할은 기본 제외`role1`와`role2` :

```sql
ALTER USER john DEFAULT ROLE ALL EXCEPT role1, role2
```

## CREATE ROLE {# create-role-statement}

를 만듭니다. [역할] (../../ operations / access-rights.md # role-management).

### 구문 {# create-role-syntax}

```sql
CREATE ROLE [IF NOT EXISTS | OR REPLACE] name
    [SETTINGS variable [= value] [MIN [=] min_value] [MAX [=] max_value] [READONLY | WRITABLE] | PROFILE 'profile_name'] [...]
```

### 설명 {# create-role-description}

역할은 '권한 (grant.md # grant-privileges) 롤에 부여 된 사용자는이 역할의 모든 권한을 가져옵니다.

사용자가 지정할 수 여러 역할입니다. 사용자에게 적용 할 수있는 것을 보조 역할의 조합에 의한 [SET ROLE (misc.md # set-role-statement) 계산서. 특권의 최종 범위는 적용된 모든 역할의 모든 권한의 조합입니다. 사용자가 권한을 부여 직접 한번의 사용자 계정 또는 권한을 부여한다.

사용자가 기본 역할을 응용 한 사용자 로그인합니다. 기본 역할을 설정하려면 SET DEFAULT ROLE (misc.md # set-default-role-statement) 문 또는 ALTER USER] (alter.md # alter-user-statement) 계산서.

롤을 취소하려면 REVOKE (revoke.md) 계산서.

롤을 삭제하려면 DROP ROLE (misc.md # drop-role-statement) 계산서. 삭제 된 역할은 부여 된 모든 사용자 및 역할에서 자동으로 취소됩니다.

### 예 {# create-role-examples}

```sql
CREATE ROLE accountant;
GRANT SELECT ON db. * TO accountant;
```

이 일련의 쿼리는 역할을 만듭니다`accountant` 그것은에서 데이터를 읽을 수있는 권한을 가지고 있습니다`accounting` 데이터베이스.

사용자에게 역할 부여`mira` :

```sql
GRANT accountant TO mira;
```

후 역할이 부여 된 사용자가 사용할 수있는 실행시킵니다. 예를 들면 :

```sql
SET ROLE accountant;
SELECT * FROM db. *;
```

## CREATE ROW POLICY {# create-row-policy-statement}

를 만듭니다. 행 필터 (../../ operations / access-rights.md # row-policy-management) 사용자가 테이블에서 읽을 수 있습니다.

### 구문 {# create-row-policy-syntax}

```sql
CREATE [ROW] POLICY [IF NOT EXISTS | OR REPLACE] policy_name [ON CLUSTER cluster_name] ON [db] table
    [AS {PERMISSIVE | RESTRICTIVE}]
    [FOR SELECT]
    [USING condition]
    [TO {role [...] | ALL | ALL EXCEPT role [...]}]
```

#### 섹션 AS {# create-row-policy-as}

이 섹션

확인 정책에 액세스가 부여되어 있습니다. 같은 테이블에 적용되는 빠밋 시브 정책은 boolean를 사용하여 결합됩니다`OR` 운영자 정책은 기본적으로 허용되어 있습니다.

제한적인 정책 접근 제한합니다. 같은 테이블에 적용되는 제한 정책은 부울 값을 사용하여 결합됩니다`AND` 운영자

제한 정책은 허용 필터를 통과 한 행에 적용됩니다. 제한적인 정책을 설정해도 허용적인 정책을 설정하지 않으면 사용자는 테이블에서 행을 검색 할 수 없습니다.

#### 섹션에 {# create-row-policy-to}

섹션`TO` 역할과 사용자의 혼합 목록을 지정할 수 있습니다. `CREATE ROW POLICY ... TO accountant, john @ localhost`.

키워드`ALL` 현재 사용자를 포함한 모든 ClickHouse 사용자를 의미합니다. 키워드`ALL EXCEPT` allow to는 모든 사용자 목록에서 일부 사용자를 제외합니다. `CREATE ROW POLICY ... TO ALL EXCEPT accountant, john @ localhost`

### 예 {#examples}

-`CREATE ROW POLICY filter ON mydb.mytable FOR SELECT USING a <1000 TO accountant, john @ localhost`
-`CREATE ROW POLICY filter ON mydb.mytable FOR SELECT USING a <1000 TO ALL EXCEPT mira`

## CREATE QUOTA {# create-quota-statement}

를 만듭니다. 할당량 (../../ operations / access-rights.md # quotas-management) 사용자 또는 역할에 할당 할 수 있습니다.

### 구문 {# create-quota-syntax}

```sql
CREATE QUOTA [IF NOT EXISTS | OR REPLACE] name [ON CLUSTER cluster_name]
    [KEYED BY { 'none'| 'user name'| 'ip address'| 'client key'| 'client key or user name'| 'client key or ip address'}]
    [FOR [RANDOMIZED] INTERVAL number {SECOND | MINUTE | HOUR | DAY | WEEK | MONTH | QUARTER | YEAR}
        {MAX {{QUERIES | ERRORS | RESULT ROWS | RESULT BYTES | READ ROWS | READ BYTES | EXECUTION TIME} = number} [...] |
         NO LIMITS | TRACKING ONLY} [...]
    [TO {role [...] | ALL | ALL EXCEPT role [...]}]
```

### 예 {# create-quota-example}

현재 사용자 쿼리의 최대 수를 123 개월 이내로 제한하는 제약 :

```sql
CREATE QUOTA qA FOR INTERVAL 15 MONTH MAX QUERIES 123 TO CURRENT_USER
```

## CREATE SETTINGS PROFILE {# create-settings-profile-statement}

를 만듭니다. 설정 프로파일 (../../ operations / access-rights.md # settings-profiles-management) 사용자 또는 역할에 할당 할 수 있습니다.

### 구문 {# create-settings-profile-syntax}

```sql
CREATE SETTINGS PROFILE [IF NOT EXISTS | OR REPLACE] name [ON CLUSTER cluster_name]
    [SETTINGS variable [= value] [MIN [=] min_value] [MAX [=] max_value] [READONLY | WRITABLE] | INHERIT 'profile_name'] [...]
```

# 예 {# create-settings-profile-syntax}

를 만듭니다. `max_memory_usage_profile` 값과 제약을 가지는 설정 프로파일`max_memory_usage` 설정. 그것을 지정`robin` :

```sql
CREATE SETTINGS PROFILE max_memory_usage_profile SETTINGS max_memory_usage = 100000001 MIN 90000000 MAX 110000000 TO robin
```

원본 기사 (https://clickhouse.tech/docs/en/query_language/create/) <! - hide ->
