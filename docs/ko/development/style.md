---
machine_translated : true
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd
toc_priority : 68
toc_title : "C ++ \ u30B3 \ u30FC \ u30C9 \ u306E \ u66F8 \ u304D \ u65B9"
---

# C ++ 코드 작성 {# how-to-write-c-code}

## 일반적인 권장 사항 {# general-recommendations}

** 1 ** 다음은 권장 사항이며, 요구 사항은 없습니다.

** 2 ** 코드를 편집 할 경우 기존 코드의 형식을 따르는 것이 의미가 있습니다.

** 3 ** 일관성을 위해 코드 스타일이 필요합니다. 일관성하여 코드를 읽기 쉽게 코드 검색도 간단합니다.

** 4 ** 규칙의 대부분은 논리적 인 이유를 가지고 있지 않다; 그들은 확립 된 관행에 따라 결정됩니다.

## 서식 {#formatting}

** 1 ** 많은 형식이 자동으로 실행되는 것이 야`clang-format`.

** 2 ** 들여 쓰기 4 공간입니다. 탭에 공간이 추가되도록 개발 환경을 구성합니다.

** 3 ** 괄호의 시작과 끝은 다른 줄에 있어야합니다.

```cpp
inline void readBoolText (bool & x, ReadBuffer & buf)
{
    char tmp = '0';
    readChar (tmp, buf);
    x = tmp! = '0';
}
```

** 4 ** 함수 본체 전체가 하나의 경우`statement` 그것은 한 줄에 넣을 수 있습니다. 중괄호 주위에 공간을 배치합니다 (줄 공간 외에).

```cpp
inline size_t mask () const {return buf_size () - 1;}
inline size_t place (HashValue x) const {return x & mask ();}
```

** 5 ** 기능을 위해. 을주지 않는 쑥에 고정하여 사용합니다.

```cpp
void reinsert (const Value & x)
```

```cpp
memcpy (& buf [place_value, & x, sizeof (x));
```

** 6 **에서`if``for``while` 다른 식으로 (함수 호출과는 대조적으로) 시작 괄호 앞에 공백이 삽입됩니다.

```cpp
for (size_t i = 0; i <rows; i + = storage.index_granularity)
```

** 7 ** 이항 연산자의 주위에 공간을 추가 (`+`,`-``*``/``%`...) and the ternary operator`?`.

```cpp
UInt16 year = (s [0] - '0') * 1000 + (s [1] - '0') * 100 + (s [2] - '0') * 10 + (s [3] - '0 ');
UInt8 month = (s [5] - '0') * 10 + (s [6] - '0');
UInt8 day = (s [8] - '0') * 10 + (s [9] - '0');
```

** 8 ** 개행이 입력되는 경우 연산자를 새 줄에 놓고 그 전에 들여 쓰기를 늘립니다.

```cpp
if (elapsed_ns)
    message << "("
        << rows_read_on_server * 1000000000 / elapsed_ns << "rows / s."
        << bytes_read_on_server * 1000.0 / elapsed_ns << "MB / s)";
```

** 9 ** 필요에 따라 행의 정렬에 공간을 사용할 수 있습니다.

```cpp
dst.ClickLogID = click.LogID;
dst.ClickEventID = click.EventID;
dst.ClickGoodEvent = click.GoodEvent;
```

** 10 ** 연산자의 주위에 공간을 사용하지 않는`.``->`.

필요에 따라 연산자를 다음 줄로 줄 바꿈 수 있습니다. 이 경우 그 이전의 오프셋이 증가한다.

** 11 ** 단항 연산자의 구분에 공간을 사용하지 마십시오 (`--``+`,`*``&`...) from the argument.

** 12 ** 입력 후 공백, 콤마라고 할 것입니다. 동일한 규칙은 내부의 세미콜론을 위해 간다`for` 식.

** 13 ** 구분에 공간을 사용하지 마십시오`[]`운영자

** 14 **에서`template <...>`식은 사이에 공백을 사용합니다`template`과`<`; 후 공백없이`<`또는 전에`>`.

