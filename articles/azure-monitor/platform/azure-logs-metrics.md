---
title: Azure 中的日志和指标 |Microsoft Docs
description: 概述 Azure 中的诊断日志，提供有关 Azure 资源操作的丰富、频繁的数据。
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 3fe220506e074f881a16c1805d25fb4b39927488
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2019
ms.locfileid: "71262357"
---
# <a name="logs-and-metrics-in-azure"></a>Azure 中的日志和指标
存在不同的[日志](data-platform-logs.md)和[指标](data-platform-metrics.md)

监视 Azure 中的应用程序和服务可以分为存储在[azure 数据平台](data-platform.md)中的应用程序和服务。 

日志和指标可以分为两类

- 无需任何配置即可自动生成的平台日志和指标 



| 层 | 平台日志 | 平台指标 | 自定义日志 | 自定义指标 |
|:---|:---|:---|:---|:---|
| 应用程序  | | | | |
| 来宾操作系统     | 检测信号 |  | 诊断扩展<br>Log Analytics 代理 | 诊断扩展 |
| Resource     | [资源日志](resource-logs-overview.md)<br>（特定于每个服务） | [资源指标](metrics-supported.md)<br>（特定于每个服务） | | [自定义指标](metrics-custom-overview.md) |
| 订阅 | [活动日志](activity-logs-overview.md) | | | |
| 租户       | 

## <a name="next-steps"></a>后续步骤

* [将资源诊断日志流式传输到事件中心](resource-logs-stream-event-hubs.md)
* [使用 Azure Monitor REST API 更改资源诊断设置](https://docs.microsoft.com/rest/api/monitor/)
* [使用 Azure Monitor 分析 Azure 存储中的日志](collect-azure-metrics-logs.md)
