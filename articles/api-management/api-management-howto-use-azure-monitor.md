---
title: 在 Azure API 管理中监视已发布的 API | Microsoft Docs
description: 遵循本教程的步骤了解如何在 Azure API 管理中监视 API。
services: api-management
author: vladvino
manager: cfowler
ms.service: api-management
ms.workload: mobile
ms.custom: mvc
ms.topic: tutorial
ms.date: 06/15/2018
ms.author: apimpm
ms.openlocfilehash: bee93cf84f4beda0684127102942447630219881
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82128846"
---
# <a name="monitor-published-apis"></a>监视已发布的 API

通过 Azure Monitor，可直观显示、查询、路由和存档来自 Azure 资源的指标或日志并对其执行操作。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 查看活动日志
> * 查看资源日志
> * 查看 API 的指标 
> * 针对 API 收到的未经授权的调用设置警报规则

下方视频介绍如何使用 Azure Monitor 监视 API 管理。 

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Monitor-API-Management-with-Azure-Monitor/player]

## <a name="prerequisites"></a>先决条件

+ 了解 [Azure API 管理术语](api-management-terminology.md)。
+ 请完成以下快速入门：[创建一个 Azure API 管理实例](get-started-create-service-instance.md)。
+ 此外，请完成以下教程：[导入和发布第一个 API](import-and-publish.md)。

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="view-metrics-of-your-apis"></a>查看 API 的指标

API 管理每分钟发出一次指标，几乎可让你实时了解 API 的状态和运行状况。 下面汇总了一些可用指标：

* 容量：帮助做出有关升级/降级 APIM 服务的决策。 指标每分钟发出，在报告时反映网关容量。 指标范围为 0-100，是根据 CPU 和内存利用率等网关资源计算的。
* 网关请求总数：期间内的 API 请求数。 
* 成功的网关请求数：接收成功的 HTTP 响应代码（包括 304、307 以及任何小于 301 的代码，例如 200）的 API 请求数。
* 失败的网关请求数：接收错误的 HTTP 响应代码（包括 400 以及任何大于 500 的代码）的 API 请求数。
* 未经授权的网关请求数：接收包括 401、403 和 429的 HTTP 响应代码的 API 请求数。
* 其他网关请求数：接收不属于上述任何类别的 HTTP 响应代码（例如 418）的 API 请求数。

![指标图表](./media/api-management-azure-monitor/apim-monitor-metrics.png)

访问指标：

1. 在靠近页面底部的菜单中选择“指标”。 

    ![指标](./media/api-management-azure-monitor/api-management-metrics-blade.png)

1. 从下拉列表中选择所需的指标。 例如，“请求”  。 
1. 该图显示 API 调用总数。
1. 可以使用**请求**指标的维度来筛选该图表。 例如，单击“添加筛选器”  ，选择“后端响应代码”  ，输入 500 作为值。 现在，该图表显示了 API 后端中失败的请求数。   

## <a name="set-up-an-alert-rule-for-unauthorized-request"></a>针对未经授权的请求设置警报规则

可配置为基于指标和活动日志接收警报。 通过 Azure Monitor 可配置警报，使警报触发时执行以下操作：

* 发送电子邮件通知
* 调用 Webhook
* 调用 Azure 逻辑应用

配置警报：

1. 在靠近页面底部的菜单栏中选择“警报”。 

    ![alerts](./media/api-management-azure-monitor/alert-menu-item.png)

2. 对于此警报，请单击“新建警报规则”。 
3. 单击“添加条件”。 
4. 在“信号类型”下拉列表中选择“指标”。 
5. 选择“未经授权的网关请求”作为要监视的信号。 

    ![alerts](./media/api-management-azure-monitor/signal-type.png)

6. 在“配置信号逻辑”视图中指定触发警报的阈值，然后单击“完成”。  

    ![alerts](./media/api-management-azure-monitor/threshold.png)

7. 选择现有的操作组或创建新组。 在下面的示例中，将向管理员发送电子邮件。 

    ![alerts](./media/api-management-azure-monitor/action-details.png)

8. 提供警报规则的名称和说明，然后选择严重级别。 
9. 按“创建警报规则”。 
10. 现在，尝试在不使用 API 密钥的情况下调用会议 API。 将会触发警报，向管理员发送电子邮件。 

## <a name="activity-logs"></a>活动日志

活动日志提供有关对 API 管理服务执行的操作的见解。 通过活动日志，可确定对 API 管理服务执行的任何写入操作 (PUT、POST、DELETE) 的“操作内容、操作人员和操作时间”。

> [!NOTE]
> 活动日志不包括读取 (GET) 操作或者通过 Azure 门户或原始管理 API 执行的操作。

可在 API 管理服务中访问活动日志，或在 Azure Monitor 中访问所有 Azure 资源的日志。 

![活动日志](./media/api-management-azure-monitor/apim-monitor-activity-logs.png)

要查看活动日志，请执行以下操作：

1. 选择 APIM 服务实例。
2. 单击“活动日志”  。

    ![活动日志](./media/api-management-azure-monitor/api-management-activity-logs-blade.png)

3. 选择所需的筛选范围，然后单击“应用”  。

## <a name="resource-logs"></a>资源日志

资源日志提供了大量有关操作和错误的信息，这些信息对于审核和故障排除非常重要。 资源日志不同于活动日志。 活动日志提供对在 Azure 资源上执行的操作的见解。 资源日志提供对资源执行的操作的见解。

若要配置资源日志，请执行以下操作：

