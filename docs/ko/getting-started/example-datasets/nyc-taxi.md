--- 
toc_priority : 16 
toc_title : "뉴욕 택시" 
--- 

# 뉴욕 택시 {# new-york-taxi-data} 

이 데이터 세트는 두 가지 방법으로 얻을 수 있습니다 : 

- 원시 데이터에서 가져 오기 
- 파티션 된 데이터 다운로드 

## 원시 데이터를 가져 오는 방법 {# how-to-import-the-raw-data} 

데이터 세트의 설명과 다운로드 방법은 https : //github.com/toddwschneider/nyc-taxi-data와 http : //tech.marksblogg.com/billion-nyc-taxi-rides-redshift.html를 참조하십시오. 

다운로드하면 CSV 파일에서 약 227GB의 비 압축 데이터가 생성됩니다. 다운로드는 약 1Gbit 회선에서 1 시간 이상 걸립니다. (s3.amazonaws.com에서 병렬 다운로드는 1Gbit 채널의 절반 이상을 회복합니다). 

일부 파일은 완전히 다운로드되지 않을 수 있습니다. 파일 크기를 확인하고 의심 스럽다고 생각되는 것은 다시 다운로드하십시오. 

파일에 잘못된 줄이 포함되어있을 수 있습니다 다음과 같이 수정 할 수 있습니다. 

```bash
sed -E '/(.*,){18,}/d'data / yellow_tripdata_2010-02.csv> data / yellow_tripdata_2010-02.csv_ 
sed -E '/(.*,){18,}/d'data /yellow_tripdata_2010-03.csv> data / yellow_tripdata_2010-03.csv_ 
mv data / yellow_tripdata_2010-02.csv_ data / yellow_tripdata_2010-02.csv 
mv data / yellow_tripdata_2010-03.csv_ data / yellow_tripdata_2010-03.csv 
``` 

그럼 PostgreSQL 에서 데이터를 전처리해야합니다. 그러면 (지도상의 점과 뉴욕시의 행정 구역을 일치시키기 위해) 다각형의 점 선택을 만들고 JOIN을 사용하여 모든 데이터를 하나의 비정규 화 된 평면 테이블에 조인 합니다. 이렇게는 PostGIS을 지원하는 PostgreSQL을 설치해야합니다. 

`inititialize_database.sh`을 수행 할 때주의가 필요하며, 모든 테이블이 올바르게 작성되었는지를 수동으로 다시 확인하십시오. 

1 개월 분의 데이터를 PostgreSQL로 처리하는 데 약 20 ~ 30 분 총 약 48 시간 정도 걸립니다. 

다운로드 한 행은 다음과 같이 확인할 수 있습니다 : 

```bash
$ time psql nyc-taxi-data -c "SELECT count (*) FROM trips;" 
## Count 
 1298979494 
(1 row) 

real 7m9.164s 
           trips .dropoff_latitude, 
```

(이것은 Mark Litwintschik 씨가 일련의 블로그에서보고 한 11 억 줄을 약간 웃돌고 있습니다) 


PostgreSQL의 데이터는 370GB의 용량을 사용합니다. 

PostgreSQL에서 데이터를 내보내기 : 

```sql 
COPY 
( 
    SELECT trips.id, 
           trips.vendor_id, 
           trips.pickup_datetime, 
           trips.dropoff_datetime, 
           trips.store_and_fwd_flag, 
           trips.rate_code_id, 
           trips.pickup_longitude, 
           trips.pickup_latitude, 
           trips.dropoff_longitude, 
           trips .passenger_count, 
           trips.trip_distance, 
           trips.fare_amount, 
           trips.extra, 
           trips.mta_tax,
           trips.tip_amount, 
           trips.tolls_amount, 
           trips.ehail_fee, 
           trips.improvement_surcharge, 
           trips.total_amount, 
           trips.payment_type, 
           trips.trip_type, 
           trips.pickup, 
           trips.dropoff, 

           cab_types.type cab_type, 

           weather.precipitation_tenths_of_mm rain, 
           weather.snow_depth_mm, 
           weather.snowfall_mm,  
           weather.max_temperature_tenths_degrees_celsius max_temp,
           weather.min_temperature_tenths_degrees_celsius min_temp, 
           weather.average_wind_speed_tenths_of_meters_per_second wind, 

           pick_up.gid pickup_nyct2010_gid,
           pick_up.ctlabel pickup_ctlabel, 
           pick_up.borocode pickup_borocode, 
           pick_up.boroname pickup_boroname, 
           pick_up.ct2010 pickup_ct2010, 
           pick_up.boroct2010 pickup_boroct2010, 
           pick_up.cdeligibil pickup_cdeligibil, 
           pick_up.ntacode pickup_ntacode, 
           pick_up.ntaname pickup_ntaname, 
           pick_up.puma pickup_puma, 

           drop_off.gid dropoff_nyct2010_gid, 
           drop_off.ctlabel dropoff_ctlabel,  
           drop_off.borocode dropoff_borocode,
           drop_off.boroname dropoff_boroname, 
           drop_off.ct2010 dropoff_ct2010, 
           drop_off.boroct2010 dropoff_boroct2010, 
           drop_off.cdeligibil dropoff_cdeligibil, 
           drop_off.ntacode dropoff_ntacode, 
           drop_off.ntaname dropoff_ntaname, 
           drop_off.puma dropoff_puma 
    FROM trips 
    LEFT JOIN cab_types 
        ON trips.cab_type_id = cab_types.id 
    LEFT JOIN central_park_weather_observations_raw weather 
        ON weather.date = trips.pickup_datetime : : date 
    LEFT JOIN nyct2010 pick_up 
        ON pick_up.gid = trips.pickup_nyct2010_gid 
    LEFT JOIN nyct2010 drop_off 
        ON drop_off.gid = trips.dropoff_nyct2010_gid 
) TO '/opt/milovidov/nyc-taxi-data/trips.tsv'; 
```

데이터 스냅 샷은 초당 약 50MB의 속도로 만들어집니다. 스냅 샷을 생성하는 동안 PostgreSQL는 초당 약 28MB의 속도로 디스크에서 읽습니다. 
여기에는 약 5 시간이 걸립니다. 결과 TSV 파일 590612904969 바이트입니다. 

ClickHouse에서 임시 테이블을 생성합니다 : 

