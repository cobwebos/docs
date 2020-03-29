---
title: SAP 应用程序的 Azure 接近放置组 |微软文档
description: 使用 Azure 邻近放置组描述 SAP 部署方案
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
ms.date: 01/17/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 01ce1599f86082aef3ff53d298cc53896074af66
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76277597"
---
# <a name="azure-proximity-placement-groups-for-optimal-network-latency-with-sap-applications"></a>Azure 接近放置组，实现 SAP 应用程序的最佳网络延迟
基于 SAP NetWeaver 或 SAP S/4HANA 体系结构的 SAP 应用程序对 SAP 应用程序层和 SAP 数据库层之间的网络延迟非常敏感。 这种敏感性是应用程序层中运行的大多数业务逻辑的结果。 由于 SAP 应用程序层运行业务逻辑，因此以每秒数千或数万的速率以高频率向数据库层发出查询。 在大多数情况下，这些查询的性质很简单。 它们通常可以在 500 微秒或更短秒内在数据库层上运行。

在网络上花费的时间将此类查询从应用程序层发送到数据库层并接收结果集返回的时间对运行业务流程所需的时间有重大影响。 这种对网络延迟的敏感度是您需要在 SAP 部署项目中实现最佳网络延迟的原因。 有关如何对网络延迟进行分类的指南，请参阅[SAP 注释#1100926 - 常见问题解答：网络性能](https://launchpad.support.sap.com/#/notes/1100926/E)。

在许多 Azure 区域中，数据中心的数量有所增加。 这种增长也是由可用性区域的引入引起的。 同时，客户（尤其是高端 SAP 系统）的客户在 M 系列系列（HANA 大型实例）中使用更多特殊的 VM SKU。 这些 Azure 虚拟机类型在特定 Azure 区域中的所有数据中心中不可用。 由于这两种趋势，客户经历了网络延迟，这些延迟不在最佳范围内。 在某些情况下，这种延迟会导致其 SAP 系统的性能不理想。

为了防止这些问题，Azure 提供[邻近放置组](https://docs.microsoft.com/azure/virtual-machines/linux/co-location)。 此新功能已用于部署各种 SAP 系统。 有关邻近放置组的限制，请参阅本段开头引用的文章。 本文介绍可以或应该使用 Azure 邻近放置组的 SAP 方案。

## <a name="what-are-proximity-placement-groups"></a>什么是邻近放置组？ 
Azure 邻近放置组是逻辑构造。 定义一个区域时，它将绑定到 Azure 区域和 Azure 资源组。 部署 VM 时，邻近放置组由以下方式引用：

- 部署在数据中心中的第一个 Azure VM。 您可以将第一个虚拟机视为"范围 VM"，该虚拟机基于 Azure 分配算法部署在数据中心中，最终与特定可用性区域的用户定义相结合。
- 部署的所有后续 VM 都引用邻近放置组，以便将所有随后部署的 Azure VM 放在第一个虚拟机的同一数据中心中。

> [!NOTE]
> 如果没有部署能够在第一个 VM 所在的数据中心运行特定 VM 类型的主机硬件，则请求的 VM 类型的部署不会成功。 你会得到一条失败的消息。

单个[Azure 资源组](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal)可以为其分配多个邻近位置组。 但是，只能将邻近放置组分配给一个 Azure 资源组。

使用邻近放置组时，请记住以下注意事项：

- 当您旨在为 SAP 系统实现最佳性能，并使用接近放置组将自己限制为系统的单个 Azure 数据中心时，可能无法在放置组中组合所有类型的 VM 系列。 出现这些限制的原因是，在部署放置组的"作用域 VM"的数据中心中可能不存在运行特定 VM 类型的主机硬件。
- 在这种 SAP 系统的生命周期中，您可能被迫将系统移动到另一个数据中心。 例如，如果您决定横向扩展 HANA DBMS 层应从 4 个节点移动到 16 个节点，并且没有足够的容量来获取数据中心中使用的 12 个 VM，则可能需要此移动。
- 由于硬件的停用，Microsoft 可能会为您在其他数据中心使用的 VM 类型（而不是最初使用的数据中心）建立容量。 在这种情况下，您可能需要将所有邻近放置组的 VM 移动到另一个数据中心。

## <a name="proximity-placement-groups-with-sap-systems-that-use-only-azure-vms"></a>具有仅使用 Azure VM 的 SAP 系统的邻近放置组
Azure 上的大多数 SAP NetWeaver 和 S/4HANA 系统部署不使用[HANA 大型实例](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)。 对于不使用 HANA 大型实例的部署，在 SAP 应用程序层和 DBMS 层之间提供最佳性能非常重要。 为此，请只为系统定义 Azure 接近放置组。

在大多数客户部署中，客户为 SAP 系统构建单个[Azure 资源组](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal)。 在这种情况下，生产 ERP 系统资源组与其邻近放置组之间存在一对一关系。 在其他情况下，客户水平组织其资源组，并在单个资源组中收集所有生产系统。 在这种情况下，生产 SAP 系统的资源组与生产 SAP ERP、SAP BW 等的几个邻近放置组之间存在一对多关系。

避免将多个 SAP 生产或非生产系统捆绑在单个接近放置组中。 当少量 SAP 系统或 SAP 系统以及一些周围应用程序需要进行低延迟网络通信时，可以考虑将这些系统移动到一个接近放置组中。 您应该避免捆绑系统，因为在接近放置组中分组的系统越多，机会就越高：

- 您需要无法在邻近放置组限定到的特定数据中心中运行的 VM 类型。
- 非主流 VM（如 M 系列 VM）的资源最终可能得不到满足，因为随着时间的推移，您会将软件添加到接近放置组。

如下所述，理想的配置如下所示：

![仅具有 Azure VM 的邻近放置组](./media/sap-proximity-placement-scenarios/ppg-for-all-azure-vms.png)

在这种情况下，单个 SAP 系统分别分组到一个资源组中，每个资源组各有一个邻近放置组。 您使用 HANA 横向扩展配置还是 DBMS 扩展配置，没有相关性。

## <a name="proximity-placement-groups-and-hana-large-instances"></a>邻近放置组和 HANA 大型实例
如果某些 SAP 系统依赖于适用于应用程序层[的 HANA 大型实例](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)，则当您使用在[修订版 4 行或图章](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance)中部署的 HANA 大型实例单元时，HANA 大型实例单元和 Azure VM 之间的网络延迟可能会显著改善。 一个改进是，HANA 大型实例单元在部署时，使用接近放置组进行部署。 您可以使用该邻近放置组来部署应用程序层 VM。 因此，这些 VM 将部署在承载 HANA 大型实例单元的同一数据中心中。

要确定 HANA 大型实例单元是否部署在修订版 4 戳或行中，请[选中"Azure HANA 大型实例"通过 Azure 门户进行控制](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-li-portal#look-at-attributes-of-single-hli-unit)的文章。 在 HANA 大型实例单元的属性概述中，还可以确定邻近放置组的名称，因为它是在部署 HANA 大型实例单元时创建的。 属性概述中显示的名称是应将应用程序层 VM 部署到的邻近放置组的名称。

与仅使用 Azure 虚拟机的 SAP 系统相比，在使用 HANA 大型实例时，在决定要使用的[Azure 资源组](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal)数量时的灵活性较低。 [HANA 大型实例租户](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-know-terms)的所有 HANA 大型实例单元都分组到单个资源组中，如[本文](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-li-portal#display-of-hana-large-instance-units-in-the-azure-portal)所述。 除非您部署到不同的租户以分离生产和非生产系统或其他系统，否则所有 HANA 大型实例单元都将部署在一个 HANA 大型实例租户中。 此租户与资源组具有一对一关系。 但是，将为每个单个单元定义单独的接近放置组。

因此，Azure 资源组和单个租户的邻近放置组之间的关系如下所示：

![邻近放置组和 HANA 大型实例](./media/sap-proximity-placement-scenarios/ppg-for-hana-large-instance-units.png)

## <a name="example-of-deployment-with-proximity-placement-groups"></a>具有邻近放置组的部署示例
以下是一些 PowerShell 命令，可用于使用 Azure 邻近放置组部署 VM。

登录到[Azure 云行壳](https://azure.microsoft.com/features/cloud-shell/)后，第一步是检查您是否在要用于部署的 Azure 订阅中：

<pre><code>
Get-AzureRmContext
</code></pre>

如果需要更改为其他订阅，可以通过运行此命令来执行此操作：

<pre><code>
Set-AzureRmContext -Subscription "my PPG test subscription"
</code></pre>

通过运行此命令创建新的 Azure 资源组：

<pre><code>
New-AzResourceGroup -Name "myfirstppgexercise" -Location "westus2"
</code></pre>

通过运行此命令创建新的邻近放置组：

<pre><code>
New-AzProximityPlacementGroup -ResourceGroupName "myfirstppgexercise" -Name "letsgetclose" -Location "westus2"
</code></pre>

使用如下所示的命令将第一个 VM 部署到接近放置组中：

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppganchorvm" -Location "westus2" -OpenPorts 80,3389 -ProximityPlacementGroup "letsgetclose" -Size "Standard_DS11_v2"
</code></pre>

前面的命令部署基于 Windows 的 VM。 此 VM 部署成功后，在 Azure 区域中定义邻近放置组的数据中心范围。 引用邻近放置组的所有后续 VM 部署（如前一命令所示）都将部署在同一 Azure 数据中心中，只要 VM 类型可以托管在放置在该数据中心的硬件上，并且该 VM 类型的容量为可用。

## <a name="combine-availability-sets-and-availability-zones-with-proximity-placement-groups"></a>将可用性集和可用性区域与邻近放置组相结合
对 SAP 系统部署使用可用性区域的缺点之一是，无法使用特定区域内的可用性集来部署 SAP 应用程序层。 您希望 SAP 应用程序层部署在同一区域中的 DBMS 层。 不支持在部署单个 VM 时引用可用性区域和可用性集。 因此，以前，您被迫通过引用区域来部署应用程序层。 您失去了确保应用程序层 VM 分布在不同更新和失败域的能力。

通过使用接近放置组，可以绕过此限制。 下面是部署顺序：

- 创建接近放置组。
- 通过引用可用性区域来部署锚定 VM（通常是 DBMS 服务器）。
- 创建引用 Azure 邻近组的可用性集。 （请参阅本文后面的命令。
- 通过引用可用性集和邻近放置组来部署应用程序层 VM。

部署 VM 时，引用可用性区域和邻近放置组时，引用的不是上一节所示部署第一个 VM：

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppganchorvm" -Location "westus2" -OpenPorts 80,3389 -Zone "1" -ProximityPlacementGroup "letsgetclose" -Size "Standard_E16_v3"
</code></pre>

成功部署此虚拟机将在一个可用性区域中托管 SAP 系统的数据库实例。 邻近放置组的范围固定到表示您定义的可用区域的数据中心之一。

假设您以与 DBMS VM 相同的方式部署中央服务 VM，引用相同的区域或区域以及相同的邻近放置组。 在下一步中，您需要创建要用于 SAP 系统应用程序层的可用性集。

您需要定义和创建接近放置组。 创建可用性集的命令需要对邻近放置组 ID（而不是名称）的附加引用。 您可以使用以下命令获取接近放置组的 ID：

<pre><code>
Get-AzProximityPlacementGroup -ResourceGroupName "myfirstppgexercise" -Name "letsgetclose"
</code></pre>

创建可用性集时，在使用托管磁盘（除非另有指定否则默认）和邻近放置组时需要考虑其他参数：

<pre><code>
New-AzAvailabilitySet -ResourceGroupName "myfirstppgexercise" -Name "myppgavset" -Location "westus2" -ProximityPlacementGroupId "/subscriptions/my very long ppg id string" -sku "aligned" -PlatformUpdateDomainCount 3 -PlatformFaultDomainCount 2 
</code></pre>

理想情况下，应使用三个容错域。 但是，受支持的容错域的数量可能因地区而异。 在这种情况下，特定区域可能的最大容错域数为两个。 要部署应用程序层 VM，需要添加对可用性集名称和邻近放置组名称的引用，如下所示：

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppgavsetappvm" -Location "westus2" -OpenPorts 80,3389 -AvailabilitySetName "myppgavset" -ProximityPlacementGroup "letsgetclose" -Size "Standard_DS11_v2"
</code></pre>

此部署的结果是：
- 位于特定可用性区域或可用性区域的 SAP 系统的 DBMS 层和中央服务。
- 通过与 DBMS VM 或 VM 位于同一 Azure 数据中心的可用性集的 SAP 应用程序层。

> [!NOTE]
> 由于将一个 DBMS VM 部署到一个区域，将第二个 DBMS VM 部署到另一个区域以创建高可用性配置，因此每个区域都需要不同的邻近放置组。 使用的任何可用性集也是如此。

## <a name="move-an-existing-system-into-proximity-placement-groups"></a>将现有系统移动到接近放置组
如果已经部署了 SAP 系统，则可能需要优化某些关键系统的网络延迟，并将应用程序层和 DBMS 层位于同一数据中心中。 要将完整 Azure 可用性集的 VM 移动到已限定的现有邻近放置组，需要关闭可用性集的所有 VM，并通过 Azure 门户将可用性集分配给现有邻近放置组，电源外壳或 CLI。 如果要将不属于可用性集的 VM 移动到现有接近放置组中，只需关闭 VM 并将其分配给现有邻近放置组。 


## <a name="next-steps"></a>后续步骤
查看文档：

- [Azure 上的 SAP 工作负载：规划和部署检查表](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist)
- [预览：使用 Azure CLI 将 VM 部署到邻近放置组](https://docs.microsoft.com/azure/virtual-machines/linux/proximity-placement-groups)
- [预览：使用 PowerShell 将 VM 部署到邻近放置组](https://docs.microsoft.com/azure/virtual-machines/windows/proximity-placement-groups)
- [用于 SAP 工作负载的 Azure 虚拟机 DBMS 部署注意事项](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)

