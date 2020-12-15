---
toc_priority : 11
toc_title : "설치"
---

# 설치 {#installation}

## 시스템 요구 사항 {# system-requirements}

ClickHouse은 x86_64, AArch64 또는 PowerPC64LE CPU 아키텍처를 가진 Linux, FreeBSD, 또는 Mac OS X에서 실행할 수 있습니다.

공식 사전 빌드 바이너리는 일반적으로 x86_64 용으로 컴파일되어 있으며, SSE 4.2 명령어 세트를 사용하고 있습니다. 현재의 CPU가 SSE 4.2을 지원하는지 여부를 확인하는 명령은 다음과 같습니다 :

```bash
$ grep -q sse4_2 / proc / cpuinfo && echo "SSE 4.2 supported"|| echo "SSE 4.2 not supported"
```

SSE를 지원하지 않는 프로세서에서 ClickHouse을 실행하려면 SSE 4.2을 지원하는지, AArch64 또는 PowerPC64LE 아키텍처 위에서 적절한 설정과 조정, 소스에서 ClickHouse를 빌드 (# from- sources)해야합니다.

## 사용할 수있는 설치 옵션 {# available-installation-options}

### DEB 패키지에서 {# install-from-deb-packages}

Debian 또는 Ubuntu 용으로 컴파일 된 공식 패키지`deb`을 사용하는 것이 좋습니다. 다음 명령을 실행하여 패키지를 설치하십시오 :


```bash
{% include 'install / deb.sh'%}
```

최신 버전을 사용하고 싶다면`stable`을`testing`로 바꿉니다. (테스트 환경에서는이를 권장합니다)

마찬가지로, [여기] (https://repo.clickhouse.tech/deb/stable/main/)에서 패키지를 다운로드하여 수동으로 설치할 수도 있습니다.

#### 패키지 {#packages}

-`clickhouse-common-static` - 컴파일 된 ClickHouse 이진 파일을 설치합니다.
-`clickhouse-server` -`clickhouse-server`에 대한 심볼릭 링크를 만들고 기본 서버 설정을 설치합니다.
-`clickhouse-client` -`clickhouse-server` 및 다른 client 관련 도구에 대한 심볼릭 링크를 만들고 client 관련 설정 파일을 설치합니다.
-`clickhouse-common-static-dbg` - 컴파일 된 ClickHouse 이진 파일을 디버깅 정보와 함께 설치합니다.

### RPM 패키지에서 {# from-rpm-packages}

CentOS, RedHat, 기타 모든 rpm 기반의 Linux 배포판에서 공식 컴파일 된`rpm` 패키지를 사용하는 것을 권장합니다.

우선 공식 저장소를 추가해야합니다 :

```bash
sudo yum install yum-utils
sudo rpm --import https://repo.clickhouse.tech/CLICKHOUSE-KEY.GPG
sudo yum-config-manager --add-repo https://repo.clickhouse.tech/rpm/stable/x86_64
```

최신 버전을 사용하고 싶다면`stable`을`testing`로 바꿉니다. (테스트 환경에서이 권장됩니다). `prestable`도 종종 같이 사용할 수 있습니다.

그리고 다음 명령을 실행하여 패키지를 설치합니다 :

```bash
sudo yum install clickhouse-server clickhouse-client
```

마찬가지로, [여기] (https://repo.clickhouse.tech/rpm/stable/x86_64)에서 패키지를 다운로드하여 수동으로 설치할 수도 있습니다.

### Tgz 아카이브에서 {# from-tgz-archives}

모든 Linux 배포판에서`deb`과`rpm` 패키지를 설치할 수없는 경우에는 공식 컴파일 된`tgz` 아카이브를 사용하는 것이 좋습니다.

필요한 버전 저장소 https://repo.clickhouse.tech/tgz/에서`curl` 또는`wget`에서 다운로드 할 수 있습니다. 그 후, 다운로드 한 아카이브의 압축을 풀고 설치 스크립트에서 설치하십시오. 최신 버전의 예는 다음과 같습니다 :

```bash
export LATEST_VERSION =`curl https://api.github.com/repos/ClickHouse/ClickHouse/tags 2> / dev / null | grep -Eo '[0-9] + \. [0-9] + \. [ 0-9] + \. [0-9] + '| head -n 1`
curl -O https://repo.clickhouse.tech/tgz/clickhouse-common-static-$LATEST_VERSION.tgz
curl -O https://repo.clickhouse.tech/tgz/clickhouse-common-static-dbg-$LATEST_VERSION.tgz
curl -O https://repo.clickhouse.tech/tgz/clickhouse-server-$LATEST_VERSION.tgz
curl -O https://repo.clickhouse.tech/tgz/clickhouse-client-$LATEST_VERSION.tgz

tar -xzvf clickhouse-common-static- $ LATEST_VERSION.tgz
sudo clickhouse-common-static- $ LATEST_VERSION / install / doinst.sh

tar -xzvf clickhouse-common-static-dbg- $ LATEST_VERSION.tgz
sudo clickhouse-common-static-dbg- $ LATEST_VERSION / install / doinst.sh

tar -xzvf clickhouse-server- $ LATEST_VERSION.tgz
sudo clickhouse-server- $ LATEST_VERSION / install / doinst.sh
sudo /etc/init.d/clickhouse-server start

tar -xzvf clickhouse-client- $ LATEST_VERSION.tgz
sudo clickhouse-client- $ LATEST_VERSION / install / doinst.sh
```

프로덕션 환경에서는 최신의`stable` 버전을 사용하는 것이 좋습니다. GitHub의 페이지 https://github.com/ClickHouse/ClickHouse/tags에서 접미사`-stable`이 버전 번호로 확인할 수 있습니다.

### Docker 이미지에서 {# from-docker-image}

Docker에서 ClickHouse를 실행하려면 다음 [DockerHub (https://hub.docker.com/r/yandex/clickhouse-server/)의 가이드를 따르십시오. 그 이미지는 내부에서 공식`deb` 패키지를 사용하고 있습니다.

### 비표준 환경을위한 사전 컴파일 된 바이너리에서 {# from-binaries-non-linux}

비 Linux 운영 체제와 AArch64 CPU 아키텍처를 위해 ClickHouse 빌드는`master` 브랜치의 최신 커밋에서 크로스 컴파일 된 바이너리를 제공합니다. (몇 시간 지연 될 수 있습니다)


- [macOS (https://builds.clickhouse.tech/master/macos/clickhouse) -`curl -O 'https://builds.clickhouse.tech/master/macos/clickhouse'&& chmod a + x ./ clickhouse`
- FreeBSD (https://builds.clickhouse.tech/master/freebsd/clickhouse) -`curl -O 'https://builds.clickhouse.tech/master/freebsd/clickhouse'&& chmod a + x ./ clickhouse`
- [AArch64 (https://builds.clickhouse.tech/master/aarch64/clickhouse) -`curl -O 'https://builds.clickhouse.tech/master/aarch64/clickhouse'&& chmod a + x ./ clickhouse`

다운로드 후`clickhouse client`을 사용하여 서버에 연결하거나`clickhouse local`를 사용하여 로컬 데이터를 처리 할 수 ​​있습니다. `clickhouse server`을 실행하려면 GitHub에서 server (https://github.com/ClickHouse/ClickHouse/blob/master/programs/server/config.xml) 및 users (https : // github .com / ClickHouse / ClickHouse / blob / master / programs / server / users.xml)의 설정 파일을 추가로 다운로드해야합니다.

이러한 빌드 충분히 테스트되지 않았으므로 프로덕션 환경에서의 사용은 권장되지 않지만, 자기 책임으로 할 수 있습니다. 이러한에서는 ClickHouse 기능의 일부만 사용할 수 있습니다.

### 소스에서 {# from-sources}

ClickHouse을 수동으로 컴파일하려면 다음 [Linux] (../ development / build.md) 또는 Mac OS X (../ development / build-osx.md)의 지시에 따릅니다.

패키지를 컴파일하고 설치할 수 있으며, 패키지를 설치하지 않고 프로그램을 사용할 수 있습니다. 또한 수동으로 빌드하면 SSE 4.2의 요구 사항을 해제하거나 AArch64 CPU 용으로 빌드 할 수 있습니다.

      Client : programs / clickhouse-client
      Server : programs / clickhouse-server

사용자에게 데이터와 메타 데이터의 폴더를 만들고`chown`해야합니다. 그 경로는 서버 설정 (src / programs / server / config.xml)에서 변경할 수 있습니다. 디폴트는 다음과 같습니다 :

      / opt / clickhouse / data / default /
      / opt / clickhouse / metadata / default /

Gentoo는 소스에서 ClickHouse를 설치하기 위해`emerge clickhouse`를 사용할 수 있습니다.

## 시작 {#launch}

서버를 데몬으로 실행하려면 :

```bash
$ sudo service clickhouse-server start
```

`service` 명령이없는 경우에는 다음과 같이 실행합니다 :

```bash
$ sudo /etc/init.d/clickhouse-server start
```

`/ var / log / clickhouse-server /`디렉토리의 로그를 참조하십시오.

서버가 시작되지 않는 경우는`/ etc / clickhouse-server / config.xml` 파일의 설정을 확인하십시오.

마찬가지로 콘솔에서 다음과 같이 수동으로 시작할 수 있습니다 :

```bash
$ clickhouse-server --config-file = / etc / clickhouse-server / config.xml
```

이 경우 콘솔 개발시 유용한 로그가 출력됩니다. 설정 파일이 현재 디렉토리에있는 경우,`--config-file` 매개 변수를 지정할 필요가 없습니다. 기본적으로`/ config.xml`을 사용합니다.


ClickHouse 액세스 제한 설정을 지원하고 있습니다. 그들은`users.xml` 파일 (`config.xml` 옆)에 있습니다. 기본적으로`default` 사용자가 암호없이 어디서나 액세스가 허용됩니다. `user / default / networks`를 참조하여 자세한 내용은 [ "설정 파일"(../ operations / configuration-files.md)을 참조하십시오.


서버를 시작한 후 명령 줄 클라이언트를 사용하여 서버에 연결할 수 있습니다 :

```bash
$ clickhouse-client
```

기본적으로 사용자`default`에서`localhost : 9000`에 암호없이 연결합니다. 또한`--host` 인수를 사용하여 원격 서버에 연결할 수 있습니다.

단말은 UTF-8 인코딩을 사용해야합니다. 자세한 내용은 명령 줄 클라이언트 "(../ interfaces / cli.md)를 참조하십시오.

예 :

```bash
$ ./clickhouse-client
ClickHouse client version 0.0.18749.
Connecting to localhost : 9000.
Connected to ClickHouse server version 0.0.18749.

:) SELECT 1

SELECT 1

┌─1─┐
│ 1 │
└───┘

1 rows in set. Elapsed : 0.003 sec.

:)
```

** 축하합니다! 시스템이 움직였습니다! **

테스트를 계속하려면 테스트 데이터 집합을 다운로드하거나 튜토리얼 (https://clickhouse.tech/tutorial.html)를 참조하십시오.

원본 기사 (https://clickhouse.tech/docs/en/getting_started/install/) <! - hide ->
