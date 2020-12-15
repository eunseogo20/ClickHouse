--- 
machine_translated : true 
machine_translated_rev : 72537a2d527c63c07aa5d2361a8829f3895cf2bd 
toc_priority : 58 
toc_title : "\ u4F7F \ u7528 \ u6CD5 \ u306E \ u63A8 \ u5968 \ u4E8B \ u9805" 
--- 

# 사용법 권장 사항 {# usage-recommendations} 

## CPU 규모 지사 {# cpu-scaling-governor} 

항상`performance` 스케일링 지사. 그`on-demand` 스케일링 총재의 작품 그렇지 않으면 항상 높습니다. 

```bash 
$ echo 'performance'| sudo tee / sys / devices / system / cpu / cpu * / cpufreq / scaling_governor 
``` 

## CPU 제한 {# cpu-limitations} 

프로세서의 과열을 방지합니다. 사용`dmesg` cpu의 클럭 속도가 과열로 제한되어 있는지 확인합니다. 
제한은 데이터 센터 수준에서 외부에서 설정할 수 있습니다. 다음을 사용할 수 있습니다`turbostat` 부하의 밑에 그것을 감시한다. 

## RAM {#ram}

소량의 데이터 (최대 200GB 압축)의 경우 데이터 량만큼의 메모리를 사용하는 것이 좋습니다. 
대량의 데이터의 경우와 대화 형 (온라인) 쿼리를 처리하는 경우 핫 데이터 집합이 페이지의 캐시에 맞게 적절한 양의 RAM (128GB 
에도 데이터 량 50TB 서버에 대한 것 128GB RAM을 크게 향상 쿼리 성능에 비해 64GB 샘플이 있습니다. 

오버 커밋을 해제하지 않습니다. 값`cat / proc / sys / vm / overcommit_memory` 0 또는 1로해야합니다. 달려라. 

``` bash 
$ echo 0 | sudo tee / proc / sys / vm / overcommit_memory 
``` 

## 거대한 페이지 {# huge-pages} 

항상 투명한 거대 페이지를 해제합니다. 이것은 메모리 할당을 방해하고 성능이 크게 에 떨어집니다. 

```bash 
$ echo 'never'| sudo tee / sys / kernel / mm / transparent_hugepage / enabled 
``` 

사용`perf top` 메모리 관리를 위해 커널에서 보낸 시간을 감시하는 . 
영구 거대한 페이지를 할당 할 필요는 없습니다. 

## Storage 서브 시스템 {# storage-subsystem} 

예산으로 SSD를 사용할 수있는 경우는 SSD를 사용하십시오.
그렇지 않은 경우는 HDD를 사용합니다. SATA Hdd7200RPM가합니다. 

우선 서버 지역의 하드 디스크에 작은 여러 서버와 함께 디스크가 늘어난다. 
물건의 보존 아카이브에서 쿼리 선반합니다. 

## RAID {#raid} 

HDD를 사용하는 경우 RAID-10, RAID 5, RAID-6 또는 RAID-50를 결합 할 수 있습니다. 
Linux에서 소프트웨어 RAID가 우수합니다 (`mdadm`) LVM을 사용하는 것은 바람직하지 않습니다. 
RAID-10을 만들 때`far` 레이아웃. 
예산이 허락한다면, RAID-10을 선택합니다. 

4 개 이상의 디스크가있는 경우 RAID-6 (우선) 또는 RAID-50을 사용하십시오. 
RAID-5, RAID-6 또는 RAID-50을 사용하는 경우 항상 stripe_cache_size을 늘리십시오. 

```bash 
$ echo 4096 | sudo tee / sys / block / md2 / md / stripe_cache_size 
``` 

식을 사용하여 장치의 수와 블록 크기에서 정확한 숫자를 계산합니다 :`2 * num_devices * chunk_size_in_bytes / 4096`. 

모든 RAID 구성은 1024KB 블록 사이즈로 충분합니다. 
없는 세트의 블록 크기가 너무 많습니다. 

SSD는 RAID-0을 사용할 수 있습니다.
에 관계없이 RAID를 이용, 사용 및 중복 데이터입니다. 

긴 대기열에서 NCQ를 활성화합니다. HDD의 경우는 CFQ 스케줄러를 선택하고 SSD의 경우는 noop를 선택합니다. 감소하지 마십시오 'readahead'설정. 
HDD의 경우, 쓰기 캐시를 활성화합니다. 

## 파일 시스템 {# file-system} 

Ext4는 가장 신뢰할 수있는 옵션입니다. 마운트 옵션의 설정`noatime, nobarrier`. 
XFS 적합하지만 ClickHouse에서 철저하게 테스트되어 있지 않습니다. 
다른 대부분의 파일 시스템 사양. 파일 시스템의 늦은 배달입니다. 

## Linux 커널 {# linux-kernel} 

이전 Linux 커널을 사용하지 마십시오. 

## 인터넷 {#network} 

IPv6를 사용하는 경우 루트 캐시의 크기를 늘립니다. 
3.2 이전 Linux 커널은 IPv6 구현에 대해 많은 문제가있었습니다. 

가능하면 10GB 이상의 네트워크를 사용하십시오. 1Gb도 작동하지만, 수십 테라 바이트의 데이터를 사용하여 복제에 패치를 적용하거나 대량의 중간 데이터를 사용하여 분산 쿼리를 처리 할 경우 

## 사육사 {#zookeeper} 

아마 이미 다른 목적에 ZooKeeper를 사용하고 있습니다. 아직 과부하가되어 있지 않은 경우는 ZooKeeper와 동일한 설치를 사용할 수 있습니다.

It 's best to use a fresh version of ZooKeeper - 3.4.9 or later. The version in stable Linux distributions may be outdated . 

다른 ZooKeeper 클러스터간에 데이터를 전송하려면 수동으로 작성된 스크립트를 사용하지 마십시오. 결코 사용하지 않는다 "zkcopy"같은 이유로 유틸리티 https : //github.com/ksprojects/zkcopy/issues/15 

기존 ZooKeeper 클러스터를 둘로 분할하는 경우 올바른 방법은 복제본의 수를 늘려, 그것을 를 두 독립적 인 클러스터로 재구성하는 것입니다. 

ClickHouse와 같은 서버에서 ZooKeeper를 실행하지 마십시오. 에서 사육사가 매우 민감한 위해 시간 지연과 ClickHouse를 이용하는 것도 가능 사용 가능한 모든 시스템 자원입니다. 

기본적으로 사육사는 시한 폭탄입니다 : 

> ZooKeeper 서버는 기본 설정을 사용하면 오래된 스냅 샷과 로그 파일을 삭제하지 않습니다 (autopurge 참조). 

이 폭탄은 제거해야 

다음 사육사 (3.5.1) 설정은 Yandex에서 사용되고 있습니다. 달처럼 Metrica의 생산 환경 20,2017 : 

동물원 cfg : 

```bash 
# http://hadoop.apache.org/zookeeper/docs/current/zookeeperAdmin.html

# The number of milliseconds of each tick 
tickTime = 2000 
# The number of ticks that the initial 
# synchronization phase can take 
initLimit = 30000 
# The number of ticks that can pass between 
# sending a request and getting an acknowledgement 
syncLimit = 10 

maxClientCnxns = 2000 

maxSessionTimeout = 60000000 
# the directory where the snapshot is stored. 
dataDir = / opt / zookeeper / {{ '{{'}} cluster [ 'name'] {{ '}}'}} / data 
# Place the dataLogDir to a separate physical disc for better performance 
dataLogDir = / opt / zookeeper / {{ '{{'}} cluster [ 'name'] {{ '}}'}} / logs 

autopurge.snapRetainCount = 10 
autopurge.purgeInterval = 1


# To avoid seeks ZooKeeper allocates space in the transaction log file in 
# blocks of preAllocSize kilobytes. The default block size is 64M. One reason 
# for changing the size of the blocks is to reduce the block size if snapshots 
# are taken more often. (Also, see snapCount) 
preAllocSize = 131072 

# Clients can submit requests faster than ZooKeeper can process them , 
# especially if there are a lot of clients. to prevent ZooKeeper from running 
# out of memory due to queued requests, ZooKeeper will throttle clients so that 
# there is no more than globalOutstandingLimit outstanding requests in the 
# system. The default limit is 1,000.ZooKeeper logs transactions to a in the 
# transaction log. After snapCount transactions are written to a log file a file a 
# snapshot is started and a new transaction log file is started . The default 
# snapCount is 10,000 
snapCount = 3000000 

# If this option is defined, requests will be will logged to a trace file named 
# traceFile.year.month.day. 
# traceFile = 

# leader accepts client connections. Default value is "yes"The leader machine 
# coordinates updates. For higher update throughput at thes slight expense of 
# read throughput the leader can be configured to not accept clients and focus 
# on coordination. 
leaderServes = yes 

standaloneEnabled = false 
dynamicConfigFile = / etc / zookeeper - {{ '{{'}} cluster [ 'name'] {{ '}}'}} / conf / zoo.cfg.dynamic 
``` 

Java 버전 : 

```text
Java (TM) SE Runtime Environment (build 1.8.0_25-b17) 
Java HotSpot (TM) 64-Bit Server VM (build 25.25-b02, mixed mode) 
``` 

JVM 매개 변수 : 

```bash 
NAME = zookeeper - {{ '{{'}} cluster [ 'name'] {{ '}}'}} 
ZOOCFGDIR = / etc / $ nAME / conf 

# TODO this is really ugly 
# How to find out, which jars are needed? 
# seems, that log4j requires the log4j.properties file to be in the classpath
CLASSPATH = "$ ZOOCFGDIR : / usr / build / classes / usr / build / lib / *. jar : /usr/share/zookeeper/zookeeper-3.5.1-metrika.jar : / usr / share / zookeeper / slf4j- log4j12-1.7.5.jar : /usr/share/zookeeper/slf4j-api-1.7.5.jar : /usr/share/zookeeper/servlet-api-2.5-20081211.jar : / usr / share / zookeeper / netty -3.7.0.Final.jar : /usr/share/zookeeper/log4j-1.2.16.jar : /usr/share/zookeeper/jline-2.11.jar : /usr/share/zookeeper/jetty-util-6.1. 26.jar : /usr/share/zookeeper/jetty-6.1.26.jar : /usr/share/zookeeper/javacc.jar : /usr/share/zookeeper/jackson-mapper-asl-1.9.11.jar : / usr / share / zookeeper / jackson-core-asl-1.9.11.jar : /usr/share/zookeeper/commons-cli-1.2.jar : / usr / src / java / lib / *. jar : / usr / etc / zookeeper " 

ZOOCFG ="$ ZOOCFGDIR / zoo.cfg " 
ZOO_LOG_DIR = / var / log / $ NAME 
USER = zookeeper 
GROUP = zookeeper 
PIDDIR = / var / run / $ NAME
PIDFILE = $ PIDDIR / $ NAME.pid+ PrintTenuringDistribution \ 
    -XX : + PrintGCApplicationStoppedTime \
SCRIPTNAME = / etc / init.d / $ NAME 
JAVA = / usr / bin / java 
ZOOMAIN = "org.apache.zookeeper.server.quorum.QuorumPeerMain" 
ZOO_LOG4J_PROP = "INFO, ROLLINGFILE" 
JMXLOCALONLY = false 
JAVA_OPTS = "- Xms { { '{{'}} cluster.get ( 'xms', '128M') {{ '}}'}} \ 
    -Xmx {{ '{{'}} cluster.get ( 'xmx', '1G') {{ '}}'}} \ 
    -Xloggc : /var/log/$NAME/zookeeper-gc.log \ 
    -XX : + UseGCLogFileRotation \ 
    -XX : NumberOfGCLogFiles = 16 \ 
    -XX : GCLogFileSize = 16M \ 
    -verbose : gc \ 
    -XX : + PrintGCTimeStamps \ 
    -XX : + PrintGCDateStamps \ 
    -XX : + PrintGCDetails 
    -XX : + PrintTenuringDistribution \ 
    -XX :+ PrintGCApplicationConcurrentTime \ 
    -XX : + PrintSafepointStatistics \ 
    -XX : + UseParNewGC \ 
    -XX : + UseConcMarkSweepGC \ 
-XX : + CMSParallelRemarkEnabled " 
``` 

소금 init : 

```text 
description"zookeeper - {{ '{{' }} cluster [ 'name'] {{ '}}'}} centralized coordination service " 

start on runlevel [2345] 
stop on runlevel [! 2345] 

respawn 

limit nofile 8192 8192 

pre-start script 
    [-r"/ etc / zookeeper - {{ '{{'}} cluster [ 'name'] {{ '}}'}} / conf / environment "] || exit 0 
    / etc / zookeeper - {{ '{{'}} cluster [ ' name '] {{'}} '}} / conf / environment 
    [-d $ ZOO_LOG_DIR] || mkdir -p $ ZOO_LOG_DIR
    chown $ USER : $ GROUP $ ZOO_LOG_DIR 
end script 
 
script
    / etc / zookeeper - {{ '{{'}} cluster [ 'name'] {{ '}}'}} / conf / environment 
    [-r / etc / default / zookeeper] && / etc / default / zookeeper 
    if [-z "$ JMXDISABLE"]; then 
        JAVA_OPTS = "$ JAVA_OPTS -Dcom.sun.management.jmxremote -Dcom.sun.management.jmxremote.local.only = $ JMXLOCALONLY" 
    fi 
    exec start-stop-daemon --start -c $ USER --exec $ JAVA --name zookeeper - {{ '{{'}} cluster [ 'name'] {{ '}}'}} \ 
        - - cp $ CLASSPATH $ JAVA_OPTS -Dzookeeper.log.dir = $ {ZOO_LOG_DIR} \ 
        -Dzookeeper.root.logger = $ {ZOO_LOG4J_PROP} $ ZOOMAIN $ ZOOCFG 
end script 
``` 

{## 원래 기사 (https ://clickhouse.tech/docs/en/operations/tips/) ##}
