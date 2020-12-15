--- 
toc_priority : 15 
toc_title : OnTime 
--- 

# OnTime {#ontime} 

이 데이터 세트는 두 가지 방법으로 얻을 수 있습니다 : 

- 원시 데이터에서 가져 오기 
- 파티션 된 다운로드 

## 원시 데이터에서 가져온 {# import- from-raw-data} 

데이터 다운로드 : 

```bash 
for s in`seq 1987 2018` 
do 
for m in`seq 1 12` 
do 
wget https://transtats.bts.gov/PREZIP/On_Time_Reporting_Carrier_On_Time_Performance_1987_present_${s} _ $ {m} .zip 
done 
done 
``` 

(https://github.com/Percona-Lab/ontime-airline-performance/blob/master/download.sh보다) 

테이블 만들기 : 

```sql 
CREATE TABLE `ontime` ( 
  `Year` UInt16, 
  `Quarter` UInt8,
  `Month` UInt8, 
  `DayofMonth` UInt8, 
  `DayOfWeek` UInt8, 
  `FlightDate` Date, 
  `UniqueCarrier` FixedString (7), 
  `AirlineID` Int32, 
  `Carrier` FixedString (2), 
  `TailNum` String, 
  `FlightNum` String , 
  `OriginAirportID` Int32, 
  `OriginAirportSeqID` Int32, 
  `OriginCityMarketID` Int32, 
  `Origin` FixedString (5), 
  `OriginCityName` String, 
  `OriginState` FixedString (2), 
  `OriginStateFips` String, 
  `OriginStateName` String, 
  `OriginWac` Int32, 
  `DestAirportID` Int32, 
  `DestAirportSeqID` Int32, 
  `DestCityMarketID` Int32, 
  `Dest` FixedString (5) 
  `DestCityName` String, 
  `DestState` FixedString (2), 
  `DestStateFips` String, 
  `DestStateName` String, 
  `DestWac` Int32, 
  `CRSDepTime` Int32, 
  `DepTime` Int32, 
  `DepDelay` Int32, 
  `DepDelayMinutes` Int32, 
  `DepDel15 `Int32, 
  `DepartureDelayGroups` String, 
  `DepTimeBlk` String, 
  `TaxiOut` Int32, 
  `WheelsOff` Int32, 
  `WheelsOn` Int32, 
  `TaxiIn` Int32, 
  `CRSArrTime` Int32, 
  `ArrTime` Int32, 
  `ArrDelay` Int32, 
  `ArrDelayMinutes `Int32, 
  `ArrDel15` Int32, 
  `ArrivalDelayGroups` Int32, 
  `ArrTimeBlk` String,
  `Cancelled` UInt8,  
  `CancellationCode` FixedString (1)
  `Diverted` UInt8, 
  `CRSElapsedTime` Int32, 
  `ActualElapsedTime` Int32, 
  `AirTime` Int32, 
  `Flights` Int32, 
  `Distance` Int32, 
  `DistanceGroup` UInt8, 
  `CarrierDelay` Int32, 
  `WeatherDelay` Int32, 
  `NASDelay` Int32, 
  `SecurityDelay` Int32, 
  `LateAircraftDelay` Int32, 
  `FirstDepTime` String, 
  `TotalAddGTime` String, 
  `LongestAddGTime` String, 
  `DivAirportLandings` String, 
  `DivReachedDest` String, 
  `DivActualElapsedTime` String, 
  `DivArrDelay` String, 
  `DivDistance` String, 
  `Div1Airport` String,
  `Div1AirportID` Int32,  
  `Div1AirportSeqID` Int32,
  `Div1WheelsOn` String, 
  `Div1TotalGTime` String, 
  `Div1LongestGTime` String, 
  `Div1WheelsOff` String, 
  `Div1TailNum` String, 
  `Div2Airport` String, 
  `Div2AirportID` Int32, 
  `Div2AirportSeqID` Int32, 
  `Div2WheelsOn` String, 
  `Div2TotalGTime` String, 
  `Div2LongestGTime` String, 
  `Div2WheelsOff` String, 
  `Div2TailNum` String, 
  `Div3Airport` String, 
  `Div3AirportID` Int32, 
  `Div3AirportSeqID` Int32, 
  `Div3WheelsOn` String, 
  `Div3TotalGTime` String, 
  `Div3LongestGTime` String, 
  `Div3WheelsOff` String, 
  `Div3TailNum` String, 
  `Div4Airport` String, 
  `Div4AirportID` Int32,
  `Div4AirportSeqID` Int32, 
  `Div4WheelsOn` String, 
  `Div4TotalGTime` String, 
  `Div4LongestGTime` String, 
  `Div4WheelsOff` String, 
  `Div4TailNum` String, 
  `Div5Airport` String, 
  `Div5AirportID` Int32, 
  `Div5AirportSeqID` Int32, 
  `Div5WheelsOn` String, 
  `Div5TotalGTime` String, 
  `Div5LongestGTime` String, 
  `Div5WheelsOff` String, 
  `Div5TailNum` String 
) ENGINE = MergeTree 
PARTITION BY Year 
ORDER BY (Carrier, FlightDate) 
SETTINGS index_granularity = 8192; 
``` 

데이터로드 :
 
```bashontime` 같은 완전한 테이블 이름을 사용해야합니다. 
## 쿼리 {#queries} 
Q0.






```sql 
SELECT avg (c1) 
FROM 
( 
    SELECT Year, Month, count (*) AS c1 
    FROM ontime 
    GROUP BY Year, Month 
); 
``` 

Q1. 2000 년부터 2008 년까지 하루에 비행 수 

` ``sql 
SELECT DayOfWeek, count (*) AS c 
FROM ontime 
WHERE Year> = 2000 AND Year <= 2008 
GROUP BY DayOfWeek 
ORDER BY c DESC; 
``` 

Q2. 2000 년부터 2008 년까지 10 분 이상 지연 운항 수를 요일별로 그룹화 

```sql 
SELECT DayOfWeek, count (*) AS c 
FROM ontime 
WHERE DepDelay> 10 AND Year> = 2000 AND Year <= 2008 
GROUP BY DayOfWeek 
ORDER BY c DESC; 
``` 

Q3. 2000 년부터 2008 년까지의 공항 별 지연 건수 

```sql
SELECT Origin, count (*) AS c 
FROM ontime 
WHERE DepDelay> 10 AND Year> = 2000 AND Year <= 2008 
GROUP BY Origin 
ORDER BY c DESC 
LIMIT 10; 
``` 

Q4 2007 년의 경력 별 지연의 수 

` ``sql 
SELECT Carrier, count (*) 
FROM ontime 
WHERE DepDelay> 10 AND Year = 2007 
GROUP BY Carrier 
ORDER BY count (*) DESC; 
``` 

Q5. 2007 년의 경력 별 지연의 비율 

```sql 
SELECT Carrier , c, c2, c * 100 / c2 as c3 
FROM 
( 
    SELECT 
        Carrier, 
        count (*) aS c 
    FROM ontime 
    WHERE DepDelay> 10 
        AND Year = 2007 
    GROUP BY Carrier 
)
JOIN 
    WHERE DepDelay> 10
( 
    SELECT 
        Carrier, 
        count (*) AS c2 
    FROM ontime 
    WHERE Year = 2007 
    GROUP BY Carrier 
) USING Carrier 
ORDER BY c3 DESC; 
``` 

같은 쿼리의 더 나은 버전 : 

```sql 
SELECT Carrier avg (DepDelay> 10) * 100 AS c3 
FROM ontime 
WHERE Year = 2007 
GROUP BY Carrier 
ORDER BY c3 DESC 
``` 

Q6. 이전 요청을 2000 년부터 2008 년까지 펼친 것 

```sql 
SELECT Carrier, c, c2, c * 100 / c2 as c3 
FROM 
( 
    SELECT 
        Carrier, 
        count (*) aS c 
    FROM ontime 
        AND Year> = 2000 AND Year <= 2008
    GROUP BY Carrier 
) 
JOIN 
( 
    SELECT 
        Carrier, 
        count (*) AS c2 
    FROM ontime 
    WHERE Year> = 2000 AND Year <= 2008 
    GROUP BY Carrier 
) USING Carrier 
ORDER BY c3 DESC; 
``` 

같은 쿼리의 더 나은 버전 : 

`` `sql 
SELECT Carrier avg (DepDelay> 10) * 100 AS c3 
FROM ontime 
WHERE Year> = 2000 AND Year <= 2008 
GROUP BY Carrier 
ORDER BY c3 DESC; 
``` 

Q7. 연도 별, 10 분 이상 지연 운항 비율 

```sql 
sELECT Year, c1 / c2 
fROM 
( 
    select  
        Year,
        count (*) * 100 as c1 
    from ontime
    WHERE DepDelay> 10 
    GROUP BY Year 
) 
JOIN 
( 
    select 
        Year, 
        count (*) as c2 
    from ontime 
    GROUP BY Year 
) USING (Year) 
ORDER BY Year; 
``` 

같은 쿼리의 더 나은 버전 : 

```sql 
SELECT Year, avg (DepDelay> 10) * 100 
FROM ontime 
GROUP BY Year 
ORDER BY Year; 
``` 

Q8. 다년간의 직행 도시 수별 인기있는 목적지 

```sql 
SELECT DestCityName, uniqExact (OriginCityName) AS u 
FROM ontime 
WHERE Year> = 2000 and Year <= 2010 
GROUP BY DestCityName 
ORDER BY u DESC LIMIT 10; 
``` 

Q9. 

```sql
SELECT Year, count (*) AS c1 
FROM ontime 
GROUP BY Year; 
``` 

Q10. 

```sql 
SELECT 
   min (Year), max (Year), Carrier, count (*) AS cnt, 
   sum (ArrDelayMinutes> 30) AS flights_delayed, 
   round (sum (ArrDelayMinutes> 30) / count (*) 2) AS rate 
FROM ontime 
WHERE 
   DayOfWeek NOT IN (6,7) AND OriginState NOT IN ( 'AK', 'HI', 'PR', ' VI ') 
   aND DestState NOT IN ('AK ','HI ','PR ','VI ') 
   aND FlightDate <'2010-01-01 ' 
GROUP by Carrier 
HAVING cnt> 100000 and max (Year)> 1990 
ORDER by rate DESC 
LIMIT 1000; 
``` 

보너스 :

```sql 
SELECT avg (cnt) 
FROM
( 
GROUP BY OriginCityName
    SELECT Year, Month, count (*) AS cnt 
    FROM ontime 
    WHERE DepDel15 = 1 
    GROUP BY Year, Month 
); 

SELECT avg (c1) FROM 
( 
    SELECT Year, Month, count (*) AS c1 
    FROM ontime 
    GROUP BY Year, Month 
) ; 

SELECT DestCityName, uniqExact (OriginCityName) AS u 
FROM ontime 
GROUP BY DestCityName 
ORDER BY u DESC 
LIMIT 10; 

SELECT OriginCityName, DestCityName, count () AS c 
FROM ontime 
GROUP BY OriginCityName, DestCityName 
ORDER BY c DESC 
LIMIT 10; 

SELECT OriginCityName, count () AS c 
FROM ontime 
ORDER BY c DESC 
LIMIT 10; 
```

이 성능 테스트는 Vadim Tkachenko에 의해 작성되었습니다. 다음을 참조하십시오. 

- https://www.percona.com/blog/2009/10/02/analyzing-air-traffic-performance-with-infobright-and-monetdb/ 
- https://www.percona.com/blog/2009/ 10 / 26 / air-traffic-queries-in-luciddb / 
- https://www.percona.com/blog/2009/11/02/air-traffic-queries-in-infinidb-early-alpha/ 
- https : //www.percona.com/blog/2014/04/21/using-apache-hadoop-and-impala-together-with-mysql-for-data-analysis/ 
- https://www.percona.com/blog / 2016 / 01 / 07 / apache-spark-with-air-ontime-performance-data / 
- http://nickmakos.blogspot.ru/2012/08/analyzing-air-traffic-performance-with.html 

원본 기사 (https://clickhouse.tech/docs/en/getting_started/example_datasets/ontime/) <! - hide ->