1. 选择 APIM 服务实例。
2. 单击“诊断设置”。 

    ![资源日志](./media/api-management-azure-monitor/api-management-diagnostic-logs-blade.png)

3. 单击“启用诊断”  。 可以将资源日志与指标一起存档到存储帐户，将其流式传输到事件中心，或者将其发送到 Azure Monitor 日志。 

“API 管理”当前提供有关单个 API 请求的资源日志（每小时进行批处理），其中每个条目具有以下架构：

```json
{  
    "isRequestSuccess" : "",
    "time": "",
    "operationName": "",
    "category": "",
    "durationMs": ,
    "callerIpAddress": "",
    "correlationId": "",
    "location": "",
    "httpStatusCodeCategory": "",
    "resourceId": "",
    "properties": {   
        "method": "", 
        "url": "", 
        "clientProtocol": "", 
        "responseCode": , 
        "backendMethod": "", 
        "backendUrl": "", 
        "backendResponseCode": ,
        "backendProtocol": "",  
        "requestSize": , 
        "responseSize": , 
        "cache": "", 
        "cacheTime": "", 
        "backendTime": , 
        "clientTime": , 
        "apiId": "",
        "operationId": "", 
        "productId": "", 
        "userId": "", 
        "apimSubscriptionId": "", 
        "backendId": "",
        "lastError": { 
            "elapsed" : "", 
            "source" : "", 
            "scope" : "", 
            "section" : "" ,
            "reason" : "", 
            "message" : ""
        } 
    }      
}  
```

| properties  | 类型 | 说明 |
| ------------- | ------------- | ------------- |
| isRequestSuccess | boolean | 如果 HTTP 请求完成时，响应状态代码在 2xx 或 3xx 范围内，则为 true |
| time | 日期时间 | 网关开始处理请求的时间戳 |
| operationName | 字符串 | 常量值“'Microsoft.ApiManagement/GatewayLogs” |
| category | 字符串 | 常量值“GatewayLogs” |
| durationMs | integer | 从网关收到请求到响应全部发送出去经过的时间（毫秒）。 它包括 clienTime、cacheTime 和 backendTime。 |
| callerIpAddress | 字符串 | 直接网关调用方（可以是中介）的 IP 地址 |
| correlationId | 字符串 | 由 API 管理分配的唯一 http 请求标识符 |
| location | 字符串 | 处理请求的网关所在 Azure 区域的名称 |
| httpStatusCodeCategory | 字符串 | http 响应状态代码的类别：成功（301 或以下，或者 304 或 307）、未授权（401、403、429）、错误（400、500 到 600）、其他 |
| resourceId | 字符串 | API 管理资源 /SUBSCRIPTIONS/\<subscription>/RESOURCEGROUPS/\<resource-group>/PROVIDERS/MICROSOFT.APIMANAGEMENT/SERVICE/\<name> 的 ID |
| properties | 对象 (object) | 当前请求的属性 |
| method | 字符串 | 传入请求的 HTTP 方法 |
| url | 字符串 | 传入请求的 URL |
| clientProtocol | 字符串 | 传入请求的 HTTP 协议版本 |
| responseCode | integer | 发送到客户端的 HTTP 响应的状态代码 |
| backendMethod | 字符串 | 发送到后端的请求的 HTTP 方法 |
| backendUrl | 字符串 | 发送到后端的请求的 URL |
| backendResponseCode | integer | 从后端收到的 HTTP 响应代码 |
| backendProtocol | 字符串 | 发送到后端的请求的 HTTP 协议版本 | 
| requestSize | integer | 在请求处理过程中从客户端接收的字节数 | 
| responseSize | integer | 在请求处理过程中发送到客户端的字节数 | 
| cache | 字符串 | 在请求处理过程中涉及的 API 管理缓存的状态（即命中、未命中、无） | 
| cacheTime | integer | 花在整个 API 管理缓存 IO（连接、发送和接收字节）上的时间（毫秒） | 
| backendTime | integer | 花在整个后端 IO（连接、发送和接收字节）上的时间（毫秒） | 
| clientTime | integer | 花在整个客户端 IO（连接、发送和接收字节）上的时间（毫秒） | 
| apiId | 字符串 | 当前请求的 API 实体标识符 | 
| operationId | 字符串 | 当前请求的操作实体标识符 | 
| productId | 字符串 | 当前请求的产品实体标识符 | 
| userId | 字符串 | 当前请求的用户实体标识符 | 
| apimSubscriptionId | 字符串 | 当前请求的订阅实体标识符 | 
| backendId | 字符串 | 当前请求的后端实体标识符 | 
| lastError | 对象 (object) | 上一个请求处理错误 | 
| elapsed | integer | 从网关收到请求到发生错误经过的时间（毫秒） | 
| source | 字符串 | 导致错误的策略或内部处理程序的名称 | 
| scope | 字符串 | 导致错误的策略所在策略文档的范围 | 
| section | 字符串 | 导致错误的策略所在策略文档的节 | 
| reason | 字符串 | 错误原因 | 
| message | 字符串 | 错误消息 | 

## <a name="next-steps"></a>后续步骤

在本教程中，你了解了如何执行以下操作：

> [!div class="checklist"]
> * 查看活动日志
> * 查看资源日志
> * 查看 API 的指标
> * 针对 API 收到的未经授权的调用设置警报规则

转到下一教程：

> [!div class="nextstepaction"]
> [跟踪调用](api-management-howto-api-inspector.md)
