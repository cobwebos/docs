---
title: Azure 前门中的监视指标和日志*微软文档
description: 本文介绍 Azure 前门支持的不同指标和访问日志
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
ms.openlocfilehash: b935355cce36a6e26b168db286ab40248f8f0f68
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471721"
---
# <a name="monitoring-metrics-and-logs-in-azure-front-door"></a>监视 Azure 前门中的指标和日志

通过使用 Azure 前门，可以通过以下方式监视资源：

- **指标**. Azure 前门目前有七个指标用于查看性能计数器。
- **日志**。 活动和诊断日志允许从资源中保存或使用性能、访问和其他数据，以便进行监视。

### <a name="metrics"></a>指标

指标是某些 Azure 资源的一项功能，允许您在门户中查看性能计数器。 以下是可用的前门指标：

| 指标 | 指标显示名称 | 单位 | 维度 | 描述 |
| --- | --- | --- | --- | --- |
| RequestCount | 请求计数 | Count | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Front Door 服务的客户端请求数。  |
| RequestSize | 请求大小 | 字节 | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | 以请求的形式从客户端发送到 Front Door 的字节数。 |
| ResponseSize | 响应大小 | 字节 | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | 以响应的形式从 Front Door 发送到客户端的字节数。 |
| TotalLatency | 总延迟 | 毫秒 | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | 从前门收到的客户端请求计算的时间，直到客户端从前门确认最后一个响应字节。 |
| BackendRequestCount | 后端请求计数 | Count | HttpStatus</br>HttpStatusGroup</br>后端 | 从 Front Door 发送到后端的请求数。 |
| BackendRequestLatency | 后端请求延迟 | 毫秒 | 后端 | 自 Front Door 向后端发送请求起，直至 Front Door 接收到来自后端的最后一个响应字节为止，所计算的时间。 |
| BackendHealthPercentage | 后端运行状况百分比 | 百分比 | 后端</br>BackendPool | 从 Front Door 到后端，成功运行状况探测的百分比。 |
| WebApplicationFirewallRequestCount | Web 应用程序防火墙请求计数 | Count | PolicyName</br>RuleName</br>操作 | Front Door 的应用层安全性所处理的客户端请求数。 |

## <a name="activity-logs"></a><a name="activity-log"></a>活动日志

活动日志提供有关前门上执行的操作的信息。 它们还确定前门上执行的任何写入操作（放置、发布或删除）的内容、人员以及时间。

>[!NOTE]
>活动日志不包括读取（获取）操作。 它们也不包括使用 Azure 门户或原始管理 API 执行的操作。

访问前门的活动日志或 Azure 监视器中 Azure 资源的所有日志。 要查看活动日志，请执行以下操作：

1. 选择前门实例。
2. 选择**活动日志**。

    ![活动日志](./media/front-door-diagnostics/activity-log.png)

3. 选择筛选范围，然后选择 **"应用**"。

## <a name="diagnostic-logs"></a><a name="diagnostic-logging"></a>诊断日志
诊断日志提供有关对审核和故障排除非常重要的操作和错误的丰富信息。 诊断日志与活动日志不同。

活动日志提供对 Azure 资源执行操作的见解。 诊断日志提供对资源执行的操作的见解。 有关详细信息，请参阅[Azure 监视器诊断日志](../azure-monitor/platform/platform-logs-overview.md)。

![诊断日志](./media/front-door-diagnostics/diagnostic-log.png)

要配置前门的诊断日志：：

1. 选择 Azure 前门。

2. 选择**诊断设置**。

3. 选择“启用诊断”****。 将诊断日志和指标以及指标存档到存储帐户，将它们流式传输到事件中心，或将它们发送到 Azure 监视器日志。

前门当前提供诊断日志（每小时批处理）。 诊断日志提供单个 API 请求，每个条目具有以下架构：

| properties  | 描述 |
| ------------- | ------------- |
| 后端主机名 | 如果请求被转发到后端，则此字段表示后端的主机名。 如果请求重定向或转发到区域缓存（为路由规则启用缓存时），此字段将为空。 |
| 缓存状态 | 对于缓存方案，此字段定义 POP 处的缓存命中/错过 |
| ClientIp | 发出请求的客户端的 IP 地址。 如果请求中有 X-前转 -For 标头，则从同一标头中选取客户端 IP。 |
| ClientPort | 发出请求的客户端的 IP 端口。 |
| HttpMethod | 请求使用的 HTTP 方法。 |
| HttpStatusCode | 从代理返回的 HTTP 状态代码。 |
| HttpStatus 详细信息 | 在请求上生成状态。 可在状态引用表中找到此字符串值的含义。 |
| HttpVersion | 请求或连接的类型。 |
| POP | 请求着陆边缘的简短名称。 |
| 请求字节 | HTTP 请求消息的大小（以字节为单位），包括请求标头和请求正文。 |
| 请求库里 | 已收到请求的 URI。 |
| 响应字节 | 后端服务器作为响应发送的字节。  |
| 路由规则名称 | 请求匹配的路由规则的名称。 |
| 安全协议 | 请求使用的 TLS/SSL 协议版本，如果没有加密，则为 null。 |
| 已发送到原始盾 | 布尔字段表示第一个环境中是否存在缓存未命中，并且请求已发送到区域缓存。 如果路由规则是重定向或未启用缓存时，请忽略此字段。 |
| TimeTaken | 操作所花的时间长度（以毫秒为单位）。 |
| 跟踪参考 | 标识前门提供的请求的唯一引用字符串，也作为 X-Azure-Ref 标头发送到客户端。 需要搜索特定请求的访问日志中的详细信息。 |
| UserAgent | 客户端使用的浏览器类型。 |

**注：** 对于各种路由配置和流量行为，某些字段（如后端主机名、缓存状态、发送到原体屏蔽和 POP 字段）可能会以不同的值响应。 下表解释了不同的值，这些字段将具有各种方案：

| 方案 | 日志条目计数 | POP | 后端主机名 | 已发送到原始盾 | 缓存状态 |
| ------------- | ------------- | ------------- | ------------- | ------------- | ------------- |
| 未启用缓存的路由规则 | 1 | 边缘 POP 代码 | 转发请求的后端 | False | CONFIG_NOCACHE |
| 启用缓存的路由规则。 缓存命中边缘 POP | 1 | 边缘 POP 代码 | 空 | False | 打 |
| 启用缓存的路由规则。 在边缘 POP 处缓存未命中，但在父缓存 POP 处命中缓存 | 2 | 1. 边缘 POP 代码</br>2. 父缓存 POP 代码 | 1. 父缓存 POP 主机名</br>2. 空 | 1. 真实</br>2. 假 | 1. 小姐</br>2. PARTIAL_HIT |
| 启用缓存的路由规则。 边缘缓存和父缓存 POP 的缓存错过 | 2 | 1. 边缘 POP 代码</br>2. 父缓存 POP 代码 | 1. 父缓存 POP 主机名</br>2. 有助于填充缓存的后端 | 1. 真实</br>2. 假 | 1. 小姐</br>2. 小姐 |


## <a name="next-steps"></a>后续步骤

- [创建 Front Door 配置文件](quickstart-create-front-door.md)
- [前门的工作原理](front-door-routing-architecture.md)
