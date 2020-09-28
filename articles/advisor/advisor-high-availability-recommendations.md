---
title: 利用 Advisor 提高应用程序的可靠性
description: 使用 Azure 顾问确保并提高业务关键 Azure 部署的可靠性。
ms.topic: article
ms.date: 09/27/2020
ms.openlocfilehash: 1e256d99f8d78ddff318f963dcb21e9b4537f110
ms.sourcegitcommit: ada9a4a0f9d5dbb71fc397b60dc66c22cf94a08d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/28/2020
ms.locfileid: "91405184"
---
# <a name="improve-the-reliability-of-your-application-by-using-azure-advisor"></a>使用 Azure Advisor 提高应用程序的可靠性

Azure 顾问可帮助确保并提高业务关键应用程序的连续性。 可以在顾问仪表板的 " **可靠性** " 选项卡上通过 advisor 获取可靠性建议。

## <a name="check-the-version-of-your-check-point-network-virtual-appliance-image"></a>检查检查点网络虚拟设备映像的版本

顾问可以确定虚拟机是否在运行平台服务操作过程中已知丢失网络连接的检查点映像版本。 顾问建议将帮助你升级到用于解决此问题的较新版本的映像。 此检查将通过更好的网络连接来确保业务连续性。

## <a name="ensure-application-gateway-fault-tolerance"></a>确保应用程序网关容错

此建议可确保由应用程序网关提供支持的任务关键型应用程序的业务连续性。 顾问标识未配置容错的应用程序网关实例。 然后，它会建议您可采取的修正操作。 顾问标识中型或大型单实例应用程序网关，并建议至少添加一个实例。 它还标识单实例和多实例的小型应用程序网关，并建议将它们迁移到中型或大型 Sku。 顾问建议执行这些操作，以确保将应用程序网关实例配置为满足这些资源的当前 SLA 要求。

## <a name="protect-your-virtual-machine-data-from-accidental-deletion"></a>防止意外删除虚拟机数据

设置虚拟机备份可确保业务关键型数据的可用性，并防止意外删除或损坏。 Advisor 标识未启用备份的虚拟机，并建议启用备份。 

## <a name="ensure-you-have-access-to-azure-experts-when-you-need-it"></a>请确保在需要时可以访问 Azure 专家

当你运行业务关键型工作负荷时，有必要访问技术支持部门，这一点很重要。 Advisor 识别潜在的业务关键订阅，它们的支持计划中不包含技术支持。 建议升级到包括技术支持的选项。

## <a name="create-azure-service-health-alerts-to-be-notified-when-azure-problems-affect-you"></a>创建 Azure 服务运行状况警报，以便在 Azure 问题影响时收到通知

