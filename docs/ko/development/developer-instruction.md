--- 
machine_translated : true 
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd 
toc_priority : 61 
toc_title : "\ u521D \ u5FC3 \ u8005ClickHouse \ u958B \ u767A \ u8005 \ u306E \ u6307 \ u793A" 
--- 

ClickHouse 빌드는 Linux, FreeBSD, Mac OS X에서 지원 되어 있습니다. 

# Windows를 사용하는 경우 {# if-you-use-windows} 

Windows를 사용하는 경우 Ubuntu에서 가상 머신을 생성해야합니다. 하는 가상 머신을 설치하십시오 VirtualBox 다운로드 할 수 있습니다 Ubuntu 웹 사이트 : https : //www.ubuntu.com/#download.를 작성하십시오 가상 머신에서 다운로드 한 이미지를 유지 적어도 4GB RAM 잡아줍니다. Ubuntu에서 명령 줄 터미널을 실행하려면 해당 단어를 포함하는 프로그램을 찾아보세요 "terminal"그 이름 (gnome-terminal, konsole 등) 또는 단순히 Ctrl + Alt + T를 누릅니다. 

# 32 비트 시스템을 사용하는 경우 {# if-you-use-a-32-bit-system} 

ClickHouse 수없는 일을 32 비트 시스템. 키노 획득에 대한 액세스는 64 비트 시스템을 계속할 수있다.

# GitHub의 저장소를 생성 {# creating a-repository-on-github} 

ClickHouse repository에서 시작하려면 GitHub 계정이 필요합니다. 

아마 이미 가지고 있지만, 그렇지 않은 경우 등록하십시오 https : //github.com.SSH 키가 없으면이를 생성하고 GitHub에 업로드해야합니다. 당신의 패치를 보낼 필요합니다. 다른 SSH 서버에서 사용하는 것과 같은 SSH 키를 사용할 수 있습니다. 

ClickHouse 저장소 포크를 만듭니다. 그렇게는 "fork"오른쪽 상단의 버튼 https://github.com/ClickHouse/ClickHouse 그것은 당신의 계정에 ClickHouse / ClickHouse의 자체 복사본을 포크합니다. 

개발 프로세스는 처음 의도 한 변경을 ClickHouse 포크에 커밋 한 다음 "pull request"이러한 변경을 메인 저장소 (ClickHouse / ClickHouse)에 동의합니다. 

작 git 저장소를 설치하십시오`git`. 

Ubuntu에서 이렇게 명령 줄 터미널에서 실행합니다 : 

    sudo apt update 
    sudo apt install git 

간단한 설명서를 사용 Git에서 가능한 한 빨리 보내주십시오 https : //education.github.com/git-cheat-sheet-education.pdf.
자세한 설명서 Git보고 https://git-scm.com/book/en/v2. 

# 개발 시스템에 저장소의 복제 {# cloning-a-repository-to-your-development-machine} 

그런 다음 소스 파일 이 작업 시스템에 다운로드해야합니다. 이것은 "to clone a repository"작업 시스템에 저장소의 로컬 복사본을 만들 수 있습니다. 

명령 줄 터미널에서 실행 : 

    git clone --recursive git@github.com : your_github_username / ClickHouse.git 
    cd ClickHouse 

참고 : 대리 해주세요 * your_github_username * 적절한 것! 

이 명령 디렉터리 만들기`ClickHouse` 프로젝트의 작업 복사본을 포함한다. 

빌드 시스템의 실행에 문제가 발생할 수 있기 때문에 작업 디렉토리 경로에 공백이 포함되지 않은 것이 중요합니다.

ClickHouse 저장소는 다음을 사용합니다`submodules`. That is what the references to additional repositories are called (ie external libraries on which the project depends). It means that when cloning the repository you need to specify the `--recursive` 위의 예와 같이 플래그. 경우 저장소에서 복제 한 submodules, 다운로드를 실행해야합니다거야 :

    git submodule init 
    git submodule update 

명령으로 상태를 확인할 수 있습니다 :`git submodule status`. 

다음 오류 메시지가 나타날 : 

    Permission denied (publickey). 
    fatal : Could not read from remote repository. 

    Please make sure you have the correct access rights 
    and the repository exists. 

