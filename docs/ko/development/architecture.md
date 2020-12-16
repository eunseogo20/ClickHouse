# ClickHouse 구조의 개요 {#overview-of-clickhouse-architecture}

clickHouse는 진정한 컬럼 지향 DBMS입니다. 데이터는 열별로 저장되며 배열(벡터 또는 열 청크) 실행 중에 저장됩니다. 가능할 때마다 작업은 개별 값이 아닌 배열로 전달됩니다. 이를 “벡터화 된 쿼리 실행”이라고 하며 실제 데이터 처리 비용을 줄이는 데 도움이 됩니다.

> 이 아이디어는 새로운 것이 아닙니다. APL(A programming language, 1957) 및 그 후손 : A+(APL dialect), J(1990), K(1993) 및 Q(Programming language from Kx Systems, 2003)로 거슬러 올라갑니다. 배열 프로그래밍은 과학적 데이터 처리에 사용됩니다. 이 아이디어는 관계형 데이터베이스에서도 새로운 것이 아닙니다. 예를 들어 VetorWise시스템에서 사용됩니다.(Actian Corporation의 Actian Vector Analytic Database라고도 함)

쿼리 처리 속도를 높이기 위한 두 가지 접근 방식이 있습니다. 벡터화 된 쿼리 실행과 런타임 코드 생성입니다. 후자는 모든 간접 및 동적 디스패치를 제거합니다. 이러한 접근 방식 중 어느 것도 다른 접근 방식보다 더 나은 것은 아닙니다. 런타임 코드 생성은 많은 작업을 통합하여 CPU실행 단위와 파이프 라인을 완전히 활용하면 더 좋을 수 있습니다. 벡터화 된 쿼리 실행은 캐시에 쓰고 다시 읽어야하는 임시 벡터를 포함하므로 실용성이 떨어질 수 있습니다. 임시 데이터가 L2 캐시에 맞지 않으면 문제가 됩니다. 그러나 벡터화 된 쿼리 실행은 CPU의 SIMD기능을 보다 쉽게 활용합니다. 우리 친구들이 쓴 연구 논문은 두 가지 접근 방식을 결합하는 것이 더 낫다는 것을 보여줍니다. ClickHouse는 벡터화 된 쿼리 실행을 사용하며 런타임 코드 생성에 대한 초기 지원이 제한됩니다.

## 열 {#columns} 

`IColumn`인터페이스는 메모리의 열 (실제로는 열 청크)을 나타내는 데 사용됩니다. 이 인터페이스는 다양한 관계 연산자의 구현을 위한 도우미 메서드를 제공합니다. 거의 모든 작업은 변경할 수 없습니다. 원래 열을 수정하지 않고 새로 수정 된 열을 만듭니다. 예를 들어 `OColumn :: filter` 매서드는 필터 바이트 마스크를 받습니다. 그것은 사용되는 WHERE 및 HAVING 관계 연산자, 추가 예: IColumn :: permute 지원 ORDER BY 하는 IColumn :: cut 방법, 지원 하는 방법 LIMIT.
다양한 IColumn 구현 (ColumnUInt8, columnString 등) 이 열의 메모리 레이아웃을 담당합니다. 메모리 렝ㅣ아웃은 일반적으로 연속 배열입니다. 정수 유형의 열의 경우 std:: vector. 내용 String 및 Array 모든 배열 요소들에 대한 하나, 연속적으로 배치하고, 각 어레이의 시작 오프셋을 위한 두 번째: 열 두개의 벡터이다. 또한 이 ColumnConst 그 저장 한 메모리에 값하지만 열 것 같습니다.
## 필드 {#field}

그럼에도 불구하고 개별 가치로 작업하는 것도 가능합니다. 개별 값을 나타 내기 위해 이 Field사용됩니다. Field 단지 차별 노동 조합니다. UInt64, Int64, Float64, String와 Array. IColumn갖는 Operator[] A와 n의 값을 얻을 수 있는 방법 Field 및 insert를 추가하는 방법은 Field 컬럼의 단부에 있다. 이러한 메서드는 Field개별 값을 나타내는 임시 개체를 처리해야하기 때문에 그다지 효율적이지 않습니다. 같은 보다 효율적인 방법이 있습니다. insertFrom, insertRangeFrom 등이.
`Field` 테이블의 특정 데이터 형식에 대한 충분한 정보가 없습니다. 예를 들어,`UInt8``UInt16``UInt32`, and`UInt64` 모두로 표시됩니다`UInt64`에서`Field`.
## 누출 된 추상화 {# leaky-abstractions}