```cpp
template <typename TKey, typename TValue>
struct AggregatedStatElement
{}
```

** 15 ** 클래스와 구조체는`public``private`, and`protected`과 동일한 수준에서`class / struct` 코드의 나머지 부분을 들여 씁니다.

```cpp
template <typename T>
class MultiVersion
{
public :
    /// Version of object for usage. shared_ptr manage lifetime of version.
    using Version = std :: shared_ptr <const T>;
    ...
}
```

** 16 ** 같은 경우`namespace` 파일 전체에 사용 된 다른 중요한 것은 없습니다. `namespace`.

** 17 ** 블록이`if``for``while` 또는 다른 수식은 하나의`statement` 괄호는 생략 가능합니다. 을 넣어`statement` 다른 줄에 대신. 이 규칙은 중첩 대해서만 유효합니다`if``for``while` ...

그러나 내부의 경우`statement` 중괄호 또는`else` 외부 블록은 중괄호로 기술 할 필요가 있습니다.

```cpp
/// Finish write.
for (auto & stream : streams)
    stream.second-> finalize ();
```

** 18 ** 줄 끝에 공백이 있어서는 안됩니다.

** 19 ** 소스 파일은 UTF-8 인코딩입니다.

** 20 ** ASCII 문자는 문자열 리터럴로 사용할 수 있습니다.

```cpp
<< ""<< (timer.elapsed () / chunks_stats.hits) << "μsec / hit.";
```

** 21 ** 한 줄에 여러 식을 작성하지 마십시오.

** 22 ** 함수의 코드 섹션을 그룹화하고 빈 줄을 여러 이하로 구분합니다.

** 23 ** 함수, 클래스 등을 빈줄로 구분합니다.

** 24 **`A const` (값 관련) 형명의 전에 작성해야합니다.

```cpp
// correct
const char * pos
const std :: string & s
// incorrect
char const * pos
```

** 25 ** 포인터 또는 참조를 선언 할 때`*`와`&`기호는 양쪽의 공백으로 구분해야합니다.

```cpp
// correct
const char * pos
// incorrect
const char * pos
const char * pos
```

** 26 ** 템플릿 유형을 사용할 때는 그들을`using` 키워드 (가장 간단한 경우 제외).

즉, 템플릿 매개 변수는`using` 코드는 반복되지 않습니다.

`using` 함수 등 로컬로 선언 할 수 있습니다.

```cpp
// correct
using FileStreams = std :: map <std :: string std :: shared_ptr <Stream >>;
FileStreams streams;
// incorrect
std :: map <std :: string std :: shared_ptr <Stream >> streams;
```

** 27 ** 하나의 문장에서 다른 형식의 변수를 여러 선언하지 마십시오.

```cpp
// incorrect
int x * y;
```

** 28 ** C 스타일 캐스트는 사용하지 마십시오.

```cpp
// incorrect
std :: cerr << (int) c <<; std :: endl;
// correct
std :: cerr << static_cast <int> (c) << std :: endl;
```

** 29 ** 클래스와 구조체는 각 가시 범위 내에서 멤버와 함수를 개별적으로 그룹화합니다.

** 30 ** 작은 클래스와 구조체의 경우, 메소드 선언을 구현으로부터 분리 할 필요가 없습니다.

똑같은 클래스 나 구조체의 작은 메소드에 적용됩니다.

