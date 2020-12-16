---
machine_translated : true
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd
toc_priority : 60
toc_title : IPv6
---

## IPv6 {# ipv6}

`IPv6`를 기반으로 도메인입니다`FixedString (16)`IPv6 값을 저장하기위한 형식화 된 대체 역할을합니다. 그것은 검사에서 인간에 적응 입출력 포맷 및 칼럼의 타입 정보를 조밀 한 저장을 제공한다.

### 기본적인 사용법 {# basic-usage}

```sql
CREATE TABLE hits (url String, from IPv6) ENGINE = MergeTree () ORDER BY url;

DESCRIBE TABLE hits;
```

```text
┌─name─┬─type───┬─default_type─┬─default_expression─┬─comment─┬─codec_expression─┐
│ url │ String │ │ │ │ │
│ from │ IPv6 │ │ │ │ │
└──────┴────────┴──────────────┴────────────────── ──┴─────────┴──────────────────┘
```

또는`IPv6` 키로 도메인 :

```sql
CREATE TABLE hits (url String, from IPv6) ENGINE = MergeTree () ORDER BY from;
```

`IPv6` 도메인 :

```sql
INSERT INTO hits (url, from) VALUES ( 'https://wikipedia.org', '2a02 : aa08 : e000 : 3100 :: 2') ( 'https://clickhouse.tech', '2001 : 44c8 : 129 : 2632 : 33 : 0 : 252 : 2 ') ('https://clickhouse.tech/docs/en/ ','2a02 : e980 : 1e :: 1 ');

SELECT * FROM hits;
```

```text
┌─url────────────────────────────────┬─from──────────── ──────────────┐
│ https://clickhouse.tech │ 2001 : 44c8 : 129 : 2632 : 33 : 0 : 252 : 2 │
│ https://clickhouse.tech/docs/en/ │ 2a02 : e980 : 1e : 1 │
│ https://wikipedia.org │ 2a02 : aa08 : e000 : 3100 : 2 │
└────────────────────────────────────┴──────────── ───────────────────┘
```

값이 포함 된 압축 바이너리 형식 :

```sql
SELECT toTypeName (from), hex (from) FROM hits LIMIT 1;
```

```text
┌─toTypeName (from) ─┬─hex (from) ────────────────────────┐
│ IPv6 │ 200144C8012926320033000002520002 │
└──────────────────┴────────────────────────────── ────┘
```

도메인 값은 암시 적으로 형식 이외에 변환 할 수 없습니다`FixedString (16)`.
변환하려면`IPv6` 문자열 값은 명시 적으로 그것을해야합니다`IPv6NumToString ()`함수 :

```sql
SELECT toTypeName (s) IPv6NumToString (from) as s FROM hits LIMIT 1;
```

```text
┌─toTypeName (IPv6NumToString (from)) ─┬─s─────────────────────────────┐
│ String │ 2001 : 44c8 : 129 : 2632 : 33 : 0 : 252 : 2 │
└───────────────────────────────────┴───────────── ──────────────────┘
```

또는 a에 캐스팅`FixedString (16)`값 :

```sql
SELECT toTypeName (i), CAST (from as FixedString (16)) as i FROM hits LIMIT 1;
```

```text
┌─toTypeName (CAST (from 'FixedString (16)')) ─┬─i───────┐
│ FixedString (16) │ │
└───────────────────────────────────────────┴───── ────┘
```

원본 기사 (https://clickhouse.tech/docs/en/data_types/domains/ipv6) <! - hide ->