`IColumn` 방법에 대한 일반적인 관계 변용의 데이터도 있습니다만, 그 모든 채운다. 예를 들어,`ColumnUInt64` 두 열의 합계를 계산하는 방법이 없습니다. `ColumnString` 부분 문자열 검색을 수행하는 방법이 없습니다. 이러한 수많은 루틴은`IColumn`.
열의 다양한 함수는 다음과 같이하여 일반적으로 비효율적 인 방법으로 구현할 수 있습니다`IColumn` 추출하는 방법`Field` 값 또는 특정 데이터의 내부 메모리 레이아웃의 지식을 사용하여 특수한 방법으로`IColumn` 구현. 에서 실시하는 주조 기능을 특정`IColumn` 내부 표현을 직접 입력하여 처리합니다. 예를 들어,`ColumnUInt64`은`getData` 내부 배열에 대한 참조를 반환하는 메서드는 다른 루틴이 직접 그 배열을 읽거나 채 웁니다. 우리는 가지고있다 "leaky abstractions"다양한 일상의 효율적인 전문화를 가능하게한다.

## 데이터 형 {#data_types}

`IDataType` 열 또는 개별 값의 청크를 바이너리 또는 텍스트 형식으로 읽고 쓸 수있는 것입니다. `IDataType` 테이블의 데이터 형에 해당합니다. 예를 들면 다음과 같습니다`DataTypeUInt32``DataTypeDateTime``DataTypeString` 등.

`IDataType`와`IColumn` 서로 완만하게 관련되어있을뿐입니다. 다른 데이터 형식은 메모리에 나타낼 수 있습니다`IColumn` 구현. 예를 들어,`DataTypeUInt32`와`DataTypeDateTime`로 표현된다. `ColumnUInt32` 또는`ColumnConstUInt32`. 또한 동일한 데이터 형식으로 표현 할수없는 한`IColumn` 구현. 예를 들어,`DataTypeUInt8`에서 표현할 수있는`ColumnUInt8` 또는`ColumnConstUInt8`.

`IDataType`화물의 메타 데이터를 지칭합니다. 예를 들어,`DataTypeUInt8` 아무것도 저장하지 않습니다 (vptr을 제외). `DataTypeFixedString` 그냥 가게`N` (고정 크기 문자열의 크기).

`IDataType` 도우미 방법에 대한 다양한 데이터 포맷 예를 들어, 쿼트 가능한 값을 직렬화하고 JSON 값을 직렬화하거나 XML 형식의 일부로 값을 serialize 할 방법이 있습니다. 데이터 형식에 대한 직접적인 지원은하지 않습니다. 예를 들어, 다른 데이터 형식`Pretty`와`TabSeparated` 동일하게 사용할 수 있습니다`serializeTextEscaped`에서 도우미 메서드`IDataType` 인터페이스

## 블록 {#block}

A`Block` 메모리 테이블의 부분 집합 (청크)를 나타내는 컨테이너입니다. 그것은 단순한 트리플 세트입니다 :`(IColumn, IDataType, column name)`쿼리가 실행되는 동안 데이터는 다음과 같은 방법으로 처리됩니다`Block`s 우리는 a가 있으면`Block`, 우리는 데이터를 가지고 (에서`IColumn` 유형에 대한 정보가 있습니다`IDataType`) 그것은 그 라인을 어떻게 처리할지 알려줍니다. 이것은 테이블의 원래 열 이름 또는 임시 계산 결과를 얻기 위해 할당 된 인공적인 이름 중 하나입니다.

블록의 열에 대해 함수를 계산할 때 그 결과를 포함한 다른 열을 블록에 추가합니다. 나중에 불필요한 열 블록에서 삭제할 수 있지만 수정할 수는 없습니다. 공통 부분 식을 제거하는 데 유용합니다.

블록 만들기위한 각 처리 청크의 데이터입니다. 동일한 유형의 계산은 열 이름과 형식은 다른 블록에서 동일하게 유지 열 데이터 만 변경되는 점에 유의하십시오. 블록 크기가 작 으면 shared_ptrs과 열 이름을 복사하기위한 임시 문자열의 오버 헤드가 높아지기 때문에 블록 헤더에서 블록 데이터를 분할

## 블록의 흐름 {# block-streams}

