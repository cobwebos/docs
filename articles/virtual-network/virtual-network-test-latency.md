---
title: 在 Azure 虚拟网络中测试 Azure 虚拟机网络延迟 |Microsoft Docs
description: 了解如何测试虚拟网络中 Azure 虚拟机之间的网络延迟
services: virtual-network
documentationcenter: na
author: steveesp
manager: Marina Lipshteyn
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/29/2019
ms.author: steveesp
ms.openlocfilehash: 77ea14097538f722569acb5a0371674776aac8e5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "84687797"
---
# <a name="test-vm-network-latency"></a>测试 VM 网络延迟

若要获得最准确的结果，请使用为该任务设计的工具来测量 Azure 虚拟机 (VM) 网络延迟。 市售的工具包括 SockPerf（适用于 Linux）和 latte.exe（适用于 Windows），它们可以隔离和测量网络延迟，同时排除其他类型的延迟，例如应用程序延迟。 这些工具重点测量影响应用程序性能的网络流量类型（即传输控制协议 [TCP] 和用户数据报协议 [UDP] 流量）。 

其他常见的连接工具（例如 Ping）可以测量延迟，但其结果可能并不代表实际工作负荷中使用的网络流量。 这是因为，其中的大多数工具使用 Internet 控制消息协议 (ICMP)，而应用程序流量处理该协议的方式不同，因此其结果可能不适用于使用 TCP 和 UDP 的工作负荷。 

在准确测试大多数应用程序所用协议的网络延迟方面，SockPerf（适用于 Linux）和 latte.exe（适用于 Windows）生成的结果最为相关。 本文将介绍这两个工具。

## <a name="overview"></a>概述

通过使用两个 VM（一个作为发送方，另一个作为接收方），可以创建一个双向信道。 使用此方法，你可以双向发送和接收数据包，并测量往返时间 (RTT)。

可以使用此方法测量两个 VM 甚至两台物理计算机之间的网络延迟。 延迟测量可用于以下方案：

- 为部署的 VM 之间的网络延迟建立基准。
- 在进行下述相关更改后，比较网络延迟变化产生的效果：
  - 操作系统 (OS) 或网络堆栈软件，包括配置更改。
  - VM 部署方法，例如部署到可用性区域或邻近位置组 (PPG) 。
  - VM 属性，例如加速网络或大小更改。
  - 虚拟网络，例如路由或筛选更改。

### <a name="tools-for-testing"></a>测试工具
要测量延迟，有两个不同的工具可供你选择：

