---
title: Linux 性能工具
titleSuffix: Azure Kubernetes Service
description: 了解如何排查和解决在使用 Azure Kubernetes 服务 (AKS) 时遇到的常见问题
services: container-service
author: alexeldeib
ms.service: container-service
ms.topic: troubleshooting
ms.date: 02/10/2020
ms.author: aleldeib
ms.openlocfilehash: eb6b126b4d1794adf0380432040190b91a17a675
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77925600"
---
# <a name="linux-performance-troubleshooting"></a>Linux 性能故障排除

Linux 机器上的资源耗尽是一个常见的问题，可以通过各种症状表现出来。 本文档提供了可用于帮助诊断此类问题的工具的高级概述。

其中许多工具接受生成滚动输出的间隔。 这种输出格式通常使发现模式更容易。 如果接受，示例调用将包括`[interval]`。

其中许多工具具有广泛的历史记录和广泛的配置选项集。 此页面仅提供一个简单的调用子集，以突出显示常见问题。 规范的信息源始终是每个特定工具的参考文档。 该文件将比这里提供的文件更彻底。

## <a name="guidance"></a>指南

系统化调查性能问题的方法。 两种常见方法是 USE（利用率、饱和度、错误）和红色（速率、错误、持续时间）。 RED 通常用于基于请求的监视的服务上下文。 USE 通常用于监视资源：对于计算机中的每个资源，监视利用率、饱和度和错误。 任何计算机上的四种主要资源是 cpu、内存、磁盘和网络。 这些资源的高利用率、饱和度或错误率表示系统可能存在问题。 当出现问题时，请调查根本原因：为什么磁盘 IO 延迟很高？ 磁盘或虚拟机是否限制？ 哪些进程正在写入设备以及哪些文件？

一些常见问题和指标的例子来诊断它们：
- IOPS 限制：使用 iostat 测量每个设备 IOPS。 确保没有单个磁盘超过其限制，并且所有磁盘的总和都小于虚拟机的限制。
- 带宽限制：使用 iostat 作为 IOPS，但测量读/写吞吐量。 确保每台设备和聚合吞吐量都低于带宽限制。
- SNAT 耗尽：这可以表现为 SAR 中的高活动（出站）连接。 
- 数据包丢失：这可以通过代理通过 TCP 重新传输计数相对于已发送/接收计数进行测量。 和`sar``netstat`都可以显示此信息。

## <a name="general"></a>常规

这些工具是通用的，涵盖基本系统信息。 它们是进一步调查的良好起点。

### <a name="uptime"></a>超时时间

```
$ uptime
 19:32:33 up 17 days, 12:36,  0 users,  load average: 0.21, 0.77, 0.69
```

uptime 提供系统 UPtime 和 1、5 和 15 分钟负载平均值。 这些负载平均值大致对应于执行工作的线程或等待不间断工作的线程完成。 在绝对这些数字可能难以解释，但随着时间的推移，他们可以告诉我们有用的信息：

- 1 分钟平均> 5 分钟平均值意味着负载正在增加。
- 1 分钟平均< 5 分钟平均值意味着负载正在减少。

uptime 还可以阐明信息不可用的原因：问题可能自行解决，或者在用户访问计算机之前重新启动。

负载平均值高于可用 CPU 线程数可能表示给定工作负载存在性能问题。

### <a name="dmesg"></a>德梅斯格

```
$ dmesg | tail 
$ dmesg --level=err | tail
```

dmesg 转储内核缓冲区。 像 OOMKill 这样的事件向内核缓冲区添加一个条目。 在 dmesg 日志中查找 OOMKill 或其他资源耗尽消息是问题的有力指标。

### <a name="top"></a>top

```
$ top
Tasks: 249 total,   1 running, 158 sleeping,   0 stopped,   0 zombie
%Cpu(s):  2.2 us,  1.3 sy,  0.0 ni, 95.4 id,  1.0 wa,  0.0 hi,  0.2 si,  0.0 st
KiB Mem : 65949064 total, 43415136 free,  2349328 used, 20184600 buff/cache
KiB Swap:        0 total,        0 free,        0 used. 62739060 avail Mem

   PID USER      PR  NI    VIRT    RES    SHR S  %CPU %MEM     TIME+ COMMAND
116004 root      20   0  144400  41124  27028 S  11.8  0.1 248:45.45 coredns
  4503 root      20   0 1677980 167964  89464 S   5.9  0.3   1326:25 kubelet
     1 root      20   0  120212   6404   4044 S   0.0  0.0  48:20.38 systemd
     ...
```

