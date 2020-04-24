---
title: 如何审核 Azure Cosmos DB 控制平面操作
description: 了解如何在 Azure Cosmos DB 中审核控制平面操作（如添加区域、更新吞吐量、区域故障转移、添加 VNet 等）
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: sngun
ms.openlocfilehash: 32dd598b8fc62c0ec68f86f95b02f9f3d98cedd2
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/24/2020
ms.locfileid: "82116292"
---
# <a name="how-to-audit-azure-cosmos-db-control-plane-operations"></a>如何审核 Azure Cosmos DB 控制平面操作

Azure Cosmos DB 中的控制平面是一项 RESTful 服务，可用于对 Azure Cosmos 帐户执行一组不同的操作。 它向最终用户公开公共资源模型（例如数据库、帐户）和各种操作，以便对资源模型执行操作。 控制平面操作包括对 Azure Cosmos 帐户或容器所做的更改。 例如，创建 Azure Cosmos 帐户、添加区域、更新吞吐量、区域故障转移、添加 VNet 等操作都是控制平面操作。 本文介绍如何在 Azure Cosmos DB 中审核控制平面操作。 你可以使用 Azure CLI、PowerShell 或 Azure 门户在 Azure Cosmos 帐户上运行控制平面操作，而对于容器，请使用 Azure CLI 或 PowerShell。

以下是审核控制平面操作有用的一些示例方案：

* 要在修改 Azure Cosmos 帐户的防火墙规则时收到警报。 若要对管理 Azure Cosmos 帐户的网络安全的规则进行未经授权的修改并采取快速操作，此警报是必需的。

* 如果在 Azure Cosmos 帐户中添加或删除了新区域，你希望收到警报。 添加或删除区域会影响计费和数据主权要求。 此警报将帮助你检测你的帐户中的区域的意外添加或删除。

* 你需要从诊断日志获取有关所更改内容的更多详细信息。 例如，VNet 发生了更改。

## <a name="disable-key-based-metadata-write-access"></a>禁用基于密钥的元数据写入访问权限

