---
title: 如何审核 Azure 宇宙 DB 控制平面操作
description: 了解如何审核在 Azure Cosmos DB 中添加区域、更新吞吐量、区域故障转移、添加 VNet 等控制平面操作
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: sngun
ms.openlocfilehash: 64ad8e6b1101d8486268c857b3a7752e1801f52c
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/31/2020
ms.locfileid: "80420284"
---
# <a name="how-to-audit-azure-cosmos-db-control-plane-operations"></a>如何审核 Azure 宇宙 DB 控制平面操作

控制平面操作包括对 Azure Cosmos 帐户或容器的更改。 例如，创建 Azure Cosmos 帐户、添加区域、更新吞吐量、区域故障转移、添加 VNet 等是一些控制平面操作。 本文介绍如何审核 Azure Cosmos DB 中的控制平面操作。

## <a name="disable-key-based-metadata-write-access"></a>禁用基于密钥的元数据写入访问
 
在审核 Azure Cosmos DB 中的控制平面操作之前，请禁用帐户上的基于密钥的元数据写入访问。 禁用基于密钥的元数据写入访问时，通过帐户密钥连接到 Azure Cosmos 帐户的客户端将被禁止访问该帐户。 通过将属性设置为 true，`disableKeyBasedMetadataWriteAccess`可以禁用写入访问。 设置此属性后，任何资源的更改都可能仅从具有适当的基于角色的访问控制 （RBAC） 角色和凭据的用户进行。 要了解有关如何设置此属性的更多详细信息，请参阅["防止 SDK 更改](role-based-access-control.md#preventing-changes-from-cosmos-sdk)"一文。

 关闭元数据写入访问时请考虑以下几点：

* 评估并确保应用程序不会使用 SDK 或帐户密钥进行更改上述资源的元数据调用（例如，创建集合、更新吞吐量等）。

* 目前，Azure 门户使用帐户密钥进行元数据操作，因此这些操作将被阻止。 或者，使用 Azure CLI、SDK 或资源管理器模板部署来执行此类操作。

## <a name="enable-diagnostic-logs-for-control-plane-operations"></a>为控制平面操作启用诊断日志

可以使用 Azure 门户为控制平面操作启用诊断日志。 使用以下步骤启用控制平面操作上的日志记录：

1. 登录到[Azure 门户](https://portal.azure.com)并导航到 Azure Cosmos 帐户。

1. 打开 **"诊断设置"** 窗格，为要创建的日志提供**名称**。

1. 选择**日志类型的 ControlPlane 请求**，然后选择"**发送到日志分析**"选项。

您还可以将日志存储在存储帐户或流中存储到事件中心。 本文演示如何发送日志以进行日志分析，然后查询它们。 启用后，诊断日志需要几分钟才能生效。 可以跟踪该点之后执行的所有控制平面操作。 以下屏幕截图演示如何启用控制平面日志：

![启用控制平面请求日志记录](./media/audit-control-plane-logs/enable-control-plane-requests-logs.png)

## <a name="view-the-control-plane-operations"></a>查看控制平面操作

打开日志记录后，请使用以下步骤跟踪特定帐户的操作：

1. 登录到[Azure 门户](https://portal.azure.com)。
1. 从左侧导航打开 **"监视器"** 选项卡，然后选择 **"日志"** 窗格。 它打开一个 UI，您可以在其中轻松运行具有该特定帐户的查询。 运行以下查询以查看控制平面日志：

   ```kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="ControlPlaneRequests"
   | where TimeGenerated >= ago(1h)
   ```

将 VNET 添加到 Azure Cosmos 帐户时，以下屏幕截图捕获日志：

![添加 VNet 时控制平面日志](./media/audit-control-plane-logs/add-ip-filter-logs.png)

更新 Cassandra 表的吞吐量时，以下屏幕截图捕获日志：

![更新吞吐量时控制平面日志](./media/audit-control-plane-logs/throughput-update-logs.png)

## <a name="identify-the-identity-associated-to-a-specific-operation"></a>标识与特定操作关联的标识

如果要进一步调试，可以使用活动 ID 或操作的时间戳标识**活动日志**中的特定操作。 时间戳用于某些资源管理器客户端，其中活动 ID 未显式传递。 活动日志提供有关启动操作的标识的详细信息。 以下屏幕截图演示如何使用活动 ID 并在活动日志中找到与其关联的操作：

![使用活动 ID 并查找操作](./media/audit-control-plane-logs/find-operations-with-activity-id.png)

## <a name="next-steps"></a>后续步骤

* [浏览 Azure 宇宙 DB 的 Azure 监视器](../azure-monitor/insights/cosmosdb-insights-overview.md?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json)
* [使用 Azure Cosmos DB 中的指标进行监视和调试](use-metrics.md)