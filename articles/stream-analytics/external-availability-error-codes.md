---
title: 外部可用性错误代码 - Azure 流分析
description: 通过外部可用性错误代码排查 Azure 流分析问题。
ms.author: mamccrea
author: mamccrea
ms.topic: troubleshooting
ms.date: 05/07/2020
ms.service: stream-analytics
ms.openlocfilehash: c70a3a2d0630148a077f9c149ba40d48a6b7c0ae
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86045274"
---
# <a name="azure-stream-analytics-external-availability-error-codes"></a>Azure 流分析外部可用性错误代码

可以使用活动日志和资源日志来帮助调试 Azure 流分析作业中的意外行为。 本文列出了每个外部可用性错误代码的说明。 依赖服务不可用时，会发生外部可用性错误。

## <a name="externalserviceunavailable"></a>ExternalServiceUnavailable

* **原因**：服务暂时不可用。
* **建议**：流分析将继续尝试访问该服务。

## <a name="eventhubmessagingerror"></a>EventHubMessagingError

* **原因**：流分析在与 EventHub 通信时遇到错误。 


## <a name="next-steps"></a>后续步骤

* [对输入连接进行故障排除](stream-analytics-troubleshoot-input.md)
* [对 Azure 流分析输出进行故障排除](stream-analytics-troubleshoot-output.md)
* [对 Azure 流分析查询进行故障排除](stream-analytics-troubleshoot-query.md)
* [使用资源日志对 Azure 流分析进行故障排除](stream-analytics-job-diagnostic-logs.md)
* [Azure 流分析数据错误](data-errors.md)
