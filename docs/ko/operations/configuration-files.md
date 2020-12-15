---
machine_translated : true
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd
toc_priority : 50
toc_title : "\ u8A2D \ u5B9A \ u30D5 \ u30A1 \ u30A4 \ u30EB"
---

# 설정 파일 {#configuration_files}

ClickHouse 여러 파일 구성 관리를 지원합니다. 주 설정 파일에서 지정할 수이지`/ etc / clickhouse-server / config.xml` 기타 파일은`/ etc / clickhouse-server / config.d` 디렉토리.

!!! note "주"
    모든 구성 파일은 XML 형식이어야합니다. 또한 일반적으로 동일한 루트 요소를 가지고 있어야합니다`<yandex>`.

메인 구성 파일에 지정된 일부 설정은 다른 구성 파일에 덮어 쓸 수 있습니다. 그`replace` 또는`remove` 이러한 구성 파일의 요소에 속성을 지정할 수 있습니다.

모두 지정되지 않은 경우 요소의 내용을 재귀 적으로 결합하고 중복 자식 값을 대체합니다.

만약`replace` 지정된 요소 전체를 지정된 요소로 바꿉니다.

만약`remove` 지정되면 요소를 제거합니다.

이 설정은 또한 "substitutions"요소가`incl` 특성 파일에서 해당 대체 값으로 사용됩니다. 기본적으로 파일 경로를 대체 할`/ etc / metrika.xml`. 이것은에서 바꿀 수 있습니다 [include_from (server-configuration-parameters / settings.md # server_configuration_parameters-include_from) 서버 설정 요소. 대체 값은 다음과 같이 지정됩니다`/ yandex / substitution_name`이 파일의 요소. 로 지정된 치환의 경우`incl`없는 경우 기록됩니다. ClickHouse가 부족한 대체를 기록하지 않도록하려면`optional = "true"`특성 (예를 들어, 매크로 (server-configuration-parameters / settings.md)).

대체는 ZooKeeper에서도 실행할 수 있습니다. 이렇게는 속성을 지정합니다`from_zk = "/ path / to / node"`. 요소의 값은 노드의 내용으로 대체합니다. `/ path / to / node` 사육사에서. 또한 ZooKeeper 노드에 XML 하위 트리 전체를 배치 할 수 있습니다.

그`config.xml` 파일을 지정하여 다른 config 사용자 설정 프로파일에 할당합니다. 이 설정에 대한 상대 경로는`users_config` 요소. 기본적으로 다음과 같습니다`users.xml` 만약`users_config` 사용자 설정, 프로필 및 할당량은`config.xml`.

사용자 설정은 다음과 같은 별도의 파일로 분할 할 수 있습니다`config.xml`와`config.d /`.
디`users_config` 설정없이`.xml`와 연결된 후위`.d`.
Directory`users.d` 기본적으로`users_config` 디폴트는`users.xml`.
예를 들어, 다음과 같이 사용자마다 별도의 설정 파일을 만들 수 있습니다 :

```bash
$ cat /etc/clickhouse-server/users.d/alice.xml
```

```xml
<yandex>
    <users>
      <alice>
          <profile> analytics </ profile>
            <networks>
                  <ip> : / 0 </ ip>
            </ networks>
          <password_sha256_hex> ... </ password_sha256_hex>
          <quota> analytics </ quota>
      </ alice>
    </ users>
</ yandex>
```

각 설정 파일은 서버와도`file-preprocessed.xml` 시작 파일. 이 파일은 완료된 모든 대체 및 재정이 포함되어 있으며, 정보 제공을 목적으로하고 있습니다. 설정 파일에서 ZooKeeper의 대체가 사용되고 있어도, 서버를 시작할 때 ZooKeeper를 사용할 수없는 경우, 서버는 전처리 된 파일에서 설정을로드합니다.

서버 설정 파일의 변경, 교체 및 덮어 쓰기를 실행할 때 사용 된 파일 및 ZooKeeper 노드를 추적하고 사용자 및 클러스터 구성을 그 자리에서 다시로드 즉, 서버를 다시 시작하지 않고 클러스터 사용자 및 그 설정을 변경할 수 있습니다.

원본 기사 (https://clickhouse.tech/docs/en/operations/configuration_files/) <! - hide ->