在 Azure Cosmos DB 中审核控制平面操作之前，请在你的帐户上禁用基于密钥的元数据写入访问权限。 禁用基于密钥的元数据写访问后，将无法通过帐户密钥连接到 Azure Cosmos 帐户的客户端访问该帐户。 可以通过将`disableKeyBasedMetadataWriteAccess`属性设置为 true 来禁用写入访问权限。 设置此属性后，可以从具有适当的基于角色的访问控制（RBAC）角色和凭据的用户发生对任何资源的更改。 若要了解有关如何设置此属性的详细信息，请参阅[阻止 sdk 更改](role-based-access-control.md#preventing-changes-from-cosmos-sdk)一文。 禁用写访问后，基于 SDK 的吞吐量更改将继续工作。

关闭元数据写入访问权限时，请注意以下几点：

* 评估并确保应用程序不会进行通过使用 SDK 或帐户密钥来更改上述资源（例如，创建集合、更新吞吐量、...）的元数据调用。

* 当前，Azure 门户使用帐户密钥进行元数据操作，因此将阻止这些操作。 此外，还可以使用 Azure CLI、Sdk 或资源管理器模板部署来执行此类操作。

## <a name="enable-diagnostic-logs-for-control-plane-operations"></a>为控制平面操作启用诊断日志

您可以使用 Azure 门户为控制平面操作启用诊断日志。 启用后，诊断日志会将操作记录为包含相关详细信息的一对启动和完整事件。 例如， *RegionFailoverStart*和*RegionFailoverComplete*将完成区域故障转移事件。

使用以下步骤在控制平面操作上启用日志记录：

1. 登录[Azure 门户](https://portal.azure.com)，导航到你的 Azure Cosmos 帐户。

1. 打开 "**诊断设置**" 窗格，提供要创建的日志的**名称**。

1. 选择 " **ControlPlaneRequests** " 作为 "日志类型"，并选择 "**发送到 Log Analytics** " 选项。

你还可以将日志存储在存储帐户或流中。 本文介绍如何将日志发送到 log analytics，并对其进行查询。 启用后，诊断日志需要几分钟才能生效。 可以跟踪在该点之后执行的所有控制平面操作。 以下屏幕截图显示了如何启用控制平面日志：

![启用控制平面请求日志记录](./media/audit-control-plane-logs/enable-control-plane-requests-logs.png)

## <a name="view-the-control-plane-operations"></a>查看控制平面操作

启用日志记录后，请使用以下步骤来跟踪特定帐户的操作：

1. 登录[Azure 门户](https://portal.azure.com)。

1. 从左侧导航栏中打开 "**监视器**" 选项卡，然后选择 "**日志**" 窗格。 它将打开一个 UI，你可以在其中轻松地在范围内运行具有该特定帐户的查询。 运行以下查询以查看控制平面日志：

   ```kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="ControlPlaneRequests"
   | where TimeGenerated >= ago(1h)
   ```

将 VNET 添加到 Azure Cosmos 帐户时，以下屏幕截图捕获日志：

![添加 VNet 时的控制平面日志](./media/audit-control-plane-logs/add-ip-filter-logs.png)

当更新 Cassandra 表的吞吐量时，以下屏幕截图将捕获日志：

![更新吞吐量时的控制平面日志](./media/audit-control-plane-logs/throughput-update-logs.png)

## <a name="identify-the-identity-associated-to-a-specific-operation"></a>标识与特定操作关联的标识

如果要进一步调试，可以通过使用活动 ID 或操作的时间戳来标识**活动日志**中的特定操作。 时间戳用于某些不显式传递活动 ID 的资源管理器客户端。 活动日志提供有关用于启动操作的标识的详细信息。 以下屏幕截图演示了如何使用活动 ID 并在活动日志中查找与它关联的操作：

![使用活动 ID 和查找操作](./media/audit-control-plane-logs/find-operations-with-activity-id.png)

## <a name="control-plane-operations-for-azure-cosmos-account"></a>Azure Cosmos 帐户的控制平面操作

下面是帐户级别可用的控制平面操作。 大多数操作都在帐户级别进行跟踪。 这些操作在 Azure monitor 中作为指标提供：

* 添加的区域
* 删除区域
* 帐户已删除
* 区域故障转移
* 创建的帐户
* 虚拟网络已删除
* 已更新帐户网络设置
* 已更新帐户复制设置
* 帐户密钥已更新
* 已更新帐户备份设置
* 帐户诊断设置已更新

## <a name="control-plane-operations-for-database-or-containers"></a>数据库或容器的控制平面操作

下面是可在数据库和容器级别使用的控制平面操作。 这些操作在 Azure monitor 中作为指标提供：

* SQL 数据库已更新
* 已更新 SQL 容器
* SQL 数据库吞吐量已更新
* 已更新 SQL 容器吞吐量
* SQL 数据库已删除
* SQL 容器已删除
* Cassandra 密钥空间更新
* 已更新 Cassandra 表
* 已更新 Cassandra 密钥空间吞吐量
* 已更新 Cassandra 表吞吐量
* 已删除 Cassandra 密钥空间
* 已删除 Cassandra 表
* 已更新 Gremlin 数据库
* 已更新 Gremlin 图
* 已更新 Gremlin 数据库吞吐量
* 已更新 Gremlin Graph 吞吐量
* Gremlin 数据库已删除
* 已删除 Gremlin 关系图
* 已更新 Mongo 数据库
* Mongo 集合已更新
* 已更新 Mongo 数据库吞吐量
* 已更新 Mongo 集合吞吐量
* Mongo 数据库已删除
* Mongo 集合已删除
* 已更新 AzureTable 表
* 已更新 AzureTable 表吞吐量
* 已删除 AzureTable 表

## <a name="diagnostic-log-operations"></a>诊断日志操作

以下是针对不同操作的诊断日志中的操作名称：

* RegionAddStart, RegionAddComplete
* RegionRemoveStart, RegionRemoveComplete
* AccountDeleteStart, AccountDeleteComplete
* RegionFailoverStart, RegionFailoverComplete
* AccountCreateStart, AccountCreateComplete
* AccountUpdateStart, AccountUpdateComplete
* VirtualNetworkDeleteStart, VirtualNetworkDeleteComplete
* DiagnosticLogUpdateStart, DiagnosticLogUpdateComplete

对于 API 特定的操作，该操作采用以下格式命名：

* /Apikind/+ ApiKindResourceType + OperationType + 开始/完成
* /Apikind/+ ApiKindResourceType + "吞吐量" + operationType + 开始/完成

**示例** 

* CassandraKeyspacesUpdateStart, CassandraKeyspacesUpdateComplete
* CassandraKeyspacesThroughputUpdateStart, CassandraKeyspacesThroughputUpdateComplete

*ResourceDetails*属性包含作为请求负载的整个资源正文，其中包含请求更新的所有属性

## <a name="next-steps"></a>后续步骤

* [探索 Azure Cosmos DB Azure Monitor](../azure-monitor/insights/cosmosdb-insights-overview.md?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json)
* [使用 Azure Cosmos DB 中的指标进行监视和调试](use-metrics.md)
