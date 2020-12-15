--- 
machine_translated : true 
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd 
toc_priority : 30 
toc_title : "\ u30E1 \ u30EB \ u30B2 \ u30C4 \ u30EA \ u30FC" 
--- 

# 메루게쯔리 {# table_engines-mergetree} 

그`MergeTree`이 가족의 엔진 그리고 다른 엔진 (`* MergeTree`)는 가장 강력한 ClickHouse 테이블 엔진입니다. 

엔진`MergeTree` 제품군은 매우 많은 양의 데이터를 테이블에 삽입 할 수 있도록 설계되어 있습니다. 데이터가 기록 된 테이블 부, 규칙 적용을위한 통합 부품입니다. 이 방법은 효율적으로 지속적으로 갱신 데이터 저장 중을 지원하지 않습니다. 

주요 특징 : 

- 기본 키 

    이것을 만들 수있는 소형 느슨하게 지표를 찾아 데이터를 빠르게합니다. 

-이 경우 분할 키 (custom-partitioning-key.md)이 지정된다. 

    ClickHouse 같은 결과를 가진 동일한 데이터에 대한 일반적인 작업보다 효과적인 파티션이있는 특정 작업을 지원합니다. ClickHouse도 자동으로 차단하면 파티션 데이터의 파티셔닝 키에 지정된 쿼리. 이 개선하기위한 쿼리.

- 데이터 복제를 지원합니다. 

    가족`ReplicatedMergeTree` 테이블을 제공 데이터 복제 자세한 내용은 데이터 복제 (replication.md) 

- 데이터 샘플링입니다. 

    필요에 따라 테이블에서 데이터 샘플링 방법을 설정할 수 있습니다. 

