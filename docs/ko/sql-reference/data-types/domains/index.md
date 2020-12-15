---
machine_translated : true
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd
toc_folder_title : "\ u30C9 \ u30E1 \ u30A4 \ u30F3"
toc_priority : 56
toc_title : "\ u6982 \ u8981"
---

# 도메인 {#domains}

도메인은 기존의 기본형에 몇 가지 추가 기능을 추가하는 특수 목적의 형태이지만, 기본 데이터 형의 온와이야 및 온 디스크 형식은 현재 ClickHouse는 사용자 정의 도메인을 지원 하지 않습니다.

예를 들어 해당 기본 유형을 사용할 수있는 임의의 장소에서 도메인을 사용할 수 있습니다 :

- 도메인 형식의 열을 만들
- 도메인 열에서 / 읽기 / 쓰기 값
- 기본형을 인덱스로 사용할 수있는 경우는 인덱스로 사용합니다
- 도메인 열의 값을 가지는 함수의 호출

### 도메인의 추가 기능 {# extra-features-of-domains}

- 명시적인 열 유형 이름`SHOW CREATE TABLE` 또는`DESCRIBE TABLE`
- 인간 친화적 인 형식에서 입력`INSERT INTO domain_table (domain_column) VALUES (...)`
- 인간 친화적 인 형식으로 출력`SELECT domain_column FROM domain_table`
- 인간 친화적 인 형식으로 외부 소스에서 데이터를로드 :`INSERT INTO domain_table FORMAT CSV ...`

### 제한 {#limitations}

- 기본형 인덱스 열을 도메인 형식으로 변환 할 수 없습니다`ALTER TABLE`.
- 다른 열 또는 테이블에서 데이터를 삽입 할 때 문자열 값을 암시 적으로 도메인 값으로 변환 할 수 없습니다.
- 도메인은 저장된 값에 제약 조건을 추가하지 않습니다.

원본 기사 (https://clickhouse.tech/docs/en/data_types/domains/overview) <! - hide ->
