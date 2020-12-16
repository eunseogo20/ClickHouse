---
machine_translated : true
machine_translated_rev : ad252bbb4f7e2899c448eb42ecc39ff195c8faa1
toc_priority : 40
toc_title : "ANSI \ u306E \ u4E92 \ u63DB \ u6027"
---

# ClickHouse SQL 방언의 ANSI SQL 호환 {# ansi-sql-compatibility-of-clickhouse-sql-dialect}

!!! note "주"
    이 기사는 표 38에 의존하고 있습니다 "Feature taxonomy and definition for mandatory features", Annex F of ISO / IEC CD 9075-2 : 2013.

## 행동의 차이 {# differences-in-behaviour}

다음 표는 ClickHouse 쿼리 기능이 작동하지만 ANSI SQL에 지정된대로 작동하지 않는 경우를 보여줍니다.

| Feature ID | 기능 이름 | 차이 |
| ------------ | ---------------------------------- | - -------------------------------------------------- ------------------------------ |
| E011 | 숫자 데이터 형식 | 숫자 리터럴과 기간으로 해석되는 근사 (`Float64`) 대신 정확한 (`Decimal`) |
| E051-05 | 선택한 항목의 이름을 변경할 수 있습니다 | 항목의 이름 변경은 선택 결과 만보다 가시성의 범위가 넓어집니다 |
| E141-01 | NULL이 아닌 제약 |`NOT NULL` 묵시적위한 테이블 열을 기준으로 기본 |
| E011-04 | 산술 연산자 | ClickHouse 넘쳐 대신 다음의 연산 결과의 데이터 형식을 기반으로 사용자 정의 규칙 |

## 기능 상태 {# feature-status}

