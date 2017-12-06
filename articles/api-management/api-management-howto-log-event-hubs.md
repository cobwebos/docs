---
title: "如何在 Azure API 管理中将事件记录到 Azure 事件中心 | Microsoft Docs"
description: "了解如何在 Azure API 管理中将事件记录到 Azure 事件中心。"
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 88f6507d-7460-4eb2-bffd-76025b73f8c4
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: 066f151aa96b3a57c86515411ba05a982c10aa5f
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/28/2017
---
# <a name="how-to-log-events-to-azure-event-hubs-in-azure-api-management"></a>如何在 Azure API 管理中将事件记录到 Azure 事件中心
Azure 事件中心是一个高度可缩放的数据引入服务，每秒可以引入数百万的事件，从而使你能够处理和分析连接设备和应用程序生成的海量数据。 事件中心充当事件管道“前门”，将数据收集到事件中心后，可以使用任何实时分析提供程序或批处理/存储适配器来转换和存储这些数据。 事件中心可将事件流的生成与这些事件的使用分离开来，因此，事件使用者可以根据自己的计划访问事件。

本文是[将 Azure API 管理与事件中心集成](https://azure.microsoft.com/documentation/videos/integrate-azure-api-management-with-event-hubs/)视频的配套内容，介绍了如何使用 Azure 事件中心记录 API 管理事件。

## <a name="create-an-azure-event-hub"></a>创建 Azure 事件中心
若要创建新事件中心，请登录到 [Azure 经典门户](https://manage.windowsazure.com)并依次单击“新建”->“应用服务”->“服务总线”->“事件中心”->“快速创建”。 输入事件中心名称、区域，选择订阅，并选择命名空间。 如果之前未创建命名空间，可通过在“命名空间”文本框中键入名称来创建命名空间。 配置所有属性后，单击“创建新事件中心”创建事件中心。

![创建事件中心][create-event-hub]

接下来，导航到新事件中心的“配置”选项卡，并创建两个**共享访问策略**。 将第一个命名为“Sending”，并为其提供 **Send** 权限。

![Sending 策略][sending-policy]

将第二个命名为“Receiving”，并为其提供 **Listen** 权限，然后单击“保存”。

![Receiving 策略][receiving-policy]

每个共享访问策略都允许应用程序与事件中心之间发送和接收事件。 若要访问这些策略的连接字符串，请导航到事件中心的“仪表板”选项卡并单击“连接信息”。

![连接字符串][event-hub-dashboard]

**Sending** 连接字符串在记录事件时使用，**Receiving** 连接字符串在从事件中心下载事件时使用。

![连接字符串][event-hub-connection-string]

## <a name="create-an-api-management-logger"></a>创建 API 管理记录器
现在有了事件中心，下一步是在 API 管理服务中配置[记录器](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity)，以便它可以将事件记录到事件中心。

使用 [API 管理 REST API](http://aka.ms/smapi) 配置 API 管理记录器。 在第一次使用 REST API 之前，查看[先决条件](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/api-management-rest#Prerequisites)并确保已[启用对 REST API 的访问](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/api-management-rest#EnableRESTAPI)。

若要创建记录器，请使用以下 URL 模板发出 HTTP PUT 请求。

`https://{your service}.management.azure-api.net/loggers/{new logger name}?api-version=2014-02-14-preview`

* 将 `{your service}` 替换为 API 管理服务实例的名称。
* 将 `{new logger name}` 替换为新记录器的所需名称。 配置 [log-to-eventhub](https://msdn.microsoft.com/library/azure/dn894085.aspx#log-to-eventhub) 策略时，将引用此名称

将以下标头添加到请求。

* 内容类型：应用程序/json
* 授权：SharedAccessSignature 58...
  * 有关生成 `SharedAccessSignature` 的说明，请参阅 [Azure API 管理 REST API 身份验证](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-authentication)。

使用以下模板指定请求正文。

```json
{
  "loggertype" : "AzureEventHub",
  "description" : "Sample logger description",
  "credentials" : {
    "name" : "Name of the Event Hub from the Azure Classic Portal",
    "connectionString" : "Endpoint=Event Hub Sender connection string"
    }
}
```

* `loggertype` 必须设置为 `AzureEventHub`。
* `description` 提供记录器的可选说明，并且可在需要时为零长度。
* `credentials` 包含 Azure 事件中心的 `name` 和 `connectionString`。

发出请求时，如果创建记录器，则返回 `201 Created` 的状态代码。

> [!NOTE]
> 有关其他可能的返回代码及其原因，请参阅[创建记录器](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity#PUT)。 若要查看如何执行其他操作，如列表、更新和删除，请参阅[记录器](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity)实体文档。
>
>

## <a name="configure-log-to-eventhubs-policies"></a>配置 log-to-eventhubs 策略
在 API 管理中配置记录器后，可配置 log-to-eventhubs 策略以记录所需事件。 log-to-eventhubs 策略可在入站策略部分或出站策略部分中使用。

要配置策略，请登录到 [Azure 门户](https://portal.azure.com)，导航到 API 管理服务，并单击“发布者门户”访问发布者门户。

![发布者门户][publisher-portal]

单击左侧 API 管理菜单中的“策略”、选择所需的产品和 API，并单击“添加策略”。 在此示例中，我们向 **Unlimited** 产品中的 **Echo API** 添加策略。

![添加策略][add-policy]

将光标放在 `inbound` 策略部分中，并单击“Log to EventHub”策略插入 `log-to-eventhub` 策略声明模板。

![策略编辑器][event-hub-policy]

```xml
<log-to-eventhub logger-id ='logger-id'>
  @( string.Join(",", DateTime.UtcNow, context.Deployment.ServiceName, context.RequestId, context.Request.IpAddress, context.Operation.Name))
</log-to-eventhub>
```

将 `logger-id` 替换为在上一步中配置的 API 管理记录器的名称。

可使用返回字符串作为 `log-to-eventhub` 元素值的任何表达式。 在此示例中，记录包含日期和时间、服务名称、请求 ID、请求 IP 地址和操作名称的字符串。

单击“保存”保存更新后的策略配置。 保存后，策略立即处于活动状态，并且事件记录到指定的事件中心。

## <a name="next-steps"></a>后续步骤
* 了解有关 Azure 事件中心的详细信息
  * [Azure 事件中心入门](../event-hubs/event-hubs-c-getstarted-send.md)
  * [使用 EventProcessorHost 接收消息](../event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph.md)
  * [事件中心编程指南](../event-hubs/event-hubs-programming-guide.md)
* 了解有关 API 管理和事件中心集成的详细信息
  * [记录器实体引用](https://docs.microsoft.com/rest/api/apimanagement/loggers)
  * [log-to-eventhub 策略引用](https://docs.microsoft.com/azure/api-management/api-management-advanced-policies#log-to-eventhub)
  * [使用 Azure API 管理、事件中心和 Runscope 监视 API](api-management-log-to-eventhub-sample.md)    

## <a name="watch-a-video-walkthrough"></a>观看视频演练
> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Integrate-Azure-API-Management-with-Event-Hubs/player]
>
>

[publisher-portal]: ./media/api-management-howto-log-event-hubs/publisher-portal.png
[create-event-hub]: ./media/api-management-howto-log-event-hubs/create-event-hub.png
[event-hub-connection-string]: ./media/api-management-howto-log-event-hubs/event-hub-connection-string.png
[event-hub-dashboard]: ./media/api-management-howto-log-event-hubs/event-hub-dashboard.png
[receiving-policy]: ./media/api-management-howto-log-event-hubs/receiving-policy.png
[sending-policy]: ./media/api-management-howto-log-event-hubs/sending-policy.png
[event-hub-policy]: ./media/api-management-howto-log-event-hubs/event-hub-policy.png
[add-policy]: ./media/api-management-howto-log-event-hubs/add-policy.png
