---
title: 针对 Azure Vm 的 TCP/IP 性能优化 |Microsoft Docs
description: 了解各种常用的 TCP/IP 性能优化技术及其与 Azure VM 之间的关系。
services: virtual-network
documentationcenter: na
author: rimayber
manager: paragk
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/02/2019
ms.author: rimayber
ms.reviewer: dgoddard, stegag, steveesp, minale, btalb, prachank
ms.openlocfilehash: bb23484903ac3ce129c6e7a7a27e0765c227fb1d
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/17/2019
ms.locfileid: "68297784"
---
# <a name="tcpip-performance-tuning-for-azure-vms"></a>适用于 Azure VM 的 TCP/IP 性能优化

本文介绍常用的 TCP/IP 性能优化技术，以及将其用于 Azure 上运行的虚拟机时要注意的一些事项。 其中将会简要地概述这些技术，并探讨其优化方式。

## <a name="common-tcpip-tuning-techniques"></a>常用的 TCP/IP 优化技术

### <a name="mtu-fragmentation-and-large-send-offload"></a>MTU、分段和大规模发送卸载

#### <a name="mtu"></a>MTU

最大传输单元 (MTU) 是可以通过网络接口发送的最大帧（数据包），以字节为单位。 MTU 是可配置的设置。 在 Azure VM 上使用的默认 MTU（也是大多数网络设备上全局使用的默认设置）为 1,500 字节。

#### <a name="fragmentation"></a>分段

当发送的数据包超过网络接口的 MTU 时，就会发生分段。 TCP/IP 堆栈会将该数据包分解成符合接口 MTU 的较小片段。 分段发生在 IP 层，与底层协议（例如 TCP）无关。 通过 MTU 为 1,500 的网络接口发送 2,000 字节的数据包时，该数据包将分解成一个 1,500 字节的数据包和一个 500 字节的数据包。

源与目标之间的路径中的网络设备可能会丢弃超过 MTU 的数据包，或者将数据包分段成较小的片段。

#### <a name="the-dont-fragment-bit-in-an-ip-packet"></a>IP 数据包中的 "不分段" 位

不分段 (DF) 位是 IP 协议标头中的标志。 DF 位指示发送方与接收方之间的路径中的网络设备不得将数据包分段。 设置此位的原因有很多。 （请参阅本文的“路径 MTU 发现”部分中的一个示例。）当网络设备接收到不分段位集的数据包, 并且该数据包超过设备的接口 MTU 时, 标准行为是设备丢弃数据包。 设备会将一条 ICMP Fragmentation Needed（需要 ICMP 分段）消息发回给数据包的原始源。

#### <a name="performance-implications-of-fragmentation"></a>分段对性能的影响

分段可能会对性能造成负面影响。 影响性能的主要原因之一是分段再重组数据包会影响 CPU/内存。 当网络设备需要将某个数据包分段时，它必须分配 CPU/内存资源来执行分段。

重组数据包时也是如此。 网络设备必须存储所有片段，直到这些片段被接收并可重组成原始数据包为止。 此分段和重组过程还会导致延迟。

分段可能对性能造成的另一种负面影响是，分段的数据包可能失序。 不按顺序接收数据包时，某些类型的网络设备可能会丢弃数据包。 如果发生这种情况，必须重新传输整个数据包。

通常由网络防火墙等安全设备或网络设备的接收缓冲区用尽时丢弃片段。 当网络设备的接收缓冲区耗尽时，网络设备会尝试重组分段的数据包，但没有资源可用于存储和恢复数据包。

可将分段视为一种负面操作，但通过 Internet 连接各种网络时，有必要支持分段。

#### <a name="benefits-and-consequences-of-modifying-the-mtu"></a>修改 MTU 的好处和后果

一般情况下，可以通过增大 MTU 来创建更高效的网络。 传输的每个数据包会包含已添加到原始数据包的标头信息。 当分段操作创建更多数据包时，标头开销将会增加，因此会降低网络的效率。