```sql 
CREATE TABLE trips 
( 
trip_id UInt32, 
vendor_id String, 
pickup_datetime DateTime, 
dropoff_datetime Nullable (DateTime) 
store_and_fwd_flag Nullable (FixedString (1)), 
rate_code_id Nullable (UInt8) 
pickup_longitude Nullable ( Float64) 
pickup_latitude Nullable (Float64) 
dropoff_longitude Nullable (Float64) 
dropoff_latitude Nullable (Float64) 
passenger_count Nullable (UInt8)
trip_distance Nullable (Float64) 
fare_amount Nullable (Float32), 
extra Nullable (Float32) 
mta_tax Nullable (Float32) 
tip_amount Nullable (Float32) 
tolls_amount Nullable (Float32) 
ehail_fee Nullable (Float32) 
improvement_surcharge Nullable (Float32) 
total_amount Nullable (Float32) 
payment_type Nullable (String) 
trip_type Nullable (UInt8) 
pickup Nullable (String) 
dropoff Nullable (String) 
cab_type Nullable (String) 
precipitation Nullable (UInt8)
snow_depth Nullable (UInt8)  
snowfall Nullable (UInt8)
max_temperature Nullable (UInt8) 
min_temperature Nullable (UInt8) 
average_wind_speed Nullable (UInt8) 
pickup_nyct2010_gid Nullable (UInt8) 
pickup_ctlabel Nullable (String) 
pickup_borocode Nullable (UInt8) 
pickup_boroname Nullable (String) 
pickup_ct2010 Nullable (String) 
pickup_boroct2010 Nullable (String) 
pickup_cdeligibil Nullable (FixedString (1)), 
pickup_ntacode Nullable (String) 
pickup_ntaname Nullable (String) 
pickup_puma Nullable (String) 
dropoff_nyct2010_gid Nullable (UInt8)
dropoff_ctlabel Nullable (String)  
dropoff_borocode Nullable (UInt8)
dropoff_boroname Nullable (String) 
dropoff_ct2010 Nullable (String) 
dropoff_boroct2010 Nullable (String) 
dropoff_cdeligibil Nullable (String) 
dropoff_ntacode Nullable (String) 
dropoff_ntaname Nullable (String) 
dropoff_puma Nullable (String) 
) ENGINE = Log; 
``` 

이 필드를 더 올바른 데이터 형식으로 변환하거나 가능하면 NULL을 제거하는 데 필요합니다. 

```bash 
$ time clickhouse-client --query = "INSERT INTO trips FORMAT TabSeparated"<trips.tsv 

real 75m56.214s 
``` 

데이터의 읽기 속도는 112 ~ 140Mb / s입니다. 
1 스트림에서 Log 형태의 테이블에 데이터를로드하는 데 76 분이 걸렸다. 
이 테이블의 데이터는 142GB를 사용합니다.

(Postgres에서 직접 데이터를 가져 오려면`COPY ... TO PROGRAM`)를 사용하여도 가능합니다. 

불행히도, 날씨 관련 필드 (강수량 ... 평균 풍속)은 모두 NULL로 가득 차있었습니다. 따라서 최종 데이터 세트로부터 삭제합니다. 

먼저 하나의 서버에 테이블을 만듭니다. 그 후에 테이블을 분산시킵니다. 

요약 테이블을 만듭니다 : 