템플릿 화 된 클래스와 구조체의 경우, 메소드 선언을 구현으로부터 분리하지 마십시오 (그렇지 않으면 동일한 번역 단위로 정의해야

** 31 ** 행은 140 자에서 80 자 대신 줄 바꿈 수 있습니다.

** 32 ** Postfix가 필요없는 경우는 항상 접두사의 증가 / 감소 연산자를 사용합니다.

```cpp
for (Names :: const_iterator it = column_names.begin (); it! = column_names.end (); ++ it)
```

## 코멘트 {#comments}

** 1 ** 코드의 모든 아닌 사소한 부분에 주석을 추가하십시오.

이것은 매우 중요합니다. 서면으로 의견 만 업데이트 싶습니다 -이 코드에 필요한 또는 잘못된 것입니다.

```cpp
/ ** Part of piece of memory, that can be used.
  * For example, if internal_buffer is 1MB, and there was only 10 bytes loaded to buffer from file for reading,
  * then working_buffer will have size of only 10 bytes
  * (working_buffer.end () will point to position right after those 10 bytes available for read).
  * /
```

** 2 ** 댓글은 필요에 따라 상세하게 설정할 수 있습니다.

** 3 ** 기술 코드 앞에 주석을 배치합니다. 드물게 주석이 같은 라인의 코드 뒤에 올 수 있습니다.

```cpp
/ ** Parses and executes the query.
* /
void executeQuery (
    ReadBuffer & istr /// Where to read the query from (and data for INSERT, if applicable)
    WriteBuffer & ostr /// Where to write the result
    Context & context /// DB, tables, data types, engines, functions, aggregate functions ...
    BlockInputStreamPtr & query_plan /// Here could be written the description on how query was executed
    QueryProcessingStage :: Enum stage = QueryProcessingStage : Complete /// Up to which stage process the SELECT query
    )
```

** 4 ** 코멘트는 영어로 작성해야합니다.

** 5 **를 작성하고 도서관을 포함한 상세한 설명에 설명을 주로 헤더 파일입니다.

** 6 ** 추가 정보를 제공하지 않는 코멘트 추가하지 마십시오. 특히 방치하지 마십시오 하늘의 댓글이 같은 :

```cpp
/ *
* Procedure Name :
* Original procedure name :
* Author :
* Date of creation :
* Dates of modification :
* Modification authors :
* Original file name :
* Purpose :
* Intent :
* Designation :
* Classes used :
* Constants :
* Local variables :
* Parameters :
* Date of creation :
* Purpose :
* /
```

이 예는 자원에서 차용되고 있습니다 http://home.tamk.fi/~jaalto/course/coding-style/doc/unmainainable-code/.

** 7 ** 쓰레기 코멘트를 작성하지 마십시오 (작성자, 작성일 ..) 각 파일의 선두에있다.

** 8 ** 줄 주석은 슬래시로 시작합니다 :`///`여러 줄의 코멘트는`/ **`. 이러한 의견은 "documentation"

참고 : Doxygen을 사용하면 이러한 댓글에서 문서를 생성 할 수 있습니다. 그러나 Ide 코드를 탐색하는 것이 편리하므로 Doxygen은 일반적으로 사용되지 않습니다.

** 9 ** 여러 줄 주석의 시작과 끝에 빈 행을 포함 할 수 없습니다 (여러 줄 주석을 닫기 행을 제외).

** 10 ** 주석 코드는 기본적인 주석은 "documenting"코멘트.

** 11 ** 삭제 주석 처리되어 제품의 코드 깊다.

** 12 ** 댓글과 코드 욕설을 사용하지 마십시오.

** 13 ** 대문자는 사용하지 마십시오. 과도한 구두점을 사용하지 마십시오.

```cpp
/// WHAT THE FAIL ???
```

** 14 ** 사용하지 않는 코멘트를 delimeters.

```cpp
/// *********************************************** *******
```

** 15 ** 댓글에서 논의를 시작하지 마십시오.

```cpp
/// Why did you do this stuff?
```

** 16 ** 그것이 무엇인지를 설명하는 블록의 마지막에 댓글을 쓸 필요는 없습니다.

```cpp
/// for
```

## 이름 {#names}

** 1 ** 변수와 클래스 멤버의 이름에 밑줄이있는 문자를 사용합니다.

```cpp
size_t max_block_size;
```

** 2 ** 함수 (메소드)의 이름은 소문자로 시작 camelCase를 사용합니다.

```cpp
std :: string getName () const override {return "Memory";}
```

** 3 ** 클래스 (구조체)의 이름은 대문자로 시작 CamelCase를 사용합니다. I 이외의 접두사는 인터페이스는 사용되지 않습니다.

```cpp
class StorageMemory : public IStorage
```

** 4 **`using` 클래스처럼, 또는`_t` 마지막으로.

** 5 ** 템플릿 형 인수 이름 : 간단한 경우에는 다음과 같이합니다`T`;`T``U`;`T1``T2`.

더 복잡한 경우에는 클래스 이름의 규칙을 따르거나 접두사를 추가합니다`T`.

```cpp
template <typename TKey, typename TValue>
struct AggregatedStatElement
```

** 6 ** 템플릿 정수 인수의 이름 : 변수 이름 규칙에 따르거나`N` 간단한 경우는.

```cpp
template <bool without_www>
struct ExtractDomain
```

** 7 ** 추상 클래스 (인터페이스)에 대해서는`I` 접두사

```cpp
class IBlockInputStream
```

** 8 ** 변수를 로컬에서 사용하는 경우, 짧은 이름을 사용할 수 있습니다.

그렇지 않으면 의미를 설명하는 이름을 사용합니다.

```cpp
bool info_successfully_loaded = false;
```

** 9 ** 이름`define`s 및 전역 상수를 사용 ALL_CAPS을 밑줄 (_).

```cpp
#define MAX_SRC_TABLE_NAMES_TO_STORE 1000
```

** 10 ** 파일 이름은 내용과 같은 스타일을 사용해야합니다.

파일에 하나의 클래스가 포함되어있는 경우 클래스 (CamelCase)와 같은 방법으로 파일 이름을 지정합니다.

파일에 하나의 기능이 포함되어있는 경우, 함수 (camelCase)와 같은 방법으로 파일 이름을 지정합니다.

** 11 ** 이름 약어가 포함되어있는 경우 :

- 변수 이름의 경우 약어는 소문자를 사용해야합니다`mysql_connection` (안`mySQL_connection`).
- 클래스와 함수의 이름은 약어에 대문자를 사용합니다`MySQLConnection` (안`MySqlConnection`).

** 12 ** 클래스 멤버를 초기화하는 데에만 사용되는 생성자 인수는 클래스 멤버와 같은 이름을 붙일 필요가 있습니다 만, 마지막에 밑줄

```cpp
FileQueueProcessor (
    const std :: string & path_,
    const std :: string & prefix_,
    std :: shared_ptr <FileHandler> handler_)
    : path (path_)
    prefix (prefix_)
    handler (handler_)
    log (& Logger :: get ( "FileQueueProcessor"))
{
}
```

인수가 생성자 본문에서 사용되지 않은 경우 밑줄 접미사를 생략 할 수 있습니다.

** 13 ** 로컬 변수와 클래스 멤버의 이름에 차이는 없습니다 (접두사는 필요하지 않습니다).

```cpp
timer (not m_timer)
```

** 14 ** 상수에`enum` 대문자 카멜 케이스를 사용합니다. ALL_CAPS도 허용됩니다. 만약`enum`는 비 로컬이다. `enum class`.

```cpp
enum class CompressionMethod
{
    QuickLZ = 0,
    LZ4 = 1,
};
```

** 15 ** 모든 이름은 영문이어야합니다. 러시아 음역은 허용되지 않습니다.

    not Stroka

** 16 ** 약어는 잘 알려진 경우 (Wikipedia와 검색 엔진 약어의 의미를 쉽게 찾을 수있는 경우)에 허용됩니다.

    `AST``SQL`.

    Not`NVDH` (some random letters)

축소 된 일반적으로 사용되는 경우, 불완전한 단어는 허용됩니다.

댓글 옆에 성명이 포함되어있는 경우에는 약어를 사용할 수 있습니다.

** 17 ** C ++ 소스 코드를 가진 파일 이름은`.cpp` 연장. 헤더 파일은`.h` 연장.

## 코드 작성 {# how-to-write-code}

** 1 ** 메모리 관리.

수동 메모리 출시 (`delete`) 라이브러리 코드에서만 사용할 수 있습니다.

라이브러리 코드는`delete` operator는 소멸자에서만 사용할 수 있습니다.

응용

예 :

- 가장 간단한 방법은 스택에 객체를 배치하거나 다른 클래스의 구성원이 될 것입니다.
- 다수의 작은 개체의 경우 컨테이너를 사용합니다.
- 힙에 존재하는 소수의 개체가 자동으로 할당 해제하려면 다음을 사용합니다`shared_ptr / unique_ptr`.

** 2 ** 자원 관리.

사용`RAII` 위를 참조하십시오.

** 3 ** 오류 처리.

예외를 사용합니다. 대부분의 경우 예외를 throw 할뿐 그것을 잡을 필요는 없습니다 (`RAII`).

오프라인

사용자 요청을 처리하는 서버에서는 일반적으로 연결 처리기의 최상위 수준에서 예외를 잡기 충분합니다.

스레드 기능, 특수 모든 예외 rethrow 메인 스레드 후`join`.

```cpp
/// If there were not any calculations yet, calculate the first block synchronously
if (! started)
{
    calculate ();
    started = true;
}
else /// If calculations are already in progress, wait for the result
    pool.wait ();

if (exception)
    exception-> rethrow ();
```

없는 숨겨진 예외입니다. 모든 예외를 맹목적으로 기록 할뿐만 아닙니다.

```cpp
// Not correct
catch (...) {}
```

몇 가지 예외를 무시해야하는 경우는 특정 예외에 대해서만 실행하고 나머지를 다시 throw합니다.

```cpp
catch (const DB :: Exception & e)
{
    if (e.code () == ErrorCodes :: UNKNOWN_AGGREGATE_FUNCTION)
        return nullptr;
    else
        throw;
}
```

응답 코드 또는 함수를 사용하는 경우`errno` 항상 결과를 확인하고 오류의 경우는 예외를 throw합니다.

```cpp
if (0! = close (fd))
    throwFromErrno ( "Can not close file"+ file_name, ErrorCodes :: CANNOT_CLOSE_FILE);
```

`Do not use assert`.

** 4 ** 예외 타입.

응용 프로그램 코드에서 복잡한 예외 계층을 사용할 필요가 없습니다. 예외 텍스트는 시스템 관리자가 이해할 수있는 것입니다.

** 5 ** 던지기에서 예외가 발생하는 경우 destructors.

이것은 권장하지 않지만 허용하고 있습니다.

다음 옵션을 사용합니다 :

- 함수의 작성 (`done ()`또는`finalize ()`) 그것은 예외로 이어질 수있는 모든 작업을 사전에 실시합니다. 그 함수가 불려 갔을 경우 나중에 소멸자에 예외는 없을 것입니다.
- 작업도 복잡하고 (메시지를 보내는 등의 네트워크)을 넣을 수있는 다른 방법은 클래스의 사용자를 호출하기 전에 파괴.
- 소멸자에서 예외가 있으면 그것을 숨길보다 기록하는 것이 좋습니다 (로거가 사용 가능한 경우).
- 간단한 신청은 의존는 수락 가능합니다`std :: terminate` (다음의 경우`noexcept` 기본적으로 c ++ 11) 예외를 처리한다.

** 6 ** 익명 코드 블록.

특정 변수를 로컬하기 위해 하나의 함수에 다른 코드 블록을 만들어 블록을 종료 할 때 소멸자가 호출되도록해야

```cpp
Block block = data.in-> read ();

{
    std :: lock_guard <std :: mutex> lock (mutex);
    data.ready = true;
    data.block = block;
}

ready_any.set ();
```

** 7 ** 멀티 스레드

오프라인 :

- 단일 CPU 코어에서 최상의 성능을 얻고 자합니다. 필요에 따라 코드를 병렬화 할 수 있습니다.

서버 응용 프로그램 :

- 스레드 풀을 사용하여 요청을 처리합니다. 이 시점에서 아직 기 작업을 필요로하는 관리 보조 스위칭시의 값입니다. ※

Fork는 병렬화에는 사용되지 않습니다.

** 8 ** 동기화 스레드.

정지 된 수 있으며 다른 스레드가 다른 메모리 세포에 의해 다른 캐시 라인)을 사용하지 않는 스레드가 동기화를 제외`joinAll`).

