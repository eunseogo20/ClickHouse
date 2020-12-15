---
machine_translated : true
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd
toc_priority : 47
toc_title : "\ u30AF \ u30EA \ u30C3 \ u30AF \ u30CF \ u30A6 \ u30B9 \ u306E \ u66F4 \ u65B0"
---

# 클릭 하우스 업데이트 {# clickhouse-update}

ClickHouse가 deb 패키지에서 설치가 :

```bash
$ sudo apt-get update
$ sudo apt-get install clickhouse-client clickhouse-server
$ sudo service clickhouse-server restart
```

권장 deb 패키지 이외의 것을 사용하여 ClickHouse를 설치 한 경우, 적절한 업데이트 방법을 사용합니다.

ClickHouse 분산 update를 지원하지 않습니다. 작업은 개별 서버마다 연속적으로 실행해야합니다. 클러스터의 모든 서버를 동시에 업데이트하지 마십시오.
