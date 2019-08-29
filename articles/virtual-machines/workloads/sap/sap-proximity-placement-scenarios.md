---
title: 适用于 SAP 应用程序的最佳网络延迟的 Azure 邻近位置组 |Microsoft Docs
description: 介绍 Azure 邻近布局组的 SAP 部署方案
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/15/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 02dcb7174dd9cb2926ef2fafda4b521b939ae68a
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70077989"
---
# <a name="azure-proximity-placement-groups-for-optimal-network-latency-with-sap-applications"></a>用于最大程度地降低 SAP 应用程序网络延迟的 Azure 邻近放置组
基于 SAP NetWeaver 或 SAP S/4HANA 体系结构的 SAP 应用程序对于 SAP 应用程序层和 SAP 数据库层之间的网络延迟很敏感。 这种体系结构的敏感性原因在于, 大多数业务逻辑都是在应用程序层中执行的。 由于执行业务逻辑, SAP 应用程序层以高频率 (每秒几千个) 向数据库层发出查询。 在大多数情况下, 这些查询的性质很简单。 通常, 可以在500或更小的时间内在数据库层上执行。 网络上花费的时间将此类查询从应用程序层发送到数据库层, 从数据库层接收结果集对执行业务流程所需的时间有重大影响。 网络延迟的这一敏感度是需要在 SAP 部署项目中花费时间来实现最佳网络延迟。 [SAP 说明 #1100926-常见问题解答:网络性能](https://launchpad.support.sap.com/#/notes/1100926/E), SAP 发布了如何分类网络延迟的一些准则。

在一侧, 许多 Azure 区域中的数据中心增长, 也会通过引入可用性区域来触发。 另一方面, 客户 (尤其是对于高端 SAP 系统) 使用 M 系列系列或 HANA 大型实例中的更特殊的 VM Sku。 在特定 Azure 区域中的所有数据中心内都不存在的 Azure 虚拟机类型。 由于这两个倾向, 客户遇到了这样的情况: 网络延迟未处于最佳范围内, 在某些情况下, 会导致其 SAP 系统的性能不佳。

为了防止出现这种问题, Azure 提供了称为[Azure 邻近度放置组](https://docs.microsoft.com/azure/virtual-machines/linux/co-location)的构造。 这一新功能已用于部署各种不同的 SAP 系统。 查看引用的有关邻近性放置组限制的文章。 本文介绍可以使用或应使用 Azure 邻近性放置组的不同 SAP 方案。

## <a name="what-are-proximity-placement-groups"></a>什么是邻近性放置组 
Azure 邻近性放置组是一种逻辑构造, 在定义阶段, 它绑定到 Azure 区域和 Azure 资源组。 在 Vm 的部署过程中, 将引用邻近的放置组:

- 要在数据中心上进行结算的第一个部署的 Azure VM。 第一个虚拟机可被视为定位点 VM, 该 VM 基于 Azure 分配算法部署在数据中心, 并最终与特定 Azure 可用性区域的用户定义结合使用。
- 在部署了引用邻近位置组的所有后续 Vm 之后, 会将所有后续部署的 Azure Vm 放置在第一个虚拟机所在的数据中心内。

> [!NOTE]
> 如果没有部署可在第一个 VM 所在的数据中心内运行特定 VM 类型的主机硬件, 则所需的 VM 类型的部署将不会成功, 并且将以失败消息结束。 这种情况可能是这样的: 更多非主流 Vm (如具有 Gpu 的虚拟机或 HPC VM 类型) 应围绕, 例如已部署为第一个 VM 类型的 M 系列 VM

单个[Azure 资源组](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal)可以有多个分配给其自身的邻近位置组。 但是, 一个邻近的放置组只能分配给一个 Azure 资源组。

使用邻近位置组时, 应注意以下事项:

- 当你为 SAP 系统实现最佳性能, 并通过使用邻近位置组将其限制为针对此系统的单个 Azure 数据中心时, 你可能无法在此类邻近放置组中合并所有类型的 VM 系列。 原因在于, 需要独占运行特定 VM 类型的某些主机硬件可能不存在于已部署放置组的 "锚式 VM" 的数据中心。
- 在此类 SAP 系统的生命周期中, 您可以强制将系统移到另一个数据中心。 在您决定横向扩展 HANA DBMS 层的情况下, 可以强制执行此类移动, 例如, 从四个节点移动到16个节点。 但是没有足够的容量来获取已在同一个数据中心内使用的类型的额外12个虚拟机。
- 由于硬件退役, Microsoft 可能会为你在另一个数据中心中使用的 VM 类型而不是同一数据中心构建容量。 出现这种情况意味着可以将邻近位置组的所有 Vm 移到另一个数据中心。


## <a name="azure-proximity-placement-groups-with-sap-systems-using-azure-vms-exclusively"></a>仅限使用 Azure Vm 的 SAP 系统的 Azure 邻近性放置组
在 Azure 上部署 SAP NetWeaver 和 S/4HANA 系统的多数情况下, 不使用[HANA 大型实例](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)。 对于这类系统的部署, 必须在 SAP 应用程序层和 DBMS 层之间提供最佳性能。 为此, 只需为此系统定义 Azure 邻近性放置组。 在大多数客户部署中, 客户选择为 SAP 系统构建单个[Azure 资源组](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal)。 在这种情况下, 可能会有1:1 关系, 例如, 生产 ERP 系统资源组及其邻近位置组。 在其他一些部署情况下, 客户会在水平方向上组织其资源组, 并在单个资源组中收集所有生产系统。 在这种情况下, 在生产 SAP 系统的资源组与生产 SAP ERP、SAP BW 等的多个邻近位置组之间, 会有1到很多的关系。应该避免将多个甚至所有 SAP 生产或非生产系统捆绑到单个邻近的放置组。 在例外情况下, 少量 SAP 系统或 SAP 系统以及某些应用程序需要较低的网络通信, 则可以考虑将这些系统移到一个邻近的放置组。 此类建议的原因是, 在邻近性放置组中分组的系统越多, 机会就越大:

- 需要的 VM 类型不能在邻近位置组定位的特定数据中心内运行。
- 某些非主流 Vm (如 M 系列) 的资源最终无法再完成, 因为你需要在一段时间内将附加软件添加到现有的邻近放置组。

所述的理想用法如下所示:

![所有 Azure Vm 的邻近 placemen 组](./media/sap-proximity-placement-scenarios/ppg-for-all-azure-vms.png)

在这种情况下, 单个 SAP 系统在一个资源组中分组, 每个资源组都有一个邻近的放置组。 无论你使用 HANA 横向扩展配置还是 DBMS 扩展配置, 都不会有任何依赖关系。


## <a name="azure-proximity-placement-groups-and-hana-large-instances"></a>Azure 邻近性组和 HANA 大型实例
对于某些 SAP 系统依赖于[HANA 大型实例](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)作为应用程序层的情况, 当使用已在使用中的 Hana 大型实例单元时, 可以在 Hana 大型实例单元和 Azure vm 之间进行严重改进。部署在[修订版本4行或标记](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance)中。 其中一项改进是, HANA 大型实例单元部署后, 会获得已部署的邻近位置组。 你可以使用该邻近布局组来部署你的应用程序层虚拟机。 因此, 这些 Vm 将部署在托管 HANA 大型实例单元的同一数据中心内。

若要检测到 HANA 大型实例单元是否部署在修订版本4标记或行中, 请[通过 Azure 门户查看 AZURE HANA 大型实例](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-li-portal#look-at-attributes-of-single-hli-unit)的文章。 在 HANA 大型实例单元的特性概述中, 还可以找到邻近位置组的名称, 因为它是在部署时为 HANA 大型实例单元创建的。 "属性概述" 中显示的名称是邻近感应位置组的名称, 你应该使用将应用程序层 Vm 部署到中。

与仅使用 Azure 虚拟机的 SAP 系统相反, 在使用 HANA 大型实例时, 应考虑使用多少个[azure 资源组](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal)。 [Hana 大型实例租户](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-know-terms)的所有 Hana 大型实例单元按[此处](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-li-portal#display-of-hana-large-instance-units-in-the-azure-portal)所述分组到单个 Azure 资源组中。 除非你希望将部署到不同的租户, 例如生产环境和非生产系统或特定系统, 否则, 所有 HANA 大型实例单元将部署在一个 HANA 大型实例租户中, 后者再次与 Azure 进行了1:1 关系资源组。 而所有单个单位都将定义单独的邻近感应位置组。 

因此, 单个租户的 Azure 资源组和邻近位置组之间的分组如下所示:

![所有 Azure Vm 的邻近布局组](./media/sap-proximity-placement-scenarios/ppg-for-hana-large-instance-units.png)


## <a name="short-example-of-deploying-with-azure-proximity-placement-groups"></a>部署 Azure 邻近位置组的简短示例
若要演示如何使用 Azure 邻近布局组来部署 VM, 请参阅下面的 PowerShell 命令列表, 该列表演示了如何使用 Azure 邻近布局组进行第一小操作

使用[Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/)登录后的第一步是检查是否在正确的 Azure 订阅中, 以使用命令进行部署:

<pre><code>
Get-AzureRmContext
</code></pre>

如果需要更改为其他订阅, 可以通过执行以下命令来执行此操作:

<pre><code>
Set-AzureRmContext -Subscription "my PPG test subscription"
</code></pre>

第三步是使用此命令创建新的 Azure 资源组:

<pre><code>
New-AzResourceGroup -Name "myfirstppgexercise" -Location "westus2"
</code></pre>

现在可以创建新的邻近性放置组, 如下所示:

<pre><code>
New-AzProximityPlacementGroup -ResourceGroupName "myfirstppgexercise" -Name "letsgetclose" -Location "westus2"
</code></pre>

你现在可以开始使用类似以下的命令将第一个 VM 部署到此邻近位置组:

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppganchorvm" -Location "westus2" -OpenPorts 80,3389 -ProximityPlacementGroup "letsgetclose" -Size "Standard_DS11_v2"
</code></pre>

使用上述命令, 可部署基于 Windows 的 VM。 此 VM 部署成功后, 将在 Azure 区域中定义邻近感应位置组的数据中心作用域。 引用邻近位置组的所有后续 VM 部署都将部署在同一 Azure 数据中心, 前提是 VM 类型可以托管在该数据中心内的硬件上, 并且/或该 VM 类型的容量可用。

## <a name="combine-availability-sets-and-availability-zones-with-proximity-placement-groups"></a>将可用性集和可用性区域与邻近组相结合 
使用可用性区域进行 SAP 系统部署, 缺点之一是不能使用特定区域内的可用性集来控制 SAP 应用程序层的部署。 由于你想要将 SAP 应用程序层部署在 DBMS 层所在的同一区域中, 并在部署单个 VM 时引用可用性区域和可用性集, 因此强制你通过引用区域来部署应用程序层因此, 它将丧失确保应用程序层 Vm 在不同的更新域和故障域间分散的功能。 在邻近位置组的帮助下, 你可以克服此限制。 部署顺序如下所示:

- 创建邻近放置组
- 通过引用特定的 Azure 可用性区域, 部署 "锚式 VM", 通常为 DBMS 服务器
- 创建引用 Azure 邻近性组的可用性集 (请参阅下文)
- 通过引用可用性集和邻近度放置组来部署应用程序层 Vm

部署 VM 时, 请参考 Azure 可用性区域和邻近位置组, 而不是在上面所示的情况下部署第一个 VM, 如下所示:

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppganchorvm" -Location "westus2" -OpenPorts 80,3389 -Zone "1" -ProximityPlacementGroup "letsgetclose" -Size "Standard_E16_v3"
</code></pre>

成功部署此虚拟机, 它将在一个 Azure 可用性区域中托管 SAP 系统的数据库实例, 将邻近位置组的作用域固定到表示所定义可用性区域的数据中心之一.

我们假设你以与 DBMs Vm 相同的方式部署中心服务 Vm, 方法是引用 DBMS Vm 和相同邻近位置组的相同区域。 在下一步中, 需要创建要用于 SAP 系统的应用程序层的可用性集。
需要定义和创建邻近感应的放置组。 用于创建可用性集的命令需要对邻近位置组 ID (不是名称) 的其他引用。 可以通过以下方式获取邻近组的 "邻近" 放置组的 ID:



<pre><code>
Get-AzProximityPlacementGroup -ResourceGroupName "myfirstppgexercise" -Name "letsgetclose"
</code></pre>

当你创建可用性集时, 你需要在使用托管磁盘时考虑其他参数 (默认情况下, 除非另有指定) 和邻近位置组:

<pre><code>
New-AzAvailabilitySet -ResourceGroupName "myfirstppgexercise" -Name "myppgavset" -Location "westus2" -ProximityPlacementGroupId "/subscriptions/my very long ppg id string" -sku "aligned" -PlatformUpdateDomainCount 3 -PlatformFaultDomainCount 2 
</code></pre>

理想情况下, 应使用三个容错域。 但是, 受支持的容错域的数量可能会因区域而异。 在这种情况下, 特定区域可能的最大容错域计数为2。 若要部署应用程序层 Vm, 需要添加对可用性集名称和邻近组名称的引用, 如下所示:

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppgavsetappvm" -Location "westus2" -OpenPorts 80,3389 -AvailabilitySetName "myppgavset" -ProximityPlacementGroup "letsgetclose" -Size "Standard_DS11_v2"
</code></pre>

此序列的最终结果将是你的 SAP 系统的 DBMS 层和中心服务, 位于特定的可用性区域和 SAP 应用程序层中, 而 SAP 应用程序层位于与 DBMS VM 相同的 Azure 数据中心内的可用性集部署.

> [!NOTE]
> 将一个 DBMS VM 部署到一个区域, 并将第二个 DBMS VM 部署到另一个区域以创建高可用性配置时, 你将需要为每个区域提供不同的邻近位置组。 对于你可能使用的可用性集, 相同的 si 为 true

## <a name="get-an-existing-system-into-azure-proximity-placement-groups"></a>将现有系统纳入 Azure 邻近度放置组
在已部署 SAP 系统的情况下, 你可能希望优化某些关键系统的网络延迟, 并在同一数据中心内找到应用程序层和 DBMS 层。 在 "邻近性放置组" 功能的公共预览阶段, 需要删除 Vm 和新的 Vm 创建, 才能执行此类迁移到邻近的放置组。 在此功能的这一阶段, 无需关闭 Vm 即可将这些关闭虚拟机分配到邻近位置组。


## <a name="next-steps"></a>后续步骤
查阅文档：

- [Azure 规划和部署清单上的 SAP 工作负荷](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist)
- [预览：使用 Azure CLI 将 Vm 部署到邻近位置组](https://docs.microsoft.com/azure/virtual-machines/linux/proximity-placement-groups)
- [预览：使用 PowerShell 将 Vm 部署到邻近位置组](https://docs.microsoft.com/azure/virtual-machines/windows/proximity-placement-groups)
- [部署适用于 SAP 工作负荷的 Azure 虚拟机 DBMS 的注意事项](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)

