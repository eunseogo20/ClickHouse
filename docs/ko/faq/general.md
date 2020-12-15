---
machine_translated : true
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd
toc_priority : 78
toc_title : "\ u4E00 \ u822C \ u7684 \ u306A \ u8CEA \ u554F"
---

# 일반적인 질문 {# general-questions}

## 왜 MapReduce와 같은 것을 사용합니까? {# why-not-use-something-like-mapreduce}

MapReduce와 같은 시스템은 reduce 작업을 분산 정렬을 기반으로하는 분산 컴퓨팅 시스템이라고 부를 수 있습니다. 이 클래스 [Apache Hadoop (http://hadoop.apache.org) Yandex는 사내 솔루션 YT를 사용하고 있습니다.

이러한 시스템의 적절한 온라인 쿼리에 의한 높은 기대를 제로로하기로 즉, web 인터페이스의 백엔드로 사용 할 수 없습니다. 이러한 시스템의 도움 실시간 데이터 업데이트를했다. 분산 정렬 작업의 결과와 모든 중간 결과 (있는 경우)가 단일 서버의 RAM에있는 경우 reduce 작업을 수행하는 가장 좋은 방법은 없습니다. 이러한 경우 해시 테이블은 reduce 작업을 수행하는 가장 좋은 방법입니다. Map-reduce 작업을 최적화하기위한 일반적인 방법은 RAM의 해시 테이블을 사용하여 사전 집계 (부분 감소)입니다. 사용자는이 최적화를 수동으로 실행합니다. 분산 정렬은 단순한 map-reduce 작업을 수행 할 때 성능 저하의 주요 원인 중 하나입니다.

대부분의 MapReduce 구현에서는 클러스터에서 임의의 코드를 실행할 수 있습니다. 그러나 선언적 쿼리 언어는 실험을 신속하게 수행하기 위해 OLAP에 적합합니다. 예를 들어, Hadoop에는 Hive와 Pig 있습니다. 또한 Spark의 Cloudera Impala와 Shark (고대), Spark SQL, Presto, Apache Drill에 대해서도 검토하십시오. 이러한 작업을 수행 할 때 성능은 특수한 시스템에 비해 매우 적합하지 않지만, 비교적 지연이 높기 때문에 이러한 시스템

##되어 있을까요? 하고 있기 때문에 인코딩 이용의 경우 Oracle을 ODBC? {# oracle-odbc-encodings}

외부 사전의 소스로 ODBC 드라이버를 통해 Oracle을 사용하는 경우, 외부 사전의 올바른 값을 설정해야`NLS_LANG` 환경 변수 in`/ etc / default / clickhouse`. 자세한 내용은을 참조 제발 [Oracle NLS_LANG FAQ (https://www.oracle.com/technetwork/products/globalization/nls-lang-099431.html).

** 예 **

```sql
NLS_LANG = RUSSIAN_RUSSIA.UTF8
```

## ClickHouse에서 파일로 데이터를 내보내려면? {# how-to-export-to-file}

### INTO OUTFILE 절 사용 {# using-into-outfile-clause}

추가 [INTO OUTFILE (../ sql-reference / statements / select / into-outfile.md # into-outfile-clause) 쿼리 절.

예를 들면 :

```sql
SELECT * FROM table INTO OUTFILE 'file'
```

기본적으로 ClickHouse은 [TabSeparated] (../ interfaces / formats.md # tabseparated) 출력 데이터의 형식. 선택하려면 [데이터 형식] (../ interfaces / formats.md)는 [FORMAT 절] (../ sql-reference / statements / select / format.md # format-clause).

예를 들면 :

```sql
SELECT * FROM table INTO OUTFILE 'file'FORMAT CSV
```

### 파일 엔진 테이블의 사용 {# using a-file-engine-table}

더보기 [파일] (../ engines / table-engines / special / file.md).

### 명령 줄 리디렉션 {# using-command-line-redirection}

```sql
$ clickhouse-client --query "SELECT * from table"--format FormatName> result.txt
```

더보기 [clickhouse- 클라이언트 (../ interfaces / cli.md).

{## 원래 기사 (https://clickhouse.tech/docs/en/faq/general/) ##}
