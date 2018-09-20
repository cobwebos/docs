---
title: 计算的容量规划 Azure Stack |Microsoft Docs
description: 了解如何规划 Azure Stack 部署的计算能力。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2018
ms.author: jeffgilb
ms.reviewer: prchint
ms.custom: mvc
ms.openlocfilehash: b98879483d35a91810c9e9ab5e0ac81151bde52f
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/19/2018
ms.locfileid: "46368961"
---
# <a name="azure-stack-compute-capacity-planning"></a>Azure Stack 计算容量规划
[支持在 Azure Stack 上的 VM 大小](.\user\azure-stack-vm-sizes.md)是所支持在 Azure 上的一个子集。 Azure 施加沿许多向量，以避免资源 （服务器本地和服务级别） 的过度消耗的资源限制。 而不施加某些限制租户使用情况，租户体验将会降低其他租户 overconsume 资源时。 从虚拟机的网络出口，是在 Azure Stack 上的位置匹配 Azure 限制的带宽上限。 对于存储资源，存储 IOPs 限制已经实现了在 Azure Stack，以避免资源过度的存储访问的租户。  

## <a name="vm-placement-and-virtual-to-physical-core-overprovisioning"></a>VM 放置和虚拟与物理核心过度预配
在 Azure Stack 中，没有要用于 VM 放置租户来指定特定服务器的方法。 放置 Vm 时，仅考虑因素是该 VM 类型的主机上是否存在足够的内存。 Azure Stack 不会不过度使用内存;但是，允许的核心数的过度提交。 在现有虚拟与物理内核作为一个因素的过度预配比看不到放置算法，因为每个主机可以有不同的比。 

在 Azure 中，以实现高可用性的多 VM 生产系统中，虚拟机放置在可用性集中地分布到多个容错域。 这就意味着 Vm 放入可用性集是以物理方式相互隔离的在机架上，以允许进行故障还原，如以下关系图中所示：

![容错和更新域](media\azure-stack-capacity-planning\domains.png)


从故障中复原的 Azure Stack 基础结构时，基础技术 （故障转移群集） 仍会产生一定的停机时间的 Vm 受影响的物理服务器上出现硬件故障。 目前，Azure Stack 支持的可用性集最多三个容错域与 Azure 保持一致。 置于可用性集中的 VM 在物理上是彼此隔离的，换句话说，会尽可能均衡地让其分散到多个容错域（Azure Stack 节点）中。 如果不存在硬件故障，故障的容错域中的 Vm 将在其他节点重新启动，但是，如果可能，请从同一个可用性集中其他 Vm 保留在单独的容错域中。 当硬件重新联机时，会对 VM 重新进行均衡操作，以维持高可用性。

Azure 用于提供高可用性的另一个概念是在可用性集中的更新域的形式。 更新域是可以同时维护或重新启动的基础硬件逻辑组。 在 Azure Stack 中，VM 会先跨群集中的其他联机主机进行实时迁移，然后其基础主机才会进行更新。 由于在主机更新期间不会造成租户停机，因此 Azure Stack 上存在更新域功能只是为了确保与 Azure 实现模板兼容。

## <a name="azure-stack-resiliency-resources"></a>Azure Stack 可恢复性资源
允许修补和更新 Azure Stack 集成系统，并以适应物理硬件故障，总服务器内存的一部分被保留并且放置租户虚拟机 (VM) 的不可用。

如果某个服务器发生故障，将剩余，可用来提供有关 VM 可用性的服务器上重新启动故障的服务器上托管的 Vm。 同样，在修补和更新过程中，在服务器上运行的所有 Vm 将进行都实时迁移到其他可用，服务器。 此 VM 管理或移动可仅实现保留的容量以允许重新启动或迁移发生时。

以下计算会导致可用于租户 VM 放置的总数、 可用内存。 此内存容量是在整个 Azure Stack 缩放单位。

  VM 放置虚拟机的可用内存 = 总服务器内存 – 复原预留 – Azure Stack 基础结构开销<sup>1</sup>

  复原能力保留 = H + R * (N-1) + V * (N-2)

> 其中：
> - H = 单个服务器内存的大小
> - N = 大小的缩放单位 （服务器的数量）
> - R = 操作系统预留的操作系统开销<sup>2</sup>
> - V = 缩放单位中的最大 VM

  <sup>1</sup> azure Stack 基础结构开销 = 208 GB

  <sup>2</sup>操作系统保留的开销 = 15%的节点内存。 操作系统预留值是一个估计值，并将因服务器和常规操作系统开销的物理内存容量。

值为 V，缩放单位中的最大 VM 是动态基于最大租户 VM 内存大小。 例如，7 GB 或 112 GB 或任何其他受支持的 VM 内存大小，以 Azure Stack 解决方案，可能是最大 VM 值。

上述计算公式是一个估计值和基于 Azure Stack 的当前版本可能会有所更改。 部署租户虚拟机和服务的能力取决于已部署的解决方案的具体情况。 此示例的计算是只是指南，并部署 Vm 的功能不是绝对的答案。



## <a name="next-steps"></a>后续步骤
[存储容量规划](capacity-planning-storage.md)
