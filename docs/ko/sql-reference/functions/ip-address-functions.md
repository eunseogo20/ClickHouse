--- 
machine_translated : true 
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd 
toc_priority : 55 
toc_title : "IP \ u30A2 \ u30C9 \ u30EC \ u30B9 \ u306E \ u64CD \ u4F5C" 
--- 

# IP 주소를 조작하는 함수 {# functions-for-working -with-ip-addresses} 

## IPv4NumToString (num) {# ipv4numtostringnum} 

UInt32 번호를 가지고 있습니다. 빅 endian의 IPv4 주소로 해석합니다. 해당 IPv4 주소가 포함 된 문자열을 aBCd (소수점 이하의 점으로 구분 된 값) 형식으로 반환합니다. 

## IPv4StringToNum (s) {# ipv4stringtonums} 

IPv4NumToString의 역함수. IPv4 주소의 형식이 잘못된 경우는 0을 돌려줍니다. 

## IPv4NumToStringClassC (num) {# ipv4numtostringclasscnum} 

IPv4NumToString 비슷하지만 마지막 옥텟 대신 xxx를 사용합니다. 

예 : 

```sql 
SELECT 
    IPv4NumToStringClassC (ClientIP) AS k,
    count () AS c 
FROM test.hits
GROUP BY k 
ORDER BY c DESC 
LIMIT 10 
``` 

```text 
┌─k──────────────┬─────c─┐ 
│ 83.149.9.xxx │ 26238 │ 
│ 217.118.81.xxx │ 26074 │ 
│ 213.87.129.xxx │ 25481 │ 
│ 83.149.8.xxx │ 24984 │ 
│ 217.118.83.xxx │ 22797 │ 
│ 78.25.120.xxx │ 22354 │ 
│ 213.87.131 .xxx │ 21285 │ 
│ 78.25.121.xxx │ 20887 │ 
│ 188.162.65.xxx │ 19694 │ 
│ 83.149.48.xxx │ 17406 │ 
└────────────────┴ ───────┘ 
``` 

를 사용하여 이후 'xxx'매우 희귀하지만, 이것은 미래에 변경 될 수 있습니다. 이 조각의 정확한 형식에 의존하지 않는 것이 좋습니다. 

### IPv6NumToString (x) {# ipv6numtostringx}

바이너리 형식의 IPv6 주소를 포함 FixedString (16) 값을 받아들입니다. 이 주소가 포함 된 문자열을 텍스트 형식으로 반환합니다.  
┌─IPv6NumToString (ClientIP6) ──────────────┬─ ────c─┐
IPv6 매핑 된 IPv4 주소는 :: ffff : 111.222.33.44 형식으로 출력됩니다. 예 : 

```sql 
SELECT IPv6NumToString (toFixedString (unhex ( '2A0206B8000000000000000000000011') 16)) AS addr 
``` 

```text 
┌─addr─────────┐ 
│ 2a02 : 6b8 : 11 │ 
└──────────────┘ 
``` 

```sql 
SELECT 
    IPv6NumToString (ClientIP6 AS k), 
    count () AS c 
FROM hits_all 
WHERE EventDate = today () AND substring (ClientIP6, 1, 12)! = unhex ( '00000000000000000000FFFF') 
GROUP BY k 
ORDER BY c DESC 
LIMIT 10 
``` 

```text 
│ 2a02 : 2168 : aaa : bbbb : 2 │ 24695 │ 
│ 2a02 : 2698 : abcd : abcd : abcd : abcd : 8888 : 5555 │ 22408 │
│ 2a02 : 6b8 : 0 : fff :: ff │ 16389 │ 
│ 2a01 : 4f8 : 111 : 6666 : 2 │ 16016 │ 
│ 2a02 : 2168 : 888 : 222 : 1 │ 15896 │ 
│ 2a01 : 7e00 :: ffff : ffff : ffff : 222 │ 14774 │ 
│ 2a02 : 8109 : eee : ee : eeee : eeee : eeee : eeee │ 14443 │ 
│ 2a02 : 810b : 8888 : 888 : 8888 : 8888 : 8888 : 8888 │ 14345 │ 
│ 2a02 : 6b8 : 0 : 444 : 4444 : 4444 : 4444 : 4444 │ 14279 │ 
│ 2a01 : 7e00 : ffff : ffff : ffff : ffff │ 13880 │ 
└────────────────── ───────────────────────┴───────┘ 
``` 