下面是一个示例。 以太网标头大小为 14 字节，加上用于确保帧一致性的 4 字节帧检查序列。 如果发送一个 2,000 字节的数据包，则会在网络中添加 18 字节的以太网开销。 如果将该数据包分段成一个 1,500 字节的数据包和一个 500 字节的数据包，则每个数据包以太网标头为 18 字节，总共为 36 字节。

请记住，增大 MTU 不一定可以创建更高效的网络。 如果应用程序发送仅有 500 字节的数据包，则不管 MTU 是 1,500 字节还是 9,000 字节，标头开销都是相同的。 仅当网络使用受 MTU 影响的更大数据包大小时，才会变得更加高效。

#### <a name="azure-and-vm-mtu"></a>Azure 和 VM MTU

Azure VM 的默认 MTU 为 1,500 字节。 Azure 虚拟网络堆栈尝试按 1,400 字节将数据包分段。

请注意，虚拟网络堆栈本质上并不是低效的，因为即使 VM 的 MTU 为 1,500，该堆栈也会按 1,400 字节将数据包分段。 大部分网络数据包远远小于 1,400 或 1,500 字节。

#### <a name="azure-and-fragmentation"></a>Azure 和分段

虚拟网络堆栈设置为丢弃“无序片段”（不按原始分段顺序抵达的分段数据包）。 丢弃这些数据包的主要原因是 2018 年 11 月公布了一个称作 FragmentSmack 的网络安全漏洞。

FragmentSmack 是 Linux 内核处理分段的 IPv4 和 IPv6 数据包重组时存在的一个缺陷。 远程攻击者可能会利用此缺陷来触发高开销的片段重组操作，导致目标系统上的 CPU 用量增大并出现拒绝服务错误。

#### <a name="tune-the-mtu"></a>优化 MTU

可以像在任何其他操作系统中一样配置 Azure VM MTU。 但是，在配置 MTU 时，应考虑到 Azure 中发生的上述分段情况。

我们不建议客户增大 VM MTU。 本文旨在详细说明 Azure 如何实现 MTU 和执行分段。

> [!IMPORTANT]
>增大 MTU 不一定可以提高性能，反而可能会对应用程序的性能造成负面影响。
>
>

#### <a name="large-send-offload"></a>大规模发送卸载

