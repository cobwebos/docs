---
title: "Azure 虚拟机网络吞吐量 | Microsoft Docs"
description: "了解 Azure 虚拟机网络吞吐量。"
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/13/2017
ms.author: jdial
ms.openlocfilehash: 536a779d7de51180aa6410911dea2b6c47780c2f
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/16/2017
---
# <a name="virtual-machine-network-throughput"></a>虚拟机网络吞吐量

Azure 提供各种虚拟机大小和类型，每一种包含的性能各不相同。 在这些性能中，有一种是网络吞吐量（也称带宽），以兆位/秒 (Mbps) 表示。 由于虚拟机托管在共享硬件上，因此网络容量必须在共享同一硬件的虚拟机中公平地共享。 在分配时，较大的虚拟机相对于较小的虚拟机会获得相对较多的带宽。
 
分配给每个虚拟机的网络带宽按虚拟机的传出（出站）流量计算。 从虚拟机流出的所有网络流量均计入分配限制，不管流向哪个目标。 例如，如果虚拟机的限制为 1,000 Mbps，则不管出站流量的目标是同一虚拟网络中的另一虚拟机，还是 Azure 外部，均适用该限制。
 
传入流量不直接计算，或者说不直接受到限制。 但是，其他因素（例如 CPU 和存储限制）可能会影响虚拟机处理传入数据的能力。

[加速网络](virtual-network-create-vm-accelerated-networking.md)是一项旨在改进网络性能（包括延迟、吞吐量和 CPU 使用率）的功能。 虽然加速网络可以改进虚拟机的吞吐量，但仍受分配给该虚拟机的带宽的限制。
 
Azure 虚拟机必须有一个（但也可能有多个）连接的网络接口。 分配给某个虚拟机的带宽是流经所有网络接口（已连接到该虚拟机）的所有出站流量的总和。 换言之，分配的带宽是针对每个虚拟机的，不管为该虚拟机连接了多少网络接口。 若要了解不同的 Azure VM 大小支持的网络接口数，请查看 Azure [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 和 [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) VM 大小。 

## <a name="expected-network-throughput"></a>预期的网络吞吐量

若要详细了解每种 VM 大小支持的预期出站吞吐量和网络接口数，请查看 Azure [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 和 [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) VM 大小。 选择一个类型（例如“通用”），然后在生成的页面上选择一个大小系列（例如“Dv2 系列”）。 每个系统都有一个表，在最后一列（名为“最大 NIC 数/预期网络性能(Mbps)”）中包含网络规格。 

吞吐量限制适用于虚拟机。 吞吐量不受以下因素影响：
- **网络接口数**：带宽限制是源自虚拟机的所有出站流量的累积。
- **加速网络**：尽管此功能有助于流量达到已发布的限制，但不会更改限制。
- **流量目标**：所有目标都计入出站限制。
- **协议**：基于所有协议的所有出站流量都计入限制。

## <a name="next-steps"></a>后续步骤

- [优化虚拟机操作系统的网络吞吐量](virtual-network-optimize-network-bandwidth.md)
- 针对虚拟机[测试网络吞吐量](virtual-network-bandwidth-testing.md)。