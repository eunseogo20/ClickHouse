--- 
toc_priority : 12 
toc_title : "튜토리얼" 
--- 

# ClickHouse 튜토리얼 {# clickhouse-tutorial} 

##이 튜토리얼에 기대되는 것은? {# what-to-expect-from-this-tutorial} 

이 튜토리얼은 간단한 ClickHouse 클러스터를 설정하는 방법에 대해 설명합니다. 그것은 소규모이지만, 탄력성 및 확장됩니다. 
그런 다음 데이터 세트 예제 중 하나를 사용하여 데이터를 입력하고 몇 가지 데모쿠에리을 실행합니다. 

## 단일 노드의 설정 {# single-node-setup} 

분산 환경은 복잡하기 때문에 우선은 단일 서버 또는 가상 머신에 ClickHouse을 전개하는 것부터 시작합니다. 
ClickHouse는 일반적으로 deb (../ getting-started / install.md # install-from-deb-packages) or [rpm] (../ getting-started / install.md # from-rpm-packages) 패키지 설치하지만 지원되지 않는 OS를 위해 [대안] (install.md # from-docker-image)가 있습니다. 

예를 들어,`deb` 패키지를 선택하여 실행하는 경우 : 

```bash 
{% include 'install / deb.sh'%} 
```

설치된 패키지 내용 : 

-`clickhouse-client` 패키지에는 [clickhouse-client] (../ interfaces / cli.md) 응용 프로그램 대화 형 ClickHouse 콘솔 클라이언트가 포함되어 있습니다. 
-`clickhouse-common` 패키지는 ClickHouse 실행 파일이 포함되어 있습니다. 
-`clickhouse-server` 패키지는 ClickHouse를 서버로 실행하기위한 설정 파일이 포함되어 있습니다. 

서버 설정 파일은`/ etc / clickhouse-server /`에 있습니다. 진행하기 전에`config.xml`의`<path>`요소에 주목하십시오. 
경로는 데이터를 저장할 위치를 결정하기 때문에 디스크 공간의 큰 볼륨에 배치해야합니다. 기본값은`/ var / lib / clickhouse /`입니다. 
설정을 조정하려면`config.xml` 파일을 직접 편집하는 것은 불편합니다. `config.xml` 요소를 재정의하는 데 권장되는 방법은`config.xml`의 "패치"역할을하는 [config.d 디렉토리 내의 파일] (../ operations / configuration-files.md )을 만드는 것입니다. 

기억할지 모르지만,`clickhouse-server` 패키지 설치 후 자동으로 시작되거나 업데이트 후 자동으로 다시 시작되지 않습니다. 서버를 시작하는 방법은 init 시스템에 의존하고 있으며, 대부분은 다음과 같이되어 있습니다 :

```bash 
sudo service clickhouse-server start 
``` 

또는 

```bash 
sudo /etc/init.d/clickhouse-server start 
``` 

서버 로그의 기본 위치는`/ var / log / clickhouse-server / `입니다. 서버가 클라이언트의 연결 준비가되면`Ready for connections` 메시지를 기록합니다. 

일단`clickhouse-server`를 시작하고 실행하면`clickhouse-client`을 사용하여 서버에 연결하고`SELECT "Hello, world!";`같은 테스트 쿼리를 실행할 수 있습니다. 

<details markdown = "1"> 

<summary> Clickhouse- 클라이언트의 빠른 tips </ summary> 

대화 형 모드 : 

```bash 
clickhouse-client 
clickhouse-client --host = ... --port = ... - user = ... --password = ... 
``` 

다중 쿼리를 사용 : 

```bash 
clickhouse-client -m 
clickhouse-client --multiline
``` 
 
배치 모드에서 쿼리 실행 :

```bash 
clickhouse-client --query = 'SELECT 1' 
echo 'SELECT 1'| clickhouse-client 
clickhouse-client <<< 'SELECT 1' 
``` 

지정한 형식의 파일에서 데이터를 insert하기 : 

```bash 
clickhouse-client --query = 'INSERT INTO table VALUES'<data.txt 
clickhouse-client --query = 'INSERT INTO table FORMAT TabSeparated'<data.tsv 
``` 

</ details> 

## 샘플 데이터 세트 가져 오기 {# import-sample-dataset} 

ClickHouse 서버에 일부 샘플 데이터를 넣어 봅시다. 
이 튜토리얼에서는 ClickHouse가 오픈 소스가되기 이전 (자세한 내용은 [역사] (../ introduction / history.md) 참조)에 처음 실전 용도로 사용 된 서비스 인 Yandex.Metrica 익명화 된 데이터를 사용합니다.

[Yandex.Metrica 데이터 세트를 가져 오려면 몇 가지 방법] (../ getting-started / example-datasets / metrica.md)가 있지만 튜토리얼로 가장 자주 사용하는 방법을 사용합니다. 

### 테이블 데이터의 다운로드 및 배포 {# download-and-extract-table-data} 

```bash 
curl https://datasets.clickhouse.tech/hits/tsv/hits_v1.tsv.xz | unxz --threads =`nproc`> hits_v1.tsv 
curl https://datasets.clickhouse.tech/visits/tsv/visits_v1.tsv.xz | unxz --threads =`nproc`> visits_v1.tsv 
``` 

확장 된 파일의 크기 약 10GB입니다. 

### 테이블 만들기 {# create-tables} 

대부분의 데이터베이스 관리 시스템과 같이 ClickHouse는 논리적으로 테이블을 "데이터베이스"에 그룹화합니다. 
`default` 데이터베이스가 있지만,`tutorial`라는 새것을 창조합니다 : 

```bash 
clickhouse-client --query "CREATE DATABASE IF NOT EXISTS tutorial" 
```
 
테이블을 작성하기위한 구문은 데이터베이스에 비해 훨씬 더 복잡합니다 (참조] (../ sql -reference / statements / create.md)). 일반적으로`CREATE TABLE` 문장은 3 개의 키를 지정해야합니다. 

