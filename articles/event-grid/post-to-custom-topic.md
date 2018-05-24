---
title: 将事件发布到自定义 Azure 事件网格主题
description: 说明如何将事件发布到 Azure 事件网格的自定义主题
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/17/2018
ms.author: tomfitz
ms.openlocfilehash: e4256de1d9112d785b6d1cd52067fc99144a0a04
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/18/2018
---
# <a name="post-to-custom-topic-for-azure-event-grid"></a>发布到 Azure 事件网格的自定义主题

本文说明如何将事件发布到自定义主题。 它显示发布和事件数据的格式。 [服务级别协议 (SLA)](https://azure.microsoft.com/support/legal/sla/event-grid/v1_0/) 仅适用于与预期格式匹配的发布。

## <a name="endpoint"></a>终结点

使用 URI 格式 `https://<topic-endpoint>?api-version=2018-01-01` 将 HTTP POST 发送到自定义主题。

例如，有效的 URI 为：`https://exampletopic.westus2-1.eventgrid.azure.net/api/events?api-version=2018-01-01`。

若要使用 Azure CLI 获取自定义主题的终结点，请使用：

```azurecli-interactive
az eventgrid topic show --name <topic-name> -g <topic-resource-group> --query "endpoint"
```

若要使用 Azure PowerShell 获取自定义主题的终结点，请使用：

```powershell
(Get-AzureRmEventGridTopic -ResourceGroupName <topic-resource-group> -Name <topic-name>).Endpoint
```

## <a name="header"></a>标头

在请求中包含一个名为 `aeg-sas-key` 的标头值，其中包含身份验证密钥。

例如，有效的标头值为 `aeg-sas-key: VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg==`。

若要使用 Azure CLI 获取自定义主题的密钥，请使用：

```azurecli
az eventgrid topic key list --name <topic-name> -g <topic-resource-group> --query "key1"
```

若要使用 PowerShell 获取自定义主题的密钥，请使用：

```powershell
(Get-AzureRmEventGridTopicKey -ResourceGroupName <topic-resource-group> -Name <topic-name>).Key1
```

## <a name="event-data"></a>事件数据

就自定义主题而言，顶级数据包含与标准资源所定义事件相同的字段。 这些属性之一是包含自定义主题所特有的属性的数据属性。 作为事件发布者，你确定该数据对象的属性。 使用以下架构：

```json
[
  {
    "id": string,    
    "eventType": string,
    "subject": string,
    "eventTime": string-in-date-time-format,
    "data":{
      object-unique-to-each-publisher
    },
    "dataVersion": string
  }
]
```

有关这些属性的说明，请参阅 [Azure 事件网格事件架构](event-schema.md)。 将事件发布到事件网格主题时，数组的总大小最大可为 1 MB。 数组中的每个事件被限制为 64 KB。

例如，有效的事件数据架构是：

```json
[{
  "id": "1807",
  "eventType": "recordInserted",
  "subject": "myapp/vehicles/motorcycles",
  "eventTime": "2017-08-10T21:03:07+00:00",
  "data": {
    "make": "Ducati",
    "model": "Monster"
  },
  "dataVersion": "1.0"
}]
```

## <a name="response"></a>响应

发布到主题终结点后，你将收到响应。 响应是标准 HTTP 响应代码。 一些常见的响应如下所示：

|结果  |响应  |
|---------|---------|
|成功  | 200 正常  |
|事件数据的格式不正确 | 400 错误请求 |
|访问密钥无效 | 401 未授权 |
|终结点不正确 | 404 未找到 |
|数组或事件超出大小限制 | 413 有效负载太大 |

对于错误，消息正文采用以下格式：

```json
{
    "error": {
        "code": "<HTTP status code>",
        "message": "<description>",
        "details": [{
            "code": "<HTTP status code>",
            "message": "<description>"
    }]
  }
}
```

## <a name="next-steps"></a>后续步骤

* 有关监视事件传送的信息，请参阅[监视事件网格消息传送](monitor-event-delivery.md)。
* 有关身份验证密钥的详细信息，请参阅[事件网格安全性和身份验证](security-authentication.md)。
* 有关创建 Azure 事件网格订阅的详细信息，请参阅[事件网格订阅架构](subscription-creation-schema.md)。
