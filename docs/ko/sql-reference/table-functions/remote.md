---
machine_translated : true
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd
toc_priority : 40
toc_title : "\ u30EA \ u30E2 \ u30FC \ u30C8"
---

# 원격 remoteSecure {# remote-remotesecure}

에 자유롭게 접근 할 수있는 원격 서버를 만들지 않고`Distributed` 테이블.

서명 :

```sql
remote ( 'addresses_expr', db, table [ 'user'[ 'password'])
remote ( 'addresses_expr', db.table [ 'user'[ 'password'])
remoteSecure ( 'addresses_expr', db, table [ 'user'[ 'password'])
remoteSecure ( 'addresses_expr', db.table [ 'user'[ 'password'])
```

`addresses_expr` - An expression that generates addresses of remote servers. This may be just one server address. The server address is`host : port` 또는 단순히`host` 호스트 서버 이름 또는 IPv4 또는 IPv6 주소로 지정할 수 있습니다. IPv6 주소는 대괄호로 지정합니다. 포트는 원격 서버의 TCP 포트입니다. 포트가 생략되면 다음과 같이됩니다`tcp_port` 서버의 설정 파일 (기본적으로 9000).

!!! important "중요"
    포트는 IPv6 주소에 필요합니다.

예 :

```text
example01-01-1
example01-01-1 : 9000
localhost
127.0.0.1
[::] : 9000
[2a02 : 6b8 : 0 : 1111 : 11 : 9000
```

여러 개의 주소는 쉼표로 구분합니다. 이 경우 ClickHouse 분산 처리를 사용하기 위해 지정된 모든 주소 (다른 데이터를 가진 샤드 등)에 쿼리를 보냅니다.

예 :

```text
example01-01-1, example01-02-1
```

식의 일부는 중괄호로 지정할 수 있습니다. 앞의 예는 다음과 같이 설명 될 수 있습니다 :

```text
example01-0 {1,2} -1
```

중괄호는 두 점 (음이 아닌 정수)로 구분 된 값의 범위를 포함 할 수 있습니다. 이 경우 범위는 샤드 주소를 생성하는 값 세트로 확장됩니다. 첫 번째 숫자가 제로로 시작하는 경우, 값은 같은 제로 배치에서 형성됩니다. 앞의 예는 다음과 같이 설명 될 수 있습니다 :

```text
example01- {01..02} -1
```

중괄호 쌍을 여러 개있는 경우 해당 집합의 직접 곱을 생성합니다.

괄호 안의 주소와 주소의 일부는 파이프 기호 (\ |)로 구분할 수 있습니다. 이 경우 해당 주소의 세트는 복제로 해석됩니다 쿼리는 첫 번째 정상적인 복제본으로 전송됩니다. 그러나 복제는 현재 설정되어있는 순서로 반복됩니다. [load_balancing (../../ operations / settings / settings.md) 설정.

예 :

```text
example01- {01..02} - {1 | 2}
```

이 예에서는 복제가 두 개있다 샤드를 지정합니다.

생성 된 주소의 수는 상수에 의해 제한됩니다. 지금 이것은 1000 주소입니다.

를 사용하여`remote` 테이블 함수는`Distributed`이 경우 서버 연결 요청에 대해 다시 설정되기 때문입니다. 또한 호스트 이름이 설정되어있는 경우, 이름은 해결 된 다양한 복제본에서 작업 할 때 오류가 포함되지 않습니다. 많은 쿼리를 처리하는 경우에는 항상`Distributed`을 사용하지 마십시오. `remote` 테이블 함수.

그`remote` 테이블 함수는 다음의 경우에 유용합니다 :

- 액세스 특정 서버에 대한 데이터와 비교, 디버깅, 테스트 실시를하고있었습니다.
- 연구 목적의 다양한 ClickHouse 클러스터 간의 쿼리.
- 수동으로 행해지는 빈도가 낮은 분산 요청합니다.
- 서버 세트가 매번 정의되는 분산 요청합니다.

사용자가 지정되어 있지 않은 경우,`default`가 사용된다.
암호를 지정하지 않으면 빈 암호가 사용됩니다.

`remoteSecure` - 마찬가지로`remote` but with secured connection. Default port - [tcp_port_secure (../../ operations / server-configuration-parameters / settings.md # server_configuration_parameters-tcp_port_secure) 설정 또는 9440에서.

원본 기사 (https://clickhouse.tech/docs/en/query_language/table_functions/remote/) <! - hide ->