```sql 
CREATE TABLE trips_mergetree 
ENGINE = MergeTree (pickup_date, pickup_datetime 8192) 
AS SELECT 

trip_id, 
CAST (vendor_id AS Enum8 ( '1'= 1 '2'= 2 'CMT'= 3 'VTS'= 4 'DDS'= 5 'B02512'= 10 'B02598'= 11 'B02617'= 12 'B02682'= 13 'B02764'= 14)) AS vendor_id, 
toDate ( pickup_datetime) AS pickup_date, 
ifNull (pickup_datetime, toDateTime (0)) AS pickup_datetime, 
toDate (dropoff_datetime) AS dropoff_date,
assumeNotNull (store_and_fwd_flag) IN ( 'Y', '1', '2') AS store_and_fwd_flag, 
assumeNotNull (rate_code_id) AS rate_code_id, 
assumeNotNull (pickup_longitude) AS pickup_longitude, 
assumeNotNull (pickup_latitude) AS pickup_latitude, 
assumeNotNull (dropoff_longitude) AS dropoff_longitude, 
assumeNotNull (dropoff_latitude) AS dropoff_latitude, 
assumeNotNull (passenger_count) AS passenger_count, 
assumeNotNull (trip_distance) AS trip_distance, 
assumeNotNull (fare_amount) AS fare_amount, 
assumeNotNull (extra) AS extra, 
assumeNotNull (mta_tax) AS mta_tax, 
assumeNotNull (tip_amount) AS tip_amount, 
assumeNotNull ( tolls_amount) AS tolls_amount, 
assumeNotNull (ehail_fee) AS ehail_fee,
assumeNotNull (improvement_surcharge) AS improvement_surcharge, 
assumeNotNull (total_amount) AS total_amount, 
CAST ((assumeNotNull (payment_type) AS pt) IN ( 'CSH', 'CASH', 'Cash', 'CAS', 'Cas', '1') ? 'CSH': (pt IN ( 'CRD', 'Credit', 'Cre', 'CRE', 'CREDIT', '2')? 'CRE': (pt IN ( 'NOC', 'No Charge' 'No', '3')? 'NOC': (pt IN ( 'DIS', 'Dispute', 'Dis', '4')? 'DIS': 'UNK'))) AS Enum8 ( 'CSH '= 1'CRE '= 2'UNK '= 0'NOC '= 3'DIS '= 4)) AS payment_type_, 
assumeNotNull (trip_type) AS trip_type, 
ifNull (toFixedString (unhex (pickup) 25) , toFixedString ( '',25)) AS pickup, 
ifNull (toFixedString (unhex (dropoff) 25) toFixedString ( '25)) AS dropoff, 
CAST (assumeNotNull (cab_type) AS Enum8 ('yellow '= 1'green '= 2, 'uber'= 3)) AS cab_type,

assumeNotNull (pickup_nyct2010_gid) AS pickup_nyct2010_gid, 
toFloat32 (ifNull (pickup_ctlabel '0')) AS pickup_ctlabel, 
assumeNotNull (pickup_borocode) AS pickup_borocode, 
CAST (assumeNotNull (pickup_boroname) AS Enum8 ( 'Manhattan'= 1 'Queens'= 4 ' Brooklyn '= 3' '= 0'Bronx '= 2'Staten Island '= 5)) AS pickup_boroname, 
toFixedString (ifNull (pickup_ct2010'000000 ') 6) AS pickup_ct2010, 
toFixedString (ifNull (pickup_boroct2010' 0000000 '), 7) AS pickup_boroct2010, 
CAST (assumeNotNull (ifNull (pickup_cdeligibil' ')) AS Enum8 (' '= 0'E '= 1'I '= 2)) AS pickup_cdeligibil, 
toFixedString (ifNull (pickup_ntacode '0000') 4) AS pickup_ntacode,

CAST (assumeNotNull (pickup_ntaname) AS Enum16 ( ''= 0 'Airport'= 1 'Allerton-Pelham Gardens'= 2 'Annadale-Huguenot-Prince \'s Bay-Eltingville '= 3'Arden Heights '= 4 'Astoria'= 5 'Auburndale'= 6 'Baisley Park'= 7 'Bath Beach'= 8, 'Battery Park City-Lower Manhattan'= 9 'Bay Ridge'= 10 'Bayside-Bayside Hills '= 11'Bedford '= 12'Bedford Park-Fordham North '= 13'Bellerose '= 14'Belmont '= 15'Bensonhurst East '= 16'Bensonhurst West '= 17'Borough Park '= 18'Breezy Point-Belle Harbor-Rockaway Park-Broad Channel '= 19'Briarwood-Jamaica Hills '= 20'Brighton Beach '= 21'Bronxdale '= 22'Brooklyn Heights-Cobble Hill '= 23,'Brownsville'= 24 'Bushwick North'= 25 'Bushwick South'= 26 'Cambria Heights'= 27 'Canarsie'= 28 'Carroll Gardens-Columbia Street-Red Hook'= 29 'Central Harlem North -Polo Grounds '= 30'Central Harlem South '= 31'Charleston-Richmond Valley-Tottenville '= 32'Chinatown '= 33'Claremont-Bathgate '= 34'Clinton '= 35'Clinton Hill ' = 36 'Co-op City'= 37 'College Point'= 38 'Corona'= 39 'Crotona Park East'= 40 'Crown Heights North'= 41 'Crown Heights South'= 42 ' Cypress Hills-City Line '= 43'DUMBO-Vinegar Hill-Downtown Brooklyn-Boerum Hill '= 44'Douglas Manor-Douglaston-Little Neck '= 45'Dyker Heights '= 46'East Concourse-Concourse Village '= 47'East Elmhurst '= 48'East Flatbush-Farragut '= 49'East Flushing '= 50'East Harlem North '= 51'East Harlem South '= 52'East New York '= 53'East New York (Pennsylvania Ave) '= 54'East Tremont '= 55'East Village '= 56'East Williamsburg '= 57'Eastchester-Edenwald-Baychester '= 58' Elmhurst '= 59'Elmhurst-Maspeth '= 60'Erasmus '= 61'Far Rockaway-Bayswater '= 62'Flatbush '= 63'Flatlands '= 64'Flushing '= 65'Fordham South ' = 66 'Forest Hills'= 67 'Fort Greene'= 68 'Fresh Meadows-Utopia'= 69 'Ft. Totten-Bay Terrace-Clearview'= 70 'Georgetown-Marine Park-Bergen Beach-Mill Basin '= 71 'Glen Oaks-Floral Park - New Hyde Park'= 72 'Glendale'= 73 'Gramercy'= 74 'Grasmere-Arrochar-Ft. Wadsworth'= 75 'Gravesend'= 76 'Great Kills '= 77'Greenpoint '= 78'Grymes Hill-Clifton-Fox Hills '= 79'Hamilton Heights '= 80'Hammels-Arverne-Edgemere '= 81'Highbridge '= 82'Hollis '= 83 'Homecrest'= 84 'Hudson Yards-Chelsea-Flatiron-Union Square'= 85 'Hunters Point-Sunnyside-West Maspeth'= 86 'Hunts Point'= 87 'Jackson Heights'= 88 'Jamaica' = 89 'Jamaica Estates-Holliswood'= 90 'Kensington-Ocean Parkway'= 91 'Kew Gardens'= 92 'Kew Gardens Hills'= 93 'Kingsbridge Heights'= 94 'Laurelton'= 95 'Lenox Hill-Roosevelt Island '= 96'Lincoln Square '= 97'Lindenwood-Howard Beach '= 98'Longwood '= 99'Lower East Side '= 100'Madison '= 101'Manhattanville '= 102 'Marble Hill-Inwood'= 103 'Mariner \'s Harbor-Arlington-Port Ivory-Graniteville '= 104'Maspeth '= 105'Melrose South-Mott Haven North '= 106'Middle Village '= 107 'Midtown-Midtown South'= 108 'Midwood'= 109 'Morningside Heights'= 110 'Morrisania-Melrose'= 111 'Mott Haven-Port Morris'= 112 'Mount Hope'= 113 'Murray Hill '= 114'Murray Hill-Kips Bay '= 115'New Brighton-Silver Lake '= 116'New Dorp-Midland Beach '= 117'New Springville-Bloomfield-Travis '= 118'North Corona '= 119'North Riverdale-Fieldston-Riverdale '= 120'North Side-South Side '= 121'Norwood '= 122'Oakland Gardens '= 123'Oakwood-Oakwood Beach '= 124' Ocean Hill '= 125'Ocean Parkway South '= 126'Old Astoria '127'Old Town-Dongan Hills-South Beach '= 128'Ozone Park '= 129'Park Slope-Gowanus '= 130, 'Parkchester'= 131 'Pelham Bay-Country Club-City Island'= 132 'Pelham Parkway'= 133 'Pomonok-Flushing Heights-Hillcrest'= 134 'Port Richmond'= 135 'Prospect Heights'= 136 'Prospect Lefferts Gardens-Wingate'= 137 'Queens Village'= 138 'Queensboro Hill'= 139 'Queensbridge-Ravenswood-Long Island City'= 140 'Rego Park'= 141 'Richmond Hill '= 142'Ridgewood '= 143'Rikers Island '= 144'Rosedale '= 145'Rossville-Woodrow '= 146'Rugby-Remsen Village '= 147'Schuylerville-Throgs Neck-Edgewater Park '= 148'Seagate-Coney Island '= 149'Sheepshead Bay-Gerritsen Beach-Manhattan Beach '= 150'SoHo-TriBeCa-Civic Center-Little Italy '= 151'Soundview-Bruckner '= 152'Soundview -Castle Hill-Clason Point-Harding Park '= 153'South Jamaica '= 154'South Ozone Park '= 155'Springfield Gardens North '= 156'Springfield Gardens South-Brookville '= 157'Spuyten Duyvil- Kingsbridge '= 158'St. Albans '= 159'Stapleton-Rosebank '= 160'Starrett City '= 161'Steinway '= 162'Stuyvesant Heights '= 163 'Stuyvesant Town-Cooper Village'= 164 'Sunset Park East'= 165 'Sunset Park West'= 166 'Todt Hill-Emerson Hill-Heartland Village-Lighthouse Hill'= 167 'Turtle Bay-East Midtown '= 168'University Heights-Morris Heights '= 169'Upper East Side-Carnegie Hill '= 170'Upper West Side '= 171'Van Cortlandt Village '= 172'Van Nest-Morris Park-Westchester Square '= 173'Washington Heights North '= 174'Washington Heights South '= 175'West Brighton '= 176'West Concourse '= 177'West Farms-Bronx River '= 178'West New Brighton- New Brighton-St. George '= 179'West Village '= 180'Westchester-Unionport '= 181'Westerleigh '= 182'Whitestone '= 183'Williamsbridge-Olinville '= 184'Williamsburg '= 185'Windsor Terrace '= 186'Woodhaven '= 187'Woodlawn-Wakefield '= 188'Woodside '= 189'Yorkville '190'park-cemetery -etc-Bronx '= 191'park-cemetery-etc-Brooklyn '192'park-cemetery-etc-Manhattan '= 193'park-cemetery-etc-Queens '= 194'park-cemetery-etc -Staten Island '= 195)) AS pickup_ntaname,park-cemetery-etc-Staten Island '= 195)) AS pickup_ntaname,park-cemetery-etc-Staten Island '= 195)) AS pickup_ntaname,

toUInt16 (ifNull (pickup_puma '0')) AS pickup_puma, 

assumeNotNull (dropoff_nyct2010_gid) AS dropoff_nyct2010_gid, 
toFloat32 (ifNull (dropoff_ctlabel '0')) AS dropoff_ctlabel, 
assumeNotNull (dropoff_borocode) AS dropoff_borocode, 
CAST (assumeNotNull (dropoff_boroname) AS Enum8 ( 'Manhattan'= 1 'Queens'= 4 'Brooklyn'= 3 ''= 0 'Bronx'= 2 'Staten Island'= 5)) AS dropoff_boroname, 
toFixedString (ifNull (dropoff_ct2010 '000000' ) 6) AS dropoff_ct2010, 
toFixedString (ifNull (dropoff_boroct2010 '0000000'), 7) AS dropoff_boroct2010, 
CAST (assumeNotNull (ifNull (dropoff_cdeligibil '')) AS Enum8 ( ''= 0 'E'= 1 ' I '= 2)) AS dropoff_cdeligibil,
toFixedString (ifNull (dropoff_ntacode, '0000') 4) AS dropoff_ntacode,

CAST (assumeNotNull (dropoff_ntaname) AS Enum16 ( ''= 0 'Airport'= 1 'Allerton-Pelham Gardens'= 2 'Annadale-Huguenot-Prince \'s Bay-Eltingville '= 3'Arden Heights '= 4 'Astoria'= 5 'Auburndale'= 6 'Baisley Park'= 7 'Bath Beach'= 8, 'Battery Park City-Lower Manhattan'= 9 'Bay Ridge'= 10 'Bayside-Bayside Hills '= 11'Bedford '= 12'Bedford Park-Fordham North '= 13'Bellerose '= 14'Belmont '= 15'Bensonhurst East '= 16'Bensonhurst West '= 17'Borough Park '= 18'Breezy Point-Belle Harbor-Rockaway Park-Broad Channel '= 19'Briarwood-Jamaica Hills '= 20'Brighton Beach '= 21'Bronxdale '= 22'Brooklyn Heights-Cobble Hill '= 23,'Brownsville'= 24 'Bushwick North'= 25 'Bushwick South'= 26 'Cambria Heights'= 27 'Canarsie'= 28 'Carroll Gardens-Columbia Street-Red Hook'= 29 'Central Harlem North -Polo Grounds '= 30'Central Harlem South '= 31'Charleston-Richmond Valley-Tottenville '= 32'Chinatown '= 33'Claremont-Bathgate '= 34'Clinton '= 35'Clinton Hill ' = 36 'Co-op City'= 37 'College Point'= 38 'Corona'= 39 'Crotona Park East'= 40 'Crown Heights North'= 41 'Crown Heights South'= 42 ' Cypress Hills-City Line '= 43'DUMBO-Vinegar Hill-Downtown Brooklyn-Boerum Hill '= 44'Douglas Manor-Douglaston-Little Neck '= 45'Dyker Heights '= 46'East Concourse-Concourse Village '= 47'East Elmhurst '= 48'East Flatbush-Farragut '= 49'East Flushing '= 50'East Harlem North '= 51'East Harlem South '= 52'East New York '= 53'East New York (Pennsylvania Ave) '= 54'East Tremont '= 55'East Village '= 56'East Williamsburg '= 57'Eastchester-Edenwald-Baychester '= 58' Elmhurst '= 59'Elmhurst-Maspeth '= 60'Erasmus '= 61'Far Rockaway-Bayswater '= 62'Flatbush '= 63'Flatlands '= 64'Flushing '= 65'Fordham South ' = 66 'Forest Hills'= 67 'Fort Greene'= 68 'Fresh Meadows-Utopia'= 69 'Ft. Totten-Bay Terrace-Clearview'= 70 'Georgetown-Marine Park-Bergen Beach-Mill Basin '= 71 'Glen Oaks-Floral Park - New Hyde Park'= 72 'Glendale'= 73 'Gramercy'= 74 'Grasmere-Arrochar-Ft. Wadsworth'= 75 'Gravesend'= 76 'Great Kills '= 77'Greenpoint '= 78'Grymes Hill-Clifton-Fox Hills '= 79'Hamilton Heights '= 80'Hammels-Arverne-Edgemere '= 81'Highbridge '= 82'Hollis '= 83 'Homecrest'= 84 'Hudson Yards-Chelsea-Flatiron-Union Square'= 85 'Hunters Point-Sunnyside-West Maspeth'= 86 'Hunts Point'= 87 'Jackson Heights'= 88 'Jamaica' = 89 'Jamaica Estates-Holliswood'= 90 'Kensington-Ocean Parkway'= 91 'Kew Gardens'= 92 'Kew Gardens Hills'= 93 'Kingsbridge Heights'= 94 'Laurelton'= 95 'Lenox Hill-Roosevelt Island '= 96'Lincoln Square '= 97'Lindenwood-Howard Beach '= 98'Longwood '= 99'Lower East Side '= 100'Madison '= 101'Manhattanville '= 102 'Marble Hill-Inwood'= 103 'Mariner \'s Harbor-Arlington-Port Ivory-Graniteville '= 104'Maspeth '= 105'Melrose South-Mott Haven North '= 106'Middle Village '= 107 'Midtown-Midtown South'= 108 'Midwood'= 109 'Morningside Heights'= 110 'Morrisania-Melrose'= 111 'Mott Haven-Port Morris'= 112 'Mount Hope'= 113 'Murray Hill '= 114'Murray Hill-Kips Bay '= 115'New Brighton-Silver Lake '= 116'New Dorp-Midland Beach '= 117'New Springville-Bloomfield-Travis '= 118'North Corona '= 119'North Riverdale-Fieldston-Riverdale '= 120'North Side-South Side '= 121'Norwood '= 122'Oakland Gardens '= 123'Oakwood-Oakwood Beach '= 124' Ocean Hill '= 125'Ocean Parkway South '= 126'Old Astoria '127'Old Town-Dongan Hills-South Beach '= 128'Ozone Park '= 129'Park Slope-Gowanus '= 130, 'Parkchester'= 131 'Pelham Bay-Country Club-City Island'= 132 'Pelham Parkway'= 133 'Pomonok-Flushing Heights-Hillcrest'= 134 'Port Richmond'= 135 'Prospect Heights'= 136 'Prospect Lefferts Gardens-Wingate'= 137 'Queens Village'= 138 'Queensboro Hill'= 139 'Queensbridge-Ravenswood-Long Island City'= 140 'Rego Park'= 141 'Richmond Hill '= 142'Ridgewood '= 143'Rikers Island '= 144'Rosedale '= 145'Rossville-Woodrow '= 146'Rugby-Remsen Village '= 147'Schuylerville-Throgs Neck-Edgewater Park '= 148'Seagate-Coney Island '= 149'Sheepshead Bay-Gerritsen Beach-Manhattan Beach '= 150'SoHo-TriBeCa-Civic Center-Little Italy '= 151'Soundview-Bruckner '= 152'Soundview -Castle Hill-Clason Point-Harding Park '= 153'South Jamaica '= 154'South Ozone Park '= 155'Springfield Gardens North '= 156'Springfield Gardens South-Brookville '= 157'Spuyten Duyvil- Kingsbridge '= 158'St. Albans '= 159'Stapleton-Rosebank '= 160'Starrett City '= 161'Steinway '= 162'Stuyvesant Heights '= 163 'Stuyvesant Town-Cooper Village'= 164 'Sunset Park East'= 165 'Sunset Park West'= 166 'Todt Hill-Emerson Hill-Heartland Village-Lighthouse Hill'= 167 'Turtle Bay-East Midtown '= 168'University Heights-Morris Heights '= 169'Upper East Side-Carnegie Hill '= 170'Upper West Side '= 171'Van Cortlandt Village '= 172'Van Nest-Morris Park-Westchester Square '= 173'Washington Heights North '= 174'Washington Heights South '= 175'West Brighton '= 176'West Concourse '= 177'West Farms-Bronx River '= 178'West New Brighton- New Brighton-St. George '= 179'West Village '= 180'Westchester-Unionport '= 181'Westerleigh '= 182'Whitestone '= 183'Williamsbridge-Olinville '= 184'Williamsburg '= 185'Windsor Terrace '= 186'Woodhaven '= 187'Woodlawn-Wakefield '= 188'Woodside '= 189'Yorkville '190'park-cemetery -etc-Bronx '= 191'park-cemetery-etc-Brooklyn '192'park-cemetery-etc-Manhattan '= 193'park-cemetery-etc-Queens '= 194'park-cemetery-etc -Staten Island '= 195)) AS dropoff_ntaname,park-cemetery-etc-Staten Island '= 195)) AS dropoff_ntaname,park-cemetery-etc-Staten Island '= 195)) AS dropoff_ntaname,

toUInt16 (ifNull (dropoff_puma '0')) AS dropoff_puma 

FROM trips 
``` 

