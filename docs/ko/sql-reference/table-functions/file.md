---
machine_translated : true
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd
toc_priority : 37
toc_title : "\ u30D5 \ u30A1 \ u30A4 \ u30EB"
---

# 파일 {#file}

파일에서 테이블을 만듭니다. 이 테이블 함수는 다음과 같습니다 [url] (url.md) 및 hdfs (hdfs.md) 완즈

```sql
file (path, format, structure)
```

** 입력 매개 변수 **

-`path` - The relative path to the file from [user_files_path (../../ operations / server-configuration-parameters / settings.md # server_configuration_parameters-user_files_path) 경로 파일을 지원 glob에 읽기 전용 모드` *``?`,`{abc, def}`와`{N..M}`어디`N``M` - numbers \`` 'abc', 'def'` - strings.
-`format` - The 형식 (../../ interfaces / formats.md # formats) 파일.
-`structure` - Structure of the table. Format` 'column1_name column1_type, column2_name column2_type ...'`.

** 반환 값 **

테이블의 지정된 구조를 읽거나 쓰는 데이터를 지정된 파일입니다.

** 예 **

설정`user_files_path` 그리고 파일의 내용`test.csv` :

```bash
$ grep user_files_path /etc/clickhouse-server/config.xml
    <user_files_path> / var / lib / clickhouse / user_files / </ user_files_path>

$ cat /var/lib/clickhouse/user_files/test.csv
    1,2,3
    3,2,1
    78,43,45
```

테이블에서`test.csv` 그리고 그때 처음 두 행의 선택 :

```sql
SELECT *
FROM file ( 'test.csv', 'CSV', 'column1 UInt32, column2 UInt32, column3 UInt32')
LIMIT 2
```

```text
┌─column1─┬─column2─┬─column3─┐
│ 1 │ 2 │ 3 │
│ 3 │ 2 │ 1 │
└─────────┴─────────┴─────────┘
```

```sql
- getting the first 10 lines of a table that contains 3 columns of UInt32 type from a CSV file
SELECT * FROM file ( 'test.csv', 'CSV', 'column1 UInt32, column2 UInt32, column3 UInt32') LIMIT 10
```

** 경로의 로브 **

여러 빠스콘뽀네에 대한 처리중인 파일이 존재하는 일치의 경로 패턴뿐만 아니라 접미사 또는 접두사).

-`*`- Substitutes any number of any characters except`/`빈 문자열을 포함한다.
-`?`- Substitutes any single character.
-`{some_string, another_string, yet_another_one}`- Substitutes any of strings` 'some_string', 'another_string', 'yet_another_one'`.
-`{N..M}`- Substitutes any number in range from N to M including both borders.

구조와`{}`에 유사합니다 [원격 테이블 기능 (../../ sql-reference / table-functions / remote.md)).

** 예 **

1. 다음 상대 경로를 사용하여 여러 파일이 있다고 가정합니다 :

- 'some_dir / some_file_1'
- 'some_dir / some_file_2'
- 'some_dir / some_file_3'
- 'another_dir / some_file_1'
- 'another_dir / some_file_2'
- 'another_dir / some_file_3'

1.이 파일의 행의 양을 조회합니다 :

<! - ->

```sql
SELECT count (*)
FROM file ( '{some, another} _dir / some_file_ {1..3}' 'TSV', 'name String, value UInt32')
```

1. 쿼리의 양 행의 모든 ​​파일 디렉토리 :

<! - ->

```sql
SELECT count (*)
FROM file ( '{some, another} _dir / *', 'TSV', 'name String, value UInt32')
```

!!! warning "경고"
    파일`?`.

** 예 **

쿼리에서 데이터 파일 이름`file000``file001` ...`file999` :

```sql
SELECT count (*)
FROM file ( 'big_dir / file {0..9} {0..9} {0..9}' 'CSV', 'name String, value UInt32')
```

## 가상 열 {# virtual-columns}

-`_path` - Path to the file.
-`_file` - Name of the file.

** 참고하십시오. **

- 가상 열 (https://clickhouse.tech/docs/en/operations/table_engines/#table_engines-virtual_columns)

원본 기사 (https://clickhouse.tech/docs/en/query_language/table_functions/file/) <! - hide ->
