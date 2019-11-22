---
title: 使用 Azure Site Recovery 将 Azure VM 移动到另一个区域
description: 使用 Azure Site Recovery 将 Azure VM 从一个 Azure 区域移动到另一个 Azure 区域。
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: 05d0fcb9e689b8954bffaff402475762344c023d
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/14/2019
ms.locfileid: "74091939"
---
# <a name="moving-azure-vms-to-another-azure-region"></a>将 Azure VM 移动到另一个 Azure 区域

本文概述了使用 [Azure Site Recovery](site-recovery-overview.md) 将 Azure VM 移动到另一个 Azure 区域的原因和所涉及的步骤。 


## <a name="reasons-to-move-azure-vms"></a>移动 Azure VM 的原因

移动 VM 的原因如下：

- 你已在一个区域中部署了 VM，而 Azure 添加了新的支持区域，并且该区域更靠近应用程序或服务的最终用户。 在这种情况下，可将 VM 按原样移到新区域以降低延迟。 若要整合订阅或者监管或组织规则要求移动 VM，可以使用相同的方法。
- VM 部署为单一实例 VM 或部署为可用性集的一部分。 若要提高可用性 SLA，可将 VM 移到可用性区域。

## <a name="steps-to-move-azure-vms"></a>移动 Azure VM 的步骤

移动 VM 的过程包括以下步骤：

1. 验证先决条件。
2. 准备源 VM。
3. 准备目标区域。
4. 将数据复制到目标区域。 使用 Azure Site Recovery 复制技术将源 VM 中的数据复制到目标区域。
5. 测试配置。 复制完成后，通过执行到非生产网络的测试故障转移来测试配置。
6. 执行移动。
7. 丢弃源区域中的资源。

> [!NOTE]
> 以下部分提供了有关这些步骤的详细信息。
> [!IMPORTANT]
> 目前，Azure Site Recovery 支持将 VM 从一个区域移到另一个区域，但不支持在一个区域中移动。

## <a name="typical-architectures-for-a-multi-tier-deployment"></a>多层部署的典型体系结构

本部分介绍 Azure 中的多层应用程序的最常用部署体系结构。 示例是采用公共 IP 的三层式应用程序。 每个层（Web 层、应用层和数据库层）各有两个 VM，并已通过 Azure 负载均衡器连接到其他层。 数据库层在 VM 之间使用 SQL Server Always On 复制，以实现高可用性。

* **跨不同的层部署的单一实例 VM**：层中的每个 VM 配置为单一实例 VM，并已通过负载均衡器连接到其他层。 此配置最容易采用。

     ![跨层的单一实例 VM 部署](media/move-vm-overview/regular-deployment.png)

* **每个层中跨可用性集部署的 VM**：层中的每个 VM 在可用性集中进行配置。 [可用性集](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets)确保在 Azure 上部署的 VM 能够跨群集中多个隔离的硬件节点分布。 这可以确保当 Azure 中发生硬件或软件故障时，只有一部分 VM 会受到影响，整体解决方案仍可使用和操作。

     ![跨可用性集的 VM 部署](media/move-vm-overview/avset.png)

* **每个层中跨可用性区域部署的 VM**：层中的每个 VM 跨[可用性区域](https://docs.microsoft.com/azure/availability-zones/az-overview)进行配置。 Azure 区域中的可用性区域是容错域和更新域的组合。 例如，如果在 Azure 区域的三个区域中创建三个或更多 VM，则 VM 将有效分布在三个容错域和三个更新域中。 Azure 平台会识别更新域上的此分布，以确保不同区域中的 VM 不会同时更新。

     ![可用性区域部署](media/move-vm-overview/zone.png)

## <a name="move-vms-as-is-to-a-target-region"></a>将 VM 按原样移到目标区域

根据上述[体系结构](#typical-architectures-for-a-multi-tier-deployment)，下面演示了将 VM 按原样移到目标区域后的部署布局。

* **跨不同的层部署的单一实例 VM**

     ![跨层的单一实例 VM 部署](media/move-vm-overview/single-zone.png)

* **每个层中跨可用性集部署的 VM**

     ![跨区域可用性集](media/move-vm-overview/crossregionaset.png)

* **每个层中跨可用性区域部署的 VM**

     ![跨可用性区域的 VM 部署](media/move-vm-overview/azonecross.png)

## <a name="move-vms-to-increase-availability"></a>移动 VM 以提高可用性

* **跨不同的层部署的单一实例 VM**

     ![跨层的单一实例 VM 部署](media/move-vm-overview/single-zone.png)

* **每个层中跨可用性集部署的 VM**：使用 Azure Site Recovery 为 VM 启用复制时，可将可用性集中的 VM 配置到独立的可用性区域。 完成移动操作后，可用性 SLA 将达到 99.9%。

     ![跨可用性集和可用性区域的 VM 部署](media/move-vm-overview/aset-azone.png)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> 
> * [将 Azure VM 移到另一区域](azure-to-azure-tutorial-migrate.md)
> 
> * [将 Azure VM 移到可用性区域中](move-azure-vms-avset-azone.md)