일반적으로 GitHub에 연결하기위한 SSH 키가 없다는 것을 의미합니다. 이 키는 일반적으로`~ / .ssh`. SSH 키를 받아들이는 GitHub UI 설정 섹션에 업로드해야합니다.  
원래 ClickHouse 보고서의 주소를 로컬 저장소에 추가하여 거기에서 업데이트 를 얻을 수 있습니다 :

또한 복제를 저장소에 의해 https 프로토콜 : 

    git clone https://github.com/ClickHouse/ClickHouse.git 

그러나 변경을 서버에 보낼 수 없습니다. 에도 그대로 사용했던 단지 일시적으로 메모리의 사용 범위의 크기는 SSH 키 후 교환 원격 주소 저장소`git remote` 명령 


    git remote add upstream git@github.com : ClickHouse / ClickHouse.git 

이 명령 을 성공적으로 실행하면 다음과 같이 기본 ClickHouse 저장소에서 업데이트를 검색 할 수 있습니다`git pull upstream master`. 

## 서브 모듈의 조작 {# working-with-submodules} 

git에서 서브 모듈을 조작하는 고통입니다. 다음 명령은 관리 할 수 있습니다 : 

    #! each command accepts --recursive 
    # Update remote URLs for submodules. Barely rare case 
    git submodule sync 
    # Add new submodules 
    git submodule init 
    # Update existing submodules to the current state 
    git submodule update
    # Two last commands could be merged together 
    git submodule update --init 

다음 명령은 모든 서브 모듈을 초기 상태로 재설정하는 데 도움이됩니다 (! 쯔づ쯔つ귤 나무 - 내부의 변경은 삭제됩니다) : 
 
    # Synchronizes submodules ' remote URL with .gitmodules
    git submodule sync --recursive 
    # update the registered submodules with initialize not yet initialized 
    git submodule update --init --recursive 
    # Reset all changes done after HEAD 
    git submodule foreach git reset --hard 
    # Clean files from .gitignore 
    git submodule foreach git clean -xfd 
    # Repeat last 4 commands for all submodule 
    git submodule foreach git submodule sync --recursive 
    git submodule foreach git submodule update --init --recursive
    git submodule foreach git submodule foreach git reset --hard 
    git submodule foreach git submodule foreach git clean -xfd 

# 빌드 시스템 {# build-system} 

ClickHouse는 구축을위한 CMake와 닌자를 사용하고 있습니다. 

CMake- 닌자 파일 (빌드 작업)을 생성 할 수있는 메타 빌드 시스템.
닌자 -이 cmake 생성 된 작업을 수행하는 데 사용되는 속도에 초점을 맞춘 작은 빌드 시스템. 

Ubuntu, Debian 또는 Mint run 설치하려면`sudo apt install cmake ninja-build`. 

센토스에서는 레드햇 런`sudo yum install cmake ninja-build`. 

Arch 또는 Gentoo를 사용하는 경우는 아마 CMake 설치 방법을 스스로 알고 있습니다. 

위한 CMake 및 닌자 Mac OS X 처음 자작으로 설치 설치 씨에 의해 brew : 

    / usr / bin / ruby -e "$ (curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install ) " 
    brew install cmake ninja 

다음은 cMake 버전을 확인합니다 :`cmake --version` 3.3 미만의 경우는 web 사이트에서 최신 버전을 설치하십시오. https://cmake.org/download/. 

# 모든 외부 라이브러리 {# optional-external-libraries} 

ClickHouse 빌드에 여러 외부 라이브러리를 사용합니다. 그 모든 서브 모듈의 소스에서 ClickHouse과 함께 구축되어 있기 때문에 따로 설치할 필요가 없습니다. 목록은 다음 위치에서 확인할 수 있습니다`contrib`.

# C ++ 컴파일러 {# c-compiler} 

ClickHouse 빌드 버전 9 이후 GCC와 Clang 버전 8의 컴파일러가 지원됩니다. 

