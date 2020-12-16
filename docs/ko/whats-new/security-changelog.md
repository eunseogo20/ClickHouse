--- 
machine_translated : true 
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd 
toc_priority : 76 
toc_title : "\uBCF4\uC548 \uBCC0\uACBD \uB85C\uADF8" 
--- 

## ClickHouse 출시 19.14.3.3,2019-09 -10에서 수정 {# fixed-in-clickhouse-release-19-14-3-3-2019-09-10} 

### CVE-2019-15024 {# cve-2019-15024} 

Аn attacker that has write access to ZooKeeper and who ican run a custom server available from the network where ClickHouse runs , can create a custom-built malicious server that will act as a ClickHouse replica and register it in ZooKeeper . When another replica will fetch data part from the malicious replica , it can force clickhouse-server to write to arbitrary path on filesystem.

신용 : Yandex 정보 보안 팀의 Eldar Zaitov 

### CVE-2019-16535 {# cve-2019-16535} 

Аn OOB read, OOB write and integer underflow in decompression algorithms can be used to achieve RCE or DoS via native protocol . 

신용 : Yandex 정보 보안 팀의 Eldar Zaitov 

### CVE-2019-16536 {# cve-2019-16536} 

스택 오버플로 DoS 따라 실행시킬 수 있으며, 악성 인증 클라이언트 

신용 : Yandex 정보 보안 팀의 Eldar Zaitov 

## clickHouse 출시 19.13.6.1,2019-09-20 수정 {# fixed-in-clickhouse-release-19-13-6-1-2019-09-20} 

### CVE-2019- 18657 {# cve-2019-18657} 

테이블 함수`url`이 취약점으로 인해 공격자는 요구에 임의의 HTTP 헤더를 삽입 할 수있었습니다. 

신용 : [Nikita Tikhomirov](https://github.com/NSTikhomirov) 

## ClickHouse 출시 18.12.13,2018-09-10 수정되었습니다 {# fixed-in-clickhouse-release-18-12- 13-2018-09-10}

### CVE-2018-14672 {# cve-2018-14672} 

기능 탑재 CatBoost 모델의 예로 포커스 traversal 독서 모든 파일을 오류 메시지가 반환됩니다. 

신용 : Yandex 정보 보안 팀의 안드레이 Krasichkov 

## ClickHouse 출시 18.10.3,2018-08-13 수정되었습니다 {# fixed-in-clickhouse-release-18-10-3-2018-08-13} 

### CVE-2018-14671 {# cve-2018-14671} 

unixODBC 허용 하중 모든 공유 객체의 파일 시스템에서 원격 코드 실행 취약점이 존재합니다. 

신용 : Yandex 정보 보안 팀의 Andrey Krasichkov과 Evgeny Sidorov 

## ClickHouse 출시 1.1.54388,2018-06-28 수정 {# fixed-in-clickhouse-release-1-1-54388-2018-06-28} 

# ## CVE-2018-14668 {# cve-2018-14668} 

"remote"테이블 함수에서 임의의 기호 허용 "user", "password"와 "default_database"분야를 횡단 프로토콜 요청 위조 공격이었다. 

신용 : Yandex 정보 보안 팀의 안드레이 Krasichkov

## ClickHouse 출시 1.1.54390,2018-07-06 수정 {# fixed-in-clickhouse-release-1-1-54390-2018-07-06} 

### CVE-2018-14669 {# cve-2018 -14669} 

ClickHouse MySQL 클라이언트가 있었다 "LOAD DATA LOCAL INFILE"기능이 활성화되면 악성 MySQL 데이터베이스 읽기 임의의 파일 연결 ClickHouse 서버입니다. 

신용 : Yandex 정보 보안 팀의 Andrey Krasichkov과 Evgeny Sidorov 

## ClickHouse 출시 1.1.54131,2017-01-10 수정 {# fixed-in-clickhouse-release-1-1-54131-2017-01-10} 

# ## CVE-2018-14670 {# cve-2018-14670} 

Deb 패키지에 잘못된 구성은 데이터베이스의 남용으로 이어질 수 있습니다. 

신용 : 영국 국립 사이버 보안 센터 (NCSC) 

{## [원본 기사](https://clickhouse.tech/docs/en/security_changelog/) ##}