블록 스트림을위한 가공 데이터입니다. 를 사용하여 지금 류의 블록에서 데이터를 읽어 들이기위한 어딘가에 데이터 변환 또는 기록 데이터를 어딘가에 있다는 것입니다. `IBlockInputStream`은`read` 사용 가능한 상태에서 다음 블록을 취득하는 메소드. `IBlockOutputStream`은`write` 어딘가에 블록을 밀어 방법.

스트림 :

1. 테이블에 대한 읽기 및 쓰기. 테이블 만 반환합니다 스트림을 읽거나 쓸 블록입니다.
2. 데이터 형식의 구현. 예를 들어, 단말기에 데이터를 출력하려면`Pretty` 서식하면 블록을 밀어 블록 출력 스트림을 작성하고 서식합니다.
3. 데이터 변환을 수행합니다. 당신이 가지고 있다고하자`IBlockInputStream` 의사를 만들자는 스트림입니다. 만드는`FilterBlockInputStream` 스트림으로 초기화합니다 그 블록을 당길 때부터`FilterBlockInputStream`으로 끌어 블록에서 스트림 필터는 필터를 돌려줍니다 차단합니다. 쿼리의 실행 파이프 라인으로 표현했습니다.

보다 정교한 변환합니다. 예를 들어,`AggregatingBlockInputStream`에서 읽을 모든 데이터의 프레임 워크를 이용하여 집합체에서 그것을 돌려줍니다 스트림 집계 트에 이미 사용되고 있습니다. 다른 예 :`UnionBlockInputStream` 생성자의 여러 입력 소스와 다수의 스레드를 허용합니다. 다중 스레드를 시작하고 여러 소스에서 병렬로로드합니다.

> 블록 스트림은 "pull"제어 흐름에 접근 : 첫 번째 스트림에서 블록을 끌어하면 중첩 된 스트림에서 필요한 블록을 끌어되고 실행 파이프 라인 전체 모두 "pull"또한 "push"제어 흐름은 암시 적이며 여러 쿼리의 동시성 (많은 파이프 라인을 병합) 등의 다양한 기능 구현을 제한하기 위해 최선의 해결책이 제한은 코루찐 또는 서로를 기다리는 여분의 스레드를 실행하는 것만으로 극복 할 수 있습니다. 즉,있는 계산 단위에서 다른 측정 단위의 외부에 데이터를 전달하는 로직을 발견하고 제어 흐름을 명시 적으로하면 더 가능성이 있습니다. 이것은보기 [기사] (http://journal.stuffwithstuff.com/2013/01/13/iteration-inside-and-out/) 더 생각을.

쿼리 실행 파이프 라인의 각 단계에서 임시 데이터가 생성됩니다. 임시 데이터가 CPU 캐시에 맞게 블록 크기를 충분히 작게 해 둡니다. 그 전제는 임시 데이터의 쓰기와 읽기는 다른 계산과 비교하여 거의 무료입니다. 이것은 파이프 라인의 많은 작업을 함께 융합시키는 것입니다. 파이프 라인을 최대한 짧게하여 임시 데이터를 많이 제거 할 수 있지만,이 장점이지만 단점도 있습니다. 예를 들어, 분할 파이프 라인을 사용하면 중간 데이터 캐시 동시에 수행되는 유사한 쿼리의 중간 데이터 훔치고 유사한 쿼리 파이프 라인

## 형식 {#formats}

데이터 형식에서 실시 블록 이루어지고있다. 거기에는 "presentational"다음과 같이 클라이언트에 데이터 출력에만 적합한 형식입니다`Pretty`만을 제공하는 형식`IBlockOutputStream`. 그리고 입출력 포맷이 같은 있습니다`TabSeparated` 또는 `JSONEachRow`.

행 스트림 있습니다 :`IRowInputStream`와`IRowOutputStream` 블록이 아닌 개별 행에서 데이터를 끌어 / 푸시 할 수 있습니다. 또한 행 지향 형식의 구현을 단순화하는 데에만 필요합니다. 래퍼`BlockInputStreamFromRowInputStream`와`BlockOutputStreamFromRowOutputStream` 행 지향의 스트림을 일반 블록 지향 스트림으로 변환 할 수 있습니다.

## I / O {#io}

바이트 지향 입출력에는 다음과 같은 것들이 있습니다`ReadBuffer`와`WriteBuffer` 추상 클래스. 그들은 C ++ 대신 사용됩니다`iostream` 걱정하지 마십시오 : 모든 성숙한 C ++ 프로젝트는`iostream` 정당한 이유로 s.

`ReadBuffer`와`WriteBuffer` 단순한 연속적인 버퍼이며, 그 버퍼의 위치를 ​​가리키는 커서입니다. 구현에는없는 독자적인 메모리 버퍼입니다. 다음 데이터 버퍼를 채우기 가상 메소드가 있습니다`ReadBuffer`) 또는 버퍼를 어딘가에 플래시 (`WriteBuffer`) 가상 메소드는 드물게 호출됩니다.

