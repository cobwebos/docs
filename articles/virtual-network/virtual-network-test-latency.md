---
title: 测试 Azure 虚拟网络中的 Azure 虚拟机网络延迟 |Microsoft Docs
description: 了解如何在虚拟网络上的 Azure 虚拟机之间测试网络延迟
services: virtual-network
documentationcenter: na
author: steveesp
manager: Marina Lipshteyn
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/29/2019
ms.author: steveesp
ms.openlocfilehash: 760a181b4459db28d3a6eee5022cc0f984c4bee0
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73588273"
---
# <a name="testing-network-latency"></a>测试网络延迟

使用为任务设计的工具测量网络延迟将提供最准确的结果。 可通过 SockPerf （适用于 Linux）和 Latte （适用于 Windows）等公开使用的工具来隔离和度量网络延迟，同时排除其他类型的延迟，如应用程序延迟。 这些工具重点介绍了影响应用程序性能的网络流量种类，即 TCP 和 UDP。 其他常见的连接工具（如 ping）有时可能用于测量延迟，但这些结果可能不代表实际工作负荷中使用的网络流量。 这是因为，这些工具中的大多数都使用 ICMP 协议，该协议的处理方式不同于应用程序流量，结果可能不适用于使用 TCP 和 UDP 的工作负荷。 对于大多数应用程序使用的协议的精确网络延迟测试，SockPerf （适用于 Linux）和 Latte （适用于 Windows）将产生最相关的结果。 本文档介绍这两种工具。

## <a name="overview"></a>概述

使用两个 Vm （一个作为发送方，另一个作为接收方），创建双向通信通道以双向发送和接收数据包，以测量往返时间（RTT）。

这些步骤可用于测量两个虚拟机（Vm）之间或者两个物理计算机之间的网络延迟。 延迟度量可用于以下情况：

- 建立部署的 Vm 之间网络延迟的基准
- 比较在对的相关更改后网络延迟更改的影响：
  - 操作系统或网络堆栈软件，包括配置更改
  - VM 部署方法，例如部署到区域或 PPG
  - VM 属性，如加速网络或大小更改
  - 虚拟网络，例如路由或筛选更改

### <a name="tools-for-testing"></a>用于测试的工具
为了衡量延迟，我们有两个不同的选项，一个用于基于 Windows 的系统，另一个用于基于 Linux 的系统

对于 Windows： latte （Windows） [https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b](https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b)

