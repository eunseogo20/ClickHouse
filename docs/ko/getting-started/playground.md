---
toc_priority : 14
toc_title : Playground
---

# ClickHouse Playground {# clickhouse-playground}

[ClickHouse Playground (https://play.clickhouse.tech)는 서버 나 클러스터를 설정하지 않고 즉시 쿼리를 실행 ClickHouse을 시도 할 수 있습니다.
몇 가지 예를 들어 데이터 세트는 Playground뿐만 아니라 ClickHouse의 기능을 보여주는 샘플 쿼리로 사용할 수 있습니다. 또한 ClickHouse의 LTS 버전에서 시도 할 수 있습니다.

ClickHouse Playground은 [Yandex.Cloud (https://cloud.yandex.com/)에 호스트되는 m2.small [Managed Service for ClickHouse (https://cloud.yandex.com/services/managed- clickhouse) 인스턴스 (4 vCPU 32 GB RAM)에서 볼 수 있습니다. 클라우드 공급자에 대한 자세한 내용은 [여기] (../ commercial / cloud.md).

임의의 HTTP 클라이언트를 사용하여 운동장에 쿼리를 만들 수 있습니다. 예를 들어 [curl (https://curl.haxx.se), wget (https://www.gnu.org/software/wget/), JDBC (../interfaces/jdbc.md) 또는 [ODBC] (../ interfaces / odbc.md) 드라이버를 사용하여 연결을 설정합니다.
ClickHouse을 지원하는 소프트웨어 제품에 대한 자세한 정보는 [여기] (../ interfaces / index.md)를 참조하십시오.

## 자격 증명 {#credentials}

| 매개 변수 | 값 |
| : ---------------------------- | : ------------------ -------------------- |
| HTTPS 끝점 |`https : //play-api.clickhouse.tech : 8443` |
| 기본 TCP 종점 |`play-api.clickhouse.tech : 9440` |
| 사용자 이름 |`playgrounnd` |
| 비밀번호 |`clickhouse` |


특정 ClickHouse 릴리스에서 시험하기 위해 추가 엔드 포인트가 있습니다. (포트 및 사용자 / 암호는 위와 동일합니다).

- 20.3 LTS :`play-api-v20-3.clickhouse.tech`
- 19.14 LTS :`play-api-v19-14.clickhouse.tech`

!!! note "비고"
이러한 엔드 포인트는 모든 보안 TLS 연결이 필요합니다.


## 제한 사항 {#limitations}

쿼리는 읽기 전용 사용자로 실행됩니다. 여기에는 몇 가지 제한이 있습니다.

- DDL 쿼리는 허용되지 않습니다.
- INSERT 쿼리는 허용되지 않습니다.

또한 다음의 설정이 이루어지고 있습니다.

- [max_result_bytes = 10485760] (../ operations / settings / query_complexity / # max-result-bytes)
- [max_result_rows = 2000] (../ operations / settings / query_complexity / # setting-max_result_rows)
- [result_overflow_mode = break] (../ operations / settings / query_complexity / # result-overflow-mode)
- [max_execution_time = 60000] (../ operations / settings / query_complexity / # max-execution-time)

## 예 {#examples}

`curl`를 사용하여 HTTPS 끝점에 연결하는 예 :

```bash
curl "https://play-api.clickhouse.tech:8443/?query=SELECT+'Play+ClickHouse\!';&user=playground&password=clickhouse&database=datasets"
```

[CLI] (../ interfaces / cli.md)에서 TCP 끝점에 연결하는 예 :

```bash
clickhouse client --secure -h play-api.clickhouse.tech --port 9440 -u playground --password clickhouse -q "SELECT 'Play ClickHouse \!'"
```

## 구현의 세부 사항 {# implementation-details}

ClickHouse Playground의 Web 인터페이스는 ClickHouse [HTTP API (../ interfaces / http.md)를 통해 요청합니다.
Playground 백엔드 추가 서버 측 애플리케이션을 수반하지 않는 단순한 ClickHouse 클러스터입니다.
위와 같이, ClickHouse HTTPS 및 TCP / TLS 끝점은 Playground의 일부로 공개되며,
모두 위의 보호와 더 나은 글로벌 연결을위한 레이어를 추가하려면 [Cloudflare Spectrum (https://www.cloudflare.com/products/cloudflare-spectrum/)을 통해 프록시됩니다 있습니다.

!!! warning "주의"
    어떠한 경우에도 인터넷에 ClickHouse 서버를 공개하는 것은 ** 비추천입니다 **.
    개인 네트워크에서만 접속을 대기하고 적절하게 설정된 방화벽에 의해 보호되고 있는지 확인하십시오.
