---
machine_translated : true
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd
toc_priority : 29
toc_title : "\ u30D7 \ u30ED \ u30AD \ u30B7"
---

# 외부 공급 {# proxy-servers-from-third-party-developers}

## chproxy {#chproxy}

[chproxy (https://github.com/Vertamedia/chproxy)는 ClickHouse 데이터베이스에 대한 HTTP 프록시와로드 밸런서입니다.

특징 :

- 사용자 당 루티
- 가동 가능한 한계.
- SSL 인증서의 자동 renewal.

Go 구현.

## 킷텐하우스 {#kittenhouse}

[킷텐하우스 (https://github.com/VKCOM/kittenhouse)하는 것이 중요하다 현지 대리인과 ClickHouse 응용 프로그램 서버의 경우에도 버퍼에 삽입 데이터와 고객 측입니다.

특징 :

- 메모리 및 디스크의 데이터 버퍼링.
- 테이블 단위의 라우팅.
- 부하 분산 및 상태 확인.

Go 구현.

## 클릭 하우스 - 대량 {# clickhouse-bulk}

[클릭 하우스 - 벌크 (https://github.com/nikepan/clickhouse-bulk) 간단한 ClickHouse 삽입 수집가입니다.

특징 :

- 그룹의 요구 송신에 의한 임계 값 또는 간격으로 나와 있습니다.
- 여러 원격 서버.
- 기본 인증.

Go 구현.

원본 기사 (https://clickhouse.tech/docs/en/interfaces/third-party/proxy/) <! - hide ->
