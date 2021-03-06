---
machine_translated : true
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd
toc_priority : 67
toc_title : "Aarch64\uC6A9Linux\uC5D0\uC11CClickHouse\uC744\uAD6C\uCD95\uD558\uB294\uBC29\uBC95(ARM64)"
---

# Aarch64 (ARM64) 아키텍처 용 Linux에서 ClickHouse을 구축하는 방법 {# how-to-build-clickhouse-on-linux-for-aarch64-arm64-architecture}

이것은 Linux 시스템을 사용하여 빌드하는 경우를위한 것입니다`clickhouse` AARCH64CPU 아키텍처를 가진 다른 Linux 시스템에서 실행되는 바이너리. 이 목적을 위해 지속적인 통합을 검사를 수행 Linux 서버

AARCH64 크로스 빌드는 빌드 명령 (build.md) 연락처 따르라

# Clang-8을 설치 {# install-clang-8}

의 지시에 따르십시오 https://apt.llvm.org/ 당신의 Ubuntu 또는 Debian 설치 용.
예를 들어, Ubuntu Bionic에서 다음 명령을 사용할 수 있습니다 :

```bash
echo "deb [trusted = yes] http://apt.llvm.org/bionic/ llvm-toolchain-bionic-8 main"| sudo tee /etc/apt/sources.list.d/llvm.list
sudo apt-get 업데이트
sudo apt-get 설치 clang-8
```

# 크로스 컴파일 도구 세트 {# install-cross-compilation-toolset}

```bash
cd 클릭 하우스
mkdir -p build-aarch64 / cmake / toolchain / linux-aarch64
wget 'https://developer.arm.com/-/media/Files/downloads/gnu-a/8.3-2019.03/binrel/gcc-arm-8.3-2019.03-x86_64-aarch64-linux-gnu.tar.xz? 개정 = 2e88a73f-d233-4f96-b1f4-d8b36e9bb0b9 & la = en '-O gcc-arm-8.3-2019.03-x86_64-aarch64-linux-gnu.tar.xz
tar xJf gcc-arm-8.3-2019.03-x86_64-aarch64-linux-gnu.tar.xz -C build-aarch64 / cmake / toolchain / linux-aarch64 --strip-components = 1
```

#  ClickHouse {# build-clickhouse}

```bash
cd 클릭 하우스
mkdir 빌드 -arm64
CC = clang-8 CXX = clang ++-8 cmake. -Bbuild-arm64 -DCMAKE_TOOLCHAIN_FILE = cmake / linux / toolchain-aarch64.cmake
닌자 -C 빌드 -arm64
```

결과 바이너리는 Aarch64CPU 아키텍처를 가진 Linux에서만 실행됩니다.
