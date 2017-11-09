---
title: "使用 Azure 网络观察程序执行数据包检查 | Microsoft 文档"
description: "本文介绍如何在 VM 中使用网络观察程序执行深度数据包检查"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: 7b907d00-9c35-40f5-a61e-beb7b782276f
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 1ad6ca4abe73336ce9ce3539fdaf2a9d7dd23fa6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="packet-inspection-with-azure-network-watcher"></a>使用 Azure 网络观察程序执行数据包检查

使用网络观察程序的数据包捕获功能，可在门户、PowerShell、CLI 中以及通过 SDK 和 REST API 以编程方式在 Azure VM 上启动和管理捕获会话。 借助数据包捕获，可通过以随时可用的格式提供信息，来解决需要数据包级数据的方案。 利用免费工具检查数据，可以检测传入和传出 VM 的通信并洞察网络流量。 数据包捕获数据的一些示例用途包括：调查网络或应用程序问题、检测网络滥用和入侵企图，或保持合规性。 本文介绍如何使用流行的开源工具打开网络观察程序提供的数据包捕获文件。 此外，还举例说明了如何计算连接延迟、识别异常流量，以及检查网络统计信息。

## <a name="before-you-begin"></a>开始之前

本文会回顾以前运行的有关数据包捕获的一些预配置方案。 这些方案演示了可以通过查看数据包捕获访问的功能。 本方案使用 [WireShark](https://www.wireshark.org/) 来检查数据包捕获。

本方案假设已在虚拟机上运行数据包捕获。 若要了解如何创建数据包捕获，请访问 [Manage packet captures with the portal](network-watcher-packet-capture-manage-portal.md)（使用门户管理数据包捕获）；若要了解如何使用 REST 进行相应操作，请访问 [Managing Packet Captures with REST API](network-watcher-packet-capture-manage-rest.md)（使用 REST API 管理数据包捕获）。

## <a name="scenario"></a>方案

本方案中的操作：

* 检查数据包捕获

## <a name="calculate-network-latency"></a>计算网络延迟

本方案说明如何查看两个终结点之间发生的传输控制协议 (TCP) 对话的初始往返时间 (RTT)。

建立 TCP 连接后，在连接中发送的前三个数据包遵循一种通常称作“三次握手”的模式。 通过检查此握手中发送的前两个数据包、客户端发出的初始请求以及服务器发出的响应，我们可以计算建立此连接时的延迟。 此延迟称为“往返时间”(RTT)。 有关 TCP 协议和三次握手的详细信息，请参阅以下资源。 https://support.microsoft.com/en-us/help/172983/explanation-of-the-three-way-handshake-via-tcp-ip

### <a name="step-1"></a>步骤 1

启动 WireShark

### <a name="step-2"></a>步骤 2

从数据包捕获加载 **.cap** 文件。 根据具体的配置，此文件保存在 Blob 中或者虚拟机本地。

### <a name="step-3"></a>步骤 3

若要查看 TCP 对话中的初始往返时间 (RTT)，只需检查 TCP 握手中涉及的前两个数据包。 我们将使用三次握手中的前两个数据包，即 [SYN]、和 [SYN, ACK] 数据包。 这两个数据包是根据 TCP 标头中的标志命名的。 本方案不使用握手中的最后一个数据包，即 [ACK] 数据包。 [SYN] 数据包由客户端发送。 收到该数据包后，服务器将发送 [ACK] 数据包，表示确认收到客户端发来的 SYN。 利用服务器响应所需的开销极少这一事实，可通过对客户端收到 [SYN, ACK] 数据包的时间与客户端发送 [SYN] 数据包的时间进行减法运算，来计算 RTT。

使用 WireShark 可以计算此值。

为了更轻松地查看 TCP 三次握手中的前两个数据包，我们将利用 WireShark 提供的筛选功能。

要应用 WireShark 中的筛选器，请展开捕获中 [SYN] 数据包的“Transmission Control Protocol”段，并检查 TCP 标头中设置的标志。

由于我们想要针对所有 [SYN] 和 [SYN, ACK] 数据包执行筛选，因此应该在标志下面确认 Syn 位设置为 1，并右键单击 Syn 位 ->“应用为筛选器”->“选定”。

![图 7][7]

### <a name="step-4"></a>步骤 4

筛选窗口内容以便仅显示设置了 [SYN] 位的数据包后，可以轻松选择所需的对话来查看初始 RTT。 在 WireShark 中查看 RTT 的一种简单方法是单击带有“SEQ/ACK”分析标记的下拉框。 然后便会显示 RTT。 在本例中RTT 为 0.0022114 秒，即 2.211 毫秒。

![图 8][8]

## <a name="unwanted-protocols"></a>不需要的协议

在 Azure 中部署的虚拟机实例上可能运行了大量的应用程序。 其中的许多应用程序可能在未得到明确许可的情况下通过网络通信。 使用数据包捕获存储网络通信，可以调查应用程序如何在网络上通信，以及检查是否出现了任何问题。

在本示例中，我们会检查以前运行的数据包捕获是否存在不需要的协议，这可能表示计算机上运行的应用程序正在进行未经授权的通信。

### <a name="step-1"></a>步骤 1

使用前一方案中的同一个捕获。单击“统计信息” > “协议层次结构”

![协议层次结构菜单][2]

此时会显示协议层次结构窗口。 此视图提供在捕获会话期间使用的所有协议的列表，以及使用协议传输和接收的数据包数目。 在查找虚拟机或网络上不需要的网络流量时，此视图可能很有作用。

![打开的协议层次结构][3]

如以下屏幕截图中所示，有的流量使用了 BitTorrent 协议来建立对等文件共享。 管理员不希望此特定虚拟机上出现 BitTorrent 流量。 现在我们发现了这种流量，可以删除此虚拟机上安装的对等软件，或者使用网络安全组或防火墙来阻止该流量。 此外，可以选择按计划运行数据包捕获，以便定期检查虚拟机上使用的协议。 有关如何在 Azure 中自动执行任务的示例，请访问 [Monitor network resources with azure automation](network-watcher-monitor-with-azure-automation.md)（使用 Azure 自动化监视网络资源）

## <a name="finding-top-destinations-and-ports"></a>查找最常使用的目标和端口

在监视网络上的应用程序和资源或者对其进行故障排除时，了解流量类型、终结点以及通信时所用的端口非常重要。 利用上述数据包捕获文件，可以快速了解与 VM 通信最频繁的目标以及使用的端口。

### <a name="step-1"></a>步骤 1

使用前一方案中的同一个捕获。单击“统计信息” > “IPv4 统计信息” > “目标和端口”

![数据包捕获窗口][4]

### <a name="step-2"></a>步骤 2

从带有线框的结果中可以看出，通过端口 111 建立了多个连接。 最常用的端口是 3389（远程桌面使用的端口），其余的端口是 RPC 动态端口。

尽管这种流量可能无害，但许多连接都使用了此端口，而管理员并不了解这些连接。

![图 5][5]

### <a name="step-3"></a>步骤 3

确定有问题的端口后，可以根据该端口筛选捕获。

本方案中的筛选器为：

```
tcp.port == 111
```

在筛选器文本框中输入上面所示的筛选器文本，并按 Enter。

![图 6][6]

从结果可以看出，所有流量来自同一子网中的某个本地虚拟机。 如果我们仍不了解发生此流量的原因，可以进一步检查数据包，确定该流量为何在端口 111 上发出这些调用。 使用这些信息可以采取相应的措施。

## <a name="next-steps"></a>后续步骤

访问 [Azure network monitoring overview](network-watcher-monitoring-overview.md)（Azure 网络监视概述），了解网络观察程序的其他诊断功能

[1]: ./media/network-watcher-deep-packet-inspection/figure1.png
[2]: ./media/network-watcher-deep-packet-inspection/figure2.png
[3]: ./media/network-watcher-deep-packet-inspection/figure3.png
[4]: ./media/network-watcher-deep-packet-inspection/figure4.png
[5]: ./media/network-watcher-deep-packet-inspection/figure5.png
[6]: ./media/network-watcher-deep-packet-inspection/figure6.png
[7]: ./media/network-watcher-deep-packet-inspection/figure7.png
[8]: ./media/network-watcher-deep-packet-inspection/figure8.png













