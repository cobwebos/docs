---
title: Azure CDN HTTP 原始日志
description: 本文介绍 Azure CDN HTTP 原始日志。
services: cdn
author: asudbring
manager: KumudD
ms.service: azure-cdn
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2020
ms.author: allensu
ms.openlocfilehash: 79ced4df8df12bf2ef1fbe0075b53d02fafb2aff
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/11/2020
ms.locfileid: "79129849"
---
# <a name="azure-cdn-http-raw-logs"></a>Azure CDN HTTP 原始日志
原始日志提供了有关操作和错误的丰富信息，这些信息对审核和故障排除非常重要。 原始日志不同于活动日志。 活动日志提供对 Azure 资源执行的操作的可见性。 原始日志提供资源的操作记录。

> [!IMPORTANT]
> HTTP raw 日志功能可用于从 Microsoft Azure CDN。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 

## <a name="sign-in-to-azure"></a>登录 Azure

在 [https://portal.azure.com](https://portal.azure.com) 中登录 Azure 门户。

## <a name="configuration"></a>配置

为 Microsoft 配置文件中的 Azure CDN 配置原始日志： 

1. 从 "Azure 门户" 菜单中，选择 "**所有资源**" >>  **\<的 CDN 配置文件 >** 。

2. 在 "**监视**" 下，选择 "**诊断设置**"。

3. 选择 " **+ 添加诊断设置**"。

    ![CDN 诊断设置](./media/cdn-raw-logs/raw-logs-01.png)

    > [!IMPORTANT]
    > 原始日志仅在配置文件级别提供，而聚合的 http 状态代码日志在终结点级别可用。

4. 在 "**诊断设置**" 下的 "**诊断设置名称**" 下输入诊断设置的名称。

5. 选择**日志**并设置保留天数。

6. 选择**目标详细信息**。 目标选项包括：
    * **发送到 Log Analytics**
        * 选择 "**订阅**" 和 " **Log Analytics" 工作区**。
    * **存档到存储帐户**
        * 选择**订阅**和**存储帐户**。
    * **流式传输到事件中心**
        * 选择 "**订阅**"、"**事件中心命名空间**"、"**事件中心名称（可选）** " 和 "**事件中心策略名称**"。

    ![CDN 诊断设置](./media/cdn-raw-logs/raw-logs-02.png)

7. 选择“保存”。

## <a name="raw-logs-properties"></a>原始日志属性

Microsoft 服务中的 Azure CDN 当前提供原始日志（每小时进行批处理）。 原始日志提供单个 API 请求，其中每个条目具有以下架构： 

| properties              | 说明                                                                                                                                                                                          |
|-----------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| TrackingReference     | 标识由前门提供的请求的唯一引用字符串，也以 X Azure 引用标头的形式发送到客户端。 需要用于搜索特定请求的访问日志中的详细信息。 |
| HttpMethod            | 请求使用的 HTTP 方法。                                                                                                                                                                     |
| HttpVersion           | 请求或连接的类型。                                                                                                                                                                   |
| RequestUri            | 已收到请求的 URI。                                                                                                                                                                         |
| RequestBytes          | HTTP 请求消息的大小（以字节为单位），包括请求标头和请求正文。                                                                                                   |
| ResponseBytes         | 后端服务器作为响应发送的字节数。                                                                                                                                                    |
| UserAgent             | 客户端使用的浏览器类型。                                                                                                                                                               |
| ClientIp              | 发出请求的客户端的 IP 地址。                                                                                                                                                  |
| TimeTaken             | 操作所用的时间长度（以毫秒为单位）。                                                                                                                                            |
| SecurityProtocol      | 请求使用的 TLS/SSL 协议版本，如果不加密，则为 null。                                                                                                                           |
| 端点              | CDN 终结点主机已在父 CDN 配置文件下进行配置。                                                                                                                                   |
| 后端主机名     | 要向其发送请求的后端主机或源的名称。                                                                                                                                |
| 发送到源盾牌 | 如果为 true，则表示请求是从源盾牌缓存而不是边缘 pop 应答的。 源盾牌是用于提高缓存命中率的父缓存。                                       |
| HttpStatusCode        | 从代理返回的 HTTP 状态代码。                                                                                                                                                        |
| HttpStatusDetails     | 请求的结果状态。 可以在状态引用表中找到此字符串值的含义。                                                                                              |
| 弹出                   | 响应用户请求的边缘 pop。 Pop 的缩写是其各自 metros 的机场代码。                                                                                   |
| 缓存状态          | 指示对象是从缓存中返回还是来自源。                                                                                                             |

## <a name="next-steps"></a>后续步骤
本文介绍了如何为 Microsoft CDN 服务启用 HTTP 原始日志。

有关 Azure CDN 和本文中所述的其他 Azure 服务的详细信息，请参阅：

* [分析](cdn-log-analysis.md)Azure CDN 使用模式。

* 详细了解 [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview)。

* [在 Azure Monitor 中配置 Log Analytics](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)。