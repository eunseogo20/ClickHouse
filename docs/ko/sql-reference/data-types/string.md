---
machine_translated : true
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd
toc_priority : 44
toc_title : "\ u6587 \ u5B57 \ u5217"
---

# 문자열 {#string}

임의의 길이의 문자열입니다. 길이는 제한되지 않는다. 값은 null 바이트를 포함한 모든 바이트 세트를 포함 할 수 있습니다.
문자열은 다른 Dbms의 VARCHAR 형태, BLOB 형태, CLOB 형 등을 바꿉니다.

## 인코딩 {#encodings}

ClickHouse는 인코딩의 개념은 없습니다. 문자열은 임의의 바이트 집합을 포함 할 수 있습니다.
이 필요한 경우는 점포 텍스트의 사용을 권장합니다 UTF-8 인코딩입니다. 적어도 터미널이 UTF-8 (권장)를 사용하는 경우는 변환하지 않고 값을 읽고 쓸 수 있습니다.
마찬가지로 문자열을 조작하기위한 특정 함수는 UTF-8로 인코딩 된 텍스트를 나타내는 바이트 세트가 문자열에 포함되어 있다는 전제하에
예를 들어, 'length'함수는 문자열의 길이를 바이트 단위로 계산합니다. 'lengthUTF8'함수는 값이 UTF-8로 인코딩되어 있다고 가정하여 Unicode 코드 포인트에서 문자열의 길이를 계산합니다.

원본 기사 (https://clickhouse.tech/docs/en/data_types/string/) <! - hide ->
