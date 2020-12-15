---
machine_translated : true
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd
toc_priority : 39
toc_title : "\ u30BB \ u30C3 \ u30C8"
---

# 세트 {#set}

항상 RAM에있는 데이터 - 세트. 이것은 in 연산자의 오른쪽에서의 사용을 의도하고 있습니다 (섹션을 참조하십시오 "IN operators").

INSERT를 사용하면 테이블에 데이터를 삽입 할 수 있습니다. 새로운 요소가 데이터 세트에 추가 된 중복은 무시됩니다.
수없는 줄을 선택합니다. 데이터를 얻을 수있는 유일한 방법은 in 연산자의 오른쪽 절반에서 데이터를 사용하는 것입니다.

데이터는 항상 RAM에 있습니다. INSERT에서 삽입 된 데이터의 블록도 디스크 테이블의 디렉토리에 기록됩니다. 서버를 시작하면이 데이터는 RAM에로드됩니다. 즉, 다시 시작되면 데이터는 제자리에 남아 있습니다.

라사바를 다시 시작 블록의 데이터 디스크가 손실 될 수도 예상됩니다. 후자의 경우 손상된 데이터가 포함 된 파일을 수동으로 삭제해야하는 경우가 있습니다.

원본 기사 (https://clickhouse.tech/docs/en/operations/table_engines/set/) <! - hide ->
