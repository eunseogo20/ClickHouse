--- 
machine_translated : true 
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd 
toc_priority : 64 
toc_title : "Linux \ u4E0A \ u3067ClickHouse \ u3092 \ u69CB \ u7BC9 \ u3059 \ u308B \ u65B9 \ u6CD5" 
--- 

# 개발을위한 ClickHouse을 구축하는 방법 {# how-to-build-clickhouse-for-development} 

다음의 튜토리얼은 Ubuntu Linux 시스템을 기반으로합니다. 
적절한 변경하면 다른 Linux 배포판에서도 작동합니다. 
지원되는 플랫폼 : x86_64 및 AArch64. Power9 지원은 실험적이다. 

## Git, CMake, Python과 닌자를 설치합니다 {# install-git-cmake-python-and-ninja} 

```bash 
$ sudo apt-get install git cmake python ninja-build 
``` 

오래된 시스템에서는 cmake 대신 cmake3. 

## GCC9 설치 {# install-gcc-10} 

이렇게 몇 가지 방법이 있습니다.

### PPA 패키지에서 설치 {# install-from-a-ppa-package} 

```bash 
$ sudo apt-get install software-properties-common 
$ sudo apt-add-repository ppa : ubuntu-toolchain-r / test 
$ sudo apt-get update 
$ sudo apt-get install gcc-10 g ++ - 10 
``` 

### 소스에서 설치 {# install-from-sources} 

봐 [utils / ci / build-gcc-from-sources. sh (https://github.com/ClickHouse/ClickHouse/blob/master/utils/ci/build-gcc-from-sources.sh) 

## 빌드에 GCC9을 사용하는 {# use-gcc-10-for -builds} 

```bash 
$ export CC = gcc-10 
$ export CXX = g ++ - 10 
``` 

## 통 차 도구 조카 "도구 채권 쯔つ케 {# checkout-clickhouse-sources} 

```bash 
$ git clone --recursive git@github.com : ClickHouse / ClickHouse.git 
```

또는 

```bash 
$ git clone --recursive https://github.com/ClickHouse/ClickHouse.git 
``` 

## 빌드 ClickHouse {# build-clickhouse} 

```bash 
$ cd ClickHouse 
$ mkdir build 
$ cd build 
$ cmake .. 
$ ninja 
$ cd .. 
``` 

실행 파일을 만들려면`ninja clickhouse`. 
이것은 만듭니다`programs / clickhouse` 실행 파일`client` 또는`server` 인수. 

# 모든 Linux에서 ClickHouse을 구축하는 방법 {# how-to-build-clickhouse-on-any-linux} 

의 구축이 필요 다음 구성 요소 : 

- Git (소스를 체크 아웃하기 위해서만 사용되며, 빌드는 필요하지 않습니다) 
- CMake3.10 이후 
- 닌자 (권장) 또는 만들기 
- C ++ 컴파일러 : gcc9 또는 clang8 이후 
- 링커 : lld 또는 gold (고전 GNU ld는 작동하지 않습니다)
- Python (LLVM 빌드에서만 사용되는 옵션입니다) 

모든 구성 요소가 설치되어있는 경우 위의 단계와 같은 방법으로 빌드 할 수 있습니다. 

Ubuntu Eoan 예 : 

    sudo apt update 
    sudo apt install git cmake ninja-build g ++ python 
    git clone --recursive https://github.com/ClickHouse/ClickHouse.git 
    mkdir build && cd build 
    cmake ../ClickHouse 
    ninja 

OpenSUSE 다니지 예 : 

    sudo zypper install git cmake ninja gcc-c ++ python lld 
    git clone --recursive https://github.com/ClickHouse/ClickHouse.git 
    mkdir build && cd build 
    cmake ../ClickHouse 
    ninja 

Fedora Rawhide 예 : 

    sudo yum update 
    yum --nogpg install git cmake make gcc-c ++ python3
    git clone --recursive https://github.com/ClickHouse/ClickHouse.git 
    mkdir build && cd build 
    cmake ../ClickHouse 
    make -j $ (nproc) 

# ClickHouse를 구축 할 필요가 없습니다 {# you-dont-have -to-build-clickhouse} 

ClickHouse는 미리 빌드 된 바이너리와 패키지로 사용할 수 있습니다. 이진은 이식성이 어떤 Linux 맛으로 실행할 수 있습니다. 

이 때문에 안정된 prestable- 시험 스 출시마다 커밋 마스터 모두 뺍니다. 

에서 신선한 빌드를 찾으려면`master`로 이동 커밋 페이지 (https://github.com/ClickHouse/ClickHouse/commits/master) 첫 번째 녹색 확인 표시 또는 커밋 근처에있는 붉은 십자가를 클릭하고 "Details"바로 뒤에 연결 "ClickHouse Build Check" 

# ClickHouse Debian 패키지 빌드 방법 {# how-to-build-clickhouse-debian-package} 

## Git과 Pbuilder 설치 {# install-git- and-pbuilder} 

```bash 
$ sudo apt-get update
$ sudo apt-get install git python pbuilder debhelper lsb-release fakeroot sudo debian-archive-keyring debian-keyring 
``` 

## 통 차 도구 조카 "도구 채권 쯔つ케 {# checkout-clickhouse-sources-1} 

` bash에 
$ git clone --recursive --branch master https://github.com/ClickHouse/ClickHouse.git 
$ cd ClickHouse 
``` 

## 해방 스크립트의 실행 {# run-release-script} 

```bash 
$ ./release 
``` 

원래 기사 (https://clickhouse.tech/docs/en/development/build/) <! - hide ->
