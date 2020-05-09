---
title: Azure 事件网格-订阅验证疑难解答
description: 本文介绍如何解决订阅验证问题。
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: spelluru
ms.openlocfilehash: a052d4c268fadc60f754630156fe0bc0d33acf3b
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2020
ms.locfileid: "82630182"
---
# <a name="troubleshoot-azure-event-grid-errors"></a>排查 Azure 事件网格错误
此故障排除指南提供了有关事件订阅验证疑难解答的信息。 


## <a name="troubleshoot-event-subscription-validation"></a>事件订阅验证疑难解答

在创建事件订阅的过程中，如果你看到错误消息（ `The attempt to validate the provided endpoint https://your-endpoint-here failed. For more details, visit https://aka.ms/esvalidation`如），则表示验证握手中出现故障。 若要解决此错误，请验证以下各方面：

- 使用 Postman 或卷曲或类似工具，通过[示例 SubscriptionValidationEvent](webhook-event-delivery.md#validation-details)请求正文执行 HTTP POST 到 webhook url 的操作。
- 如果 webhook 要实现同步验证握手机制，请验证是否在响应中返回 ValidationCode。
- 如果 webhook 要实现异步验证握手机制，请验证你的 HTTP POST 是否返回200。
- 如果 webhook 在响应中返回403（禁止访问），请检查 webhook 是否在 Azure 应用程序网关或 Web 应用程序防火墙后面。 如果是，则需要禁用这些防火墙规则，并再次执行 HTTP POST：

  920300（请求缺少 Accept 标头，我们可以解决此问题）

  942430（受限制的 SQL 字符异常检测（args）：超出了特殊字符数（12））

  920230（检测到多个 URL 编码）

  942130（SQL 注入攻击：检测到 SQL Tautology。）

  931130（可能的远程文件包含（RFI）攻击 = 域外引用/链接）

> [!IMPORTANT]
> 有关 webhook 的终结点验证的详细信息，请参阅[Webhook 事件传递](webhook-event-delivery.md)。

## <a name="validate-event-grid-event-subscription-using-postman"></a>使用 Postman 验证事件网格事件订阅
下面是一个示例，说明如何使用 Postman 来验证事件网格事件的 webhook 订阅： 

![使用 Postman 的事件网格事件订阅验证](./media/troubleshoot-subscription-validation/event-subscription-validation-postman.png)

下面是一个示例 SubscriptionValidationEvent JSON：

```json
[
  {
    "id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66",
    "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "subject": "",
    "data": {
      "validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6",
    },
    "eventType": "Microsoft.EventGrid.SubscriptionValidationEvent",
    "eventTime": "2018-01-25T22:12:19.4556811Z",
    "metadataVersion": "1",
    "dataVersion": "1"
  }
]
```

下面是成功响应的示例：

```json
{
  "validationResponse": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
}
```

若要了解有关 webhook 的事件网格事件验证的详细信息，请参阅[包含事件网格事件的终结点验证](webhook-event-delivery.md#endpoint-validation-with-event-grid-events)。

## <a name="validate-cloud-event-subscription-using-postman"></a>使用 Postman 验证云事件订阅
下面是一个示例，说明如何使用 Postman 来验证云事件的 webhook 订阅： 

![使用 Postman 进行云事件订阅验证](./media/troubleshoot-subscription-validation/cloud-event-subscription-validation-postman.png)

使用 " **HTTP 选项**" 方法验证云事件。 若要了解有关 webhook 的云事件验证的详细信息，请参阅[通过云事件进行终结点验证](webhook-event-delivery.md#endpoint-validation-with-event-grid-events)。

## <a name="event-grid-event-subscription-validation-using-curl"></a>使用卷的事件网格事件订阅验证 
下面是用于验证事件网格事件的 webhook 订阅的示例卷命令： 

```bash
curl -X POST -d '[{"id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66","topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx","subject": "","data": {"validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"},"eventType": "Microsoft.EventGrid.SubscriptionValidationEvent","eventTime": "2018-01-25T22:12:19.4556811Z", "metadataVersion": "1","dataVersion": "1"}]' -H 'Content-Type: application/json' https://{your-webhook-url.com}
```

## <a name="next-steps"></a>后续步骤
如需更多帮助，请在 [Stack Overflow 论坛](https://stackoverflow.com/questions/tagged/azure-eventgrid)中发布问题，或开具[支持票证](https://azure.microsoft.com/support/options/)。 