공식 Yandex 빌드는 약간 뛰어난 성능의 기계 코드를 생성하기 위해 GCC를 사용하고 있습니다 (우리의 벤치 마크에 따라 최대 퍼센트 그리고 Clang은 개발을 위해 평소보다 유용합니다.이 우리의 지속적인 통합 (CI) 플랫폼을 운 체크를위한 십여 조합입니다. 

UBUNTU에 GCC를 설치하려면`sudo apt install gcc g ++` 

Gcc 버전을 확인 :`gcc - version`.의 경우 다음 9 그 지시에 따를 .https : //clickhouse.tech/docs/ja/development/build/#install-gcc-10. 

Mac OS X 빌드 Clang에서만 지원됩니다. 다만 실행`brew install llvm` 

Clang을 사용할 경우 다음도 설치할 수 있습니다`libc ++`와`lld` 당신이 그것이 무엇인지 안다면.을 사용하여`ccache` 또한 추천 하다합니다. 
    cd build

# 건축 프로세스 {# the-building-process} 

ClickHouse를 구축 준비 때문에 다른 디렉토리를 만드는 것이 좋습니다`build` 내부`ClickHouse` 그것은 모든 빌드 인공물이 포함되어 하다합니다 : 

    mkdir build 

여러 가지 디렉토리 (build_release, build_debug 등)을 가질 수 있습니다. ) 빌드의 다른 유형을 위해. 

중간에`build` cmake를 실행하여 빌드를 구성합니다. 처음 실행하기 전에 컴파일러 (이 예에서는 버전 9gcc 컴파일러)를 지정하는 환경 변수를 정의해야합니다. 

Linux : 

    export CC = gcc-10 CXX = g ++ - 10 
    cmake .. 

Mac OS X : 

    export CC = clang CXX = clang ++ 
    cmake .. 

그`CC` 변수는 C 컴파일러를 지정합니다 (c 컴파일러의 약자) . `CXX` variable은 어떤 C ++ 컴파일러를 빌드하는 데 사용하는 방법을 알려줍니다. 

빠른 빌드를 위해 당신은`debug` 빌드 타입 - 최적화없는 빌드. 그 공급은 다음 매개 변수`-D CMAKE_BUILD_TYPE = Debug` : 

    cmake -D CMAKE_BUILD_TYPE = Debug .. 

빌드 유형을 변경하려면 다음 명령을 실행합니다. `build` 디렉토리.

닌자를 실행 구축 : 

    ninja clickhouse-server clickhouse-client 
 
이 예에서는 필요한 바이너리 만 구축합니다.

필요한 경우 구축 모든 바이너리 (광열비 및 시험)를 움직여 갈 필요가 닌자없는 매개 변수 : 

    ninja 

전체의 구축이 필요 약 30GB 디스크 공간 또는 15GB의 주요 binaries. 

빌드 시스템에서 대량의 RAM을 사용할 수있는 경우는 다음과 병행하여 실행되는 빌드 작업의 수를 제한해야합니다`-j` param : 

    ninja -j 1 clickhouse-server clickhouse-client 

4GB의 RAM을 탑재하는 시스템에서는 1 8GB의 RAM에 지정하는 것이 좋습니다`-j 2` 권장됩니다. 

메시지가 도착하면`ninja : error : loading 'build.ninja': No such file or directory` 이것은 빌드 구성의 생성이 실패하고 위의 메시지를 검사 할 필요가 있다는 것을 의미합니다. 

빌드 프로세스가 성공적으로 시작되면 빌드의 진행 상황, 즉 처리 된 작업 수와 작업의 수를 표시합니다. 

하면서 메시지 protobuf 파일 libhdfs2 도서관 같은`libprotobuf WARNING` 나올지도 그들은 아무런 영향을주지 않고 무시하는 것이 안전합니다. 

빌드가 성공하면 실행 파일을 가져옵니다`ClickHouse / <build_dir> / programs / clickhouse` :

    ls -l programs / clickhouse 

# ClickHouse 빌드 된 실행 파일의 실행 {# running-the-built-executable-of-clickhouse} 

현재 사용자에서 서버를 실행하려면 다음 위치로 이동 하다합니다`ClickHouse / programs / server /`(외부에 있습니다`build`) 실행 : 

    ../../build/programs/clickhouse server 

이 경우 ClickHouse는 현재 디렉토리에있는 설정 파일을 사용합니다. 수행 할 수 있습니다`clickhouse server`에서 디렉토리 경로를 지정하고 설정 파일로 명령 줄 매개 변수`--config-file`. 

다른 터미널에서 clickhouse-client를 사용하여 ClickHouse에 연결하려면 다음 위치로 이동합니다`ClickHouse / build / programs /`실행`./clickhouse client`. 

당신이 얻을 경우`Connection refused` 메시지 Mac OS X 또는 FreeBSD에서는 호스트 주소 127.0.0.1을 지정 보겠습니다 : 

    clickhouse client --host 127.0.0.1 