동기화가 필요한 경우, 대부분의 경우 다음 조건에서 mutex를 사용하면 충분하다`lock_guard`.

다른 경우는 시스템 동기화 프리미티브를 사용합니다. Busy wait는 사용하지 마십시오.

원자 연산은 가장 간단한 경우에만 사용해야합니다.

주요 전문 분야가 아닌 한, 락 프리 데이터 구조를 구현하려고하지 마십시오.

** 9 ** 포인터 대 참조.

대부분의 경우 참조를 좋아한다.

** 10 ** const.

상수 참조 상수 포인터를 사용하는`const_iterator` 및 const 메소드.

고려`const` 기본적으로 비를 사용하려면 -`const` 필요할 때만.

변수를 값으로 전달할 때`const` 일반적으로 의미가 없습니다.

** 11 ** 무 서명

사용`unsigned` 필요한 경우.

** 12 ** 숫자.

유형을 사용하는`UInt8``UInt16``UInt32``UInt64``Int8``Int16``Int32`, and`Int64`뿐만 아니라`size_t``ssize_t`, and` ptrdiff_t`.

이러한 형식을 숫자로 사용하지 마십시오 :`signed / unsigned long``long long``short``signed / unsigned char``char`.

** 13 ** 인수를 전달합니다.

참조에 의한 복소수 값 전달 (포함`std :: string`).

