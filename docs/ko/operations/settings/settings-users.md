---
machine_translated : true
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd
toc_priority : 63
toc_title : "\ u30E6 \ u30FC \ u30B6 \ u30FC \ u8A2D \ u5B9A"
---

# 사용자 설정 {# user-settings}

그`users` 섹션`user.xml` 설정 파일에 사용자를 설정합니다.

!!! note "정보"
    ClickHouse 또한 지원합니다 [SQL 기반 워크 플로 (../ access-rights.md # access-control) 사용자를 관리하기 위하여. 권장합니다.

구조`users` 섹션 :

```xml
<users>
    <! - If user name was not specified 'default'user is used ->
    <user_name>
        <password> </ password>
        <! - Or ->
        <password_sha256_hex> </ password_sha256_hex>

        <access_management> 0 | 1 </ access_management>

        <networks incl = "networks"replace = "replace">
        </ networks>

        <profile> profile_name </ profile>

        <quota> default </ quota>

        <databases>
            <database_name>
                <table_name>
                    <filter> expression </ filter>
                <table_name>
            </ database_name>
        </ databases>
    </ user_name>
    <! - Other users settings ->
</ users>
```

### user_name / 비밀번호 {# user-namepassword}

암호는 평문 또는 SHA256 (hex 형식)으로 지정할 수 있습니다.

- 일반 텍스트로 암호를 할당하려면 (** 권장되지 않는다 **) 그것을 a 놓습니다`password` 요소.

    예를 들어,`<password> qwerty </ password>`암호는 비워 둘 수 있습니다.

<a id="password_sha256_hex"> </a>

- SHA256 해시를 사용하여 암호를 할당하려면 비밀번호를`password_sha256_hex` 요소.

    예를 들어,`<password_sha256_hex> 65e84be33532fb784c48129675f9eff3a682b27168c0ea744b2cf58ee02337c5 </ password_sha256_hex>`.

    쉘에서 암호를 생성하는 방법의 예 :

          PASSWORD = $ (base64 </ dev / urandom | head -c8); echo "$ PASSWORD"; echo -n "$ PASSWORD"| sha256sum | tr -d '-'

    결과의 첫 번째 줄은 암호입니다. 두 번째 행은 해당 SHA256 해시입니다.

<a id="password_double_sha1_hex"> </a>

- MySQL 클라이언트와의 호환성을 위해 암호는 이중 SHA1 해시로 지정할 수 있습니다. 그것을 두는`password_double_sha1_hex` 요소.

    예를 들어,`<password_double_sha1_hex> 08b4a0f1de6ad37da17359e592c8d74788a83eb0 </ password_double_sha1_hex>`.

    쉘에서 암호를 생성하는 방법의 예 :

          PASSWORD = $ (base64 </ dev / urandom | head -c8); echo "$ PASSWORD"; echo -n "$ PASSWORD"| sha1sum | tr -d '-'| xxd -r -p | sha1sum | tr -d '-'

    결과의 첫 번째 줄은 암호입니다. 두 번째 행은 해당 이중 SHA1 해시입니다.

### access_management {# access_management-user-setting}

이 설정은 SQL 기반의 사용을 해제 할 수 있습니다 액세스 제어 및 계정 관리 (../ access-rights.md # access-control) 사용자를 위해.

가능한 값 :

- 0 - Disabled.
- 1 - Enabled.

기본값은 0입니다.

### user_name / 네트워크 {# user-namenetworks}

사용자가 ClickHouse 서버에 연결할 수있는 네트워크 목록입니다.

목록의 각 요소는 다음 형식 중 하나를 사용할 수 있습니다 :

-`<ip>`- IP address or network mask.

    예 :`213.180.204.3``10.0.0.1 / 8``10.0.0.1 / 255.255.255.0``2a02 : 6b8 : 3`,`2a02 : 6b8 : 3 / 64`,`2a02 : 6b8 : 3 / ffff : ffff : ffff : ffff ::`.

-`<host>`- Hostname.

    예 :`example01.host.ru`.

    체크 액세스, DNS 쿼리하고 모든 반환 된 IP 주소와 비교하여 피어 주소입니다.

-`<host_regexp>`- Regular expression for hostnames.

    예`^ example \ d \ d- \ d \ d- \ d \ .host \ .ru $`

    액세스를 확인하려면 [DNS PTR 쿼리 (https://en.wikipedia.org/wiki/Reverse_DNS_lookup) 피어 주소에 실행되고 지정된 정규식이 적용됩니다. 그럼 PTR 쿼리의 결과에 대해 다른 DNS 쿼리가 실행되고받은 모든 주소가 피어 주소와 비교됩니다. 정규 표현식은 $로 끝나는 것을 권장합니다.

모든 결과에 대한 DNS 요청을 캐시까지 서버가 다시 시작하게됩니다.

** 예 **

오픈 액세스를위한 사용자 네트워크 중 하나를 지정합니다 :

```xml
<ip> : / 0 </ ip>
```

!!! warning "경고"
    이 불안 오픈 액세스로부터 네트워크를 가지고 있지 않은 경우 방화벽을 적절하게 설정된 서버에 직접 연결됩니다.

오픈 액세스에서만 localhost를 지정합니다 :

```xml
<ip> : 1 </ ip>
<ip> 127.0.0.1 </ ip>
```

### user_name / 프로필 {# user-nameprofile}

를 지정할 수있는 설정 프로파일을 사용자입니다. 설정 프로파일은`users.xml` 파일 자세한 내용은 [설정 프로파일 (settings-profiles.md).

### user_name / 할당량 {# user-namequota}

할당량을 사용하면 일정 기간의 리소스 사용량을 추적하거나 제한 할 수 있습니다. 할당량은`quotas`
섹션`users.xml` 설정 파일.

사용자 할당량 설정을 지정할 수 있습니다. 할당량 설정에 대한 자세한 내용은 다음을 참조하십시오 할당량 (../ quotas.md # quotas).

### user_name / 데이터베이스 {# user-namedatabases}

이 섹션에서는 ClickHouse 의해 반환되는 행을 다음의 목적으로 제한 할 수 있습니다`SELECT` 쿼리로 현재 사용자가 수행 기본 열 수준입니다.

** 예 **

다음 구성력이 사용자`user1` 행만 볼 수 있습니다`table1`의 결과로`SELECT` 쿼리의 값은 다음과 같습니다. `id` 필드는 1000입니다.

```xml
<user1>
    <databases>
        <database_name>
            <table1>
                <filter> id = 1000 </ filter>
            </ table1>
        </ database_name>
    </ databases>
</ user1>
```

그`filter` 어떤 식으로 할 수 있습니다. [UInt8 (../../ sql-reference / data-types / int-uint.md) - 유형 값. 일반적으로 비교 연산자와 논리 연산자가 포함되어 있습니다. 에서 행`database_name.table1`있는 결과를 필터를 0에서도 반환하지 않습니다 사용자입니다. 이 필터는`PREWHERE` 조작 및 해제`WHERE → PREWHERE` 최적화.

원본 기사 (https://clickhouse.tech/docs/en/operations/settings/settings_users/) <! - hide ->
