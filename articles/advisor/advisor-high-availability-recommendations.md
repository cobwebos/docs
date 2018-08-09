---
title: Azure 顾问高可用性建议 | Microsoft Docs
description: 使用 Azure 顾问提高 Azure 部署的高可用性。
services: advisor
documentationcenter: NA
author: KumudD
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: advisor
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/16/2016
ms.author: kumud
ms.openlocfilehash: 297a213fe4219b834187f977e3281eb939352f60
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/07/2018
ms.locfileid: "39594427"
---
# <a name="advisor-high-availability-recommendations"></a>顾问高可用性建议

Azure 顾问可帮助确保并提高业务关键应用程序的连续性。 可以通过顾问仪表板的“高可用性”选项卡获取顾问的高可用性建议。

## <a name="ensure-virtual-machine-fault-tolerance"></a>确保虚拟机容错

要为应用程序提供冗余，建议将两个或更多虚拟机组合到一个可用性集中。 顾问标识不属于可用性集的虚拟机，并建议将它们移动到可用性集中。 这种配置可以确保在计划内或计划外维护事件期间，至少有一个虚拟机可用，并满足 Azure 虚拟机 SLA 要求。 可以选择为虚拟机创建可用性集，或将虚拟机添加到现有可用性集。

> [!NOTE]
> 如果选择创建可用性集，则必须至少再向其中添加一台虚拟机。 建议在可用性集中对两个或更多虚拟机进行分组，确保其中一台虚拟机在出现故障期间可用。

## <a name="ensure-availability-set-fault-tolerance"></a>确保可用性集容错 

要为应用程序提供冗余，建议将两个或更多虚拟机组合到一个可用性集中。 顾问标识包含单个虚拟机的可用性集，并建议向其中添加一个或多个虚拟机。 这种配置可以确保在计划内或计划外维护事件期间，至少有一个虚拟机可用，并满足 Azure 虚拟机 SLA 要求。 可以选择创建虚拟机，或将现有的虚拟机添加到可用性集。  

## <a name="ensure-application-gateway-fault-tolerance"></a>确保应用程序网关容错
为了确保由应用程序网关提供支持的任务关键型应用程序的业务连续性，顾问会标识没有针对容错进行配置的应用程序网关实例，并建议可以执行的修正操作。 顾问会标识中型或大型单实例应用程序网关，并建议至少再添加一个实例。 它还标识单实例或多实例小型应用程序网关，并建议迁移到中型或大型 SKU。 顾问建议执行这些操作以确保应用程序网关实例配置为满足这些资源的当前 SLA 要求。

## <a name="improve-the-performance-and-reliability-of-virtual-machine-disks"></a>提高虚拟机磁盘的性能和可靠性

顾问标识带标准磁盘的虚拟机，并建议升级为高级磁盘。
 
Azure 高级存储为运行 I/O 密集型工作负荷的虚拟机提供高性能、低延迟的磁盘支持。 在固态硬盘 (SSD) 上使用高级存储帐户存储数据的虚拟机磁盘。 为使应用程序实现最佳性能，建议将任何需要高 IOPS 的虚拟机磁盘迁移到高级存储。 

如果磁盘不需要高 IOPS，则可以通过将其保留在标准存储中来限制成本。 标准存储将虚拟机磁盘数据存储在硬盘驱动器 (HDD) 而不是 SSD 上。 可以选择将虚拟机磁盘迁移到高级磁盘。 大多数虚拟机 SKU 支持高级磁盘。 但是在某些情况下，如果要使用高级磁盘，可能也需要升级虚拟机 SKU。

## <a name="protect-your-virtual-machine-data-from-accidental-deletion"></a>防止意外删除虚拟机数据

设置虚拟机备份可确保业务关键型数据的可用性，并防止意外删除或损坏。  顾问标识其中未启用备份的虚拟机，并建议启用备份。 

## <a name="ensure-you-have-access-to-azure-cloud-experts-when-you-need-it"></a>确保在需要时有权访问 Azure 云专家

在运行业务关键型工作负载时，在需要时有权访问技术支持至关重要。 顾问标识在其支持计划中不包含技术支持的潜在业务关键型订阅，并建议升级到包含技术支持的选项。

## <a name="create-azure-service-health-alerts-to-be-notified-when-azure-issues-affect-you"></a>创建 Azure 服务运行状况警报，以便在 Azure 问题影响你时收到通知

我们建议设置 Azure 服务运行状况警报，以便在 Azure 服务问题影响你时收到通知。 [Azure 服务运行状况](https://azure.microsoft.com/features/service-health/)是一项免费服务，可在你受到 Azure 服务问题影响时提供个性化指导和支持。 顾问会识别未配置警报的订阅，并建议创建一个警报。

## <a name="configure-traffic-manager-endpoints-for-resiliency"></a>配置流量管理器终结点以便进行复原

如果任何给定的终结点出现故障，则包含多个终结点的流量管理器配置文件将体验到更高的可用性。 将终结点放在不同区域中可进一步提高服务可靠性。 顾问会识别仅有一个终结点的流量管理器配置文件，并建议在另一个区域中至少再添加一个终结点。

如果为邻近路由配置的流量管理器配置文件中的所有终结点都位于同一区域，则来自其他区域的用户可能会遇到连接延迟。 如果一个区域中的所有终结点都出现故障，则将一个终结点添加或移动到另一个区域会提高整体性能并提供更好的可用性。 顾问会识别为邻近路由配置的流量管理器配置文件（其中所有终结点都位于同一区域中），并建议将一个终结点添加或移动到另一个 Azure 区域。

如果为地理路由配置了流量管理器配置文件，则会根据已定义的区域将流量路由到终结点。 如果某个区域出现故障，则不会进行预定义的故障转移。 拥有一个“区域分组”配置为“所有(世界)”的终结点可避免流量被丢弃并提高服务可用性。 顾问会识别为地理路由配置的流量管理器配置文件（其中没有终结点配置为将“区域分组”设置为“所有(世界)”），并建议更改该配置。

## <a name="how-to-access-high-availability-recommendations-in-advisor"></a>如何访问顾问中的高可用性建议

1. 登录 [Azure 门户](https://portal.azure.com)，并打开[顾问](https://aka.ms/azureadvisordashboard)。

2.  在顾问仪表板中，单击“高可用性”选项卡。

## <a name="next-steps"></a>后续步骤

有关顾问建议的详细信息，请参阅以下资源：
* [Azure 顾问简介](advisor-overview.md)
* [顾问入门](advisor-get-started.md)
* [顾问成本建议](advisor-performance-recommendations.md)
* [顾问性能建议](advisor-performance-recommendations.md)
* [顾问安全性建议](advisor-security-recommendations.md)