함수가 힙에 생성 된 객체의 소유권을 취득하는 경우는 인수의 형태를 만듭니다`shared_ptr` 또는`unique_ptr`.

** 14 ** 반환 값.

대부분의 경우`return`. 쓰지 않는다`return std :: move (res)`.

함수가 객체를 힙에 할당하고 반환하는 경우 다음과 같이합니다`shared_ptr` 또는`unique_ptr`.

드물게 인수를 사용하여 값을 반환해야하는 경우가 있습니다. 이 경우 인수는 참조해야합니다.

```cpp
using AggregateFunctionPtr = std :: shared_ptr <IAggregateFunction>;

/ ** Allows creating an aggregate function by its name.
  * /
class AggregateFunctionFactory
{
public :
    AggregateFunctionFactory ();
    AggregateFunctionPtr get (const String & name, const DataTypes & argument_types) const;
```

** 15 ** 이름 공간.

다른 것을 사용할 필요가 없습니다`namespace` 적용 코드를위한.

작은 도서관에서도이 필요하지 않습니다.

중대형 라이브러리의 경우는 모두`namespace`.

도서관`.h` 파일 사용할 수 있습니다`namespace detail` 응용 프로그램 코드에 필요하지 구현 세부 사항을 숨길한다.

에서`.cpp`을 사용할 수 있습니다`static` 또는 기호를 숨기려면 익명의 이름 공간.

