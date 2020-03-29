---
title: include 文件
description: include 文件
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/04/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 4860dcac666f790fed199536338e50a967113c20
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "76748914"
---
本文概述了 Azure 虚拟机 (VM) 的可用性功能。

## <a name="high-availability"></a>高可用性

工作负载通常分布在不同的虚拟机中，以获得高吞吐量、高性能，并在 VM 因更新或其他事件而受到影响时创建冗余。 

Azure 提供的选项很少，以实现高可用性。 首先，让我们来谈谈基本构造。 

### <a name="availability-zones"></a>可用性区域

[可用性区域](../articles/availability-zones/az-overview.md)扩展了保持 VM 上应用程序和数据的可用性所必须的控制级别。 可用性区域是 Azure 区域内的物理独立区域。 每个受支持的 Azure 区域有三个可用性区域。 

每个可用性区域有独立的电源、网络和散热设备。 通过将解决方案构建为使用区域中复制的 VM，可以在数据中心服务中断时保护应用和数据。 如果一个区域发生故障，另一个区域会立即提供复制的应用和数据。 

![可用性区域](./media/virtual-machines-common-regions-and-availability/three-zones-per-region.png)

详细了解如何在可用性区域中部署 [Windows](../articles/virtual-machines/windows/create-powershell-availability-zone.md) 或 [Linux](../articles/virtual-machines/linux/create-cli-availability-zone.md) VM。


### <a name="fault-domains"></a>容错域

容错域是共享公用电源和网络交换机的基础硬件逻辑组，类似于本地数据中心内的机架。 

### <a name="update-domains"></a>更新域

更新域是可以同时维护或重新启动的基础硬件逻辑组。 

Azure 平台进行定期维护时，此方法可确保至少有一个应用程序实例始终保持运行状态。 在维护期间，正在重新启动的更新域的顺序可能不会按顺序进行，但一次只重新启动一个更新域。


## <a name="virtual-machines-scale-sets"></a>虚拟机规模集 

Azure 虚拟机扩展集允许您创建和管理一组负载平衡 VM。 可以根据需求或定义的计划自动增减 VM 实例的数目。 缩放集为应用程序提供高可用性，并允许您集中管理、配置和更新许多 VM。 我们建议在规模集中创建两个或多个 VM，以提供高可用性的应用程序并满足[99.95% Azure SLA。](https://azure.microsoft.com/support/legal/sla/virtual-machines/) 规模集本身无需支付任何费用，只需为创建的每个 VM 实例付费。 当单个 VM 使用 [Azure 高级 SSD](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#premium-ssd) 时，Azure SLA 适用于计划外维护事件。 规模集中的虚拟机可以跨多个更新域和容错域部署，以最大限度地提高可用性和抵御数据中心中断以及计划内或计划外维护事件造成的中断的能力。 规模集中的虚拟机也可以部署到单个可用性区域或区域。 可用性区域部署选项可能因业务流程模式而异。

### <a name="preview-orchestration-mode-preview"></a>预览：业务流程模式预览
虚拟机缩放集允许您指定业务流程模式。  使用虚拟机缩放集业务流程模式（预览），现在可以选择规模集是否应协调在规模集配置模型之外显式创建的虚拟机，还是隐式创建的虚拟机实例基于配置模型。 选择 VM 业务流程模型允许您在区域或可用性区域中将显式定义的虚拟机分组在一起的业务流程模式。 部署在可用性区域中的虚拟机向 VM 提供区域隔离，因为它们绑定到可用性区域边界，并且不会受到该区域其他可用性区域中可能发生的任何故障的影响。 

|   | "编排模式"："虚拟机"（虚拟机）| "编排模式"："缩放集VM"（虚拟机缩放设置VM） |
|----|----|----|
| VM 配置模型| 无。 虚拟机配置文件在比例集模型中未定义。 | 必需。 虚拟机配置文件填充在比例集模型中。 |
| 将新 VM 添加到规模集| 创建 VM 时，会显式将 VM 添加到规模集。 | VM 会根据 VM 配置模型、实例计数和自动缩放规则隐式创建并添加到缩放集。 |
| 可用性区域| 在一个可用性区域中支持区域部署或 VM| 支持区域部署或多个可用区域;可以定义区域平衡策略 |
| 容错域| 可以定义容错域计数。 2 或 3 基于区域支持，5 个用于可用区域。 分配的 VM 容错域将保留 VM 生命周期，包括取消分配和重新启动。 | 可以为非区域部署定义 1、2 或 3 个容错域，为可用性区域部署定义 5 个容错域。 分配的 VM 故障域不会保留 VM 生命周期，虚拟机在分配时分配了一个容错域。 |
| 更新域| 不适用。 更新域会自动映射到容错域| 不适用。 更新域会自动映射到容错域 |

**故障域和更新域**

虚拟机规模集通过对齐容错域和更新域来简化高可用性设计。 您只需要为比例集定义容错域计数。 缩放集可用的容错域数可能因区域而异。 请参阅[管理 Azure 中的虚拟机的可用性](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability)。


## <a name="availability-sets"></a>可用性集
可用性集是数据中心内的 VM 的逻辑分组，可让 Azure 了解应用程序的构建方式，以便提供冗余和可用性。 建议在可用性集内创建两个或多个 VM，提供高度可用的应用程序，并满足 [99.95% Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) 的要求。 可用性集本身是免费的，只需为创建的每个 VM 实例付费。 当单个 VM 使用 [Azure 高级 SSD](../articles/virtual-machines/windows/disks-types.md#premium-ssd) 时，Azure SLA 适用于计划外维护事件。

在可用性集中，VM 会自动分布在这些容错域中。 此方法可限制潜在物理硬件故障、网络中断或断电的影响。

对于使用 [Azure 托管磁盘](../articles/virtual-machines/windows/faq-for-disks.md)的 VM，在使用托管可用性集时，VM 与托管磁盘容错域一致。 该一致性可确保附加到 VM 的所有托管磁盘都在同一托管磁盘容错域内。 

在托管可用性集中，只能创建带托管磁盘的 VM。 托管磁盘容错域的数目因区域而异 - 每个区域两个或三个托管磁盘容错域。 可以阅读有关这些适用于 [Linux VM](../articles/virtual-machines/linux/manage-availability.md?#use-managed-disks-for-vms-in-an-availability-set) 或 [Windows VM](../articles/virtual-machines/windows/manage-availability.md?#use-managed-disks-for-vms-in-an-availability-set) 的托管磁盘容错域的详细信息。

![托管可用性集](./media/virtual-machines-common-manage-availability/md-fd-updated.png)


可用性集中的 VM 也会在更新域之间自动分发。 

![可用性集](./media/virtual-machines-common-manage-availability/ud-fd-configuration.png)

## <a name="next-steps"></a>后续步骤
现在即可开始使用这些可用性和冗余功能构建 Azure 环境。 有关最佳实践的信息，请参阅 [Azure 可用性的最佳实践](/azure/architecture/checklist/resiliency-per-service)。

