---
title: 在 Azure 虚拟网络中测试 Azure 虚拟机网络延迟 |Microsoft Docs
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
ms.openlocfilehash: 00efc2754948d53d4f80a6261dbd4041b358185b
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/06/2019
ms.locfileid: "74896364"
---
# <a name="test-vm-network-latency"></a>测试 VM 网络延迟

若要获得最准确的结果，请使用为任务设计的工具来度量 Azure 虚拟机（VM）网络延迟。 公开提供的工具（如 SockPerf （适用于 Linux）和 latte （适用于 Windows））可以隔离并度量网络延迟，同时排除其他类型的延迟，如应用程序延迟。 这些工具专注于影响应用程序性能的网络流量类型（即，传输控制协议 [TCP] 和用户数据报协议 [UDP] 通信）。 

其他常见的连接工具（如 Ping）可能会度量延迟，但其结果可能并不代表实际工作负荷中使用的网络流量。 这是因为，这些工具中的大多数都采用 Internet 控制消息协议（ICMP），其处理方式不同于应用程序流量，其结果可能不适用于使用 TCP 和 UDP 的工作负载。 

对于大多数应用程序使用的协议的精确网络延迟测试，SockPerf （适用于 Linux）和 latte （适用于 Windows）会生成最相关的结果。 本文介绍这两种工具。

## <a name="overview"></a>概述

通过使用两个 Vm （一个作为发送方，另一个作为接收方），可以创建一个双向信道。 利用此方法，你可以双向发送和接收数据包，并测量往返时间（RTT）。

您可以使用此方法来测量两个 Vm 之间的网络延迟，甚至在两个物理计算机之间。 延迟度量可用于以下情况：

- 建立部署的 Vm 之间网络延迟的基准。
- 比较在对的相关更改后网络延迟更改的影响：
  - 操作系统（OS）或网络堆栈软件，包括配置更改。
  - VM 部署方法，例如，部署到可用性区域或邻近位置组（PPG）。
  - VM 属性，如加速网络或大小更改。
  - 虚拟网络，例如路由或筛选更改。

### <a name="tools-for-testing"></a>用于测试的工具
为了衡量延迟，有两个不同的工具选项：

