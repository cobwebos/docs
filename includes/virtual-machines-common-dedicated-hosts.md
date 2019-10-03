---
title: include 文件
description: include 文件
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 07/26/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: d27b3613acb2980ff4116825197d018f9c183baa
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2019
ms.locfileid: "71266851"
---
## <a name="benefits"></a>优点 

保留整个主机具有以下优势：

-   物理服务器级别的硬件隔离。 不会在主机上放置任何其他 Vm。 专用主机部署在相同的数据中心内，与其他非独立主机主机共享相同的网络和底层存储基础结构。
-   控制由 Azure 平台启动的维护事件。 尽管大部分维护事件对虚拟机的影响不大，但在某些情况下，暂停的每秒都会产生影响。 使用专用主机时，可以选择维护时段，以降低对服务的影响。
-   利用 Azure 混合权益，你可以将自己的 Windows 和 SQL 许可证带到 Azure。 使用混合权益可提供额外的好处。 有关详细信息，请参阅[Azure 混合权益](https://azure.microsoft.com/pricing/hybrid-benefit/)。



## <a name="groups-hosts-and-vms"></a>组、主机和 Vm  

![查看专用主机的新资源。](./media/virtual-machines-common-dedicated-hosts/dedicated-hosts2.png)

**主机组**是代表专用主机集合的资源。 在区域和可用性区域中创建主机组，并向其添加主机。

**主机**是映射到 Azure 数据中心中的物理服务器的资源。 创建主机时，会分配物理服务器。 主机是在主机组中创建的。 主机提供一个 SKU，用于描述可以创建的 VM 大小。 每个主机可以托管多个不同大小的 Vm，前提是它们来自相同的大小系列。

在 Azure 中创建 VM 时，可以选择要用于 VM 的专用主机。 你可以完全控制哪些 Vm 放置在主机上。


## <a name="high-availability-considerations"></a>高可用性注意事项 

为实现高可用性，应该部署多个 Vm，并将其分布到多个主机（最少2个）。 使用 Azure 专用主机时，可以使用多个选项来预配基础结构，从而形成故障隔离边界。

### <a name="use-availability-zones-for-fault-isolation"></a>使用可用性区域进行故障隔离

可用性区域是 Azure 区域内的唯一物理位置。 每个区域由一个或多个数据中心组成，这些数据中心配置了独立电源、冷却和网络。 在单个可用性区域中创建主机组。 创建后，所有主机都将放置在该区域中。 要实现跨区域的高可用性，需要创建多个主机组（每个区域一个），并相应地分散主机。

如果向可用性区域分配主机组，则必须在同一区域中创建在该主机上创建的所有 Vm。

### <a name="use-fault-domains-for-fault-isolation"></a>使用容错域实现故障隔离

可以在特定的容错域中创建主机。 与规模集或可用性集中的 VM 一样，不同容错域中的主机将放置在数据中心的不同物理机架上。 创建主机组时，需要指定容错域计数。 在主机组中创建主机时，会为每个主机分配容错域。 Vm 不需要任何容错域分配。

容错域不同于归置。 两个主机具有相同的容错域并不意味着它们彼此接近。

容错域的作用域限定为主机组。 不应在两个主机组之间进行反相关性假设，除非它们在不同的可用性区域中。

部署到具有不同容错域的主机的 Vm 将在多个存储戳记上具有其基本托管磁盘服务，以提高故障隔离保护。

### <a name="using-availability-zones-and-fault-domains"></a>使用可用性区域和容错域

可以同时使用这两项功能来实现更好的故障隔离。 在这种情况下，你将在中为每个主机组指定可用性区域和容错域计数，将容错域分配给组中的每个主机，并为每个 Vm 分配一个可用性区域

[此处](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)提供的资源管理器示例模板使用区域和容错域传播主机以实现区域中的最大复原能力。

## <a name="maintenance-control"></a>维护控制

有时可能会更新支持虚拟机的基础结构，以提高可靠性、性能和安全性，并启动新功能。 Azure 平台会尽可能最大程度地减少平台维护的影响，但具有*维护敏感*工作负荷的客户不能容忍需要冻结或断开 VM 维护的时间。

**维护控制**向客户提供一个选项，用于跳过在其专用主机上计划的定期平台更新，并在35天滚动窗口中进行选择时应用。

> [!NOTE]
>  维护控制目前处于有限的预览阶段，需要载入过程。 提交[提名调查](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR6lJf7DwiQxNmz51ksQvxV9UNUM3UllWUjBMTFZQUFhHUDI0VTBPQlJFNS4u)，申请此预览版。

## <a name="capacity-considerations"></a>容量注意事项

预配专用主机之后，Azure 会将其分配给物理服务器。 当你需要预配 VM 时，这可保证容量的可用性。 Azure 使用区域（或区域）中的全部容量为主机选择物理服务器。 这也意味着，客户可以在不考虑群集中用尽空间的情况下，增加其专用主机占用量。

## <a name="quotas"></a>配额

对于专用主机，每个区域的默认配额限制为3000个 vcpu。 但对于主机使用的 VM 大小系列的配额，还会限制可以部署的主机数。 例如，"即**用即付**" 订阅在 "美国东部" 区域中只能有10个个 vcpu 的配额，适用于 Dsv3 大小系列。 在这种情况下，你需要将配额增加到至少64个 vcpu，然后才能部署专用主机。 如果需要，请选择右上角的 "**请求增加**" 按钮以进行请求。

![门户中的 "使用情况和配额" 页的屏幕截图](./media/virtual-machines-common-dedicated-hosts/quotas.png)

有关详细信息，请参阅[Virtual Machine vCPU 配额](/azure/virtual-machines/windows/quotas)。

## <a name="pricing"></a>定价

无论部署多少个 Vm，都按专用主机对用户收费。 在你的月度语句中，你将看到新的可计费资源类型。 专用主机上的 Vm 仍会显示在你的声明中，但将具有0价格。

主机价格根据 VM 系列、类型（硬件大小）和区域设置。 主机价格相对于主机支持的最大 VM 大小。

软件许可、存储和网络使用情况与主机和 Vm 分开计费。 不会更改这些可计费项。

有关详细信息，请参阅[Azure 专用主机定价](https://aka.ms/ADHPricing)。
 
## <a name="vm-families-and-hardware-generations"></a>VM 系列和硬件代

SKU 是为主机定义的，它表示 VM 大小系列和类型。 可以在单个主机内混合使用不同大小的多个 Vm，只要它们属于相同的大小系列。 该类型是当前在该区域中可用的硬件生成。

同一`types` VM 系列的不同之处是来自不同的 cpu 供应商，并且具有不同的 cpu 生成和核心数。

若要了解详细信息，请参阅 "主机[定价" 页](https://aka.ms/ADHPricing)。

在预览期间，我们将支持以下主机 SKU\types：DSv3_Type1 和 ESv3_Type1

 
## <a name="host-life-cycle"></a>主机生命周期


Azure 监视和管理主机的运行状况状态。 查询主机时，将返回以下状态：

| 正常运行状态   | 描述       |
|----------|----------------|
| 主机可用     | 主机没有已知问题。   |
| 正在调查的主机  | 我们正在寻找的主机遇到一些问题。 这是 Azure 尝试并确定确定的问题的范围和根本原因所需的过渡状态。 主机上运行的虚拟机可能会受到影响。 |
| 主机挂起解除分配   | Azure 无法将主机恢复回正常状态，要求你将虚拟机从此主机中重新部署。 如果`autoReplaceOnFailure`启用，则虚拟机将*服务修复*到正常运行的硬件。 否则，虚拟机可能在即将失败的主机上运行。|
| 已解除分配主机  | 所有虚拟机都已从主机中删除。 由于硬件是从轮换中取出的，因此不再向此主机收费。   |

