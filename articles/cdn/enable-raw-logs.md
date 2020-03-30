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
ms.openlocfilehash: c6e8570746ae3dd0051dbec084c89d90580d28b1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371633"
---
# <a name="azure-cdn-http-raw-logs"></a>Azure CDN HTTP 原始日志
原始日志提供有关对审核和故障排除非常重要的操作和错误的丰富信息。 原始日志与活动日志不同。 活动日志提供对 Azure 资源上执行的操作的可见性。 原始日志提供资源的操作记录。

> [!IMPORTANT]
> HTTP 原始日志功能可用于 Microsoft 的 Azure CDN。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 

## <a name="sign-in-to-azure"></a>登录 Azure

登录到 中的[https://portal.azure.com](https://portal.azure.com)Azure 门户。

## <a name="configuration"></a>Configuration

要从 Microsoft 配置文件配置 Azure CDN 的原始日志，请执行以下规定： 

1. 在 Azure 门户菜单中，选择**\<CDN 配置文件>****的所有资源** >> 。

2. 在 **"监视"** 下，选择 **"诊断"设置**。

3. 选择“+ 添加诊断设置”。****

    ![CDN 诊断设置](./media/cdn-raw-logs/raw-logs-01.png)

    > [!IMPORTANT]
    > 原始日志仅在配置文件级别中可用，而聚合的 http 状态代码日志在终结点级别中可用。

4. 在**诊断设置**下，在诊断设置名称 下输入**诊断设置的名称**。

5. 选择**日志**并设置保留天数。

6. 选择**目标详细信息**。 目的地选项包括：
    * **发送到 Log Analytics**
        * 选择**订阅**和**日志分析工作区**。
    * **存档到存储帐户**
        * 选择**订阅**和**存储帐户**。
    * **流式传输到事件中心**
        * 选择**订阅**、**事件中心命名空间**、**事件中心名称（可选）** 和**事件中心策略名称**。

    ![CDN 诊断设置](./media/cdn-raw-logs/raw-logs-02.png)

7. 选择“保存”。****

## <a name="raw-logs-properties"></a>原始日志属性

来自 Microsoft 服务的 Azure CDN 当前提供原始日志。 原始日志提供单个 API 请求，每个条目具有以下架构： 

| properties              | 描述                                                                                                                                                                                          |
|-----------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 跟踪参考     | 标识前门提供的请求的唯一引用字符串，也作为 X-Azure-Ref 标头发送到客户端。 需要搜索特定请求的访问日志中的详细信息。 |
| HttpMethod            | 请求使用的 HTTP 方法。                                                                                                                                                                     |
| HttpVersion           | 请求或连接的类型。                                                                                                                                                                   |
| 请求库里            | 已收到请求的 URI。                                                                                                                                                                         |
| 请求字节          | HTTP 请求消息的大小（以字节为单位），包括请求标头和请求正文。                                                                                                   |
| 响应字节         | 后端服务器作为响应发送的字节。                                                                                                                                                    |
| UserAgent             | 客户端使用的浏览器类型。                                                                                                                                                               |
| ClientIp              | 发出请求的客户端的 IP 地址。                                                                                                                                                  |
| TimeTaken             | 操作所花的时间长度（以毫秒为单位）。                                                                                                                                            |
| 安全协议      | 请求使用的 TLS/SSL 协议版本，如果没有加密，则为 null。                                                                                                                           |
| 终结点              | CDN 终结点主机已在父 CDN 配置文件下配置。                                                                                                                                   |
| 后端主机名称     | 发送请求的后端主机或源的名称。                                                                                                                                |
| 发送到源屏蔽 | 如果为 true，则表示请求是从源屏蔽缓存而不是边缘弹出应答的。 原点屏蔽是用于提高缓存命中率的父缓存。                                       |
| HttpStatusCode        | 从代理返回的 HTTP 状态代码。                                                                                                                                                        |
| HttpStatus 详细信息     | 在请求上生成状态。 可在状态引用表中找到此字符串值的含义。                                                                                              |
| 流行                   | 响应用户请求的边缘弹出。 持久性有机污染物的缩写是各自地铁的机场代码。                                                                                   |
| 缓存状态          | 表示对象是从缓存返回的还是来自源的。                                                                                                             |
> [!IMPORTANT]
> HTTP Raw 日志功能可自动用于**2020 年 2 月 25**日之后创建或更新的任何配置文件。 对于之前创建的 CDN 配置文件，应在设置日志记录后更新 CDN 终结点。 例如，可以在 CDN 终结点下导航到地理筛选，并阻止与其工作负载无关的任何国家/地区并命中保存。 

> [!NOTE]
> 可以通过运行查询在日志分析配置文件下查看日志。 示例查询将类似于 Azure 诊断 |其中类别 = "AzureCdn 访问日志"

## <a name="next-steps"></a>后续步骤
在本文中，您为 Microsoft CDN 服务启用了 HTTP 原始日志。

有关 Azure CDN 和本文中提到的其他 Azure 服务的详细信息，请参阅：

* [分析](cdn-log-analysis.md)Azure CDN 使用模式。

* 了解有关[Azure 监视器](https://docs.microsoft.com/azure/azure-monitor/overview)的更多。

* [在 Azure 监视器 中配置日志分析](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)。