구현`ReadBuffer` /`WriteBuffer` 압축을 구현하기 위해 파일과 파일 기술자와 네트워크 소켓 (`CompressedWriteBuffer` is initialized with another WriteBuffer and performs compression before writing data to it), and for other purposes - the names `ConcatReadBuffer``LimitReadBuffer`, and`HashingWriteBuffer` 자신에 대한 이야기.

Read / WriteBuffers는 바이트만을 취급합니다. 에서 함수가 있습니다`ReadHelpers`와`WriteHelpers` 입력 / 출력 포맷에 도움 헤더 파일. 예를 들어, 소수 형식의 숫자를 쓴다 도우미가 있습니다.

결과 세트를 쓰고 싶을 때 어떻게되는지를 살펴 보자`JSON` 표준 출력으로 포맷합니다. 결과 집합을받을 준비가되어 있습니다`IBlockInputStream` 만들려는`WriteBufferFromFileDescriptor (STDOUT_FILENO)`stdout에 바이트를 쓴다. 만드는`JSONRowOutputStream` 그래서 초기화됩니다`WriteBuffer` 줄을 쓰려면`JSON` 표준 출력에. 만드는`BlockOutputStreamFromRowOutputStream` 게다가, 그것을 다음과 같이 나타냅니다`IBlockOutputStream`. 그럼 전화`copyData` 데이터를 전송하려면`IBlockInputStream`에`IBlockOutputStream` 그리고 모든이 작동합니다. 내부적으로는`JSONRowOutputStream` 다양한 JSON 구분 기호를 쓰고`IDataType :: serializeTextJSON`을 참조하는 메소드`IColumn` 인수로 행 번호를 지정합니다. 그 결과`IDataType :: serializeTextJSON`에서 메소드를 호출합니다`WriteHelpers.h` : 예를 들어,`writeText` 숫자와`writeJSONString` 위해`DataTypeString`.

## 테이블 {#tables}

그`IStorage` 인터페이스입니다. 다른 구현의 인터페이스가 다른 테이블 엔진입니다. 예로는`StorageMergeTree``StorageMemory`, 등등. 이 클래스의 인스턴스

키`IStorage` 방법은`read`와`write`. 또한`alter``rename``drop`, 등등. 그`read`이 메서드는 다음 인수를 받아들입니다. `AST` 고려해야 쿼리 및 반환 스트림의 필요한 수. 하나 이상을 반환합니다`IBlockInputStream` 쿼리를 실행하는 동안 테이블 엔진에서 완료된 데이터 처리 단계에 대한 개체 정보.

대부분의 경우, read 메소드는 지정된 열을 테이블에서 읽을 수는 있지만 그 이후의 데이터 처리는 실시하지 않습니다. 모든 데이터 처리 쿼리의 통역이나 외부의 책임`IStorage`.

하지만 눈에 띄는 예외가 있습니다 :

- AST 쿼리는`read` 법에 따라 처리하고 테이블 엔진을 사용할 수있는 손가락의 이용 및 읽기 적은 데이터를 표시합니다.
- 때때로 테이블 엔진을 처리 할 수있는 데이터 자체 인 경우에도 특정 단계에있다. 예를 들어,`StorageDistributed` 원격 서버에 쿼리를 전송하고 다른 원격 서버에서 데이터를 병합 할 수있는 무대로 데이터를 처리하도록 요청하고 그 전에 처리 된 데이터를 반환이 쿼리의 통역을 마무리 가공 데이터 입니다.

테이블의`read` 방법은 여러`IBlockInputStream` 병렬 데이터 처리를 가능하게하는 개체입니다. 이러한 여러 블록의 입력 스트림에서 테이블에서 행 하였다. 그렇다면 이러한 스트림을 독립적으로 계산할 수있는 다양한 변환 (식 평가 및 필터링 등)에서 랩하고`UnionBlockInputStream` 그 위에 병렬로 여러 스트림에서 읽습니다.

또한`TableFunction` 이들은 일시적인을 반환하는 함수입니다`IStorage`에서 사용하는 오브젝트`FROM` 쿼리 절.

