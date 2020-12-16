---
machine_translated : true
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd
toc_priority : 46
toc_title : "\ u5165 \ u529B"
---

# 입력 {#input}

`input (structure)`-로 보내는 데이터를 효과적으로 바꾸고 삽입하는 것을 허용하는 표 기능
다른 구조를 가진 테이블에 지정된 구조를 가진 서버.

`structure` - 다음 형식으로 서버에 전송되는 데이터의 구조` 'column1_name column1_type, column2_name column2_type ...'`.
예를 들어,` 'id UInt32, name String'`.

이 함수는 다음 경우에만 사용할 수 있습니다`INSERT SELECT` 그렇지 않으면 보통 테이블 함수처럼 작동합니다
(예를 들어, 하위 쿼리 등으로 사용할 수 있습니다.)

데이터는 보통의 방법으로 전송 할 수 있습니다`INSERT` 쿼리 및 임의 사용 가능한 전달됩니다 형식 (../../ interfaces / formats.md # formats)
쿼리의 마지막에 지정해야합니다 (보통과는 다릅니다`INSERT SELECT`).

이 기능의 주요 특징은 서버가 클라이언트에서 데이터를 수신하는 동시에 변환하는 것입니다
식 목록에 따라`SELECT` 절과 대상 테이블에 삽입. 임시 테이블
전송 된 모든 데이터는 작성되지 않습니다.

** 예 **

-는`test` 테이블의 구조는 다음과 같습니다`(a String, b String)`
    그리고 데이터`data.csv` 다른 구조를 가지고 있습니다`(col1 String, col2 Date, col3 Int32)`삽입 쿼리
    에서 데이터`data.csv`에`test` 동시 변환 테이블은 다음과 같습니다 :

<! - ->

```bash
$ cat data.csv | clickhouse-client --query = "INSERT INTO test SELECT lower (col1), col3 * col3 FROM input ( 'col1 String, col2 Date, col3 Int32') FORMAT CSV";
```

- 만약`data.csv` 같은 구조의 데이터를 포함하는`test_structure` 테이블로`test` 그리고이 두 쿼리가 동일 :

<! - ->

```bash
$ cat data.csv | clickhouse-client --query = "INSERT INTO test FORMAT CSV"
$ cat data.csv | clickhouse-client --query = "INSERT INTO test SELECT * FROM input ( 'test_structure') FORMAT CSV"
```

원본 기사 (https://clickhouse.tech/docs/en/query_language/table_functions/input/) <! - hide ->
