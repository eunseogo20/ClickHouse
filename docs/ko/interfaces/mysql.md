--- 
machine_translated : true 
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd 
toc_priority : 20 
toc_title : "MySQL \ u30A4 \ u30F3 \ u30BF" 
--- 

# MySQL 인터페이스 {# mysql-interface} 

ClickHouse는 MySQL wire protocol을 지원하고 있습니다. 에서 활성화 할 수있는 [mysql_port] (../ operations / server-configuration-parameters / settings.md # server_configuration_parameters-mysql_port) 설정 파일에서 설정 : 

```xml 
<mysql_port> 9004 </ mysql_port> 
`` ` 

명령 줄 도구를 사용하여 연결 예`mysql` : 

```bash 
$ mysql --protocol tcp -u default -P 9004 
``` 

연결이 성공하면 출력 : 

```text 
Welcome to the MySQL monitor. Commands end with; or \ g.  
Your MySQL connection id is 4
Server version : 20.2.1.1-ClickHouse

Copyright (c) 2000, 2019, Oracle and / or its affiliates. All rights reserved. 

Oracle is a registered trademark of Oracle Corporation and / or its 
affiliates. Other names may be trademarks of their respective 
owners. 

Type 'help;'or ' \ h 'for help. Type'\ c 'to clear the current input statement. 

mysql> 
``` 

와의 호환성을 유지하기 위해 모든 MySQL 고객 추천으로 지정 사용자 암호 [더블 SHA1 (. ./operations/settings/settings-users.md#password_double_sha1_hex) 설정 파일. 
경우 사용자의 암호가 지정 [SHA256] (../ operations / settings / settings-users.md # password_sha256_hex) 일부 클라이언트는 인증 할 수 없습니다 (mysqljs 및 이전 버전의 명령 줄 도구 mysql). 

제한 : 

- 작성 문의에는 대응하지 않습니다 

- 일부 데이터 형식은 문자열로 전송됩니다

원본 기사 (https://clickhouse.tech/docs/en/interfaces/mysql/) <! - hide ->