로 대체 할 수 생산 판 ClickHouse 바이너리 설치되는 시스템의 사용자 정의 구축 ClickHouse 진 의류 ClickHouse 쓸 기계의 지시에 따라 공식 사이트에서 다음 다음을 실행합니다 :

    sudo service clickhouse-server stop 
    sudo cp ClickHouse / build / programs / clickhouse / usr / bin / 
    sudo service clickhouse-server start 

게다가`clickhouse-client``clickhouse-server` 그리고 다른 것은 일반적으로 공유 하는`clickhouse` 바이너리 

를 운영 할 수 있습니다 사용자 정의 구축 ClickHouse 바이너리 설정 파일에서 ClickHouse 패키지를 설치 시스템 : 

    sudo service clickhouse-server stop 
    sudo -u clickhouse ClickHouse / build / programs / clickhouse server --config-file /etc/clickhouse-server/config.xml 

# IDE (통합 개발 환경) {# ide-integrated-development-environment} 

사용하는 IDE를 모르는 경우는 CLion을 사용하는 것이 좋습니다. CLion는 상용 소프트웨어이지만, 30 일 무료 평가판을 제공합니다. 또한 학생들을위한 무료입니다. CLion는 Linux와 Mac OS X 모두에서 사용할 수 있습니다.

KDevelop와 QTCreator는 ClickHouse을 개발하기위한 IDE의 다른 탁월한 선택입니다. KDevelop는 매우 편리한 IDE로 제공되지만 불안정합니다. 또한 KDevelop 충돌이 개설해야 프로젝트 클릭 "Stop All"프로젝트의 파일 목록을 열 자마자 버튼을 누릅니다. 그런 후 KDevelop는 잘해야합니다. 

간단한 코드 편집기로, Sublime Text 또는 Visual Studio Code 또는 Kate (모든 Linux에서 사용 가능)을 사용할 수 있습니다. 

만약을 위해, CLion가 작성하게 언급 할 가치가 있습니다`build` 자신의 경로, 그것도 자신의 선택에`debug` 빌드 타입의 경우, 구성을 위해 CLion에 정의되어있는 CMake의 버전을 사용하여 설치되는 것은 아닙니다. `make` 빌드 작업을 수행하려면`ninja`. 이것은 정상적인 현상입니다 만, 혼란을 피하기 위해 그것을 염두에 두십시오. 

# 코드 생성 {# writing-code} 

의 설명 ClickHouse 건축에서 최대한 빨리 보내주십시오 https://clickhouse.tech/docs/en/ 개발 / 아키텍처 / 

코드 스타일 가이드 : https : //clickhouse.tech/ docs / en / 개발 / 스타일 / 

필기 시험 https : //clickhouse.tech/docs/en/development/tests/

작업 목록 https : //github.com/ClickHouse/ClickHouse/issues? q = is % 3Aopen + is % 3Aissue + label % 3A % 22easy + task % 22 

# 테스트 데이터 {# test-data} 

