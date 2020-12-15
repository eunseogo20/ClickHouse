--- 
machine_translated : true 
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd 
toc_priority : 34 
toc_title : "\ u5916 \ u90E8 \ u30C7 \ u30FC \ u30BF" 
--- 

# 쿼리 처리의 외부 데이터 {# external-data-for-query-processing} 

ClickHouse는 쿼리의 처리에 필요한 데이터를 SELECT 쿼리와 함께 서버에 보낼 수 있습니다. 이 데이터는 임시 테이블에 저장됩니다 (섹션을 참조 "Temporary tables")와 쿼리에서 사용할 수 있습니다 (예를 들어, in 연산자). 

예를 들어, 중요한 사용자 식별자를 가지는 텍스트 파일이있는 경우이 목록 필터링을 사용하는 쿼리와 함께 서버에 업로드에서 

대량의 외부 데이터를 포함한 여러 쿼리를 실행해야하는 경우이 기능을 사용 하지 마십시오. 사전 DB에 데이터를 업로드하는 것이 좋습니다. 

외부 데이터는 명령 줄 클라이언트 (비 대화식 모드) 또는 HTTP 인터페이스를 사용하여 업로드 할 수 있습니다. 

명령 줄 클라이언트에서는 parameters 섹션을 다음 형식으로 지정할 수 있습니다 

```bash  
--external --file = ... [--name = ...] [--format = ...] [- types = ... | --structure = ...]
``` 

하고 여러 부분처럼,이 테이블이 전송됩니다.

** - external ** - Marks the beginning of a clause. 
** - file ** - Path to the file with the table dump, or - which refers to stdin. 
Stdin에서 얻을 수있는 것은 단일 테이블 만 . 

다음 매개 변수는 선택 사항입니다 : ** - name ** - Name of the table. If omitted, _data is used. 
** - format ** - Data format in the file. If omitted, TabSeparated is used. 

다음 중 지의 매개 변수가 필요합니다 : ** - types ** - A list of comma-separated column types. For example :`UInt64, String`. The columns will be named _1 _2 ... 
** - structure ** - The table structure in the format`UserID UInt64``URL String`. 열 이름과 형식을 정의합니다. 

에 지정된 파일 'file'에 지정된 형식으로 해석됩니다. 'format'에 지정된 데이터 형식을 사용합니다 'types'또는 'structure'. 테이블이 업로드 서버에 대한 액세스를 임시 테이블의 이름 'name'. 

예 :
 
```bash
$ echo -ne "1 \ n2 \ n3 \ n"| clickhouse-client --query = "SELECT count () FROM test.visits WHERE TraficSourceID IN _data"--external --file = - --types = Int8 
849897 
$ cat / etc / passwd | sed 's / : / \ t / g'| clickhouse-client --query = "SELECT shell, count () AS c FROM passwd GROUP BY shell ORDER BY c DESC "--external --file = - --name = passwd --structure = 'login String, unused String, uid UInt16, gid UInt16, comment String, home String, shell String' 
/ bin / sh 20 
/ bin / false 5 
/ bin / bash 4 
/ usr / sbin / nologin 1 
/ bin / sync 1 
```

HTTP 인터페이스를 사용하는 경우 외부 데이터는 multipart / form-data 형식으로 전달됩니다. 각 테이블은 별도의 파일로 전송됩니다. 테이블 이름은 파일 이름에서 가져옵니다. 그 'query_string'매개 변수가 전달됩니다 'name_format', 'name_types', and 'name_structure'여기에서 'name'이 매개 변수가 해당 테이블의 이름을 지정합니다. 매개 변수의 의미는 명령 줄 클라이언트를 사용하는 경우와 동일합니다. 

예 : 

```bash 
$ cat / etc / passwd | sed 's / : / \ t / g'> passwd.tsv 

$ curl -F 'passwd=@passwd.tsv;' 'http : // localhost : 8123 / ? query = SELECT + shell + count () + AS + c + FROM + passwd + GROUP + BY + shell + ORDER + BY + c + DESC & passwd_structure = login + String + unused + String + uid + UInt16 + gid + UInt16 + comment + String + home + String + shell + String '


원본 기사 (https://clickhouse.tech/docs/en/operations/table_engines/external_data/) <! - hide ->
