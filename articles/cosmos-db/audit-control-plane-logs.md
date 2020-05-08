---
title: 如何审核 Azure Cosmos DB 控制平面操作
description: 了解如何在 Azure Cosmos DB 中审核控制平面操作，例如添加区域、更新吞吐量、区域故障转移、添加 VNet，等等
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/23/2020
ms.author: sngun
ms.openlocfilehash: a5df7866f7897109dbd7a0ea8a52b857ab671875
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/03/2020
ms.locfileid: "82735345"
---
# <a name="how-to-audit-azure-cosmos-db-control-plane-operations"></a>如何审核 Azure Cosmos DB 控制平面操作

Azure Cosmos DB 中的控制平面是一项 RESTful 服务，可用于对 Azure Cosmos 帐户执行一组不同的操作。 它向最终用户公开公共资源模型（例如数据库、帐户）和各种操作，以便对资源模型执行操作。 控制平面操作包括对 Azure Cosmos 帐户或容器所做的更改。 例如，创建 Azure Cosmos 帐户、添加区域、更新吞吐量、区域故障转移、添加 VNet 等操作都是控制平面操作。 本文介绍如何在 Azure Cosmos DB 中审核控制平面操作。 你可以使用 Azure CLI、PowerShell 或 Azure 门户在 Azure Cosmos 帐户上运行控制平面操作，而对于容器，请使用 Azure CLI 或 PowerShell。

以下是审核控制平面操作有用的一些示例方案：

* 要在修改 Azure Cosmos 帐户的防火墙规则时收到警报。 若要对管理 Azure Cosmos 帐户的网络安全的规则进行未经授权的修改并采取快速操作，此警报是必需的。

* 如果在 Azure Cosmos 帐户中添加或删除了新区域，你希望收到警报。 添加或删除区域会影响计费和数据主权要求。 此警报将帮助你检测你的帐户中的区域的意外添加或删除。

* 你需要从诊断日志获取有关所更改内容的更多详细信息。 例如，VNet 发生了更改。

## <a name="disable-key-based-metadata-write-access"></a>禁用基于密钥的元数据写入访问

在 Azure Cosmos DB 中审核控制平面操作之前，请在帐户中禁用基于密钥的元数据写入访问。 禁用基于密钥的元数据写入访问后，会阻止通过帐户密钥连接到 Azure Cosmos 帐户的客户端访问该帐户。 可以通过将 `disableKeyBasedMetadataWriteAccess` 属性设置为 true 来禁用写入访问。 设置此属性后，可以从具有适当的基于角色的访问控制（RBAC）角色和凭据的用户发生对任何资源的更改。 若要详细了解如何设置此属性，请参阅[阻止从 SDK 进行更改](role-based-access-control.md#preventing-changes-from-cosmos-sdk)一文。 

`disableKeyBasedMetadataWriteAccess`启用后，如果基于 SDK 的客户端运行创建或更新操作，则*不允许对资源 "ContainerNameorDatabaseName" 执行 "操作" POST 操作，因为返回 Azure Cosmos DB 终结点*。 你必须为你的帐户启用此类操作的访问权限，或通过 Azure 资源管理器、Azure CLI 或 Azure Powershell 执行创建/更新操作。 若要切换回，请使用 Azure CLI 将 disableKeyBasedMetadataWriteAccess 设置为**false** ，如[阻止 Cosmos SDK 的更改](role-based-access-control.md#preventing-changes-from-cosmos-sdk)一文中所述。 请确保将的值更改`disableKeyBasedMetadataWriteAccess`为 "false"，而不是 "true"。

禁用元数据写入访问时，请注意以下几点：

* 进行评估，并确保应用程序不会使用 SDK 或帐户密钥发出更改上述资源（例如，创建集合、更新吞吐量等）的元数据调用。

* 目前，Azure 门户会对元数据操作使用帐户密钥，因此这些操作将被阻止。 请使用 Azure CLI、SDK 或资源管理器模板部署来执行此类操作。

## <a name="enable-diagnostic-logs-for-control-plane-operations"></a>对控制平面操作启用诊断日志

可以使用 Azure 门户对控制平面操作启用诊断日志。 启用后，诊断日志会将操作记录为包含相关详细信息的一对启动和完整事件。 例如， *RegionFailoverStart*和*RegionFailoverComplete*将完成区域故障转移事件。

使用以下步骤对控制平面操作启用日志记录：

1. 登录到 [Azure 门户](https://portal.azure.com)，导航到你的 Azure Cosmos 帐户。

1. 打开“诊断设置”窗格，为要创建的日志提供名称。  

1. 选择“ControlPlaneRequests”作为日志类型，然后选择“发送到 Log Analytics”选项。  

还可以将日志存储在存储帐户中，或将其流式传输到事件中心。 本文介绍如何将日志发送到 Log Analytics，然后查询这些日志。 启用诊断日志后，要使诊断日志生效，需要几分钟的时间。 可以跟踪在该时间点之后执行的所有控制平面操作。 以下屏幕截图显示如何启用控制平面日志：

![启用控制平面请求日志记录](./media/audit-control-plane-logs/enable-control-plane-requests-logs.png)

## <a name="view-the-control-plane-operations"></a>查看控制平面操作

启用日志记录后，使用以下步骤跟踪针对特定帐户的操作：

1. 登录到 [Azure 门户](https://portal.azure.com)。

1. 在左侧导航栏中打开“监视”选项卡，然后选择“日志”窗格。   此时会打开一个 UI，可在其中轻松针对该特定帐户按范围运行查询。 运行以下查询以查看控制平面日志：

   ```kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="ControlPlaneRequests"
   | where TimeGenerated >= ago(1h)
   ```

当 Azure Cosmos 帐户的一致性级别发生更改时，以下屏幕截图将捕获日志：

![添加 VNet 时的控制平面日志](./media/audit-control-plane-logs/add-ip-filter-logs.png)

以下屏幕截图捕获了更新 Cassandra 表的吞吐量时的日志：

![更新吞吐量时的控制平面日志](./media/audit-control-plane-logs/throughput-update-logs.png)

## <a name="identify-the-identity-associated-to-a-specific-operation"></a>识别与特定操作关联的标识

若要进一步进行调试，可以使用活动 ID 或者按照操作的时间戳，来识别“活动日志”中的特定操作。  时间戳用于某些未显式传递活动 ID 的资源管理器客户端。 “活动日志”提供有关用于启动操作的标识的详细信息。 以下屏幕截图显示如何使用活动 ID，以及如何在“活动日志”中查找与该 ID 关联的操作：

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
* SqlContainersUpdateStart, SqlContainersUpdateComplete

*ResourceDetails*属性包含作为请求负载的整个资源正文，其中包含请求更新的所有属性

## <a name="diagnostic-log-queries-for-control-plane-operations"></a>控制平面操作的诊断日志查询

下面是为控制平面操作获取诊断日志的一些示例：

```kusto
AzureDiagnostics 
| where Category =="ControlPlaneRequests"
| where  OperationName startswith "SqlContainersUpdateStart"
```

```kusto
AzureDiagnostics 
| where Category =="ControlPlaneRequests"
| where  OperationName startswith "SqlContainersThroughputUpdateStart"
```

## <a name="next-steps"></a>后续步骤

* [探索 Azure Cosmos DB Azure Monitor](../azure-monitor/insights/cosmosdb-insights-overview.md?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json)
* [使用 Azure Cosmos DB 中的指标进行监视和调试](use-metrics.md)