* 对于基于 Windows 的系统： [latte （Windows）](https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b)
* 对于基于 Linux 的系统： [SockPerf （Linux）](https://github.com/mellanox/sockperf)

通过使用这些工具，可帮助确保仅对 TCP 或 UDP 有效负载传递时间进行度量，而不是应用程序不使用的其他数据包类型，也不会影响它们的性能。

### <a name="tips-for-creating-an-optimal-vm-configuration"></a>创建最佳 VM 配置的提示

创建 VM 配置时，请记住以下建议：
- 使用最新版本的 Windows 或 Linux。
- 启用加速网络以获得最佳结果。
- 使用[Azure 邻近性放置组](https://docs.microsoft.com/azure/virtual-machines/linux/co-location)部署 vm。
- 更大的 Vm 通常比小型 Vm 更好。

### <a name="tips-for-analysis"></a>分析提示

在分析测试结果时，请记住以下建议：

- 部署、配置和优化完成后，尽早建立基线。
- 始终将新结果与基线或进行比较，并将其从一个测试更改为另一个测试。
- 每当观察到更改或计划更改时重复测试。


## <a name="test-vms-that-are-running-windows"></a>测试运行 Windows 的 Vm

### <a name="get-latteexe-onto-the-vms"></a>将 latte 获取到 Vm

下载[最新版本的 latte](https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b)。

请考虑将 latte 放在单独的文件夹中，如*c:\tools*。

### <a name="allow-latteexe-through-windows-defender-firewall"></a>允许 latte 通过 Windows Defender 防火墙

在*接收方*上，在 Windows Defender 防火墙上创建允许规则，以允许 latte 流量到达。 最简单的方法是按名称允许整个 latte 程序，而不是允许特定的 TCP 端口入站。

通过运行以下命令，允许 latte 通过 Windows Defender 防火墙：

```cmd
netsh advfirewall firewall add rule program=<path>\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY
```

例如，如果将 latte 复制到*c:\tools*文件夹中，则该命令为：

`netsh advfirewall firewall add rule program=c:\tools\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY`

### <a name="run-latency-tests"></a>运行延迟测试

* 在*接收方*上启动 latte （从 CMD 窗口运行，而不是从 PowerShell 运行）：

    ```cmd
    latte -a <Receiver IP address>:<port> -i <iterations>
    ```

    大约65000迭代的时间足够长，以返回有代表性的结果。

    任何可用的端口号都是正确的。

    如果 VM 的 IP 地址为10.0.0.4，则命令将如下所示：

    `latte -a 10.0.0.4:5005 -i 65100`

* 在*发件人*上，启动 latte （从 CMD 窗口，而不是从 PowerShell 运行）：

    ```cmd
    latte -c -a <Receiver IP address>:<port> -i <iterations>
    ```

    生成的命令与接收方上的命令相同，不同之处在于，添加&nbsp; *-c*以指示这是*客户端*或*发送方*：

    `latte -c -a 10.0.0.4:5005 -i 65100`

等待结果。 测试可能需要几分钟才能完成，具体取决于 Vm 的距离。 请考虑从较少的迭代开始，以测试是否成功，然后再运行较长的测试。

## <a name="test-vms-that-are-running-linux"></a>测试运行 Linux 的 Vm

若要测试运行 Linux 的 Vm，请使用[SockPerf](https://github.com/mellanox/sockperf)。

### <a name="install-sockperf-on-the-vms"></a>在 Vm 上安装 SockPerf

在 Linux Vm 上（*发送方*和*接收方*），运行以下命令以在 vm 上准备 SockPerf。 为主要发行版提供了命令。

#### <a name="for-red-hat-enterprise-linux-rhelcentos"></a>对于 Red Hat Enterprise Linux （RHEL）/CentOS

运行以下命令：

```bash
#RHEL/CentOS - Install Git and other helpful tools
    sudo yum install gcc -y -q
    sudo yum install git -y -q
    sudo yum install gcc-c++ -y
    sudo yum install ncurses-devel -y
    sudo yum install -y automake
    sudo yum install -y autoconf
```

#### <a name="for-ubuntu"></a>适用于 Ubuntu

运行以下命令：

```bash
#Ubuntu - Install Git and other helpful tools
    sudo apt-get install build-essential -y
    sudo apt-get install git -y -q
    sudo apt-get install -y autotools-dev
    sudo apt-get install -y automake
    sudo apt-get install -y autoconf
```

#### <a name="for-all-distros"></a>适用于所有发行版

按照以下步骤复制、编译和安装 SockPerf：

```bash
#Bash - all distros

#From bash command line (assumes Git is installed)
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

SockPerf 安装完成后，Vm 即可运行延迟测试。 

首先，在*接收方*上启动 SockPerf。

任何可用的端口号都是正确的。 在此示例中，我们使用端口12345：

```bash
#Server/Receiver - assumes server's IP is 10.0.0.4:
sudo sockperf sr --tcp -i 10.0.0.4 -p 12345
```

既然服务器正在侦听，客户端就可以开始将数据包发送到它正在侦听的端口（在本例中为12345）上的服务器。

大约100秒足够长，可返回代表性结果，如以下示例中所示：

```bash
#Client/Sender - assumes server's IP is 10.0.0.4:
sockperf ping-pong -i 10.0.0.4 --tcp -m 350 -t 101 -p 12345  --full-rtt
```

等待结果。 根据 Vm 的距离，迭代次数会有所不同。 若要在运行较长的测试之前测试是否成功，请考虑从大约5秒的较短测试开始。

此 SockPerf 示例使用350字节的消息大小，这是一般数据包的典型值。 可以调整更高或更低的大小，以获得更准确地表示 Vm 上运行的工作负荷的结果。


## <a name="next-steps"></a>后续步骤
* 使用[Azure 邻近性放置组](https://docs.microsoft.com/azure/virtual-machines/linux/co-location)提高延迟。
* 了解如何为你的方案[优化 vm 网络](../virtual-network/virtual-network-optimize-network-bandwidth.md)。
* 了解[如何将带宽分配给虚拟机](../virtual-network/virtual-machine-network-throughput.md)。
* 有关详细信息，请参阅[Azure 虚拟网络常见问题解答](../virtual-network/virtual-networks-faq.md)。