!!! info "정보" 
    의 병합 (../ special / merge.md # merge) 엔진에 속하지 않습니다`* MergeTree` 가족이다 

## 테이블 만들기 {# table_engine-mergetree-creating-a- table} 

```sql 
CREATE tABLE [IF NOT EXISTS] [db] table_name [ON CLUSTER cluster] 
( 
    name1 [type1] [DEFAULT | MATERIALIZED | ALIAS expr1] [TTL expr1, 
    name2 [type2] [DEFAULT | MATERIALIZED | ALIAS expr2] [TTL expr2, 
    ... 
    INDEX index_name1 expr1 TYPE type1 (...) GRANULARITY value1,  
    INDEX index_name2 expr2 TYPE type2 (...) GRANULARITY value2
) ENGINE = MergeTree () 
[PARTITION BY expr] 
[ORDER BY expr] 
[PRIMARY KEY expr] 
[SAMPLE BY expr] 
[TTL expr [DELETE | TO DISK 'xxx'| TO VOLUME 'xxx', ...] 
[SETTINGS name = value, ...] 
` `` 

매개 변수에 대한 설명은 쿼리의 설명 만들기 (../../../ sql-reference / statements / create.md) 

!!! note "주" 
    `INDEX` 실험적인 기능 입니다. 데이터 생략 색인 (# table_engine-mergetree-data_skipping-indexes) 

### 쿼리 절 {# mergetree-query-clauses} 

-`ENGINE` - Name and parameters of the engine`ENGINE = MergeTree ()`. 그 `MergeTree` 엔진에는 매개 변수가 없습니다. 

-`PARTITION BY` - The 분할 키 (custom-partitioning-key.md) 

    월 영역 분할의 경우는`toYYYYMM (date_column)`식 여기에서`date_column` 형의 날짜가 열입니다 [ 날짜 (../../../ sql-reference / data-types / date.md) 여기서 파티션 이름은` "YYYYMM"`형식.

-`ORDER BY` - The sorting key. 

    열 또는 식의 튜플. 예 :`ORDER BY (CounterID, EventDate)`. 

-`PRIMARY KEY` - The primary key if it [소트 키와는 다릅니다 (# choosing-a-primary-key-that-differs-from-the-sorting- key) 

    기본적으로 기본 키는 소트 키와 동일합니다. `ORDER BY` 절). 따라서 대부분의 경우 다른`PRIMARY KEY` 절. 

-`SAMPLE BY` - An expression for sampling. 

    샘플링 식을 사용하는 경우, 기본 키에 그것을 포함해야합니다. 예 :`SAMPLE BY intHash32 (UserID) ORDER BY (CounterID, EventDate, intHash32 (UserID))`. 

-`TTL` - A list of rules specifying storage duration of rows and defining logic of automatic parts movement 디스크 및 볼륨 사이] (# table_engine-mergetree-multiple-volumes) 

    식에는 다음이 필요합니다`Date` 또는`DateTime` 결과로 열. 예 : 
    `TTL date + INTERVAL 1 DAY`

    규칙의 유형`DELETE | TO DISK 'xxx'| TO VOLUME 'xxx'`식이 충족되면 (현재 시간에 도달 한 경우) 파트에 대해 수행 할 동작을 지정합니다 : 만료 행 삭제 파트의 이동 (파트의 모든 (`TO DISK 'xxx'`) 또는 볼륨 (`TO VOLUME'xxx'`) 규칙의 기본 유형은 삭제합니다 (`DELETE`) 목록 여러 규칙 지정이 없어요`DELETE` 규칙 

    자세한 내용은을 참조하십시오 열 및 테이블의 TTL (# table_engine-mergetree-ttl) 

-`SETTINGS` - Additional parameters that control the behavior of the`MergeTree` : 

    -`index_granularity` - Maximum number of data rows between the marks of an index . Default value : 8192. See 데이터 저장 (# mergetree-data-storage). 
    -`index_granularity_bytes` - Maximum size of data granules in bytes. Default value : 10Mb. to restrict the granule size only by number of rows , set to 0 (not recommended) See 데이터 저장 (# mergetree-data-storage).
    -`enable_mixed_granularity_parts` - Enables or disables transitioning to control the granule size with the `index_granularity_bytes` 설정. 버전 19.11 이전`index_granularity` 제한 과립의 크기에 대한 설정. 그`index_granularity_bytes` 설정의 개선 ClickHouse 성능의 선정에서 데이터를 테이블의 큰 행 (수십, 수백 메가 바이트) 있고 테이블의 큰 행이 설정 테이블의 효율화`SELECT` 쿼리. 
    -`use_minimalistic_part_header_in_zookeeper` - Storage method of the data parts headers in ZooKeeper . If`use_minimalistic_part_header_in_zookeeper = 1` 그 사육사 상점이 적다. 자세한 내용은을 참조하십시오 설정 설명 (../../../ operations / server-configuration-parameters / settings.md # server-settings-use_minimalistic_part_header_in_zookeeper)에서 "Server configuration parameters".
    -`min_merge_bytes_to_use_direct_io` - The minimum data volume for merge operation that is required for using direct I / O access to the storage disk. When merging data parts, ClickHouse calculates the total storage volume of all the data to be merged . If the volume exceeds `min_merge_bytes_to_use_direct_io` 바이트 ClickHouse 직접 입출력 인터페이스를 사용하여 저장소 디스크에 데이터를 읽고 씁니다 (`O_DIRECT` 옵션). 만약`min_merge_bytes_to_use_direct_io = 0` 후 직접 I / O가 비활성화됩니다. 기본값 :`10 * 1024 * 1024 * 1024` 바이트 수.
        <a name="mergetree_setting-merge_with_ttl_timeout"> </a> 
    -`merge_with_ttl_timeout` - Minimum delay in seconds before repeating a merge with TTL . Default value : 86400 (1 day).
    -`write_final_mark` - Enables or disables writing the final index mark at the end of data part (after the last byte) Default value : 1. Do not turn it off. 
    -`merge_max_block_size` - Maximum number of rows in block for merge operations. Default value : 8192. 
    -`storage_policy` - Storage policy. See 다중 블록 장치를위한 데이터 저장 (# table_engine-mergetree-multiple-volumes) 

** 섹션 설정 예 ** 

```sql 
ENGINE MergeTree () PARTITION BY toYYYYMM (EventDate) ORDER BY (CounterID, EventDate, intHash32 (UserID)) SAMPLE BY intHash32 (UserID) SETTINGS index_granularity = 8192 
```
 
이 예를 들어 월별로 분할을 설정합니다.

또한 사용자 ID에 의해 해시로 샘플링하는 식을 설정합니다. 이렇게하면 각 테이블의 데이터를 의사 난수 화 할 수 있습니다`CounterID`와`EventDate`.을 정의하면 [SAMPLE] (../../../ sql-reference / statements / select / sample. md # select-sample-clause) 어구 데이터를 선택하면 ClickHouse는 일부 사용자에 대해 균등 한 난수 데이터 샘플을 돌려줍니다. 

그`index_granularity` 8192이 기본값이기 때문에 설정을 생략 할 수 있습니다. 

<details markdown = "1"> 

<summary> 추천되고 있지 않습니다 법률 테이블을 만들 </ summary> 

!!! attention "주의" 
    용도로는 사용하지 마십시오 방법으로 새 프로젝트 가능하면 이전 프로젝트 위의 방법으로 전환합니다. 

```sql 
CREATE TABLE [IF NOT EXISTS] [db] table_name [ON CLUSTER cluster] 
( 
    name1 [type1] [DEFAULT | MATERIALIZED | ALIAS expr1,  
    name2 [type2] [DEFAULT | MATERIALIZED | ALIAS expr2, 
    ... .
) ENGINE [=] MergeTree (date-column [, sampling_expression, (primary, key)


-`date-column` - The name of a column of the 날짜 (../../../ sql-reference / data-types / date.md) 타입. ClickHouse을 자동으로 파티션 달에 근거합니다. 파티션 이름은` "YYYYMM"`형식. 
-`sampling_expression` - An expression for sampling. 
-`(primary, key)`- Primary key : Type : [튜플 () (../../../ sql-reference / data-types / tuple.md ) 
-`index_granularity` - the granularity of an index. the number of data rows between the "marks"인덱스. 값 8192은 대부분의 작업에 적합합니다. 

** 예 ** 
 
```sql 
MergeTree (EventDate, intHash32 (UserID) (CounterID, EventDate, intHash32 (UserID)), 8192) 
```

그`MergeTree` 엔진은 메인 엔진의 구성 방법은 위의 예와 같이 구성되어 있습니다. 
</ details> 

## 데이터 저장 {# mergetree-data-storage} 

테이블의 데이터 부품의 분별하여 그 유효한 유형을 사용합니다.

데이터가 테이블에 삽입되면 개별 데이터 파트가 만들어 각각 기본 키에서 사전 순으로 정렬됩니다. 예를 들어, 기본 키가`(CounterID, Date)`파트의 데이터는 다음과 같이 정렬됩니다`CounterID` 및 각 중`CounterID` 그것은 붙일 수 있습니다`Date`. 

데이터에 속하는 다른 파티션 이 분리의 다른 부분입니다. 배경은 ClickHouse는 더 효과적인 저장을위한 데이터 부분을 합병한다. 다른 구획에 속하는 부분은 병합되지 않습니다. 병합기구는 동일한 기본 키를 가진 모든 행이 동일한 데이터 부분에 포함되는 것은 보증되지 않습니다. 

각 데이터 부분은 논리적으로 그래뉼로 분할됩니다. 과립은 데이터를 선택할 때 ClickHouse 읽을 최소의 불가분 데이터 세트입니다. ClickHouse 행이나 값을 분할하지 않기 때문에 각 그래뉼은 항상 정수의 행이 포함됩니다. 과립의 첫 번째 행은 행의 기본 키 값이 표시됩니다. 각 데이터 ClickHouse을 만들고 인덱스 파일을 저장하는 것입니다. 각 열에 대해 기본 키에 있는지 여부에 관계없이 ClickHouse에도 같은 표시가 포함됩니다. 이 마크 또는 데이터를 찾을 직렬 파일입니다.

과립의 크기에 의해 제한됩니다`index_granularity`와`index_granularity_bytes` 테이블 엔진의 설정. 과립 열 수는에 놓습니다`[1 index_granularity]`행의 크기에 따라 다양합니다. 과립의 크기는 초과 할 수 있습니다`index_granularity_bytes` 단일 행의 크기가 설정 값보다 큰 경우. 이 경우, 과립의 크기는 줄의 크기와 같다. 

## 쿼리의 기본 키와 인덱스 {# primary-keys-and-indexes-in-queries} 

을 가지고`(CounterID, Date)`예로 기본 키. 이 경우, 정렬 및 인덱스는 다음과 같이 나타낼 수 있습니다 : 

      Whole data : ----------------------------- ----------------]
      CounterID : aaaaaaaaaaaaaaaaaabbbbcdeeeeeeeeeeeeefgggggggghhhhhhhhhiiiiiiiiikllllllll] 
      Date : 1111111222222233331233211111222222333211111112122222223111112223311122333]  
      Marks : | | | | | | | | | | | 
                      a 1 a 2 a 3 b 3 e 2 e 3 g, 1 h, 2 i 1 i 3 l 3
      Marks numbers : 0 1 2 3 4 5 6 7 8 9 10 

데이터 쿼리를 지정하는 경우 : 

-`CounterID in ( 'a', 'h')`서버는 마크 범위의 데이터를 읽습니다`[0, 3)`와`[6, 8)`. 
-`CounterID IN ( 'a', 'h') AND Date = 3` 서버는 마크의 범위의 데이터를 읽기 하다합니다`[1, 3)`와`[7, 8)`. 
-`Date = 3` 서버는 마크의 범위에서 데이터를 읽습니다`[1, 10]`. 

위의 예는 항상 사용하는 것이 더 효과적인 지표는 전체 검사! 

에 핍 지수에서 추가 데이터를 읽어들입니다. 기본 키의 단일 범위를 읽을 때`index_granularity * 2` 여분의 열 각 데이터 블록 읽을 수 있습니다. 

느슨하게 지표 수있는 작업은 매우 많은 테이블 행에서 종종 지수는 컴퓨터 응용 프로그램입니다.

ClickHouse는 고유의 기본 키는 필요하지 않습니다. 같은 기본 키에 여러 행을 삽입 할 수 있습니다. 

### 기본 키의 선택 {# selecting-the-primary-key} 

기본 키 열 수는 명시 적으로 제한되지 않습니다. 데이터 구조에 따라 기본 키에 다소 포함될 수 있습니다. 이것은 : 

- 색인의 성능을 향상시킵니다.

    주키가`(a, b)`그런 다음 다른 열을 추가합니다`c` 다음의 조건을 만족하면 성능이 향상됩니다 : 

    - 열에 조건을 가진 쿼리가 있습니다`c`. 
    - 긴 데이터 범위 (긴 데이터 범위보다 몇 배 긴`index_granularity`)와 동일한 값을 가진다. `(a, b)`일반적입니다. 즉, 다른 열을 추가하면 꽤 긴 데이터 범위를 건너 뛸 수 있습니다. 

- 데이터 압축을 개선합니다. 

    ClickHouse는 기본 키에 의해 데이터를 정렬하므로 일관성이 높을수록 압축이 좋아집니다. 

- 추가적인 로직이 통합 데이터 부분의 축소 병합 트리 (collapsingmergetree.md # table_engine-collapsingmergetree) 및 서밍 마 게임 트리 (summingmergetree.md) 엔진이다 

    이 경우 * 소트 키 * 이것은 기본 키는 다릅니다. 

긴 주키는 삽입의 성능 및 메모리 소비에 악영향을 미치고 있지만, 기본 키 추가 열은 ClickHouse의 성능에 영향을주지 않습니다`SELECT` 쿼리. 

### 정렬 키와 다른 기본 키 선택 {# choosing-a-primary key-that-differs-from-the-sorting-key} 

소트 키 (데이터 부분의 행을 정렬하는 식)과는 다른 주 키 (마크마다 인덱스 파일에 기록되는 값을 가지는 식)를 지정할 수 있습니다. 이 경우, 주키 식 튜플 정렬 키 식 튜플의 접두사해야합니다.
 
이 기능은 합산 마 게임 트리 (summingmergetree.md) 와 
[AggregatingMergeTree (aggregatingmergetree.md) 테이블 엔진. 일반적인 경우는 이용하시는 경우 엔진의 테이블은 두 종류의 컬럼 : * 크기 *와 * 대책 * 전형적인 쿼리는 어떤 메이저 열의 값을 집계합니다`GROUP BY` 그리고 차원 에 의한 여과. Sumingmergetree과 AggregatingMergeTree는 정렬 키 값이 같은 행을 집계하기 위해 모든 차원을 추가하는 것은 자연 스럽습니다. 그 결과, 키 식은 열 긴 목록으로 구성되며,이 목록은 수시로 새롭게 추가 된 차원으로 업데이트해야합니다. 

이 경우 효율적인 범위 스캔을 제공하고 나머지 차원 열을 정렬 키타뿌루에 추가하는 기본 키는 수열만을 남길 말이 

[ALTER] (../../../ sql-reference / statements / alter.md) 새로운 열이 테이블과 정렬 키에 동시에 추가되면 기존 데이터 부분을 변경할 필요가 없기 때문에 소트 키의 가벼운 작업입니다. 오래된 정렬 키는 새로운 정렬 키 접두사이며, 새롭게 추가 된 열에 데이터가 없기 때문에 테이블의 변경의 순간에 데이터 신구 정렬 

### 쿼리에서 인덱스와 파티션의 사용 { # use-of-indexes-and-partitions-in-queries}

때문에`SELECT` 클릭 ClickHouse을 분석 여부의 지표를 사용할 수 있습니다. 인덱스는 다음의 경우에 사용할 수 있습니다`WHERE / PREWHERE` 절에는 등가 비교 또는 부등식 연산을 나타내는 식 (연결 요소 중 하나로서, 또는 완전히)가 있습니다. `IN` 또는`LIKE` 기본 키 또는 파티션 키의 열 또는 표현식 또는 이들의 열의 특정 부분으로 반복 기능, 또는 이러한 식의 논리적 관계에 고정 뿌레휘ッ

따라서 기본 키 중 하나 또는 더 많은 범위에서 쿼리를 빠르게 수행 할 수 있습니다. 이 예에서는 특정 추적 태그 특정 태그 및 날짜 범위 특정 태그와 날짜, 날짜 범위를 가진 여러 태그 등에 대해 쿼리를 실행하면 쿼리가 빠르게 

다음과 같이 구성된 엔진을 보고하자 : 

      ENGINE MergeTree () PARTITION BY toYYYYMM (EventDate) ORDER BY (CounterID, EventDate) SETTINGS index_granularity = 8192 

이 경우 쿼리 : 

```sql 
SELECT count () FROM table WHERE EventDate = toDate (now ( )) AND CounterID = 34 
SELECT count () FROM table WHERE EventDate = toDate (now ()) AND (CounterID = 34 OR CounterID = 42)
SELECT count () FROM table WHERE ((EventDate> = toDate ( '2014-01-01') AND EventDate <= toDate ( '2014-01-31')) OR EventDate = toDate ( '2014-05-01') ) AND CounterID IN (101500, 731962, 160656) AND (CounterID = 101500 OR EventDate! = toDate ( '2014-05-01')) 
``` 

ClickHouse의 기본 키 지표의 트림 잘못된 데이터를 매월 분할 키 브로셔, 홈페이지 칸막이 부적절한 날. 

위의 쿼리의 인덱스가 사용되는 경우에도 복잡한 표현입니다. 테이블에서 읽기이 있었다를 사용한 지표 수な가득 채 웁니다. 

다음 예제에서는 인덱스는 사용할 수 없습니다. 

```sql 
SELECT count () FROM table WHERE CounterID = 34 OR URL LIKE '% upyachka %' 
``` 

쿼리를 실행할 때 ClickHouse이 인덱스를 사용할 수 있는지 여부를 확인하려면 설정을 사용합니다 [force_index_by_date (../../../ operations / settings / settings.md # settings-force_index_by_date) 및 force_primary_key (../../../ operations / settings / settings.md).

분할에 의한 달에서 읽기 전용이 데이터 블록을 포함하기 때문에 연설에 표시된 범위 내에서 적절하게 취급합니다. 이 경우, 데이터 블록에는 많은 날짜 (월 전체까지)의 데이터가 포함될 수 있습니다. 블록에서 데이터는 기본 키에 의해 정렬됩니다. 따라서 기본 키 접두사를 지정하지 날짜 조건 만 쿼리를 사용하면 단일 날짜보다 많은 데이터를 읽습니다. 

### 부분적으로 단조로운 기본 키 인덱스의 사용 {# use-of-index-for-partially-monotonic-primary-keys} 

예를 들어, 달의 일을 생각해 봅시다. 그들은 a을 형성 [단조 계열 (https://en.wikipedia.org/wiki/Monotonic_function) 한 달, 그러나 더 장기간 단조로운 없습니다. 이것은 부분적으로 단조로운 순서입니다. 사용자가 부분적으로 단조로운 기본 키 테이블을 만들 때 ClickHouse 정상적으로 스파 스 인덱스를 만듭니다. 사용자가 이런 종류의 테이블에서 데이터를 선택하면 ClickHouse은 쿼리 조건을 분석합니다. 이것은 쿼리 매개 변수와 인덱스 마크 사이의 거리를 계산할 수 있기 때문입니다. 

쿼리 매개 변수 범위의 기본 키 값이 단조로운 순서를 나타내지 않는 경우 ClickHouse는 인덱스를 사용할 수 없습니다. 이 경우 ClickHouse는 전체 검사 방법을 사용합니다. 

ClickHouse는이 논리를 달의 날짜 순서뿐만 아니라 부분적으로 단조로운 순서를 나타내는 기본 키도 사용합니다. 

### 데이터 건너 인덱스 (실험) {# table_engine-mergetree-data_skipping-indexes}

인덱스 선언은`CREATE` 쿼리. 

```sql 
INDEX index_name expr TYPE type (...) GRANULARITY granularity_value 
``` 

에서 테이블을위한`* MergeTree` 가족 데이터의 비거리 지표를 지정할 수 있습니다. 

이러한 인덱스는 지정된 식에 대한 정보를 블록으로 집계합니다. `granularity_value` 과립 (과립의 크기는을 사용하여 지정됩니다`index_granularity` 테이블 엔진으로 설정). 그렇다면 이러한 집계는`SELECT` 쿼리를 감축 량의 데이터를로드 디스크에 의해 조작 큰 블록의 데이터를`where` 쿼리가 충족되지 않습니다. 

** 예 ** 

```sql 
CREATE TABLE table_name 
( 
    u64 UInt64, 
    i32 Int32, 
    s String, 
    ... 
    INDEX a (u64 * i32, s) TYPE minmax GRANULARITY 3, 
    INDEX b (u64 * length (s)) TYPE set (1000) GRANULARITY 4 
) ENGINE = MergeTree () 
... 
```

이 경우 인덱스를 ClickHouse에서 사용하면 다음 쿼리 디스크에서 읽을 데이터 량을 줄일 수 있습니다 : 

```sql 
SELECT count () FROM table WHERE s < 'z' 
SELECT count () FROM table WHERE u64 * i32 == 10 AND u64 * length (s)> = 1234 
``` 

#### 사용 가능한 인덱스 유형 {# available-types-of-indices} 

-`minmax` 

    지정된 식의 극한값을 저장 합니다 (식이`tuple`의 각 요소의 극값을 저장합니다.`tuple`)를 사용하여 저장 정보의 비거리 블록과 같은 데이터는 유효한 유형을 사용합니다. 

- 'set (max_rows)` 

    지정된 식의 고유 한 값을 저장합니다. `max_rows` 행`max_rows = 0` 즉 "no limits") 값을 사용하여`WHERE` 식은 데이터 블록에서는 만족할 수 없습니다. 

-`ngrambf_v1 (n, size_of_bloom_filter_in_bytes, number_of_hash_functions, random_seed)`

    스토어 a [Bloom 필터 (https://en.wikipedia.org/wiki/Bloom_filter) 그러면 ngrams에서 블록의 데이터입니다. 문자열에서만 작동합니다. 최적화에 사용할 수있는`equals``like`와`in` 식. 

    -`n` - ngram size, 
    -`size_of_bloom_filter_in_bytes` - Bloom filter size in bytes (you can use large values here, for example, 256 or 512, because it can be compressed well) 
    -`number_of_hash_functions` - The number of hash functions used in the Bloom filter. 
    -`random_seed` - The seed for Bloom filter hash functions. 

-`tokenbf_v1 (size_of_bloom_filter_in_bytes, number_of_hash_functions, random_seed)` 

    와 같은`ngrambf_v1` 그러나 ngrams 대신 토큰을 저장합니다. 토큰은 영숫자가 아닌 문자로 구분 된 배열입니다.

-`bloom_filter (false_positive)`- Stores a [Bloom 필터 (https://en.wikipedia.org/wiki/Bloom_filter) 지정된 열의 경우. 

    어떤`false_positive` 매개 변수는 필터에서 위양성 응답을받을 확률입니다. 가능한 값 : (0,1). 기본값은 0.025입니다. 

    해당 데이터 형식 :`Int *``UInt *``Float *``Enum``Date``DateTime``String``FixedString``Array``LowCardinality``Nullable `. 

    다음 함수를 사용할 수 있습니다 : 같음 (../../../ sql-reference / functions / comparison-functions.md), notEquals (../../../ sql -reference / functions / comparison-functions.md) [시] (../../../ sql-reference / functions / in-functions.md), 노팅] (../../ .. /sql-reference/functions/in-functions.md),은 (../../../ sql-reference / functions / array-functions.md). 

<! - -> 

``` sql 
INDEX sample_index (u64 * length (s)) TYPE minmax GRANULARITY 4 
INDEX sample_index2 (u64 * length (str), i32 + f64 * 100, date str) TYPE set (100) GRANULARITY 4
INDEX sample_index3 (lower (str) str) TYPE ngrambf_v1 (3, 256, 2, 0) GRANULARITY 4 
``` 

#### 기능 지원 {# functions-support} 

의 조건`WHERE` 절에는 열 동작 함수 호출이 포함되어 있습니다. 열이 인덱스의 일부인 경우 ClickHouse 함수를 실행할 때이 인덱스를 사용하려고합니다. ClickHouse 지원 다른 일부 기능을 사용할 수 있습니다. 

그`set` 인덱스는 모든 기능과 사용 할 수있다. 기타 인덱스의 함수 일부를 다음 표에 나타냅니다. 

| 함수 (연산자) / 색인 | 주키 | minmax | ngrambf_v1 | tokenbf_v1 | bloom_filter name |  
| ---------------------------- -------------------------------------------------- ----------------------------- | -------- | -------- | - ----------- | ------------- | -------------------- |
| 같음 (= ==) (../../../ sql-reference / functions / comparison-functions .md # function-equals) | ✔ | ✔ | ✔ | ✔ | ✔ |
| [notEquals (! = \ <\>)] (../../../ sql-reference / functions / comparison-functions.md # function-notequals) | ✔ | ✔ | ✔ | ✔ | ✔ | 
| [같이] (../../../ sql-reference / functions / string-search-functions.md # function-like) | ✔ | ✔ | ✔ | ✗ | ✗ |  
| [notLike] (../ ../../sql-reference/functions/string-search-functions.md#function-notlike) | ✔ | ✔ | ✔ | ✗ | ✗ |
| 시작 ( ../../../sql-reference/functions/string-functions.md#startswith) | ✔ | ✔ | ✔ | ✔ | ✗ | 
| [endsWith (../../../ sql- reference / functions / string-functions.md # endswith) | ✗ | ✗ | ✔ | ✔ | ✗ | 
| [멀티 검색 (../ .. /../sql-reference/functions/string-search-functions.md#function-multisearchany) | ✗ | ✗ | ✔ | ✗ | ✗ | 
| [시] (../../../ sql-reference /functions/in-functions.md#in-functions) | ✔ | ✔ | ✔ | ✔ | ✔ | 
| [노팅] (../../../ sql-reference / functions / in-functions.md # in-functions) | ✔ | ✔ | ✔ | ✔ | ✔ |
| [less (\ <) (../../../ sql-reference / functions / comparison-functions.md # function-less) | ✔ | ✔ | ✗ | ✗ | ✗ |  
| [그레이터 (\>)] (../../../ sql- reference / functions / comparison-functions.md # function-greater) | ✔ | ✔ | ✗ | ✗ | ✗ |
| [lessOrEquals (\ <=) (../../../ sql-reference / functions / comparison-functions.md # function-lessorequals) | ✔ | ✔ | ✗ | ✗ | ✗ | 
| [greaterOrEquals (\> =) (../../../sql-reference/functions/comparison-functions.md#function-greaterorequals) | ✔ | ✔ | ✗ | ✗ | ✗ | 
| 비어 있음 (../../ .. /sql-reference/functions/array-functions.md#function-empty) | ✔ | ✔ | ✗ | ✗ | ✗ |
| [노텐뿌티 (../../../ sql-reference / functions / array-functions.md # function-notempty) | ✔ | ✔ | ✗ | ✗ | ✗ | 
| 헤이스트 켄 | ✗ | ✗ | ✗ | ✔ | ✗ | 
 
Ngram 크기 보다 작은 정수 인수를 가지는 함수는 다음과 같이 사용할 수 없습니다`ngrambf_v1` 쿼리 최적화를 위해.

블루에서 위양성 일치해야하므로`ngrambf_v1``tokenbf_v1`, and`bloom_filter` 인덱스는 함수의 결과가 false가 될 것으로 예상되는 쿼리 최적화에 사용할 수 없습니다. : 

- 최적화 할 수 있습니다 : 
    -`s LIKE '% test %'` 
    -`NOT s NOT LIKE '% test %'` 
    -`s = 1` 
    -`NOT s! = 1` 
    -`startsWith (s ' test ')` 
- 최적화 할 수없는 : 
    -`NOT s LIKE'% test % '` 
    -`s NOT LIKE'% test % ' 
    -`s! = 1` 
    -`NOT startsWith (s'test ')` 

## 동시 데이터 액세스 {# concurrent-data-access} 

동시 테이블 액세스는 멀티 버전 관리를 사용합니다. 즉, 테이블의 읽기와 업데이트가 동시에 발생하면 쿼리의 시점에서 현재의 부분 집합에서 데이터를 읽습니다. 긴 잠금은 없습니다. 삽입은 읽기 작업에 방해가되지 않습니다. 

테이블에서 읽기는 자동으로 병렬 처리됩니다. 

## 열 및 테이블의 TTL {# table_engine-mergetree-ttl} 

값의 유효 기간을 결정합니다.

그`TTL` 절은 테이블 전체 및 개별 열마다 설정할 수 있습니다. 테이블 수준의 TTL에 지정된 논리가 자동으로 이동 데이터 디스크 사이라고합니다. 

표현하고, 평가해야한다 다 날짜 (../../../ sql-reference / data-types / date.md) 또는 DateTime (../../../ sql -reference / data-types / datetime.md) 데이터 형식입니다. 

예 : 

```sql 
TTL time_column 
TTL time_column + interval 
``` 

정의하려면`interval`, use 시간 간격 (../../../ sql-reference / operators / index.md # operators- datetime) 연산자. 

```sql 
TTL date_time + INTERVAL 1 MONTH 
TTL date_time + INTERVAL 15 HOUR 
``` 

### 라인 TTL {# mergetree-column-ttl} 

열의 값이 만료되면 ClickHouse는 열의 데이터 형식의 기본값으로 대체합니다. 데이터 부분의 모든 열의 값이 만료 된 경우 ClickHouse은 파일 시스템의 데이터 부분에서이 열을 제거합니다. 

그`TTL` 절은 키 열은 사용할 수 없습니다. 

예 : 

TTL을 사용하여 테이블 만들기

```sql 
CREATE TABLE example_table 
( 
    d DateTime, 
    a Int TTL d + INTERVAL 1 MONTH, 
    b Int TTL d + INTERVAL 1 MONTH, 
    c String 
) 
ENGINE = MergeTree 
PARTITION BY toYYYYMM (d) 
ORDER BY d; 
``` 

기존 테이블의 열에 대한 TTL 추가 

```sql 
ALTER TABLE example_table 
    MODIFY COLUMN 
    c String TTL d + INTERVAL 1 DAY; 
``` 

열 TTL을 변경 

```sql 
ALTER TABLE example_table 
    MODIFY COLUMN 
    c String TTL d + INTERVAL 1 MONTH; 
``` 

### 테이블 TTL {# mergetree-table-ttl}

테이블에서의 표현의 제거에 종료되었습니다 열에 여러 표현을 자동으로 부품의 이동과 디스크 또한 (# table_engine-mergetree-multiple-volumes) 테이블의 행이 만료되면 ClickHouse 대응하는 모든 행을 삭제합니다. 부품 이동 피쳐의 경우 부품의 모든 행이 이동할 수있는 기준을 충족해야합니다. 

```sql 
TTL expr [DELETE | TO DISK 'aaa'| TO VOLUME 'bbb', ... 
``` 

TTL 규칙의 유형은 각 TTL 식에 따를 수 있습니다. 이것은식이 충족되면 (현재 시간에 도달)에 수행되는 작업에 영향을줍니다) : 

-`DELETE` 삭제 행을 종료했습니다 (기본 동작); 
-`TO DISK 'aaa'` - 디스크 부분의 이동`aaa`; 
-`TO VOLUME 'bbb'` - 디스크 부분의 이동`bbb`. 

예 : 

TTL을 사용하여 테이블 만들기 

```sql 
CREATE TABLE example_table 
(  
    d DateTime,
    a Int 
) 
ENGINE = MergeTree 
PARTITION BY toYYYYMM (d) 
ORDER BY d 
TTL d + INTERVAL 1 MONTH [DELETE]
    d + INTERVAL 1 WEEK TO VOLUME 'aaa', 
    d + INTERVAL 2 WEEK TO DISK 'bbb'; 
``` 

테이블의 TTL 변경 

```sql 
ALTER TABLE example_table 
    MODIFY TTL d + INTERVAL 1 DAY; 
``` 

** 데이터 삭제 ** 

만료 된 TTL 데이터는 ClickHouse 데이터 부분을 결합 할 때 제거된다. 

때 ClickHouse있는 데이터의 기간이 종료되었으므로, 행 off 일정에 예약 할 필요가 없습니다. 이러한 병합의 빈도를 제어하려면 다음과 같이 설정할 수 있습니다`merge_with_ttl_timeout`. 값이 너무 작 으면 대량의 자원을 소비 할 수있는 일정 외 병합이 다수 실행됩니다. 

를 실행하면`SELECT` 쿼리와 합병은 아니지만 가능하면 종료했습니다. 그것을 방지하려면 OPTIMIZE (../../../ sql-reference / statements / misc.md # misc_operations-optimize) 쿼리 앞에`SELECT`. 

## 여러 블록 장치를위한 데이터 저장 {# table_engine-mergetree-multiple-volumes} 

### 소개 {#introduction}

`MergeTree` 가족의 테이블 엔진에서 데이터를 여러 블록 장치 예를 들어, 특정 테이블의 데이터가 암시 적으로 다음과 같이 분할되는 경우에 유용합니다 "hot"과 "cold"최신 데이터는 정기적으로 요구되지만 약간의 공간 만 필요하지 않습니다. 반대로, fat-tailed 기록 데이터는 거의 요구되지 않습니다. 여러 디스크가 유효한 경우에, "hot"데이터는 고속 디스크 (예 NVMe Ssd 또는 메모리)에 배치 될 수 있습니다. "cold"데이터 - 비교적 느린 (예 : HDD). 

데이터 부분은 최소 이동 가능한 단위를위한 것입니다`MergeTree`- 엔진 테이블. 부분에 속하는 데이터는 하나의 디스크에 저장됩니다. 데이터 부분은 백그라운드에서 (사용자 설정에 따라) 디스크간에 이동할 수 있습니다. [ALTER] (../../../ sql-reference / statements / alter.md # alter_move-partition) 쿼리. 

### 용어 {#terms} 

- Disk - Block device mounted to the filesystem. 
- Storage policy - Set of volumes and the rules for moving data between them . 
- Default disk - Disk that stores the path specified in the 경로 (../../../ operations / server-configuration-parameters /settings.md#server_configuration_parameters-path) 서버 설정.
- Volume - Ordered set of equal disks (similar to [JBOD (https://en.wikipedia.org/wiki/Non-RAID_drive_architectures)) 
        <

의 명칭을 기재하기 때문에 시스템 테이블, 시스템 스토리지 정책 (../../../ operations / system-tables.md # system_tables-storage_policies) 및 시스템 디스크 (../../ ../operations/system-tables.md#system_tables-disks) 적용 설정을 저장 정책을위한 테이블을 사용`storage_policy` 설정`MergeTree`- 엔진 가족 테이블. 

### 설정 {# table_engine-mergetree-multiple-volumes_configure} 

디스크 볼륨 및 스토리지 정책은`<storage_configuration>`메인 파일에 태그를 붙이는`config.xml` 또는`config.d` 디렉토리. 

조직 구조 : 

```xml 
<storage_configuration> 
    <disks> 
        <disk_name_1> <! - disk name -> 
            <path> / mnt / fast_ssd / clickhouse / </ path> 
        <disk_name_2> 
            <path> / mnt / hdd1 / clickhouse / </ path> 
            <keep_free_space_bytes> 
        </ disk_name_2> 
        <disk_name_3> 
            <path> / mnt / hdd2 / clickhouse / </ path> 
            <keep_free_space_bytes> 10485760 </ keep_free_space_bytes> 
        </ disk_name_3> 

        ... 
    </ disks> 

    ... 
</ storage_configuration> 
``` 

태그 : 

-`<disk_name_N>`- Disk name. Names must be different for all disks. 
-`path` - path under which a server will store data (`data`와`shadow` 폴더)로 끝나야 수 있습니다. '/'. 
-`keep_free_space_bytes` - the amount of free disk space to be reserved . 

디스크 정의의 순서는 중요하지 않습니다. 

보관 정책 설정 태그 : 

```xml 
<
    <policies>
        <policy_name_1> 
            <volumes> 
                <volume_name_1> 
                    <disk> disk_name_from_disks_configuration </ disk> 
                    <max_data_part_size_bytes> 1073741824 </ max_data_part_size_bytes> 
                </ volume_name_1> 
                <volume_name_2> 
                    <! - configuration -> 
                </ volume_name_2> 
                <! - more volumes -> 
            </ volumes> 
            <move_factor> 0.2 </ move_factor> 
        </ policy_name_1> 
        <policy_name_2> 
            <! - configuration -> 
        </ policy_name_2> 

        <! - more policies ->
    </ policies> 
    ... 
</ storage_configuration> 
``` 

태그 : 

-`policy_name_N` - Policy name. Policy names must be unique. 
-`volume_name_N` - Volume name. Volume names must be unique. 
-`disk` - a disk within a volume. 
-`max_data_part_size_bytes` - the maximum size of a part that can be stored on any of the volume 's disks . 
-`move_factor` - when the amount of available space gets lower than this factor , data automatically start to move on the next volume if any (by default 0.1) 

구성의 예 : 

```xml 
<storage_configuration> 
    ... 
    <policies> 
                <single> <! - volume name -> 
        <hdd_in_order> <!- policy name ->
            <volumes> 
    </ policies>
                    <disk> disk1 </ disk> 
                    <disk> disk2 </ disk> 
                </ single> 
            </ volumes> 
        </ hdd_in_order> 

        <moving_from_ssd_to_hdd> 
            <volumes> 
                <hot> 
                    <disk> fast_ssd </ disk> 
                    <max_data_part_size_bytes> 1073741824 < / max_data_part_size_bytes> 
                </ hot> 
                <cold> 
                    <disk> disk1 </ disk> 
                </ cold> 
            </ volumes> 
            <move_factor> 0.2 </ move_factor>
        </ moving_from_ssd_to_hdd> 
```
    ... 
</ storage_configuration> 

주어진 예제에서는`hdd_in_order` 정책은 라운드 로빈 (https://en.wikipedia.org/wiki/Round-robin_scheduling) 접근이 이런 정책을 정의 기미 양 (`single`) 데이터 부분은 원형의 순서로 모든 디스크에 저장되어 있습니다. 이러한 정책 주별로 연구 결과에 대해 배우는 것과 동시에 유가 여러 개있는 경우 같은 디스크를 탑재 한 시스템이 RAID 설정을합니다. 개별 디스크 드라이브는 신뢰할 수 없기 때문에 복제 계수가 3 이상으로 보충 할 필요가있을 수 있습니다. 

시스템에서 사용 가능한 디스크의 종류가 다를`moving_from_ssd_to_hdd` 대신 정책을 사용할 수 있습니다. 볼륨`hot` SSD 디스크로 구성되어 있습니다 (`fast_ssd`)이 볼륨에 저장할 수있는 부품의 최대 크기는 1GB입니다. 1GB보다 큰 모든 부분에 직접 저장됩니다`cold` HDD 디스크를 포함하는 볼륨`disk1`. 
또한 한 번 디스크`fast_ssd` 80 % 이상에 의해 채워지고, 데이터로 이동합니다 얻습니다`disk1 `배경 프로세스에 의해. 

스토리지 정책의 볼륨 열거 순서는 중요합니다. 볼륨이 가득 차면 데이터는 다음 볼륨으로 이동됩니다. 데이터는 순차적으로 저장되기 때문에 디스크 열거 순서가 중요합니다. 

만들 때 테이블은 적용 설정을 저장 정책에서 : 

```sql 
CREATE TABLE table_with_non_default_policy ( 
    EventDate Date, 
    OrderID UInt64, 
    BannerID UInt64, 
    SearchPhrase String 
) ENGINE = MergeTree 
ORDER BY (OrderID, BannerID) 
PARTITION BY toYYYYMM (EventDate) 
SETTINGS storage_policy = 'moving_from_ssd_to_hdd' 
``` 

그`default` 스토리지 정책은 하나의 볼륨 만 사용하는 것을 의미합니다. `<path>`테이블을 작성하면 해당 스토리지 정책은 변경할 수 없습니다. 

### 자세한 {#details} 

의 경우`MergeTree` 테이블 데이터가있는 디스크는 다른 방법 : 

- 삽입의 결과로 (`INSERT` 쿼리). 
- 백그라운드 병합 중 [돌연변이] (../../../ sql-reference / statements / alter.md # alter-mutations) 
- 다른 복제본에서 다운로드 
- 파티션 동결의 결과로 [ALTER TABLE ... FREEZE pARTITION (../../../ sql-reference / statements / alter.md # alter_freeze-partition).

이러한 모든 경우를 제외하고는 돌연변이와 파티션의 동결은 일부가 저장된 대량의 디스크에 저장 정책 :
백그라운드에서 부품은 공간의 양에 따라 볼륨간에 이동됩니다 (`move_factor` 매개 변수) 볼륨이 설정 파일에 선언 된 순서에 따라.

1. 부품을 저장하는 데 충분한 디스크 공간이 첫 번째 볼륨 (정의 순서) (`unreserved_space> current_part_size`)로 지정된 크기의 부분을 저장할 수 있습니다 (`max_data_part_size_bytes> current_part_size`)가 선정된다. 
2.이 볼륨에서 데이터의 이전 청크를 저장하는 데 사용되는 파트 사이즈보다 여유 공간이 많은 디스크가 선택됩니다 (`unreserved_space - keep_free_space_bytes> current_part_size`) 

후드 돌연변이 및 분할 동결 이용합니다 하드 링크 (https://en.wikipedia.org/wiki/Hard_link) 하드 링크와 디스크는 지원하지 않기 때문에이 경우 부품의 보관과 동일한 디스크 초기입니다. 

데이터는 마지막 데이터에서 첫 번째 데이터로 전송되는 것은 아닙니다. 시스템 테이블 시스템 part_log (../../../ operations / system-tables.md # system_tables-part-log) (필드`type = MOVE_PART`) 및 시스템 부품 (../../ ../operations/system-tables.md#system_tables-parts) (필드`path`와`disk`) 배경의 움직임을 감시합니다. 또한 자세한 정보는 서버 로그에 기재되어 있습니다. 

사용자의 힘으로 이동중의 일부 또는 파티션에서 양 다른 쿼리 [ALTER TABLE ... MOVE PART \ | PARTITION ... TO VOLUME \ | DISK ... (../../../ sql-reference / statements / alter .md # alter_move-partition) 백그라운드 작업의 모든 제한이 고려됩니다. 쿼리는 혼자 이동을 시작하고 백그라운드 작업이 완료 될 때까지 대기하지 않습니다. 충분한 공간이 없거나 필요한 조건 중 하나라도 충족되지 않은 경우 사용자는 오류 메시지를 표시합니다. 

데이터의 이동은 데이터의 복제를 방해하지 않습니다. 따라서 다른 보관 정책을 지정할 수 있으며, 같은 테이블의 다른 복제본. 

백그라운드 병합과 돌연변이가 완료되면 오래된 부분은 일정 시간 후에 만 삭제됩니다 (`old_parts_lifetime`). 
그동안 다른 볼륨 또는 디스크는 이동되지 않습니다. 따라서 부품이 최종적으로 제거 될 때까지 그들은 점유 된 디스크 공간의 평가를 위해 고려된다.

원본 기사 (https://clickhouse.tech/docs/ru/operations/table_engines/mergetree/) <! - hide ->
