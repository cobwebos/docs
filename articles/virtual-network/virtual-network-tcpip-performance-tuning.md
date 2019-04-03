---
title: Azure Vm 的 TCP/IP 性能优化 |Microsoft Docs
description: 了解各种常见 TCP/IP 性能优化技术，以及它们与 Azure Vm 的关系。
services: virtual-network
documentationcenter: na
author:
- rimayber
- dgoddard
- stegag
- steveesp
- minale
- btalb
- prachank
manager: paragk
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 3/30/2019
ms.author:
- rimayber
- dgoddard
- stegag
- steveesp
- minale
- btalb
- prachank
ms.openlocfilehash: c5d4f67e9c1e4e983133675c440b8c5b64183227
ms.sourcegitcommit: 04716e13cc2ab69da57d61819da6cd5508f8c422
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/02/2019
ms.locfileid: "58851712"
---
# <a name="tcpip-performance-tuning-for-azure-vms"></a>TCP/IP 的性能优化 Azure Vm

本文的目的是讨论常见 TCP/IP 性能优化技术以及其在 Microsoft Azure 上运行的虚拟机的注意事项。 基本了解的概念，并接着讨论如何优化至关重要。

## <a name="common-tcpip-tuning-techniques"></a>常见的 TCP/IP 优化技术

### <a name="mtu-fragmentation-and-large-send-offload-lso"></a>MTU、 碎片和大量发送卸载 (LSO)

#### <a name="explanation-of-mtu"></a>MTU 的说明

最大传输单元 (MTU) 是以字节为单位，可以通过网络接口发送指定的最大大小帧 （数据包）。 MTU 是可配置的设置和 MTU 使用 Azure Vm 的默认值和大多数网络设备上的默认设置全局范围内，为 1500 个字节。

#### <a name="explanation-of-fragmentation"></a>碎片的说明

当超过网络接口的 MTU 发送一个数据包时，会出现碎片。 TCP/IP 堆栈将分解为较小的部分 （片段） 符合接口 MTU 的数据包。 碎片发生在 IP 层，并独立于基础协议 （如 TCP)。 当通过网络接口的 MTU 为 1500年发送 2000年字节数据包时，然后它将分解为一个 1500年个字节的数据包和一个 500 个字节的数据包。

源和目标之间的路径中的网络设备可以选择删除超过 MTU 的数据包或数据包拆分成较小片段。

#### <a name="the-dont-fragment-df-bit-in-an-ip-packet"></a>IP 数据包中的"不要分段 (DF)"位

Don't Fragment 位是 IP 协议标头中的标志。 当 DF 位被设置时，它指示发送方和接收方之间的路径上的中间网络设备必须将数据包不分段。 原因有很多原因可能会设置此位 （请参阅下面的一个示例中的路径发现部分）。 如果网络设备收到不分段设置位，数据包，该数据包的大小超过设备接口 MTU，则标准行为是，设备能够丢弃该数据包并将"需要 ICMP 分段"数据包发送回原始的源数据包。

#### <a name="performance-implications-of-fragmentation"></a>性能产生的碎片影响

碎片可能会负面的性能影响。 性能影响的主要原因之一是碎片的 CPU/内存影响和重组的数据包。 当网络设备需要片段数据包时，它必须分配的 CPU/内存资源来执行碎片。 相同必须发生时数据包重新合并。 网络设备必须存储的所有片段，直到在接收到以便它可以将其重新整合成原始数据包。 碎片/重新汇编的这一过程也会导致延迟，因为程序集重新碎片/过程。

碎片的其他可能的负面性能含义是零碎的数据包可能到达顺序。 无序数据包可能会导致某些类型的网络设备，若要删除的无序数据包-需要重新传输的整个包。 用于删除片段的典型方案包括网络防火墙等安全设备或已耗尽时的网络设备的接收缓冲区。 当网络设备的接收缓冲区已用尽，网络设备正在尝试重新组装零碎的数据包，但是没有资源来存储和 reassume 数据包。

