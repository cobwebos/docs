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
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2020
ms.locfileid: "77925600"
---
# <a name="linux-performance-troubleshooting"></a>Linux 性能故障排除

Linux 计算机上的资源耗尽是常见问题，可以通过多种不同的症状进行清单。 本文档提供可用于帮助诊断此类问题的工具的高级概述。

其中的许多工具都接受生成滚动输出的间隔。 这种输出格式通常使发现模式更为简单。 如果接受，示例调用将包含 `[interval]`。

其中的许多工具都有大量的历史记录和各种配置选项。 本页仅提供了一个简单的调用子集来突出显示常见问题。 信息的规范源始终是每个特定工具的参考文档。 与此处提供的文档相比，该文档将更为全面。

## <a name="guidance"></a>指南

在您的方法中以系统化方式调查性能问题。 两种常见方法是使用（利用率、饱和度、错误）和红色（速率、错误、持续时间）。 红色通常用于基于请求的监视的服务上下文中。 使用通常用于监视资源：针对计算机中的每个资源、监视利用率、饱和度和错误。 在任何计算机上，四种主要类型的资源是 cpu、内存、磁盘和网络。 其中任何资源的高利用率、饱和度或错误率都表明系统可能存在问题。 存在问题时，请调查根本原因：为什么磁盘 IO 延迟很高？ 磁盘或虚拟机 SKU 是否受到限制？ 哪些进程正在写入设备以及哪些文件？

解决这些问题的常见问题和指示器的一些示例：
- IOPS 限制：使用 iostat 测量每个设备的 IOPS。 请确保没有单个磁盘超出其限制，并且所有磁盘的总和小于虚拟机的限制。
- 带宽限制：使用 iostat 作为 IOPS，但测量读/写吞吐量。 确保每设备和聚合吞吐量低于带宽限制。
- SNAT 消耗：此项可以是以 SAR 形式表现为高活动（出站）连接。 
- 数据包丢失：此操作可通过 TCP 重传计数（相对于发送/接收的计数）来衡量。 `sar` 和 `netstat` 都可以显示此信息。

## <a name="general"></a>常规

这些工具是通用的，涵盖基本的系统信息。 它们是进行进一步调查的良好开端。

### <a name="uptime"></a>olo

```
$ uptime
 19:32:33 up 17 days, 12:36,  0 users,  load average: 0.21, 0.77, 0.69
```

运行时间可提供系统运行时间和1、5和15分钟的负载平均值。 这些负载平均值大致对应于执行工作的线程或等待不间断工作完成。 在绝对情况下，这些数字很难解释，但随着时间的推移，它们可告诉我们有用的信息：

- 每分钟平均 > 5 分钟平均意味着负载增加。
- 每分钟平均 < 5 分钟平均意味着负载递减。

运行时间还可能会导致信息无法使用的原因：此问题可能已自行解决或重启，然后用户才能访问计算机。

负载平均值大于可用的 CPU 线程数可能表示给定的工作负荷出现性能问题。

### <a name="dmesg"></a>dmesg

```
$ dmesg | tail 
$ dmesg --level=err | tail
```

dmesg 转储内核缓冲区。 类似于 OOMKill 的事件将条目添加到内核缓冲区。 在 dmesg 日志中查找 OOMKill 或其他资源消耗消息，这是一个很明显的问题。

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
- CPU 使用率，在这种情况下，主要显示空闲时间。
- 总计、可用和使用的系统内存。

`top` 可能会丢失生存期较短的进程;诸如 `htop` 和 `atop` 之类的替代方法在修复其中一些缺点时提供类似的接口。

## <a name="cpu"></a>CPU

这些工具提供了 CPU 利用率信息。 这对于滚动输出特别有用，在这种情况下，可以轻松地发现模式。

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

`mpstat` 将相似的 CPU 信息打印到顶部，但按 CPU 线程细分。 一次查看所有内核对于检测高度不均衡的 CPU 使用率非常有用，例如，单线程应用程序使用1个核心100% 的利用率。 在系统中的所有 Cpu 上聚合时，此问题可能更难发现。

### <a name="vmstat"></a>vmstat

```
$ vmstat [interval]
procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
 2  0      0 43300372 545716 19691456    0    0     3    50    3    3  2  1 95  1  0
```