```sql 
SELECT 
    IPv6NumToString (ClientIP6 AS k), 
    count () AS c 
FROM hits_all 
``` 
WHERE EventDate = today ()
GROUP BY k 
ORDER BY c DESC 
LIMIT 10

```text 
┌─IPv6NumToString (ClientIP6) ─┬──────c─┐ 
│ :: ffff : 94.26.111.111 │ 747440 │ 
│ :: ffff : 37.143.222.4 │ 529483 │ 
│ :: ffff : 5.166. 111.99 │ 317707 │ 
│ :: ffff : 46.38.11.77 │ 263086 │ 
│ :: ffff : 79.105.111.111 │ 186611 │ 
│ :: ffff : 93.92.111.88 │ 176773 │ 
│ :: ffff : 84.53.111.33 │ 158709 │ 
│ : : ffff : 217.118.11.22 │ 154004 │ 
│ :: ffff : 217.118.11.33 │ 148449 │ 
│ :: ffff : 217.118.11.44 │ 148243 │ 
└────────────────── ──────────┴────────┘ 
``` 

## IPv6StringToNum (s) {# ipv6stringtonums} 

IPv6NumToString의 역함수. IPv6 주소의 형식이 잘못된 경우는 null 바이트 문자열을 반환합니다. 
HEX는 대문자 또는 소문자입니다.

## IPv4ToIPv6 (x) {# ipv4toipv6x} 