大规模发送卸载 (LSO) 可以通过将数据包分段任务卸载到以太网适配器来提高网络性能。 启用 LSO 后，TCP/IP 堆栈会创建一个较大的 TCP 数据包并将其发送到以太网适配器进行分段，然后转发片段。 LSO 的好处是无需让 CPU 将数据包分段成符合 MTU 的大小，而是将该处理任务卸载到硬件中执行分段的以太网接口。 若要详细了解 LSO 的好处，请参阅[支持大规模发送卸载](https://docs.microsoft.com/windows-hardware/drivers/network/performance-in-network-adapters#supporting-large-send-offload-lso)。

启用 LSO 后，Azure 客户在执行数据包捕获时可能会看到较大的帧大小。 这种大帧大小可能会导致一些客户考虑到碎片发生, 或者不使用较大的 MTU。 以太网适配器可以使用 LSO 将更大的最大片段大小 (MSS) 播发到 TCP/IP 堆栈，以创建更大的 TCP 数据包。 然后，整个未分段的帧将转发到以太网适配器，并在 VM 上执行的数据包捕获中显示。 但是, 以太网适配器会根据以太网适配器的 MTU 将数据包分解为多个较小的帧。

### <a name="tcp-mss-window-scaling-and-pmtud"></a>TCP MSS 窗口缩放和 PMTUD

#### <a name="tcp-maximum-segment-size"></a>TCP 最大片段大小

TCP 最大片段大小 (MSS) 是一项限制 TCP 片段大小的设置，可避免将 TCP 数据包分段。 操作系统往往使用以下公式来设置 MSS：

`MSS = MTU - (IP header size + TCP header size)`

IP 标头和 TCP 标头各为 20 字节，总共为 40 字节。 因此，MTU 为 1,500 的接口的 MSS 为 1,460。 但是，MSS 是可配置的。

此项设置符合在源与目标之间建立 TCP 会话时的 TCP 三向握手。 双方都会发送 MSS 值，两个值的较小者用于 TCP 连接。

请记住，源和目标的 MTU 不是 MSS 值的唯一决定因素。 中间网络设备（例如 VPN 网关，包括 Azure VPN 网关）可以独立于源和目标调整 MTU，以确保提供最佳网络性能。

#### <a name="path-mtu-discovery"></a>路径 MTU 发现

MSS 会经过协商，但不一定表明可以使用实际 MSS。 这是因为，源与目标之间的路径中的其他网络设备使用的 MTU 值，可能小于源和目标的 MTU 值。 在这种情况下，MTU 小于数据包的设备将会丢弃该数据包。 该设备会发回包含其 MTU 的 ICMP Fragmentation Needed (Type 3, Code 4) 消息。 此 ICMP 消息可让源主机适当减小其路径 MTU。 此过程称为“路径 MTU 发现”(PMTUD)。

PMTUD 过程的效率低下，会影响网络性能。 如果发送的数据包超过网络路径的 MTU，需要使用更低的 MSS 重新传输该数据包。 如果发送方未收到 ICMP Fragmentation Needed 消息，原因可能是路径中存在网络防火墙（通常称为“PMTUD 黑洞”），因此发送方不知道它需要降低 MSS 并持续重新传输数据包。  这就是我们不建议增加 Azure VM MTU 的原因。

#### <a name="vpn-and-mtu"></a>VPN 和 MTU

如果使用执行封装（例如 IPsec VPN）的 VM，在数据包大小和 MTU 方面还需要注意其他一项事项。 VPN 将更多标头添加到数据包，这会增大数据包的大小并需要减小 MSS。

对于 Azure，我们建议将 TCP MSS 钳位设置为 1,350 字节，将隧道接口 MTU 设置为 1,400。 有关详细信息，请参阅 [VPN 设备和 IPSec/IKE 参数页](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices)。

### <a name="latency-round-trip-time-and-tcp-window-scaling"></a>延迟、往返时间和 TCP 窗口缩放

#### <a name="latency-and-round-trip-time"></a>延迟和往返时间

网络延迟受制于光纤网络的光速。 TCP 的网络吞吐量实际上还受制于两个网络设备之间的往返时间 (RTT)。

| | | | |
|-|-|-|-|
|Route |**距离**|**单向时间**|**RTT**|
|纽约到旧金山|4148千米|21毫秒|42 ms|
|纽约到伦敦|5585千米|28毫秒|56 ms|
|纽约到悉尼|15993千米|80 ms|160 ms|

此表显示了两个位置之间的直线距离。 网络中的距离往往大于直线距离。 下面是计算受制于光速的最小 RTT 的简单公式：

`minimum RTT = 2 * (Distance in kilometers / Speed of propagation)`

可以使用 200 作为传播速度。 这是光在 1 毫秒内传播的距离（以米为单位）。

接下来, 我们将纽约转到旧金山。 两者之间的直线距离为 4,148 公里。 将该值插入公式，会得到以下结果：

`Minimum RTT = 2 * (4,148 / 200)`

公式输出以毫秒为单位。

若要获得最佳网络性能，符合逻辑的做法是选择两者之间距离最短的目的地。 还应将虚拟网络设计为优化流量路径并减少延迟。 有关详细信息，请参阅本文的“网络设计注意事项”部分。

#### <a name="latency-and-round-trip-time-effects-on-tcp"></a>延迟和往返时间对 TCP 的影响

往返时间直接影响到最大 TCP 吞吐量。 在 TCP 协议中，“窗口大小”是指发送方需要收到接收方的确认之前，可以通过 TCP 连接发送的最大流量。  如果 TCP MSS 设置为 1,460，TCP 窗口大小设置为 65,535，则发送方在必须收到接收方的确认之前，可以发送 45 个数据包。 如果发送方未收到确认，将重新传输数据。 公式如下：

`TCP window size / TCP MSS = packets sent`

在此示例中，65,535 / 1,460 的结果已四舍五入为 45。

正是这种“等待确认”状态（确保可靠传送数据的机制）导致 RTT 影响了 TCP 吞吐量。 发送方等待确认的时间越长，在发送更多数据之前需要等待的时间也就越长。

下面是计算单个 TCP 连接的最大吞吐量的公式：

`Window size / (RTT latency in milliseconds / 1,000) = maximum bytes/second`

下表显示了单个 TCP 连接的最大每秒兆字节吞吐量。 （为便于阅读，此处使用了每秒兆字节作为度量单位。）

| | | | |
|-|-|-|-|
|**TCP 窗口大小（字节）**|**RTT 延迟（毫秒）**|**最大每秒兆字节吞吐量**|**最大每秒兆位吞吐量**|
|65,535|1|65.54|524.29|
|65,535|30|2.18|17.48|
|65,535|60|1.09|8.74|
|65,535|90|.73|5.83|
|65,535|120|.55|4.37|

如果数据包丢失，则 TCP 连接的最大吞吐量将会减小，此时，发送方会重新传输它已发送的数据。

#### <a name="tcp-window-scaling"></a>TCP 窗口缩放

TCP 窗口缩放可以动态增大 TCP 窗口大小，以便在需要收到确认之前可以发送更多数据。 在前面的示例中，在需要收到确认之前会发送 45 个数据包。 如果增加在需要收到确认之前可发送的数据包数，则会减少发送方等待确认的次数，因而可以增大 TCP 最大吞吐量。

下表演示了这些关系：

| | | | |
|-|-|-|-|
|**TCP 窗口大小（字节）**|**RTT 延迟（毫秒）**|**最大每秒兆字节吞吐量**|**最大每秒兆位吞吐量**|
|65,535|30|2.18|17.48|
|131,070|30|4.37|34.95|
|262,140|30|8.74|69.91|
|524,280|30|17.48|139.81|

但是，TCP 窗口大小的 TCP 标头值长度仅为 2 个字节，这意味着，接收窗口的最大值为 65,535。 为了提高最大窗口大小，我们引入了 TCP 窗口缩放因子。

该缩放因子也是可以在操作系统中配置的设置。 下面是使用缩放因子计算 TCP 窗口大小的公式：

`TCP window size = TCP window size in bytes \* (2^scale factor)`

下面是窗口缩放因子为 3，窗口大小为 65,535 时的计算公式：

`65,535 \* (2^3) = 262,140 bytes`

缩放因子为 14 时，TCP 窗口大小为 14（允许的最大偏移量）。 TCP 窗口大小为 1,073,725,440 字节（8.5 千兆位）。

#### <a name="support-for-tcp-window-scaling"></a>TCP 窗口缩放支持

Windows 可为不同的连接类型设置不同的缩放因子。 （连接类包括数据中心、Internet 等。）可以使用 `Get-NetTCPConnection` PowerShell 命令查看窗口缩放连接类型：

```powershell
Get-NetTCPConnection
```

可以使用 `Get-NetTCPSetting` PowerShell 命令查看每个类的值：

```powershell
Get-NetTCPSetting
```

可以使用 `Set-NetTCPSetting` PowerShell 命令在 Windows 中设置初始 TCP 窗口大小和 TCP 缩放因子。 有关详细信息，请参阅 [Set-NetTCPSetting](https://docs.microsoft.com/powershell/module/nettcpip/set-nettcpsetting?view=win10-ps)。

```powershell
Set-NetTCPSetting
```

下面是 `AutoTuningLevel` 的有效 TCP 设置：

| | | | |
|-|-|-|-|
|**AutoTuningLevel**|**缩放因子**|**缩放乘数**|**用于计算<br/>最大窗口大小的公式**|
|已禁用|无|无|窗口大小|
|受限|4|2^4|窗口大小 * (2^4)|
|严格限制|2|2^2|窗口大小 * (2^2)|
|一般|8|2^8|窗口大小 * (2^8)|
|试验|14|2^14|窗口大小 * (2^14)|

这些设置很有可能会影响 TCP 性能，但请记住，Internet 中不受 Azure 控制的许多其他因素也可能会影响 TCP 性能。

#### <a name="increase-mtu-size"></a>增大 MTU 大小

由于 MTU 越大，MSS 也就越大，因此你可能想要知道，增大 MTU 是否可以提高 TCP 性能。 很有可能并非如此。 使用超过 TCP 流量大小的数据包大小既有利也有弊。 如前所述，影响 TCP 吞吐量性能的最重要因素是 TCP 窗口大小、丢包和 RTT。

> [!IMPORTANT]
> 我们不建议 Azure 客户更改虚拟机上的默认 MTU 值。
>
>

### <a name="accelerated-networking-and-receive-side-scaling"></a>加速网络和接收端缩放

#### <a name="accelerated-networking"></a>加速网络

一直以来，来宾 VM 和虚拟机监控程序/主机上的虚拟机网络功能都会大量消耗 CPU 资源。 传入主机的每个数据包由主机 CPU 在软件中进行处理，包括所有虚拟网络的封装和解封。 因此，通过主机的流量越多，CPU 负载就越高。 此外，如果主机 CPU 正忙于处理其他操作，则还会影响网络吞吐量和延迟。 Azure 使用加速网络解决了此问题。

加速网络通过 Azure 的内部可编程硬件以及 SR-IOV 之类的技术，提供一贯的超低网络延迟。 加速网络将大量的 Azure 软件定义网络堆栈从 CPU 转移到基于 FPGA 的 SmartNIC。 这项变化可让最终用户应用程序回收计算周期，减轻 VM 上的负载，减少波动和延迟不一致性。 换而言之，性能可能更具确定性。

加速网络通过允许来宾 VM 绕过主机并直接与主机的 SmartNIC 建立数据路径, 提高了性能。 加速网络的部分优势如下：

- **降低延迟/提高每秒数据包数 (pps)** ：从数据路径中去除虚拟交换机可以消除数据包在主机中进行策略处理所花费的时间，同时增大了 VM 中可处理的数据包数。

- **减少波动**：虚拟交换机处理取决于需要应用的策略数量，以及正在执行处理的 CPU 工作负荷。 将策略实施卸载到硬件消除了这种可变性，因为可以将数据包直接传送到 VM，消除了主机与 VM 之间的通信，以及所有的软件中断和上下文切换。

- **降低 CPU 利用率**：绕过主机中的虚拟交换机可以减少用于处理流量的 CPU 资源。

若要使用加速网络，需要在每个适用的 VM 上显式启用它。 有关说明，请参阅[创建启用加速网络的 Linux 虚拟机](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli)。

#### <a name="receive-side-scaling"></a>接收端缩放

接收端缩放 (RSS) 是一种网络驱动程序技术，它通过在多处理器系统中的多个 CPU 之间分配接收处理，更有效地分配网络流量的接收负载。 简单来说，RSS 可让系统处理更多的接收流量，因为它使用所有可用的 CPU，而不是只使用一个。 有关 RSS 的更多技术讨论，请参阅[接收端缩放简介](https://docs.microsoft.com/windows-hardware/drivers/network/introduction-to-receive-side-scaling)。

在 VM 上启用加速网络后，若要获得最佳性能，需要启用 RSS。 RSS 还可为不使用加速网络的 Vm 提供权益。 有关如何确定 RSS 是否已启用及其启用方法的概述，请参阅[优化 Azure 虚拟机的网络吞吐量](https://aka.ms/FastVM)。

### <a name="tcp-timewait-and-timewait-assassination"></a>TCP TIME_WAIT 和 TIME_WAIT 抹消

TCP TIME_WAIT 是影响网络和应用程序性能的另一项常用设置。 在用作客户端或服务器（“源 IP:源端口”+“目标 IP:目标端口”）的、不断打开和关闭多个套接字的繁忙 VM 上，在 TCP 正常运行期间，给定的套接字最终可能会长时间处于 TIME_WAIT 状态。 TIME_WAIT 状态是指在关闭某个套接字之前允许在其上传送任何附加的数据。 因此，TCP/IP 堆栈通常会通过静默丢弃客户端的 TCP SYN 数据包来防止重复使用套接字。

套接字处于 TIME_WAIT 状态的时间长短是可配置的。 该值的范围为 30 秒到 240 秒。 套接字是有限的资源，在任意给定时间可使用的套接字数目是可配置的。 （可用套接字数目通常约为 30,000 个。）如果可用套接字已耗尽，或者客户端和服务器的 TIME_WAIT 设置不匹配，当某个 VM 尝试重复使用处于 TIME_WAIT 状态的套接字时，新的连接将会失败，因为 TCP SYN 数据包已静默丢弃。

通常可以在操作系统的 TCP/IP 堆栈中配置出站套接字的端口范围值。 这同样适用于 TCP TIME_WAIT 设置和重复使用套接字的情况。 更改这些数字可能会提高可伸缩性。 但是，根据具体的情况，这些更改可能会导致互操作性问题。 应小心更改这些值。

可以使用 TIME_WAIT 抹消来解决此缩放限制。 使用 TIME_WAIT 抹消可以在某些情况下重用某个套接字，例如，当新连接的 IP 数据包中的序列号超过前一连接的最后一个数据包的序列号时。 在这种情况下，操作系统允许建立新连接（接受新的 SYN/ACK），并强制关闭处于 TIME_WAIT 状态的上一个连接。 Azure 中的 Windows VM 支持此功能。 若要了解其他 VM 是否支持此功能，请咨询 OS 供应商。

若要了解如何配置 TCP TIME_WAIT 设置和源端口范围，请参阅[可修改哪些设置来提高网络性能](https://docs.microsoft.com/biztalk/technical-guides/settings-that-can-be-modified-to-improve-network-performance)。

## <a name="virtual-network-factors-that-can-affect-performance"></a>可能会影响性能的虚拟网络因素

### <a name="vm-maximum-outbound-throughput"></a>VM 最大出站吞吐量

Azure 提供多种 VM 大小和类型，每种大小和类型的性能各不相同。 其中一项功能是以兆位/秒 (Mbps) 计量的网络吞吐量（或带宽）。 由于虚拟机托管在共享硬件上，因此网络容量需在使用同一硬件的虚拟机中公平地共享。 与更小的虚拟机相比，为更大的虚拟机分配的带宽更多。

分配给每个虚拟机的网络带宽按虚拟机的传出（出站）流量计算。 从虚拟机流出的所有网络流量均计入分配限制，不管流向哪个目标。 例如，如果虚拟机的限制为 1,000 Mbps，则不管出站流量的目标是同一虚拟网络中的另一虚拟机，还是 Azure 外部，均适用该限制。

传入流量不直接计算，或者说不直接受到限制。 但还有其他因素 (如 CPU 和存储限制) 可能会影响虚拟机处理传入数据的能力。

加速网络旨在改进网络性能（包括延迟、吞吐量和 CPU 使用率）。 加速网络可以提高虚拟机的吞吐量, 但最多只能执行虚拟机的分配带宽。

Azure 虚拟机上至少附加了一个网络接口。 它们可能包含多个网络接口。 分配给某个虚拟机的带宽是流经所有网络接口（已连接到该虚拟机）的所有出站流量的总和。 换言之，带宽是按虚拟机分配的，而不管该虚拟机上附加了多少个网络接口。

[Azure 中 Windows 虚拟机的大小](https://docs.microsoft.com/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json)详细说明了每种 VM 大小支持的预期出站吞吐量和网络接口数。 若要查看最大吞吐量，请选择一种类型（例如“常规用途”），然后在结果页上找到有关大小系列的部分（例如“Dv2 系列”）。  对于每个系列，有一个表格的最后一列中提供了网络规范，其标题为“最大 NIC 数/预期网络带宽 (MBps)”。

吞吐量限制适用于虚拟机。 吞吐量不受这些因素的影响：

- **网络接口数**：带宽限制适用于来自虚拟机的所有出站流量的总和。

- **加速网络**：尽管此功能有助于流量达到已发布的限制，但不会更改限制。

- **流量目标**：所有目标都计入出站限制。

- **协议**：基于所有协议的所有出站流量都计入限制。

有关详细信息，请参阅[虚拟机网络带宽](https://aka.ms/AzureBandwidth)。

### <a name="internet-performance-considerations"></a>Internet 性能注意事项

如本文所述，Internet 因素以及不受 Azure 控制的因素可能会影响网络性能。 以下是其中的部分因素：

- **延迟**：两个目标之间的往返时间可能会受中间网络问题、不采用“最短”距离路径的流量，以及欠佳对等互连路径的影响。

- **数据包丢失**：数据包丢失可能是网络拥塞、物理路径问题和性能不佳的网络设备造成的。

- **MTU 大小/分段**：在路径中分段可能导致数据延迟抵达，或数据包不按顺序抵达，而这可能会影响数据包的传送。

Traceroute 是一个不错的工具，它可以测量源设备与目标设备之间的每条网络路径上的网络性能特征（例如数据包丢失和延迟）。

### <a name="network-design-considerations"></a>网络设计注意事项

除了本文前面所述的考虑因素以外，虚拟网络的拓扑也可能会影响网络性能。 例如，将流量全局回传到单一中心虚拟网络的中心辐射型设计会造成网络延迟，因而会影响总体网络性能。

网络流量通过的网络设备数也会影响总体延迟。 例如，在中心辐射型设计中，如果流量在传输到 Internet 之前会通过辐射网络虚拟设备和中心虚拟设备，则网络虚拟设备可能会造成延迟。

### <a name="azure-regions-virtual-networks-and-latency"></a>Azure 区域、虚拟网络和延迟

Azure 区域由一个笼统的地理区域中的多个数据中心构成。 这些数据中心的实体位置可能并不相互靠近。 在某些情况下，它们之间相隔 10 公里。 虚拟网络是位于 Azure 物理数据中心网络顶部的逻辑叠加层。 虚拟网络并不意味着数据中心内部采用任何特定的网络拓扑。

例如，同一个虚拟网络和子网中的两个 VM 可能位于不同的机架、设备排中，甚至位于不同的数据中心内。 它们可能会按光纤电缆的英尺或公里数相分隔。 这种变数可能会在不同的 VM 之间造成可变的延迟（相差几毫秒）。

VM 的地理位置以及两个 VM 之间的潜在延迟可能受可用性集和可用性区域的配置影响。 但是，某个区域中数据中心之间的距离与该区域直接相关，主要受该区域中数据中心拓扑的影响。

### <a name="source-nat-port-exhaustion"></a>源 NAT 端口耗尽

Azure 中的部署可与 Azure 外部的公共 Internet 和/或公共 IP 地址空间中的终结点进行通信。 当实例发起出站连接时，Azure 会动态将专用 IP 地址映射到公共 IP 地址。 在 Azure 创建此映射后，发起的出站流的返回流量还可以抵达发起该流的专用 IP 地址。

对于每个出站连接，Azure 负载均衡器需要保持此映射一段时间。 根据 Azure 的多租户性质，对每个 VM 的每个出站流保持此映射可能会消耗大量的资源。 因此，需要根据 Azure 虚拟网络的配置设置一些限制。 或者，更准确地说，Azure VM 在给定的时间只能建立特定数量的出站连接。 达到这些限制时，VM 无法建立更多的出站连接。

但是，此行为是可配置的。 有关 SNAT 和 SNAT 端口耗尽的详细信息，请参阅[此文](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections)。

## <a name="measure-network-performance-on-azure"></a>测试 Azure 上的网络性能

本文所述的最大性能值与两个 VM 之间的网络延迟/往返时间 (RTT) 相关。 本部分提供有关如何测试延迟/RTT 以及如何测试 TCP 性能和 VM 网络性能的一些建议。 可以使用本部分所述的方法，来优化前面所述的 TCP/IP 和网络值并执行性能测试。 可将延迟、MTU、MSS 和窗口大小值插入前面提供的计算公式，并将理论最大值与测试期间观测到的实际值进行比较。

### <a name="measure-round-trip-time-and-packet-loss"></a>测量往返时间和丢包率

TCP 性能严重依赖于 RTT 和丢包率。 测量 RTT 和丢包率的最简单方法是使用 Windows 和 Linux 中提供的 PING 实用工具。 PING 的输出会显示源与目标之间的最小/最大/平均延迟。 它还会显示丢包率。 PING 默认使用 ICMP 协议。 可以使用 PsPing 来测试 TCP RTT。 有关详细信息，请参阅 [PsPing](https://docs.microsoft.com/sysinternals/downloads/psping)。

### <a name="measure-actual-throughput-of-a-tcp-connection"></a>测量 TCP 连接的实际吞吐量

NTttcp 是用于测试 Linux 或 Windows VM 的 TCP 性能的工具。 可以更改各项 TCP 设置，然后测试使用 NTttcp 所带来的优势。 有关详细信息，请参阅以下资源：

- [带宽/吞吐量测试 (NTttcp)](https://aka.ms/TestNetworkThroughput)

- [NTttcp 实用工具](https://gallery.technet.microsoft.com/NTttcp-Version-528-Now-f8b12769)

### <a name="measure-actual-bandwidth-of-a-virtual-machine"></a>测量虚拟机的实际带宽

可以使用名为 iPerf 的工具来测试不同的 VM 类型、加速网络等的性能。 在 Linux 和 Windows 上也可以使用 iPerf。 iPerf 可以使用 TCP 或 UDP 来测试总体网络吞吐量。 iPerf TCP 吞吐量测试结果受本文所述的因素（例如延迟和 RTT）的影响。 因此，如果只是测试最大吞吐量，UDP 可能会生成更好的结果。

有关详细信息，请参阅以下文章：

- [排查 Expressroute 网络性能问题](https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-network-performance)

- [如何验证虚拟网络的 VPN 吞吐量](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-validate-throughput-to-vnet)

### <a name="detect-inefficient-tcp-behaviors"></a>检测低效的 TCP 行为

在数据包捕获中，Azure 客户可能会看到带有 TCP 标志（SACK、DUP ACK、RETRANSMIT 和 FAST RETRANSMIT）的 TCP 数据包，这可能表示存在网络性能问题。 具体而言，这些数据包表示由于数据包丢失而导致网络效率低下。 但数据包丢失不一定是 Azure 性能问题造成的。 性能问题可能是应用程序问题、操作系统问题或者不直接与 Azure 平台相关的问题造成的。

另请注意，某些重新传输和重复 ACK 在网络中是正常的。 TCP 协议原本就很可靠。 数据包捕获中出现这些 TCP 数据包并不一定证明存在系统性的网络问题，除非这些数据包大量出现。

尽管如此，出现此类数据包表明 TCP 吞吐量并未实现其最大性能，本文的其他部分已讨论了原因。

## <a name="next-steps"></a>后续步骤

了解 Azure VM 的 TCP/IP 性能优化后，我们建议了解[规划虚拟网络](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm)时的其他考虑因素，或[详细了解如何连接和配置虚拟网络](https://docs.microsoft.com/azure/virtual-network/)。