또한`namespace`에 사용할 수있는`enum` 해당 이름이 외부에 떨어지지 않게하려면`namespace` (그러나 그것을 사용하는 것이 좋습니다`enum class`).

** 16 ** lazy 초기화.

초기화에 인수가 필요한 경우는 일반적으로 기본 생성자를 작성하지 마십시오.

나중에 초기화를 지연시켜야 할 경우 잘못된 개체를 만드는 기본 생성자를 추가 할 수 있습니다. 또는 소수의 개체의 경우 다음을 사용할 수 있습니다`shared_ptr / unique_ptr`.

```cpp
Loader (DB :: Connection * connection_, const std :: string & query, size_t max_block_size_);

/// For deferred initialization
Loader () {}
```

** 17 ** 가상 함수.

클래스가 다형적인 사용을 의도하지 않는 경우, 함수를 가상 할 필요는 없습니다. 이것은 소멸자도 마찬가지입니다.

** 18 ** 인코딩

어디서나 UTF-8을 사용합니다. 사용`std :: string`와`char *`. 사용하지 않는`std :: wstring`와`wchar_t`.

** 19 ** 로깅

코드의 어디에나 예를 참조하십시오.

커밋하기 전에 무의미한 로그 및 디버그 로그 및 기타 디버그 출력을 모두 삭제합니다.

