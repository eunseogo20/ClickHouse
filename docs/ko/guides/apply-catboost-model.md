--- 
machine_translated : true 
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd 
toc_priority : 41 
toc_title : "CatBoost \ u30E2 \ u30C7 \ u30EB \ u306E \ u9069 \ u7528" 
--- 

# ClickHouse에서 Catboost 모델의 적용 {# applying-catboost-model-in- clickhouse} 

[CatBoost (https://catboost.ai)에서 개발 된 무료 오픈 소스 경사 부스트 라이브러리입니다 [Yandex (https://yandex.com/company/) 기계 학습을 위해. 

이 단계에서는 SQL에서 모델 추론을 실행하여 ClickHouse 사전에 훈련 된 모델을 적용하는 방법을 학습합니다. 

ClickHouse에서 CatBoost 모델을 적용하려면 : 

1. 테이블 만들기 (# create-table) 
2. 테이블에 데이터를 삽입합니다 (# insert-data-to-table) 
3. ClickHouse 에 catBoost을 통합 (# integrate-catboost-into-clickhouse) (모든 단계). 
4. SQL에서 모델 추론을 수행하는 (# run-model-inference).

CatBoost 모델의 교육에 대한 자세한 내용은 모형의 학습과 적용 (https://catboost.ai/docs/features/training.html#training) 

## 전제 조건 {#prerequisites} 

당신이 가지고 있지 않은 경우 [독 노동자 (https://docs.docker.com/install/) 아직 그것을 설치하십시오. 

!!! note "주" 
    [독 노동자 (https://www.docker.com) CatBoost과 ClickHouse 설치를 시스템의 나머지 부분에서 분리하는 컨테이너를 만들 수있는 소프트웨어 플랫폼입니다. 

CatBoost 모델을 적용하기 전에 : 

** 1 ** 풀 [독 노동자 이미지 (https://hub.docker.com/r/yandex/tutorial-catboost-clickhouse) 레지스트리에서 : 

```bash 
$ docker pull yandex / tutorial-catboost-clickhouse 
``` 

이 Docker 이미지는 catBoost과 ClickHouse을 실행하는 데 필요한 코드 런타임 라이브러리 환경 변수 설정 파일이 포함되어 있습니다. 

** 2 ** Docker 이미지가 성공적으로 끌어온 확인합니다 : 

```bash 
$ docker image ls
REPOSITORY TAG IMAGE ID CREATED SIZE 
yandex / tutorial-catboost-clickhouse latest 622e4d17945b 22 hours ago 1.37GB 
``` 

** 3 ** 기 Docker 컨테이너에 기반이 이미지 : 

```bash 
$ docker run -it -p 8888 : 8888 yandex / tutorial-catboost-clickhouse 
``` 

## 1. 테이블 만들기 {# create-table} 

교육 샘플 ClickHouse 테이블을 만들려면 : 

** 1 ** 대화 형 모드에서 ClickHouse console 클라이언트를 시작 : 

```bash 
$ clickhouse client 
``` 

!!! note "주" 
    ClickHouse 서버는 Docker 컨테이너에서 이미 실행되고 있습니다. 

** 2 ** 명령을 사용하여 테이블을 만듭니다 : 

```sql 
:) CREATE TABLE amazon_train 
(
    date Date MATERIALIZED today (), 
    ACTION UInt8, 
    RESOURCE UInt32, 
    MGR_ID UInt32, 
    ROLE_ROLLUP_1 UInt32, 
    ROLE_ROLLUP_2 UInt32, 
    ROLE_DEPTNAME UInt32, 
    ROLE_TITLE UInt32, 
    ROLE_FAMILY_DESC UInt32, 
    ROLE_FAMILY UInt32, 
    ROLE_CODE UInt32 
) 
ENGINE = MergeTree ORDER BY date 
``` 

** 3. ** ClickHouse console 클라이언트에서 종료 : 

```sql 
:) exit 
``` 

## 2. 테이블에 데이터를 삽입합니다 {# insert-data-to-table} 

데이터를 삽입하려면 : 

** 1 ** 다음 명령을 실행합니다 : 

```bash 
$ clickhouse client --host 127.0.0.1 --query 'INSERT INTO amazon_train FORMAT CSVWithNames'<~ / amazon / train.csv 
```
 
** 2 ** 대화 형 모드에서 ClickHouse console 클라이언트를 시작하기 : 

```bash 
$ clickhouse client 
``` 

** 3 ** 데이터가 업로드 된 것을 확인 : 

```sql 
:) SELECT count () FROM amazon_train 

SELECT count () 
FROM amazon_train 

+ -count () - + 
| 65538 | 
+ --- ---- + 
``` 

## 3. clickHouse에 catBoost를 통합하는 {# integrate-catboost-into-clickhouse} 

!!! note "주" 
    ** 어떤 단계. ** Docker 이미지는 CatBoost과 ClickHouse을 실행하는 데 필요한 모든 것이 포함되어 있습니다. 

Clickhouse에 CatBoost을 통합하려면 : 

** 1 ** 평가 라이브러리를 구축합니다.

CatBoost 모델을 평가하는 가장 빠른 방법은 컴파일입니다`libcatboostmodel <so | dll | dylib>`도서관에 대한 자세한 내용은 도서관을 찾아보기 CatBoost 문서 (https://catboost.ai/docs/concepts/c- plus-plus-api_dynamic-c-pluplus-wrapper.html) 

** 2 ** 새로운 디렉토리를 원하는 위치에 생성하고 임의의 이름으로 만듭니다. `data` 만든 라이브러리를 그 안에 넣습니다. 의 Docker 이미지가 이미 포함되어있는 도서관`data / libcatboostmodel.so`. 

** 3 ** Config model 새로운 디렉토리를 원하는 위치에 원하는 이름으로 만듭니다. `models`. 

** 4 ** 어떤 이름의 모델 구성 파일을 만듭니다. `models / amazon_model.xml`. 

** 5 ** 모델 구성에 대한 설명 : 

```xml 
<models> 
    <model> 
        <! - Model type. Now catboost only -> 
        <type> catboost </ type> 
        <! - model name -> 
        <name> amazon </ name> 
        <! - Path to trained model ->
        <path> /home/catboost/tutorial/catboost_model.bin </ path> 
        <! - Update interval -> 
        <lifetime> 0 </ lifetime> 
    </ model> 
</ models> 
``` 
    modelEvaluate ( 'amazon'
                RESOURCE, 
                MGR_ID,


```xml 
<! - File etc / clickhouse-server / config.d / models_config.xml -> 
<catboost_dynamic_library_path> /home/catboost/data/libcatboostmodel.so </ catboost_dynamic_library_path> 
<models_config> / home / catboost / models / * _ model.xml </ models_config> 
``` 

## 4. SQL에서 모델 추론을 수행하는 {# run-model-inference} 

시험 모델의 clickHouse 트`$ clickhouse client`. 

모델이 작동하는 확인합시다 : 

```sql 
:) SELECT 
                ROLE_CODE)> 0 AS prediction, 
    ACTION AS target
                ROLE_ROLLUP_1, 
                ROLE_ROLLUP_2, 
                ROLE_DEPTNAME, 
                ROLE_TITLE, 
                ROLE_FAMILY_DESC, 
                ROLE_FAMILY, 
FROM amazon_train 
LIMIT 10 
``` 

!!! note "주" 
    함수 모델 평가 (../ sql-reference / functions / other-functions.md # function-modelevaluate ) 멀티 클래스 모델 클래스 당 원시 예측을 가지는 튜플을 반환합니다. 

은 확률을 예측하자 : 

```sql 
:) SELECT 
    modelEvaluate ( 'amazon', 
                RESOURCE, 
                MGR_ID, 
                ROLE_ROLLUP_1, 
                ROLE_ROLLUP_2, 
                ROLE_DEPTNAME, 
                ROLE_TITLE,
                ROLE_FAMILY_DESC, 
                ROLE_FAMILY, 
                ROLE_CODE) AS prediction,  
    1 / ( 1 + exp (-prediction)) AS probability, 
    ACTION AS target
FROM amazon_train 
LIMIT 10 
``` 

!!! note "주" 
    자세한 내용 [exp ()] (../ sql-reference / functions / math-functions.md) 기능. 

샘플 LogLoss을 계산합시다 : 

```sql 
:) SELECT -avg (tg * log (prob) + (1 - tg) * log (1 - prob)) AS logloss 
FROM 
( 
    SELECT 
        modelEvaluate ( 'amazon' 
                    RESOURCE , 
                    MGR_ID, 
                    ROLE_ROLLUP_1, 
                    ROLE_ROLLUP_2, 
                    ROLE_DEPTNAME, 
                    ROLE_TITLE, 
                    ROLE_FAMILY_DESC, 
                    ROLE_FAMILY, 
                    ROLE_CODE) AS prediction,
        1. / (1 + exp (-prediction)) AS prob, 
        ACTION AS tg 
    FROM amazon_train 
) 
``` 

!!! note "주" 
    자세한 내용 [avg ()] (../ sql-reference / aggregate-functions /reference.md#agg_function-avg)과 로그 ()] (../ sql-reference / functions / math-functions.md) 기능. 

원본 기사 (https://clickhouse.tech/docs/en/guides/apply_catboost_model/) <! - hide ->
