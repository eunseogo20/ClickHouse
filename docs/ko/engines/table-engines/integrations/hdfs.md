---
machine_translated : true
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd
toc_priority : 36
toc_title : HDFS
---

# HDFS {# table_engines-hdfs}

이 엔진은 [Apache Hadoop (https://en.wikipedia.org/wiki/Apache_Hadoop) 생태계 및 관리 데이터 [HDFS (https://hadoop.apache.org/docs/current/hadoop-project- dist / hadoop-hdfs / HdfsDesign.html) 클릭 하우스를 통해. 이 엔진은 동일합니다
에 [파일] (../ special / file.md # table_engines-file) 및 URL (../ special / url.md # table_engines-url) 엔진이 Hadoop 고유의 기능을 제공합니다.

## 사용법 {#usage}

```sql
ENGINE = HDFS (URI, format)
```

그`URI` 매개 변수는 HDFS의 파일 URI 전체입니다.
그`format` 매개 변수를 지정하거나 파일의 파일 형식 실행하려면
`SELECT`이 형식은 입력과 실행을 위해 지원해야합니다
`INSERT` queries - for output. The available formats are listed in the
[형식] (../../../ interfaces / formats.md # formats) 섹션
경로 부분`URI` globs를 포함 할 수 있습니다. 이 경우, 테이블은 readonly입니다.

** 예 **

** 1 ** 설치`hdfs_engine_table` 테이블 :

```sql
CREATE TABLE hdfs_engine_table (name String, value UInt32) ENGINE = HDFS ( 'hdfs : // hdfs1 : 9000 / other_storage', 'TSV')
```

** 2 ** Fill 파일 :

```sql
INSERT INTO hdfs_engine_table VALUES ( 'one', 1), ( 'two', 2), ( 'three', 3)
```

** 3 ** 데이터 조회 :

```sql
SELECT * FROM hdfs_engine_table LIMIT 2
```

```text
┌─name─┬─value─┐
│ one │ 1 │
│ two │ 2 │
└──────┴───────┘
```

## 구현의 세부 사항 {# implementation-details}

- 읽고 쓸 수있는 병렬
- 지원하지 않습니다 :
    -`ALTER`와`SELECT ... SAMPLE` 작전이다
    - 인덱스.
    - 복제.

** 경로의 로브 **

여러 빠스콘뽀네에 대한 처리중인 파일이 존재하는 일치의 경로 패턴입니다. 파일의 목록은`SELECT` (안`CREATE` 순간).

-`*`- Substitutes any number of any characters except`/`빈 문자열을 포함한다.
-`?`- Substitutes any single character.
-`{some_string, another_string, yet_another_one}`- Substitutes any of strings` 'some_string', 'another_string', 'yet_another_one'`.
-`{N..M}`- Substitutes any number in range from N to M including both borders.

구조와`{}`에 유사합니다 [원격] (../../../ sql-reference / table-functions / remote.md) 테이블 함수.

** 예 **

1. HDFS에 다음 Uri를 가진 TSV 형식의 파일이 일부 있다고합니다 :

- 'hdfs : // hdfs1 : 9000 / some_dir / some_file_1'
- 'hdfs : // hdfs1 : 9000 / some_dir / some_file_2'
- 'hdfs : // hdfs1 : 9000 / some_dir / some_file_3'
- 'hdfs : // hdfs1 : 9000 / another_dir / some_file_1'
- 'hdfs : // hdfs1 : 9000 / another_dir / some_file_2'
- 'hdfs : // hdfs1 : 9000 / another_dir / some_file_3'

1. あはいくつ 가지 방법을 생각할 수있는 테이블의 구성은 모든 파일 :

<! - ->

```sql
CREATE TABLE table_with_range (name String, value UInt32) ENGINE = HDFS ( 'hdfs : // hdfs1 : 9000 / {some, another} _dir / some_file_ {1..3}' 'TSV')
```

다른 방법 :

```sql
CREATE TABLE table_with_question_mark (name String, value UInt32) ENGINE = HDFS ( 'hdfs : // hdfs1 : 9000 / {some, another} _dir / some_file_?', 'TSV')
```

테이블은 모든 파일의 두 디렉토리 (모든 파일이 충족되어야 형식은 스키마에 기재된 쿼리) :

```sql
CREATE TABLE table_with_asterisk (name String, value UInt32) ENGINE = HDFS ( 'hdfs : // hdfs1 : 9000 / {some, another} _dir / *', 'TSV')
```

!!! warning "경고"
    파일`?`.

** 예 **

이렇게 생성 된 테이블과 파일 이름`file000``file001` ...`file999` :

```sql
CREARE TABLE big_table (name String, value UInt32) ENGINE = HDFS ( 'hdfs : // hdfs1 : 9000 / big_dir / file {0..9} {0..9} {0..9}' 'CSV')
```

## 가상 열 {# virtual-columns}

-`_path` - Path to the file.
-`_file` - Name of the file.

** 참고하십시오. **

- 가상 열 (../ index.md # table_engines-virtual_columns)

원본 기사 (https://clickhouse.tech/docs/en/operations/table_engines/hdfs/) <! - hide ->
