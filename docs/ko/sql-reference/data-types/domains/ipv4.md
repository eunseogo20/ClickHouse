---
machine_translated : true
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd
toc_priority : 59
toc_title : IPv4
---

## IPv4 {# ipv4}

`IPv4`를 기반으로 도메인입니다`UInt32` IPv4 값을 저장하기위한 형식화 된 대체 역할을합니다. 그것은 검사에서 인간에 적응 입출력 포맷 및 칼럼의 타입 정보를 조밀 한 저장을 제공한다.

### 기본적인 사용법 {# basic-usage}

```sql
CREATE TABLE hits (url String, from IPv4) ENGINE = MergeTree () ORDER BY url;

DESCRIBE TABLE hits;
```

```text
┌─name─┬─type───┬─default_type─┬─default_expression─┬─comment─┬─codec_expression─┐
│ url │ String │ │ │ │ │
│ from │ IPv4 │ │ │ │ │
└──────┴────────┴──────────────┴────────────────── ──┴─────────┴──────────────────┘
```

또는 IPv4 도메인을 키로 사용할 수 있습니다 :

```sql
CREATE TABLE hits (url String, from IPv4) ENGINE = MergeTree () ORDER BY from;
```

`IPv4` 도메인은 IPv4 문자열로 정의 입력 형식을 지원 :

```sql
INSERT INTO hits (url, from) VALUES ( 'https://wikipedia.org', '116.253.40.133') ( 'https://clickhouse.tech', '183.247.232.58') ( 'https : // clickhouse .tech / docs / en / ','116.106.34.242 ');

SELECT * FROM hits;
```

```text
┌─url────────────────────────────────┬───────────from─┐
│ https://clickhouse.tech/docs/en/ │ 116.106.34.242 │
│ https://wikipedia.org │ 116.253.40.133 │
│ https://clickhouse.tech │ 183.247.232.58 │
└────────────────────────────────────┴──────────── ────┘
```

값이 포함 된 압축 바이너리 형식 :

```sql
SELECT toTypeName (from), hex (from) FROM hits LIMIT 1;
```

```text
┌─toTypeName (from) ─┬─hex (from) ─┐
│ IPv4 │ B7F7E83A │
└──────────────────┴───────────┘
```

도메인 값은 암시 적으로 형식 이외에 변환 할 수 없습니다`UInt32`.
변환하려면`IPv4` 문자열 값은 명시 적으로 그것을해야합니다`IPv4NumToString ()`함수 :

```sql
SELECT toTypeName (s) IPv4NumToString (from) as s FROM hits LIMIT 1;
```

    ┌─toTypeName (IPv4NumToString (from)) ─┬─s──────────────┐
    │ String │ 183.247.232.58 │
    └───────────────────────────────────┴───────────── ───┘

또는 a에 캐스팅`UInt32` 값 :

```sql
SELECT toTypeName (i), CAST (from as UInt32) as i FROM hits LIMIT 1;
```

```text
┌─toTypeName (CAST (from 'UInt32')) ─┬──────────i─┐
│ UInt32 │ 3086477370 │
└──────────────────────────────────┴────────────┘
```

원본 기사 (https://clickhouse.tech/docs/en/data_types/domains/ipv4) <! - hide ->
