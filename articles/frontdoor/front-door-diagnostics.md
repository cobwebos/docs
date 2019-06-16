---
title: 监视指标和 Azure 第一道防线服务中的日志 |Microsoft Docs
description: 本主题介绍不同的指标和 Azure 第一道防线服务支持的访问日志
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
ms.openlocfilehash: 16770ea0a320b3d9f081cc21a102ab050a6467f6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60736766"
---
# <a name="monitoring-metrics-and-logs-in-azure-front-door-service"></a>监视指标和 Azure 第一道防线服务中的日志

通过使用 Azure 第一道防线服务，可以按下列方式监视资源：

- **指标** 应用程序网关当前有七个指标可用来查看性能计数器。
- **日志**。 活动日志和诊断日志允许性能、 访问和其他数据来保存或出于监视目的从资源使用。

### <a name="metrics"></a>度量值

指标是某些 Azure 资源，可用于在门户中查看性能计数器的功能。 以下是可用的第一道防线指标：

| 指标 | 指标显示名称 | 单位 | 维度 | 描述 |
| --- | --- | --- | --- | --- |
| RequestCount | 请求计数 | Count | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Front Door 服务的客户端请求数。  |
| RequestSize | 请求大小 | 字节 | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | 以请求的形式从客户端发送到 Front Door 的字节数。 |
| ResponseSize | 响应大小 | 字节 | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | 以响应的形式从 Front Door 发送到客户端的字节数。 |
| TotalLatency | 总延迟 | 毫秒 | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | 从客户端请求接收的第一道防线，直到客户端确认从第一道防线的最后一个响应字节计算的时间。 |
| BackendRequestCount | 后端请求计数 | Count | HttpStatus</br>HttpStatusGroup</br>后端 | 从 Front Door 发送到后端的请求数。 |
| BackendRequestLatency | 后端请求延迟 | 毫秒 | 后端 | 自 Front Door 向后端发送请求起，直至 Front Door 接收到来自后端的最后一个响应字节为止，所计算的时间。 |
| BackendHealthPercentage | 后端运行状况百分比 | 百分比 | 后端</br>BackendPool | 从 Front Door 到后端，成功运行状况探测的百分比。 |
| WebApplicationFirewallRequestCount | Web 应用程序防火墙请求计数 | Count | PolicyName</br>RuleName</br>操作 | Front Door 的应用层安全性所处理的客户端请求数。 |

## <a name="activity-log"></a>活动日志

活动日志提供有关操作的第一道防线服务上完成。 它们还决定了什么，谁，以及何时为任何写入第一道防线服务采取的操作 （put、 post 或 delete）。

>[!NOTE]
>活动日志不包括读取 (get) 操作。 它们还不包括使用 Azure 门户或原始管理 API 执行的操作。

在你的第一道防线服务或 Azure 资源在 Azure Monitor 中的所有日志中的访问活动日志。 要查看活动日志，请执行以下操作：

1. 选择你的第一道防线实例。
2. 选择**活动日志**。

    ![活动日志](./media/front-door-diagnostics/activity-log.png)

3. 选择筛选的作用域，然后选择**应用**。

## <a name="diagnostic-logging"></a>诊断日志
诊断日志提供有关操作和错误对于审核和疑难解答很重要的丰富信息。 诊断日志不同于活动日志。

活动日志提供 Azure 资源上执行的操作的见解。 诊断日志提供针对资源执行的操作详细信息。 有关详细信息，请参阅[Azure Monitor 诊断日志](../azure-monitor/platform/diagnostic-logs-overview.md)。

![诊断日志](./media/front-door-diagnostics/diagnostic-log.png)

若要配置你的第一道防线服务诊断日志：

1. 选择 Azure 第一道防线服务。

2. 选择**诊断设置**。

3. 选择“启用诊断”  。 存档到存储帐户的指标和诊断日志、 流式传输到事件中心，或将其发送到 Azure Monitor 日志。

第一道防线服务目前提供诊断日志 （每小时进行批处理）。 诊断日志提供单个 API 请求，其中每个条目具有以下架构：

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

- [创建的第一道防线配置文件](quickstart-create-front-door.md)
- [第一道防线的工作原理](front-door-routing-architecture.md)
