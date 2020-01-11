---
title: 验证 Microsoft Azure 虚拟网络的 VPN 吞吐量
description: 本文旨在帮助用户验证从本地资源到达 Azure 虚拟机的网络吞吐量。
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 05/29/2019
ms.author: radwiv
ms.reviewer: chadmat;genli
ms.openlocfilehash: a88e339e82484c2ec1cd2276f6218fa718b990f9
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/10/2020
ms.locfileid: "75860480"
---
# <a name="how-to-validate-vpn-throughput-to-a-virtual-network"></a>如何验证到达虚拟网络的 VPN 吞吐量

通过 VPN 网关连接，可以在 Azure 内的虚拟网络与本地 IT 基础结构之间创建安全的跨界连接。

本文将演示如何验证从本地资源到达 Azure 虚拟机 (VM) 的网络吞吐量。

> [!NOTE]
> 本文旨在帮助诊断并解决常见的问题。 如果使用以下信息无法解决问题，请[与支持人员联系](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。

## <a name="overview"></a>概述

VPN 网关连接涉及以下组件：

* 本地 VPN 设备（查看已[验证的 vpn 设备](vpn-gateway-about-vpn-devices.md#devicetable)的列表。）
* 公共 internet
* Azure VPN 网关
* Azure VM

下图显示的是通过 VPN 建立的从本地网络至 Azure 虚拟网络的逻辑连接。

![利用 VPN 建立的从客户网络至 MSFT 网络的逻辑连接](./media/vpn-gateway-validate-throughput-to-vnet/VPNPerf.png)

## <a name="calculate-the-maximum-expected-ingressegress"></a>计算最大的预期流入/流出量

1. 确定应用程序的基准吞吐量需求。
1. 确定 Azure VPN 网关的吞吐量限制。 有关帮助，请参阅[关于 VPN 网关](vpn-gateway-about-vpngateways.md#gwsku)的 "网关 sku" 部分。
1. 确定与 VM 大小相应的 [Azure VM 吞吐量指南](../virtual-machines/virtual-machines-windows-sizes.md)。
1. 确定 Internet 服务提供商 (ISP) 的带宽。
1. 通过获取 VM、VPN 网关或 ISP 的最小带宽来计算预期吞吐量;它以每秒兆位数（/）除以8（8）来度量。

如果计算出的吞吐量无法满足应用程序的基准吞吐量要求，则必须增加识别为瓶颈的资源的带宽。 若要调整 Azure VPN 网关的大小，请参阅[更改网关 SKU](vpn-gateway-about-vpn-gateway-settings.md#gwsku)。 若要调整虚拟机的大小，请参阅[调整 VM 的大小](../virtual-machines/virtual-machines-windows-resize-vm.md)。 如果未遇到预期的 Internet 带宽，则还可与 ISP 联系。

> [!NOTE]
> VPN 网关吞吐量是所有 Site-to-Site\VNET-to-VNET 或点到站点连接的聚合。

## <a name="validate-network-throughput-by-using-performance-tools"></a>使用性能工具验证网络吞吐量

此验证应在非高峰时段执行，因为测试期间的 VPN 隧道吞吐量饱和度无法给出准确的结果。

此测试将使用 iPerf 工具来实施，此工具在 Windows 和 Linux 上均可使用，并且有“客户端”和“服务器”两种模式。 它仅限适用于 Windows Vm 的3Gbps。

此工具不会对磁盘执行任何读/写操作。 它只会生成从一端至另一端的自生成 TCP 流量。 它根据用于测量客户端和服务器节点间可用带宽的试验生成统计信息。 在两个节点间进行测试时，一个节点充当服务器，另一个节点充当客户端。 完成此测试后，建议你反转节点的角色，以测试两个节点上的上传和下载吞吐量。

### <a name="download-iperf"></a>下载 iPerf

下载 [iPerf](https://iperf.fr/download/iperf_3.1/iperf-3.1.2-win64.zip)。 有关详情，请参阅 [iPerf 文档](https://iperf.fr/iperf-doc.php)。

 > [!NOTE]
 > 本文中讨论的第三方产品由独立于 Microsoft 的公司生产。 Microsoft 对这些产品的性能和可靠性不作任何明示或默示担保。

### <a name="run-iperf-iperf3exe"></a>运行 iPerf (iperf3.exe)

1. 启用允许流量的 NSG/ACL 规则（对于 Azure VM 上的公共 IP 地址测试）。

1. 在两个节点上，为端口 5001 启用防火墙例外。

   **Windows：** 以管理员身份运行以下命令：

   ```CMD
   netsh advfirewall firewall add rule name="Open Port 5001" dir=in action=allow protocol=TCP localport=5001
   ```

   若要在测试完成后删除规则，请运行此命令：

   ```CMD
   netsh advfirewall firewall delete rule name="Open Port 5001" protocol=TCP localport=5001
   ```

   **Azure Linux：** Azure Linux 映像具有可许可的防火墙。 如果有应用程序在侦听某个端口，则流量会被允许通过。 受保护的自定义映像可能需要显式打开端口。 常见的 Linux 操作系统层防火墙包括 `iptables`、`ufw` 或 `firewalld`。

1. 在服务器节点上，更改为从中提取 iperf3.exe 的目录。 然后，在服务器模式下运行 iPerf，并将其设置为侦听端口5001上的以下命令：

   ```CMD
   cd c:\iperf-3.1.2-win65

   iperf3.exe -s -p 5001
   ```

   > [!Note]
   > 端口5001可自定义，以在你的环境中考虑特定的防火墙限制。

1. 在客户端节点上，更改为从中提取 iperf 工具的目录，并运行以下命令：

   ```CMD
   iperf3.exe -c <IP of the iperf Server> -t 30 -p 5001 -P 32
   ```

   客户端将端口5001上的三十秒流量定向到服务器。 标志 "-P" 表示我们正在建立32同时连接到服务器节点。

   以下屏幕显示了本示例中的输出：

   ![输出](./media/vpn-gateway-validate-throughput-to-vnet/06theoutput.png)

1. （可选）若要保留测试结果，请运行以下命令：

   ```CMD
   iperf3.exe -c IPofTheServerToReach -t 30 -p 5001 -P 32  >> output.txt
   ```

1. 完成前面的步骤后，请执行与反向角色相同的步骤，以使服务器节点现在为客户端节点，反之亦然。

> [!Note]
> Iperf 不是唯一的工具。 [NTTTCP 是用于测试的替代解决方案](https://docs.microsoft.com/azure/virtual-network/virtual-network-bandwidth-testing)。

## <a name="test-vms-running-windows"></a>测试运行 Windows 的 Vm

### <a name="load-latteexe-onto-the-vms"></a>将 Latte 加载到 Vm

下载最新版本的[Latte](https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b)

请考虑将 Latte 放在单独的文件夹中，例如 `c:\tools`

### <a name="allow-latteexe-through-the-windows-firewall"></a>允许 Latte 通过 Windows 防火墙

在接收方上，在 Windows 防火墙上创建允许规则，以允许 Latte 流量到达。 最简单的方法是按名称允许整个 Latte 程序，而不是允许特定的 TCP 端口入站。

### <a name="allow-latteexe-through-the-windows-firewall-like-this"></a>允许 Latte 通过 Windows 防火墙（如下所示）

`netsh advfirewall firewall add rule program=<PATH>\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY`

例如，如果将 latte 复制到 "c:\tools" 文件夹中，则该命令

`netsh advfirewall firewall add rule program=c:\tools\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY`

### <a name="run-latency-tests"></a>运行延迟测试

在接收方上启动 latte （从 CMD 运行，而不是从 PowerShell 运行）：

`latte -a <Receiver IP address>:<port> -i <iterations>`

围绕65k 迭代的时间足够长，以返回有代表性的结果。

任何可用的端口号都是正确的。

如果 VM 具有10.0.0.4 的 IP 地址，它将如下所示

`latte -c -a 10.0.0.4:5005 -i 65100`

在发送方上启动 latte （从 CMD 运行，而不是从 PowerShell 运行）

`latte -c -a <Receiver IP address>:<port> -i <iterations>`

生成的命令与接收器上的命令相同，只是添加了 "-c" 以指示这是 "客户端" 或发送方

`latte -c -a 10.0.0.4:5005 -i 65100`

等待结果。 根据 Vm 的相隔程度，可能需要几分钟才能完成。 请考虑从较少的迭代开始，以测试是否成功，然后再运行较长的测试。

## <a name="test-vms-running-linux"></a>测试运行 Linux 的 Vm

使用[SockPerf](https://github.com/mellanox/sockperf)测试 vm。

### <a name="install-sockperf-on-the-vms"></a>在 Vm 上安装 SockPerf

在 Linux Vm 上（发送方和接收方），运行以下命令以在 Vm 上准备 SockPerf：

#### <a name="centos--rhel---install-git-and-other-helpful-tools"></a>CentOS/RHEL-安装 GIT 和其他有用的工具

`sudo yum install gcc -y -q`
`sudo yum install git -y -q`
`sudo yum install gcc-c++ -y`
`sudo yum install ncurses-devel -y`
`sudo yum install -y automake`

#### <a name="ubuntu---install-git-and-other-helpful-tools"></a>Ubuntu-安装 GIT 和其他有用的工具

`sudo apt-get install build-essential -y`
`sudo apt-get install git -y -q`
`sudo apt-get install -y autotools-dev`
`sudo apt-get install -y automake`

#### <a name="bash---all"></a>Bash-全部

从 bash 命令行（假定安装了 git）

`git clone https://github.com/mellanox/sockperf`
`cd sockperf/`
`./autogen.sh`
`./configure --prefix=`

做出的速度较慢，可能需要几分钟时间

`make`

简化安装

`sudo make install`

### <a name="run-sockperf-on-the-vms"></a>在 Vm 上运行 SockPerf

#### <a name="sample-commands-after-installation-serverreceiver---assumes-servers-ip-is-10004"></a>安装后的示例命令。 服务器/接收方-假定服务器的 IP 为10.0.0。4

`sudo sockperf sr --tcp -i 10.0.0.4 -p 12345 --full-rtt`

#### <a name="client---assumes-servers-ip-is-10004"></a>客户端-假定服务器的 IP 为10.0.0。4

`sockperf ping-pong -i 10.0.0.4 --tcp -m 1400 -t 101 -p 12345  --full-rtt`

> [!Note]
> 请确保 VM 和网关之间的吞吐量测试过程中没有中间跃点（例如虚拟设备）。
> 如果从上面的 iPERF/NTTTCP 测试产生不良结果（在整体吞吐量方面），请参阅以下文章，了解可能导致问题的根本原因的主要因素： https://docs.microsoft.com/azure/virtual-network/virtual-network-tcpip-performance-tuning

具体而言，在这些测试过程中，从客户端和服务器并行收集的数据包捕获跟踪（Wireshark/网络监视器）将有助于评估不良性能。 这些跟踪可能包括数据包丢失、高延迟、MTU 大小。 碎片、TCP 0 窗口、单序片段等。

## <a name="address-slow-file-copy-issues"></a>解决文件复制速度缓慢问题

即使使用上述步骤评估的总体吞吐量（iPERF/NTTTCP/等等）都很好，当使用 Windows 资源管理器或拖放到 RDP 会话时，可能会遇到文件断开缓慢的情况。 此问题通常是由以下的一个或两个因素造成的：

* 文件复制应用程序（如 Windows 资源管理器和 RDP）在复制文件时没有使用多个线程。 为了提高性能，请通过多线程文件复制应用程序（如 [Richcopy](https://technet.microsoft.com/magazine/2009.04.utilityspotlight.aspx)）使用 16 或 32 个线程来复制文件。 若要更改 Richcopy 中的文件复制线程数目，请单击“操作” > “复制选项” > “文件复制”。

   ![文件复制缓慢问题](./media/vpn-gateway-validate-throughput-to-vnet/Richcopy.png)<br>

   > [!Note]
   > 并非所有应用程序都能正常工作，并且并非所有应用程序/进程都利用了所有线程。 如果运行测试，则可以看到某些线程为空，不提供准确的吞吐量结果。
   > 若要检查您的应用程序文件传输性能，请使用多线程，方法是增加或减少线程数，以便找到应用程序或文件传输的最佳吞吐量。

* VM 磁盘读/写速度不够快。 有关详细信息，请参阅 [Azure 存储器故障排除](../storage/common/storage-e2e-troubleshooting.md)。

## <a name="on-premises-device-external-facing-interface"></a>本地设备上的对外接口

已提到，你希望 Azure 通过本地网络网关上的 VPN 访问本地范围的子网。 同时，在 Azure 中将 VNET 地址空间定义为本地设备。

* **基于路由的网关**：基于路由的 vpn 的策略或流量选择器配置为任意到任意（或通配符）。

* **基于策略的网关**：基于策略的 vpn 会根据本地网络和 Azure VNet 之间的地址前缀的组合，加密数据包并将数据包定向到 IPsec 隧道。 通常会在 VPN 配置中将策略（或流量选择器）定义为访问列表。

* **UsePolicyBasedTrafficSelector**连接：（将 "UsePolicyBasedTrafficSelectors" 连接到连接上的 $True 会将 Azure VPN 网关配置为连接到本地基于策略的 VPN 防火墙。 如果启用 PolicyBasedTrafficSelectors，则需要确保 VPN 设备具有与 Azure 虚拟网络前缀之间的本地网络（本地网关）前缀的所有组合定义的匹配流量选择器，而不是any 到 any。

不适当的配置可能导致在隧道中频繁断开连接、数据包丢弃、吞吐量损坏和延迟。

## <a name="check-latency"></a>检查延迟

您可以使用以下工具来检查延迟：

* WinMTR
* TCPTraceroute
* `ping` 和 `psping` （这些工具可以提供对 RTT 的良好估计，但不能在所有情况下使用它们。）

![检查延迟](./media/vpn-gateway-validate-throughput-to-vnet/08checkinglatency.png)

如果在输入 MS 网络主干之前，在任何跃点上注意到延迟时间较高，则可能需要通过 Internet 服务提供商进行进一步调查。

如果从 "msn.net" 内的跃点中发现了很大的异常延迟峰值，请联系 MS 支持以进行进一步调查。

## <a name="next-steps"></a>后续步骤

有关详细信息或帮助，请查看以下链接：

* [Microsoft 支持](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)
