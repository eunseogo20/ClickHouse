---
machine_translated : true
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd
toc_priority : 54
toc_title : "Url \ u306E \ u64CD \ u4F5C"
---

# Url을 조작하는 함수 {# functions-for-working-with-urls}

이 함수는 모든 RFC를 따르지 않습니다. 그들은 개선 된 성능을 위해 최대한 쉽게된다.

## URL의 일부를 추출하는 함수 {# functions-that-extract-parts-of-a-url}

관련 부분이 URL에 없으면 빈 문자열이 반환됩니다.

### 프로토콜 {#protocol}

URL에서 프로토콜을 추출합니다.

Examples of typical returned values ​​: http, https, ftp, mailto, tel, magnet ...

### 도메인 {#domain}

URL에서 호스트 이름을 추출합니다.

```sql
domain (url)
```

** 파라미터 **

-`url` - URL : Type : [문자열] (../../ sql-reference / data-types / string.md).

URL은 제도의 유무에 관계없이 지정할 수 있습니다. 예 :

```text
svn + ssh : //some.svn-hosting.com : 80 / repo / trunk
some.svn-hosting.com:80/repo/trunk
https://yandex.com/time/
```

이 예에서는`domain` 함수는 다음 결과를 반환합니다 :

```text
some.svn-hosting.com
some.svn-hosting.com
yandex.com
```

** 반환 값 **

- 호스트 이름입니다. ClickHouse가 입력 문자열을 URL로서 해석 할 수있는 경우.
- 빈 문자열. ClickHouse가 입력 문자열을 URL로 해석 할 수없는 경우.

유형 :`String`.

** 예 **

```sql
SELECT domain ( 'svn + ssh : //some.svn-hosting.com : 80 / repo / trunk')
```

```text
┌─domain ( 'svn + ssh : //some.svn-hosting.com : 80 / repo / trunk') ─┐
│ some.svn-hosting.com │
└───────────────────────────────────────────────── ───────┘
```

### domainWithoutWWW {#domainwithoutwww}

도메인을 돌려 여러 도메인을 삭제하지 않습니다 'www.'그것의 처음부터 존재하는 경우.

### 최상위 도메인 {#topleveldomain}

URL에서 최상위 도메인을 추출합니다.

```sql
topLevelDomain (url)
```

** 파라미터 **

-`url` - URL : Type : [문자열] (../../ sql-reference / data-types / string.md).

URL은 제도의 유무에 관계없이 지정할 수 있습니다. 예 :

```text
svn + ssh : //some.svn-hosting.com : 80 / repo / trunk
some.svn-hosting.com:80/repo/trunk
https://yandex.com/time/
```

** 반환 값 **

- 도메인 이름. ClickHouse가 입력 문자열을 URL로서 해석 할 수있는 경우.
- 빈 문자열. ClickHouse가 입력 문자열을 URL로 해석 할 수없는 경우.

유형 :`String`.

** 예 **

```sql
SELECT topLevelDomain ( 'svn + ssh : //www.some.svn-hosting.com : 80 / repo / trunk')
```

```text
┌─topLevelDomain ( 'svn + ssh : //www.some.svn-hosting.com : 80 / repo / trunk') ─┐
│ com │
└───────────────────────────────────────────────── ───────────────────┘
```

### firstSignificantSubdomain {#firstsignificantsubdomain}

를 돌려 준다. "first significant subdomain"이것은 Yandex에 고유의 비표준적인 개념입니다. 메틀리카 첫 번째 중요한 하위 도메인은 다음의 경우 차상위 도메인입니다 'com', 'net', 'org'또는 'co'. 그렇지 않으면 3 차 도메인입니다. 예를 들어,`firstSignificantSubdomain ( 'https://news.yandex.ru/') = 'yandex', firstSignificantSubdomain ( 'https://news.yandex.com.tr/') = 'yandex'`. 목록 "insignificant "두 번째 수준 도메인

### cutToFirstSignificantSubdomain {#cuttofirstsignificantsubdomain}

최고 수준의 하위 도메인이 포함 된 도메인의 일부를 반환합니다. "first significant subdomain"(위의 설명 참조).

예를 들어,`cutToFirstSignificantSubdomain ( 'https://news.yandex.com.tr/') = 'yandex.com.tr'`.

