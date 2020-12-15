---
machine_translated : true
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd
toc_priority : 21
toc_title : "\ u5165 \ u529B \ u304A \ u3088 \ u3073 \ u51FA \ u529B \ u30D5 \ u30A9 \ u30FC \ u30DE \ u30C3 \
  \ u30C8 "
---

# 입출력 데이터의 포맷 {#formats}

ClickHouse 수용 및 회신 데이터 프레임 워크입니다. 입력 단자 지원되는 형식을 사용하여 지정된 데이터를 분석 할 수 있습니다`INSERT`s 실행하는`SELECT` 파일, URL, HDFS 등의 파일 백업 테이블에서 또는 외부 사전을 읽을 수 수 있습니다. 출력에서 지원되는 포맷을 사용하여
a 결과`SELECT` 및 실행`INSERT` 파일 백업 테이블에 s.

지원되는 포맷은 :

| 형식 | 입력 | 출력 |
| ------------------------------------------------- ---------------- | ------ | ------ |
| [TabSeparated (# tabseparated) | ✔ | ✔ |
| [TabSeparatedRaw (# tabseparatedraw) | ✗ | ✔ |
| [TabSeparatedWithNames (# tabseparatedwithnames) | ✔ | ✔ |
| [TabSeparatedWithNamesAndTypes (# tabseparatedwithnamesandtypes) | ✔ | ✔ |
| [템플릿] (# format-template) | ✔ | ✔ |
| [TemplateIgnoreSpaces (# templateignorespaces) | ✔ | ✗ |
| [CSV (# csv) | ✔ | ✔ |
| [CSVWithNames (# csvwithnames) | ✔ | ✔ |
| 사용자 정의 구분 기호 (# format-customseparated) | ✔ | ✔ |
| 값 (# data-format-values) | ✔ | ✔ |
| [수직] (# vertical) | ✗ | ✔ |
| [VerticalRaw (# verticalraw) | ✗ | ✔ |
| [JSON (# json) | ✗ | ✔ |
| [JSONCompact (# jsoncompact) | ✗ | ✔ |
| [JSONEachRow (# jsoneachrow) | ✔ | ✔ |
| [TSKV (# tskv) | ✔ | ✔ |
| [예쁜] (# pretty) | ✗ | ✔ |
| [예쁜 컴팩트 (# prettycompact) | ✗ | ✔ |
| [예쁜 컴팩트 모노 블록 (# prettycompactmonoblock) | ✗ | ✔ |
| [프리티 노 스케이프 (# prettynoescapes) | ✗ | ✔ |
| [PrettySpace (# prettyspace) | ✗ | ✔ |
| [뿌로토부후 (# protobuf) | ✔ | ✔ |
| [아부로 (# data-format-avro) | ✔ | ✔ |
| [아브로 합류 (# data-format-avro-confluent) | ✔ | ✗ |
| [마루] (# data-format-parquet) | ✔ | ✔ |
| [ORC (# data-format-orc) | ✔ | ✗ |
| [로우 바이너리 (# rowbinary) | ✔ | ✔ |
| [RowBinaryWithNamesAndTypes (# rowbinarywithnamesandtypes) | ✔ | ✔ |
| [기본] (# native) | ✔ | ✔ |
| [Null] (# null) | ✗ | ✔ |
| [XML] (# xml) | ✗ | ✔ |
| [CapnProto (# capnproto) | ✔ | ✗ |

ClickHouse 설정을 사용하여 여러 가지 형식 처리 매개 변수를 제어 할 수 있습니다. 자세한 내용은 [설정] (../ operations / settings / settings.md) 섹션

## TabSeparated {#tabseparated}

TabSeparated 형식으로 데이터가 행마다 기록됩니다. 각 행은 탭으로 구분 된 값이 포함됩니다. 각 값 다음에 탭이 계속됩니다 만, 행의 마지막 값 뒤에 줄 바꿈이옵니다. 엄정 한 Unix 라인 피드 가정합니다. 마지막 줄에는 마지막에 개행도 포함해야합니다. 값은 따옴표를 둘러 않고 텍스트 형식으로 작성되며, 특수 문자는 이스케이프됩니다.

이 형식은 이름에서도 사용할 수 있습니다`TSV`.

그`TabSeparated` 형식은 편리한 가공 데이터를 사용자 프로그램이나 소개합니다. 이것은 HTTP 인터페이스 및 명령 줄 클라이언트 배치 모드에서 기본적으로 사용됩니다. 이 형식은 다른 Dbms간에 데이터를 전송할 수 있습니다. 예를 들어, MySQL에서 덤프를 얻을 수 ClickHouse에 업로드 할 수도 있고 그 반대도 가능합니다.

그`TabSeparated` 서식은 합계 (총 함께 사용하는 경우)와 극값 (경우)의 출력이 지원됩니다 'extremes'1)로 설정된다. 이러한 경우 합계와 극값이 메인 데이터 뒤에 출력됩니다. 주요 결과 총 값 및 극값은 빈 줄로 구분됩니다. 예 :

```sql
SELECT EventDate, count () AS c FROM test.hits GROUP BY EventDate WITH TOTALS ORDER BY EventDate FORMAT TabSeparated``
```

```text
2014-03-17 1406958
2014-03-18 1383658
2014-03-19 1405797
2014-03-20 1353623
2014-03-21 1245779
2014-03-22 1031592
2014-03-23 ​​1046491

1970-01-01 8873898

2014-03-17 1031592
2014-03-23 ​​1406958
```

### 데이터 서식 {# data-formatting}

정수는 소수점 형식으로 작성되어 있습니다. 번호를 포함 할 수 추가 "+"첫 번째 문자 (해석시는 무시되고 서식 설정시는 기록되지 않습니다). 부가 아닌 수치는 마이너스 부호를 포함 할 수 없습니다. 읽을 때 빈 문자열을 0으로 해석하거나 (부호 형의 경우) 마이너스 기호만으로 구성되는 문자열을 0으로 해석 할 수 해당 데이터 형식에 맞지 않는 수치 오류 메시지 없이 다른 숫자로 해석 될 수 있습니다.

부동 소수점 숫자는 소수점 형식으로 작성됩니다. 도트 소수점 구분 기호로 사용됩니다. 지수 항목이 지원되고 있습니다. 'inf' '+ inf', '-inf', and 'nan'부동 소수점 항목 소수점으로 시작하거나 끝나지 할 수 있습니다.
서식 동안 부동 소수점 정밀도가 없어 질 가능성이 있습니다.
해석 중에 가장 가까운 시스템 표현 가능한 번호를 읽을 수는 엄격하게 필요하지 않습니다.

날짜는 YYYY-MM-DD 형식으로 기술 된 같은 형식으로 해석되지만 구분 기호로 문자가 사용됩니다.
시간이있는 날짜는 다음 형식으로 작성됩니다`YYYY-MM-DD hh : mm : ss` 같은 형식으로 해석되지만 구분 기호로 문자로 해석됩니다.
이것은 모든 클라이언트 또는 서버가 시작할 때 시스템 시간대에 발생합니다 (데이터 형식에 따라). 시간있는 날짜의 경우, 서머 타임은 지정되지 않습니다. 따라서 일광 절약 시간 사이에 덤프 시간이 있으면 덤프 데이터와 명확하게 일치하지 않기 때문에 분석은 두 번 중 하나를 선택합니다.
읽기 작업 중에 오류 메시지없이 자연스러운 오버 플로우 또는 null의 날짜와 시간으로 잘못된 날짜와 시간을 분석 할 수 있습니다.

예외로, 정확히 10 자리 숫자로 구성되어있는 경우, 시간을 포함한 날짜의 분석도 Unix 타임 스탬프 형식으로 지원됩니다. 결과는 시간대에 의존하지 않습니다. YYYY-MM-DD hh : mm : ss와 NNNNNNNNNN 형식은 자동으로 구별됩니다.

문자열은 백 슬래시로 이스케이프 된 특수 문자로 출력됩니다. 다음의 이스케이프 시퀀스를 사용 출력 :`\ b`,`\ f`,`\ r``\ n``\ t``\ 0``\ '``\\`. 분석 에도 대응 배열`\ a``\ v`, and`\ xHH` (hex 이스케이프 시퀀스)와`\ c` 시퀀스`c`는 임의의 문자입니다 (이 시퀀스는`c`). 이러한 데이터를로드 형식에 대응하고 개행 해 쓸 수`\ n` 또는`\`, 또는 개행으로. 예를 들어, 문자열`Hello world` 공백 대신 단어 사이의 줄 바꿈을 사용하면 다음 중 하나의 변형에서 분석 할 수 있습니다 :

```text
Hello \ nworld

Hello \
world
```

돌연변이에서 지원하여 MySQL에 쓸 때 탭 구 잘릴 장.

TabSeparated 형식으로 데이터를 전달할 때 탈출해야하는 최소 문자 세트 : tab 줄 바꿈 (LF) 및 백 슬래시.

만 작은 세트의 기호는 자동으로 이스케이프됩니다. 당신의 단말기가 출력 망칠 문자열 값에 쉽게 발생 할 수 있습니다.

배열은 대괄호로 둘러싸인 콤마로 구분 된 값 목록으로 작성됩니다. 배열의 Number 항목은 정상적으로 서식됩니다. `Date`와`DateTime` 형은 작은 따 quotes로 작성됩니다. 문자열은 위와 같은 이스케이프 규칙에서 한 따 quotes로 작성됩니다.

[NULL] (../ sql-reference / syntax.md)로 서식하는`\ N`.

각 요소 [중첩] (../ sql-reference / data-types / nested-data-structures / nested.md) 구조는 배열로 표현됩니다.

예를 들면 :

```sql
CREATE TABLE nestedt
(
    `id` UInt8,
    `aux` Nested (
        a UInt8,
        b String
    )
)
ENGINE = TinyLog
```

```sql
INSERT INTO nestedt Values ​​(1, 1, 'a'])
```

```sql
SELECT * FROM nestedt FORMAT TSV
```

```text
1 [1] [ 'a']
```

## TabSeparatedRaw {#tabseparatedraw}

과는 다른`TabSeparated` 행이 탈출하지 않고 기록하는 형식입니다.
이 형식은 쿼리 결과의 출력에만 적합하지만 분석 (테이블에 삽입 할 데이터의 취득)에는 적합하지 않습니다.

이 형식은 이름에서도 사용할 수 있습니다`TSVRaw`.

## TabSeparatedWithNames {#tabseparatedwithnames}

과는 다른`TabSeparated` 열 이름이 첫 번째 줄에 기록되는 형식.
분석 중 첫 번째 행은 완전히 무시됩니다. 열 이름을 사용하여 위치를 확인하거나 열 정확성을 확인 할 수 없습니다.
(헤더 행의 구문 분석 지원은 향후에 추가 될 수 있습니다.)

이 형식은 이름에서도 사용할 수 있습니다`TSVWithNames`.

## TabSeparatedWithNamesAndTypes {#tabseparatedwithnamesandtypes}

과는 다른`TabSeparated` 열 이름은 첫 행에 기록 된 열 유형은 두 번째 행에 기록됩니다.
분석 중 첫째 줄과 둘째 줄은 완전히 무시됩니다.

이 형식은 이름에서도 사용할 수 있습니다`TSVWithNamesAndTypes`.

## 템플릿 {# format-template}

이 형식에서 지정하는 사용자 지정 형식 문자열과 개체 틀에 대한 값을 지정하고 도망합니다.

설정을 사용합니다`format_template_resultset``format_template_row``format_template_rows_between_delimiter` and some settings of other formats (eg`output_format_json_quote_64bit_integers` 사용하는 경우`JSON` 이스케이프 더 참조)

설정`format_template_row` 다음 구문에서 행의 서식 문자열을 포함하는 파일의 경로를 지정합니다 :

`delimiter_1 $ {column_1 : serializeAs_1} delimiter_2 $ {column_2 : serializeAs_2} ... delimiter_N`,

어디`delimiter_i` 값의 구분자입니다 (`$`기호 수 놓치고`$$`)
`column_i` 값을 선택하거나 삽입되는 열 이름 또는 색인을 지정합니다 (비어있는 경우, 열은 생략됩니다)
`serializeAs_i` 열 값의 이스케이프 규칙입니다. 다음 탈출 규칙에 대응 :

-`CSV``JSON``XML` (같은 이름의 형식과 동일)
-`Escaped` (마찬가지로`TSV`)
-`Quoted` (마찬가지로`Values`)
-`Raw` (탈출하지 않고, 마찬가지로`TSVRaw`)
-`None` (이스케이프 규칙없이 설명 참조)

이스케이프 규칙이 생략 된 경우`None` 사용됩니다. `XML`와`Raw` 출력을 위해서만 적합합니다.

따라서 다음과 같은 형식 문자열의 경우 :

      `Search phrase : $ {SearchPhrase : Quoted} count : $ {c : Escaped} ad price : $$$ {price : JSON};`

값`SearchPhrase``c`와`price` 열은 다음과 같이 이스케이프`Quoted``Escaped`와`JSON` 사이에 (선택을 위해) 인쇄되거나 (삽입을위한 에) 기대됩니다`Search phrase :``count :``, ad price : $`와`;`구분. 예를 들면 :

`Search phrase : 'bathroom interior design', count : 2166, ad price : $ 3;`

그`format_template_rows_between_delimiter` 이것은 마지막 행을 제외한 모든 행 다음에 인쇄됩니다 (또는 기대됩니다). (`\ n` 기본적으로)

설정`format_template_resultset`이 경로는 resultset의 형식 문자열이 포함됩니다. Resultset의 형식 문자열은 행의 형식 문자열과 같은 구문을 가지고, 접두사, 접미사 및 추가 정보를 출력하는 방법을 지정할 수 있습니다. 열 이름 대신에 다음 플레이스 홀더가 포함되어 있습니다 :

-`data` 데이터를 포함하는 행입니다`format_template_row`로 구분 된 형식`format_template_rows_between_delimiter`이 개체 틀의 첫 번째 자리 표시 자 형식의 문자열입니다.
-`totals` 총 값을 가진 행입니다. `format_template_row` 서식 (총 사용하는 경우)
-`min`은 최소 값을 가진 행입니다. `format_template_row` 서식 (극값이 1로 설정되어있는 경우)
-`max`는 최대 값을 갖는 행입니다. `format_template_row` 서식 (극값이 1로 설정되어있는 경우)
-`rows` 출력 행의 총 수
-`rows_before_limit` 거기에 있던 것이다 행의 최소 수는 제한 없음입니다. 출력의 경우 만 포함하는 쿼리를 제한합니다. 쿼리에 GROUP BY가 포함되어있는 경우 rows_before_limit_at_least 제한없이 이루어 행의 정확한 숫자입니다.
-`time` 요청의 실행 시간을 초 단위로 지정합니다
-`rows_read`로드 된 행 수
-`bytes_read` (압축되지 않은)로드 된 바이트 수입니다

자리 표시 자`data``totals``min`와`max` 탈출 규칙을 지정해서는 안됩니다`None` 명시 적으로 지정해야합니다). 나머지 자리 표시자를있는 탈출 규칙을 지정합니다.
만약`format_template_resultset` 설정은 빈 문자열입니다`$ {data}`기본값으로 사용됩니다.
Insert 쿼리 형식은 접두사 또는 접미사의 경우 일부 열 또는 일부 필드를 생략 할 수 있습니다 (예 참조).

예를 선택 :

```sql
SELECT SearchPhrase, count () AS c FROM test.hits GROUP BY SearchPhrase ORDER BY c DESC LIMIT 5 FORMAT Template SETTINGS
format_template_resultset = '/some/path/resultset.format', format_template_row = '/some/path/row.format', format_template_rows_between_delimiter = '\ n'
```

`/ some / path / resultset.format` :

```text
<! DOCTYPE HTML>
<html> <head> <title> Search phrases </ title> </ head>
 <body>
  <table border = "1"> <caption> Search phrases </ caption>
    <tr> <th> Search phrase </ th> <th> Count </ th> </ tr>
    $ {data}
  </ table>
  <table border = "1"> <caption> Max </ caption>
    $ {max}
  </ table>
  <b> Processed $ {rows_read : XML} rows in $ {time : XML} sec </ b>
 </ body>
</ html>
```

`/ some / path / row.format` :

```text
<tr> <td> $ {0 : XML} </ td> <td> $ {1 : XML} </ td> </ tr>
```

결과 :

```html
<! DOCTYPE HTML>
<html> <head> <title> Search phrases </ title> </ head>
 <body>
  <table border = "1"> <caption> Search phrases </ caption>
    <tr> <th> Search phrase </ th> <th> Count </ th> </ tr>
    <tr> <td> </ td> <td> 8267016 </ td> </ tr>
    <tr> <td> bathroom interior design </ td> <td> 2166 </ td> </ tr>
    <tr> <td> yandex </ td> <td> 1655 </ td> </ tr>
    <tr> <td> spring 2014 fashion </ td> <td> 1549 </ td> </ tr>
    <tr> <td> freeform photos </ td> <td> 1480 </ td> </ tr>
  </ table>
  <table border = "1"> <caption> Max </ caption>
    <tr> <td> </ td> <td> 8873898 </ td> </ tr>
  </ table>
  <b> Processed 3095973 rows in 0.1569913 sec </ b>
 </ body>
</ html>
```

삽입 예 :

```text
Some header
Page views : 5, User id : 4324182021466249494, Useless field : hello, Duration : 146, Sign : -1
Page views : 6, User id : 4324182021466249494, Useless field : world, Duration : 185, Sign : 1
Total rows : 2
```

```sql
INSERT INTO UserActivity FORMAT Template SETTINGS
format_template_resultset = '/some/path/resultset.format', format_template_row = '/some/path/row.format'
```

`/ some / path / resultset.format` :

```text
Some header \ n $ {data} \ nTotal rows : $ {: CSV} \ n
```

`/ some / path / row.format` :

```text
Page views : $ {PageViews : CSV}, User id : $ {UserID : CSV} Useless field : $ {: CSV} Duration : $ {Duration : CSV}, Sign : $ {Sign : CSV}
```

`PageViews``UserID``Duration`와`Sign` 내부의 플레이스 홀더는 테이블의 열 이름입니다. 후의 값`Useless field` 행과 이후`\ nTotal rows :`in suffix는 무시됩니다.
모든 단락 문자의 입력 데이터를 엄밀하게 동일한 구분자로 지정된 포맷 문자열입니다.

## TemplateIgnoreSpaces {#templateignorespaces}

이 형식은 입력에만 적합합니다.
유사한`Template` 그러나 입력 스트림에서 구분 기호와 값 사이에 공백을 건너 뜁니다. 그러나 형식 문자열에 공백이 포함되어있는 경우 이러한 문자는 입력 스트림에서 사용됩니다. 하지만 지정 빈 자리 표시 자 (`$ {}`또는`$ {: None}`) 구분 문자를 여러 부분으로 분할하여 그들 사이의 공간을 무시합니다. 같은 자리 표시자를 사용하겠습니다 비거리 공백입니다.
그것은 읽을 수 있습니다`JSON` 열의 값이 모든 행에 같은 순서를 가지는 경우,이 형식을 사용합니다. 예를 들어, 다음 요청은 format의 출력에서 ​​데이터를 삽입하는 데 사용할 수 있습니다 [JSON (# json) :

```sql
INSERT INTO table_name FORMAT TemplateIgnoreSpaces SETTINGS
format_template_resultset = '/some/path/resultset.format', format_template_row = '/some/path/row.format', format_template_rows_between_delimiter = ''
```

`/ some / path / resultset.format` :

```text
{$ {} "meta"$ {} : $ {: JSON}, $ {} "data"$ {} : $ {} $ {data} $ {}, $ {} "totals"$ {} : $ {: JSON}, $ {} "extremes"$ {} : $ {: JSON}, $ {} "rows"$ {} : $ {: JSON}, $ {} "rows_before_limit_at_least"$ {} : $ { : JSON} $ {}}
```

`/ some / path / row.format` :

```text
{$ {} "SearchPhrase"$ {} : $ {} $ {phrase : JSON} $ {}, $ {} "c"$ {} : $ {} $ {cnt : JSON} $ {}}
```

## TSKV {#tskv}

TabSeparated 비슷하지만 name = value 형식으로 값을 출력합니다. 이름은 TabSeparated 형식과 같은 방법으로 탈출 = 기호 이스케이프됩니다.

```text
SearchPhrase = count () = 8267016
SearchPhrase = bathroom interior design count () = 2166
SearchPhrase = yandex count () = 1655
SearchPhrase = 2014 spring fashion count () = 1549
SearchPhrase = freeform photos count () = 1480
SearchPhrase = angelina jolie count () = 1245
SearchPhrase = omsk count () = 1112
SearchPhrase = photos of dog breeds count () = 1091
SearchPhrase = curtain designs count () = 1064
SearchPhrase = baku count () = 1000
```

[NULL] (../ sql-reference / syntax.md)로 서식하는`\ N`.

```sql
SELECT * FROM t_null FORMAT TSKV
```

```text
x = 1 y = \ N
```

작은 열이 많은 경우,이 형식은 무효이며, 일반적으로 사용하는 이유는 없습니다. 그럼에도 불구하고 그것은 JSONEachRow보다 나쁘지 않습니다 효율성 측면에서.

Both data output and parsing are supported in this format. For parsing, any order is supported for the values ​​of different columns. It is acceptable for some values ​​to be omitted - they are treated as equal to their default values. In this case, zeros and blank rows are used as default values. Complex values ​​that could be specified in the table are not supported as defaults.

구문 분석 할 수있는 추가 필드`tskv` 등호 또는 값 없음. 이 항목은 무시됩니다.

## CSV {#csv}

쉼표로 구분 된 값 형식 (RFC (https://tools.ietf.org/html/rfc4180)).

서식의 경우 행은 큰 따옴표로 묶여 있습니다. 문자열에 큰 따옴표는 행의 큰 따옴표로 출력됩니다. 문자를 이스케이프 규칙은 다른 없습니다. Date와 date-time은 큰 따옴표로 묶습니다. 수치는 따옴표없이 출력됩니다. 값은 구분 기호로 구분됩니다. ``기본적으로. 구분 문자 설정에서 정의되어 있습니다 [format_csv_delimiter] (../ operations / settings / settings.md # settings-format_csv_delimiter) 행은 Unix 라인 피드 (LF)를 사용하여 구분됩니다. 먼저 배열은 TabSeparated 형식처럼 문자열로 직렬화 된 결과 문자열은 큰 따옴표로 CSV로 출력됩니다. CSV 형식의 튜플은 별도의 열로 직렬화됩니다 (즉, 튜플 내의 상자는 손실됩니다).

```bash
$ clickhouse-client --format_csv_delimiter = "|"--query = "INSERT INTO test.csv FORMAT CSV"<data.csv
```

\ * 기본적으로 구분 문자는``.를 참조하십시오. [format_csv_delimiter] (../ operations / settings / settings.md # settings-format_csv_delimiter) 더 많은 정보를위한 설정.

분석시에는 모든 값을 따옴표의 유무에 관계없이 분석 할 수 있습니다. 큰 따옴표와 작은 따 quotes 모두 지원합니다. 행 따옴표없이 배치 할 수 있습니다. 이 경우 구분 기호 또는 줄 바꿈 (CR 또는 LF)까지 해석됩니다. RFC에 위반하여 따옴표없이 행을 해석하면 선행 및 후행 공백과 탭은 무시됩니다. 라인 피드는 Unix (LF), Windows (CR LF) 및 Mac OS Classic (CR LF) 타입을 모두 지원합니다.

빈 인용되지 않은 입력 값은 각 열의 디폴트 값으로 대체합니다.
[input_format_defaults_for_omitted_fields] (../ operations / settings / settings.md # session_settings-input_format_defaults_for_omitted_fields)
유효합니다.

`NULL` 서식하는`\ N` 또는`NULL` 또는 빈 인용되지 않은 문자열 (설정을 참조 [input_format_csv_unquoted_null_literal_as_null] (../ operations / settings / settings.md # settings-input_format_csv_unquoted_null_literal_as_null ) 및 input_format_defaults_for_omitted_fields] (../ operations / settings / settings.md # session_settings-input_format_defaults_for_omitted_fields)).

CSV 형식으로 총과 극한의 출력은 다음과 같이 지원됩니다`TabSeparated`.

## CSVWithNames {#csvwithnames}

또한 다음과 같은 헤더 행을 출력합니다`TabSeparatedWithNames`.

## 사용자 정의 구분 {# format-customseparated}

유사한 [템플릿] (# format-template)입니다 만, 지문을 읽거나 모든 컬럼을 사용 탈출 규칙에서 설정`format_custom_escaping_rule` 설정에서 구분 기호`format_custom_field_delimiter``format_custom_row_before_delimiter``format_custom_row_after_delimiter``format_custom_row_between_delimiter ``format_custom_result_before_delimiter`와`format_custom_result_after_delimiter` 형식 문자열에서하지 않습니다.
또한`CustomSeparatedIgnoreSpaces` 형식은 다음과 같습니다`TemplateIgnoreSpaces`.

## JSON {#json}

JSON 형식의 데이터를 출력합니다. 데이터 테이블 외에 열 이름과 형식 및 출력 행의 총 개수 제한이없는 경우에 출력 될 수있는 행의 수 등의 추가 정보를 출력합니다. 예 :

```sql
SELECT SearchPhrase, count () AS c FROM test.hits GROUP BY SearchPhrase WITH TOTALS ORDER BY c DESC LIMIT 5 FORMAT JSON
```

```json
{
        "meta":
        [
                {
                        "name": "SearchPhrase"
                        "type": "String"
                }
                {
                        "name": "c"
                        "type": "UInt64"
                }
        ,

        "data":
        [
                {
                        "SearchPhrase": "",
                        "c": "8267016"
                }
                {
                        "SearchPhrase": "bathroom interior design"
                        "c": "2166"
                }
                {
                        "SearchPhrase": "yandex"
                        "c": "1655"
                }
                {
                        "SearchPhrase": "spring 2014 fashion"
                        "c": "1549"
                }
                {
                        "SearchPhrase": "freeform photos"
                        "c": "1480"
                }
        ,

        "totals":
        {
                "SearchPhrase": "",
                "c": "8873898"
        }

        "extremes":
        {
                "min":
                {
                        "SearchPhrase": "",
                        "c": "1480"
                }
                "max":
                {
                        "SearchPhrase": "",
                        "c": "8267016"
                }
        }

        "rows": 5

        "rows_before_limit_at_least": 141137
}
```

JSON은 JavaScript와 호환성이 있습니다. 이를 보장하기 위해 일부 문자는 추가 이스케이프됩니다. `/`로 이스케이프`\ /`; 대체 개행`U + 2028`과`U + 2029` 브라우저에서는 이스케이프됩니다`\ uXXXX`. ASCII 제어 문자는 이스케이프됩니다. `\ b`,`\ f`,`\ n``\ r``\ t`를 사용하여 00-1F 범위의 나머지 바이트와 마찬가지로`\ uXXXX` sequences. Invalid UTF-8 sequences are changed to the replacement character so the output text will consist of valid UTF-8 sequences. For compatibility with JavaScript, Int64 and UInt64 integers are enclosed in double-quotes by default. to remove the quotes, you can set the configuration parameter [output_format_json_quote_64bit_integers] (../ operations / settings / settings.md # session_settings-output_format_json_quote_64bit_integers) 0으로한다.

`rows` - ​​The total number of output rows.

`rows_before_limit_at_least` 거기에있는 행의 최소 수는 제한없이 이었겠죠. 출력의 경우 만 포함하는 쿼리를 제한합니다.
쿼리에 GROUP BY가 포함되어있는 경우 rows_before_limit_at_least 제한없이 이루어 행의 정확한 숫자입니다.

`totals` - Total values ​​(when using WITH TOTALS).

`extremes` - Extreme values ​​(when extremes are set to 1).

이 형식은 쿼리 결과의 출력에만 적합하지만 분석 (테이블에 삽입 할 데이터의 취득)에는 적합하지 않습니다.

ClickHouse 지원 [NULL] (../ sql-reference / syntax.md)로 표시됩니다. `null` JSON 출력.

도 참조. [JSONEachRow (# jsoneachrow) 형식.

## JSONCompact {#jsoncompact}

JSON과 다른 점은 데이터 행이 객체가 아닌 배열에서 출력되는 점입니다.

예 :

```json
{
        "meta":
        [
                {
                        "name": "SearchPhrase"
                        "type": "String"
                }
                {
                        "name": "c"
                        "type": "UInt64"
                }
        ,

        "data":
        [
                [ "", "8267016",
                [ "bathroom interior design", "2166",
                [ "yandex", "1655",
                [ "fashion trends spring 2014", "1549",
                [ "freeform photo", "1480"]
        ,

        "totals": [ "", "8873898",

        "extremes":
        {
                "min": [ "", "1480",
                "max": [ "", "8267016"]
        }

        "rows": 5

        "rows_before_limit_at_least": 141137
}
```

이 형식은 쿼리 결과의 출력에만 적합하지만 분석 (테이블에 삽입 할 데이터의 취득)에는 적합하지 않습니다.
도 참조. `JSONEachRow` 형식.

## JSONEachRow {#jsoneachrow}

이 형식을 사용하는 경우 ClickHouse 행을 구분 줄 바꿈으로 구분 된 JSON 개체로 출력되지만 데이터 전체 유효한 JSON은 없습니다.

```json
{ "SearchPhrase": "curtain designs", "count ()": "1064"}
{ "SearchPhrase": "baku", "count ()": "1000"}
{ "SearchPhrase": "", "count ()": "8267016"}
```

데이터를 삽입 할 때 각 행에 개별 JSON 개체를 지정해야합니다.

### 데이터 삽입 {# inserting-data}

```sql
INSERT INTO UserActivity FORMAT JSONEachRow { "PageViews": 5, "UserID": "4324182021466249494", "Duration"146 "Sign": - 1} { "UserID": "4324182021466249494", "PageViews": 6 "Duration "185"Sign ": 1}
```

클릭 하우스 :

- 객체의 키와 값 쌍의 순서.
- 일부 값을 선택합니다.

ClickHouse을 무시한 공간 요소는 쉼표 뒤에 객체. 모든 개체를 한줄로 전달할 수 있습니다. 줄 바꿈으로 분리 할 필요가 없습니다.

** 생략 된 값의 처리 **

ClickHouse 생략 된 값을 대응하는 디폴트 값으로 바꿉니다 데이터 형식 (../ sql-reference / data-types / index.md).

만약`DEFAULT expr`에 따라 다른 대체 규칙을 사용합니다. [input_format_defaults_for_omitted_fields] (../ operations / settings / settings.md # session_settings-input_format_defaults_for_omitted_fields) 설정.

다음 표를 생각합니다 :

```sql
CREATE TABLE IF NOT EXISTS example_table
(
    x UInt32,
    a DEFAULT x * 2
) ENGINE = Memory;
```

- 만약`input_format_defaults_for_omitted_fields = 0`의 기본값`x`와`a` 동일한`0`의 기본값으로`UInt32` 데이터 형식).
- 만약`input_format_defaults_for_omitted_fields = 1`의 기본값`x` 동일한`0`하지만 기본값은`a` 같은`x * 2`.

!!! note "경고"
    데이터를 삽입 할 때`insert_sample_with_metadata = 1`, ClickHouse 삽입과 비교하여 더 많은 전산 자원을 소비합니다`insert_sample_with_metadata = 0`.

### 데이터 선택 {# selecting-data}

생각`UserActivity` 예로 표 :

```text
┌──────────────UserID─┬─PageViews─┬─Duration─┬─Sign─┐
│ 4324182021466249494 │ 5 │ 146 │ -1 │
│ 4324182021466249494 │ 6 │ 185 │ 1 │
└─────────────────────┴───────────┴──────────┴──── ──┘
```

쿼리`SELECT * FROM UserActivity FORMAT JSONEachRow` 쯔づゥ쯔. :

```text
{ "UserID": "4324182021466249494", "PageViews": 5, "Duration"146 "Sign": - 1}
{ "UserID": "4324182021466249494", "PageViews": 6 "Duration": 185, "Sign": 1}
```

과는 달리 [JSON (# json) 잘못된 UTF-8 시퀀스 대체는 없습니다. 값은 다음과 같은 방법으로 탈출합니다`JSON`.

!!! note "주"
    임의의 바이트 세트를 문자열로 출력 할 수 있습니다. 사용하는`JSONEachRow` 테이블의 데이터가 정보를 잃지 않고 JSON으로 포맷 할 수있는 것이 확실한 경우 format.

### 중첩 사용 {# jsoneachrow-nested}

당신이 테이블을 가지고 있다면 [중첩] (../ sql-reference / data-types / nested-data-structures / nested.md) 데이터 형식의 열은 같은 구조로 JSON 데이터를 삽입 할 수 수 있습니다. 이 기능을 사용하려면 input_format_import_nested_json] (../ operations / settings / settings.md # settings-input_format_import_nested_json) 설정.

예를 들어, 다음 표를 생각해 봅시다 :

```sql
CREATE TABLE json_each_row_nested (n Nested (s String, i Int32)) ENGINE = Memory
```

에서 볼 수있는 것처럼`Nested` ClickHouse는 중첩 된 각 구성 요소를 별도의 라인으로 처리합니다 (`ns`와`ni` 우리 테이블에). 다음의 방법으로 데이터를 삽입 할 수 있습니다 :

```sql
INSERT INTO json_each_row_nested FORMAT JSONEachRow { "ns": "abc", "def", "ni": [1, 23]}
```

삽입 데이터로 계층 JSON 개체 [input_format_import_nested_json = 1] (../ operations / settings / settings.md # settings-input_format_import_nested_json).

```json
{
    "n": {
        "s": "abc", "def",
        "i": [1, 23]
    }
}
```

이 설정이 없으면 ClickHouse는 예외를 throw합니다.

```sql
SELECT name, value FROM system.settings WHERE name = 'input_format_import_nested_json'
```

```text
┌─name────────────────────────────┬─value─┐
│ input_format_import_nested_json │ 0 │
└─────────────────────────────────┴───────┘
```

```sql
INSERT INTO json_each_row_nested FORMAT JSONEachRow { "n": { "s": "abc", "def", "i": [1, 23]}}
```

```text
Code : 117. DB :: Exception : Unknown field found while parsing JSONEachRow format : n (at row 1)
```

```sql
SET input_format_import_nested_json = 1
INSERT INTO json_each_row_nested FORMAT JSONEachRow { "n": { "s": "abc", "def", "i": [1, 23]}}
SELECT * FROM json_each_row_nested
```

```text
┌─ns───────────┬─ni────┐
│ [ 'abc', 'def'] │ [1,23] │
└───────────────┴────────┘
```

## 네이티브 {#native}

가장 효율적인 형식. 데이터 쓰기 및 읽기를 차단 바이너리 형식입니다. 각 블록이 블록 내의 행수 열 수, 열 이름과 형태 및 라인의 일부가 차례로 기록됩니다. 즉,이 형식은 "columnar"- it does not convert columns to rows. This is the format used in the native interface for interaction between servers, for using the command-line client, and for C ++ clients.

이 형식을 사용하면 ClickHouse DBMS에서만 읽을 수있는 덤프를 빠르게 생성 할 수 있습니다. 이 형식에서 직접 작업하는 것은 의미가 없습니다.

## Null {#null}

아무것도 출력되지 않습니다. 그러나 쿼리 처리 명령 줄 클라이언트를 사용하는 경우, 데이터는 클라이언트로 전송됩니다. 이 사용을위한 시험, 성능 시험을합니다.
분명이 형식은 출력에만 적합하며, 분석에는 적합하지 않습니다.

## 예쁜 {#pretty}

출력 데이터로의 Unicode 트 테이블도 용 ANSI- 이스케이프 시퀀스 설정 색 단자입니다.
테이블의 전체 격자가 그려진 각 행은 단말의 두 행을 차지하고 있습니다.
각 결과 블록은 별도의 테이블로 출력됩니다. 이것은 결과를 버퍼링하지 않고 블록을 출력 할 수 있도록하는 데 필요합니다 (모든 값의 가시 폭을 미리 계산하기 위해 버퍼

[NULL] (../ sql-reference / syntax.md)로 출력됩니다`ᴺᵁᴸᴸ`.

예 (아래 그림 [예쁜 컴팩트 (# prettycompact) 형식) :

```sql
SELECT * FROM t_null
```

```text
┌─x─┬────y─┐
│ 1 │ ᴺᵁᴸᴸ │
└───┴──────┘
```

행 깨끗한 \ * 형식으로 이스케이프되지 않습니다. 예를 위해 표시되어 있습니다 [예쁜 컴팩트 (# prettycompact) 형식 :

```sql
SELECT 'String with \'quotes \ 'and \ t character'AS Escaping_test
```

```text
┌─Escaping_test────────────────────────┐
│ String with 'quotes'and character │
└──────────────────────────────────────┘
```

단말기에 대량의 데이터를 덤프하는 것을 방지하기 위해 첫 번째 10,000 행만 인쇄됩니다. 행 수가 10,000 이상일 경우 메시지 "Showed first 10 000"인쇄됩니다.
이 형식은 쿼리 결과의 출력에만 적합하지만 분석 (테이블에 삽입 할 데이터의 취득)에는 적합하지 않습니다.

Pretty 형식은 합계 (총 함께 사용하는 경우) 및 극값 (경우)의 출력을 지원합니다 'extremes'1)로 설정된다. 이러한 경우 합계와 극값은 메인 데이터의 후 별도의 테이블로 출력됩니다. 예 (아래 그림 [예쁜 컴팩트 (# prettycompact) 형식) :

```sql
SELECT EventDate, count () AS c FROM test.hits GROUP BY EventDate WITH TOTALS ORDER BY EventDate FORMAT PrettyCompact
```

```text
┌──EventDate─┬───────c─┐
│ 2014-03-17 │ 1406958 │
│ 2014-03-18 │ 1383658 │
│ 2014-03-19 │ 1405797 │
│ 2014-03-20 │ 1353623 │
│ 2014-03-21 │ 1245779 │
│ 2014-03-22 │ 1031592 │
│ 2014-03-23 ​​│ 1046491 │
└────────────┴─────────┘

Totals :
┌──EventDate─┬───────c─┐
│ 1970-01-01 │ 8873898 │
└────────────┴─────────┘

Extremes :
┌──EventDate─┬───────c─┐
│ 2014-03-17 │ 1031592 │
│ 2014-03-23 ​​│ 1406958 │
└────────────┴─────────┘
```

## 예쁜 컴팩트 {#prettycompact}

과는 다른 [예쁜] (# pretty) 격자 선 사이에 그려지고 결과가 더 컴팩트하게된다는 점에서.
이 형식은 대화식 모드의 명령 줄 클라이언트에서 기본적으로 사용됩니다.

## 예쁜 컴팩트 모노 블록 {#prettycompactmonoblock}

과는 다른 [예쁜 컴팩트 (# prettycompact) 최대 10,000 개의 행이 버퍼링 된 블록이 아니라 하나의 테이블로 출력됩니다.

## 예쁜 노 스케이프 {#prettynoescapes}

Ansi 이스케이프 시퀀스가 ​​사용되지 않는다는 점에서 Pretty과 다릅니다. 이것은 브라우저에서이 형식을 표시하기 위해 필요합니다. 'watch'명령 줄

예 :

```bash
$ watch -n1 "clickhouse-client --query = 'SELECT event, value FROM system.events FORMAT PrettyCompactNoEscapes'"
```

HTTP 인터페이스를 사용하여 브라우저에서 볼 수 있습니다.

### 예쁜 컴팩트 노 스케이프 {#prettycompactnoescapes}

이전 설정과 동일합니다.

### 예쁜 스파 세노 스케이프 스 {#prettyspacenoescapes}

이전 설정과 동일합니다.

## PrettySpace {#prettyspace}

과는 다른 [예쁜 컴팩트 (# prettycompact) 안에서 그리드 대신 빈 (공백)가 사용됩니다.

## 로우 이진 {#rowbinary}

바이너리 형식으로 각 행에 데이터를 서식 및 분석합니다. 행과 값은 구분 기호없이 연속적으로 표시됩니다.
이 형식은 행 기반이기 때문에 기본 형식보다 효율적으로하지 않습니다.

정수는 고정 길이 리틀 엔디안 표현을 사용합니다. 예를 들어, UInt64는 8 바이트를 사용합니다.
DateTime은 Unix 타임 스탬프를 값으로 포함 UInt32로 표현됩니다.
Date 값으로 1970-01-01 이후의 날짜를 포함한 UInt16 개체로 표현됩니다.
문자열은 varint 길이 (부호 없음)로 표시됩니다 [LEB128 (https://en.wikipedia.org/wiki/LEB128)) 후 문자열의 바이트가 계속됩니다.
FixedString는 단순히 바이트 시퀀스로 표현됩니다.

배열은 varint의 길이로 표시됩니다 (부호 없음 [LEB128 (https://en.wikipedia.org/wiki/LEB128)) 후, 배열이 연속하는 요소가 계속됩니다.

위해 [NULL] (../ sql-reference / syntax.md # null-literal) 1 또는 0을 포함한 추가 바이트가 각각의 전에 추가됩니다 [Null 가능 (../ sql-reference / data-types / nullable.md) 값. 1의 경우, 값은`NULL`이 바이트는 다른 값으로 해석됩니다. 0의 경우, 바이트 이후의 값은`NULL`.

## RowBinaryWithNamesAndTypes {#rowbinarywithnamesandtypes}

유사한 [로우 바이너리 (# rowbinary), 그러나 추가 된 헤더 :

- [LEB128 (https://en.wikipedia.org/wiki/LEB128) - 인코딩 된 열 수 (N)
- N`String`s 열 이름 지정
- N`String`s 열 유형 지정

## 값 {# data-format-values}

모든 행을 괄호로 표시합니다. 행은 쉼표로 구분됩니다. 마지막 행 다음에 쉼표가 없습니다. 괄호 안의 값은 쉼표로 구분합니다. 숫자는 따옴표없이 소수점 형식으로 출력됩니다. 배열은 대괄호로 출력됩니다. 문자열, 날짜 및 시간이있는 날짜는 따옴표로 출력됩니다. 규칙의 탈출과 분석은 [TabSeparated (# tabseparated) 형식. 포맷 중에 공백은 삽입되지 않지만, 분석 중에 허용되지 생략됩니다 (배열 값의 공백은 허용되지 않습니다). [NULL] (../ sql-reference / syntax.md)로 표시된다. `NULL`.

The minimum set of characters that you need to escape when passing data in Values ​​format : single quotes and backslashes.

이것에 의해 사용되는 형식입니다`INSERT INTO t VALUES ...`그러나 쿼리 결과 서식에도 사용할 수 있습니다.

도 참조. : [input_format_values_interpret_expressions] (../ operations / settings / settings.md # settings-input_format_values_interpret_expressions) 및 input_format_values_deduce_templates_of_expressions] (../ operations / settings / settings.md # settings-input_format_values_deduce_templates_of_expressions) 설정.

## 세로 {#vertical}

열 이름을 지정하여 각각의 값을 다른 행에 출력합니다. 이 형식은 각 행이 여러 열로 구성되는 경우 하나 또는 몇 개의 행만을 인쇄하는 데 유용합니다.

[NULL] (../ sql-reference / syntax.md)로 출력됩니다`ᴺᵁᴸᴸ`.

예 :

```sql
SELECT * FROM t_null FORMAT Vertical
```

```text
Row 1 :
──────
x : 1
y : ᴺᵁᴸᴸ
```

행은 세로 형식으로 이스케이프되지 않습니다 :

```sql
SELECT 'string with \'quotes \ 'and \ t with some special \ n characters'AS test FORMAT Vertical
```

```text
Row 1 :
──────
test : string with 'quotes'and with some special
 characters
```

이 형식은 쿼리 결과의 출력에만 적합하지만 분석 (테이블에 삽입 할 데이터의 취득)에는 적합하지 않습니다.

## VerticalRaw {#verticalraw}

유사한 수직 (# vertical) 그러나 이스케이프 무효. 이 형식은 쿼리 결과의 출력에만 적합하며 분석 (데이터의 수신 및 테이블에 삽입)에는 적합하지 않습니다.

## XML {#xml}

XML 형식은 출력에만 적합하며, 분석에는 적합하지 않습니다. 예 :

```xml
<? xml version = '1.0'encoding = 'UTF-8'?>
<result>
        <meta>
                <columns>
                        <column>
                                <name> SearchPhrase </ name>
                                <type> String </ type>
                        </ column>
                        <column>
                                <name> count () </ name>
                                <type> UInt64 </ type>
                        </ column>
                </ columns>
        </ meta>
        <data>
                <row>
                        <SearchPhrase> </ SearchPhrase>
                        <field> 8267016 </ field>
                </ row>
                <row>
                        <SearchPhrase> bathroom interior design </ SearchPhrase>
                        <field> 2166 </ field>
                </ row>
                <row>
                        <SearchPhrase> yandex </ SearchPhrase>
                        <field> 1655 </ field>
                </ row>
                <row>
                        <SearchPhrase> 2014 spring fashion </ SearchPhrase>
                        <field> 1549 </ field>
                </ row>
                <row>
                        <SearchPhrase> freeform photos </ SearchPhrase>
                        <field> 1480 </ field>
                </ row>
                <row>
                        <SearchPhrase> angelina jolie </ SearchPhrase>
                        <field> 1245 </ field>
                </ row>
                <row>
                        <SearchPhrase> omsk </ SearchPhrase>
                        <field> 1112 </ field>
                </ row>
                <row>
                        <SearchPhrase> photos of dog breeds </ SearchPhrase>
                        <field> 1091 </ field>
                </ row>
                <row>
                        <SearchPhrase> curtain designs </ SearchPhrase>
                        <field> 1064 </ field>
                </ row>
                <row>
                        <SearchPhrase> baku </ SearchPhrase>
                        <field> 1000 </ field>
                </ row>
        </ data>
        <rows> 10 </ rows>
        <rows_before_limit_at_least> 141137 </ rows_before_limit_at_least>
</ result>
```

열 이름에 허용되는 형식이없는 경우 다음과 같이합니다 'field'요소 이름으로 사용됩니다. 일반적으로 XML 구조는 JSON 구조를 따릅니다.
Just as for JSON, invalid UTF-8 sequences are changed to the replacement character so the output text will consist of valid UTF-8 sequences.

문자열 값에서는 문자`<`와`&`로 이스케이프`<`와`&`.

배열은 다음과 같이 출력되는`<array> <elem> Hello </ elem> <elem> World </ elem> ... </ array>`및 튜플로`<tuple> <elem> Hello < / elem> <elem> World </ elem> ... </ tuple>`.

## CapnProto {#capnproto}

Cap'n Proto 프로토콜 버퍼 나 절약을 닮은 이진 메시지 형식이지만, JSON 또는 MessagePack 같은 것은 없습니다.

Cap'n Proto 메시지는 강력하게 형식화 된 자체 설명이 아니기 때문에 외부 스키마 기술이 필요합니다. 스키마는 그 자리에서 적용되고 쿼리가 캐시됩니다.

```bash
$ cat capnproto_messages.bin | clickhouse-client --query "INSERT INTO test.hits FORMAT CapnProto SETTINGS format_schema = 'schema : Message'"
```

어디`schema.capnp`이 보입니다 :

```capnp
struct Message {
  SearchPhrase @ 0 : Text;
  c @ 1 : Uint64;
}
```

역 직렬화는 효과적이며 일반적으로 시스템 부하를 증가시키지 않습니다.

도 참조. 스키마 서식 (# formatschema).

## 뿌로토부후 {#protobuf}

Protobuf은 -입니다 프로토콜 (https://developers.google.com/protocol-buffers/) 형식.

이 형식은 외부 형식 스키마가 필요합니다. 이 스키마를 캐시 사이의 쿼리.
ClickHouse은 모두 지원`proto2`와`proto3` 구문. 반복 / 옵션에 필요한 분야에 대응하고 있습니다.

사용 예 :

```sql
SELECT * FROM test.table FORMAT Protobuf SETTINGS format_schema = 'schemafile : MessageType'
```

```bash
cat protobuf_messages.bin | clickhouse-client --query "INSERT INTO test.table FORMAT Protobuf SETTINGS format_schema = 'schemafile : MessageType'"
```

여기에서 파일`schemafile.proto`이 보입니다 :

```capnp
syntax = "proto3";

message MessageType {
  string name = 1;
  string surname = 2;
  uint32 birthDate = 3;
  repeated string phoneNumbers = 4;
};
```

의 대응 관계는 테이블 열 - 분야의 프로토콜 버퍼 메시지 유형 ClickHouse 비교 훈육 된 이름이 사용되고있다.
이 비교는 대소 문자를 구분하지 않습니다. `_` (밑줄)와`.` (도트)는 동일한 것으로 간주됩니다.
열의 형태와 프로토콜 버퍼 메시지 필드가 다르면 필요한 변환이 적용됩니다.

중첩 된 메시지에 대응합니다. 예를 들어, 필드의 경우`z` 다음 메시지 유형

```capnp
message MessageType {
  message XType {
    message YType {
      int32 z;
    };
    repeated YType y;
  };
  XType x;
};
```

ClickHouse는 이름있는 열을 검색하려고합니다`xyz` (또는`x_y_z` 또는`X.y_Z` 등등).
중첩 된 메시지는 중첩 데이터 구조 (../ sql-reference / data-types / nested-data-structures / nested.md).

다음과 같이 protobuf 스키마에 정의 된 기본값

```capnp
syntax = "proto2";

message MessageType {
  optional int32 result_per_page = 3 [default = 10];
}
```

적용되지 않는다. 테이블 기본값 (../ sql-reference / statements / create.md # create-default-values) 그 대신에 사용됩니다.

ClickHouse의 입력 및 출력 protobuf 메시지`length-delimited` 형식.
이것은 모든 메시지가 그 길이를 기록하기 전에 [varint (https://developers.google.com/protocol-buffers/docs/encoding#varints).
도 참조. [일반 언어에서 길이 구분 protobuf 메시지를 읽고 쓰는 방법 (https://cwiki.apache.org/confluence/display/GEODE/Delimiting+Protobuf+Messages).

## 아부로 {# data-format-avro}

[Apache Avro (http://avro.apache.org/) Apache의 Hadoop 프로젝트에서 개발 된 행 지향의 데이터 직렬화 프레임 워크입니다.

ClickHouse Avro 형식은 읽기와 쓰기를 지원합니다 [Avro 데이터 파일 (http://avro.apache.org/docs/current/spec.html#Object+Container+Files).

### 데이터 형식의 일치 {# data_types-matching}

다음 표에는 지원되는 데이터 유형과 ClickHouse과의 일치를 나타냅니다 데이터 형식 (../ sql-reference / data-types / index.md)에서`INSERT`와`SELECT` 쿼리.

| Avro 데이터 형`INSERT` | ClickHouse 데이터 형 | Avro 데이터 형`SELECT` |
| --------------------------------------------- | --- -------------------------------------------------- -------------------------------------------------- ------------ | ------------------------------ |
|`boolean``int``long``float``double` | [Int (8/16/32)] (../ sql-reference / data-types / int-uint.md) [UInt (8/16/32)] (../ sql-reference / data-types / int-uint.md) |`int` |
|`boolean``int``long``float``double` | [Int64] (../ sql-reference / data-types / int-uint.md), UInt64] (../ sql-reference / data-types / int-uint.md) |`long` |
|`boolean``int``long``float``double` | [Float32] (../ sql-reference / data-types / float.md) |`float` |
|`boolean``int``long``float``double` | [Float64] (../ sql-reference / data-types / float.md) |`double` |
|`bytes``string``fixed``enum` | [문자열] (../ sql-reference / data-types / string.md) |`bytes` |
|`bytes``string``fixed` | 고정 문자열 (N)] (../ sql-reference / data-types / fixedstring.md) |`fixed (N)`|
|`enum` | [Enum (8 월 16 일)] (../ sql-reference / data-types / enum.md) |`enum` |
|`array (T)`| [배열 (T)] (../ sql-reference / data-types / array.md) |`array (T)`|
|`union (null, T)`,`union (T, null)`| [Nullable (T)] (../ sql-reference / data-types / date.md) |`union (null, T)` |
|`null` | [Nullable (Nothing)] (../ sql-reference / data-types / special-data-types / nothing.md) |`null` |
|`int (date)`\ * | 날짜 (../ sql-reference / data-types / date.md) |`int (date)`\ * |
|`long (timestamp-millis)`\ * | [DateTime64 (3)] (../ sql-reference / data-types / datetime.md) |`long (timestamp-millis)`\ * |
|`long (timestamp-micros)`\ * | [DateTime64 (6)] (../ sql-reference / data-types / datetime.md) |`long (timestamp-micros)`\ * |

\ * [Avro 논리 유형 (http://avro.apache.org/docs/current/spec.html#Logical+Types)

지원되지 않는 Avro 데이터 형식 :`record` (비 루트),`map`

지원되지 않는 Avro 논리 데이터 형식 :`uuid``time-millis``time-micros``duration`

### 데이터 삽입 {# inserting-data-1}

Avro 파일에서 ClickHouse 테이블에 데이터를 삽입하려면 :

```bash
$ cat file.avro | clickhouse-client --query = "INSERT INTO {some_table} FORMAT Avro"
```

입력 Avro 파`record` 타입.

의 대응 관계는 테이블 열 분야의 아브 스키마 ClickHouse 비교 훈육 된 이름이 사용되고있다. 이 비교는 대소 문자를 구분합니다.
사용하지 않는 항목은 생략됩니다.

데이터 유형 ClickHouse 테이블의 열이 해당 분야에서 아브로 데이터를 삽입합니다. 데이터를 삽입 할 때 ClickHouse은 위의 표에 따라 데이터 형식을 해석 한 다음 캐스트 (../ sql-reference / functions / type-conversion-functions.md # type_conversion_function-cast) 해당 컬럼 유형 데이터.

### 데이터 선택 {# selecting-data-1}

ClickHouse 테이블에서 Avro 파일에 데이터를 선택하려면 :

```bash
$ clickhouse-client --query = "SELECT * FROM {some_table} FORMAT Avro"> file.avro
```

열 이름은 필수입니다 :

-로 시작하는`[A-Za-z_]`
- 그럼 만`[A-Za-z0-9_]`

출력 Avro 압축 및 동기화 간격 이하로 설정할 수 있습니다 [output_format_avro_codec] (../ operations / settings / settings.md # settings-output_format_avro_codec) 및 output_format_avro_sync_interval] (../ operations / settings / settings.md # settings -output_format_avro_sync_interval) 각각.

## 아브 합류 {# data-format-avro-confluent}

AvroConfluent 지원 해독 단일 개체 아브 메시지를 사용하는 [카프카] (https://kafka.apache.org/) 및 Confluent 스키마 레지스트리 (https://docs.confluent.io/current/schema-registry /index.html).

각 Avro 메시지 스키마 레지스트리를 사용하여 실제 스키마를 해결할 수있는 스키마 id가 포함됩니다.

스키마 캐시 한 번에 해결됩니다.

스키마 레지스트리의 URL은 설정된 [format_avro_schema_registry_url] (../ operations / settings / settings.md # settings-format_avro_schema_registry_url)

### 데이터 형식의 일치 {# data_types-matching-1}

같은 [아부로 (# data-format-avro)

### 사용법 {#usage}

신속한 검증 스키마 해상도를 사용할 수 [kafkacat (https://github.com/edenhill/kafkacat) 및 통 차 도구 조카 "도구 채권 쯔つ케] (../ operations / utilities / clickhouse-local. md) :

```bash
$ kafkacat -b kafka-broker -C -t topic1 -o beginning -f '% s'-c 3 | clickhouse-local --input-format AvroConfluent --format_avro_schema_registry_url 'http : // schema-registry'-S "field1 Int64, field2 String "-q 'select * from table'
1 a
2 b
3 c
```

사용하려면`AvroConfluent` 및 카프카] (../ engines / table-engines / integrations / kafka.md) :

```sql
CREATE TABLE topic1_stream
(
    field1 String,
    field2 String
)
ENGINE = Kafka ()
SETTINGS
kafka_broker_list = 'kafka-broker'
kafka_topic_list = 'topic1'
kafka_group_name = 'group1'
kafka_format = 'AvroConfluent';

SET format_avro_schema_registry_url = 'http : // schema-registry';

SELECT * FROM topic1_stream;
```

!!! note "경고"
    설정`format_avro_schema_registry_url`으로 구성해야합니다`users.xml` 재부팅 후 그 값을 유지한다.

## 마루 {# data-format-parquet}

[아파치의 세공 (http://parquet.apache.org/) Hadoop 에코 시스템에 널리 보급되어있는 주상 저장 형식입니다. ClickHouse 지원을 읽기 및 쓰기 작업을 위해이 형식입니다.

### 데이터 형식의 일치 {# data_types-matching-2}

다음 표에는 지원되는 데이터 유형과 ClickHouse과의 일치를 나타냅니다 데이터 형식 (../ sql-reference / data-types / index.md)에서`INSERT`와`SELECT` 쿼리.

| Parquet 데이터 형 (`INSERT`) | ClickHouse 데이터 형 | Parquet 데이터 형 (`SELECT`) |
| ---------------------------- | -------------------- --------------------------------------- | ---------- ------------------ |
|`UINT8``BOOL` | [UInt8] (../ sql-reference / data-types / int-uint.md) |`UINT8` |
|`INT8` | [Int8] (../ sql-reference / data-types / int-uint.md) |`INT8` |
|`UINT16` | [UInt16] (../ sql-reference / data-types / int-uint.md) |`UINT16` |
|`INT16` | [Int16] (../ sql-reference / data-types / int-uint.md) |`INT16` |
|`UINT32` | [UInt32] (../ sql-reference / data-types / int-uint.md) |`UINT32` |
|`INT32` | [Int32] (../ sql-reference / data-types / int-uint.md) |`INT32` |
|`UINT64` | [UInt64] (../ sql-reference / data-types / int-uint.md) |`UINT64` |
|`INT64` | [Int64] (../ sql-reference / data-types / int-uint.md) |`INT64` |
|`FLOAT``HALF_FLOAT` | [Float32] (../ sql-reference / data-types / float.md) |`FLOAT` |
|`DOUBLE` | [Float64] (../ sql-reference / data-types / float.md) |`DOUBLE` |
|`DATE32` | 날짜 (../ sql-reference / data-types / date.md) |`UINT16` |
|`DATE64``TIMESTAMP` | [DateTime] (../ sql-reference / data-types / datetime.md) |`UINT32` |
|`STRING``BINARY` | [문자열] (../ sql-reference / data-types / string.md) |`STRING` |
| - | [FixedString] (../ sql-reference / data-types / fixedstring.md) |`STRING` |
|`DECIMAL` | 소수 (../ sql-reference / data-types / decimal.md) |`DECIMAL` |

ClickHouse는 구성 가능한 정밀도를 지원합니다`Decimal` 타입. 그`INSERT` 쿼리는 마루를 취급`DECIMAL` ClickHouse로 입력합니다`Decimal128` 타입.

대응하고 있지 세공의 데이터 유형 :`DATE32``TIME32``FIXED_SIZE_BINARY``JSON``UUID``ENUM`.

데이터 유형 ClickHouse 테이블의 열이 해당 분야의 세공의 데이터를 삽입합니다. 데이터를 삽입 할 때 ClickHouse은 위의 표에 따라 데이터 형식을 해석 한 다음 캐스트 (../ query_language / functions / type_conversion_functions / # type_conversion_function-cast) ClickHouse 테이블 컬럼에 설정되어있는 데이터 형 데이터.

### 데이터 삽입 및 선택 {# inserting-and-selecting-data}

다음 명령을 사용하여 파일에서 파 켓 데이터를 ClickHouse 테이블에 삽입 할 수 있습니다 :

```bash
$ cat filename} | clickhouse-client --query = "INSERT INTO {some_table} FORMAT Parquet"
```

ClickHouse 테이블에서 데이터를 선택하고 다음 명령 Parquet 형식의 파일로 저장할 수 있습니다.

```bash
$ clickhouse-client --query = "SELECT * FROM {some_table} FORMAT Parquet"> {some_file.pq}
```

Hadoop과 데이터를 교환하려면 다음을 사용할 수 있습니다 [HDFS 테이블 엔진 (../ engines / table-engines / integrations / hdfs.md).

## ORC {# data-format-orc}

[Apache ORC (https://orc.apache.org/) Hadoop 에코 시스템에 널리 보급되어있는 주상 저장 형식입니다. 이 형식의 데이터는 ClickHouse에만 삽입 할 수 있습니다.

### 데이터 형식의 일치 {# data_types-matching-3}

다음 표에는 지원되는 데이터 유형과 ClickHouse과의 일치를 나타냅니다 데이터 형식 (../ sql-reference / data-types / index.md)에서`INSERT` 쿼리.

| ORC 데이터 형 (`INSERT`) | ClickHouse 데이터 형 |
| ------------------------ | ------------------------ ----------------------------- |
|`UINT8``BOOL` | [UInt8] (../ sql-reference / data-types / int-uint.md) |
|`INT8` | [Int8] (../ sql-reference / data-types / int-uint.md) |
|`UINT16` | [UInt16] (../ sql-reference / data-types / int-uint.md) |
|`INT16` | [Int16] (../ sql-reference / data-types / int-uint.md) |
|`UINT32` | [UInt32] (../ sql-reference / data-types / int-uint.md) |
|`INT32` | [Int32] (../ sql-reference / data-types / int-uint.md) |
|`UINT64` | [UInt64] (../ sql-reference / data-types / int-uint.md) |
|`INT64` | [Int64] (../ sql-reference / data-types / int-uint.md) |
|`FLOAT``HALF_FLOAT` | [Float32] (../ sql-reference / data-types / float.md) |
|`DOUBLE` | [Float64] (../ sql-reference / data-types / float.md) |
|`DATE32` | 날짜 (../ sql-reference / data-types / date.md) |
|`DATE64``TIMESTAMP` | [DateTime] (../ sql-reference / data-types / datetime.md) |
|`STRING``BINARY` | [문자열] (../ sql-reference / data-types / string.md) |
|`DECIMAL` | 소수 (../ sql-reference / data-types / decimal.md) |

ClickHouse는 구성 가능한 정밀도를 지원합니다`Decimal` 타입. 그`INSERT` 쿼리는 ORC를 취급`DECIMAL` ClickHouse로 입력합니다`Decimal128` 타입.

지원되지 않는 ORC 데이터 형식 :`DATE32``TIME32``FIXED_SIZE_BINARY``JSON``UUID``ENUM`.

ClickHouse 테이블 열의 데이터 형식은 해당 ORC 데이터 필드와 일치 할 필요는 없습니다. 데이터를 삽입 할 때 ClickHouse은 위의 표에 따라 데이터 형식을 해석 한 다음 캐스트 (../ sql-reference / functions / type-conversion-functions.md # type_conversion_function-cast) ClickHouse 테이블 열의 데이터 형식 집합으로 데이터.

### 데이터 삽입 {# inserting-data-2}

다음 명령을 사용하여 파일에서 Orc 데이터를 ClickHouse 테이블에 삽입 할 수 있습니다 :

```bash
$ cat filename.orc | clickhouse-client --query = "INSERT INTO some_table FORMAT ORC"
```

Hadoop과 데이터를 교환하려면 다음을 사용할 수 있습니다 [HDFS 테이블 엔진 (../ engines / table-engines / integrations / hdfs.md).

## 스키마 형식 {#formatschema}

형식 스키마가 포함 된 파일 이름은이 설정에 따라 설정됩니다`format_schema`.
형식 중 하나를 사용하는 경우이 설정을 설정해야합니다`Cap'n Proto`와`Protobuf`.
형식 스키마 파일 이름이 파일의 메시지 유형 이름의 조합으로 콜론으로 구분됩니다,
eg`schemafile.proto : MessageType`.
파일 형식의 표준 확장자를 가지고있는 경우 (예를 들어,`.proto` 위해`Protobuf`)
이 경우 형식 스키마는 다음과 같습니다`schemafile : MessageType`.

를 통해 데이터를 입력하거나 출력하는 경우 [클라이언트] (../ interfaces / cli.md)에서 대화 모드] (../ interfaces / cli.md # cli_usage) 형식 스키마에 지정된 파일 이름
를 포함 할 수 절대 경로 이름은 상대 경로를 현재 디렉토리의 클라이언트
클라이언트를 사용하는 경우 일괄 모드] (../ interfaces / cli.md # cli_usage)는 경로의 스키마 "상대적"으로 지정해야합니다.

를 통해 데이터를 입력하거나 출력 할 때 [HTTP 인터페이스 (../ interfaces / http.md) 형식 스키마에 지정된 파일 이름
지정된 디렉토리에 있어야합니다. [format_schema_path] (../ operations / server-configuration-parameters / settings.md # server_configuration_parameters-format_schema_path)
서버 구성.

## 스킵 오류 {#skippingerrors}

다음과 같은 형식이 있습니다`CSV``TabSeparated``TSKV``JSONEachRow``Template``CustomSeparated`와`Protobuf` 분석 에러가 발생했을 경우에 깨진 줄을 건너 뛰고 다음 행의 시작 부분에서 분석을 계속할 수 있습니다. 더보기 [input_format_allow_errors_num] (../ operations / settings / settings.md # settings-input_format_allow_errors_num)과
[input_format_allow_errors_ratio] (../ operations / settings / settings.md # settings-input_format_allow_errors_ratio) 설정.
제한 :
- 해석 에러의 경우`JSONEachRow` 새로운 행 (또는 EOF)까지 모든 데이터를 건너 뜁니다. `\ n` 오류를 정확하게 계산합니다.
-`Template`와`CustomSeparated` 마지막 열 뒤에 delimiter를 사용하여 행 사이에 delimiter를 사용하면 다음 줄의 시작을 찾을 수 있습니다.

원본 기사 (https://clickhouse.tech/docs/en/interfaces/formats/) <! - hide ->