* 对于基于 Windows 的系统：[latte.exe (Windows)](https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b)
* 对于基于 Linux 的系统： [SockPerf (linux) ](https://github.com/mellanox/sockperf)

使用这些工具有助于确保仅测量 TCP 或 UDP 有效负载传送时间，而不测量应用程序未使用的，且不影响应用程序性能的 ICMP (Ping) 或其他数据包类型。

### <a name="tips-for-creating-an-optimal-vm-configuration"></a>有关创建最佳 VM 配置的技巧

创建 VM 配置时，请记住以下建议：
- 使用最新版本的 Windows 或 Linux。
- 启用加速网络以获得最佳结果。
- 使用 [Azure 邻近性放置组](https://docs.microsoft.com/azure/virtual-machines/linux/co-location)部署 vm。
- 大型 VM 的性能往往优于小型 VM。

### <a name="tips-for-analysis"></a>有关分析的提示

在分析测试结果时，请记住以下建议：

- 完成部署、配置和优化后尽早建立基线。
- 始终将新结果与基线进行比较，或者在进行受控的更改后将每次测试结果进行比较。
- 每当观测到或规划更改后重复测试。


## <a name="test-vms-that-are-running-windows"></a>测试运行 Windows 的 VM

### <a name="get-latteexe-onto-the-vms"></a>将 latte.exe 加载到 VM

下载 [最新版本的 latte.exe](https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b)。

考虑将 latte.exe 放在单独的文件夹中，例如 *c:\tools*。

### <a name="allow-latteexe-through-windows-defender-firewall"></a>允许 latte.exe 通过 Windows Defender 防火墙

在“接收方”** 的 Windows Defender 防火墙中创建“允许”规则，以允许 latte.exe 流量抵达。 最简单的方法是按名称允许整个 latte.exe 程序，而不是允许特定的 TCP 端口入站。

通过运行以下命令，允许 latte.exe 通过 Windows Defender 防火墙：

```cmd
netsh advfirewall firewall add rule program=<path>\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY
```

例如，如果将 latte.exe 复制到 *c:\tools* 文件夹中，则该命令为：

`netsh advfirewall firewall add rule program=c:\tools\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY`

### <a name="run-latency-tests"></a>运行延迟测试

* 在“接收方”**，启动 latte.exe（从 CMD 窗口而不是从 PowerShell 运行它）：

    ```cmd
    latte -a <Receiver IP address>:<port> -i <iterations>
    ```

    大约 65,000 次迭代就足以返回具有代表性的结果。

    可输入任意可用端口号。

    如果 VM 的 IP 地址为 10.0.0.4，则命令如下：

    `latte -a 10.0.0.4:5005 -i 65100`

* 在“发送方”**，启动 latte.exe（从 CMD 窗口而不是从 PowerShell 运行它）：

    ```cmd
    latte -c -a <Receiver IP address>:<port> -i <iterations>
    ```

    生成的命令与在接收方相同，只是添加了 &nbsp;*-c* 来指示这是“客户端”或“发送方”****：

    `latte -c -a 10.0.0.4:5005 -i 65100`

等待结果。 测试可能需要几分钟时间才能完成，具体取决于 VM 之间的距离。 考虑先运行较少的迭代次数以使测试成功，然后再运行较长的测试。

## <a name="test-vms-that-are-running-linux"></a>测试运行 Linux 的 VM

若要测试运行 Linux 的 VM，请使用 [SockPerf](https://github.com/mellanox/sockperf)。

### <a name="install-sockperf-on-the-vms"></a>在 VM 上安装 SockPerf

在 Linux VM 上（包括“发送方”和“接收方”****），运行以下命令以在 VM 上准备 SockPerf。 提供的命令适用于主要分发版。

#### <a name="for-red-hat-enterprise-linux-rhelcentos"></a>对于 Red Hat Enterprise Linux (RHEL) /CentOS

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

#### <a name="for-ubuntu"></a>对于 Ubuntu

运行以下命令：

```bash
#Ubuntu - Install Git and other helpful tools
    sudo apt-get install build-essential -y
    sudo apt-get install git -y -q
    sudo apt-get install -y autotools-dev
    sudo apt-get install -y automake
    sudo apt-get install -y autoconf
```

#### <a name="for-all-distros"></a>对于所有分发版

请按以下步骤复制、编译并安装 SockPerf：

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

### <a name="run-sockperf-on-the-vms"></a>在 VM 上运行 SockPerf

SockPerf 安装完成后，便可以在 VM 上运行延迟测试了。 

首先，在“接收方”** 启动 SockPerf。

可输入任意可用端口号。 在此示例中，我们使用端口 12345：

```bash
#Server/Receiver - assumes server's IP is 10.0.0.4:
sudo sockperf sr --tcp -i 10.0.0.4 -p 12345
```

现在，服务器正在侦听，客户端可以通过服务器侦听的端口（在本例中为 12345）开始将数据包发送到服务器。

等待大约 100 秒就足以返回有代表性的结果，如以下示例中所示：

```bash
#Client/Sender - assumes server's IP is 10.0.0.4:
sockperf ping-pong -i 10.0.0.4 --tcp -m 350 -t 101 -p 12345  --full-rtt
```

等待结果。 迭代次数根据 VM 的距离而异。 考虑先运行大约 5 秒的短时间测试以使测试成功，然后再运行较长时间的测试。

此 SockPerf 示例使用 350 字节消息大小，这是平均数据包的典型大小。 你可以调大或调小消息大小，使结果能够更准确地代表 VM 上运行的工作负荷。


## <a name="next-steps"></a>后续步骤
* 使用 [Azure 邻近性放置组](https://docs.microsoft.com/azure/virtual-machines/linux/co-location)提高延迟。
* 了解如何根据方案[优化 VM 的网络](../virtual-network/virtual-network-optimize-network-bandwidth.md)。
* 了解 [如何将带宽分配给虚拟机](../virtual-network/virtual-machine-network-throughput.md)。
* 有关详细信息，请参阅 [Azure 虚拟网络常见问题解答](../virtual-network/virtual-networks-faq.md)。