为碎片是通过 Internet 连接不同的网络的必要条件，碎片可能会被视为负操作，但支持。

#### <a name="benefits-and-consequences-of-modifying-the-mtu"></a>优势和后果的修改 MTU

作为常规语句，增加 MTU 可以创建更高效的网络。 传输的每个数据包已添加到原始数据包的其他标头信息。 多个数据包意味着开销，多个标头中，网络将因此效率较低。

例如，以太网标头大小为 14 个字节和 4 字节帧检验序列 (FCS) 以确保帧一致性。 如果发送一个 2000年个字节的数据包，然后在网络上添加 18 个字节的以太网开销。 如果该数据包分为碎片到 1500年个字节的数据包和 500 字节数据包，每个数据包都以太网标头-18 个字节或 36 个字节。 而单一 2000年个字节的数据包将只具有 18 个字节的以太网标头。

请务必注意，增加 MTU 本身将不一定是创建更高效的网络。 如果应用程序发送仅 500 字节数据包，然后 MTU 为 1500 个字节或 9000 字节是否也将存在同一标题开销。 为了使网络为多个有效，然后它还必须使用相对于 MTU 的更大的数据包大小。

#### <a name="azure-and-vm-mtu"></a>Azure 和 VM MTU

默认值为 Azure Vm 的 MTU 为 1500 个字节。 Azure 虚拟网络堆栈将尝试片段为 1400 个字节的数据包。 但是，Azure 虚拟网络堆栈将允许数据包达 2006 个字节时，"不分段"位设置 IP 标头中。

请务必注意此碎片并不意味着 Azure 虚拟网络堆栈是本质上是效率低下，因为它片段为 1400 个字节的数据包，而 Vm 具有 MTU 为 1500年。 但实际上是大量的网络数据包远小于 1400 个字节或 1500 个字节。

#### <a name="azure-and-fragmentation"></a>Azure 和碎片

Azure 的虚拟网络堆栈现在已配置为删除"序片段"-这意味着不在其原始的碎片化顺序到达的零碎的数据包。 主要是由于网络安全漏洞，在名为 FragmentStack 2018 年 11 月宣布会丢弃这些数据包。

FragmentSmack 是 Linux 内核处理重组零碎的 IPv4 和 IPv6 数据包的方式中的一个缺陷。 远程攻击者可以使用此触发器昂贵片段重组操作，这会增加的 CPU 和拒绝服务攻击导致目标系统上的缺陷。

#### <a name="tune-the-mtu"></a>优化 MTU

Azure Vm 就像任何其他操作系统支持可配置 MTU。 但是，配置 MTU 时，应考虑在 Azure 中，会发生和上面详细说明的碎片。

Azure 不鼓励客户增加其 VM MTU。 此讨论用于 Azure 如何实现 MTU 和执行碎片今天将详细解释。

> [!IMPORTANT]
>增加 MTU 具有不会显示以提高性能，并且可能具有对应用程序性能的负面影响。
>
>

#### <a name="large-send-offload-lso"></a>大量发送卸载 (LSO)

