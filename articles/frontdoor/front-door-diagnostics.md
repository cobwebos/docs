---
title: Azure Front Door 服务 - 指标和日志记录 | Microsoft Docs
description: 本文有助于了解 Azure Front Door 服务支持的不同指标和访问日志
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/18/2018
ms.author: sharadag
ms.openlocfilehash: 5d4d591c465cae91c59e8f86ea9d3e421db0f952
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/18/2019
ms.locfileid: "58112103"
---
# <a name="monitoring-metrics-for-front-door"></a>监视 Front Door 的指标

通过使用 Azure 第一道防线服务，可以按下列方式监视资源：
* [日志](#diagnostic-logging)：通过日志记录，可出于监视目的从资源保存或使用性能、访问及其他数据。

* [指标](#metrics)：应用程序网关当前有七个指标可用来查看性能计数器。

## <a name="metrics"></a>度量值

指标是某些 Azure 资源的一项功能，可在此查看门户中的性能计数器。 对于 Front Door，提供以下指标：

| 指标 | 指标显示名称 | 单位 | 维度 | 描述 |
| --- | --- | --- | --- | --- |
| RequestCount | 请求计数 | Count | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Front Door 服务的客户端请求数。  |
| RequestSize | 请求大小 | 字节 | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | 以请求的形式从客户端发送到 Front Door 的字节数。 |
| ResponseSize | 响应大小 | 字节 | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | 以响应的形式从 Front Door 发送到客户端的字节数。 |
| TotalLatency | 总延迟 | 毫秒 | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | 自 Front Door 接收到客户端请求起，直至客户端确认来自 Front Door 的最后一个响应字节为止，所计算的时间。 |
| BackendRequestCount | 后端请求计数 | Count | HttpStatus</br>HttpStatusGroup</br>后端 | 从 Front Door 发送到后端的请求数。 |
| BackendRequestLatency | 后端请求延迟 | 毫秒 | 后端 | 自 Front Door 向后端发送请求起，直至 Front Door 接收到来自后端的最后一个响应字节为止，所计算的时间。 |
| BackendHealthPercentage | 后端运行状况百分比 | 百分比 | 后端</br>BackendPool | 从 Front Door 到后端，成功运行状况探测的百分比。 |
| WebApplicationFirewallRequestCount | Web 应用程序防火墙请求计数 | Count | PolicyName</br>RuleName</br>操作 | Front Door 的应用层安全性所处理的客户端请求数。 |

## <a name="activity-log"></a>活动日志

活动日志提供了解您指定的地址执行的操作。 使用活动日志，您可以确定"什么，谁，以及何时"的任何写入操作 (PUT、 POST、 DELETE) 执行在第一道防线。

> [!NOTE]
> 活动日志不包括读取 (GET) 操作或者通过 Azure 门户或原始管理 API 执行的操作。

您可以访问活动日志中在第一道防线，或访问 Azure Monitor 中的所有 Azure 资源的日志。 

查看活动日志：

1. 选择你的第一道防线实例。
2. 单击“活动日志”。

    ![活动日志](./media/front-door-diagnostics/activity-log.png)

3. 选择所需的筛选范围，然后单击“应用”。

## <a name="diagnostic-logging"></a>诊断日志
诊断日志提供大量有关操作和错误的信息，这些信息对审核和故障排除非常重要。 诊断日志不同于活动日志。 活动日志提供针对 Azure 资源执行的操作的详细信息。 诊断日志提供资源执行的操作的深入信息。 详细了解如何[Azure Monitor 诊断日志](../azure-monitor/platform/diagnostic-logs-overview.md)。 

若要在第一道防线配置诊断日志：

1. 选择 APIM 服务实例。
2. 单击“诊断设置”。

    ![诊断日志](./media/front-door-diagnostics/diagnostic-log.png)

3. 单击“启用诊断”。 可以将诊断日志与指标一起存档到存储帐户，将其流式传输到事件中心，或者将其发送到 Azure Monitor 日志。 

第一道防线的 azure 服务目前提供诊断日志 （每小时进行批处理），有关单个 API 请求其中每个条目具有以下架构：

| 属性  | 描述 |
| ------------- | ------------- |
| ClientIp | 发出请求的客户端的 IP 地址。 |
| ClientPort | 发出请求的客户端 IP 端口。 |
| HttpMethod | 请求使用的 HTTP 方法。 |
| HttpStatusCode | 从代理返回的 HTTP 状态代码。 |
| HttpStatusDetails | 在请求的生成状态。 在状态引用表，可以找到此字符串值的含义。 |
| HttpVersion | 在请求或连接的类型。 |
| RequestBytes | 以字节为单位，包括请求标头和请求正文的 HTTP 请求消息的大小。 |
| RequestUri | 已收到请求的 URI。 |
| ResponseBytes | 由后端服务器作为响应发送的字节数。  |
| RoutingRuleName | 请求匹配的路由规则的名称。 |
| SecurityProtocol | 使用请求则为 null，如果没有加密的 TLS/SSL 协议版本。 |
| TimeTaken | 执行的操作，以毫秒为单位的时间长度。 |
| UserAgent | 客户端使用的浏览器类型 |
| TrackingReference | 标识由第一道防线，提供服务的请求的唯一引用字符串还可以向客户端发送作为 X Azure Ref 标头。 所需的特定请求的访问日志中搜索的详细信息。 |

## <a name="next-steps"></a>后续步骤

- 了解如何[创建 Front Door](quickstart-create-front-door.md)。
- 了解 [Front Door 的工作原理](front-door-routing-architecture.md)。