1. 작성하는 테이블의 이름입니다. 
2. 테이블의 스키마. 즉, 열 및 데이터 형식 (../ sql-reference / data-types / index.md)의 목록입니다.
3. 테이블 엔진 (../ engines / table-engines / index.md)와,이 테이블에 대한 쿼리가 실제로 실행되는 방법에 대한 모든 세부 사항을 결정하는 설정. 

Yandex.Metrica는 웹 분석 서비스이며, 샘플 데이터 세트에서 기능이 망라되어 있지 않기 때문에, 만들 테이블은 2 개 밖에 없습니다 : 

-`hits`는 서비스의 대상이되는 모든 웹 사이트에서 모든 사용자 이 한 각 작업 테이블입니다. 
-`visits` 개별 행동보다는 미리 구축 된 세션을 포함하는 테이블입니다. 

이러한 테이블의 실제 CREATE TABLE 쿼리를 실행하자 : 

```sql 
CREATE TABLE tutorial.hits_v1 
( 
    `WatchID` UInt64, 
    `JavaEnable` UInt8, 
    `Title` String, 
    `RegionID` UInt32, 
    `UserID` UInt64, 
    `GoodEvent` Int16,
    `EventTime` DateTime, 
    `EventDate` Date, 
    `CounterID` UInt32, 
    `ClientIP` UInt32, 
    `ClientIP6` FixedString (16), 
    `CounterClass` Int8, 
    `OS` UInt8, 
    `UserAgent` UInt8, 
    `URL` String, 
    `Referer `String, 
    `URLDomain` String, 
    `RefererDomain` String, 
    `Refresh` UInt8, 
    `IsRobot` UInt8, 
    `RefererCategories` Array (UInt16), 
    `URLCategories` Array (UInt16), 
    `URLRegions` Array (UInt32), 
    `RefererRegions` Array (UInt32), 
    `ResolutionWidth` UInt16, 
    `ResolutionHeight` UInt16, 
    `ResolutionDepth` UInt8, 
    `FlashMajor` UInt8, 
    `FlashMinor` UInt8, 
    `FlashMinor2` String, 
    `NetMajor` UInt8, 
    `NetMinor` UInt8, 
    `UserAgentMajor` UInt16,
    `UserAgentMinor` FixedString (2), 
    `CookieEnable` UInt8, 
    `JavascriptEnable` UInt8, 
    `IsMobile` UInt8, 
    `MobilePhone` UInt8, 
    `MobilePhoneModel` String, 
    `Params` String, 
    `IPNetworkID` UInt32, 
    `TraficSourceID` Int8, 
    `SearchEngineID `UInt16, 
    `SearchPhrase` String, 
    `AdvEngineID` UInt8, 
    `IsArtifical` UInt8, 
    `WindowClientWidth` UInt16, 
    `WindowClientHeight` UInt16, 
    `ClientTimeZone` Int16, 
    `SilverlightVersion3` UInt32, 
    `SilverlightVersion4 `UInt16, 
    `ClientEventTime` DateTime,
    `SilverlightVersion1` UInt8, 
    `SilverlightVersion2` UInt8, 
    `PageCharset` String, 
    `CodeVersion` UInt32, 
    `IsLink` UInt8, 
    `IsDownload` UInt8, 
    `IsNotBounce` UInt8, 
    `FUniqID` UInt64, 
    `HID` UInt32, 
    `IsOldCounter` UInt8, 
    `IsEvent` UInt8, 
    `IsParameter` UInt8, 
    `DontCountHits` UInt8, 
    `WithHash` UInt8, 
    `HitColor` FixedString (1), 
    `UTCEventTime` DateTime, 
    `Age` UInt8, 
    `Sex` UInt8, 
    `Income` UInt8, 
    `Interests `UInt16, 
    `Robotness` UInt8, 
    `GeneralInterests` Array (UInt16) 
    `RemoteIP `UInt32, 
    `RemoteIP6` FixedString (16), 
    `WindowName` Int32, 
    `OpenerName` Int32,
    `HistoryLength` Int16, 
    `BrowserLanguage` FixedString (2), 
    `BrowserCountry` FixedString (2), 
    `SocialNetwork` String, 
    `SocialAction` String, 
    `HTTPError` UInt16, 
    `SendTiming` Int32, 
    `DNSTiming` Int32, 
    `ConnectTiming` Int32 , 
    `ResponseStartTiming` Int32, 
    `ResponseEndTiming` Int32, 
    `FetchTiming` Int32, 
    `RedirectTiming` Int32, 
    `DOMInteractiveTiming` Int32, 
    `DOMContentLoadedTiming` Int32, 
    `DOMCompleteTiming` Int32, 
    `LoadEventStartTiming` Int32,  
    `LoadEventEndTiming` Int32,
    `NSToDOMContentLoadedTiming` Int32, 
    `FirstPaintTiming` Int32 ,
    `RedirectCount` Int8, 
    `SocialSourceNetworkID` UInt8, 
    `SocialSourcePage` String, 
    `ParamPrice` Int64, 
    `ParamOrderID` String, 
    `ParamCurrency` FixedString (3), 
    `ParamCurrencyID` UInt16, 
    `GoalsReached` Array (UInt32), 
    `OpenstatServiceName` String , 
    `OpenstatCampaignID` String, 
    `OpenstatAdID` String, 
    `OpenstatSourceID` String, 
    `UTMSource` String, 
    `UTMMedium` String, 
    `UTMCampaign` String, 
    `UTMContent` String, 
    `UTMTerm` String, 
    `FromTag` String, 
    `URLHash` UInt64, 
    `CLID` UInt32, 
    `HasGCLID` UInt8,
    `RefererHash` UInt64, 
    `YCLID` UInt64, 
    `ShareService` String, 
    `ShareURL` String, 
    `ShareTitle` String, 
    `ParsedParams` Nested ( 
        Key1 String, 
        Key2 String, 
        Key3 String, 
        Key4 String, 
        Key5 String, 
        ValueDouble Float64), 
    ` IslandID` FixedString (16), 
    `RequestNum` UInt32, 
    `RequestTry` UInt8 
) 
ENGINE = MergeTree () 
PARTITION BY toYYYYMM (EventDate) 
ORDER BY (CounterID, EventDate, intHash32 (UserID)) 
SAMPLE BY intHash32 (UserID) 
SETTINGS index_granularity = 8192 
``` 

