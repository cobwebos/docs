---
title: Linux 性能工具
titleSuffix: Azure Kubernetes Service
description: 了解如何在使用 Azure Kubernetes Service (AKS) 时使用 Linux 性能工具排查和解决常见问题。
services: container-service
author: alexeldeib
ms.service: container-service
ms.topic: troubleshooting
ms.date: 02/10/2020
ms.author: aleldeib
ms.openlocfilehash: 74f65780594c7bc938ed6d59437473c4363e5848
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90982035"
---
# <a name="linux-performance-troubleshooting"></a>Linux 性能故障排除

Linux 计算机上耗尽资源是一个常见问题，可以通过各种症状表现出来。 本文档从较高层面概述可帮助诊断此类问题的工具。

其中的许多工具接受生成滚动输出所遵循的间隔。 此输出格式往往使得模式的查明要容易得多。 接受后，示例调用将包含 `[interval]`。

其中的许多工具提供详细的历史记录以及各种配置选项。 本页仅提供一个简单的调用子集来突出常见问题。 规范的信息来源始终是每个特定工具的参考文档。 与本文相比，该文档的内容要全面得多。

## <a name="guidance"></a>指南

采用系统性的方法调查性能问题。 两种常用方法是 USE（利用率、饱和度、错误）和 RED（比率、错误、持续时间）。 RED 通常在服务上下文中用于基于请求的监视。 USE 通常用于监视资源：针对计算机中的每个资源，监视利用率、饱和度和错误。 在任何计算机上，四种主要类型的资源是 CPU、内存、磁盘和网络。 如果其中的任何资源出现较高的利用率、饱和度或错误率，则表明系统可能出现了问题。 出现问题时调查根本原因：磁盘 IO 延迟为何这么高？ 磁盘或虚拟机 SKU 是否受到限制？ 哪些进程正在写入到设备，以及正在写入到哪些文件？

常见问题的一些示例，以及在诊断时考虑的迹象：
- IOPS 限制：使用 iostat 测量每个设备的 IOPS。 确保没有任何一个磁盘超过其限制，并且所有磁盘的 IOPS 总和小于虚拟机的限制。
- 带宽限制：像测量 IOPS 时那样使用 iostat，但此处测量的是读/写吞吐量。 确保每个设备的吞吐量和聚合吞吐量低于带宽限制。
- SNAT 耗尽：此问题的表现形式可能是 SAR 中的活动（出站）连接数较多。 
- 丢包率：可以由代理通过相对于发送/接收计数的 TCP 重传计数来测量此值。 `sar` 和 `netstat` 都可以显示此信息。

## <a name="general"></a>常规

这些工具是通用型的，涵盖基本系统信息。 它们是进一步调查的良好入手点。

### <a name="uptime"></a>运行时间

```
$ uptime
 19:32:33 up 17 days, 12:36,  0 users,  load average: 0.21, 0.77, 0.69
```

运行时间提供系统运行时间，以及 1、5 和 15 分钟负载平均值。 这些负载平均值大致对应于正在执行工作，或者正在等待不能间断的工作完成的线程。 从绝对意义上讲，这些数字可能难以解释，但经过一段时间的测量，它们就能反映有用的信息：

- 1 分钟平均值大于 5 分钟平均值意味着负载正在递增。
- 1 分钟平均值小于 5 分钟平均值意味着负载正在递减。

运行时间还可以阐明信息不可用的原因：问题可能已自行解决，或者在用户能够访问计算机之前通过重启予以解决。

负载平均值高于可用 CPU 线程数可能意味着给定的工作负荷出现了性能问题。

### <a name="dmesg"></a>dmesg

```
$ dmesg | tail 
$ dmesg --level=err | tail
```

dmesg 转储内核缓冲区。 OOMKill 等事件会将一个条目添加到内核缓冲区。 在 dmesg 日志中发现 OOMKill 或其他资源耗尽消息，是出现问题的明显迹象。

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

`top` 提供当前系统状态的全面概述。 标头提供一些有用的聚合信息：

- 任务状态：正在运行、正在睡眠、已停止。
- CPU 利用率，在本例中主要显示空闲时间。
- 总计、可用和已用的系统内存。

`top` 可能缺少生存期较短的进程；`htop` 和 `atop` 等替代项提供类似的接口，同时会修复其中的一些短板。

## <a name="cpu"></a>CPU

这些工具提供 CPU 利用率信息。 生成滚动输出（在其中可以轻松查明模式）时，这些信息特别有用。

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

`mpstat` 在最前面输出类似的 CPU 信息，但信息已按 CPU 线程细分。 一次性查看所有核心可能有利于检测 CPU 使用率严重不平衡的问题，例如，某个单线程应用程序以 100% 的利用率使用了一个核心。 针对系统中的所有 CPU 聚合时，此问题可能更难以查明。

