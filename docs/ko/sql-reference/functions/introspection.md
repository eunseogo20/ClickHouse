--- 
machine_translated : true 
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd 
toc_priority : 65 
toc_title : "\ u5185 \ u7701" 
--- 

# 내관 함수 {# introspection-functions} 

이 장에서 설명하는 함수를 사용하여 내성 있습니다 [ELF] (https://en.wikipedia.org/wiki/Executable_and_Linkable_Format) 및 DWARF (https://en.wikipedia.org/wiki/DWARF) 쿼리 프로파일. 

!!! warning "경고" 
    이러한 기능은이 필요한 경우가 안전하게 배려 

내관 기능의 적절한 작동을 위해 : 

- 설치`clickhouse-common-static-dbg` 패키지. 

- 세트 [allow_introspection_functions (../../ operations / settings / settings.md # settings-allow_introspection_functions) 1로 설정합니다. 

        For security reasons introspection functions are disabled by default .

ClickHouse 프로파일 러 보고서를 [trace_log (../../ operations / system-tables.md # system_tables-trace_log) 시스템 테이블. 테이블 프로파일에서 설정됩니다. 

## 주소 트린 {#addresstoline} 

ClickHouse server 프로세스의 가상 메모리 주소를 ClickHouse 소스 코드의 파일명과 행 번호로 변환합니다. 

공식 ClickHouse 패키지를 사용하려면`clickhouse-common-static-dbg` 패키지. 

** 구문 ** 

```sql 
addressToLine (address_of_binary_instruction) 
``` 

** 파라미터 ** 

-`address_of_binary_instruction` (UInt64 (../../ sql-reference / data-types / int-uint.md) ) - Address of instruction in a running process. 

** 반환 값 ** 

- 소스 코드 파일 이름이 파일의 줄 번호를 콜론으로 구분합니다. 

        For example,`/ build / obj-x86_64-linux-gnu /../ src / Common / ThreadPool.cpp : 199`, where`199` is a line number.

- 함수가 디버그 정보를 찾지 못한 경우 바이너리의 이름. 

- 주소가 잘못된 경우 빈 문자열. 

유형 : 문자열 (../../ sql-reference / data-types / string.md) 

** 예 ** 

반성 기능 활성화 : 

```sql 
SET allow_introspection_functions = 1 
``` 

에서 첫 번째 문자열을 선택하는`trace_log` 시스템 테이블 : 

```sql 
SELECT * FROM system.trace_log LIMIT 1 \ G 
``` 

```text 
Row 1 : 
────── 
event_date : 2019-11- 19 
event_time : 2019-11-19 18:57:23 
revision : 54429 
timer_type : Real 
thread_number : 48 
query_id : 421b6855-1858-45a5-8f37-f383409d6d72
trace : 140658411141617,94784174532828,94784076370703,94784076372094,94784076361020,94784175007680,140658411116251,140658403895439] 
``` 

그`trace` 분야의 스택 트레이스를 즉시 샘플링합니다. 

단일 주소의 소스 코드 파일 이름과 줄 번호를 검색 : 

```sql 
SELECT addressToLine (94784076370703) \ G 
``` 

```text 
Row 1 : 
────── 
addressToLine (94784076370703) : / build /obj-x86_64-linux-gnu/../src/Common/ThreadPool.cpp:199 
``` 

스택 트레이스 전체에 대한 함수의 적용 : 

```sql 
SELECT 
    arrayStringConcat (arrayMap (x -> addressToLine (x) trace), '\ n') AS trace_source_code_lines 
FROM system.trace_log 
LIMIT 1 
\ G 
```

그 [arrayMap (higher-order-functions.md # higher_order_functions-array-map) 기능은 각각의 개별 요소를 처리하는 것을 허용합니다`trace` 의한 배열`addressToLine` 기능. 이 작업의 결과는`trace_source_code_lines` 출력 라인. 

```text 
Row 1 : 
────── 
trace_source_code_lines : /lib/x86_64-linux-gnu/libpthread-2.27.so 
/ usr / lib / debug / usr / bin / clickhouse 
/ build / obj-x86_64-linux- gnu /../ src / Common / ThreadPool.cpp : 199 
/build/obj-x86_64-linux-gnu/../src/Common/ThreadPool.h:155 
/ usr / include / c ++ / 9 / bits / atomic_base. h : 551 
/ usr / lib / debug / usr / bin / clickhouse 
/lib/x86_64-linux-gnu/libpthread-2.27.so 
/build/glibc-OTsEL5/glibc-2.27/misc/../sysdeps/unix/sysv /linux/x86_64/clone.S:97 
``` 

## addressToSymbol {#addresstosymbol}

로 변환하는 가상 메모리 주소에 ClickHouse 서버 프로세스의 심볼에서 ClickHouse 오브젝트 파일입니다. 

** 구문 ** 

```sql 
addressToSymbol (address_of_binary_instruction) 
``` 

** 파라미터 ** 

-`address_of_binary_instruction` (UInt64 (../../ sql-reference / data-types / int-uint.md) ) - Address of instruction in a running process. 

** 반환 값 ** 

- ClickHouse 오브젝트 파일의 기호. 
- 주소가 잘못된 경우 빈 문자열. 

유형 : 문자열 (../../ sql-reference / data-types / string.md) 

** 예 ** 

반성 기능 활성화 : 

```sql 
SET allow_introspection_functions = 1 
``` 

에서 첫 번째 문자열을 선택하는`trace_log` 시스템 테이블 : 

```sql 
SELECT * FROM system.trace_log LIMIT 1 \ G 
``` 

```text 
Row 1 :
────── 
event_date : 2019-11-20 
event_time : 2019-11-20 16:57:59 
revision : 54429 
timer_type : Real 
thread_number : 48 
query_id : 724028bf-f550-45aa-910d-2af6212b94ac 
trace : 94138803686098, 94138815010911,94138815096522,94138815101224,94138815102091,94138814222988,94138806823642,94138814457211,94138806823642,94138814457211,94138806823642,94138806795179,94138806796144,94138753770094,94138753771646,94138753760572,94138852407232,140399185266395,140399178045583] 
``` 

그`trace` 분야의 스택 트레이스를 즉시 샘플링합니다. 

단일 주소의 심볼 가져 오기 : 

```sql 
SELECT addressToSymbol (94138803686098) \ G 
``` 

```text 
Row 1 : 
──────
addressToSymbol (94138803686098) : _ZNK2DB24IAggregateFunctionHelperINS_20AggregateFunctionSumImmNS_24AggregateFunctionSumDataImEEEEE19addBatchSinglePlaceEmPcPPKNS_7IColumnEPNS_5ArenaE 
``` 

스택 트레이스 전체에 대한 함수의 적용 : 

```sql 
SELECT 
    arrayStringConcat (arrayMap (x -> addressToSymbol (x), trace), '\ n') AS trace_symbols 
FROM system.trace_log 
LIMIT 1 
\ G 
``` 

그 [arrayMap (higher-order-functions.md # higher_order_functions-array-map) 기능은 각각의 개별 요소를 처리하는 것을 허용합니다`trace` 의한 배열`addressToSymbols` 기능. 이 작업의 결과는`trace_symbols` 출력 라인. 

```text 
Row 1 : 
──────
trace_symbols : _ZNK2DB24IAggregateFunctionHelperINS_20AggregateFunctionSumImmNS_24AggregateFunctionSumDataImEEEEE19addBatchSinglePlaceEmPcPPKNS_7IColumnEPNS_5ArenaE 
_ZNK2DB10Aggregator21executeWithoutKeyImplERPcmPNS0_28AggregateFunctionInstructionEPNS_5ArenaE 
_ZN2DB10Aggregator14executeOnBlockESt6vectorIN3COWINS_7IColumnEE13immutable_ptrIS3_EESaIS6_EEmRNS_22AggregatedDataVariantsERS1_IPKS3_SaISC_EERS1_ISE_SaISE_EERb 
_ZN2DB10Aggregator14executeOnBlockERKNS_5BlockERNS_22AggregatedDataVariantsERSt6vectorIPKNS_7IColumnESaIS9_EERS6_ISB_SaISB_EERb 
_ZN2DB10Aggregator7executeERKSt10shared_ptrINS_17IBlockInputStreamEERNS_22AggregatedDataVariantsE 
_ZN2DB27AggregatingBlockInputStream8readImplEv 
_ZN2DB17IBlockInputStream4readEv 
_ZN2DB26ExpressionBlockInputStream8readImplEv
_ZN2DB17IBlockInputStream4readEv 
_ZN2DB26ExpressionBlockInputStream8readImplEv 
_ZN2DB17IBlockInputStream4readEv 
_ZN2DB28AsynchronousBlockInputStream9calculateEv 
_ZNSt17_Function_handlerIFvvEZN2DB28AsynchronousBlockInputStream4nextEvEUlvE_E9_M_invokeERKSt9_Any_data 
_ZN14ThreadPoolImplI20ThreadFromGlobalPoolE6workerESt14_List_iteratorIS0_E 
_ZZN20ThreadFromGlobalPoolC4IZN14ThreadPoolImplIS_E12scheduleImplIvEET_St8functionIFvvEEiSt8optionalImEEUlvE1_JEEEOS4_DpOT0_ENKUlvE_clEv 
_ZN14ThreadPoolImplISt6threadE6workerESt14_List_iteratorIS0_E 
execute_native_thread_routine 
start_thread 
clone 
``` 

## 데만구루 {#demangle}

를 사용하여 얻을 수있는 심볼을 변환합니다 [addressToSymbol (# addresstosymbol) C ++ 함수 이름의 함수. 

** 구문 ** 

```sql 
demangle (symbol) 
``` 

** 파라미터 ** 

-`symbol` (문자열 (../../ sql-reference / data-types / string.md)) - Symbol from an object file. 

** 반환 값 ** 

- C ++ 함수의 이름입니다. 
- 기호가 잘못된 경우 빈 문자열. 

유형 : 문자열 (../../ sql-reference / data-types / string.md) 

** 예 ** 

반성 기능 활성화 : 

```sql 
SET allow_introspection_functions = 1 
``` 

에서 첫 번째 문자열을 선택하는`trace_log` 시스템 테이블 : 

```sql 
SELECT * FROM system.trace_log LIMIT 1 \ G 
``` 

```text 
Row 1 : 
──────  
event_date : 2019-11- 20
event_time : 2019-11- 20 16:57:59
revision : 54429 
timer_type : Real 
thread_number : 48 
query_id : 724028bf-f550-45aa-910d-2af6212b94ac 
trace : 94138803686098,94138815010911,94138815096522,94138815101224,94138815102091,94138814222988,94138806823642,94138814457211,94138806823642,94138814457211,94138806823642,94138806795179,94138806796144,94138753770094 , 94138753771646,94138753760572,94138852407232,140399185266395,140399178045583] 
``` 

그`trace` 분야의 스택 트레이스를 즉시 샘플링합니다. 

단일 주소의 함수 이름 검색 : 

```sql 
SELECT demangle (addressToSymbol (94138803686098)) \ G 
``` 

```text 
Row 1 : 
──────
demangle (addressToSymbol (94138803686098)) : DB :: IAggregateFunctionHelper <DB :: AggregateFunctionSum <unsigned long, unsigned long, DB :: AggregateFunctionSumData <unsigned long>>> :: addBatchSinglePlace (unsigned long, char *, DB :: IColumn const * * DB :: Arena *) const 
``` 

스택 트레이스 전체에 대한 함수의 적용 : 

```sql 
SELECT 
    arrayStringConcat (arrayMap (x -> demangle (addressToSymbol (x)), trace), '\ n') AS trace_functions 
FROM system.trace_log 
LIMIT 1 
\ G 
``` 

그 [arrayMap (higher-order-functions.md # higher_order_functions-array-map) 기능은 각각의 개별 요소를 처리하는 것을 허용합니다`trace`에 따르면 배열`demangle` 기능. 이 작업의 결과는`trace_functions` 출력 라인. 

```text 
Row 1 : 
──────
trace_functions : DB :: IAggregateFunctionHelper <DB :: AggregateFunctionSum <unsigned long, unsigned long, DB :: AggregateFunctionSumData <unsigned long>>> :: addBatchSinglePlace (unsigned long, char *, DB :: IColumn const **, DB :: Arena *) const 
DB :: Aggregator : executeWithoutKeyImpl (char * &, unsigned long, DB :: Aggregator : AggregateFunctionInstruction * DB :: Arena *) const 
DB :: Aggregator : executeOnBlock (std :: vector <COW <DB :: IColumn> :: immutable_ptr <DB :: IColumn> std :: allocator <COW <DB :: IColumn> :: immutable_ptr <DB :: IColumn>>> unsigned long, DB :: AggregatedDataVariants & std :: vector <DB :: IColumn const * std :: allocator <DB :: IColumn const *>> & std :: vector <std :: vector <DB :: IColumn const * std :: allocator <DB :: IColumn const *>> std :: allocator <std ::vector <DB :: IColumn const * std :: allocator <DB :: IColumn const *>>>> &, bool &) 
DB :: Aggregator : executeOnBlock (DB :: Block const & DB를 : : AggregatedDataVariants & std :: vector <DB :: IColumn const * std :: allocator <DB :: IColumn const *>> & std :: vector <std :: vector <DB :: IColumn const * std :: allocator <DB :: IColumn const *>> std :: allocator <std :: vector <DB :: IColumn const * std :: allocator <DB :: IColumn const *>>>> &, bool &) 
DB :: Aggregator :: execute (std :: shared_ptr <DB :: IBlockInputStream> const &, DB :: AggregatedDataVariants &)
DB :: AggregatingBlockInputStream :: readImpl () 
DB :: IBlockInputStream :: read () 
DB :: ExpressionBlockInputStream :: readImpl () 
DB :: IBlockInputStream :: read () 
DB :: ExpressionBlockInputStream :: readImpl () 
DB :: IBlockInputStream : : read () 
DB :: AsynchronousBlockInputStream : calculate () 
ThreadFromGlobalPool :: ThreadFromGlobalPool <ThreadPoolImpl <ThreadFromGlobalPool> :: scheduleImpl <void> (std :: function < void ()>, int std :: optional <unsigned long>) :: {lambda () # 3}> (ThreadPoolImpl <ThreadFromGlobalPool> :: scheduleImpl <void> (std :: function <void ()>, int, std :: optional <unsigned long>) :: {lambda () # 3} &&) : {lambda () # 1} ::operator () () const 
std :: _ Function_handler <void (), DB :: AsynchronousBlockInputStream :: next () : {lambda () # 1}> ::_M_invoke (std :: _ Any_data const &)
ThreadPoolImpl <ThreadFromGlobalPool> :: worker (std :: _ List_iterator <ThreadFromGlobalPool>)
ThreadPoolImpl <std :: thread> :: worker (std :: _ List_iterator <std :: thread>) 
execute_native_thread_routine 
start_thread 
clone 
```
