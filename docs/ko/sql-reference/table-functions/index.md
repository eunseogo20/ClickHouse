--- 
machine_translated : true 
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd 
toc_folder_title : "\ u30C6 \ u30FC \ u30D6 \ u30EB \ u95A2 \ u6570" 
toc_priority : 34 
toc_title : "\ u306F \ u3058 \ u3081 \ u306B" 
--- 

# 테이블 함수 {# table- functions} 

테이블 함수는 테이블을 구축하는 방법입니다. 

다음 테이블 함수를 사용할 수 있습니다 : 

- [FROM] (../ statements / select / from.md) 절`SELECT` 쿼리. 

        The method for creating a temporary table that is available only in the current query . The table is deleted when the query finishes. 

- 테이블을 \ <table_function () \>로 만들기 (../ statements / create.md # create -table-query) 쿼리. 

        It 's one of the methods of creating a table . 

!!! warning "경고"
    테이블 함수를 사용할 수 없습니다. [allow_ddl (../../ operations / settings / permissions-for-queries.md # settings_allow_ddl) 설정은 무효입니다. 

| 함수 | 설명 | 
| ----------------------- | -------------------- -------------------------------------------------- -------------------------------------------------- ---------------- | 
| [파일] (file.md) |을 만듭니다. [파일] (../../ engines / table-engines / special / file.md) - 엔진 테이블. | 
| 병합 (merge.md) |을 만듭니다. 병합 (../../ engines / table-engines / special / merge.md) - 엔진 테이블. |
| [숫자] (numbers.md) | 정수로 채워진 단일 열이있는 테이블을 만듭니다. | 
| [원격] (remote.md) |에 자유롭게 접근 할 수있는 원격 서버를 만들지 않고 분산 (../../ engines / table-engines / special / distributed.md) - 엔진 테이블. | 
| [url] (url.md) |을 만듭니다. [Url] (../../ engines / table-engines / special / url.md) - 엔진 테이블. | 
| [mysql] (mysql.md) |을 만듭니다. [MySQL] (../../ engines / table-engines / integrations / mysql.md) - 엔진 테이블. | 
| [jdbc] (jdbc.md) |을 만듭니다. [JDBC] (../../ engines / table-engines / integrations / jdbc.md) - 엔진 테이블. |
| [odbc] (odbc.md) |을 만듭니다. [ODBC] (../../ engines / table-engines / integrations / odbc.md) - 엔진 테이블. |  
| [hdfs (hdfs.md) |을 만듭니다. [HDFS (../../ engines / table-engines / integrations / hdfs.md) - 엔진 테이블. |

원래 기사 (https://clickhouse.tech/docs/en/query_language/table_functions/) <! - hide ->
