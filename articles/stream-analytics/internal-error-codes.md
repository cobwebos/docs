---
title: 使用 Azure 流分析错误代码进行故障排除
description: 通过内部错误代码排查 Azure 流分析问题。
ms.author: mamccrea
author: mamccrea
ms.topic: conceptual
ms.date: 05/07/2020
ms.service: stream-analytics
ms.openlocfilehash: c8c792d548fc953cb5d059f322ad1f3139c91562
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83595116"
---
# <a name="azure-stream-analytics-internal-error-codes"></a>Azure 流分析内部错误代码

可以使用活动日志和资源日志来帮助调试 Azure 流分析作业中的意外行为。 本文列出了每个内部错误代码的说明。 内部错误是指当流分析无法区分错误是内部可用性错误还是系统中的 bug 时，流分析平台内引发的一般性错误。

## <a name="kafkainvalidrequest"></a>KafkaInvalidRequest

* **原因**：发送到 Kafka 服务器的请求无效。

## <a name="kafkainputerror"></a>KafkaInputError

* **原因**：Kafka 输入遇到问题。

## <a name="cosmosdboutputbatchsizetoolarge"></a>CosmosDBOutputBatchSizeTooLarge

* **原因**：用于写入 Cosmos DB 的批大小太大。 
* **建议**：使用较小的批大小重试。

## <a name="next-steps"></a>后续步骤

* [对输入连接进行故障排除](stream-analytics-troubleshoot-input.md)
* [对 Azure 流分析输出进行故障排除](stream-analytics-troubleshoot-output.md)
* [对 Azure 流分析查询进行故障排除](stream-analytics-troubleshoot-query.md)
* [使用资源日志对 Azure 流分析进行故障排除](stream-analytics-job-diagnostic-logs.md)
* [Azure 流分析数据错误](data-errors.md)