| Feature ID | 기능 이름 | 상태 | 의견 |
| ------------ | ------------------------------------ -------------------------------------------------- ------------- | ---------------------------- | ------- -------------------------------------------------- -------------------------------------------------- -------------------------------------------------- --------------------------- |
| ** E011 ** | ** 숫자 데이터 형식 ** | ** 부분적 ** {는 text-warning} | |
| E011-01 | 정수 및 SMALLINT 데이터 유형 | 예. {.text-success} | |
| E011-02 | 실수 배정 밀도 부동 소수점 데이터 유형 데이터 유형 | 부분적 {.text-warning} |`FLOAT (<binary_precision>)`,`REAL`와`DOUBLE PRECISION` 지원하지 않습니다 |
| E011-03 | DECIMAL과 NUMERIC 데이터 형 | 부분적 {.text-warning} | 만`DECIMAL (p, s)`지원합니다. `NUMERIC` |
| E011-04 | 산술 연산자 | 예. {.text-success} | |
| E011-05 | 수치 비교 | 예. {.text-success} | |
| E011-06 | 숫자 데이터 형식 간의 암시 적 캐스팅 | 아니오. {.text-danger} | ANSI SQL있는 모든 암시 적 숫자 형식 간의 캐스트가 ClickHouse에 의존하는 기능을 갖는 복수의 과부하 대신 암시 적 캐스팅 |
| ** E021 ** | ** 문자열 유형 ** | ** 부분적 ** {는 text-warning} | |
| E021-01 | 문자 데이터 형식 | 아니오. {.text-danger} | |
| E021-02 | 문자 변화 형 데이터 형 | 아니오. {.text-danger} |`String` 동작뿐만 아니라 길이 제한에 |
| E021-03 | 문자 리터럴 | 부분적 {.text-warning} | 연속 리터럴과 문자 세트의 자동 연결은 지원되지 않습니다 |
| E021-04 | CHARACTER_LENGTH 함수 | 부분적 {.text-warning} | 아니오. `USING` 절 |
| E021-05 | OCTET_LENGTH 함수 | 아니오. {.text-danger} |`LENGTH` 마찬가지로 작동합니다 |
| E021-06    | SUBSTRING                                                                                         | 部分的 {.text-warning}      | サポートなし `SIMILAR` と `ESCAPE` 句、ない `SUBSTRING_REGEX` バリアント                                                                                                               |
| E021-07    | 文字の連結                                                                                        | 部分的 {.text-warning}      | いいえ。 `COLLATE` 句                                                                                                                                                                  |
| E021-08 | 위와 기능 | 예. {.text-success} | |
| E021-09 | 트림 기능 | 예. {.text-success} | |
| E021-10    | 固定長および可変長文字ストリング型間の暗黙的なキャスト                                            | いいえ。 {.text-danger}     | ANSI SQLできる任意の暗黙の間のキャスト文字列の種類がClickHouseに依存しての機能を有する複数の過負荷の代わりに暗黙的なキャスト                                                           |
| E021-11    | 位置関数                                                                                          | 部分的 {.text-warning}      | サポートなし `IN` と `USING` 句、ない `POSITION_REGEX` バリアント                                                                                                                      |
| E021-12 | 문자의 비교 | 예. {.text-success} | |
| ** E031 ** | ** 식별자 ** | ** 부분적 ** {는 text-warning} | |
| E031-01 | 구분 식별자 | 부분적 {.text-warning} | Unicode 리터럴 지원은 제한된 |
| E031-02 | 문자 식별자 | 예. {.text-success} | |
| E031-03 | 끝의 밑줄 | 예. {.text-success} | |
| ** E051 ** | ** 기본적인 쿼리 사양 ** | ** 부분적 ** {는 text-warning} | |
| E051-01    | SELECT DISTINCT                                                                                   | はい。 {.text-success}      |                                                                                                                                                                                        |
| E051-02    | GROUP BY句                                                                                        | はい。 {.text-success}      |                                                                                                                                                                                        |
| E051-04 | 그룹에 의한 열을 포함 할 수 없다`<select list>`| 예. {.text-success} | |
| E051-05 | 선택한 항목의 이름을 변경할 수 있습니다 | 예. {.text-success} | |
| E051-06 | 어구를 가진 | 예. {.text-success} | |
| E051-07 | 선택 목록의 자격 \ * | 예. {.text-success} | |
| E051-08 | FROM 절에 상관 이름 | 예. {.text-success} | |
| E051-09 | FROM 절에 열 이름을 변경합니다 | 아니오. {.text-danger} | |
| ** E061 ** | ** 기본적인 조건과 검색 조건 ** | ** 부분적 ** {는 text-warning} | |
| E061-01 | 비교 조건 | 예. {.text-success} | |
| E061-02 | 술어 사이 | 부분적 {.text-warning} | 아니오. `SYMMETRIC`와`ASYMMETRIC` 절 |
| E061-03 | 값의 목록을 가지는 조건으로 | 예. {.text-success} | |
| E061-04 | 술어처럼 | 예. {.text-success} | |
| E061-05 | LIKE 술어 : 이스케이프 구 | 아니오. {.text-danger} | |
| E061-06 | Null 조건 | 예. {.text-success} | |
| E061-07 | 정량화 된 비교 조건 | 아니오. {.text-danger} | |
| E061-08 | 존재하는 조건 | 아니오. {.text-danger} | |
| E061-09 | 비교 술어의 하위 | 예. {.text-success} | |
| E061-11    | In述語のサブクエリ                                                                                | はい。 {.text-success}      |                                                                                                                                                                                        |
| E061-12    | 定量化された比較述語のサブクエリ                                                                  | いいえ。 {.text-danger}     |                                                                                                                                                                                        |
| E061-13 | 상관 하위 쿼리 | 아니오. {.text-danger} | |
| E061-14 | 검색 | 예. {.text-success} | |
| ** E071 ** | ** 기본적인 쿼리 식 ** | ** 부분적 ** {는 text-warning} | |
| E071-01 | UNION DISTINCT 테이블 연산자 | 아니오. {.text-danger} | |
| E071-02 | UNION ALL 테이블 연산자 | 예. {.text-success} | |
| E071-03 | DISTINCT 테이블 연산자를 제외 | 아니오. {.text-danger} | |
| E071-05    | 列の結合経由でテーブル事業者の必要のない全く同じデータ型                                          | はい。 {.text-success}      |                                                                                                                                                                                        |
| E071-06    | サブクエリ内のテーブル演算子                                                                      | はい。 {.text-success}      |                                                                                                                                                                                        |
| ** E081 ** | ** 기본 권한 ** | ** 부분적 ** {는 text-warning} | 진행중인 작업 |
| ** E091 ** | ** 함수의 설정 ** | ** 예. ** {는 text-success} | |
| E091-01 | AVG | 예. {.text-success} | |
| E091-02 | COUNT | 예. {.text-success} | |
| E091-03 | MAX | 예. {.text-success} | |
| E091-04 | MIN | 예. {.text-success} | |
| E091-05 | SUM | 예. {.text-success} | |
| E091-06 | 모든 수량 자 | 아니오. {.text-danger} | |
| E091-07 | 다른 수량 자 | 부분적 {.text-warning} | 집계 기능을 지원 |
| ** E101 ** | ** 기본 데이터 조작 ** | ** 부분적 ** {는 text-warning} | |
| E101-01 | INSERT 문 | 예. {.text-success} | 주 : ClickHouse의 기본 키는`UNIQUE` 제약 |
| E101-03 | 검색된 UPDATE 문 | 아니오. {.text-danger} | 거기에는`ALTER UPDATE` 배치 데이터 변경에 대한 지침 |
| E101-04 | 검색된 DELETE 문 | 아니오. {.text-danger} | 거기에는`ALTER DELETE` 배치 데이터 삭제를위한 지침 |
| ** E111 ** | ** 단일 행 SELECT 문 ** | ** 아니오. ** {는 text-danger} | |
| ** E121 ** | ** 기본적으로 커서를 지원 ** | ** 아니오. ** {는 text-danger} | |
| E121-01 | DECLARE CURSOR | 아니오. {.text-danger} | |
| E121-02 | ORDER BY 열을 선택 목록에 포함 할 필요가 없습니다 | 아니오. {.text-danger} | |
| E121-03 | ORDER BY 절의 값 식 | 아니오. {.text-danger} | |
| E121-04 | 열린 정책 | 아니오. {.text-danger} | |
| E121-06 | 위치 된 UPDATE 문 | 아니오. {.text-danger} | |
| E121-07    | 位置づけDELETEステートメント                                                                      | いいえ。 {.text-danger}     |                                                                                                                                                                                        |
| E121-08    | 閉じる文                                                                                          | いいえ。 {.text-danger}     |                                                                                                                                                                                        |
| E121-10 | FETCH 문 : 암시 적 NEXT | 아니오. {.text-danger} | |
| E121-17 | 홀드 커서 첨부 | 아니오. {.text-danger} | |
| ** E131 ** | ** Null 값 지원 (값 대신 null) ** | ** 부분적 ** {는 text-warning} | 일부 제한이 적용됩니다 |
| ** E141 ** | ** 기본적인 무결성 제약 ** | ** 부분적 ** {는 text-warning} | |
| E141-01    | NULLでない制約                                                                                    | はい。 {.text-success}      | 注: `NOT NULL` は黙示のためのテーブル列によるデフォルト                                                                                                                                |
| E141-02    | NULLでない列の一意制約                                                                            | いいえ。 {.text-danger}     |                                                                                                                                                                                        |
| E141-03 | 기본 키 제약 조건 | 아니오. {.text-danger} | |
| E141-04 | 참조 삭제 조치 및 참조 update 작업 모두에 대한 NO ACTION의 기본값을 가진 기본 외래 키 제약 조건 | 아니오. {.text-danger} | |
| E141-06 | 제약의 체크 | 예. {.text-success} | |
| E141-07 | 열의 기본값 | 예. {.text-success} | |
| E141-08 | 기본 키에서 유추 NULL이 아닙니다 | 예. {.text-success} | |
| E141-10 | 외래 키 이름은 임의의 순서로 지정할 수 있습니다 | 아니오. {.text-danger} | |
| **E151**   | **取引サポート**                                                                                  | **いいえ。**{.text-danger} |                                                                                                                                                                                        |
| E151-01    | COMMIT文                                                                                          | いいえ。 {.text-danger}     |                                                                                                                                                                                        |
| E151-02 | ROLLBACK 문 | 아니오. {.text-danger} | |
| ** E152 ** | ** 기본 세트 명세서 ** | ** 아니오. ** {는 text-danger} | |
| E152-01 | SET TRANSACTION 문 : 분리 레벨 SERIALIZABLE 절 | 아니오. {.text-danger} | |
| E152-02 | SET TRANSACTION 문 : READ ONLY 절과 READ WRITE 절 | 아니오. {.text-danger} | |
| ** E153 ** | ** 하위 쿼리를 사용하여 업데이트 가능한 쿼리 ** | ** 아니오. ** {는 text-danger} | |
| ** E161 ** | ** 선두의 double minus을 사용하여 SQL 코멘트 ** | ** 예. ** {는 text-success} | |
| ** E171 ** | ** SQLSTATE 지원 ** | ** 아니오. ** {는 text-danger} | |
| ** E182 ** | ** 호스트 언어 바인딩 ** | ** 아니오. ** {는 text-danger} | |
| ** F031 ** | ** 기본 스키마 작업 ** | ** 부분적 ** {는 text-warning} | |
| F031-01 | 영구적 기반 테이블을 만드는 CREATE TABLE 문 | 부분적 {.text-warning} | 아니오. `SYSTEM VERSIONING``ON COMMIT``GLOBAL``LOCAL``PRESERVE``DELETE``REF IS``WITH OPTIONS``UNDER``LIKE``PERIOD FOR` 절 및 사용자 해결 데이터 형의 지원없이 |
| F031-02 | CREATE VIEW 문 | 부분적 {.text-warning} | 아니오. `RECURSIVE``CHECK``UNDER``WITH OPTIONS` 어구 및 사용자 해결 데이터 형의 지원없이 |
| F031-03 | 그랜트 정책 | 예. {.text-success} | |
| F031-04 | ALTER TABLE 문 : ADD COLUMN 절 | 부분적 {.text-warning} | 지원없이`GENERATED` 절 및 시스템 기간 |
| F031-13 | DROP TABLE 문 : RESTRICT 절 | 아니오. {.text-danger} | |
| F031-16 | DROP VIEW 문 : RESTRICT 절 | 아니오. {.text-danger} | |
| F031-19 | REVOKE 문 : RESTRICT 절 | 아니오. {.text-danger} | |
| ** F041 ** | ** 기본적인 결합 테이블 ** | ** 부분적 ** {는 text-warning} | |
| F041-01 | Inner join 반드시하는 것은 아니지만, 내부 키워드) | 예. {.text-success} | |
| F041-02 | 내부 키워드 | 예. {.text-success} | |
| F041-03 | LEFT OUTER JOIN | 예. {.text-success} | |
| F041-04 | RIGHT OUTER JOIN | 예. {.text-success} | |
| F041-05 | 외부 조인은 중첩 될 수 있습니다 | 예. {.text-success} | |
| F041-07 | 왼쪽 외부 조인 또는 오른쪽 외부 조인의 내부 테이블은 내부 조인에서도 사용할 수 있습니다 | 예. {.text-success} | |
| F041-08 | 모든 비교 연산자가 지원) | 아니오. {.text-danger} | |
| ** F051 ** | ** 기본 시간 ** | ** 부분적 ** {는 text-warning} | |
| F051-01 | 날짜 데이터 형식 (날짜 리터럴 지원 포함) | 부분적 {.text-warning} | 리터럴 없음 |
| F051-02 | 초 소수 정밀도가 0 이상의 시간 데이터 형식 (시간 리터럴 지원 포함) | 아니오. {.text-danger} | |
| F051-03 | 타임 스탬프 데이터 형식이 포함 된 지원의 타임 스탬프 문자와 소수점 이하의 초의 정밀도로 적어도 0-6 | 아니오. {.text-danger} |`DateTime64` time 비슷한 기능을 제공합니다 |
| F051-04 | 날짜, 시간 및 타임 스탬프 데이터 형식의 비교 조건 | 부분적 {.text-warning} | 사용 가능한 데이터 형식은 하나뿐입니다 |
| F051-05 | Datetime 형식과 문자열 사이의 명시 적 캐스팅 | 그렇습니다. {.text-success} | |
| F051-06 | CURRENT_DATE | 아니오. {.text-danger} |`today ()`비슷합니다 |
| F051-07 | LOCALTIME | 아니오. {.text-danger} |`now ()`비슷합니다 |
| F051-08 | LOCALTIMESTAMP | 아니오. {.text-danger} | |
| ** F081 ** | ** 뷰의 조합 및 제외 ** | ** 부분적 ** {는 text-warning} | |
| ** F131 ** | ** 그룹화 작업 ** | ** 부분적 ** {는 text-warning} | |
| F131-01 | 여기에서 그룹은 조항 대응하여 쿼리를 처리하는 클라우드의 경우 로그 전망 | 예. {.text-success} | |
| F131-02 | 그룹화 된 뷰 | 예. {.text-success} | |
| F131-03 | 세트 기능을 지원하여 쿼리를 처리하는 클라우드의 경우 로그 전망 | 예. {.text-success} | |
| F131-04 | GROUP BY 절과 HAVING 절 및 그룹화 뷰를 서브 쿼리 | 예. {.text-success} | |
| F131-05 | GROUP BY 절과 HAVING 절 및 그룹화 뷰를 사용하여 단일 행 선택 | 아니오. {.text-danger} | |
| ** F181 ** | ** 여러 모듈 지원 ** | ** 아니오. ** {는 text-danger} | |
| ** F201 ** | ** 캐스트 기능 ** | ** 예. ** {는 text-success} | |
| ** F221 ** | ** 명시 적 기본값 ** | ** 아니오. ** {는 text-danger} | |
| ** F261 ** | ** 대소 문자 식 ** | ** 예. ** {는 text-success} | |
| F261-01 | 간단한 경우 | 예. {.text-success} | |
| F261-02 | 검색 케이스 | 예. {.text-success} | |
| F261-03 | NULLIF | 예. {.text-success} | |
| F261-04    | COALESCE                                                                                          | はい。 {.text-success}      |                                                                                                                                                                                        |
| **F311**   | **スキーマ定義文**                                                                                | **部分的**{.text-warning}  |                                                                                                                                                                                        |
| F311-01 | CREATE SCHEMA | 아니오. {.text-danger} | |
| F311-02 | 영구적 기본 테이블의 테이블 만들기 | 예. {.text-success} | |
| F311-03    | CREATE VIEW                                                                                       | はい。 {.text-success}      |                                                                                                                                                                                        |
| F311-04    | CREATE VIEW: WITH CHECK OPTION                                                                    | いいえ。 {.text-danger}     |                                                                                                                                                                                        |
| F311-05 | 그랜트 정책 | 예. {.text-success} | |
| ** F471 ** | ** 스칼라 서브 쿼리 값 ** | ** 예. ** {는 text-success} | |
| ** F481 ** | ** 전개 된 NULL 술어 ** | ** 예. ** {는 text-success} | |
| ** F812 ** | ** 기본 플래그 설정 ** | ** 아니오. ** {는 text-danger} | |
| **T321**   | **基本的なSQL呼び出しルーチン**                                                                   | **いいえ。**{.text-danger} |                                                                                                                                                                                        |
| T321-01    | オーバーロードのないユーザー定義関数                                                              | いいえ。 {.text-danger}     |                                                                                                                                                                                        |
| T321-02 | 과부하되지 않은 사용자 정의 저장 프로 시저 | 아니오. {.text-danger} | |
| T321-03 | 함수 호출 | 아니오. {.text-danger} | |
| T321-04 | CALL 문 | 아니오. {.text-danger} | |
| T321-05 | RETURN 문 | 아니오. {.text-danger} | |
| ** T631 ** | ** 하나의리스트 요소를 가지는 조건에서 ** | ** 예. ** {는 text-success} | |
