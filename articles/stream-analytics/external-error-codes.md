---
title: 外部错误代码 - Azure 流分析
description: 通过外部错误代码对 Azure 流分析进行故障排除。
ms.author: mamccrea
author: mamccrea
ms.topic: conceptual
ms.date: 05/07/2020
ms.service: stream-analytics
ms.openlocfilehash: fad0794a3628e6a89d1c2b2c80bb27d70937db95
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83650032"
---
# <a name="azure-stream-analytics-external-error-codes"></a>Azure 流分析外部错误代码

可以使用活动日志和资源日志来帮助调试 Azure 流分析作业中的意外行为。 本文列出了每个外部错误代码的说明。 外部错误是指由上游或下游服务引发的一般错误，流分析无法将这些错误区分为数据错误、配置错误或外部可用性错误。

## <a name="adapterinitializationerror"></a>AdapterInitializationError

* **原因**：初始化适配器时出错。

## <a name="adapterfailedtowriteevents"></a>AdapterFailedToWriteEvents

* **原因**：将数据写入适配器时出错。

## <a name="azurefunctionhttperror"></a>AzureFunctionHttpError

* **原因**：Azure 函数返回了一个 HTTP 错误。

## <a name="azurefunctionfailedtosendmessage"></a>AzureFunctionFailedToSendMessage

* **原因**：流分析未能将事件写入 Azure Functions。

## <a name="azurefunctionredirecterror"></a>AzureFunctionRedirectError

* **原因**：输出到 Azure Functions 时出现重定向错误。

## <a name="azurefunctionclienterror"></a>AzureFunctionClientError

* **原因**：输出到 Azure Functions 时出现客户端错误。

## <a name="azurefunctionservererror"></a>AzureFunctionServerError

* **原因**：输出到 Azure Functions 时出现服务器错误。

## <a name="azurefunctionhttptimeouterror"></a>AzureFunctionHttpTimeOutError

* **原因**：写入 Azure 函数失败，原因是 http 请求超时。 
* **建议**：检查 Azure Functions 日志中是否存在潜在延迟。

## <a name="eventhubargumenterror"></a>EventHubArgumentError

* **原因**：输入偏移量无效。 这可能是由于故障转移造成的。
* **建议**：从上次输出时间重新启动流分析作业。

## <a name="eventhubfailedtowriteevents"></a>EventHubFailedToWriteEvents

* **原因**：向事件中心发送数据时出错。

## <a name="cosmosdbconnectionfailureaftermaxretries"></a>CosmosDBConnectionFailureAfterMaxRetries

* **原因**：在最大重试次数后，流分析无法连接到 Cosmos DB 帐户。

## <a name="cosmosdbfailureaftermaxretries"></a>CosmosDBFailureAfterMaxRetries

* **原因**：在最大重试次数后，流分析无法查询 Cosmos DB 数据库和集合。

## <a name="cosmosdbfailedtocreatestoredprocedure"></a>CosmosDBFailedToCreateStoredProcedure

* **原因**：多次重试后，CosmosDB 无法创建存储过程。

## <a name="cosmosdboutputrequesttimeout"></a>CosmosDBOutputRequestTimeout

* **原因**：upsert 存储过程返回一个错误。 

## <a name="sqldatabaseoutputinitializationerror"></a>SQLDatabaseOutputInitializationError

* **原因**：流分析无法初始化 SQL 数据库输出。

## <a name="sqldatabaseoutputwriteerror"></a>SQLDatabaseOutputWriteError

* **原因**：流分析无法将事件写入 SQL 数据库输出。

## <a name="sqldwoutputinitializationerror"></a>SQLDWOutputInitializationError

* **原因**：初始化 Synapse SQL 池输出时出错。

## <a name="sqldwoutputwriteerror"></a>SQLDWOutputWriteError

* **原因**：将输出写入 Synapse SQL 池时出错。

## <a name="next-steps"></a>后续步骤

* [对输入连接进行故障排除](stream-analytics-troubleshoot-input.md)
* [对 Azure 流分析输出进行故障排除](stream-analytics-troubleshoot-output.md)
* [对 Azure 流分析查询进行故障排除](stream-analytics-troubleshoot-query.md)
* [使用资源日志对 Azure 流分析进行故障排除](stream-analytics-job-diagnostic-logs.md)
* [Azure 流分析数据错误](data-errors.md)
