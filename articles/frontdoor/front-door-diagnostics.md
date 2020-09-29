---
title: 在 Azure 前门中监视指标和日志 |Microsoft Docs
description: 本文介绍 Azure 前门支持的不同指标和访问日志
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: duau
ms.openlocfilehash: a1e77b5f669d1b492f2d71063a6c77bec1178696
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91449284"
---
# <a name="monitoring-metrics-and-logs-in-azure-front-door"></a>在 Azure 前门中监视指标和日志

使用 Azure 前门，可以通过以下方式监视资源：

- **指标** Azure 前门目前有8个指标来查看性能计数器。
- **日志**。 活动和诊断日志允许出于监视目的从资源保存或使用性能、访问及其他数据。

### <a name="metrics"></a>指标

度量值是某些 Azure 资源的一项功能，可用于在门户中查看性能计数器。 以下是可用的前门指标：

| 指标 | 指标显示名称 | 计价单位 | 维度 | 说明 |
| --- | --- | --- | --- | --- |
| RequestCount | 请求计数 | Count | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Front Door 服务的客户端请求数。  |
| RequestSize | 请求大小 | 字节 | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | 以请求的形式从客户端发送到 Front Door 的字节数。 |
| ResponseSize | 响应大小 | 字节 | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | 以响应的形式从 Front Door 发送到客户端的字节数。 |
| TotalLatency | 总延迟 | 毫秒 | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | 由前门接收的客户端请求计算的时间，直到客户端从前门确认最后一个响应字节。 |
| BackendRequestCount | 后端请求计数 | Count | HttpStatus</br>HttpStatusGroup</br>后端 | 从 Front Door 发送到后端的请求数。 |
| BackendRequestLatency | 后端请求延迟 | 毫秒 | 后端 | 自 Front Door 向后端发送请求起，直至 Front Door 接收到来自后端的最后一个响应字节为止，所计算的时间。 |
| BackendHealthPercentage | 后端运行状况百分比 | 百分比 | 后端</br>BackendPool | 从 Front Door 到后端，成功运行状况探测的百分比。 |
| WebApplicationFirewallRequestCount | Web 应用程序防火墙请求计数 | Count | PolicyName</br>RuleName</br>操作 | Front Door 的应用层安全性所处理的客户端请求数。 |

## <a name="activity-logs"></a><a name="activity-log"></a>活动日志

活动日志提供有关在前门上完成的操作的信息。 它们还决定了任何写入操作 (put、post 或 delete) 在前门上执行的操作。

>[!NOTE]
>活动日志不包括读取 (获取) 操作。 它们还不包括使用 Azure 门户或原始管理 API 执行的操作。

在 Azure Monitor 中访问你的前端或 Azure 资源的所有日志中的活动日志。 要查看活动日志，请执行以下操作：

1. 选择前门实例。
2. 选择“活动日志”。

    :::image type="content" source="./media/front-door-diagnostics/activity-log.png" alt-text="活动日志&quot;:::

3. 选择 &quot;筛选范围&quot;，然后选择 " **应用**"。

## <a name="diagnostic-logs"></a><a name="diagnostic-logging"></a>诊断日志
诊断日志提供了有关操作和错误的丰富信息，这些信息对审核和故障排除非常重要。 诊断日志不同于活动日志。

活动日志可深入了解对 Azure 资源所做的操作。 诊断日志提供资源已完成的操作的见解。 有关详细信息，请参阅 [Azure Monitor 诊断日志](../azure-monitor/platform/platform-logs-overview.md)。

:::image type="content" source="./media/front-door-diagnostics/diagnostic-log.png" alt-text="活动日志&quot;:::

3. 选择 &quot;筛选范围&quot;，然后选择 " **诊断设置**"。

3. 选择“启用诊断”。 将诊断日志和指标存档到存储帐户，将其流式传输到事件中心，或将其发送到 Azure Monitor 日志。

前门目前提供的诊断日志 (按小时批处理) 。 诊断日志提供单个 API 请求，其中每个条目具有以下架构：

| 属性  | 说明 |
| ------------- | ------------- |
| BackendHostname | 如果将请求转发到后端，此字段表示后端的主机名。 如果为路由规则) 启用了缓存，则此字段将为空（如果请求被重定向或转发到区域 (缓存）。 |
| CacheStatus | 对于缓存方案，此字段定义了弹出窗口中的缓存命中/未命中 |
| ClientIp | 发出请求的客户端的 IP 地址。 如果请求中有一个 X 转发的标头，则从同一获取客户端 IP。 |
| ClientPort | 发出请求的客户端的 IP 端口。 |
| HttpMethod | 请求使用的 HTTP 方法。 |
| HttpStatusCode | 从代理返回的 HTTP 状态代码。 |
| HttpStatusDetails | 请求的结果状态。 此字符串值的含义可以在状态引用表中找到。 |
| HttpVersion | 请求或连接的类型。 |
| POP | 请求着陆的边缘的短名称。 |
| RequestBytes | HTTP 请求消息的大小（以字节为单位），包括请求标头和请求正文。 |
| RequestUri | 已收到请求的 URI。 |
| ResponseBytes | 后端服务器作为响应发送的字节数。  |
| RoutingRuleName | 请求匹配的路由规则的名称。 |
| RulesEngineMatchNames | 请求匹配的规则的名称。 |
| SecurityProtocol | 请求所使用的 TLS/SSL 协议版本，如果没有加密，则为 null。 |
| SentToOriginShield | 布尔值字段，表示在第一个环境上是否出现缓存未命中，并向区域缓存发送请求。 如果路由规则是重定向或未启用缓存，则忽略此字段。 |
| TimeTaken | 从请求的第一个字节到响应的最后一个字节的时间长度（以秒为单位）。 |
| TrackingReference | 标识由 Front Door 提供的请求的唯一引用字符串，该请求还会以 X-Azure-Ref 标头的形式发送到客户端。 是搜索特定请求访问日志中的详细信息必需的。 |
| UserAgent | 客户端使用的浏览器类型。 |

**注意：** 对于各种路由配置和流量行为，某些字段（如 backendHostname、cacheStatus、sentToOriginShield 和 POP 字段）可能会用不同的值来响应。 下表说明了不同的值，这些字段适用于各种方案：

| 方案 | 日志条目计数 | POP | BackendHostname | SentToOriginShield | CacheStatus |
| ------------- | ------------- | ------------- | ------------- | ------------- | ------------- |
| 未启用缓存的路由规则 | 1 | 边缘 POP 代码 | 转发请求的后端 | 错误 | CONFIG_NOCACHE |
| 启用了缓存的路由规则。 在边缘 POP 处命中缓存 | 1 | 边缘 POP 代码 | 空 | 错误 | 命中 |
| 启用了缓存的路由规则。 边缘 POP 处的缓存未命中，但在父缓存 POP 处命中缓存 | 2 | 1. 边缘 POP 代码</br>2. 父缓存 POP 代码 | 1. 父缓存 POP 主机名</br>2。空 | 1. True</br>2. False | 1. 未命中</br>2. PARTIAL_HIT |
| 启用了缓存的路由规则。 边缘和父缓存弹出窗口中的缓存未命中 | 2 | 1. 边缘 POP 代码</br>2. 父缓存 POP 代码 | 1. 父缓存 POP 主机名</br>2. 帮助填充缓存的后端 | 1. True</br>2. False | 1. 未命中</br>2. 未命中 |

## <a name="next-steps"></a>后续步骤

- [创建 Front Door 配置文件](quickstart-create-front-door.md)
- [前门的工作方式](front-door-routing-architecture.md)
