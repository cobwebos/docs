---
title: include 文件
description: include 文件
services: functions
author: nzthiago
ms.service: azure-functions
ms.topic: include
ms.date: 02/21/2018
ms.author: nzthiago
ms.custom: include file
ms.openlocfilehash: ffb29fc76313e8870b52cb0a63936da7853ea6ce
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2019
ms.locfileid: "58305170"
---
## <a name="timeout"></a>函数应用超时持续时间 

超时持续时间的函数应用定义中的 functionTimeout 属性[host.json](../articles/azure-functions/functions-host-json.md#functiontimeout)项目文件。 下表显示的默认和最大值，在这两个计划的分钟和这两个运行时版本：

| 计划 | 运行时版本 | 默认 | 最大值 |
|------|---------|---------|---------|
| 消耗 | 1.x | 5 | 10 |
| 消耗 | 2.x | 5 | 10 |
| 应用服务 | 1.x | 不受限制 | 不受限制 |
| 应用服务 | 2.x | 30 | 不受限制 |

> [!NOTE] 
> 而不考虑函数应用超时设置，230 秒是时间的最大 HTTP 触发的函数可能需要对请求作出响应量。 这是由于[默认的 Azure 负载均衡器的空闲超时](../articles/app-service/faq-availability-performance-application-issues.md#why-does-my-request-time-out-after-230-seconds)。 对于处理时间较长，请考虑使用[Durable Functions 异步模式](../articles/azure-functions/durable/durable-functions-concepts.md#async-http)或[延迟实际工作并返回即时响应](../articles/azure-functions/functions-best-practices.md#avoid-long-running-functions)。