이것은 1 초에 약 428,000 라인의 속도로 3030 초가 걸립니다. 
빨리로드하려면`MergeTree` 대신`Log` 엔진에서 테이블을 만듭니다. 이 경우, 다운로드는 200 초보다 빠르게 작동합니다. 

테이블은 126GB의 디스크 공간을 사용합니다. 


```sql 
SELECT formatReadableSize (sum (bytes)) FROM system.parts WHERE table = 'trips_mergetree'AND active 
``` 

```text 
┌─formatReadableSize (sum (bytes)) ─┐ 
│ 126.18 GiB │ 
└─── ─────────────────────────────┘ 
``` 

기타에 MergeTree에서 OPTIMIZE 쿼리를 실행할 수 있습니다. 그러나 이것은없이 잘 작동하기 때문에 필요하지 않습니다. 

## 파티션 된 데이터의 다운로드 {# download-of-prepared-partitions}
 
```bash
$ curl -O https://datasets.clickhouse.tech/trips_mergetree/partitions/trips_mergetree.tar 
$ tar xvf trips_mergetree.tar -C / var / lib / clickhouse # path to ClickHouse data directory 
$ # check permissions of unpacked data, fix if required 
$ sudo service clickhouse-server restart 
$ clickhouse-client --query "select count (*) from datasets.trips_mergetree" 
``` 

