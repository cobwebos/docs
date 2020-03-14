---
title: Azure 虚拟机网络吞吐量 | Microsoft Docs
description: 了解 Azure 虚拟机网络吞吐量。
services: virtual-network
documentationcenter: na
author: steveesp
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 4/26/2019
ms.author: steveesp
ms.reviewer: kumud, mareat
ms.openlocfilehash: 47f58b25b082784177910d14ab95d8d242fda71a
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79245429"
---
# <a name="virtual-machine-network-bandwidth"></a>虚拟机网络带宽

Azure 提供各种 VM 大小和类型，每一种包含的性能各不相同。 其中一种是网络吞吐量（也称带宽），以兆位/秒 (Mbps) 表示。 由于虚拟机托管在共享硬件上，因此网络容量必须在共享同一硬件的虚拟机中公平地共享。 在分配时，较大的虚拟机相对于较小的虚拟机会获得相对较多的带宽。
 
分配给每个虚拟机的网络带宽按虚拟机的传出（出站）流量计算。 从虚拟机流出的所有网络流量均计入分配限制，不管流向哪个目标。 例如，如果虚拟机的限制为 1,000 Mbps，则不管出站流量的目标是同一虚拟网络中的另一虚拟机，还是 Azure 外部，均适用该限制。
 
传入流量不直接计算，或者说不直接受到限制。 但是，其他因素（例如 CPU 和存储限制）可能会影响虚拟机处理传入数据的能力。

加速网络是一项旨在改进网络性能（包括延迟、吞吐量和 CPU 使用率）的功能。 虽然加速网络可以改进虚拟机的吞吐量，但仍受分配给该虚拟机的带宽的限制。 若要详细了解如何使用加速网络，请查看适用于 [Windows](create-vm-accelerated-networking-powershell.md) 或 [Linux](create-vm-accelerated-networking-cli.md) 虚拟机的加速网络。
 
Azure 虚拟机必须有一个（但也可能有多个）连接的网络接口。 分配给某个虚拟机的带宽是流经所有网络接口（已连接到该虚拟机）的所有出站流量的总和。 换言之，分配的带宽是针对每个虚拟机的，不管为该虚拟机连接了多少网络接口。 若要了解不同的 Azure VM 大小支持的网络接口数，请查看 Azure [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 和 [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) VM 大小。 

## <a name="expected-network-throughput"></a>预期的网络吞吐量

若要详细了解每种 VM 大小支持的预期出站吞吐量和网络接口数，请查看 Azure [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 和 [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) VM 大小。 选择一个类型（例如“通用”），然后在生成的页面上选择一个大小系列（例如“Dv2 系列”）。 每个系统都有一个表，在最后一列（名为“最大 NIC 数/预期网络性能(Mbps)”）中包含网络规格。 

吞吐量限制适用于虚拟机。 吞吐量不受以下因素影响：
- **网络接口数**：带宽限制是源自虚拟机的所有出站流量的累积。
- **加速网络**：尽管此功能有助于流量达到已发布的限制，但不会更改限制。
- **流量目标**：所有目标都计入出站限制。
- **协议**：基于所有协议的所有出站流量都计入限制。

## <a name="network-flow-limits"></a>网络流限制

除了带宽以外，在任何给定时间虚拟机上存在的网络连接数都可能影响其网络性能。 对于名为 "流" 的数据结构，Azure 网络堆栈为 TCP/UDP 连接的每个方向维护状态。 典型的 TCP/UDP 连接将创建2个流，一个用于入站，另一个用于出站方向。 

终结点之间的数据传输需要创建多个流，以及执行数据传输的流。 一些示例包括为 DNS 解析创建的流，以及为负载均衡器运行状况探测创建的流。 另请注意，网络虚拟设备（Nva）（例如网关、代理、防火墙）会看到为在设备上终止的连接而创建的流，并由设备发起。 

![通过转发设备进行 TCP 对话的流计数](media/virtual-machine-network-throughput/flow-count-through-network-virtual-appliance.png)

## <a name="flow-limits-and-recommendations"></a>流限制和建议

如今，Azure 网络堆栈支持250K 的总网络流，并为具有8个以上 CPU 核心和10万多个流的 Vm 提供良好性能，且 CPU 内核数少于8个的 vm 具有良好的性能。 超过此限制时，网络性能会平稳地降低到50万个总流的硬限制，250K 入站和250K 出站，超过此限制后，将丢弃附加的流。

||具有 < 8 CPU 内核的 Vm|具有8个 CPU 内核的 Vm|
|---|---|---|
|<b>性能良好</b>|100K 流 |250K 流|
|<b>性能下降</b>|超过100k 流|以上250K 流|
|<b>流限制</b>|50万个流|50万个流|

[Azure Monitor](../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachines)中提供了度量值，用于跟踪 VM 或 VMSS 实例上的网络流数和流创建速率。

![azure-monitor-flow-metrics.png](media/virtual-machine-network-throughput/azure-monitor-flow-metrics.png)

连接建立和终止速度还会影响网络性能，因为连接建立和终止将 CPU 与数据包处理例程结合在一起。 建议根据预期流量模式对工作负荷进行基准测试，并适当地横向扩展工作负荷以满足性能需求。 

## <a name="next-steps"></a>后续步骤

- [优化虚拟机操作系统的网络吞吐量](virtual-network-optimize-network-bandwidth.md)
- 针对虚拟机[测试网络吞吐量](virtual-network-bandwidth-testing.md)。
