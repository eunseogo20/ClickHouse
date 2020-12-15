--- 
machine_translated : true 
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd 
toc_priority : 65 
toc_title : "Mac OS X \ u3067ClickHouse \ u3092 \ u69CB \ u7BC9 \ u3059 \ u308B \ u65B9 \ u6CD5" 
--- 

# Mac OS X에서 ClickHouse을 구축하는 방법 {# how-to-build-clickhouse-on-mac-os-x} 

빌드는 mac oS X10.15 (Catalina) 

## 자작 설치 {# install-homebrew} 

```bash 
$ / usr / bin / ruby -e "$ (curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)" 
``` 

## 설치에 필요한 컴파일러, 툴, 도서관 {# install-required-compilers-tools-and -libraries} 

```bash 
$ brew install cmake ninja libtool gettext 
``` 

## 통 차 도구 조카 "도구 채권 쯔つ케 {# checkout-clickhouse-sources} 

```bash
$ git clone --recursive git@github.com : ClickHouse / ClickHouse.git 
``` 

또는 

```bash 
$ git clone --recursive https://github.com/ClickHouse/ClickHouse.git 

$ cd ClickHouse 
``` 

## 빌드 clickHouse {# build-clickhouse} 

```bash 
$ mkdir build 
$ cd build 
$ cmake .. -DCMAKE_CXX_COMPILER =`which clang ++`-DCMAKE_C_COMPILER =`which clang` 
$ ninja 
$ cd .. 
``` 

## 경고 {#caveats} 

Clickhouse-server를 실행하려면 시스템 maxfiles 변수를 늘리십시오. 

!!! info "주" 
    Sudo를 사용해야합니다. 

이렇게하려면 다음 파일을 생성합니다 : 

/ 라이브러리 / LaunchDaemons / limit 맥스 파일 기본 목록 : 

```xml 
<? xml version = "1.0"encoding = "UTF-8"?>
<! DOCTYPE plist PUBLIC "- // Apple // DTD PLIST 1.0 // EN" 
        "http://www.apple.com/DTDs/PropertyList-1.0.dtd"> 
<plist version = "1.0"> 
  <dict> 
    <key> Label </ key> 
    <string> limit.maxfiles </ string> 
    <key> ProgramArguments </ key> 
    <array> 
      <string> launchctl </ string> 
      <string> limit </ string> 
      <string> maxfiles < / string> 
      <string> 524288 </ string> 
      <string> 524288 </ string> 
    </ array> 
    <key> RunAtLoad </ key> 
    <true /> 
    <key> ServiceIPC </ key>
    <false /> 
  </ dict> 
</ plist> 
``` 

다음 명령을 실행합니다 : 

```bash
$ sudo chown root : wheel /Library/LaunchDaemons/limit.maxfiles.plist 
``` 

다시 시작하라는 

검사의 경우 사용 가능한`ulimit -n` 명령 

원본 기사 (https://clickhouse.tech/docs / en / development / build_osx /) <! - hide ->
