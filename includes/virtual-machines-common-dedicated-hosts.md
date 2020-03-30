---
title: include 文件
description: include 文件
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 03/10/2020
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 2daaf9bbdf90029f0aad4333ab94e2d1d1d3d7ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79129383"
---
## <a name="limitations"></a>限制

- 专用主机当前不支持虚拟机缩放集。

## <a name="benefits"></a>优点 

保留整个主机具有以下好处：

-   物理服务器级别的硬件隔离。 主机上不会放置其他 VM。 专用主机部署在同一数据中心，并与其他非隔离主机共享相同的网络和基础存储基础结构。
-   控制由 Azure 平台启动的维护事件。 虽然大多数维护事件对虚拟机的影响很小或没有影响，但有些敏感的工作负载会影响每一秒的暂停。 使用专用主机，您可以选择加入维护窗口，以减少对服务的影响。
-   借助 Azure 混合权益，您可以将自己的 Windows 和 SQL 许可证带到 Azure。 使用混合权益可为您提供其他好处。 有关详细信息，请参阅[Azure 混合权益](https://azure.microsoft.com/pricing/hybrid-benefit/)。


## <a name="groups-hosts-and-vms"></a>组、主机和 VM  

![查看专用主机的新资源。](./media/virtual-machines-common-dedicated-hosts/dedicated-hosts2.png)

**主机组**是表示专用主机集合的资源。 在区域和可用性区域中创建主机组，并将主机添加到其中。

**主机**是一种资源，映射到 Azure 数据中心中的物理服务器。 创建主机时分配物理服务器。 在主机组中创建主机。 主机具有 SKU，用于描述可以创建哪些 VM 大小。 每个主机可以承载多个不同大小的 VM，只要它们来自同一大小系列。

在 Azure 中创建 VM 时，可以选择用于 VM 的专用主机。 您可以完全控制将哪些 VM 放置在主机上。


## <a name="high-availability-considerations"></a>高可用性注意事项 

为获得高可用性，应部署多个 VM，分布在多个主机（至少 2 个）。 使用 Azure 专用主机，您可以预配基础结构以形成故障隔离边界。

### <a name="use-availability-zones-for-fault-isolation"></a>使用可用性区域进行故障隔离

可用性区域是 Azure 区域中唯一的物理位置。 每个区域由一个或多个数据中心组成，这些数据中心配置了独立电源、冷却和网络。 在单个可用性区域中创建主机组。 创建后，所有主机都将放置在该区域内。 要跨区域实现高可用性，您需要创建多个主机组（每个区域一个），并相应地扩展主机。

如果将主机组分配给可用性区域，则必须在同一区域中创建在该主机上创建的所有 VM。

### <a name="use-fault-domains-for-fault-isolation"></a>使用容错域进行故障隔离

可以在特定的容错域中创建主机。 与规模集或可用性集中的 VM 一样，不同容错域中的主机将放置在数据中心的不同物理机架上。 创建主机组时，需要指定容错域计数。 在主机组中创建主机时，会为每个主机分配容错域。 VM 不需要任何容错域分配。

容错域与配置不同。 两个主机具有相同的容错域并不意味着它们彼此接近。

容错域的范围为主机组。 不应对两个主机组之间的反关联进行任何假设（除非它们位于不同的可用性区域）。

部署到具有不同容错域的主机的 VM 将在多个存储戳上具有其基础托管磁盘服务，以提高故障隔离保护。

### <a name="using-availability-zones-and-fault-domains"></a>使用可用性区域和容错域

可以同时使用这两种功能来实现更多的故障隔离。 在这种情况下，您将为每个主机组指定可用性区和容错域计数，将容错域分配给组中的每个主机，并为每个 VM 分配一个可用性区域

[此处](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)找到的资源管理器示例模板使用区域和容错域来扩展主机，以实现区域中的最大恢复能力。

## <a name="maintenance-control"></a>维护控制

支持虚拟机的基础结构可能会偶尔更新，以提高可靠性、性能、安全性并启动新功能。 Azure 平台尝试尽可能将平台维护的影响降至最低，但具有*维护敏感*工作负荷的客户甚至无法容忍 VM 需要冻结或断开连接进行维护的几秒钟。

**维护控制**为客户提供一个选项，可以跳过计划在其专用主机上安排的常规平台更新，然后在他们选择的时间在 35 天的滚动窗口中应用它。

> [!NOTE]
>  维护控制当前处于公共预览版中。 有关详细信息，请参阅使用**[CLI](https://docs.microsoft.com/azure/virtual-machines/maintenance-control-cli?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json)或[PowerShell](https://docs.microsoft.com/azure/virtual-machines/maintenance-control-powershell?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json)使用维护控制的控制更新**。

## <a name="capacity-considerations"></a>容量注意事项

预配专用主机后，Azure 会将其分配给物理服务器。 这保证了在需要预配 VM 时容量的可用性。 Azure 使用区域（或区域）中的整个容量为主机选择物理服务器。 这也意味着客户能够扩展其专用主机占用空间，而不必担心群集中空间不足。

## <a name="quotas"></a>配额

每个区域的专用主机的默认配额限制为 3000 vCPU。 但是，可以部署的主机数也受到用于主机的 VM 大小系列的配额的限制。 例如，在美国东部区域，即**用即付**订阅可能只有 10 个 vCPU 的配额可用于 Dsv3 大小系列。 在这种情况下，您需要请求将配额增加到至少 64 个 vCPU，然后才能部署专用主机。 选择右上角的请求**增加**按钮，根据需要提交请求。

![门户中使用和配额页的屏幕截图](./media/virtual-machines-common-dedicated-hosts/quotas.png)

有关详细信息，请参阅虚拟机[vCPU 配额](/azure/virtual-machines/windows/quotas)。

免费试用和 MSDN 订阅没有 Azure 专用主机的配额。

## <a name="pricing"></a>定价

无论部署的 VM 数是多少，用户都会按专用主机付费。 在每月对帐单中，您将看到新的计费资源类型的主机。 专用主机上的 VM 仍将显示在您的语句中，但价格为 0。

主机价格基于 VM 系列、类型（硬件大小）和地区设置。 主机价格相对于主机上支持的最大 VM 大小相关。

软件许可、存储和网络使用情况与主机和 VM 分开计费。 这些可计费项目没有变化。

有关详细信息，请参阅[Azure 专用主机定价](https://aka.ms/ADHPricing)。

您还可以使用[Azure 专用主机的预留实例](../articles/virtual-machines/prepay-dedicated-hosts-reserved-instances.md)来节省成本。
 
## <a name="sizes-and-hardware-generations"></a>尺寸和硬件代数

SKU 为主机定义，它表示 VM 大小系列和类型。 您可以在单个主机中混合多个不同大小的 VM，只要它们的大小相同。 

*类型*是硬件生成。 同一 VM 系列的不同硬件类型将来自不同的 CPU 供应商，并且具有不同的 CPU 代和内核数。 

大小和硬件类型因地区而异。 请参阅主机[定价页面](https://aka.ms/ADHPricing)以了解更多信息。


## <a name="host-life-cycle"></a>主机生命周期


Azure 监视和管理主机的运行状况。 查询主机时，将返回以下状态：

| 运行状况状态   | 描述       |
|----------|----------------|
| 主机可用     | 主机没有已知问题。   |
| 主机正在调查中  | 我们正在调查的主机有一些问题。 这是 Azure 尝试确定所识别问题的范围和根本原因所需的过渡状态。 在主机上运行的虚拟机可能会受到影响。 |
| 主机挂起的取消分配   | Azure 无法将主机还原到正常状态，并要求您将虚拟机从此主机中重新部署。 如果`autoReplaceOnFailure`启用，您的虚拟机将*修复为*健康的硬件。 否则，虚拟机可能在即将发生故障的主机上运行。|
| 主机交易  | 所有虚拟机都已从主机中删除。 由于硬件已退出旋转，因此不再为此主机向您收费。   |