개발 ClickHouse이 필요 옆 재하 실제 구스입니다. 성능 테스트는 특히 중요합니다. 하여 특정 익명 데이터에서 Yandex. 메틀리카 또한 3GB의 하드 디스크 여유 공간이 필요합니다. 이 데이터는 대부분의 개발 작업을 수행하는 데 필요하지 않습니다. 

    sudo apt install wget xz-utils 

    wget https://datasets.clickhouse.tech/hits/tsv/hits_v1.tsv.xz 
    wget https://datasets.clickhouse.tech/visits/tsv/visits_v1.tsv.xz 

    xz -v -d hits_v1.tsv.xz 
    xz -v -d visits_v1.tsv.xz 

    clickhouse-client 

    CREATE DATABASE IF NOT EXISTS test

    CREATE TABLE test.hits (WatchID UInt64, JavaEnable UInt8, Title String, GoodEvent Int16, EventTime DateTime, EventDate Date, CounterID UInt32, ClientIP UInt32, ClientIP6 FixedString (16) RegionID UInt32, UserID UInt64, CounterClass Int8, OS UInt8, UserAgent UInt8 , URL String, Referer String, URLDomain String, RefererDomain String, Refresh UInt8, IsRobot UInt8, RefererCategories Array (UInt16) URLCategories Array (UInt16) URLRegions Array (UInt32) RefererRegions Array (UInt32) ResolutionWidth UInt16, ResolutionHeight UInt16, ResolutionDepth UInt8, FlashMajor UInt8, FlashMinor UInt8, FlashMinor2 String, NetMajor UInt8, NetMinor UInt8, UserAgentMajor UInt16, UserAgentMinor FixedString (2) CookieEnable UInt8, JavascriptEnable UInt8, IsMobile UInt8, MobilePhone UInt8, MobilePhoneModel String, Params String,IPNetworkID UInt32, TraficSourceID Int8, SearchEngineID UInt16, SearchPhrase String, AdvEngineID UInt8, IsArtifical UInt8, WindowClientWidth UInt16, WindowClientHeight UInt16, ClientTimeZone Int16, ClientEventTime DateTime, SilverlightVersion1 UInt8, SilverlightVersion2 UInt8, SilverlightVersion3 UInt32, SilverlightVersion4 UInt16, PageCharset String, CodeVersion UInt32, IsLink UInt8 , IsDownload UInt8, IsNotBounce UInt8, FUniqID UInt64, HID UInt32, IsOldCounter UInt8, IsEvent UInt8, IsParameter UInt8, DontCountHits UInt8, WithHash UInt8, HitColor FixedString (1) UTCEventTime DateTime, Age UInt8, Sex UInt8, Income UInt8, Interests UInt16, Robotness UInt8, GeneralInterests Array (UInt16) RemoteIP UInt32, RemoteIP6 FixedString (16) WindowName Int32, OpenerName Int32, HistoryLength Int16,BrowserLanguage FixedString (2) BrowserCountry FixedString (2) SocialNetwork String, SocialAction String, HTTPError UInt16, SendTiming Int32, DNSTiming Int32, ConnectTiming Int32, ResponseStartTiming Int32, ResponseEndTiming Int32, FetchTiming Int32, RedirectTiming Int32, DOMInteractiveTiming Int32, DOMContentLoadedTiming Int32, DOMCompleteTiming Int32 , LoadEventStartTiming Int32, LoadEventEndTiming Int32, NSToDOMContentLoadedTiming Int32, FirstPaintTiming Int32, RedirectCount Int8, SocialSourceNetworkID UInt8, SocialSourcePage String, ParamPrice Int64, ParamOrderID String, ParamCurrency FixedString (3) ParamCurrencyID UInt16, GoalsReached Array (UInt32) OpenstatServiceName String, OpenstatCampaignID String, OpenstatAdID String, OpenstatSourceID String, UTMSource String, UTMMedium String, UTMCampaign String,UTMContent String, UTMTerm String, FromTag String, HasGCLID UInt8, RefererHash UInt64, URLHash UInt64, CLID UInt32, YCLID UInt64, ShareService String, ShareURL String, ShareTitle String`ParsedParams.Key1` Array (String)`ParsedParams.Key2` Array ( String)`ParsedParams.Key3` Array (String)`ParsedParams.Key4` Array (String)`ParsedParams.Key5` Array (String)`ParsedParams.ValueDouble` Array (Float64) IslandID FixedString (16) RequestNum UInt32, RequestTry UInt8) ENGINE = MergeTree PARTITION BY toYYYYMM (EventDate) SAMPLE BY intHash32 (UserID) ORDER BY (CounterID, EventDate, intHash32 (UserID) EventTime);`ParsedParams.Key2` Array (String)`ParsedParams.Key3` Array (String)`ParsedParams.Key4` Array (String)`ParsedParams.Key5` Array (String)`ParsedParams.ValueDouble` Array (Float64) IslandID FixedString (16) RequestNum UInt32, RequestTry UInt8) ENGINE = MergeTree PARTITION BY toYYYYMM (EventDate) SAMPLE BY intHash32 (UserID) ORDER BY (CounterID, EventDate, intHash32 (UserID) EventTime);`ParsedParams.Key2` Array (String)`ParsedParams.Key3` Array (String)`ParsedParams.Key4` Array (String)`ParsedParams.Key5` Array (String)`ParsedParams.ValueDouble` Array (Float64) IslandID FixedString (16) RequestNum UInt32, RequestTry UInt8) ENGINE = MergeTree PARTITION BY toYYYYMM (EventDate) SAMPLE BY intHash32 (UserID) ORDER BY (CounterID, EventDate, intHash32 (UserID) EventTime);

    CREATE TABLE test.visits (CounterID UInt32, StartDate Date, Sign Int8, IsNew UInt8, VisitID UInt64, UserID UInt64, StartTime DateTime, Duration UInt32, UTCStartTime DateTime, PageViews Int32, Hits Int32, IsBounce UInt8, Referer String, StartURL String, RefererDomain String , StartURLDomain String, EndURL String, LinkURL String, IsDownload UInt8, TraficSourceID Int8, SearchEngineID UInt16, SearchPhrase String, AdvEngineID UInt8, PlaceID Int32, RefererCategories Array (UInt16) URLCategories Array (UInt16) URLRegions Array (UInt32) RefererRegions Array (UInt32 ) IsYandex UInt8, GoalReachesDepth Int32, GoalReachesURL Int32, GoalReachesAny Int32, SocialSourceNetworkID UInt8, SocialSourcePage String, MobilePhoneModel String, ClientEventTime DateTime, RegionID UInt32, ClientIP UInt32, ClientIP6 FixedString (16)RemoteIP UInt32, RemoteIP6 FixedString (16) IPNetworkID UInt32, SilverlightVersion3 UInt32, CodeVersion UInt32, ResolutionWidth UInt16, ResolutionHeight UInt16, UserAgentMajor UInt16, UserAgentMinor UInt16, WindowClientWidth UInt16, WindowClientHeight UInt16, SilverlightVersion2 UInt8, SilverlightVersion4 UInt16, FlashVersion3 UInt16, FlashVersion4 UInt16, ClientTimeZone Int16 , OS UInt8, UserAgent UInt8, ResolutionDepth UInt8, FlashMajor UInt8, FlashMinor UInt8, NetMajor UInt8, NetMinor UInt8, MobilePhone UInt8, SilverlightVersion1 UInt8, Age UInt8, Sex UInt8, Income UInt8, JavaEnable UInt8, CookieEnable UInt8, JavascriptEnable UInt8, IsMobile UInt8, BrowserLanguage UInt16, BrowserCountry UInt16, Interests UInt16, Robotness UInt8, GeneralInterests Array (UInt16) Params Array (String)`Goals.ID` Array (UInt32)`Goals.Serial` Array (UInt32)`Goals.EventTime` Array (DateTime)`Goals.Price` Array (Int64)`Goals.OrderID` Array (String)`Goals.CurrencyID` Array (UInt32) WatchIDs Array (UInt64) ParamSumPrice Int64, ParamCurrency FixedString (3) ParamCurrencyID UInt16, ClickLogID UInt64, ClickEventID Int32, ClickGoodEvent Int32, ClickEventTime DateTime, ClickPriorityID Int32, ClickPhraseID Int32, ClickPageID Int32, ClickPlaceID Int32, ClickTypeID Int32, ClickResourceID Int32, ClickCost UInt32 , ClickClientIP UInt32, ClickDomainID UInt32, ClickURL String, ClickAttempt UInt8, ClickOrderID UInt32, ClickBannerID UInt32, ClickMarketCategoryID UInt32, ClickMarketPP UInt32, ClickMarketCategoryName String, ClickMarketPPName String, ClickAWAPSCampaignName String, ClickPageName String, ClickTargetType UInt16, ClickTargetPhraseID UInt64,ClickContextType UInt8, ClickSelectType Int8, ClickOptions String, ClickGroupBannerID Int32, OpenstatServiceName String, OpenstatCampaignID String, OpenstatAdID String, OpenstatSourceID String, UTMSource String, UTMMedium String, UTMCampaign String, UTMContent String, UTMTerm String, FromTag String, HasGCLID UInt8, FirstVisit DateTime, PredLastVisit Date , LastVisit Date, TotalVisits UInt32`TraficSource.ID` Array (Int8)`TraficSource.SearchEngineID` Array (UInt16)`TraficSource.AdvEngineID` Array (UInt8)`TraficSource.PlaceID` Array (UInt16)`TraficSource. SocialSourceNetworkID` Array (UInt8)`TraficSource.Domain` Array (String)`TraficSource.SearchPhrase` Array (String)`TraficSource.SocialSourcePage` Array (String), Attendance FixedString (16), CLID UInt32, YCLID UInt64, NormalizedRefererHash UInt64,SearchPhraseHash UInt64, RefererDomainHash UInt64, NormalizedStartURLHash UInt64, StartURLDomainHash UInt64, NormalizedEndURLHash UInt64, TopLevelDomain UInt64, URLScheme UInt64, OpenstatServiceNameHash UInt64, OpenstatCampaignIDHash UInt64, OpenstatAdIDHash UInt64, OpenstatSourceIDHash UInt64, UTMSourceHash UInt64, UTMMediumHash UInt64, UTMCampaignHash UInt64, UTMContentHash UInt64, UTMTermHash UInt64, FromHash UInt64 , WebVisorEnabled UInt8, WebVisorActivity UInt32`ParsedParams.Key1` Array (String)`ParsedParams.Key2` Array (String)`ParsedParams.Key3` Array (String)`ParsedParams.Key4` Array (String)`ParsedParams. Key5` Array (String)`ParsedParams.ValueDouble` Array (Float64)`Market.Type` Array (UInt8)`Market.GoalID` Array (UInt32)`Market.OrderID` Array (String)`Market. OrderPrice` Array (Int64)`Market.PP` Array (UInt32)`Market.DirectPlaceID` Array (UInt32)`Market.DirectOrderID` Array (UInt32)`Market.DirectBannerID` Array (UInt32)`Market.GoodID` Array (String)`Market. GoodName` Array (String)`Market.GoodQuantity` Array (Int32)`Market.GoodPrice` Array (Int64) IslandID FixedString (16)) ENGINE = CollapsingMergeTree (Sign) PARTITION BY toYYYYMM (StartDate) SAMPLE BY intHash32 (UserID ) ORDER BY (CounterID, StartDate, intHash32 (UserID) VisitID);IslandID FixedString (16)) ENGINE = CollapsingMergeTree (Sign) PARTITION BY toYYYYMM (StartDate) SAMPLE BY intHash32 (UserID) ORDER BY (CounterID, StartDate, intHash32 (UserID) VisitID);IslandID FixedString (16)) ENGINE = CollapsingMergeTree (Sign) PARTITION BY toYYYYMM (StartDate) SAMPLE BY intHash32 (UserID) ORDER BY (CounterID, StartDate, intHash32 (UserID) VisitID);

    clickhouse-client --max_insert_block_size 100000 --query "INSERT INTO test.hits FORMAT TSV"<hits_v1.tsv 
    clickhouse-client --max_insert_block_size 100000 --query "INSERT INTO test.visits FORMAT TSV"<visits_v1.tsv 

