---
machine_translated : true
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd
toc_priority : 51
toc_title : "\ u30AF \ u30A9 \ u30FC \ u30BF"
---

# 할당량 {#quotas}

할당량을 사용하면 일정 기간의 리소스 사용량을 제한하고 리소스 사용을 추적 할 수 있습니다.
할당량은 사용자 설정으로 설정됩니다. 'users.xml'.

이 시스템은 단일 쿼리의 복잡성을 제한하는 기능도 있습니다. 섹션을 참조 "Restrictions on query complexity").

쿼리 복잡성의 제한과는 대조적으로, 할당량 :

- 단일 쿼리를 제한하는 것이 아니라 일정 기간 동안 수행 할 수있는 쿼리 집합에 제한을 설정합니다.
- 계좌에 대한 비용과 모든 원격 서버에 대한 분산 쿼리 처리됩니다.

섹션을보고하자 'users.xml'할당량을 정의하는 파일입니다.

```xml
<! - Quotas ->
<quotas>
    <! - Quota name ->
    <default>
        <! - Restrictions for a time period. You can set many intervals with different restrictions ->
        <interval>
            <! - Length of the interval ->
            <duration> 3600 </ duration>

            <! - Unlimited. Just collect data for the specified time interval ->
            <queries> 0 </ queries>
            <errors> 0 </ errors>
            <result_rows> 0 </ result_rows>
            <read_rows> 0 </ read_rows>
            <execution_time> 0 </ execution_time>
        </ interval>
    </ default>
```

기본적으로 할당량 사용량을 제한하지 않고 각 시간의 자원 소비를 추적합니다.
각 간격마다 계산 된 자원 소비량은 각 요청 후 서버 로그에 출력됩니다.

```xml
<statbox>
    <! - Restrictions for a time period. You can set many intervals with different restrictions ->
    <interval>
        <! - Length of the interval ->
        <duration> 3600 </ duration>

        <queries> 1000 </ queries>
        <errors> 100 </ errors>
        <result_rows> 1000000000 </ result_rows>
        <read_rows> 100000000000 </ read_rows>
        <execution_time> 900 </ execution_time>
    </ interval>

    <interval>
        <duration> 86400 </ duration>

        <queries> 10000 </ queries>
        <errors> 1000 </ errors>
        <result_rows> 5000000000 </ result_rows>
        <read_rows> 500000000000 </ read_rows>
        <execution_time> 7200 </ execution_time>
    </ interval>
</ statbox>
```

때문에 'statbox'할당량 제한 시간마다 및 24 시간마다 (86,400 초)로 설정됩니다. 시간 간격은 구현 정의의 고정 모멘트에서 시작하여 계산됩니다. 즉, 24 시간 간격은 반드시 자정에 시작되는 것은 아닙니다.

간격이 완료되면 수집 된 값은 모두 삭제됩니다. 다음 시간 할당량의 계산이 끝났어요됩니다.

제한 할 수있는 금액은 다음과 같습니다 :

`queries` - The total number of requests.

`errors` - The number of queries that threw an exception.

`result_rows` - ​​The total number of rows given as a result.

`read_rows` - ​​The total number of source rows read from tables for running the query on all remote servers.

`execution_time` - The total query execution time, in seconds (wall time).

제한을 초과 한 경우에는 어떤 제한을 초과하거나 어떤 간격을 넘 었는지, 그리고 새로운 간격이 시작되었을 때 (쿼리를 다시 보낼 수있을 때)에 관한 텍스트

할당량은 "quota key"여러 키의 자원을 개별적으로보고하는 기능. 이것의 예는 다음과 같습니다 :

```xml
<! - For the global reports designer ->
<web_global>
    <! - keyed - The quota_key "key"is passed in the query parameter,
            and the quota is tracked separately for each key value.
        For example, you can pass a Yandex.Metrica username as the key,
            so the quota will be counted separately for each username.
        Using keys makes sense only if quota_key is transmitted by the program, not by a user.

        You can also write <keyed_by_ip />, so the IP address is used as the quota key.
        (But keep in mind that users can change the IPv6 address fairly easily)
    ->
    <keyed />
```

할당량은 'users'설정 섹션. 섹션을 참조 "Access rights"

분산 쿼리 처리의 경우 누적 금액은 요청한 서버에 저장됩니다. 여기에서는 사용자가 다른 서버의 정원이 있습니다 "start over."

서버를 다시 시작하면 할당량이 재설정됩니다.

원본 기사 (https://clickhouse.tech/docs/en/operations/quotas/) <! - hide ->
