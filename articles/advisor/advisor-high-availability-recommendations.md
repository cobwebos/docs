---
title: 使用 Azure 顾问提高应用程序的可用性 | Microsoft Docs
description: 使用 Azure 顾问提高 Azure 部署的高可用性。
services: advisor
documentationcenter: NA
author: kasparks
ms.author: kasparks
ms.service: advisor
ms.topic: article
ms.date: 01/29/2019
ms.openlocfilehash: d78d8a689b860162a742e85c155205f072a3667c
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446776"
---
# <a name="improve-availability-of-your-application-with-azure-advisor"></a>使用 Azure 顾问提高应用程序的可用性

Azure 顾问可帮助确保并提高业务关键应用程序的连续性。 可以通过顾问仪表板的“高可用性”  选项卡获取顾问的高可用性建议。

## <a name="ensure-virtual-machine-fault-tolerance"></a>确保虚拟机容错

要为应用程序提供冗余，建议将两个或更多虚拟机组合到一个可用性集中。 顾问标识不属于可用性集的虚拟机，并建议将它们移动到可用性集中。 这种配置可以确保在计划内或计划外维护事件期间，至少有一个虚拟机可用，并满足 Azure 虚拟机 SLA 要求。 可以选择为虚拟机创建可用性集，或将虚拟机添加到现有可用性集。

> [!NOTE]
> 如果选择创建可用性集，则必须至少再向其中添加一台虚拟机。 建议在可用性集中对两个或更多虚拟机进行分组，确保其中一台虚拟机在出现故障期间可用。

## <a name="ensure-availability-set-fault-tolerance"></a>确保可用性集容错

要为应用程序提供冗余，建议将两个或更多虚拟机组合到一个可用性集中。 顾问标识包含单个虚拟机的可用性集，并建议向其中添加一个或多个虚拟机。 这种配置可以确保在计划内或计划外维护事件期间，至少有一个虚拟机可用，并满足 Azure 虚拟机 SLA 要求。 可以选择创建虚拟机，或将现有的虚拟机添加到可用性集。  

## <a name="use-managed-disks-to-improve-data-reliability"></a>使用托管磁盘提高数据可靠性

具有共享存储帐户或存储缩放单元的磁盘的可用性集中的虚拟机在中断期间不可对单个存储规模单元故障进行复原。 顾问将确定这些可用性集，并建议迁移到 Azure 托管磁盘。 这将确保可用性集中的不同虚拟机的磁盘彼此完全独立，以避免单点故障。 

## <a name="ensure-application-gateway-fault-tolerance"></a>确保应用程序网关容错

此建议可确保由应用程序网关提供支持的任务关键型应用程序的业务连续性。 顾问会标识未针对容错进行配置的应用程序网关实例，并且会建议可以执行的修正操作。 顾问会标识中型或大型单实例应用程序网关，并建议至少再添加一个实例。 它还标识单实例或多实例小型应用程序网关，并建议迁移到中型或大型 SKU。 顾问建议执行这些操作以确保应用程序网关实例配置为满足这些资源的当前 SLA 要求。

## <a name="protect-your-virtual-machine-data-from-accidental-deletion"></a>防止意外删除虚拟机数据

设置虚拟机备份可确保业务关键型数据的可用性，并防止意外删除或损坏。 顾问标识其中未启用备份的虚拟机，并建议启用备份。 

## <a name="ensure-you-have-access-to-azure-cloud-experts-when-you-need-it"></a>确保在需要时有权访问 Azure 云专家

在运行业务关键型工作负载时，在需要时有权访问技术支持至关重要。 顾问标识在其支持计划中不包含技术支持的潜在业务关键型订阅，并建议升级到包含技术支持的选项。

## <a name="create-azure-service-health-alerts-to-be-notified-when-azure-issues-affect-you"></a>创建 Azure 服务运行状况警报，以便在 Azure 问题影响你时收到通知