# 풀 요청 작성 {# creating-pull-request} 

GitHub의 UI로 fork 저장소로 이동합니다. 지점에서 개발하고있는 경우는 그 지점을 선택해야합니다. 있을 것입니다 "Pull request"화면에있는 버튼. 본질적으로 이것은 "create a request for accepting my changes into the main repository " 

풀 요청은 작업이 아직 완료되지 않은 경우에도 만들 수 있습니다. 이 경우 단어를 넣어주세요 "WIP"(진행중인 작업) 제목 앞에, 그것은 나중에 변경할 수 있습니다. 이것은 변화의 협조적인 검토 및 논의 및 사용 가능한 모든 테스트 실행에 도움이됩니다. 변경에 대한 간단한 설명을 제공하는 것이 중요합니다.

Yandex 직원이 태그로 당신의 PR로 라벨링 즉시 테스트가 시작됩니다 "can be tested"The results of some first checks (eg code style) will come in within several minutes. Build check results will arrive within half an hour. And the main set of tests will report itself within an hour . 

시스템은 풀 요청에 ClickHouse 바이너리 빌드를 개별적으로 준비합니다. 이 빌드를 얻으려면 "Details"다음 링크 "ClickHouse build check"수표 목록 항목. 거기에는 빌드에 대한 직접 링크가 있습니다. ClickHouse의 deb 패키지는 프로덕션 서버에 배포 할 수 있습니다 (우려가없는 경우). 

대부분의 경우 빌드 중 일부는 처음에 실패합니다. 이것은 gcc와 clang 모두 빌드를 체크한다는 사실 때문입니다. `-Werror` flag) clang을 사용합니다. 동일한 페이지에서 모든 빌드 로그를 찾을 수 있기 때문에, ClickHouse을 모든 가능한 방법으로 빌드 할 필요가 없습니다.
