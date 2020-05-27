---
title: 配置错误代码 - Azure 流分析
description: 通过配置错误代码对 Azure 流分析进行故障排除。
ms.author: mamccrea
author: mamccrea
ms.topic: conceptual
ms.date: 05/07/2020
ms.service: stream-analytics
ms.openlocfilehash: 5aa15ae4a234a56a172a0166070c32be4f822910
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83650079"
---
# <a name="azure-stream-analytics-configuration-error-codes"></a>Azure 流分析配置错误代码

可以使用活动日志和资源日志来帮助调试 Azure 流分析作业中的意外行为。 本文列出了每个配置错误代码的说明。 配置错误与作业配置或输入和输出配置相关。

## <a name="eventhubunauthorizedaccess"></a>EventHubUnauthorizedAccess

* **原因**：事件中心引发“未经授权访问”错误。

## <a name="eventhubreceiverepochconflict"></a>EventHubReceiverEpochConflict

* **原因**：存在多个具有不同 epoch 值的事件中心接收器。
* **建议**：当流分析作业正在运行时，请确保 Service Bus Explorer 或 EventProcessorHost 应用程序未连接。

## <a name="eventhubreceiverquotaexceeded"></a>EventHubReceiverQuotaExceeded

* **原因**：流分析无法连接到分区，因为已达到使用者组中每个分区允许的最大接收器数。
* **建议**：请确保其他流分析作业或 Service Bus Explorer 未使用相同的使用者组。

## <a name="eventhuboutputthrottled"></a>EventHubOutputThrottled

* **原因**：由于限制将数据写入事件中心时出错。
* **建议**：如果这种情况持续发生，请升级吞吐量。

## <a name="eventhuboutputinvalidconnectionconfig"></a>EventHubOutputInvalidConnectionConfig

* **原因**：提供的连接配置不正确。
* **建议**：更正配置并重新启动作业。

## <a name="eventhuboutputinvalidhostname"></a>EventHubOutputInvalidHostname

* **原因**：事件中心主机不可访问。
* **建议**：请确保提供的主机名正确。

## <a name="eventhuboutputunexpectedpartitioncount"></a>EventHubOutputUnexpectedPartitionCount

* **原因**：EventHub 发送方遇到了意外 EventHub 分区计数。
* **建议**：如果 EventHub 的分区计数已更改，请重新启动流分析作业。

## <a name="cosmosdbpartitionkeynotfound"></a>CosmosDBPartitionKeyNotFound

* **原因**：流分析在数据库中找不到特定 Cosmos DB 集合的分区键。
* **建议**：确保为 Cosmos DB 中的集合指定了有效分区键。

## <a name="cosmosdbinvalidpartitionkeycolumn"></a>CosmosDBInvalidPartitionKeyColumn

* **原因**：当分区键既不是叶节点也不是顶级节点时引发。

## <a name="cosmosdbinvalididcolumn"></a>CosmosDBInvalidIdColumn

* **原因**：如果另一列被选为主键属性，查询输出中就不得包含列 \[id]。

## <a name="cosmosdbdatabasenotfound"></a>CosmosDBDatabaseNotFound

* **原因**：流分析找不到 CosmosDB 数据库。

## <a name="cosmosdbcollectionnotfound"></a>CosmosDBCollectionNotFound

* **原因**：流分析在数据库中找不到特定的 Cosmos DB 集合。

## <a name="cosmosdboutputwritethrottling"></a>CosmosDBOutputWriteThrottling

* **原因**：由于 Cosmos DB 的限制，写入数据时出错。
* **建议**：升级集合性能层并调整数据库的性能。

## <a name="sqldatabaseconnectionstringerror"></a>SQLDatabaseConnectionStringError

* **原因**：流分析作业遇到身份验证错误。
* **建议**：请确保 SQL 数据库连接字符串正确。

## <a name="sqldatabasemanagedidentityauthenticationerror"></a>SQLDatabaseManagedIdentityAuthenticationError

* **原因**：流分析作业遇到身份验证错误。 
* **建议**：请确保帐户名的配置正确无误，且作业的托管标识有权访问 SQL 数据库。

## <a name="sqldatabaseoutputnotableerror"></a>SQLDatabaseOutputNoTableError

* **原因**：流分析找不到特定表的架构信息。

## <a name="sqldwoutputinvalidserviceedition"></a>SQLDWOutputInvalidServiceEdition

* **原因**：不支持 SQL 数据库。
* **建议**：使用 Synapse SQL 池。

## <a name="next-steps"></a>后续步骤

* [对输入连接进行故障排除](stream-analytics-troubleshoot-input.md)
* [对 Azure 流分析输出进行故障排除](stream-analytics-troubleshoot-output.md)
* [对 Azure 流分析查询进行故障排除](stream-analytics-troubleshoot-query.md)
* [使用资源日志对 Azure 流分析进行故障排除](stream-analytics-job-diagnostic-logs.md)
* [Azure 流分析数据错误](data-errors.md)
