---
title: 在 Azure 前门服务中监视指标和日志 |Microsoft Docs
description: 本文介绍 Azure 前门服务支持的不同指标和访问日志
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
ms.openlocfilehash: 229706ff91b776363d3e9de080e02cee5edf9c77
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2019
ms.locfileid: "71677896"
---
# <a name="monitoring-metrics-and-logs-in-azure-front-door-service"></a>在 Azure 前门服务中监视指标和日志

使用 Azure 前门服务，可以通过以下方式监视资源：

- **指标** Azure 前门目前有7个指标来查看性能计数器。
- **日志**。 活动和诊断日志允许出于监视目的从资源保存或使用性能、访问及其他数据。

### <a name="metrics"></a>指标

度量值是某些 Azure 资源的一项功能，可用于在门户中查看性能计数器。 以下是可用的前门指标：

| 指标 | 指标显示名称 | 单位 | 维度 | 描述 |
| --- | --- | --- | --- | --- |
| RequestCount | 请求计数 | Count | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Front Door 服务的客户端请求数。  |
| RequestSize | 请求大小 | 字节 | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | 以请求的形式从客户端发送到 Front Door 的字节数。 |
| ResponseSize | 响应大小 | 字节 | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | 以响应的形式从 Front Door 发送到客户端的字节数。 |
| TotalLatency | 总延迟 | 毫秒 | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | 由前门接收的客户端请求计算的时间，直到客户端从前门确认最后一个响应字节。 |
| BackendRequestCount | 后端请求计数 | Count | HttpStatus</br>HttpStatusGroup</br>后端 | 从 Front Door 发送到后端的请求数。 |
| BackendRequestLatency | 后端请求延迟 | 毫秒 | 后端 | 自 Front Door 向后端发送请求起，直至 Front Door 接收到来自后端的最后一个响应字节为止，所计算的时间。 |
| BackendHealthPercentage | 后端运行状况百分比 | Percent | 后端</br>BackendPool | 从 Front Door 到后端，成功运行状况探测的百分比。 |
| WebApplicationFirewallRequestCount | Web 应用程序防火墙请求计数 | Count | PolicyName</br>RuleName</br>操作 | Front Door 的应用层安全性所处理的客户端请求数。 |

## <a name="activity-log"></a>活动日志

活动日志提供有关在前门服务上完成的操作的信息。 它们还确定对前门服务执行任何写入操作（put、post 或 delete）的具体内容、人员和时间。

>[!NOTE]
>活动日志不包括读取（get）操作。 它们还不包括使用 Azure 门户或原始管理 API 执行的操作。

在 Azure Monitor 中访问前门服务或 Azure 资源的所有日志中的活动日志。 要查看活动日志，请执行以下操作：

1. 选择前门实例。
2. 选择 "**活动日志**"。

    ![活动日志](./media/front-door-diagnostics/activity-log.png)

3. 选择 "筛选范围"，然后选择 "**应用**"。

## <a name="diagnostic-logging"></a>诊断日志
诊断日志提供了有关操作和错误的丰富信息，这些信息对审核和故障排除非常重要。 诊断日志不同于活动日志。

活动日志可深入了解对 Azure 资源所做的操作。 诊断日志提供资源所执行的操作的见解。 有关详细信息，请参阅[Azure Monitor 诊断日志](../azure-monitor/platform/resource-logs-overview.md)。

![诊断日志](./media/front-door-diagnostics/diagnostic-log.png)

为前门服务配置诊断日志：

1. 选择 Azure 前门服务。

2. 选择 "**诊断设置**"。

3. 选择“启用诊断”。 将诊断日志和指标存档到存储帐户，将其流式传输到事件中心，或将其发送到 Azure Monitor 日志。

前门服务当前提供诊断日志（每小时进行批处理）。 诊断日志提供单个 API 请求，其中每个条目具有以下架构：

| 属性  | 描述 |
| ------------- | ------------- |
| ClientIp | 发出请求的客户端的 IP 地址。 |
| ClientPort | 发出请求的客户端的 IP 端口。 |
| HttpMethod | 请求使用的 HTTP 方法。 |
| HttpStatusCode | 从代理返回的 HTTP 状态代码。 |
| HttpStatusDetails | 请求的结果状态。 可以在状态引用表中找到此字符串值的含义。 |
| HttpVersion | 请求或连接的类型。 |
| RequestBytes | HTTP 请求消息的大小（以字节为单位），包括请求标头和请求正文。 |
| RequestUri | 已收到请求的 URI。 |
| ResponseBytes | 后端服务器作为响应发送的字节数。  |
| RoutingRuleName | 请求匹配的路由规则的名称。 |
| SecurityProtocol | 请求使用的 TLS/SSL 协议版本，如果不加密，则为 null。 |
| TimeTaken | 操作所用的时间长度（以毫秒为单位）。 |
| UserAgent | 客户端使用的浏览器类型 |
| TrackingReference | 标识由前门提供的请求的唯一引用字符串，也以 X Azure 引用标头的形式发送到客户端。 需要用于搜索特定请求的访问日志中的详细信息。 |

## <a name="next-steps"></a>后续步骤

- [创建前门配置文件](quickstart-create-front-door.md)
- [前门的工作方式](front-door-routing-architecture.md)