`top`提供了当前系统状态的广泛概述。 标头提供一些有用的聚合信息：

- 任务状态：运行、睡眠、停止。
- CPU 利用率，在这种情况下，主要显示空闲时间。
- 总计、空闲和已使用的系统内存。

`top`可能错过短期过程;替代方案喜欢`htop`并提供`atop`类似的接口，同时修复其中一些缺点。

## <a name="cpu"></a>CPU

这些工具提供 CPU 利用率信息。 这对于滚动输出特别有用，因为滚动输出模式变得容易发现。

### <a name="mpstat"></a>mpstat

```
$ mpstat -P ALL [interval]
Linux 4.15.0-1064-azure (aks-main-10212767-vmss000001)  02/10/20        _x86_64_        (8 CPU)

19:49:03     CPU    %usr   %nice    %sys %iowait    %irq   %soft  %steal  %guest  %gnice   %idle
19:49:04     all    1.01    0.00    0.63    2.14    0.00    0.13    0.00    0.00    0.00   96.11
19:49:04       0    1.01    0.00    1.01   17.17    0.00    0.00    0.00    0.00    0.00   80.81
19:49:04       1    1.98    0.00    0.99    0.00    0.00    0.00    0.00    0.00    0.00   97.03
19:49:04       2    1.01    0.00    0.00    0.00    0.00    1.01    0.00    0.00    0.00   97.98
19:49:04       3    0.00    0.00    0.99    0.00    0.00    0.99    0.00    0.00    0.00   98.02
19:49:04       4    1.98    0.00    1.98    0.00    0.00    0.00    0.00    0.00    0.00   96.04
19:49:04       5    1.00    0.00    1.00    0.00    0.00    0.00    0.00    0.00    0.00   98.00
19:49:04       6    1.00    0.00    1.00    0.00    0.00    0.00    0.00    0.00    0.00   98.00
19:49:04       7    1.98    0.00    0.99    0.00    0.00    0.00    0.00    0.00    0.00   97.03
```

`mpstat`将类似的 CPU 信息打印到顶部，但按 CPU 线程细分。 同时查看所有内核可用于检测高度不平衡的 CPU 使用率，例如，当单个线程应用程序以 100% 的利用率使用一个内核时。 聚合到系统中的所有 CPU 时，此问题可能更难发现。

### <a name="vmstat"></a>Vmstat

```
$ vmstat [interval]
procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
 2  0      0 43300372 545716 19691456    0    0     3    50    3    3  2  1 95  1  0
```

`vmstat`提供类似的信息和`mpstat``top`，枚举等待 CPU （r 列）、内存统计信息和在每个工作状态中花费的 CPU 时间百分比的进程数。

## <a name="memory"></a>内存

记忆是一个非常重要的，而且幸好是容易跟踪的资源。某些工具可以同时报告 CPU 和内存`vmstat`，例如 。 但是，像`free`这样的工具对于快速调试可能仍然很有用。

### <a name="free"></a>可用

```
$ free -m
              total        used        free      shared  buff/cache   available
Mem:          64403        2338       42485           1       19579       61223
Swap:             0           0           0
```

`free`提供有关总内存以及使用和空闲内存的基本信息。 `vmstat`由于其提供滚动输出的能力，即使对于基本内存分析也更有用。

## <a name="disk"></a>磁盘

这些工具测量磁盘 IOPS、等待队列和总吞吐量。 

### <a name="iostat"></a>iostat

```
$ iostat -xy [interval] [count]
$ iostat -xy 1 1
Linux 4.15.0-1064-azure (aks-main-10212767-vmss000001)  02/10/20        _x86_64_        (8 CPU)

avg-cpu:  %user   %nice %system %iowait  %steal   %idle
           3.42    0.00    2.92    1.90    0.00   91.76

Device:         rrqm/s   wrqm/s     r/s     w/s    rkB/s    wkB/s avgrq-sz avgqu-sz   await r_await w_await  svctm  %util
loop0             0.00     0.00    0.00    0.00     0.00     0.00     0.00     0.00    0.00    0.00    0.00   0.00   0.00
sdb               0.00     0.00    0.00    0.00     0.00     0.00     0.00     0.00    0.00    0.00    0.00   0.00   0.00
sda               0.00    56.00    0.00   65.00     0.00   504.00    15.51     0.01    3.02    0.00    3.02   0.12   0.80
scd0              0.00     0.00    0.00    0.00     0.00     0.00     0.00     0.00    0.00    0.00    0.00   0.00   0.00
```