我们建议设置 Azure 服务运行状况警报，以便在 Azure 服务问题影响你时收到通知。 [Azure 服务运行状况](https://azure.microsoft.com/features/service-health/)是一项免费服务，可在你受到 Azure 服务问题影响时提供个性化指导和支持。 顾问会识别未配置警报的订阅，并建议创建一个警报。

## <a name="configure-traffic-manager-endpoints-for-resiliency"></a>配置流量管理器终结点以便进行复原

如果任何给定的终结点出现故障，则包含多个终结点的流量管理器配置文件将体验到更高的可用性。 将终结点放在不同区域中可进一步提高服务可靠性。 顾问会识别仅有一个终结点的流量管理器配置文件，并建议在另一个区域中至少再添加一个终结点。

如果为邻近路由配置的流量管理器配置文件中的所有终结点都位于同一区域，则来自其他区域的用户可能会遇到连接延迟。 如果一个区域中的所有终结点都出现故障，则将一个终结点添加或移动到另一个区域会提高整体性能并提供更好的可用性。 顾问会识别为邻近路由配置的流量管理器配置文件（其中所有终结点都位于同一区域中）。 它会建议将一个终结点添加或移动到另一个 Azure 区域。

如果为地理路由配置了流量管理器配置文件，则会根据已定义的区域将流量路由到终结点。 如果某个区域出现故障，则不会进行预定义的故障转移。 拥有一个“区域分组”配置为“所有(世界)”的终结点可避免流量被丢弃并提高服务可用性。 顾问会识别为地理路由配置的流量管理器配置文件（其中没有终结点配置为将“区域分组”设置为“所有(世界)”）。 它会建议更改该配置以创建终结点“所有(世界)”。

## <a name="use-soft-delete-on-your-azure-storage-account-to-save-and-recover-data-after-accidental-overwrite-or-deletion"></a>对 Azure 存储帐户使用软删除可在意外覆盖或删除之后保存和恢复数据

为存储帐户启用[软删除](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete)，以便将删除的 blob 转换为软删除状态，而不是永久删除。 覆盖数据时，会生成软删除快照以保存被覆盖数据的状态。 使用软删除可以在意外删除或覆盖时进行恢复。 顾问会识别未启用软删除的 Azure 存储帐户，并建议你启用它。

## <a name="configure-your-vpn-gateway-to-active-active-for-connection-resiliency"></a>配置 VPN 网关为主动-主动以进行连接复原

在主动-主动配置中，VPN 网关的两个实例将建立 S2S VPN 隧道与本地 VPN 设备。 当一个网关实例上发生计划内维护事件或计划外事件时，流量将自动切换到其他活动 IPsec 隧道。 Azure 顾问将识别未配置为主动-主动的 VPN 网关并建议将它们配置为高可用性。

## <a name="use-production-vpn-gateways-to-run-your-production-workloads"></a>使用生产 VPN 网关运行生产工作负荷

Azure 顾问将检查任何 VPN 网关的基本 SKU，并建议您改为使用生产 SKU。 基本 SKU 专为开发和测试目的。 生产 Sku 提供更多的隧道、 BGP 支持、 主动-主动配置选项、 自定义 Ipsec/IKE 策略和更高版本稳定性和可用性。

## <a name="repair-invalid-log-alert-rules"></a>修复无效的日志警报规则

Azure 顾问将检测到具有无效的查询在其条件部分中指定的警报规则。 日志警报规则在 Azure Monitor 中创建，用于按指定时间间隔运行分析查询。 查询结果决定了是否需要触发警报。 随着时间的推移，分析查询可能会因所引用资源、表或命令的变化而变得无效。 顾问建议你先纠正警报规则，以防止获取自动禁用它，并确保在 Azure 中资源的监视范围中的查询。 [了解有关故障排除警报规则的详细信息](https://aka.ms/aa_logalerts_queryrepair)

## <a name="configure-consistent-indexing-mode-on-your-cosmos-db-collection"></a>Cosmos DB 集合上配置一致索引模式

使用延迟索引模式配置的 azure Cosmos DB 容器可能会影响查询结果的新鲜度。 顾问将检测到这种方式配置的容器，并且建议切换到一致的模式。 [了解有关索引 Cosmos DB 中的策略的详细信息](https://aka.ms/cosmosdb/how-to-manage-indexing-policy)

## <a name="configure-your-azure-cosmos-db-containers-with-a-partition-key"></a>为 Azure Cosmos DB 容器配置分区键

Azure 顾问会标识 Azure Cosmos DB 未分区的集合已接近其预配的存储配额。 它将建议迁移到新集合与分区键定义这些集合，以便它们可以自动进行横向扩展服务。 [了解有关选择分区键的详细信息](https://aka.ms/cosmosdb/choose-partitionkey)

## <a name="upgrade-your-azure-cosmos-db-net-sdk-to-the-latest-version-from-nuget"></a>将 Azure Cosmos DB .NET SDK 升级到 Nuget 提供的最新版本

Azure 顾问会标识 Azure Cosmos DB 帐户，使用的旧版本的.NET SDK 并建议升级到最新版本从 Nuget 的最新修补程序、 性能改进和新功能。 [了解有关 Cosmos DB.NET SDK 的详细信息](https://aka.ms/cosmosdb/sql-api-sdk-dotnet)

## <a name="upgrade-your-azure-cosmos-db-java-sdk-to-the-latest-version-from-maven"></a>将 Azure Cosmos DB Java SDK 升级到 Maven 提供的最新版本

Azure 顾问会标识 Azure Cosmos DB 帐户，使用的旧版本的 Java SDK 并建议升级到最新版本从 Maven 为最新修补程序、 性能改进和新功能。 [了解有关 Cosmos DB Java SDK 的详细信息](https://aka.ms/cosmosdb/sql-api-sdk-dotnet)

## <a name="upgrade-your-azure-cosmos-db-spark-connector-to-the-latest-version-from-maven"></a>将 Azure Cosmos DB Spark 连接器升级到 Maven 提供的最新版本

Azure 顾问会标识 Azure Cosmos DB 帐户，正在使用旧版本的 Cosmos DB Spark 连接器并建议升级到最新版本从 Maven 为最新修补程序、 性能改进和新功能。 [了解有关 Cosmos DB Spark 连接器的详细信息](https://aka.ms/cosmosdb/spark-connector)

## <a name="enable-virtual-machine-replication"></a>启用虚拟机复制
没有启用到另一个区域复制的虚拟机不是能够弹性应对区域性故障。 将虚拟机复制的 Azure 区域服务中断期间减少任何不利的业务影响。 顾问将检测到不具有复制的 Vm 启用和建议，以便在发生中断，您可以快速打开你的虚拟机的远程 Azure 区域中启用复制。 [了解有关虚拟机复制的详细信息](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-quickstart)

## <a name="how-to-access-high-availability-recommendations-in-advisor"></a>如何访问顾问中的高可用性建议

1. 登录 [Azure 门户](https://portal.azure.com)，并打开[顾问](https://aka.ms/azureadvisordashboard)。

2.  在顾问仪表板中，单击“高可用性”选项卡  。

## <a name="next-steps"></a>后续步骤

有关顾问建议的详细信息，请参阅以下资源：
* [Azure 顾问简介](advisor-overview.md)
* [顾问入门](advisor-get-started.md)
* [顾问成本建议](advisor-cost-recommendations.md)
* [顾问性能建议](advisor-performance-recommendations.md)
* [顾问安全性建议](advisor-security-recommendations.md)