对于 Linux： SockPerf （Linux） [https://github.com/mellanox/sockperf](https://github.com/mellanox/sockperf)

使用这些工具可确保仅对 TCP 或 UDP 有效负载传递时间进行度量，而不是应用程序不使用的其他数据包类型，也不会影响它们的性能。

### <a name="tips-for-an-optimal-vm-configuration"></a>最佳 VM 配置的提示：

- 使用最新版本的 Windows 或 Linux
- 启用加速网络以获得最佳结果
- 用[Azure 邻近性组](https://docs.microsoft.com/azure/virtual-machines/linux/co-location)部署 vm
- 更大的 Vm 通常比小型 Vm 更好

### <a name="tips-for-analysis"></a>分析提示

- 在部署、配置和优化完成后尽早建立基线
- 始终使用受控的更改将新结果与基线或其他测试进行比较
- 在观察或计划更改时重复测试


## <a name="testing-vms-running-windows"></a>测试运行 Windows 的 Vm

## <a name="get-latteexe-onto-the-vms"></a>将 Latte 获取到 Vm

下载最新版本： [https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b](https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b)

请考虑将 Latte 放在单独的文件夹中，如 c:\tools

## <a name="allow-latteexe-through-the-windows-firewall"></a>允许 Latte 通过 Windows 防火墙

在接收方上，在 Windows 防火墙上创建允许规则，以允许 Latte 流量到达。 最简单的方法是按名称允许整个 Latte 程序，而不是允许特定的 TCP 端口入站。

允许 Latte 通过 Windows 防火墙，如下所示：

netsh advfirewall firewall add rule program =\<路径\>\\latte name =&quot;Latte&quot; protocol = any dir = in action = allow enable = yes profile = ANY


例如，如果你将 latte 复制到 &quot;c：\\tools&quot; 文件夹，则该命令如下所示：

```cmd
netsh advfirewall firewall add rule program=c:\tools\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY
```

## <a name="running-latency-tests"></a>运行延迟测试

在接收方上启动 latte （从 CMD 运行，而不是从 PowerShell 运行）：

latte-&lt;接收方 IP 地址&gt;：&lt;端口&gt;-i &lt;迭代&gt;

围绕65k 迭代的时间足够长，以返回有代表性的结果。

任何可用的端口号都是正确的。

如果 VM 具有10.0.0.4 的 IP 地址，它将如下所示：

```cmd
latte -a 10.0.0.4:5005 -i 65100
```

在发送方上启动 latte （从 CMD 运行，而不是从 PowerShell 运行）：

latte-a \<接收方 IP 地址\>：\<端口\>-i \<迭代\>


生成的命令与接收器上的命令相同，只是添加 &quot;-c&quot;，以指示这是 &quot;客户端&quot; 或发送方：

```cmd
latte -c -a 10.0.0.4:5005 -i 65100
```

等待结果。 根据 Vm 的相隔程度，可能需要几分钟才能完成。 请考虑从较少的迭代开始，以测试是否成功，然后再运行较长的测试。



## <a name="testing-vms-running-linux"></a>测试运行 Linux 的 Vm

请使用 SockPerf。 它可从[https://github.com/mellanox/sockperf](https://github.com/mellanox/sockperf)

### <a name="install-sockperf-on-the-vms"></a>在 Vm 上安装 SockPerf

在 Linux Vm 上（发送方和接收方），运行以下命令以在 Vm 上准备 SockPerf。 为主要发行版提供了命令。

#### <a name="for-rhel--centos-follow-these-steps"></a>对于 RHEL/CentOS，请执行以下步骤：
```bash
#CentOS / RHEL - Install GIT and other helpful tools
    sudo yum install gcc -y -q
    sudo yum install git -y -q
    sudo yum install gcc-c++ -y
    sudo yum install ncurses-devel -y
    sudo yum install -y automake
    sudo yum install -y autoconf
```

#### <a name="for-ubuntu-follow-these-steps"></a>对于 Ubuntu，请遵循以下步骤：
```bash
#Ubuntu - Install GIT and other helpful tools
    sudo apt-get install build-essential -y
    sudo apt-get install git -y -q
    sudo apt-get install -y autotools-dev
    sudo apt-get install -y automake
    sudo apt-get install -y autoconf
```

#### <a name="for-all-distros-copy-compile-and-install-sockperf-according-to-the-following-steps"></a>对于所有发行版，请按照以下步骤进行复制、编译和安装 SockPerf：
```bash
#Bash - all distros

#From bash command line (assumes git is installed)
git clone https://github.com/mellanox/sockperf
cd sockperf/
./autogen.sh
./configure --prefix=

#make is slower, may take several minutes
make

#make install is fast
sudo make install
```

### <a name="run-sockperf-on-the-vms"></a>在 Vm 上运行 SockPerf

SockPerf 安装完成后，Vm 就可以运行延迟测试了。 

首先，在接收方上启动 SockPerf。

任何可用的端口号都是正确的。 在此示例中，我们使用端口12345：
```bash
#Server/Receiver - assumes server's IP is 10.0.0.4:
sudo sockperf sr --tcp -i 10.0.0.4 -p 12345
```
既然服务器正在侦听，客户端就可以开始将数据包发送到正在侦听的端口（在这种情况下为12345）上的服务器。

大约100秒足够长，可以返回代表结果，如以下示例中所示：
```bash
#Client/Sender - assumes server's IP is 10.0.0.4:
sockperf ping-pong -i 10.0.0.4 --tcp -m 350 -t 101 -p 12345  --full-rtt
```

等待结果。 根据 Vm 的距离，迭代次数会有所不同。 在运行较长的测试之前，请考虑从大约5秒的较短测试开始测试是否成功。

此 SockPerf 示例使用350字节消息大小，因为它是典型的平均大小数据包。 可以调整更高或较低的消息大小，以获得更准确地表示将在 Vm 上运行的工作负荷的结果。


## <a name="next-steps"></a>后续步骤
* 利用[Azure 邻近度放置组](https://docs.microsoft.com/azure/virtual-machines/linux/co-location)提高延迟
* 了解如何为你的方案[优化 vm 网络](../virtual-network/virtual-network-optimize-network-bandwidth.md)。
* 阅读有关如何[为虚拟机分配带宽](../virtual-network/virtual-machine-network-throughput.md)的信息
* 通过 [Azure 虚拟网络常见问题解答 (FAQ)](../virtual-network/virtual-networks-faq.md) 了解详细信息