추적 레벨에서도 사이클 로그인은 피해야한다.

로그 필독 로그인합니다.

로그인 할 응용 프로그램 코드에 할 수 있습니다.

로그 메시지는 영어로 작성해야합니다.

로그는 시스템 관리자가 이해할 수 있도록하십시오.

로그에 모독을 사용하지 마십시오.

로그에서 UTF-8 인코딩을 사용합니다. 드물지만 로그에 비 ASCII 문자를 사용할 수 있습니다.

** 20 ** 입출력.

사용하지 않는`iostreams` 내부의 사이클에서 핵심적인 존재로이를위한 애플리케이션 성능 (있어 이용`stringstream`).

사용하는`DB / IO` 대신 도서관.

** 21 ** 날짜와 시간입니다.

를 참조하십시오. `DateLUT` 도서관

** 22 ** 포함한다.

항상`#pragma once` 대신 경비를 포함합니다.

** 23 **를 사용합니다.

`using namespace`는 사용되지 않습니다. 다음을 사용할 수 있습니다`using` 특정 무언가. 그러나 클래스와 함수에서 로컬로합니다.

** 24 ** 사용하지`trailing return type` 필요하지 않는 기능을 위해.

```cpp
auto f () -> void
```

** 25 ** 변수의 선언과 초기화.

```cpp
// right way
std :: string s = "Hello";
std :: string s { "Hello"};

// wrong way
auto s = std :: string { "Hello"};
```

** 26 **을위한 가상 함수를 쓰는`virtual` 기본 클래스에서는`override` 대신`virtual` 자손 클래스에서.

## C ++ 사용하지 않는 기능 {# unused-features-of-c}

** 1. ** 가상 상속은 사용되지 않습니다.

** 2 ** C ++ 03의 예외자는 사용되지 않습니다.

## 프랫 {#platform}

** 1 **을 쓰고 있어요 코드의 특정.

그것이 같은 경우에는 크로스 - 플랫폼 또는 휴대 코드가 바람직하다.

** 2 ** : C ++ 20.

** 3 ** 컴파일러 :`gcc` 2020 년 현재 코드는 버전 9.3을 사용하여 컴파일되어있다. (다음을 사용하여 컴파일 할 수 있습니다`clang 8`)

표준 라이브러리가 사용됩니다 (`libc ++`).

** 4 ** OS : Linux의 Ubuntu의 정확한보다 나이가 아닙니다.

** 5 ** 코드는 x86_64CPU 아키텍처 용으로 작성된 것입니다.

CPU 명령어 세트는 서버에서 지원되는 최소 집합입니다. 현재 SSE4.2입니다.

** 6 ** 사용`-Wall -Wextra -Werror` 컴파일 플래그.

** 7 ** 정적으로 연결하는 것이 곤란한 라이브러리를 제외한 모든 라이브러리와 정적 링크를 사용합니다. `ldd` 명령).

** 8 ** 코드 릴리스 설정에서 개발 및 디버깅됩니다.

## 도구 {#tools}

** 1 ** KDevelop 좋은 IDE입니다.

** 2 ** 디버깅 사용`gdb``valgrind` (`memcheck`)`strace``-fsanitize = ...`또는`tcmalloc_minimal_debug`.

** 3 **에 대한 프로파일 링을 사용`Linux Perf``valgrind` (`callgrind`) 또는`strace -cf`.

** 4 ** 소스는 Git에 있습니다.

** 5 ** 어셈블리의 사용`CMake`.

** 6 ** 프로그램은`deb` 패키지.

** 7 ** 함을 약속 마스터가 이길 안되는거야.

선택한 버전 만 실행 가능 간주됩니다.

** 8 ** 코드가 부분적으로 만 준비되어 있지 않고 자주 커밋합니다.

이 목적을 위해 분기를 사용합니다.

당신의 코드가`master` branch 아직 빌드 할 수는 없습니다. `push` 당신은 그것을 종료하거나 며칠 이내에 그것을 제거해야합니다.