### 경로 {#path}

패스를 돌려줍니다. 예 :`/ top / news.html` 경로는 쿼리 문자열은 포함되지 않습니다.

### 빠스후루 {#pathfull}

위와 동일하지만 쿼리 문자열과 조각을 포함합니다. 예 : /top/news.html? 페이지 = 2 # 코멘트

### 쿼리 문자열 {#querystring}

쿼리 문자열을 반환합니다. 예 : 페이지 = 1 & lr = 213. query-string의 첫 번째 물음표뿐만 아니라 #와 # 이후의 모든도 포함되어 있지 않습니다.

### 조각 {#fragment}

조각 식별자를 돌려줍니다. fragment는 초기 해시 기호는 포함되지 않습니다.

### queryStringAndFragment {#querystringandfragment}

쿼리 문자열과 조각 식별자를 돌려줍니다. 예 : 페이지 = 1 # 29390.

### extractURLParameter (URL 이름) {# extracturlparameterurl-name}

의 값을 돌려줍니다. 'name'URL에 매개 변수가있는 경우. 그렇지 않으면 빈 문자열입니다. 이 이름의 매개 변수가 많을 경우는 최초로 출현하는 매개 변수를 반환합니다. 이 함수는 매개 변수 이름이 전달 된 인수와 동일한 방식으로 URL로 인코딩된다는 전제하에 작동합니다.

### extractURLParameters (URL) {#extracturlparametersurl}

URL 매개 변수에 대응하는 name = value 문자열의 배열을 돌려줍니다. 값은 어떤 방법으로도 디코딩되지 않습니다.

### extractURLParameterNames (URL) {#extracturlparameternamesurl}

URL 매개 변수의 이름에 해당하는 이름 문자열의 배열을 돌려줍니다. 값은 어떤 방법으로도 디코딩되지 않습니다.

### URLHierarchy (URL) {#urlhierarchyurl}

URL을 포함한 배열을 돌려줍니다. 경로 및 쿼리 문자열. 연속 구분 문자로 계산합니다. 컷은 모든 연속 구분 기호 뒤에 위치로 이루어집니다.

### URLPathHierarchy (URL) {#urlpathhierarchyurl}

위와 동일하지만 결과에 프로토콜 및 호스트는 없습니다. 요소 (루트)는 포함되지 않습니다. 예 : 함수는 트리를 구현하는 데 사용되는 Yandex의 URL을보고합니다. 미터법

```text
URLPathHierarchy ( 'https://example.com/browse/CONV-6788') =
[
    '/ browse /',
    '/ browse / CONV-6788'
]
```

### decodeURLComponent (URL) {#decodeurlcomponenturl}

디코딩 된 URL을 반환합니다.
예 :

```sql
SELECT decodeURLComponent ( 'http://127.0.0.1:8123/?query=SELECT%201%3B') AS DecodedURL;
```

```text
┌─DecodedURL─────────────────────────────┐
│ http://127.0.0.1:8123/?query=SELECT 1; │
└────────────────────────────────────────┘
```

## URL의 일부를 삭제하는 함수 {# functions-that-remove-part-of-a-url}

URL에 유사한 것이없는 경우 URL은 변경되지 않습니다.

### cutWWW {#cutwww}

복수를 제거하지 않습니다 'www.'URL의 도메인의 처음부터 존재하는 경우.

### cutQueryString {#cutquerystring}

쿼리 문자열을 삭제합니다. 물음표도 삭제됩니다.

### 컷 조각 {#cutfragment}

조각 식별자를 제거합니다. 숫자 기호가 삭제됩니다.

### cutQueryStringAndFragment {#cutquerystringandfragment}

쿼리 문자열 및 부분 식별자를 제거합니다. 물음표와 숫자 기호가 삭제됩니다.

### cutURLParameter (URL 이름) {# cuturlparameterurl-name}

를 제거합니다. 'name'URL 매개 변수가 존재하는 경우. 이 함수는 매개 변수 이름이 전달 된 인수와 동일한 방식으로 URL로 인코딩된다는 전제하에 작동합니다.

원본 기사 (https://clickhouse.tech/docs/en/query_language/functions/url_functions/) <! - hide ->
