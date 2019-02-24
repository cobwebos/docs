---
title: 使用 Azure Site Recovery 服务将 Azure IaaS VM 移动到其他 Azure 区域 | Microsoft Docs
description: 使用 Azure Site Recovery 将 Azure IaaS VM 从一个 Azure 区域移动到另一个 Azure 区域。
services: site-recovery
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: 1560dad31e00b00a2d27cf1675e05901e6611825
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/15/2019
ms.locfileid: "56309856"
---
# <a name="move-azure-vms-to-another-region"></a>将 Azure VM 移动到另一区域

Azure 随着客户群的扩大而不断扩建，由于需求的增大，我们将会添加新区域的支持。 此外，每个月都会添加新的服务功能。 因此，有时你想要将 VM 移到不同的区域或可用性区域以提高可用性。

本文档逐步讲解在哪种场合下可以移动 VM，并提供有关如何在目标中配置体系结构以实现更高可用性的指导。 
> [!div class="checklist"]
> * [为何要移动 Azure VM](#why-would-you-move-azure-vms)
> * [如何移动 Azure VM](#how-to-move-azure-vms)
> * [典型体系结构](#typical-architectures-for-a-multi-tier-deployment)
> * [将 VM 按原样移到目标区域](#move-azure-vms-to-another-region)
> * [移动 VM 以提高可用性](#move-vms-to-increase-availability)


## <a name="why-would-you-move-azure-vms"></a>为何要移动 Azure VM

客户移动 VM 的原因如下：-

- 如果你在一个区域中部署了 VM，而 Azure 添加了新的支持区域，并且该区域更靠近应用程序或服务的最终用户，则你可以**将 VM 按原样移到新区域**以降低延迟。 若要整合订阅或者监管/组织规则要求移动 VM，可以采用相同的方法。 
- 如果 VM 部署为单一实例 VM 或部署为可用性集的一部分，并且你希望提高可用性 SLA，则可**将 VM 移动到可用性区域中**。 

## <a name="how-to-move-azure-vms"></a>如何移动 Azure VM
移动 VM 的过程包括以下步骤：

1. 验证先决条件 
2. 准备源 VM 
3. 准备目标区域 
4. 将数据复制到目标区域 - 使用 Azure Site Recovery 复制技术将源 VM 中的数据复制到目标区域
5. 测试配置：复制完成后，通过执行到非生产网络的测试故障转移来测试配置。
6. 执行移动 
7. 丢弃源区域中的资源 


> [!IMPORTANT]
> 目前，Azure Site Recovery 支持将 VM 从一个区域移到另一个区域，而不支持在一个区域中移动。 

> [!NOTE]
> [此处](#next-steps)所述的每种方案的文档中提供了这些步骤的详细指导

## <a name="typical-architectures-for-a-multi-tier-deployment"></a>多层部署的典型体系结构
以下部分逐步讲解客户对 Azure 中的多层应用程序最常采用的部署体系结构。 此处使用的示例是采用公共 IP 的三层式应用程序。 每个层（Web 层、应用层和数据库层）各有 2 个 VM，并已通过负载均衡器连接到其他层。 数据库层在 VM 之间使用 SQL Always ON 复制，以实现高可用性 (HA)。

1.  **跨不同的层部署的单一实例 VM** - 层中的每个 VM 配置为单一实例 VM，已通过负载均衡器连接到其他层。 这是客户采用的最简单配置。

       ![single-VMs](media/move-vm-overview/regular-deployment.PNG)

2. **每个层中跨可用性集部署的 VM** - 层中的每个 VM 在可用性集中进行配置。 [可用性集](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets)确保在 Azure 上部署的 VM 能够跨群集中多个隔离的硬件节点分布。 这样，就可以确保当 Azure 中发生硬件或软件故障时，只有一部分 VM 会受到影响，整体解决方案仍可使用和操作。 
   
      ![avset](media/move-vm-overview/AVset.PNG)

3. **每个层中跨可用性集部署的 VM** - 层中的每个 VM 跨[可用性区域](https://docs.microsoft.com/azure/availability-zones/az-overview)进行配置。 Azure 区域中的可用性区域是容错域和更新域的组合。 例如，如果在 Azure 区域的三个区域中创建三个或更多 VM，则 VM 将有效分布在三个容错域和三个更新域中。 Azure 平台会识别更新域上的此分布，以确保不同区域中的 VM 不会同时更新。

      ![zone-deploymnt](media/move-vm-overview/zone.PNG)



 ## <a name="move-vms-as-is-to-a-target-region"></a>将 VM 按原样移到目标区域

根据上述[体系结构](#typical-architectures-for-a-multi-tier-deployment)，下面演示了将 VM 按原样移到目标区域后的部署布局。


1. **跨不同的层部署的单一实例 VM** 

     ![single-zone.PNG](media/move-vm-overview/single-zone.PNG)

2. **每个层中跨可用性集部署的 VM**

     ![crossregionAset.PNG](media/move-vm-overview/crossregionAset.PNG)


3. **每个层中跨可用性区域部署的 VM**
      

     ![AzoneCross.PNG](media/move-vm-overview/AzoneCross.PNG)

## <a name="move-vms-to-increase-availability"></a>移动 VM 以提高可用性

1. **跨不同的层部署的单一实例 VM** 

     ![single-zone.PNG](media/move-vm-overview/single-zone.PNG)

2. **每个层中跨可用性集部署的 VM** - 使用 Azure Site Recovery 为 VM 启用复制时，可以选择配置为将可用性集中的 VM 放入单独的可用性区域。 完成移动操作后，可用性 SLA 将达到 99.9%。

     ![aset-Azone.PNG](media/move-vm-overview/aset-Azone.PNG)


## <a name="next-steps"></a>后续步骤

本文档介绍了有关移动 VM 的一般指导。 若要了解此操作的详细执行步骤，请阅读：


> [!div class="nextstepaction"]
> * [将 Azure VM 移到另一区域](azure-to-azure-tutorial-migrate.md)

> * [将 Azure VM 移到可用性区域中](move-azure-VMs-AVset-Azone.md)

