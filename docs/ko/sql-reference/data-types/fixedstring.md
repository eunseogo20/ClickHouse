--- 
machine_translated : true 
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd 
toc_priority : 45 
toc_title : "\ u56FA \ u5B9A \ u6587 \ u5B57 \ u5217 (N)" 
--- 

# Fixedstring {#fixedstring} 

고정 길이 문자열`N` 바이트 (문자도 코드 포인트도 없다). 

열을 선언하려면`FixedString` 다음 구문을 사용합니다 : 

```sql 
<column_name> FixedString (N) 
``` 

어디`N`은 자연수이다. 

그`FixedString` 데이터가 정확하게 길이를 가지는 경우, 형태는 효율적인가요`N` 바이트 수. 다른 모든 경우에는 효율성을 저하시킬 수 있습니다. 

효율적으로 저장할 수있는 값의 예`FixedString`- 형식화 된 열 : 

- IP 주소의 이진 표현 (`FixedString (16)`IPv6의 경우). 
- Language codes (ru_RU : en_US ...) 
- Currency codes (USD, RUB ...). 
- 해시의 이진 표현 (`FixedString (16)`MD5의 경우`FixedString (32)`SHA256)을 위해.

UUID 값을 저장하려면 [UUID (uuid.md) 데이터 형식입니다. 

데이터를 삽입 할 때 ClickHouse : 

- 문자열이 포함 된 숫자가 null 바이트의 문자열을 보완합니다`N` 바이트 수. 
- 슬로우`Too large value for FixedString (N)`문자열에 다음이 포함되어있는 경우는 예외`N` 바이트 수. 

데이터를 선택하면 ClickHouse는 문자열의 끝에 null 바이트를 삭제하지 않습니다. 를 사용하는 경우`WHERE`이 절에서는 null 바이트를 수동으로 추가해야합니다. `FixedString` 값. 다음의 예에서는 다음 작업을 수행하는 방법을 보여줍니다`WHERE`과 어구`FixedString`. 

다음 표를 단일 것으로 생각하자`FixedString (2)`열 : 

``` text 
┌─name──┐ 
│ b │ 
└───────┘ 
``` 

쿼리`SELECT * FROM FixedStringTable WHERE a = 'b'` 결과 데이터를 반환하지 않습니다. 이 필터 패턴은 null 바이트까지입니다. 

```sql 
SELECT * FROM FixedStringTable 
WHERE a = 'b \ 0'
``` 

```text 
┌─a─┐ 
│ b │ 
└───┘  
```

이 동작은 MySQL과는 다릅니다. `CHAR` type (문자열은 공백으로 채워 공백은 출력을 위해 삭제됩니다). 

길이에주의하십시오. `FixedString (N)`값은 일정입니다. 그 길이 (../../ sql-reference / functions / array-functions.md # array_functions-length) 함수의 반환 값`N`있을지라도 FixedString (N)`값은 null 바이트 만 채워 하지만 빈 (../../ sql-reference / functions / string-functions.md # empty) 함수의 반환 값`1`이 경우. 

원본 기사 (https://clickhouse.tech/docs/en/data_types/fixedstring/) <! - hide ->