을 가지고`UInt32` 번호 이것을 IPv4 주소로 해석합니다. [빅 엔디안 (https://en.wikipedia.org/wiki/Endianness). A를 반환합니다`FixedString (16)`IPv6 주소를 이진 형식으로 포함 된 값. 예 : 

```sql 
SELECT IPv6NumToString (IPv4ToIPv6 (IPv4StringToNum ( '192.168.0.1'))) AS addr 
``` 

```text 
┌─addr───────────────┐ 
│ :: ffff : 192.168.0.1 │ 
└────────────────────┘ 
``` 

## cutIPv6 (x, bytesToCutForIPv6, bytesToCutForIPv4) {# cutipv6x-bytestocutforipv6-bytestocutforipv4 } 

바이너리 형식의 IPv6 주소를 포함 FixedString (16) 값을 받아들입니다. 텍스트에서 제거 된 지정된 바이트의 주소를 포함하는 문자열을 반환합니다. 예 : 

```sql 
    IPv4ToIPv6 (IPv4StringToNum ( '192.168.0.1')) AS ipv4 
WITH
    IPv6StringToNum ( '2001 : 0DB8 : AC10 : FE01 : FEED : BABE : CAFE : F00D') AS ipv6, 
┌─IPv4CIDRToRange (toIPv4 ( '192.168.5.2') 16) ─┐
SELECT 
    cutIPv6 (ipv6, 2, 0), 
    cutIPv6 (ipv4, 0, 2) 
``` 

```text 
┌─cutIPv6 (ipv6, 2, 0) ─────────────── ──┬─cutIPv6 (ipv4, 0, 2) ─┐ 
│ 2001 : db8 : ac10 : fe01 : feed : babe : cafe : 0 │ :: ffff : 192.168.0.0 │ 
└────────── ───────────────────────────┴─────────────────────┘ 
``` 

## IPv4CIDRToRange (ipv4, Cidr) {# ipv4cidrtorangeipv4-cidr} 

IPv4 및 uint8 값을 받아들입니다. [CIDR (https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) 서브넷 낮은 범위와 높은 범위를 포함 IPv4의 튜플을 반환합니다. 

```sql 
SELECT IPv4CIDRToRange (toIPv4 ( '192.168.5.2'), 16) 
``` 

```text 
```
│ ( '192.168.0.0', '192.168.255.255') │ 
└────────────────────────────────── ──────────┘ 

## IPv6CIDRToRange (ipv6, Cidr) {# ipv6cidrtorangeipv6-cidr} 

IPv6 및 Cidr을 포함 UInt8 값을 받아들입니다. 서브넷의 하위 범위와 상위 범위를 포함 IPv6의 튜플을 반환합니다. 

```sql 
SELECT IPv6CIDRToRange (toIPv6 ( '2001 : 0db8 : 0000 : 85a3 : 0000 : 0000 : ac1f : 8001'), 32); 
``` 

```text 
┌─IPv6CIDRToRange (toIPv6 ( '2001 : 0db8 : 0000 : 85a3 : 0000 : 0000 : ac1f : 8001 ') 32) ─┐ 
│ ('2001 : db8 :: ','2001 : db8 : ffff : ffff : ffff : ffff : ffff : ffff ') │ 
└─── ────────────────────────────────────────────────── ───────────────────┘ 
``` 

## toIPv4 (문자열) {# toipv4string}

별칭`IPv4StringToNum ()`이것은 IPv4 주소의 문자열 형식을 취하고 값을 반환합니다 [IPv4] (../../ sql-reference / data-types / domains / ipv4.md) 반환 값 평등 이진`IPv4StringToNum ()`. 
 
```sql 
WITH
    '171.225.130.45'as IPv4_string 
SELECT 
    toTypeName (IPv4StringToNum (IPv4_string)) 
    toTypeName (toIPv4 (IPv4_string)) 
``` 

```text 
┌─toTypeName (IPv4StringToNum (IPv4_string)) ─ ┬─toTypeName (toIPv4 (IPv4_string)) ─┐ 
│ UInt32 │ IPv4 │ 
└───────────────────────────────── ─────────┴─────────────────────────────────┘ 
``` 

``` sql 
WITH 
    '171.225.130.45'as IPv4_string 
SELECT 
    hex (IPv4StringToNum (IPv4_string)) 
    hex (toIPv4 (IPv4_string)) 
``` 

```text 
┌─hex (IPv4StringToNum (IPv4_string)) ─┬─hex (toIPv4 (IPv4_string)) ─┐ 
│ ABE1822D │ ABE1822D │
└───────────────────────────────────┴───────────── ─────────────┘ 
``` 

## toIPv6 (문자열) {# toipv6string} 

별칭`IPv6StringToNum ()`이것은 IPv6 주소의 문자열 형식을 취하고 값을 반환 하다합니다 [IPv6] (../../ sql-reference / data-types / domains / ipv6.md) 반환 값과 같은 이진`IPv6StringToNum ()`. 

```sql 
WITH 
    '2001 : 438 : ffff : : 407d : 1bc1 'as IPv6_string 
SELECT 
    toTypeName (IPv6StringToNum (IPv6_string)) 
    toTypeName (toIPv6 (IPv6_string)) 
``` 

```text 
┌─toTypeName (IPv6StringToNum (IPv6_string)) ─┬─toTypeName (toIPv6 (IPv6_string)) ─ ┐  
│ FixedString (16) │ IPv6 │
└──────────────────────────────────────────┴───── ────────────────────────────┘ 
``` 

```sql 
WITH
    '2001 : 438 : ffff : 407d : 1bc1'as IPv6_string 
SELECT 
    hex (IPv6StringToNum (IPv6_string)), 
    hex (toIPv6 (IPv6_string)) 
``` 

```text 
┌─hex (IPv6StringToNum (IPv6_string)) ─┬─hex (toIPv6 (IPv6_string)) ─────────┐ 
│ 20010438FFFF000000000000407D1BC1 │ 20010438FFFF000000000000407D1BC1 │ 
└──────────────────────────── ───────┴──────────────────────────────────┘ 
``` 

원래 기사 (https://clickhouse.tech/docs/en/query_language/functions/ip_address_functions/) <! - hide ->
