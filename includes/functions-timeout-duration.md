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
ms.openlocfilehash: eca2d3359614875e5bff0c9bb67f006f0a8cdba1
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2020
ms.locfileid: "77198320"
---
## <a name="timeout"></a>函数应用超时持续时间 

函数应用的超时持续时间由[主机 json](../articles/azure-functions/functions-host-json.md#functiontimeout)项目文件中的 `functionTimeout` 属性定义。 下表显示了计划和不同运行时版本的默认值和最大值（以分钟为单位）：

| 计划 | 运行时版本 | 默认 | 最大值 |
|------|---------|---------|---------|
| 用电量 | 1.x | 5 | 10 |
| 用电量 | 2.x | 5 | 10 |
| 用电量 | 3.x | 5 | 10 |
| 高级 | 1.x | 30 | 不受限制 |
| 高级 | 2.x | 30 | 不受限制 |
| 高级 | 3.x | 30 | 不受限制 |
| 应用服务 | 1.x | 不受限制 | 不受限制 |
| 应用服务 | 2.x | 30 | 不受限制 |
| 应用服务 | 3.x | 30 | 不受限制 |

> [!NOTE] 
> 无论函数应用超时设置如何，230秒是 HTTP 触发函数响应请求所需的最长时间。 这是由于[Azure 负载均衡器的默认空闲超时](../articles/app-service/faq-availability-performance-application-issues.md#why-does-my-request-time-out-after-230-seconds)。 对于较长的处理时间，请考虑使用[Durable Functions 异步模式](../articles/azure-functions/durable/durable-functions-overview.md#async-http)或[延迟实际工作，并返回立即响应](../articles/azure-functions/functions-best-practices.md#avoid-long-running-functions)。
