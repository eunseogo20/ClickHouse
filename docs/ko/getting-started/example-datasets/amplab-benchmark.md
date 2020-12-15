--- 
toc_priority : 17 
toc_title : "AMPLab Big Data 벤치 마크" 
--- 

# AMPLab Big Data 벤치 마크 {# amplab-big-data-benchmark} 

https://amplab.cs.berkeley.edu/benchmark/를 참조 바랍니다. 

https://aws.amazon.com에서 무료 계정에 가입하십시오. 신용 카드, 이메일, 전화 번호가 필요합니다. 
https://console.aws.amazon.com/iam/home?nc2=h_m_sc#security_credential에서 새로운 키를 가져옵니다. 


콘솔에서 다음을 수행합니다 : 

```bash 
$ sudo apt-get install s3cmd 
$ mkdir tiny; cd tiny; 
$ s3cmd sync s3 : // big-data-benchmark / pavlo / text-deflate / tiny /. 
$ cd .. 
$ mkdir 1node; cd 1node; 
$ s3cmd sync s3 : // big-data-benchmark / pavlo / text-deflate / 1node /. 
$ cd .. 
$ mkdir 5nodes; cd 5nodes;
$ s3cmd sync s3 : // big-data-benchmark / pavlo / text-deflate / 5nodes /. 
$ cd .. 
``` 

다음 ClickHouse 쿼리를 실행합니다 : 

```sql 
CREATE TABLE rankings_tiny 
( 
    pageURL String, 
    pageRank UInt32, 
    avgDuration UInt32 
) ENGINE = Log; 

CREATE TABLE uservisits_tiny 
( 
    sourceIP String, 
    destinationURL String, 
    visitDate Date, 
    adRevenue Float32, 
    UserAgent String, 
    cCode FixedString (3) 
    lCode FixedString (6) 
    searchWord String, 
    duration UInt32 
) ENGINE = MergeTree ( visitDate, visitDate 8192); 
    pageURL String,
 
CREATE TABLE rankings_1node
( 
    pageRank UInt32, 
    avgDuration UInt32 
) ENGINE = Log; 

CREATE TABLE uservisits_1node 
( 
    sourceIP String, 
    destinationURL String, 
    visitDate Date, 
    adRevenue Float32, 
    UserAgent String, 
    cCode FixedString (3) 
    lCode FixedString (6) 
    searchWord String, 
    duration UInt32 
) ENGINE = MergeTree (visitDate, visitDate 8192); 

CREATE TABLE rankings_5nodes_on_single 
( 
    pageURL String, 
    pageRank UInt32, 
    avgDuration UInt32 
) ENGINE = Log; 

CREATE TABLE uservisits_5nodes_on_single 
( 
    sourceIP String, 
    destinationURL String, 
    visitDate Date, 
    adRevenue Float32, 
    UserAgent String, 
    cCode FixedString (3) 
    lCode FixedString (6) 
    searchWord String, 
    duration UInt32 
) ENGINE = MergeTree (visitDate, visitDate 8192); 
``` 

콘솔에 다시 밖으로 다음을 실행합니다 : 

```bash 
$ for i in tiny / rankings / * deflate; do echo $ i; zlib-flate -uncompress <$ i | clickhouse-client --host = example-perftest01j --query = "INSERT INTO rankings_tiny FORMAT CSV"; done 
$ for i in tiny / uservisits / * deflate; do echo $ i; zlib-flate -uncompress <$ i | clickhouse-client --host = example-perftest01j --query = "INSERT INTO uservisits_tiny FORMAT CSV"; done
$ for i in 1node / rankings / * deflate; do echo $ i; zlib-flate -uncompress <$ i | clickhouse-client --host = example-perftest01j --query = "INSERT INTO rankings_1node FORMAT CSV"; done 
$ for i in 1node / uservisits / * deflate; do echo $ i; zlib-flate -uncompress <$ i | clickhouse-client --host = example-perftest01j --query = "INSERT INTO uservisits_1node FORMAT CSV"; done 
$ for i in 5nodes / rankings / * deflate; do echo $ i; zlib-flate -uncompress <$ i | clickhouse-client --host = example-perftest01j --query = "INSERT INTO rankings_5nodes_on_single FORMAT CSV"; done 
$ for i in 5nodes / uservisits / * deflate; do echo $ i; zlib-flate -uncompress <$ i | clickhouse-client --host = example-perftest01j --query = "INSERT INTO uservisits_5nodes_on_single FORMAT CSV";done 
``` 

데이터 검색 쿼리 예제 :

```sql  
SELECT pageURL, pageRank FROM rankings_1node WHERE pageRank> 1000

SELECT substring (sourceIP 1, 8), sum (adRevenue) FROM uservisits_1node GROUP BY substring (sourceIP 1, 8) 

SELECT 
    sourceIP, 
    sum (adRevenue) AS totalRevenue, 
    avg (pageRank) AS pageRank 
FROM rankings_1node ALL INNER JOIN 
( 
    SELECT 
        sourceIP, 
        destinationURL AS pageURL, 
        adRevenue 
    FROM uservisits_1node 
    WHERE (visitDate> '1980-01-01') AND (visitDate < '1980-04-01') 
) USING pageURL 
GROUP BY sourceIP 
ORDER BY totalRevenue DESC 
LIMIT 1 
`` ` 

원래 기사 (https://clickhouse.tech/docs/en/getting_started/example_datasets/amplab_benchmark/) <! - hide ->