大量发送卸载 (LSO) 可以通过卸载到以太网适配器的数据包分段提高网络性能。 与启用 LSO，TCP/IP 堆栈将创建较大的 TCP 数据包，然后发送到以太网适配器对于转发它之前的分段。 LSO 的好处是它可以免费从细分到符合 MTU 和该将处理卸载到硬件中执行其中的以太网接口的数据包大小的数据包的 CPU。 中可以找到更多信息优点的 LSO [Microsoft 网络适配器文档中的性能](https://docs.microsoft.com/windows-hardware/drivers/network/performance-in-network-adapters#supporting-large-send-offload-lso)。

启用 LSO 后，Azure 客户可能会看到大帧大小，当执行数据包捕获。 这些大帧大小可能会导致某些客户认为该碎片或当它不是使用 MTU 巨型。 此外，使用以太网适配器可以播发到 TCP/IP 堆栈可查看大 MSS 为了创建更大的 TCP 数据包。 此非分段的整个帧然后转发到的以太网适配器，并且会在 VM 上执行数据包捕获中可见。 但是，数据包将分解为多个较小的帧根据以太网适配器的 MTU 以太网适配器。

### <a name="tcpmss-window-scaling-and-pmtud"></a>TCP/MSS 窗口缩放和 PMTUD

#### <a name="explanation-of-tcp-mss"></a>TCP MSS 的说明

TCP 最大段大小 (MSS) 是为了避免的 TCP 数据包的碎片用于设置最大 TCP 段大小的设置。 操作系统将通常为 MSS 设置 MSS = MTU 的 IP 和 TCP 标头大小 （20 个字节或总 40 个字节）。 因此与 MTU 为 1500年接口将有 1460年 MSS。 MSS，但是，进行配置。

源和目标之间建立 TCP 会话时，将 TCP 三次握手中同意此设置。 双方都发送 MSS 值和 TCP 连接所使用的两个较小。

中间网络设备，如 VPN 网关，包括 Azure VPN 网关，可以调整独立于源和目标 MTU 以确保最佳的网络性能。 因此，应注意 MTU 的源和目标本身不是实际 MSS 值中的唯一因素。

#### <a name="explanation-of-path-mtu-discovery-pmtud"></a>路径 MTU 发现 (PMTUD) 的说明

虽然 MSS 进行协商，则可能不表示可用作源和目标之间的路径中的其他网络设备实际 MSS 可能具有低于 MTU 值的源和目标。 在这种情况下，设备的 MTU 小于数据包将丢弃该数据包，并发送回包含其 MTU 的 Internet 控制消息协议 (ICMP) 需要分段 （类型 3，代码 4） 消息。 此 ICMP 消息允许适当地降低其路径 MTU 源主机。 过程被称为路径 MTU 发现。

PMTUD 的过程是本质上是效率低下，网络性能的影响。 当超过网络路径 MTU 发送数据包时，然后必须使用较低 MSS 转发这些数据包。 如果发件人没有收到 ICMP 碎片所需的数据包，也许是因为网络防火墙中的路径 （通常称为 PMTUD 黑洞），然后发件人不知道它需要降低 mss 交互，并将持续重新传输该数据包。 出于此原因，我们不建议增加 Azure VM MTU。

#### <a name="vpn-considerations-with-mtu"></a>MTU VPN 注意事项

使用执行 （如 IPSec Vpn) 封装的虚拟机的客户可能会对数据包大小和 MTU 的其他影响。 Vpn 添加其他标头将添加到原始数据包，从而增加数据包大小和需要较小 MSS。

Azure 目前的建议是设置 TCP MSS 钳位到 1350 字节和隧道接口 MTU 1400。 可在找到更多信息[VPN 设备和 IPSec/IKE 参数页](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices)。

### <a name="latency-round-trip-time-and-tcp-window-scaling"></a>延迟、 往返时间和 TCP 窗口缩放

#### <a name="latency-and-round-trip-time"></a>延迟和往返时间

网络延迟受光速纤程光纤网络上。 实际情况是，TCP 的网络吞吐量也是有效地控制 （实际最大值） 由于往返时间 (RTT) 的两个网络设备之间。

| | | | |
|-|-|-|-|
|路由|距离|单向时间|往返时间 (RTT)|
|纽约飞往旧金山|4,148 公里|21 ms|42 ms|
|伦敦到纽约|5,585 公里|28 ms|56 ms|
|纽约到悉尼|15,993 公里|80 毫秒|160 ms|

此表显示了两个位置之间的直线距离但是，在网络中，距离长于通常的直线距离。 一个简单的公式来计算最小的 RTT 为受光速是： 最小 RTT = 2 * (距离公里 / 传播的速度)。

标准值为 200 可用于传播的速度-值为 1 毫秒传输计量 light 中的距离。

在示例中纽约飞往旧金山，它是 4,148 km 直线距离。 最小 RTT = 2 * (4,148 / 20)。 公式的输出将以毫秒为单位。