### <a name="vmstat"></a>vmstat

```
$ vmstat [interval]
procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
 2  0      0 43300372 545716 19691456    0    0     3    50    3    3  2  1 95  1  0
```

`vmstat` 提供的信息与 `mpstat` 和 `top` 类似，它可以枚举正在等待 CPU 的进程数（r 列）、内存统计信息，以及每种工作状态下花费的 CPU 时间百分比。

## <a name="memory"></a>内存

内存非常重要，幸运的是，它是一种易于跟踪的资源。某些工具（例如 `vmstat`）可以报告 CPU 和内存。 但是，`free` 之类的工具可能仍很适合用于快速调试。

### <a name="free"></a>可用

```
$ free -m
              total        used        free      shared  buff/cache   available
Mem:          64403        2338       42485           1       19579       61223
Swap:             0           0           0
```

`free` 提供有关总内存量以及已用内存和可用内存的基本信息。 甚至对于基本的内存分析，`vmstat` 也可能会发挥更大的作用，因为它能够提供滚动输出。

## <a name="disk"></a>磁盘

这些工具会测量磁盘 IOPS、等待队列和总吞吐量。 

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

`iostat` 提供磁盘利用率的深入见解。 此调用会传递 `-x` 来提供扩展的统计信息，传递 `-y` 来跳过初始输出并列显自启动后的系统平均值，传递 `1 1` 来指定要使用 1 秒间隔，并在一个输出块之后结束。 

`iostat` 公开许多有用的统计信息：

- `r/s` 和 `w/s` 是每秒读取次数和每秒写入次数。 这些值的总和即为 IOPS。
- `rkB/s` 和 `wkB/s` 是每秒读取/写入的 KB 数。 这些值的总和即为吞吐量。
- `await` 是排队请求的平均 iowait 时间（毫秒）。
- `avgqu-sz` 是所提供间隔内的平均队列大小。

在 Azure VM 上：

- 单个块设备的 `r/s` 与 `w/s` 总和不可以超过该磁盘的 SKU 限制。
- 单个块设备的 `rkB/s` 与 `wkB/s` 总和不可以超过该磁盘的 SKU 限制。
- 所有块设备的 `r/s` 与 `w/s` 总和不可以超过 VM SKU 的限制。
- 所有块设备的 `rkB/s` 与 `wkB/s 总和不可以超过 VM SKU 的限制。

请注意，OS 磁盘算作与其容量对应的最小 SKU 的托管磁盘。 例如，1024GB OS 磁盘对应于 P30 磁盘。 瞬态 OS 磁盘和临时磁盘没有各自的磁盘限制；它们只需遵守整个 VM 的限制。

await 或 avgqu-sz 的非零值也能很好地反映 IO 争用情况。

## <a name="network"></a>网络

这些工具可测量吞吐量、传输故障和利用率等网络统计信息。 更深入的分析可以公开有关拥塞和丢包的精细 TCP 统计信息。

### <a name="sar"></a>sar

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

`sar` 是用于各种分析的强大工具。 尽管本示例只是使用此工具的功能来测量网络统计信息，但此工具在测量 CPU 和内存消耗量方面同样非常有效。 本示例结合 `-n` 标志调用 `sar`，以指定 `DEV`（网络设备）关键字，并按设备显示网络吞吐量。

- `rxKb/s` 与 `txKb/s` 的总和是给定设备的总吞吐量。 如果此值超过了预配的 Azure NIC 的限制，则计算机上的工作负荷将会出现更高的网络延迟。
- `%ifutil` 测量给定设备的利用率。 当此值接近 100% 时，工作负荷会出现更高的网络延迟。

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

此 `sar` 调用使用 `TCP,ETCP` 关键字来检查 TCP 连接。 最后一行的第三列“retrans”是每秒的 TCP 重传次数。 如果此字段的值较大，则表示网络连接不可靠。 在第一行和第三行中，“active”表示连接从本地设备发起，而“remote”则表示传入连接。  SNAT 端口耗尽是 Azure 上的一个常见问题，可以借助 `sar` 进行检测。 SNAT 端口耗尽的表现形式是“active”值较大，因为此问题的原因是以较高的频率从本地发起出站 TCP 连接。

当 `sar` 进入某个间隔时，会列显滚动输出，然后列显最后一行输出，其中包含该调用的平均结果。

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

`netstat` 可以探究各种网络统计信息，在此处它是结合摘要输出调用的。 此处根据具体的问题提供了许多有用字段。 “TCP”部分中的一个有用字段是“失败连接尝试次数”。 它可能表示发生了 SNAT 端口耗尽问题，或者在发起出站连接时出现了其他问题。 重传段的比率（也在“TCP”部分下）较高可能表示数据包传送出现了问题。 