테이블 엔진의 구현 방법의 간단한 아이디어를 얻으려면 다음과 같은 간단한 것을 봐`StorageMemory` 또는`StorageTinyLog`.

>의 결과로`read` 방법`IStorage` 쯔づゥ쯔. `QueryProcessingStage` - information about what parts of the query were already calculated inside storage.

## 파서 {#parsers}

필기 재귀 하강 파서는 쿼리를 분석합니다. 예를 들어,`ParserSelectQuery` 쿼리의 다양한 부분에 대해 기본 파서를 재귀 적으로 호출 할뿐입니다. 파서는`AST` 그`AST` 노드로 표시됩니다. `IAST`.

> 파서 생성기는 사용하지 역사적인 이유가 있습니다.

## 통역사 {#interpreters}

인터프리터는 쿼리 실행 파이프 라인의 작성을 담당합니다. `AST`. 다음과 같은 간단한 통역이 있습니다`InterpreterExistsQuery`와`InterpreterDropQuery` 또는보다 정교한`InterpreterSelectQuery` 쿼리의 실행 파이프 라인의 조합 블록 입력 또는 출력 스트림 예`SELECT` 쿼리 `IBlockInputStream` 결과 세트를 읽고, INSERT 쿼리의 결과는 다음과 같습니다. `IBlockOutputStream`에 삽입하는 데이터를 기록하기 위해, 그리고 해석 결과`INSERT SELECT` 쿼리는`IBlockInputStream` 이것은 처음 읽을 때 빈 결과 집합을 반환하지만 데이터를 복사합니다`SELECT `에`INSERT` 동시에.

`InterpreterSelectQuery` 용도`ExpressionAnalyzer`와`ExpressionActions` 쿼리 분석 및 변환 용 기계. 여기에서는 대부분의 규칙 기반의 쿼리 최적화가 이루어집니다. `ExpressionAnalyzer` 모듈 변환이나 쿼리를 가능하게하는 다양한 쿼리 변환 및 최적화를 별도의 클래스로 추출해야합니다.

## 함수 {#functions}

일반 및 집계 함수가 있습니다. 집계 함수 내용은 다음 절을 참조하십시오.

Ordinary functions do not change the number of rows - they work as if they are processing each row independently. In fact, functions are not called for individual rows, but for`Block` 벡터화 된 쿼리 실행을 구현하기위한 데이터 .