** 9 ** 사소한 변경이 아닌 경우 지점을 사용하여 서버에 게시합니다.

** 10 ** 사용하지 않는 코드 저장소에서 삭제됩니다.

## 도서관 {#libraries}

** 1 ** C ++ 20 표준 라이브러리를 사용하고 있습니다 (실험적인 확장이 허용되어 있습니다). `boost`와`Poco` 프레임 워크

** 2 ** 필요에 따라 OS 패키지에서 사용할 수있는 기존의 라이브러리를 사용할 수 있습니다.

이미 사용할 수있는 좋은 해결책이 있다면 다른 라이브러리를 설치해야하는 경우에도 그것을 사용하십시오.

(가 준비 두십시오 거 나쁜 도서관에서 코드입니다.)

** 3 ** 패키지에 필요한 것이 없거나 이전 버전이나 잘못된 종류의 컴파일이 있으면 패키지에없는 라이브러리를 설치할 수 있습니다.

** 4 ** 라이브러리가 작고 자신의 복잡한 빌드 시스템이없는 경우에는 원본 파일을`contrib` 폴더.

** 5 ** 이미 사용 된 라이브러리가 우선됩니다.

## 일반적인 권장 사항 {# general-recommendations-1}

** 1 ** 가능한 한 적은 코드를 쓴다.

** 2 **받는 가장 간단한 솔루션입니다.

** 3 ** 어떻게 작동하고 내부 루프가 어떻게 작동하는지 알 때까지 코드를 작성하지 마십시오.

** 4 ** 가장 간단한 경우는`using` 클래스 나 구조체 대신.

** 5 ** 가능한 경우 복사 생성자, 대입 연산자, 소멸자 (클래스에 적어도 하나의 가상 함수가 포함되어있는 경우 가상 함수 제외) 스트 즉, 컴파일러에서 생성 된 함수가 제대로 작동해야합니다. 다음을 사용할 수 있습니다`default`.

** 6 ** 코드의 단순화가 권장됩니다. 가능하면 코드의 크기를 줄일 수 있습니다.

## 기타 권장 사항 {# additional-recommendations}

** 1 ** 명시 적으로 지정하는`std ::`의 유형에 대한`stddef.h`

권장되지 않습니다. 즉, 쓰는 것을 권장합니다`size_t` 대신`std :: size_t` 그것은 짧기 때문에.

추가하는 것은 허용됩니다`std ::`.

** 2 ** 명시 적으로 지정하는`std ::`표준 C 라이브러리 함수의 경우

권장되지 않습니다. 즉,`memcpy` 대신`std :: memcpy`.

그 이유는 다음과 같은 비표준적인 기능이 있기 때문입니다`memmem` 우리는 기회에이 기능을 사용합니다. 이 함수는`namespace std`.

당신이 쓰는 경우`std :: memcpy` 대신`memcpy` 어디서나 후`memmem`없이`std ::`이상하게 보입니다.

그럼에도 불구하고`std ::`당신이 그것을 좋아한다면.

** 3 ** 표준 C ++ 라이브러리에서 동일한 기능을 사용할 수있는 경우 C에서 함수를 사용합니다.

이것은 더 효율적인 경우 허용됩니다.

예를 들어, 다음을 사용합니다`memcpy` 대신`std :: copy` 메모리의 큰 덩어리를 복사하기 위하여.

** 4 ** 여러 줄의 함수의 인수.

다른 포장 스타일을 허가 :

```cpp
function (
  T1 x1,
  T2 x2)
```

```cpp
function (
  size_t left, size_t right,
  const & RangesInDataParts ranges,
  size_t limit)
```

```cpp
function (size_t left, size_t right,
  const & RangesInDataParts ranges,
  size_t limit)
```

```cpp
function (size_t left, size_t right,
      const & RangesInDataParts ranges,
      size_t limit)
```

```cpp
function (
      size_t left,
      size_t right,
      const & RangesInDataParts ranges,
      size_t limit)
```

원본 기사 (https://clickhouse.tech/docs/en/development/style/) <! - hide ->