`iostat`深入了解磁盘利用率。 此调用通过`-x`扩展统计信息，`-y`跳过自启动以来的初始输出打印系统平均值，并`1 1`指定我们想要的 1 秒间隔，在一个输出块后结束。 

`iostat`公开了许多有用的统计信息：

- `r/s`每秒`w/s`读取和每秒写入。 这些值的总和是 IOPS。
- `rkB/s`并且`wkB/s`是每秒读/写入的千字节。 这些值的总和是吞吐量。
- `await`是排队请求的平均 iowait 时间（以毫秒为单位）。
- `avgqu-sz`是提供间隔内的平均队列大小。

在 Azure VM 上：

- 单个块设备`r/s`的总和`w/s`和不能超过该磁盘的 SKU 限制。
- 单个块设备`rkB/s`的总和`wkB/s`和不能超过该磁盘的 SKU 限制
- 和`w/s`的所有`r/s`块设备的总和和不能超过 VM SKU 的限制。
- 所有块设备的`rkB/s`和 'wkB/s 的总和不得超过 VM SKU 的限制。

请注意，OS 磁盘计为与其容量对应的最小 SKU 的托管磁盘。 例如，1024GB OS 磁盘对应于 P30 磁盘。 临时 OS 磁盘和临时磁盘没有单独的磁盘限制;它们仅受完整的 VM 限制的限制。

await 或 avgqu-sz 的非零值也是 IO 争用的良好指标。

## <a name="network"></a>网络

这些工具测量网络统计信息，如吞吐量、传输故障和利用率。 更深入的分析可以公开有关拥塞和丢弃数据包的细粒度 TCP 统计信息。

### <a name="sar"></a>特区

```
$ sar -n DEV [interval]
22:36:57        IFACE   rxpck/s   txpck/s    rxkB/s    txkB/s   rxcmp/s   txcmp/s  rxmcst/s   %ifutil
22:36:58      docker0      0.00      0.00      0.00      0.00      0.00      0.00      0.00      0.00
22:36:58    azv604be75d832      1.00      9.00      0.06      1.04      0.00      0.00      0.00      0.00
22:36:58       azure0     68.00     79.00     27.79     52.79      0.00      0.00      0.00      0.00
22:36:58    azv4a8e7704a5b    202.00    207.00     37.51     21.86      0.00      0.00      0.00      0.00
22:36:58    azve83c28f6d1c     21.00     30.00     24.12      4.11      0.00      0.00      0.00      0.00
22:36:58         eth0    314.00    321.00     70.87    163.28      0.00      0.00      0.00      0.00
22:36:58    azva3128390bff     12.00     20.00      1.14      2.29      0.00      0.00      0.00      0.00
22:36:58    azvf46c95ddea3     10.00     18.00     31.47      1.36      0.00      0.00      0.00      0.00
22:36:58       enP1s1     74.00    374.00     29.36    166.94      0.00      0.00      0.00      0.00
22:36:58           lo      0.00      0.00      0.00      0.00      0.00      0.00      0.00      0.00
22:36:58    azvdbf16b0b2fc      9.00     19.00      3.36      1.18      0.00      0.00      0.00      0.00
```

`sar`是进行广泛分析的强大工具。 虽然此示例使用其测量网络统计信息的能力，但它在测量 CPU 和内存消耗方面同样强大。 此示例`sar`使用`-n`标志调用以指定`DEV`（网络设备）关键字，按设备显示网络吞吐量。

- 和`txKb/s`的总`rxKb/s`吞吐量是给定设备的总吞吐量。 当此值超过预配 Azure NIC 的限制时，计算机上的工作负荷将体验到更高的网络延迟。
- `%ifutil`测量给定设备的利用率。 当此值接近 100% 时，工作负载将经历更高的网络延迟。

```
$ sar -n TCP,ETCP [interval]
Linux 4.15.0-1064-azure (aks-main-10212767-vmss000001)  02/10/20        _x86_64_        (8 CPU)

22:50:08     active/s passive/s    iseg/s    oseg/s
22:50:09         2.00      0.00     19.00     24.00

22:50:08     atmptf/s  estres/s retrans/s isegerr/s   orsts/s
22:50:09         0.00      0.00      0.00      0.00      0.00

Average:     active/s passive/s    iseg/s    oseg/s
Average:         2.00      0.00     19.00     24.00

Average:     atmptf/s  estres/s retrans/s isegerr/s   orsts/s
Average:         0.00      0.00      0.00      0.00      0.00
```