```sql
CREATE TABLE tutorial.visits_v1 
    `SearchPhrase` String, 
(
    `CounterID` UInt32, 
    `StartDate` Date, 
    `Sign` Int8, 
    `IsNew` UInt8, 
    `VisitID` UInt64, 
    `UserID` UInt64, 
    `StartTime` DateTime, 
    `Duration` UInt32, 
    `UTCStartTime` DateTime, 
    `PageViews` Int32, 
    `Hits` Int32, 
    `IsBounce` UInt8, 
    `Referer` String, 
    `StartURL` String, 
    `RefererDomain` String, 
    `StartURLDomain` String, 
    `EndURL` String, 
    `LinkURL` String, 
    `IsDownload` UInt8, 
    `TraficSourceID` Int8, 
    `SearchEngineID` UInt16, 
    `AdvEngineID` UInt8, 
    `PlaceID` Int32,
    `RefererCategories` Array (UInt16), 
    `URLCategories` Array (UInt16), 
    `URLRegions` Array (UInt32), 
    `RefererRegions` Array (UInt32), 
    `IsYandex` UInt8, 
    `GoalReachesDepth` Int32, 
    `GoalReachesURL` Int32, 
    `GoalReachesAny` Int32, 
    `SocialSourceNetworkID` UInt8, 
    `SocialSourcePage` String, 
    `MobilePhoneModel` String, 
    `ClientEventTime` DateTime, 
    `RegionID` UInt32, 
    `ClientIP` UInt32, 
    `ClientIP6` FixedString (16), 
    `RemoteIP` UInt32, 
    `RemoteIP6` FixedString ( 16), 
    `IPNetworkID` UInt32,  
    `SilverlightVersion3` UInt32,
    `CodeVersion` UInt32, 
    `ResolutionWidth` UInt16, 
    `ResolutionHeight` UInt16, 
    `UserAgentMajor` UInt16, 
    `UserAgentMinor` UInt16, 
    `WindowClientWidth` UInt16, 
    `WindowClientHeight` UInt16, 
    `SilverlightVersion2` UInt8, 
    `SilverlightVersion4` UInt16, 
    `FlashVersion3` UInt16, 
    `FlashVersion4` UInt16, 
    `ClientTimeZone` Int16, 
    `OS` UInt8, 
    `UserAgent` UInt8, 
    `ResolutionDepth` UInt8, 
    `FlashMajor` UInt8, 
    `FlashMinor` UInt8, 
    `NetMajor` UInt8, 
    `NetMinor` UInt8, 
    `MobilePhone` UInt8, 
    `SilverlightVersion1` UInt8, 
    `Age` UInt8,
    `Sex` UInt8,  
    `Income` UInt8,
    `JavaEnable` UInt8, 
    `CookieEnable` UInt8, 
    `JavascriptEnable` UInt8, 
    `IsMobile` UInt8, 
    `BrowserLanguage` UInt16, 
    `BrowserCountry` UInt16, 
    `Interests` UInt16, 
    `Robotness` UInt8, 
    `GeneralInterests` Array ( UInt16), 
    `Params` Array (String), 
    `Goals` Nested ( 
        ID UInt32, 
        Serial UInt32, 
        EventTime DateTime, 
        Price Int64, 
        OrderID String, 
        CurrencyID UInt32), 
    `WatchIDs` Array (UInt64), 
    `ParamSumPrice` Int64, 
    `ParamCurrency `FixedString (3)
    `ParamCurrencyID` UInt16, 
    `ClickMarketCategoryName` String, 
    `ClickLogID `UInt64,
    `ClickEventID` Int32, 
    `ClickGoodEvent` Int32, 
    `ClickEventTime` DateTime, 
    `ClickPriorityID` Int32, 
    `ClickPhraseID` Int32, 
    `ClickPageID` Int32, 
    `ClickPlaceID` Int32, 
    `ClickTypeID` Int32, 
    `ClickResourceID` Int32, 
    `ClickCost` UInt32, 
    `ClickClientIP` UInt32, 
    `ClickDomainID` UInt32, 
    `ClickURL` String, 
    `ClickAttempt` UInt8, 
    `ClickOrderID` UInt32, 
    `ClickBannerID` UInt32, 
    `ClickMarketCategoryID` UInt32, 
    `ClickMarketPP` UInt32, 
    `ClickAWAPSCampaignName` String,
    `ClickMarketPPName` String, 
    `PredLastVisit` Date,
    `ClickPageName` String, 
    `ClickTargetType` UInt16, 
    `ClickTargetPhraseID` UInt64, 
    `ClickContextType` UInt8, 
    `ClickSelectType` Int8, 
    `ClickOptions` String, 
    `ClickGroupBannerID` Int32, 
    `OpenstatServiceName` String, 
    `OpenstatCampaignID` String, 
    `OpenstatAdID` String, 
    `OpenstatSourceID` String, 
    `UTMSource` String, 
    `UTMMedium` String, 
    `UTMCampaign` String, 
    `UTMContent` String, 
    `UTMTerm` String, 
    `FromTag` String, 
    `HasGCLID` UInt8, 
    `FirstVisit` DateTime, 
    `LastVisit` Date, 
    `TotalVisits` UInt32,
    `TraficSource` Nested ( 
        ID Int8, 
        SearchEngineID UInt16, 
        AdvEngineID UInt8, 
        PlaceID UInt16, 
        SocialSourceNetworkID UInt8, 
        Domain String, 
        SearchPhrase String, 
        SocialSourcePage String), 
    `Attendance` FixedString (16), 
    `CLID` UInt32, 
    `YCLID` UInt64, 
    `NormalizedRefererHash `UInt64, 
    `SearchPhraseHash` UInt64, 
    `RefererDomainHash` UInt64, 
    `NormalizedStartURLHash` UInt64, 
    `StartURLDomainHash` UInt64, 
    `NormalizedEndURLHash` UInt64, 
    `OpenstatServiceNameHash` UInt64, 
    `TopLevelDomain` UInt64,
    `URLScheme` UInt64, 
    `OpenstatCampaignIDHash` UInt64, 
    `OpenstatAdIDHash` UInt64, 
    `OpenstatSourceIDHash` UInt64, 
    `UTMSourceHash` UInt64, 
    `UTMMediumHash` UInt64, 
    `UTMCampaignHash` UInt64, 
    `UTMContentHash` UInt64, 
    `UTMTermHash` UInt64, 
    `FromHash` UInt64, 
    `WebVisorEnabled` UInt8, 
    `WebVisorActivity` UInt32, 
    `ParsedParams` Nested ( 
        Key1 String, 
        Key2 String, 
        Key3 String, 
        Key4 String, 
        Key5 String, 
        ValueDouble Float64), 
    `Market` Nested ( 
        Type UInt8,
        GoalID UInt32, 
        OrderID String,
        OrderPrice Int64, 
        PP UInt32, 
        DirectPlaceID UInt32, 
        DirectOrderID UInt32, 
        DirectBannerID UInt32, 
        GoodID String, 
        GoodName String, 
        GoodQuantity Int32, 
        GoodPrice Int64), 
    `IslandID` FixedString (16) 
) 
ENGINE = CollapsingMergeTree (Sign) 
PARTITION BY toYYYYMM (StartDate) 
ORDER BY (CounterID, StartDate, intHash32 (UserID) VisitID) 
SAMPLE BY intHash32 (UserID) 
SETTINGS index_granularity = 8192 
``` 