由于两个位置之间的物理距离是固定的现实，如果是必需的最大网络性能，最合理的选项将选择与它们之间的最小距离的目标。 其次，在虚拟网络中的设计决策可以进行优化的流量的路径和减少延迟。 下面的网络设计注意事项部分中说明了这些虚拟网络注意事项。

#### <a name="latency-and-round-trip-time-effects-on-tcp"></a>对 TCP 的延迟和往返时间影响

往返行程时间 (RTT) 有直接影响最大 TCP 吞吐量。 TCP 协议有一个窗口大小的概念。 窗口大小是流量的发件人必须接收确认从接收方之前，可以通过 TCP 连接发送的最长。 如果 TCP MSS 设置为 1460，且 TCP 窗口大小设置为 65535 发件人可发送 45 数据包，它必须从接收方接收确认之前。 如果未收到确认发件人会重新传输。 在此示例中，TCP 窗口大小 / TCP MSS = 发送的数据包。 或 65535 / 舍入 1460年达 45。

此"等待确认"状态下，作为一种机制来创建可靠的传递的数据，是哪些因素会影响 TCP 吞吐量的 RTT 有效地导致。 发件人确认将等待的时间越长，时间越长它必须还在发送之前等待更多的数据。

用于计算单个 TCP 连接的最大吞吐量的公式如下所示：窗口大小 / (RTT 延迟 （毫秒） / 1000年) = 最大字节数/秒。 下表以兆字节表示格式化以增加可读性，并显示了最大兆字节 / 每秒吞吐量的单个 TCP 连接。

| | | | |
|-|-|-|-|
|TCP 窗口大小 （字节）|RTT 延迟<br/>以毫秒为单位|最大值<br/>每秒吞吐量的兆字节为单位|最大值<br/> 兆位 / 秒吞吐量|
|65535|1|65.54|524.29|
|65535|30|2.18|17.48|
|65535|60|1.09|8.74|
|65535|90|.73|5.83|
|65535|120|.55|4.37|

如果没有任何数据包丢失，它会发件人一段重新传输已发送的数据时减少 TCP 连接的最大吞吐量。

#### <a name="explanation-of-tcp-window-scaling"></a>TCP 窗口缩放的说明

TCP 窗口缩放是动态地增加允许更多数据需要提供确认之前要发送的 TCP 窗口大小的概念。 在上一示例中之前确认所需,，则会发送 45 数据包。 数的增加确认之前发送的数据包，然后 TCP 最大吞吐量也就提高了减少的发件人正在等待确认的次数。

TCP 吞吐量下面一个简单的表所示：

| | | | |
|-|-|-|-|
|TCP 窗口大小<br/>以字节为单位|RTT 延迟 （毫秒）|最大值<br/>每秒吞吐量的兆字节为单位|最大值<br/> 兆位 / 秒吞吐量|
|65535|30|2.18|17.48|
|131,070|30|4.37|34.95|
|262,140|30|8.74|69.91|
|524,280|30|17.48|139.81|

但是，TCP 窗口大小的 TCP 标头值是仅 2 个字节长，这意味着接收窗口的最大值为 65535。 为了增加最大窗口大小，引入了 TCP 窗口缩放比例。

缩放比例也是可以在操作系统中配置的设置。 用于计算使用缩放比例的 TCP 窗口大小的公式如下所示：TCP 窗口大小 = TCP 窗口大小 （字节） \* (2 ^ 比例因子)。 如果窗口缩放比例为 3 和窗口大小为 65535，计算如下所示：65535 \* (2 ^3) = 262,140 个字节。 小数位数为 14 个导致 TCP 窗口大小为 14 （最大偏移量允许），则 TCP 窗口大小将 1,073,725,440 字节 （8.5 千兆字节）。

#### <a name="support-for-tcp-window-scaling"></a>TCP 窗口缩放的支持

Windows 具有能够设置不同的缩放比例上每个连接类型-有多个类的连接 （数据中心、 internet，等）。 您可以看到使用 Get NetTCPConnection powershell 命令的窗口缩放连接分类。

```powershell
Get-NetTCPConnection
```

