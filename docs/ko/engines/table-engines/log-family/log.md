---
machine_translated : true
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd
toc_priority : 33
toc_title : "\ u30ED \ u30B0"
---

# 로그 {#log}

엔진 로그 엔진 계열에 속한다. 로그 엔진의 일반적인 속성과 그 차이를 참조하십시오 로그 엔진 가족 (index.md) 기사이다

로그와는 다릅니다 [TinyLog (tinylog.md) 그 중 작은 파일의 "marks"열 파일에 존재합니다. 이 마크는 모든 데이터 블록에 기록되고 지정된 행수를 생략하기 위해 파일의 읽기를 시작할 위치를 나타내는 오프셋이 읽기를 가능하게하는 기능이 테이블 데이터를 여러 스레드).
동시 데이터 액세스의 경우 읽기 작업은 동시에 실행할 수 있지만 쓰기 작업이 읽기를 차단합니다.
로구엔지 마찬가지로 테이블에 쓰기가 실패 할 경우, 테이블은 손상 거기에서로드 할 때 오류가 반환됩니다. 로그 엔진은 임시 데이터 쓰기 번 테이블 및 테스트 또는 데모 목적에 적합합니다.

원본 기사 (https://clickhouse.tech/docs/en/operations/table_engines/log/) <! - hide ->