!!! info "정보" 
    아래에서 설명하는 쿼리를 실행하려면`datasets .trips_mergetree`처럼 완전한 테이블 이름을 사용해야합니다. 

## 단일 서버에서 결과 {# results-on-single-server} 

Q1 : 

```sql 
SELECT cab_type, count (*) FROM trips_mergetree GROUP BY cab_type 
``` 

0.490 초 

Q2 :
 
```sql
SELECT passenger_count avg (total_amount) FROM trips_mergetree GROUP BY passenger_count 
``` 

1.224 초 

Q3 : 

```sql 
SELECT passenger_count, toYear (pickup_date) AS year, count (*) FROM trips_mergetree GROUP BY passenger_count, year 
``` 

2.104 초 

Q4 : 

```sql 
SELECT passenger_count, toYear (pickup_date) AS year round (trip_distance) AS distance, count (*) 
FROM trips_mergetree 
GROUP BY passenger_count, year, distance 
ORDER BY year, count (*) DESC 
``` 

3.593 초 

사용 한 서버는 다음과 같습니다. 

Intel (R) Xeon (R) CPU E5-2650 v2 @ 2.60GHz 2 개, 총 16 개의 물리적 커널 128GiB RAM, RAID-5의 8 x 6 TB HDD.

실행 시간은 3 회 수행 한 가장 좋은 결과입니다. 그러나 두 번째 실행에서 쿼리는 파일 시스템 캐시에서 데이터를 읽습니다. 그 이상의 캐시가 발생하지 않습니다 : 데이터를 읽 각 실행에서 처리됩니다. 