몇 가지 다른 기능이 있습니다. [블록 크기 (../ sql-reference / functions / other-functions.md # function-blocksize), rowNumberInBlock] (../ sql-reference / functions / other-functions.md # function-rownumberinblock) and [runningAccumulate] (../ sql-reference / functions / other-functions.md # function-runningaccumulate) 그것은 블록 처리를 악용하여 행의 독립성에 위배됩니다.

ClickHouse에 강한 형식이 있기 때문에 암시 적 변환은 없습니다. 함수가 특정 형태의 조합을 지원하지 않는 경우, 예외가 슬로우됩니다. 물건의 기능 작업 과부하하에 다양한 결합합니다. 예를 들어,`plus` 함수 (구현하려면`+`연산자) 숫자의 조합에 대해 작동합니다 :`UInt8` +`Float32``UInt16` +`Int8`, 등등. 또한 일부 가변 인수 함수는 다음과 같은 임의의 수의 인수를 받아 들일 수 있습니다. `concat` 기능.

실시 기능이 조금 불편의 기능을 명시 적으로 파견 지원되는 데이터 유형과 대응`IColumns` 예`plus` 함수는 숫자의 조합마다 C ++ 템플릿의 인스턴스화에 의해 생성 된 코드 및 상수 또는 상수가 아닌 왼쪽과 오른쪽의 인수를 갖

템플릿 코드의 팽창을 피하기 위해 런타임 코드 생성을 구현하는데 최적의 장소입니다. 또한 fused multiply-add 같은 fused 함수를 추가하거나 하나의 루프 반복 다중 비교를 할 수 있습니다.

벡터화 된 쿼리를 실행하여 함수는 단락되지 않습니다. 예를 들어,`WHERE f (x) AND g (y)`양측이 계산됩니다,하지만 행에 대해 때`f (x)`가 제로인 (경우를 제외`f (x)`는 제로 상수 식 이다). 하지만 선택성이`f (x)`조건은 높고, 계산의`f (x)`보다 훨씬 저렴합니다`g (y)`다중 경로 계산을 구현하는 것이 좋습니다. 먼저 계산합니다`f (x)`다음 결과에 따라 열을 필터링 한 다음 계산합니다`g (y)`필터링 된 작은 데이터 청크 만.

## 집계 함수 {# aggregate-functions}

집계 함수는 상태 함수입니다. 전달 된 값을 상태에 축적하고 그 상태에서 결과를 얻을 수 있습니다. 그들은으로 관리됩니다`IAggregateFunction` 인터페이스 상태는 간소화 할 수 있습니다 (`AggregateFunctionCount` 단 하나입니다`UInt64` 값) 또는 매우 복잡한 (상태`AggregateFunctionUniqCombined` 선형 배열, 해시 테이블 및 a의 조합입니다`HyperLogLog` 확률 데이터 구조).

상태는`Arena` (메모리 풀) 높은 중요도를 실행하는 동안 여러 상태를 처리하는`GROUP BY` 쿼리. 예를 들어, 복잡한 집계 상태에서는 추가의 메모리를 할당 할 수 있습니다. 이것은 만들고 상태를 파괴하고 적절하게 그 소유권과 파괴 명령을 전달하기 위해 몇 가지주의가 필요합니다.

통합 상태를 직렬화 및 역 직렬화하여 분산 쿼리를 실행하는 동안 네트워크를 통해 전달하거나 충분한 RAM이없는 디스크에 쓸 수 그들은 테이블에 저장 될 수 있습니다`DataTypeAggregateFunction` 데이터의 증가 집계를 허용한다.

> 집계 함수 상태의 직렬화 된 데이터 형식은 현재 버전 관리되어 있지 않습니다. 통합 상태가 일시적으로 만 저장되어 있으면 ok입니다. 하지만 우리가 가지고있는`AggregatingMergeTree` 테이블 엔진이 늘어난 경우 집계 사람들에 따라 사용되고있다. 이것은 미래 집계 함수의 직렬화 형식을 변경할 때 하위 호환성이 필요한 이유입니다.

## 서버 {#server}

서버를 구현하고 여러 여러 인터페이스 :

- 외부 클라이언트의 HTTP 인터페이스.
- TCP 인터페이스의 기본 ClickHouse 클라이언트와 크로스 - 서버 통신 중에 분산 쿼리를 실행합니다.
- 인터페이스 전송 데이터 복제.

내부적으로는 코루찐과 섬유없는 원시적 인 멀티 스레드 서버입니다. 서버는 간단한 쿼리의 비율이 높은 것이 아니라 상대적으로 낮은 복잡한 쿼리의 비율을 처리하도록 설계되어 있기 때문에 각각 분석 놀라워

서버는 초기화합니다`Context` 쿼리 실행에 필요한 환경을 가진 클래스 : 사용 가능한 데이터베이스 사용자 및 권한 설정 클러스터 프로세스 목록 쿼리 로그 등의 목록입니다. 통역사는이 환경을 이용합니다.

이전 클라이언트는 새로운 서버로 말할 수 새 클라이언트는 이전 서버와 이야기 할 수 있습니다. 그러나 우리는 영원히 그것을 유지하고 싶지 않아, 우리는 약 일년 후에 이전 버전의 지원을 제거합니다.

!!! note "주"
    대부분의 외부 응용 프로그램에서 HTTP 인터페이스를 사용하는 것이 좋습니다. TCP 프로토콜은 데이터 블록을 통과하는 내부 형식을 사용하여 압축 된 데이터에는 사용자 정의 프레임을 사용합니다. 아직 공표 한 C 라이브러리에 대한이 프로토콜 할 필요하기 때문에 링크 ClickHouse 코드베이스,있는 것은 현실적이지 않습니다.

## 분산 쿼리 {# distributed-query-execution}

서버 클러스터 설치가 거의 독립되어 있습니다. 를 만들 수 있습니다`Distributed` 클러스터 서버의 테이블. 그`Distributed` table does not store data itself - it only provides a "view"모든 지방의 테이블에 여러 노드의 클러스터 A에서 선택하면`Distributed` 테이블은 쿼리를 다시 부하 분산 설정에 따라 원격 노드 를 선택하고 그들에게 쿼리를 보냅니다. 그`Distributed` 테이블 요청을 원격 서버 처리 쿼리만으로 최대의 속도로 중간 결과에서 다른 서버 수 있습니다. 그 중간 결과를 수신하여 병합합니다. 테이블을 배포하고 최대한의 일에 원격 서버를 전송하지 많은 중간 데이터 네트워크.

IN 또는 JOIN 절에 서브 쿼리가 각각`Distributed` 테이블. 이 쿼리는 다양한 전략이 있습니다.

분산 쿼리 실행을위한 글로벌 쿼리 계획은 없습니다. 각 노드는 작업의 일부 로컬 쿼리 계획이 있습니다. 원격 노드에서 쿼리를 보내고 결과를 병합합니다. 그러나 기수가 많은 그룹 BYs를 가진 복잡한 쿼리와 결합을위한 대량의 임시 데이터가있는 쿼리에서는이 불가능합니다. 그런 경우에는 "reshuffle"추가 조정이 필요한 서버 간의 데이터. ClickHouse 그런 쿼리의 실행을 지원하지 않습니다.

## 병합 트리 {# merge-tree}

`MergeTree` 주 키 기본 키 열 또는 표현식의 모든 튜플 할 수 있습니다. A의 데이터`MergeTree` 테이블은 "parts"각 파트는 데이터를 기본 키 순서에 저장하는 데이터는 주로 키타뿌루 따라 사전 순으로 정렬됩니다. 모든 테이블 열은 별도로 저장됩니다`column.bin` 이러한 부분 파일. 파일은 압축 블록으로 구성됩니다. 각 블록은 평균 크기에 따라 보통 64KB에서 1MB의 비 압축 데이터입니다. 블록은 열의 값이 연속하여 차례로 배치되어 있습니다. 열의 값은 각 열에서 순서가되기 위해 (기본 키에 의해 순서가 정의됩니다) 많은 열을 반복하면 해당 행의 값을 검색합니다.

기본 키 자체는 다음과 같습니다 "sparse"모든 행에 대처하는 것이 아니라 여러 범위의 데이터만을 취급합니다. 다른`primary.idx` file은 N 번째 행에 대해 기본 키 값이 있습니다. `index_granularity` (일반적으로 N = 8192). 또한 각 열에 대해 우리가 가지고있는`column.mrk` 파일 "marks"이것은 데이터 파일의 N 번째 행마다 오프셋됩니다. 각 마크는 파일의 압축 블록의 시작 부분까지의 오프셋 및 압축 해제 블록 내의 데이터의 선두까지의 오프셋 쌍입니다. 일반적으로 압축 된 블록은 마크에 정렬되어 압축 된 블록의 오프셋은 0입니다. 데이터를위한`primary.idx` 항상 메모리에 존재하고`column.mrk` 파일

우리는 일부에서 아무것도 읽을거야 때`MergeTree` 우리는`primary.idx` 데이터 요청 된 데이터를 포함 할 수있는 범위를 찾아 다음`column.mrk`이 범위를 읽기 시작하는 위치 데이터와 계산 오프셋. 희박함을 위해 여분의 데이터를 읽을 수 있습니다. ClickHouse 간단한 포인트 쿼리 고부하에는 적합하지 않습니다. `index_granularity` 키마다 행을 읽기 압축 된 블록 전체를 열마다 압축해야합니다. 우리는 인덱스 놀라운 기억력 소비없이 단일 서버마다 수조 라인을 유지할 수 있어야하므로 인덱스를 느슨하게했다. 또한 기본 키는 스파 스이기 때문에 고유하지 않습니다. 테이블에 동일한 키를 가진 많은 행을 가질 수 있습니다.

당신이`INSERT` 데이터의 무리에`MergeTree` 그 무리는 기본 키 순서로 정렬 된 새로운 부분을 형성합니다. 이 배경 스레드를 정기적으로 선택한 부분과 통합하여 하나의 정렬 부분의 부품이 상대적으로 낮다. 그것이라고 이유입니다`MergeTree` 물론 병합은 "write amplification"모든 부품은 불변이며, 생성 및 삭제 만 수행되지만, 변경되지 않습니다. SELECT가 실행되면 테이블의 스냅 샷 (부품 세트)가 유지됩니다. 병합 후에도 잠시 동안 오래된 부품을 보유하고 실패 후 회복을 촉진하기 위해 병합 된 부품이 손상되었을 가능성이있는 것으로 확인되면 원본 부품

`MergeTree` LSM 트리가 없습니다. "memtable"와 "log": inserted data is written directly to the filesystem. This makes it suitable only to INSERT data in batches, not by individual row and not very frequently - about once per second is ok, but a thousand times a second is not. we did it this way for simplicity 's sake, and because we are already inserting data in batches in our applications.

> MergeTree 테이블에는 하나의 (주) 인덱스 밖에 가질 수 없습니다. 예를 들어, 여러 물리적 순서로 데이터를 저장하거나 사전에 집계 된 데이터를 원래의 데이터를 포함하는 표현을 허용 할 수 있습니다.

백그라운드 병합 중에 추가 작업을 실시하고있다 MergeTree 엔진이 있습니다. 예로는`CollapsingMergeTree`와`AggregatingMergeTree`이 처리로 특별 지원했습니다. 왜냐하면 사용자는 일반적으로 백그라운드 병합이 수행되는 시간과 데이터를 제어 할 수 없기 때문입니다. `MergeTree` 테이블은 대부분의 경우 완전히 병합 된 형식이 아니라 여러 부분에 저장됩니다.

## 복제 {#replication}

ClickHouse에서 복제 테이블별로 구성 할 수 있습니다. 수도 복제되지 않는 일부 복제 테이블과 동일한 서버입니다. 또한 두 요소 복제 테이블과 세 요소 복제 테이블 등 다양한 방법으로 테이블을 복제 할 수 있습니다.

복제는`ReplicatedMergeTree` 스토리지 엔진의 경로`ZooKeeper` 스토리지 엔진의 매개 변수로 지정합니다. 같은 경로를 가진 모든 테이블`ZooKeeper` 서로 복제된다 : 그들은 데이터를 동기화하고 일관성을 유지한다. 복제 테이블을 만들거나 삭제하면 동적으로 추가 및 삭제할 수 있습니다.

복제를 사용하여 비동기 다중 마스터 계획입니다. 다음 세션을 가진 모든 복제본에 데이터를 삽입 할 수 있습니다`ZooKeeper` 데이터를 복제, 기타 모든 복제본은 비동기 적으로. ClickHouse 업데이트를 지원하지 않기 때문에 복제 충돌하지 않습니다. 삽입 쿼럼 확인이 없기 때문에 노드에 장애가 발생하면 삽입 된만큼의 데이터가 손실 될 수 있습니다.

복제 메타 데이터는 ZooKeeper에 저장됩니다. 수행 할 작업을 나타내는 복제 로그가 있습니다. 액션은 다음과 같습니다. 각 복제본 복제 로그를 큐에 그 행동에서 큐에 삽입합니다 예를 들어, 삽입은 "get the part"action을 만들고 로그인 복제 다운로드 있습니다. 병합 복제간에 조정 된 바이트가 동일한 결과를 얻을 수 있습니다. 모든 부품을 합병 한 경우와 마찬가지로 모든 복제본 그럼 달성을 보완하여 하나의 복제 리더로 복제를 시작으로 융합하고 씁니다 "merge parts"로그에 액션.

압축 된 부분 만 노드간에 전송 된 쿼리는 전송되지 않습니다. 합병 처리 된 각 복제본 종종 자발적으로 낮출 네트워크 비용을 피할 의한 네트워크가 증폭. 대 합병을 또한 네트워크하는 경우에 한하여 중복 제에 늦게 파급 해오고 있습니다.

또한 각 복제본이 상태를 부품과 체크섬 세트로 ZooKeeper에 저장합니다. 로컬 파일 시스템의 상태가 ZooKeeper 참조 상태에서 괴리하면 복제가 다른 복제에서 누락 된 부분이 손상된 부분을 다운로드 로컬 파일 시스템에 예기치 않은 데이터 나 손상된 데이터가있는 경우 ClickHouse은 그것을 삭제하지 않지만 다른 디렉토리로 이동하여 잊지 있습니다.

!!! note "주"
    ClickHouse 클러스터는 독립적 인 샤드로 구성된 각 샤드는 복제로 구성됩니다. 클러스터는 ** 탄성이 아니다 ** 따라서 새로운 샤드를 추가 한 후 데이터는 샤드 사이에서 자동으로 재조정되지 않습니다. 대신 클러스터 부하가 불균일하게 조정되는 것으로되어 있습니다. 이 구현은 더 많은 제어를 제공하고 수십 노드 등의 비교적 작은 클러스터에서도 ok입니다. 그러나 프로덕션 환경에서 사용하는 수백 개의 노드가있는 클러스터에서는이 방식은 심각한 단점입니다. 을 수행해야한다 "고 말했다 테이블 엔진에서 펼쳐지는 클러스터를 동적으로 재현 될 가능성이있는 지역 분할의 균형과 클러스터 시작합니다.

{## 원래 기사 (https://clickhouse.tech/docs/en/development/architecture/) ##}
