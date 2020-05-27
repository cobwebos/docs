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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "76748914"
---
本文概述了 Azure 虚拟机 (VM) 的可用性功能。

## <a name="high-availability"></a>高可用性

工作负荷通常分布在不同的虚拟机上，以获得高吞吐量、高性能并实现冗余，防止 VM 因更新或其他事件而受影响。 

Azure 提供了几个选项来实现高可用性。 首先，让我们谈一谈基本构造。 

### <a name="availability-zones"></a>可用性区域

[可用性区域](../articles/availability-zones/az-overview.md)扩展了在保持 VM 上应用程序和数据的可用性时的控制级别。 可用性区域是 Azure 区域中在物理上独立的区域。 每个受支持的 Azure 区域有三个可用性区域。 

每个可用性区域有独立的电源、网络和散热设备。 通过将解决方案构建为使用区域中复制的 VM，可以在数据中心服务中断时保护应用和数据。 如果一个区域发生故障，另一个区域会立即提供复制的应用和数据。 

![可用性区域](./media/virtual-machines-common-regions-and-availability/three-zones-per-region.png)

详细了解如何在可用性区域中部署 [Windows](../articles/virtual-machines/windows/create-powershell-availability-zone.md) 或 [Linux](../articles/virtual-machines/linux/create-cli-availability-zone.md) VM。


### <a name="fault-domains"></a>容错域

容错域是共享公用电源和网络交换机的基础硬件逻辑组，类似于本地数据中心内的机架。 

### <a name="update-domains"></a>更新域

更新域是可以同时维护或重新启动的基础硬件逻辑组。 

Azure 平台进行定期维护时，此方法可确保至少有一个应用程序实例始终保持运行状态。 在维护期间，更新域的重启顺序可能不会按序进行，但一次只重启一个更新域。


## <a name="virtual-machines-scale-sets"></a>虚拟机规模集 

使用 Azure 虚拟机规模集可以创建并管理一组负载均衡的 VM。 可以根据需求或定义的计划自动增减 VM 实例的数目。 规模集为应用程序提供高可用性，用于集中管理、配置和更新许多 VM。 建议在一个规模集内创建两个或多个 VM，使应用程序高度可用，并满足 [99.95% Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) 的要求。 规模集本身是免费的，你只需为创建的每个 VM 实例付费。 当单个 VM 使用 [Azure 高级 SSD](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#premium-ssd) 时，Azure SLA 适用于计划外维护事件。 规模集内的虚拟机可以跨多个更新域和容错域部署，以最大程度地提高因数据中心中断、计划内或计划外维护事件而导致停机时的可用性和复原能力。 规模集内的虚拟机还可以部署到单个可用性区域或按地区进行部署。 可用性区域部署选项可能因业务流程模式而异。

### <a name="preview-orchestration-mode-preview"></a>预览版：业务流程模式预览版
虚拟机规模集允许指定业务流程模式。  利用虚拟机规模集业务流程模式（预览版），你现在可以选择是让规模集协调在规模集配置模型之外显式创建的虚拟机，还是协调基于配置模型隐式创建的虚拟机实例。 选择业务流程模式的标准是，你可以通过 VM 业务流程模型将多个显式定义的虚拟机组合到某个地区或可用性区域中。 将虚拟机部署在可用性区域中可为 VM 提供区域隔离，因为它们位于可用性区域边界内，不受该地区其他可用性区域中可能会发生的任何故障的影响。 

|   | “orchestrationMode”：“VM” (VirtualMachine)| “orchestrationMode”：“ScaleSetVM” (VirtualMachineScaleSetVM) |
|----|----|----|
| VM 配置模型| 无。 规模集模型中未定义 VirtualMachineProfile。 | 必需。 规模集模型中填充了 VirtualMachineProfile。 |
| 向规模集添加新 VM| 创建 VM 时，VM 会显式添加到规模集。 | 可以根据 VM 配置模型、实例计数和自动缩放规则隐式创建 VM 并将其添加到规模集。 |
| 可用性区域| 支持进行地区性部署，或者支持将 VM 置于一个可用性区域中| 支持地区性部署或多个可用性区域；可以定义区域均衡策略 |
| 容错域| 可以定义容错域计数。 该数目可以是 2 或 3，具体取决于地区性支持；也可以是 5，针对可用性区域。 所分配的 VM 容错域在整个 VM 生命周期中（包括解除分配后和重启后）都将保留。 | 可以为非区域性部署定义 1、2 或 3 个容错域，为可用性区域部署定义 5 个。 所分配的 VM 容错域在 VM 生命周期中不会保留，在分配时会为虚拟机分配一个容错域。 |
| 更新域| 不适用。 更新域自动映射到容错域| 不适用。 更新域自动映射到容错域 |

**容错域和更新域**

虚拟机规模集通过协调容错域和更新域简化了高可用性设计。 只需为规模集定义容错域计数。 规模集可用的容错域的数量可能因地区而异。 请参阅[管理 Azure 中的虚拟机的可用性](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability)。


## <a name="availability-sets"></a>可用性集
可用性集是数据中心内的 VM 的逻辑分组，可让 Azure 了解应用程序的构建方式，以便提供冗余和可用性。 建议在可用性集内创建两个或多个 VM，提供高度可用的应用程序，并满足 [99.95% Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) 的要求。 可用性集本身是免费的，只需为创建的每个 VM 实例付费。 当单个 VM 使用 [Azure 高级 SSD](../articles/virtual-machines/windows/disks-types.md#premium-ssd) 时，Azure SLA 适用于计划外维护事件。

在可用性集中，VM 自动分布到这些容错域中。 此方法可限制潜在物理硬件故障、网络中断或断电的影响。

对于使用 [Azure 托管磁盘](../articles/virtual-machines/windows/faq-for-disks.md)的 VM，在使用托管可用性集时，VM 与托管磁盘容错域一致。 该一致性可确保附加到 VM 的所有托管磁盘都在同一托管磁盘容错域内。 

在托管可用性集中，只能创建带托管磁盘的 VM。 托管磁盘容错域的数目因区域而异 - 每个区域两个或三个托管磁盘容错域。 可以阅读有关这些适用于 [Linux VM](../articles/virtual-machines/linux/manage-availability.md?#use-managed-disks-for-vms-in-an-availability-set) 或 [Windows VM](../articles/virtual-machines/windows/manage-availability.md?#use-managed-disks-for-vms-in-an-availability-set) 的托管磁盘容错域的详细信息。

![托管可用性集](./media/virtual-machines-common-manage-availability/md-fd-updated.png)


可用性集中的 VM 也会自动分布到更新域中。 

![可用性集](./media/virtual-machines-common-manage-availability/ud-fd-configuration.png)

## <a name="next-steps"></a>后续步骤
现在即可开始使用这些可用性和冗余功能构建 Azure 环境。 有关最佳实践的信息，请参阅 [Azure 可用性的最佳实践](/azure/architecture/checklist/resiliency-per-service)。