3 대의 서버에 테이블을 만들 : 

각 서버에서 : 

```sql
CREATE TABLE default.trips_mergetree_third (trip_id UInt32, vendor_id Enum8 ( '1'= 1 '2'= 2 'CMT'= 3 'VTS'= 4 'DDS'= 5 'B02512'= 10 'B02598 '= 11'B02617 '= 12'B02682 '= 13'B02764 '= 14), pickup_date Date, pickup_datetime DateTime, dropoff_date Date, dropoff_datetime DateTime, store_and_fwd_flag UInt8, rate_code_id UInt8, pickup_longitude Float64, pickup_latitude Float64, dropoff_longitude Float64, dropoff_latitude Float64, passenger_count UInt8, trip_distance Float64, fare_amount Float32, extra Float32, mta_tax Float32, tip_amount Float32, tolls_amount Float32, ehail_fee Float32, improvement_surcharge Float32, total_amount Float32, payment_type_ Enum8 ( 'UNK'= 0 'CSH'= 1 'CRE '= 2'NOC '= 3,'DIS'= 4), trip_type UInt8, pickup FixedString (25) dropoff FixedString (25) cab_type Enum8 ( 'yellow'= 1 'green'= 2 'uber'= 3), pickup_nyct2010_gid UInt8, pickup_ctlabel Float32, pickup_borocode UInt8, pickup_boroname Enum8 ( ''= 0 'Manhattan'= 1 'Bronx'= 2 'Brooklyn'= 3, 'Queens'= 4 'Staten Island'= 5), pickup_ct2010 FixedString (6) pickup_boroct2010 FixedString (7) pickup_cdeligibil Enum8 ( ''= 0 'E'= 1 'I'= 2), pickup_ntacode FixedString (4) pickup_ntaname Enum16 ( ''= 0 'Airport'= 1 'Allerton-Pelham Gardens '= 2'Annadale-Huguenot-Prince \ 's Bay-Eltingville'= 3 'Arden Heights'= 4 'Astoria'= 5 'Auburndale'= 6 'Baisley Park'= 7 'Bath Beach '= 8,'Battery Park City-Lower Manhattan '= 9'Bay Ridge '= 10'Bayside-Bayside Hills '= 11'Bedford '= 12'Bedford Park-Fordham North '= 13' Bellerose '= 14'Belmont '= 15'Bensonhurst East '= 16'Bensonhurst West '= 17'Borough Park '= 18'Breezy Point-Belle Harbor-Rockaway Park-Broad Channel '= 19'Briarwood -Jamaica Hills '= 20'Brighton Beach '= 21'Bronxdale '= 22'Brooklyn Heights-Cobble Hill '= 23'Brownsville '= 24'Bushwick North '= 25'Bushwick South '= 26, 'Cambria Heights'= 27 'Canarsie'= 28 'Carroll Gardens-Columbia Street-Red Hook'= 29 'Central Harlem North-Polo Grounds'= 30 'Central Harlem South'= 31 'Charleston-Richmond Valley-Tottenville '= 32'Chinatown '= 33'Claremont-Bathgate '= 34'Clinton '= 35'Clinton Hill '= 36'Co-op City '= 37'College Point ' = 38 'Corona'= 39 'Crotona Park East'= 40 'Crown Heights North'= 41 'Crown Heights South'= 42 'Cypress Hills-City Line'= 43 'DUMBO-Vinegar Hill-Downtown Brooklyn-Boerum Hill '= 44'Douglas Manor-Douglaston-Little Neck '= 45'Dyker Heights '= 46'East Concourse-Concourse Village '= 47'East Elmhurst '= 48'East Flatbush-Farragut ' = 49 'East Flushing'= 50 'East Harlem North'= 51 'East Harlem South'= 52 'East New York'= 53 'East New York (Pennsylvania Ave)'= 54 'East Tremont'= 55 'East Village'= 56 'East Williamsburg'= 57 'Eastchester-Edenwald-Baychester'= 58 'Elmhurst'= 59 'Elmhurst-Maspeth'= 60 'Erasmus'= 61 'Far Rockaway -Bayswater '= 62'Flatbush '= 63'Flatlands '= 64'Flushing '= 65'Fordham South '= 66'Forest Hills '= 67'Fort Greene '= 68'Fresh Meadows-Utopia '= 69'Ft. Totten-Bay Terrace-Clearview '= 70'Georgetown-Marine Park-Bergen Beach-Mill Basin '= 71'Glen Oaks-Floral Park - New Hyde Park '= 72'Glendale '= 73 'Gramercy'= 74 'Grasmere-Arrochar-Ft. Wadsworth'= 75 'Gravesend'= 76 'Great Kills'= 77 'Greenpoint'= 78 'Grymes Hill-Clifton-Fox Hills'= 79 'Hamilton Heights'= 80,'Hammels-Arverne-Edgemere'= 81 'Highbridge'= 82 'Hollis'= 83 'Homecrest'= 84 'Hudson Yards-Chelsea-Flatiron-Union Square'= 85 'Hunters Point-Sunnyside-West Maspeth '= 86'Hunts Point '= 87'Jackson Heights '= 88'Jamaica '= 89'Jamaica Estates-Holliswood '= 90'Kensington-Ocean Parkway '= 91'Kew Gardens '= 92' Kew Gardens Hills '= 93'Kingsbridge Heights '= 94'Laurelton '= 95'Lenox Hill-Roosevelt Island '= 96'Lincoln Square '= 97'Lindenwood-Howard Beach '= 98'Longwood '= 99 'Lower East Side'= 100 'Madison'= 101 'Manhattanville'= 102 'Marble Hill-Inwood'= 103 'Mariner \'s Harbor-Arlington-Port Ivory-Graniteville '= 104,'Maspeth'= 105 'Melrose South-Mott Haven North'= 106 'Middle Village'= 107 'Midtown-Midtown South'= 108 'Midwood'= 109 'Morningside Heights'= 110 'Morrisania-Melrose '= 111'Mott Haven-Port Morris '= 112'Mount Hope '= 113'Murray Hill '= 114'Murray Hill-Kips Bay '= 115'New Brighton-Silver Lake '= 116'New Dorp-Midland Beach '= 117'New Springville-Bloomfield-Travis '= 118'North Corona '= 119'North Riverdale-Fieldston-Riverdale '= 120'North Side-South Side '= 121'Norwood ' = 122 'Oakland Gardens'= 123 'Oakwood-Oakwood Beach'= 124 'Ocean Hill'= 125 'Ocean Parkway South'= 126 'Old Astoria'= 127 'Old Town-Dongan Hills-South Beach '= 128'Ozone Park '= 129'Park Slope-Gowanus '= 130'Parkchester '= 131'Pelham Bay-Country Club-City Island '= 132'Pelham Parkway '= 133'Pomonok-Flushing Heights-Hillcrest '= 134'Port Richmond '= 135'Prospect Heights '= 136'Prospect Lefferts Gardens-Wingate '= 137'Queens Village '= 138'Queensboro Hill ' = 139 'Queensbridge-Ravenswood-Long Island City'= 140 'Rego Park'= 141 'Richmond Hill'= 142 'Ridgewood'= 143 'Rikers Island'= 144 'Rosedale'= 145 'Rossville -Woodrow '= 146'Rugby-Remsen Village '= 147'Schuylerville-Throgs Neck-Edgewater Park '= 148'Seagate-Coney Island '= 149'Sheepshead Bay-Gerritsen Beach-Manhattan Beach '= 150'SoHo-TriBeCa-Civic Center-Little Italy '= 151'Soundview-Bruckner '= 152'Soundview-Castle Hill-Clason Point-Harding Park '= 153' South Jamaica '= 154'South Ozone Park '= 155'Springfield Gardens North '= 156'Springfield Gardens South-Brookville '= 157'Spuyten Duyvil-Kingsbridge '= 158'St. Albans '= 159' Stapleton-Rosebank '= 160'Starrett City '= 161'Steinway '= 162'Stuyvesant Heights '= 163'Stuyvesant Town-Cooper Village '= 164'Sunset Park East '= 165'Sunset Park West ' = 166 'Todt Hill-Emerson Hill-Heartland Village-Lighthouse Hill'= 167 'Turtle Bay-East Midtown'= 168 'University Heights-Morris Heights'= 169,'Upper East Side-Carnegie Hill'= 170 'Upper West Side'= 171 'Van Cortlandt Village'= 172 'Van Nest-Morris Park-Westchester Square'= 173 'Washington Heights North'= 174 'Washington Heights South '= 175'West Brighton '= 176'West Concourse '= 177'West Farms-Bronx River '= 178'West New Brighton-New Brighton-St. George '= 179'West Village '= 180 'Westchester-Unionport'= 181 'Westerleigh'= 182 'Whitestone'= 183 'Williamsbridge-Olinville'= 184 'Williamsburg'= 185 'Windsor Terrace'= 186 'Woodhaven'= 187 ' Woodlawn-Wakefield '= 188'Woodside '= 189'Yorkville '190'park-cemetery-etc-Bronx '= 191'park-cemetery-etc-Brooklyn '= 192'park-cemetery-etc-Manhattan '= 193'park-cemetery-etc-Queens '= 194'park-cemetery-etc-Staten Island '= 195) pickup_puma UInt16, dropoff_nyct2010_gid UInt8, dropoff_ctlabel Float32, dropoff_borocode UInt8, dropoff_boroname Enum8 ( ''= 0 'Manhattan'= 1 'Bronx'= 2 'Brooklyn'= 3, 'Queens'= 4 'Staten Island'= 5), dropoff_ct2010 FixedString (6) dropoff_boroct2010 FixedString (7) , dropoff_cdeligibil Enum8 ( ''= 0 'E'= 1 'I'= 2), dropoff_ntacode FixedString (4) dropoff_ntaname Enum16 ( ''= 0 'Airport'= 1 'Allerton-Pelham Gardens'= 2 'Annadale-Huguenot-Prince \'s Bay-Eltingville '= 3'Arden Heights '= 4'Astoria '= 5'Auburndale '= 6'Baisley Park '= 7'Bath Beach '= 8,'Battery Park City-Lower Manhattan '= 9'Bay Ridge '= 10'Bayside-Bayside Hills '= 11'Bedford '= 12'Bedford Park-Fordham North '= 13' Bellerose '= 14'Belmont '= 15'Bensonhurst East '= 16'Bensonhurst West '= 17'Borough Park '= 18'Breezy Point-Belle Harbor-Rockaway Park-Broad Channel '= 19'Briarwood -Jamaica Hills '= 20'Brighton Beach '= 21'Bronxdale '= 22'Brooklyn Heights-Cobble Hill '= 23'Brownsville '= 24'Bushwick North '= 25'Bushwick South '= 26, 'Cambria Heights'= 27 'Canarsie'= 28 'Carroll Gardens-Columbia Street-Red Hook'= 29 'Central Harlem North-Polo Grounds'= 30 'Central Harlem South'= 31 'Charleston-Richmond Valley-Tottenville '= 32'Chinatown '= 33'Claremont-Bathgate '= 34'Clinton '= 35'Clinton Hill '= 36'Co-op City '= 37'College Point ' = 38 'Corona'= 39 'Crotona Park East'= 40 'Crown Heights North'= 41 'Crown Heights South'= 42 'Cypress Hills-City Line'= 43 'DUMBO-Vinegar Hill-Downtown Brooklyn-Boerum Hill '= 44'Douglas Manor-Douglaston-Little Neck '= 45'Dyker Heights '= 46'East Concourse-Concourse Village '= 47'East Elmhurst '= 48'East Flatbush-Farragut ' = 49 'East Flushing'= 50 'East Harlem North'= 51 'East Harlem South'= 52 'East New York'= 53 'East New York (Pennsylvania Ave)'= 54 'East Tremont'= 55 'East Village'= 56 'East Williamsburg'= 57 'Eastchester-Edenwald-Baychester'= 58 'Elmhurst'= 59 'Elmhurst-Maspeth'= 60 'Erasmus'= 61 'Far Rockaway -Bayswater '= 62'Flatbush '= 63'Flatlands '= 64'Flushing '= 65'Fordham South '= 66'Forest Hills '= 67'Fort Greene '= 68'Fresh Meadows-Utopia '= 69'Ft. Totten-Bay Terrace-Clearview '= 70'Georgetown-Marine Park-Bergen Beach-Mill Basin '= 71'Glen Oaks-Floral Park - New Hyde Park '= 72'Glendale '= 73 'Gramercy'= 74 'Grasmere-Arrochar-Ft. Wadsworth'= 75 'Gravesend'= 76 'Great Kills'= 77 'Greenpoint'= 78 'Grymes Hill-Clifton-Fox Hills'= 79 'Hamilton Heights'= 80,'Hammels-Arverne-Edgemere'= 81 'Highbridge'= 82 'Hollis'= 83 'Homecrest'= 84 'Hudson Yards-Chelsea-Flatiron-Union Square'= 85 'Hunters Point-Sunnyside-West Maspeth '= 86'Hunts Point '= 87'Jackson Heights '= 88'Jamaica '= 89'Jamaica Estates-Holliswood '= 90'Kensington-Ocean Parkway '= 91'Kew Gardens '= 92' Kew Gardens Hills '= 93'Kingsbridge Heights '= 94'Laurelton '= 95'Lenox Hill-Roosevelt Island '= 96'Lincoln Square '= 97'Lindenwood-Howard Beach '= 98'Longwood '= 99 'Lower East Side'= 100 'Madison'= 101 'Manhattanville'= 102 'Marble Hill-Inwood'= 103 'Mariner \'s Harbor-Arlington-Port Ivory-Graniteville '= 104,'Maspeth'= 105 'Melrose South-Mott Haven North'= 106 'Middle Village'= 107 'Midtown-Midtown South'= 108 'Midwood'= 109 'Morningside Heights'= 110 'Morrisania-Melrose '= 111'Mott Haven-Port Morris '= 112'Mount Hope '= 113'Murray Hill '= 114'Murray Hill-Kips Bay '= 115'New Brighton-Silver Lake '= 116'New Dorp-Midland Beach '= 117'New Springville-Bloomfield-Travis '= 118'North Corona '= 119'North Riverdale-Fieldston-Riverdale '= 120'North Side-South Side '= 121'Norwood ' = 122 'Oakland Gardens'= 123 'Oakwood-Oakwood Beach'= 124 'Ocean Hill'= 125 'Ocean Parkway South'= 126 'Old Astoria'= 127 'Old Town-Dongan Hills-South Beach '= 128'Ozone Park '= 129'Park Slope-Gowanus '= 130'Parkchester '= 131'Pelham Bay-Country Club-City Island '= 132'Pelham Parkway '= 133'Pomonok-Flushing Heights-Hillcrest '= 134'Port Richmond '= 135'Prospect Heights '= 136'Prospect Lefferts Gardens-Wingate '= 137'Queens Village '= 138'Queensboro Hill ' = 139 'Queensbridge-Ravenswood-Long Island City'= 140 'Rego Park'= 141 'Richmond Hill'= 142 'Ridgewood'= 143 'Rikers Island'= 144 'Rosedale'= 145 'Rossville -Woodrow '= 146'Rugby-Remsen Village '= 147'Schuylerville-Throgs Neck-Edgewater Park '= 148'Seagate-Coney Island '= 149'Sheepshead Bay-Gerritsen Beach-Manhattan Beach '= 150'SoHo-TriBeCa-Civic Center-Little Italy '= 151'Soundview-Bruckner '= 152'Soundview-Castle Hill-Clason Point-Harding Park '= 153' South Jamaica '= 154'South Ozone Park '= 155'Springfield Gardens North '= 156'Springfield Gardens South-Brookville '= 157'Spuyten Duyvil-Kingsbridge '= 158'St. Albans '= 159' Stapleton-Rosebank '= 160'Starrett City '= 161'Steinway '= 162'Stuyvesant Heights '= 163'Stuyvesant Town-Cooper Village '= 164'Sunset Park East '= 165'Sunset Park West ' = 166 'Todt Hill-Emerson Hill-Heartland Village-Lighthouse Hill'= 167 'Turtle Bay-East Midtown'= 168 'University Heights-Morris Heights'= 169,'Upper East Side-Carnegie Hill'= 170 'Upper West Side'= 171 'Van Cortlandt Village'= 172 'Van Nest-Morris Park-Westchester Square'= 173 'Washington Heights North'= 174 'Washington Heights South '= 175'West Brighton '= 176'West Concourse '= 177'West Farms-Bronx River '= 178'West New Brighton-New Brighton-St. George '= 179'West Village '= 180 'Westchester-Unionport'= 181 'Westerleigh'= 182 'Whitestone'= 183 'Williamsbridge-Olinville'= 184 'Williamsburg'= 185 'Windsor Terrace'= 186 'Woodhaven'= 187 ' Woodlawn-Wakefield '= 188'Woodside '= 189'Yorkville '190'park-cemetery-etc-Bronx '= 191'park-cemetery-etc-Brooklyn '= 192'park-cemetery-etc-Manhattan '= 193'park-cemetery-etc-Queens '= 194'park-cemetery-etc-Staten Island '= 195) dropoff_puma UInt16) ENGINE = MergeTree (pickup_date, pickup_datetime 8192)
``` 

