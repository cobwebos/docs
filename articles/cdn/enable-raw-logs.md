---
title: Azure CDN HTTP 原始日志
description: 本文介绍 Azure CDN HTTP 原始日志。
services: cdn
author: sohamnchatterjee
manager: danielgi
ms.service: azure-cdn
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2020
ms.author: sohamnc
ms.openlocfilehash: f4693cdac2d5d3a57987cc692797756314ded11e
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2020
ms.locfileid: "83747301"
---
# <a name="azure-cdn-http-raw-logs"></a>Azure CDN HTTP 原始日志
原始日志提供了大量有关操作和错误的信息，这些信息对于审核和故障排除非常重要。 原始日志不同于活动日志。 活动日志让你能够了解对 Azure 资源执行的操作。 原始日志提供资源的操作记录。

> [!IMPORTANT]
> HTTP 原始日志功能适用于 Microsoft Azure CDN。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 

## <a name="sign-in-to-azure"></a>登录 Azure

在 [https://portal.azure.com](https://portal.azure.com) 中登录 Azure 门户。

## <a name="configuration"></a>配置

为 Microsoft 配置文件中的 Azure CDN 配置原始日志： 

1. 在 Azure 门户菜单中，选择“所有资源” >>  **\<your-CDN-profile>** 。

2. 在“监视”下，选择“诊断设置” 。

3. 选择“+ 添加诊断设置”。

    ![CDN 诊断设置](./media/cdn-raw-logs/raw-logs-01.png)

    > [!IMPORTANT]
    > 原始日志仅在配置文件级别提供，而聚合的 http 状态代码日志在终结点级别提供。

4. 在“诊断设置”下，在“诊断设置名称”下输入诊断设置的名称 。

5. 选择日志并设置保留天数。

6. 选择“目标详细信息”。 目标选项有：
    * 发送到 Log Analytics
        * 选择“订阅”和“Log Analytics 工作区” 。
    * 存档到存储帐户
        * 选择“订阅”和“存储帐户” 。
    * 流式传输到事件中心
        * 选择“订阅”、“事件中心命名空间”、“事件中心名称（可选）”和“事件中心策略名称”   。

    ![CDN 诊断设置](./media/cdn-raw-logs/raw-logs-02.png)

7. 选择“保存”。

## <a name="raw-logs-properties"></a>原始日志属性

Microsoft 服务中的 Azure CDN 当前提供原始日志。 原始日志提供单个 API 请求，其中每个条目具有以下架构： 

| properties              | 说明                                                                                                                                                                                          |
|-----------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| TrackingReference     | 标识由 Front Door 提供的请求的唯一引用字符串，该请求还会以 X-Azure-Ref 标头的形式发送到客户端。 是搜索特定请求访问日志中的详细信息必需的。 |
| HttpMethod            | 请求使用的 HTTP 方法。                                                                                                                                                                     |
| HttpVersion           | 请求或连接的类型。                                                                                                                                                                   |
| RequestUri            | 已收到请求的 URI。                                                                                                                                                                         |
| RequestBytes          | HTTP 请求消息的大小（以字节为单位），包括请求标头和请求正文。                                                                                                   |
| ResponseBytes         | 后端服务器作为响应发送的字节数。                                                                                                                                                    |
| UserAgent             | 客户端使用的浏览器类型。                                                                                                                                                               |
| ClientIp              | 发出请求的客户端的 IP 地址。                                                                                                                                                  |
| TimeTaken             | 操作所用的时间长度（以毫秒为单位）。                                                                                                                                            |
| SecurityProtocol      | 请求所使用的 TLS/SSL 协议版本，如果没有加密，则为 null。                                                                                                                           |
| 端点              | CDN 终结点主机已在父 CDN 配置文件下进行配置。                                                                                                                                   |
| 后端主机名称     | 发送请求的后端主机或源的名称。                                                                                                                                |
| 发送到源防护 | 如果为 true，则表示请求是从源防护缓存（而不是边缘 pop）响应的。 源防护是用于提高缓存命中率的父缓存。                                       |
| HttpStatusCode        | 从代理返回的 HTTP 状态代码。                                                                                                                                                        |
| HttpStatusDetails     | 请求的结果状态。 此字符串值的含义可以在状态引用表中找到。                                                                                              |
| Pop                   | 响应用户请求的边缘 pop。 POP 的缩写是其各自大城市的机场代码。                                                                                   |
| 缓存状态          | 指示对象是从缓存中返回还是来自源。                                                                                                             |
> [!IMPORTANT]
> 2020 年 2 月 25 日之后创建或更新的任何配置文件可自动使用 HTTP 原始日志功能。 对于之前创建的 CDN 配置文件，应在设置日志记录后更新 CDN 终结点。 例如，可以导航到 CDN 终结点下的地区筛选，并阻止任何与工作负荷无关的国家/地区，并按保存。 

> [!NOTE]
> 可以通过运行查询来查看 Log Analytics 配置文件下的日志。 示例查询类似于               AzureDiagnostics | where Category == "AzureCdnAccessLog"

## <a name="next-steps"></a>后续步骤
本文介绍了如何为 Microsoft CDN 服务启用 HTTP 原始日志。

有关 Azure CDN 和本文中所述的其他 Azure 服务的详细信息，请参阅：

* [分析](cdn-log-analysis.md) Azure CDN 使用模式。

* 详细了解 [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview)。

* 配置 [Azure Monitor 中的 Log Analytics](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)。