建议设置 Azure 服务运行状况警报，以便在 Azure 服务问题影响你时收到通知。 [Azure 服务运行状况](https://azure.microsoft.com/features/service-health/) 是一种免费服务，在受 Azure 服务问题影响时提供个性化指导和支持。 顾问标识未配置警报并建议对其进行配置的订阅。

## <a name="configure-traffic-manager-endpoints-for-resiliency"></a>配置流量管理器终结点以便进行复原

如果任何给定终结点出现故障，则具有多个终结点的 Azure 流量管理器配置文件具有更高的可用性。 将终结点放在不同区域中可进一步提高服务可靠性。 顾问标识流量管理器配置文件，其中只有一个终结点，并建议在另一个区域中至少添加一个终结点。

如果为邻近路由配置的流量管理器配置文件中的所有终结点都位于同一区域中，则其他区域中的用户可能会遇到连接延迟。 如果一个区域中的所有终结点都出现故障，则将一个终结点添加或移动到另一个区域会提高整体性能并提供更好的可用性。 顾问会识别为邻近路由配置的流量管理器配置文件（其中所有终结点都位于同一区域中）。 它会建议将一个终结点添加或移动到另一个 Azure 区域。

如果为地理路由配置了流量管理器配置文件，则流量将根据定义的区域路由到终结点。 如果某个区域发生故障，则没有预定义的故障转移。 如果你有一个终结点，其中区域分组配置为 **全部 (World) **，你可以避免丢弃流量并提高服务可用性。 Advisor 识别为地理路由配置的流量管理器配置文件，其中没有任何终结点配置为将区域分组为 **所有 (World) **。 建议更改配置，使终结点 **全部 (世界) **。

## <a name="use-soft-delete-on-your-azure-storage-account-to-save-and-recover-data-after-accidental-overwrite-or-deletion"></a>使用 Azure 存储帐户上的软删除在意外覆盖或删除后保存和恢复数据

为存储帐户启用[软删除](../storage/blobs/soft-delete-overview.md)，以便将删除的 blob 转换为软删除状态，而不是永久删除。 覆盖数据时，会生成软删除快照以保存被覆盖数据的状态。 使用软删除可恢复意外删除或覆盖。 Advisor 标识未启用软删除的 Azure 存储帐户，并建议你启用该帐户。

## <a name="configure-your-vpn-gateway-to-active-active-for-connection-resiliency"></a>配置 VPN 网关为主动-主动以进行连接复原

在主动-主动配置中，VPN 网关的两个实例都为你的本地 VPN 设备建立 S2S VPN 隧道。 一个网关实例发生计划内维护事件或计划外事件时，会自动将流量切换到其他活动 IPsec 隧道。 Azure Advisor 标识未配置为主动-主动的 VPN 网关，并建议你将其配置为具有高可用性。

## <a name="use-production-vpn-gateways-to-run-your-production-workloads"></a>使用生产 VPN 网关运行生产工作负荷

Azure 顾问检查是否有任何使用基本 SKU 的 VPN 网关，并建议改用生产 SKU。 基本 SKU 设计用于开发和测试。 生产 Sku 产品/服务：
- 更多隧道。 
- BGP 支持。 
- 主动-主动配置选项。 
- 自定义 Ipsec/IKE 策略。 
- 更高的稳定性和可用性。

## <a name="ensure-virtual-machine-fault-tolerance-temporarily-disabled"></a>确保暂时禁用虚拟机容错 () 

若要为应用程序提供冗余，我们建议你将两个或更多虚拟机组合到一个可用性集中。 Advisor 标识不属于可用性集的虚拟机，并建议将它们移到其中一个。 此配置可确保在计划内或计划外维护期间至少有一个虚拟机可用，并满足 Azure 虚拟机 SLA 要求。 可以选择为虚拟机创建可用性集，或将虚拟机添加到现有可用性集。

> [!NOTE]
> 如果选择创建可用性集，则需要将至少一个虚拟机添加到其中。 建议在可用性集中对两个或更多虚拟机进行分组，确保其中一台虚拟机在出现故障期间可用。

## <a name="ensure-availability-set-fault-tolerance-temporarily-disabled"></a>确保 (暂时禁用可用性集容错) 

若要为应用程序提供冗余，我们建议你将两个或更多虚拟机组合到一个可用性集中。 顾问标识包含单个虚拟机的可用性集，并建议向其中添加一个或多个虚拟机。此配置可确保在计划内或计划外维护期间至少有一个虚拟机可用，并满足 Azure 虚拟机 SLA 要求。可以选择创建虚拟机，或将现有的虚拟机添加到可用性集。  

## <a name="use-managed-disks-to-improve-data-reliability-temporarily-disabled"></a>使用托管磁盘来改善 (临时禁用的数据可靠性) 

如果可用性集中的虚拟机具有共享存储帐户或存储缩放单位的磁盘，则在中断过程中无法恢复单个存储缩放单位的故障。 顾问会标识这些可用性集，并建议迁移到 Azure 托管磁盘。 此迁移将确保可用性集中虚拟机的磁盘充分隔离，以避免单点故障。 

## <a name="repair-invalid-log-alert-rules"></a>修复无效的日志警报规则

Azure 顾问检测到在 "条件" 部分中指定了无效查询的日志警报规则。 Azure Monitor 日志警报规则按指定的频率运行查询，并基于结果激发警报。 由于引用的资源、表或命令发生了变化，查询可能会因时间而变得无效。 顾问建议对警报查询进行更正，以防止自动禁用规则并确保监视范围。 有关详细信息，请参阅 [警报规则疑难解答](../azure-monitor/platform/alerts-troubleshoot-log.md#query-used-in-a-log-alert-isnt-valid)

## <a name="configure-consistent-indexing-mode-on-your-azure-cosmos-db-collection"></a>在 Azure Cosmos DB 集合中配置一致的索引模式

配置延迟索引模式 Azure Cosmos DB 容器可能会影响查询结果的新鲜度。 顾问检测到以这种方式配置的容器，并建议切换到一致模式。 [在 Azure Cosmos DB 中了解有关索引策略的详细信息。](https://aka.ms/cosmosdb/how-to-manage-indexing-policy)

## <a name="configure-your-azure-cosmos-db-containers-with-a-partition-key"></a>为 Azure Cosmos DB 容器配置分区键

Azure 顾问会标识接近其预配存储配额 Azure Cosmos DB 非分区集合。 建议你将这些集合迁移到具有分区键定义的新集合，以便服务能够自动向外扩展它们。 [了解有关选择分区键的详细信息。](https://aka.ms/cosmosdb/choose-partitionkey)

## <a name="upgrade-your-azure-cosmos-db-net-sdk-to-the-latest-version-from-nuget"></a>将 Azure Cosmos DB .NET SDK 升级到 NuGet 的最新版本

Azure 顾问会识别使用旧版本的 .NET SDK Azure Cosmos DB 帐户。 建议从 NuGet 升级到最新版本，以获取最新的修补程序、性能改进和功能。 [了解有关 Azure Cosmos DB .NET SDK 的详细信息。](https://aka.ms/cosmosdb/sql-api-sdk-dotnet)

## <a name="upgrade-your-azure-cosmos-db-java-sdk-to-the-latest-version-from-maven"></a>将 Azure Cosmos DB Java SDK 升级到 Maven 提供的最新版本

Azure 顾问会识别使用旧版本 Java SDK Azure Cosmos DB 帐户。 建议升级到 Maven 的最新版本，以获取最新的修补程序、性能改进和功能。 [详细了解 Azure Cosmos DB Java SDK。](https://aka.ms/cosmosdb/sql-api-sdk-async-java)

## <a name="upgrade-your-azure-cosmos-db-spark-connector-to-the-latest-version-from-maven"></a>将 Azure Cosmos DB Spark 连接器升级到 Maven 的最新版本

Azure 顾问会识别使用旧版本 Azure Cosmos DB Spark 连接器 Azure Cosmos DB 帐户。 建议升级到 Maven 的最新版本，以获取最新的修补程序、性能改进和功能。 [详细了解 Azure Cosmos DB Spark 连接器。](https://aka.ms/cosmosdb/spark-connector)

## <a name="consider-moving-to-kafka-21-on-hdinsight-40"></a>考虑移动到 HDInsight 4.0 上的 Kafka 2。1

从2020年7月1日开始，你将无法使用 Azure HDInsight 4.0 上的 Kafka 1.1 创建新的 Kafka 群集。 现有群集将在没有 Microsoft 支持的情况下按原样运行。 请考虑在2.1 年6月 2020 30 日之前迁移到 HDInsight 4.0 上的 Kafka，以避免潜在的系统/支持中断。

## <a name="consider-upgrading-older-spark-versions-in-hdinsight-spark-clusters"></a>考虑在 HDInsight Spark 群集中升级旧的 Spark 版本

从2020年7月1日开始，你将无法使用 Spark 2.1 或 2.2 on HDInsight 3.6 创建新的 Spark 群集。 你将无法使用 Spark 2.3 on HDInsight 4.0 创建新的 Spark 群集。 现有群集将在没有 Microsoft 支持的情况下按原样运行。 

## <a name="enable-virtual-machine-replication"></a>启用虚拟机复制
未启用复制的虚拟机不能在区域中断后复原。 在 Azure 区域中断期间，复制虚拟机可降低任何不利的业务影响。 Advisor 检测未启用复制的 Vm，并建议启用复制。 启用复制时，如果发生中断，可以快速将虚拟机带入远程 Azure 区域。 [了解有关虚拟机复制的详细信息。](../site-recovery/azure-to-azure-quickstart.md)

## <a name="upgrade-to-the-latest-version-of-the-azure-connected-machine-agent"></a>升级到最新版的 Azure Connected Machine Agent
[Azure 连接的计算机代理](https://docs.microsoft.com/azure/azure-arc/servers/manage-agent)会定期更新，其中包含 bug 修复、稳定性增强和新功能。 我们已识别出不能使用最新版本的计算机代理的资源，此顾问建议会建议将代理升级到最新版本，以获得最佳的 Azure Arc 体验。

## <a name="do-not-override-hostname-to-ensure-website-integrity"></a>请勿替代主机名以确保网站的完整性
在配置应用程序网关时，顾问建议尽量避免替代主机名。 在应用程序网关前端使用不同的域，这可能会导致 cookie 或重定向 url 中断。 请注意，在所有情况下，这种情况并不是如此，并且某些类别的后端 (（如 REST API 的) 通常不太敏感。 请确保后端能够处理此操作或更新应用程序网关配置，因此不需要将主机名覆盖到后端。 与应用服务一起使用时，将自定义域名附加到 Web 应用，并避免使用 *azurewebsites.net 主机名。* [详细了解自定义域](https://aka.ms/appgw-advisor-usecustomdomain)。

## <a name="how-to-access-high-availability-recommendations-in-advisor"></a>如何访问顾问中的高可用性建议

1. 登录 [Azure 门户](https://portal.azure.com)，并打开[顾问](https://aka.ms/azureadvisordashboard)。

2.  在顾问仪表板上，选择 " **高可用性** " 选项卡。

## <a name="next-steps"></a>后续步骤

有关顾问建议的详细信息，请参阅以下资源：
* [顾问简介](advisor-overview.md)
* [顾问入门](advisor-get-started.md)
* [顾问分数](azure-advisor-score.md)
* [顾问成本建议](advisor-cost-recommendations.md)
* [顾问性能建议](advisor-performance-recommendations.md)
* [顾问安全性建议](advisor-security-recommendations.md)
* [顾问卓越运营建议](advisor-operational-excellence-recommendations.md)
