---
machine_translated : true
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd
toc_priority : 66
toc_title : "Mac OS X \uC6A9 Linux\uC5D0\uC11C ClickHouse\uC744 \uAD6C\uCD95\uD558\uB294 \uBC29\uBC95"
---

# Mac OS X 용 Linux에서 ClickHouse을 구축하는 방법 {# how-to-build-clickhouse-on-linux-for-mac-os-x}

이것은 Linux 시스템을 사용하여 빌드하는 경우를위한 것입니다`clickhouse` 이것은 Linux 서버에서 실행되는 지속적인 통합 검사를 목적으로하고 있습니다. Mac OS X에서 ClickHouse을 직접 빌드하는 경우 다음 단계로 이동합니다 [다른 명령 (build-osx.md).

Mac OS X 용 크로스 빌드는 빌드 명령 (build.md) 연락처 따르라

# Clang-8을 설치 {# install-clang-8}

의 지시에 따르십시오 https://apt.llvm.org/ 당신의 Ubuntu 또는 Debian 설치 용.
예를 들어, 명령 슈퍼맨 같은 :

```bash
sudo echo "deb [trusted = yes] http://apt.llvm.org/bionic/ llvm-toolchain-bionic-8 main">> /etc/apt/sources.list
sudo apt-get 설치 clang-8
```

# 크로스 컴파일 도구 세트 {# install-cross-compilation-toolset}

설치 경로를 기억하자`cctools`로 $ {CCTOOLS}

```bash
mkdir $ {CCTOOLS}

git clone https://github.com/tpoechtrager/apple-libtapi.git
cd 사과 libtapi
INSTALLPREFIX = $ {CCTOOLS} ./build.sh
./install.sh
cd ..

git clone https://github.com/tpoechtrager/cctools-port.git
cd cctools-port / cctools
./configure --prefix = $ {CCTOOLS} --with-libtapi = $ {CCTOOLS} --target = x86_64-apple-darwin
설치하다
```

또한 작업 트리에 macOS X SDK를 다운로드해야합니다.

```bash
cd 클릭 하우스
wget 'https://github.com/phracker/MacOSX-SDKs/releases/download/10.14-beta4/MacOSX10.14.sdk.tar.xz'
mkdir -p build-darwin / cmake / toolchain / darwin-x86_64
tar xJf MacOSX10.14.sdk.tar.xz -C build-darwin / cmake / toolchain / darwin-x86_64 --strip-components = 1
```

# ビ ル ド ClickHouse {# build-clickhouse}

```bash
cd 클릭 하우스
mkdir 빌드 -osx
CC = clang-8 CXX = clang ++-8 cmake. -Bbuild-osx -DCMAKE_TOOLCHAIN_FILE = cmake / darwin / toolchain-x86_64.cmake \
    -DCMAKE_AR : FILEPATH = $ {CCTOOLS} / bin / x86_64-apple-darwin-ar \
    -DCMAKE_RANLIB : FILEPATH = $ {CCTOOLS} / bin / x86_64-apple-darwin-ranlib \
    -DLINKER_NAME = $ {CCTOOLS} / bin / x86_64-apple-darwin-ld
닌자 -C 빌드 -osx
```

결과 바이너리는 mach-O 실행 포맷을 가지고 Linux에서 실행 할 수 없습니다.