此调用`sar`使用`TCP,ETCP`关键字检查 TCP 连接。 最后一行的第三列"retrans"是每秒重新传输 TCP 的数量。 此字段的较高值表示网络连接不可靠。 在第一行和第三行中，"活动"表示来自本地设备的连接，而"远程"表示传入连接。  Azure 上的一个常见问题是 SNAT 端口`sar`耗尽，这可以帮助检测。 SNAT 端口耗尽将表现为高"活动"值，因为问题是由于出站率高、本地启动的 TCP 连接。

按`sar`间隔，它打印滚动输出，然后打印包含调用的平均结果的最终输出行。

### <a name="netstat"></a>netstat

```
$ netstat -s
Ip:
    71046295 total packets received
    78 forwarded
    0 incoming packets discarded
    71046066 incoming packets delivered
    83774622 requests sent out
    40 outgoing packets dropped
Icmp:
    103 ICMP messages received
    0 input ICMP message failed.
    ICMP input histogram:
        destination unreachable: 103
    412802 ICMP messages sent
    0 ICMP messages failed
    ICMP output histogram:
        destination unreachable: 412802
IcmpMsg:
        InType3: 103
        OutType3: 412802
Tcp:
    11487089 active connections openings
    592 passive connection openings
    1137 failed connection attempts
    404 connection resets received
    17 connections established
    70880911 segments received
    95242567 segments send out
    176658 segments retransmited
    3 bad segments received.
    163295 resets sent
Udp:
    164968 packets received
    84 packets to unknown port received.
    0 packet receive errors
    165082 packets sent
UdpLite:
TcpExt:
    5 resets received for embryonic SYN_RECV sockets
    1670559 TCP sockets finished time wait in fast timer
    95 packets rejects in established connections because of timestamp
    756870 delayed acks sent
    2236 delayed acks further delayed because of locked socket
    Quick ack mode was activated 479 times
    11983969 packet headers predicted
    25061447 acknowledgments not containing data payload received
    5596263 predicted acknowledgments
    19 times recovered from packet loss by selective acknowledgements
    Detected reordering 114 times using SACK
    Detected reordering 4 times using time stamp
    5 congestion windows fully recovered without slow start
    1 congestion windows partially recovered using Hoe heuristic
    5 congestion windows recovered without slow start by DSACK
    111 congestion windows recovered without slow start after partial ack
    73 fast retransmits
    26 retransmits in slow start
    311 other TCP timeouts
    TCPLossProbes: 198845
    TCPLossProbeRecovery: 147
    480 DSACKs sent for old packets
    175310 DSACKs received
    316 connections reset due to unexpected data
    272 connections reset due to early user close
    5 connections aborted due to timeout
    TCPDSACKIgnoredNoUndo: 8498
    TCPSpuriousRTOs: 1
    TCPSackShifted: 3
    TCPSackMerged: 9
    TCPSackShiftFallback: 177
    IPReversePathFilter: 4
    TCPRcvCoalesce: 1501457
    TCPOFOQueue: 9898
    TCPChallengeACK: 342
    TCPSYNChallenge: 3
    TCPSpuriousRtxHostQueues: 17
    TCPAutoCorking: 2315642
    TCPFromZeroWindowAdv: 483
    TCPToZeroWindowAdv: 483
    TCPWantZeroWindowAdv: 115
    TCPSynRetrans: 885
    TCPOrigDataSent: 51140171
    TCPHystartTrainDetect: 349
    TCPHystartTrainCwnd: 7045
    TCPHystartDelayDetect: 26
    TCPHystartDelayCwnd: 862
    TCPACKSkippedPAWS: 3
    TCPACKSkippedSeq: 4
    TCPKeepAlive: 62517
IpExt:
    InOctets: 36416951539
    OutOctets: 41520580596
    InNoECTPkts: 86631440
    InECT0Pkts: 14
```

`netstat`可以介绍各种网络统计信息，此处调用摘要输出。 根据问题，这里有许多有用的字段。 TCP 部分中的一个有用字段是"连接尝试失败"。 这可能表示 SNAT 端口耗尽或进行出站连接的其他问题。 高重新传输段率（也属于 TCP 部分）可能表示数据包传递出现问题。 
