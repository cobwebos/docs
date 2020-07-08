---
title: Azure 事件网格 - 订阅验证疑难解答
description: 本文介绍如何解决订阅验证问题。
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/21/2020
ms.author: spelluru
ms.openlocfilehash: f292d70eaaca29e714ea35b4f61a141b2d5cd2b6
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.contentlocale: zh-CN
ms.lasthandoff: 05/22/2020
ms.locfileid: "83778720"
---
# <a name="troubleshoot-azure-event-grid-subscription-validations"></a>排查 Azure 事件网格订阅验证问题
本文介绍有关如何解决订阅验证问题的信息。 

> [!IMPORTANT]
> 有关 webhook 终结点验证的详细信息，请参阅 [Webhook 事件传送](webhook-event-delivery.md)。

## <a name="validate-event-grid-event-subscription-using-postman"></a>使用 Postman 验证事件网格事件订阅
下面是一个示例，演示如何使用 Postman 来验证事件网格事件的 webhook 订阅： 

![通过使用 Postman 实现的事件网格事件订阅验证](./media/troubleshoot-subscription-validation/event-subscription-validation-postman.png)

下面是 SubscriptionValidationEvent JSON 的示例：

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

若要详细了解 webhook 事件网格事件验证，请参阅[事件网格事件的终结点验证](webhook-event-delivery.md#endpoint-validation-with-event-grid-events)。


## <a name="validate-event-grid-event-subscription-using-curl"></a>使用 Curl 验证事件网格事件订阅 
下面是用于验证事件网格事件的 webhook 订阅的 Curl 命令的示例： 

```bash
curl -X POST -d '[{"id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66","topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx","subject": "","data": {"validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"},"eventType": "Microsoft.EventGrid.SubscriptionValidationEvent","eventTime": "2018-01-25T22:12:19.4556811Z", "metadataVersion": "1","dataVersion": "1"}]' -H 'Content-Type: application/json' https://{your-webhook-url.com}
```

## <a name="validate-cloud-event-subscription-using-postman"></a>使用 Postman 验证云事件订阅
下面是一个示例，演示如何使用 Postman 来验证云事件的 webhook 订阅： 

![通过使用 Postman 实现的云事件订阅验证](./media/troubleshoot-subscription-validation/cloud-event-subscription-validation-postman.png)

使用 HTTP OPTIONS 方法执行云事件验证。 若要详细了解 webhook 云事件验证，请参阅[云事件的终结点验证](webhook-event-delivery.md#endpoint-validation-with-event-grid-events)。

## <a name="error-code-403"></a>错误代码:403
如果 webhook 在响应中返回 403（禁止访问），请检查是否在 webhook 前面配置了 Azure 应用程序网关或 Web 应用程序防火墙。 如果是这样，需要禁用以下防火墙规则，并再次执行 HTTP POST：

  - 920300（请求缺少 Accept 标头，我们可解决此问题）
  - 942430（受限 SQL 字符异常情况检测 (args)：特殊字符数已超出 (12)）
  - 920230（检测到多个 URL 编码）
  - 942130（SQL 注入攻击：检测到 SQL 同义反复。）
  - 931130（可能的远程文件包含 (RFI) 攻击 = 域外引用/链接）

## <a name="next-steps"></a>后续步骤
如需更多帮助，请将你的问题发布到 [Stack Overflow 论坛](https://stackoverflow.com/questions/tagged/azure-eventgrid)或开立[支持票证](https://azure.microsoft.com/support/options/)。 