`vmstat` 提供类似的信息 `mpstat` 和 `top`、枚举等待 CPU 的进程数（r 列）、内存统计信息，以及每个工作状态所用的 CPU 时间百分比。

## <a name="memory"></a>内存

内存非常重要，令人欣慰易于跟踪。某些工具可以报告 CPU 和内存，如 `vmstat`。 但 `free` 之类的工具仍适用于快速调试。

### <a name="free"></a>可用

```
$ free -m
              total        used        free      shared  buff/cache   available
Mem:          64403        2338       42485           1       19579       61223
Swap:             0           0           0
```

`free` 提供有关内存总量以及已用内存和可用内存的基本信息。 `vmstat` 可能更适用于基本内存分析，因为它能够提供滚动输出。

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

`iostat` 提供了深入了解磁盘利用率的信息。 此调用通过 `-x` 传递扩展统计信息，`-y` 从启动后跳过初始输出打印系统平均值，并 `1 1` 指定我们想要1秒的间隔，在一个输出块之后结束。 

`iostat` 公开了许多有用的统计信息：

- 每秒读取 `r/s` 和 `w/s`，每秒写入一次。 这些值的总和是 IOPS。
- 每秒读取/写入 `rkB/s` 和 `wkB/s`。 这些值的总和是吞吐量。
- `await` 是排队请求的平均 iowait 时间（以毫秒为单位）。
- `avgqu-sz` 是所提供的时间间隔内的平均队列大小。

在 Azure VM 上：

- 单个块设备 `r/s` 和 `w/s` 的总和可能不会超过该磁盘的 SKU 限制。
- 单个块设备的 `rkB/s` 和 `wkB/s` 的总和可能不会超过该磁盘的 SKU 限制
- 所有块设备的 `r/s` 和 `w/s` 的总和可能不会超过 VM SKU 的限制。
- 所有块设备的 `rkB/s` 和 ' wkB/s 的总和可能不会超过 VM SKU 的限制。

请注意，操作系统磁盘计数为与容量相对应的最小 SKU 的托管磁盘。 例如，1024GB OS 磁盘对应于 P30 磁盘。 暂时操作系统磁盘和临时磁盘没有单个磁盘限制;它们仅受完全 VM 限制的限制。

Await 或 avgqu-sz 的非零值也是 IO 争用的良好指示器。

## <a name="network"></a>网络

这些工具测量吞吐量、传输故障和利用率等网络统计信息。 更深入的分析可以公开有关拥塞和丢弃数据包的细化 TCP 统计信息。

### <a name="sar"></a>香港

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

`sar` 是一种功能强大的工具，可用于各种分析。 虽然此示例使用其度量网络统计信息的能力，但它在衡量 CPU 和内存消耗方面的功能同样非常强大。 此示例使用 `-n` 标志调用 `sar` 来指定 `DEV` （网络设备）关键字，并按设备显示网络吞吐量。

- `rxKb/s` 和 `txKb/s` 的总和是给定设备的总吞吐量。 如果此值超过预配的 Azure NIC 的限制，则计算机上的工作负荷将会出现网络延迟增加。
- `%ifutil` 度量给定设备的利用率。 由于此值接近100%，因此工作负荷会导致网络延迟增加。

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

此调用 `sar` 使用 `TCP,ETCP` 关键字来检查 TCP 连接。 最后一行 "retrans" 中的第三列是每秒 TCP 重新传输的次数。 此字段的最大值表示不可靠的网络连接。 在第一行和第三行中，"主动" 表示从本地设备发出连接，而 "远程" 表示传入连接。  Azure 上的一个常见问题是 SNAT 端口耗尽，这 `sar` 有助于检测。 SNAT 端口耗尽会表现为高 "活动" 值，因为问题是由本地启动的出站 TCP 连接产生的。

随着 `sar` 采用间隔，它将打印滚动输出，然后打印输出的最后一行，其中包含调用的平均结果。

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

`netstat` 可以 introspect 多种网络统计信息，这里将使用摘要输出调用。 这里有许多有用的字段，具体取决于问题。 TCP 部分中的一个有用字段是 "连接尝试失败"。 这可能表示发生了 SNAT 端口耗尽或其他导致出站连接的问题。 重新传输段的速率（也在 TCP 部分下）可能指示数据包传递出现问题。 