您可以看到每个类使用 Get NetTCPSetting powershell 命令的值。

```powershell
Get-NetTCPSetting
```

初始 TCP 窗口大小和 TCP 缩放系数可以通过设置 NetTCPSetting powershell 命令设置在 Windows 中。 详细信息可在[集 NetTCPSetting 页](https://docs.microsoft.com/powershell/module/nettcpip/set-nettcpsetting?view=win10-ps)

```powershell
Set-NetTCPSetting
```

AutoTuningLevel 的有效 TCP 设置如下所示。

| | | | |
|-|-|-|-|
|AutoTuningLevel|比例因子|缩放乘数|为公式<br/>计算最大窗口大小|
|已禁用|无|无|窗口大小|
|受限|4|2^4|窗口大小 * (2 ^4)|
|严格限制|2|2^2|窗口大小 * (2 ^2)|
|一般|8|2^8|窗口大小 * (2 ^8)|
|试验|14|2^14|窗口大小 * (2 ^14)|

虽然这些设置最有可能会影响 TCP 性能，但应注意通过 Internet、 Azure 中，控件范围之外的许多其他因素也会影响 TCP 性能。

#### <a name="increase-mtu-size"></a>增加 MTU 大小

逻辑问题是"可增加 MTU increase TCP 性能因为较大 MTU 意味着更大 MSS"？ 简单的答案 – 可能不是。 如所述，有优点也有缺点数据包大小的适用范围远远超出只需 TCP 流量。 如上所述，最重要的因素会影响 TCP 吞吐量性能是 TCP 窗口大小、 数据包丢失和 RTT。

> [!IMPORTANT]
> Azure 不建议 Azure 客户，修改虚拟机上的默认 MTU 值。
>
>

### <a name="accelerated-networking-and-receive-side-scaling"></a>加速网络和接收方缩放

#### <a name="accelerated-networking"></a>加速网络

虚拟机网络功能就是长久以来 CPU 密集型 VM 来宾和虚拟机监控程序/主机上。 通过在主机上传输的每个数据包在软件中处理由主机 CPU-包括所有虚拟网络封装/de-capsulation。 因此，更多流量的经历主机，然后更高的 CPU 负载。 并且，如果主机 CPU 正忙于执行其他操作，然后将还影响网络吞吐量和延迟。 此问题已得到解决通过加速网络。

加速的网络提供了通过 Azure 的内部可编程的硬件和诸如 SR-IOV 之类的技术的一致超低的网络延迟。 通过移动 Azure 的软件定义网络堆栈的许多关闭在 Cpu 和到基于 FPGA 的 SmartNICs 计算周期回收由最终用户应用程序，将在 VM 上的负载更小、 中延迟减少抖动和不一致。 换而言之，性能可能要更具确定性。

加速的网络通过使来宾 VM 绕过主机并建立直接与主机的 SmartNIC 数据路径来实现性能改进。 加速网络的优势是：

- **降低延迟较高每秒数据包数 (pps) /**:从数据路径中去除虚拟交换机可以消除数据包在主机中进行策略处理所花费的时间，同时增大了 VM 中可处理的数据包数。

- **减少抖动**:虚拟交换机处理取决于需要应用的策略数量，以及正在执行处理的 CPU 工作负荷。 将策略实施卸载到硬件消除了这种可变性，因为可以将数据包直接传送到 VM，省去了主机与 VM 之间的通信，以及所有的软件中断和上下文切换。

- **减少 CPU 使用率**:绕过主机中的虚拟交换机可以减少用于处理流量的 CPU 资源。

必须在每个 VM 的基础上显式启用加速的网络。 用于启用加速网络的 VM 上的说明[创建 Linux 虚拟机的加速网络网页](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli)。

#### <a name="receive-side-scaling-rss"></a>接收方缩放 (RSS)

接收方缩放是一种网络驱动程序技术的更有效地将接收网络流量分发的分发在多处理器系统中接收处理跨多个 Cpu。 简单来说，RSS 允许系统处理更长的已接收的通信，因为它而不是只是一个使用所有可用的 Cpu。 RSS 的更多技术讨论，请参阅[接收方缩放页简介](https://docs.microsoft.com/windows-hardware/drivers/network/introduction-to-receive-side-scaling)。

RSS 是所需的 VM 上启用了加速网络时获得最大的性能。 可以也有好处中没有已启用加速的网络的 Vm 上使用 RSS。 概述了如何确定是否启用了 RSS 并启用它的配置，请参阅[的 Azure 虚拟机页上优化网络吞吐量](http://aka.ms/FastVM)。

### <a name="tcp-time-wait-and-time-wait-assassination"></a>TCP 时间等待和时间等待 Assassination

影响网络和应用程序性能的另一个常见问题是 TCP 时间等待设置。 忙碌的 Vm 的打开和关闭许多套接字，作为客户端或 TCP，正常操作过程的服务器 （源 IP:Source 端口 + 目标 IP:Destination 端口） 上给定的套接字可以会在结束时间等待状态的大量的时间。 此"等待时间"状态下，用于允许在之前关闭套接字上提供任何其他数据。 因此，TCP/IP 堆栈通常以无提示方式删除客户端 TCP SYN 数据包通过防止重复使用的套接字。

此时间量套接字处于等待状态进行配置，但是可以介于 30 秒到 240 秒的时间。 套接字是有限的资源，并且可配置的可以在任何给定时间使用的插槽数 （数量通常存在于大约 30,000 潜在的套接字）。 如果已用尽此数字，或客户端和服务器具有不匹配的时间等待设置，并且 VM 尝试重复使用套接字时等待状态，则以无提示方式删除 TCP SYN 数据包时，新连接将失败。

通常配置 TCP/IP 堆栈的操作系统内的出站套接字，以及 TCP 时间等待设置和套接字重复使用的端口范围中的值。 更改这些数字可以潜在地提高可伸缩性，但具体取决于方案，可能会引入互操作性问题，应谨慎更改。

一种功能称为时间等待 Assassination 引入是为了解决此问题缩放限制。 时间等待 Assassination 允许新连接的 IP 数据包中的序列号超过序列数与以前的连接的最后一个数据包时等某些情况下重复使用套接字。 在这种情况下，操作系统将允许建立新连接 （接受新的 SYN ACK） 和强制关闭已在时间中等待状态的上一个连接。 此功能目前支持在 Azure 中 Windows Vm 上，在其他 Vm 的支持应调查与各自的 OS 供应商的 Azure 客户。

有关配置 TCP 时间等待设置和源端口范围的文档位于[提高网络性能页可以修改的设置](https://docs.microsoft.com/biztalk/technical-guides/settings-that-can-be-modified-to-improve-network-performance)。

## <a name="virtual-network-factors-that-can-affect-performance"></a>虚拟网络因素可能会影响性能

### <a name="vm-maximum-outbound-throughput"></a>VM 的最大出站吞吐量

Azure 提供各种 VM 大小和类型，每一种包含的性能各不相同。 一个此类性能功能是网络吞吐量 （或带宽），以兆位 / 秒 (Mbps)。 由于虚拟机托管在共享硬件上，因此网络容量必须在共享同一硬件的虚拟机中公平地共享。 在分配时，较大的虚拟机相对于较小的虚拟机会获得相对较多的带宽。

分配给每个虚拟机的网络带宽按虚拟机的传出（出站）流量计算。 从虚拟机流出的所有网络流量均计入分配限制，不管流向哪个目标。 例如，如果虚拟机有 1,000 Mbps 限制，该限制将应用是否将出站流量发送到相同的虚拟网络中或 Azure 外部的另一个虚拟机。
传入流量不直接计算，或者说不直接受到限制。 但是，其他因素（例如 CPU 和存储限制）可能会影响虚拟机处理传入数据的能力。

加速的网络是一项功能旨在改进网络性能，包括延迟、 吞吐量和 CPU 使用率。 虽然加速网络可以提高虚拟机的吞吐量，它可以实现虚拟机的最多分配带宽的限制。

Azure 虚拟机必须有一个（但也可能有多个）连接的网络接口。 分配给某个虚拟机的带宽是流经所有网络接口（已连接到该虚拟机）的所有出站流量的总和。 换言之，分配的带宽是针对每个虚拟机的，不管为该虚拟机连接了多少网络接口。
 
预期的出站吞吐量以及每个 VM 大小支持的网络接口数是此处详细介绍。 若要查看的最大吞吐量，选择一个类型，例如通用，然后选择在生成页上，例如 Dv2 系列大小系列。 每个序列中有一个表包含网络规格中的最后一列标题为最大 nic 数 / 预期网络性能 (Mbps)。

吞吐量限制适用于虚拟机。 吞吐量不受以下因素影响：

- **网络接口数**:带宽限制都是累积的所有出站流量从虚拟机。

- **加速网络**:尽管该功能可以有助于流量达到已发布的限制，它不会更改此限制。

- **流量目标**:所有目标都计入出站限制。

- **协议**：所有协议的所有出站流量将计入此限制。

一个[每个 VM 类型的最大带宽的表可以找到访问此页](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)，并单击相应的 VM 类型。 在每个类型页中，表将显示的最大 Nic 和最大预期的网络带宽。

有关 VM 网络带宽的详细信息可从[虚拟机的网络带宽](http://aka.ms/AzureBandwidth)。

### <a name="internet-performance-considerations"></a>Internet 性能注意事项

如本文所述，在 Internet 上和 Azure 的控制范围之外的因素会影响网络性能。 这些因素包括：

- **延迟**:两个目标之间的往返时间可能会受到中间网络流量不会采用"短距离"路径可能并不理想的对等互连路径上的问题

- **数据包丢失**:数据包丢失可能引起网络拥塞、 物理路径问题和下执行网络设备

- **MTU 大小/碎片**:在路径上的碎片会导致在数据到达或数据包到达顺序，这可能会影响数据包的传递延迟

跟踪路由是一个不错的工具来测量每个源和目标设备之间的网络路径上的网络性能特征 （例如数据包丢失和延迟）。

### <a name="network-design-considerations"></a>网络设计注意事项

以上考虑因素，以及虚拟网络性能产生影响的虚拟网络拓扑。 例如，中心辐射型设计 backhauls 全局到单个中心虚拟网络的流量将引入网络延迟，并因此影响整个网络的性能。 同样，网络设备的网络流量将通过传递数还会影响总体延迟。 例如，在中心辐射设计中，如果流量通过辐射网络虚拟设备和中心虚拟设备之前传输到 Internet，然后延迟可能会造成的网络虚拟设备。

### <a name="azure-regions-virtual-networks-and-latency"></a>Azure 区域、 虚拟网络和延迟

Azure 区域由一个常用地理区域中存在的多个数据中心组成。 这些数据中心可能不在物理上是彼此，并在某些情况下可能要分隔多达 10 千米以内。 虚拟网络是基于 Azure 的物理数据中心网络的逻辑叠加和虚拟网络并不表示任何特定的网络拓扑中的数据中心内。 例如，VM A 和 B VM 位于同一虚拟网络和子网，但可能位于不同机架、 行或甚至数据中心。 它们可能会分隔英尺光纤电缆或公里光纤电缆。 这一实际情况可能会造成变量不同 Vm 之间的延迟 （几毫秒为单位差异）。

此地理位置，因此两个 Vm 之间的延迟会影响可用性集与可用性区域的配置，但在区域中的数据中心之间的距离是特定于区域和主要受的影响数据中心区域中的拓扑。

### <a name="source-nat-port-exhaustion"></a>源 NAT 端口耗尽的情况

在 Azure 中的部署可以在公共 Internet 和/或公共 IP 空间的 Azure 外部的终结点进行通信。 当实例启动时此出站连接时，Azure 动态将专用 IP 地址映射到公共 IP 地址。 创建此映射后，此出站发起流的返回流量还可以抵达发起流的专用 IP 地址。

对于每个出站连接，Azure 负载均衡器必须维护某个时间段内此映射。 Azure 的多租户特性，维护此映射的每个 VM 每个出站流可以是需要消耗大量资源。 因此，有限制的设置和基于 Azure 虚拟网络的配置。 或更准确地说-所述 Azure VM 仅可一定数量的出站连接在给定的时间。 如果这些限制已经耗尽，然后 Azure VM 将无法进行任何进一步的出站连接。

此行为是，但是，可配置。 有关详细信息 [SNAT 和 SNAT 端口消耗]，请参阅[这篇文章](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections)。

## <a name="measure-network-performance-on-azure"></a>度量值在 Azure 上的网络性能

与网络延迟相关的这篇文章中的性能最大配置数量 / 往返时间 (RTT) 之间的两个 Vm。 本部分提供有关如何测试延迟/RTT，以及 TCP 性能和 VM 的网络性能的一些建议。 可以优化上文所述的 TCP/IP 和网络值，使用下面描述的技术的性能测试。 延迟、 MTU，MSS 和窗口大小的值可在上面列出的计算和理论最大值可以为在测试期间观察到的实际值进行比较。

### <a name="measure-round-trip-time-and-packet-loss"></a>度量值往返时间和数据包丢失

TCP 性能严重依赖 RTT 和数据包丢失。 测量 RTT 和数据包丢失的最简单方法使用 ping 实用程序，可在 Windows 和 Linux。 Ping 的输出将显示源和目标，以及数据包丢失之间的最小值/最大/平均延迟。 默认情况下，Ping 使用 ICMP 协议。 若要测试 TCP RTT，然后 PsPing 可用。 PsPing 的详细信息位于[此链接](https://docs.microsoft.com/sysinternals/downloads/psping)。

### <a name="measure-actual-throughput-of-a-tcp-connection"></a>度量值的 TCP 连接的实际吞吐量

NTttcp 是一种工具，用于测试的 Linux 或 Windows VM 的 TCP 性能。 可以调整 TCP 的各种设置和使用 NTttcp 测试的好处。 可以在下面的链接找到有关 NTttcp 的详细信息。

- [带宽/吞吐量测试 (NTttcp)](https://aka.ms/TestNetworkThroughput)

- [NTttcp 实用程序](https://gallery.technet.microsoft.com/NTttcp-Version-528-Now-f8b12769)

### <a name="measure-actual-bandwidth-of-a-virtual-machine"></a>度量值实际带宽的虚拟机

性能测试的不同 VM 类型、 加速网络，和等等，可以使用名为 Iperf，也可在 Linux 和 Windows 上的工具进行测试。 Iperf 可用于 TCP 或 UDP 测试总体网络吞吐量。 使用 Iperf TCP 吞吐量测试时将受此文章 （延迟、 RTT 和等等） 中所讨论的因素。 因此，UDP 可能会产生更好的结果，只需测试的最大吞吐量。

下面，可以找到更多信息：

- [Expressroute 网络性能故障排除](https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-network-performance)

- [如何验证虚拟网络的 VPN 吞吐量](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-validate-throughput-to-vnet)

### <a name="detect-inefficient-tcp-behaviors"></a>检测低效 TCP 行为

Azure 客户可能会看到 TCP 标志 （SACK、 重复确认，重新传输和快速重新传输） 的 TCP 数据包中可能表示网络性能问题的数据包捕获。 这些数据包明确指出由于数据包丢失率网络效率低下。 但是，数据包丢失不一定是由于 Azure 性能问题。 性能问题可能是应用程序、 操作系统或其他相关的问题可能不是直接向 Azure 平台的结果。 务必还请注意，某些重新传输数据或重复用于确认网络上的是正常 – TCP 协议生成可靠。 并在数据包捕获这些 TCP 数据包的证据不一定表示系统的网络问题除非它们是过多。

但是，它应要明确清晰地这些数据包类型是 TCP 吞吐量不能实现其最大的性能 – 其他各节所述的原因的迹象。