원본 서버 : 

```sql 
CREATE TABLE trips_mergetree_x3 AS trips_mergetree_third ENGINE = Distributed (perftest, default, trips_mergetree_third, rand ()) 
``` 

다음 쿼리에서 데이터를 다시 배포합니다 : 

```sql 
INSERT INTO trips_mergetree_x3 SELECT * FROM trips_mergetree 
``` 

여기에는 2454 초 정도 걸립니다. 

세 서버에서 쿼리를 실행하면 : 

Q1 : 0.212 초 
Q2 : 0.438 초 
Q3 : 0.733 초 
Q4 : 1.241 초 

쿼리는 선형으로 확장되고 있기 때문에 여기에서는 예상대로입니다. 

또한 140 대의 서버 클러스터에서의 결과도 얻을 수 있습니다 : 

Q1 : 0.028 초 
Q2 : 0.043 초 
Q3 : 0.051 초 
Q4 : 0.072 초 

이 경우 쿼리 처리 시간은 네트워크 지연 시간에 의해 결정됩니다. 
핀란드 Yandex 데이터 센터에있는 클라이언트를 러시아 클러스터에두고 쿼리를 실행 한 결과, 약 20 밀리 초 지연이 추가되었습니다.

## 요약 {#summary} 

| 서버 | Q1 | Q2 | Q3 | Q4 | 
| -------- | ------- | ------- | ------ - | ------- | 
| 1 | 0.490 | 1.224 | 2.104 | 3.593 | 
| 3 | 0.212 | 0.438 | 0.733 | 1.241 | 
| 140 | 0.028 | 0.043 | 0.051 | 0.072 | 

[원래 기사 (https : //clickhouse.tech/docs/en/getting_started/example_datasets/nyc_taxi/) <! - hide ->