이 쿼리는`clickhouse-client` 대화 형 모드 (사전에 쿼리를 지정하지 않고 터미널에서 시작하면됩니다)를 사용하여 수행하거나 대체 인터페이스 (../ interfaces / index.md)에서 실행할 수 있습니다.

본대로`hits_v1`은 [기본 MergeTree 엔진 (../ engines / table-engines / mergetree-family / mergetree.md)를 사용하고있어`visits_v1`은 [Collapsing MergeTree (.. /engines/table-engines/mergetree-family/collapsingmergetree.md)라는 변종을 사용하고 있습니다. 

### 데이터 가져 오기 {# import-data} 

ClickHouse에 데이터 가져 오기는 많은 다른 SQL 데이터베이스처럼 [INSERT INTO] (../ sql-reference / statements / insert-into.md) 쿼리 를 통해 이루어집니다. 그러나 데이터는 일반적으로`VALUES` 절 (이것도 지원합니다) 대신, 지원되는 직렬화 형식 (../ interfaces / formats.md) 중 하나에서 제공됩니다. 

방금 다운로드 한 파일은 탭으로 구분되어 있으며, 콘솔 클라이언트에서 다음과 같이 가져옵니다 : 

```bash 
clickhouse-client --query "INSERT INTO tutorial.hits_v1 FORMAT TSV"--max_insert_block_size = 100000 <hits_v1 .tsv 
clickhouse-client --query "INSERT INTO tutorial.visits_v1 FORMAT TSV"--max_insert_block_size = 100000 <visits_v1. 
```

ClickHouse에는 많은 조정을위한 설정 (../ operations / settings / index.md)가 콘솔 클라이언트에서 인수로 지정하는 방법이 있습니다. 어떤 설정이 가능하고, 그것이 무엇을 의미하며, 기본은 무엇인지를 아는 가장 쉬운 방법은`system.settings` 테이블에 쿼리하는 것입니다 : 

```sql 
SELECT name, value, changed, description 
FROM system.settings 
WHERE name LIKE '% max_insert_b %' 
FORMAT TSV 

max_insert_block_size 1048576 0 "the maximum block size for insertion, if we control the creation of blocks for insertion " 
``` 

필요에 따라 가져온 후 테이블을 [OPTIMIZE (../ sql-reference / statements / optimize.md) 할 수 있습니다. 
MergeTree-family 엔진으로 설정되어있는 테이블은 항상 백그라운드에서 데이터 부분의 병합을 수행하고 데이터 스토리지를 최적화합니다 (또는 적어도 그것이 의미있는 것인지를 확인합니다). 

다음 쿼리는 테이블 엔진이 스토리지 최적화를 나중에 할 것이 아니라 지금 할 수 있는지 확인합니다 : 

```bash
clickhouse-client --query "OPTIMIZE TABLE tutorial.hits_v1 FINAL" 
clickhouse-client --query "OPTIMIZE TABLE tutorial.visits_v1 FINAL" 
``` 

이 쿼리는 I / O 및 CPU에 부하가 걸리는 작업을 시작하기 때문에 테이블이 계속해서 새로운 데이터를받을 경우에는 그 상태로 백그라운드에서 병합을 실행시키는 것이 좋습니다. 

이제 테이블 가져 오기가 성공했는지 여부를 확인할 수 있습니다 : 

```bash 
clickhouse-client --query "SELECT COUNT (*) FROM tutorial.hits_v1" 
clickhouse-client --query "SELECT COUNT ( *) FROM tutorial.visits_v1 " 
``` 

## 쿼리의 예 {# example-queries} 

```sql 
SELECT 
    StartURL AS URL, 
    AVG (Duration) AS AvgDuration 
FROM tutorial.visits_v1 
WHERE StartDate BETWEEN '2014-03-23' AND '2014-03-30' 
GROUP BY URL
ORDER BY AvgDuration DESC 
``` 

```sql 
SELECT 
    sum (Sign) AS visits, 
    sumIf (Sign, has (Goals.ID, 1105530)) AS goal_visits, 
    (100. * goal_visits) / visits AS goal_percent 
FROM tutorial.visits_v1 
WHERE (CounterID = 912887) AND (toYYYYMM (StartDate) = 201403) AND (domain (StartURL) = 'yandex.ru') 
``` 

## 클러스터의 배포 {# cluster-deployment} 

ClickHouse 클러스터는 동종 클러스터 (homogenous cluster )입니다. 설치 단계는 다음과 같습니다 : 

1. 클러스터의 모든 기계에 ClickHouse server를 설치하기 
2. 설정 파일에 클러스터 설정을 할 
3. 각 인스턴스에 로컬 테이블을 만드는 
4. 분산 테이블 (../ engines / table-engines / special / distributed.md)을 만들 

분산 테이블 (../ engines / table-engines / special / distributed.md)는
실제로 ClickHouse 클러스터의 로컬 테이블에 "보기"와 같은 것입니다. 
분산 테이블에서 SELECT 쿼리는 모든 클러스터 샤드 리소스를 사용하여 실행됩니다. 
또한 여러 클러스터에 대한 설정을하고, 다른 클러스터에 뷰를 제공하는 분산 된 여러 테이블을 만들 수 있습니다. 

3 개의 샤드 각 1 개의 복제본이있는 클러스터의 설정 예 : 

```xml 
<remote_servers> 
    <perftest_3shards_1replicas> 
        <shard> 
            <replica> 
                <host> example-perftest01j.yandex.ru </ host> 
                <port> 9000 < / port> 
            </ replica> 
        </ shard> 
        <shard> 
            <replica> 
                <host> example-perftest02j.yandex.ru </ host> 
                <port> 9000 </ port>
        <shard> 
            <replica> 
                < host> example-perftest03j.yandex.ru </ host>
                <port> 9000 </ port> 
            </ replica> 
        </ shard>
    </ perftest_3shards_1replicas> 
</ remote_servers> 
``` 

더 논증으로`hits_v1`에서 사용한 것과 같은`CREATE TABLE` 쿼리를 사용하여 새 로컬 테이블을 만들어 보자 : 

```sql 
CREATE TABLE tutorial .hits_local (...) ENGINE = MergeTree () ... 
``` 

클러스터의 로컬 테이블에 뷰를 제공하는 분산 테이블을 만듭니다 : 

```sql 
CREATE TABLE tutorial.hits_all AS tutorial.hits_local 
ENGINE = Distributed (perftest_3shards_1replicas, tutorial, hits_local, rand ()); 
``` 

일반적으로 클러스터의 모든 컴퓨터에 동일한 분산 테이블을 만듭니다. 이렇게하면 클러스터의 어떤 컴퓨터에서도 분산 쿼리를 실행할 수 있습니다. 
또한 [remote] (../ sql-reference / table-functions / remote.md) 테이블 함수를 사용하여 주어진 SELECT 쿼리를위한 임시 분산 테이블을 만들 대체 옵션도 있습니다.

분산 테이블에 [INSERT SELECT] (../ sql-reference / statements / insert-into.md)을 실행하여 테이블을 여러 서버에 분산시켜 봅시다. 

```sql 
INSERT INTO tutorial.hits_all SELECT * FROM tutorial.hits_v1; 
``` 

!!! warning "주의" 
    이 방법은 큰 테이블 샤드에는 적합하지 않습니다. 
    다른 도구 [clickhouse-copier (../ operations / utilities / clickhouse-copier.md)가 어떤 큰 테이블을 다시 샤드 할 수 있습니다. 

예상대로, 계산량이 많은 쿼리는 하나의 서버가 아닌 3 대의 서버를 사용하는 것이 N 배까지 빨라질됩니다. 

이 케이스는 3 개의 샤드를 가진 클러스터를 사용하고, 각각 하나의 복제본이 포함되어 있습니다. 

프로덕션 환경에서 탄력성을 제공하기 위해 각 샤드가 여러 가용성 영역 또는 데이터 센터 (또는 적어도 랙) 사이에 2 ~ 3 개의 복제본을 포함하는 것을 권장합니다. 

3 개의 복제본을 포함하는 하나의 샤드 클러스터 설정 예 : 

```xml 
<remote_servers> 
    ... 
    <perftest_1shards_3replicas> 
        <shard> 
            <replica>org /)가 필요합니다. 
ZooKeeper 클러스터는 다른 서버 (ClickHouse 등 다른 프로세스가 실행되지 않는 장소)에 배치하는 것이 좋습니다.

ClickHouse은 모든 복제에서 데이터의 무결성을 가지고 장애 발생시 자동으로 복원 작업을 수행합니다.

기본 복제를 사용하려면 [사육사 (http://zookeeper.apache.org/) 필수입니다. ClickHouse 데이터의 무결성은 모든 복제 및 복구 절차 후 부동합니다. 다른 서버 (ClickHouse 등 다른 프로세스가 실행되고 있지 않은 장소)에 ZooKeeper 클러스터를 확장하는 것이 좋습니다. 

!!! note "비고" 
    ZooKeeper는 엄격한 요구 사항은 없습니다 : 몇 가지 간단한 경우에는 응용 프로그램 코드에서 모든 복제본에 기록 할 데이터를 복제 할 수 있습니다. 이 경우에있어서,이 방법은 ** 권장되지 않습니다 **. ClickHouse 모든 복제본에서 데이터의 일관성을 보장 할 수 없습니다. 따라서 그것은 당신의 응용 프로그램의 책임입니다. 

ZooKeeper 위치는 설정 파일을 지정합니다 : 

```xml 
<zookeeper> 
    <node> 
        <host> zoo01.yandex.ru </ host> 
        <port> 2181 </ port> 
    </ node> 
    <node> 
        <host > zoo02.yandex.ru </ host> 
        <port> 2181 </ port>
    <node> 
        <port> 2181 </ port> 
    </ node> 
</ zookeeper> 
``` 

또한 테이블을 만들 때 사용하는 각 샤드와 복제를 식별하는 매크로를 설정해야합니다 : 

```xml 
<macros> 
    <shard> 01 </ shard> 
    <replica> 01 </ replica> 
</ macros> 
``` 

복제 된 테이블을 작성할 때 복제가 없으면 새로운 첫 복제가 인스턴스화됩니다. 이미 유효한 복제본이있는 경우 새 복제본이 기존 복제본에서 데이터를 복제합니다. 
먼저 모든 복제 된 테이블을 만들고 거기에 데이터를 삽입하는 방법이 있습니다. 다른 방법으로 여러 복제본을 생성하여 데이터 삽입 후 또는 데이터 삽입시 다른 복제본을 추가하는 방법도 있습니다. 


```sql 
CREATE TABLE tutorial.hits_replica (...) 
ENGINE = ReplcatedMergeTree ( 
    '/ clickhouse_perftest / tables / {shard} / hits', 
    '{replica}'

여기에서는 [ReplicatedMergeTree] (../ engines / table-engines / mergetree-family / replication.md) 테이블 엔진을 사용하고 있습니다. 
매개 변수는 샤드와 복제의 식별자를 포함 ZooKeeper path를 지정합니다. 

```sql 
INSERT INTO tutorial.hits_replica SELECT * FROM tutorial.hits_local; 
``` 

복제는 다중 마스터 모드에서 작동합니다. 
어떤 복제에 데이터를로드 할 수 시스템은 그것을 다른 인스턴스와 자동으로 동기화합니다. 
복제는 비동기이기 때문에 어느 시점에서 모든 복제에 최근 삽입 된 데이터가 포함되어있는 것은 없습니다. 
하나의 복제는 데이터 수집을 가능하게하기 위해 시작해야합니다. 
다른 복제 데이터를 동기화 다시 활성화 될 때 무결성을 복구합니다. 
이 방법은 최근 삽입 된 데이터가 손실 될 가능성이 낮은 점에 유의하십시오. 

원본 기사 (https://clickhouse.tech/docs/en/getting_started/tutorial/) <! - hide ->
