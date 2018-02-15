---
title: "将事件发布到自定义 Azure 事件网格主题"
description: "说明如何将事件发布到 Azure 事件网格的自定义主题"
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 01/30/2018
ms.author: tomfitz
ms.openlocfilehash: 43dcdf9ab0fee5f7e61ecdc42aaf40430e272d92
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2018
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

有关这些属性的说明，请参阅 [Azure 事件网格事件架构](event-schema.md)。

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

## <a name="next-steps"></a>后续步骤

* 有关路由自定义事件的简介，请参阅[使用 Azure CLI 和事件网格创建和路由自定义事件](custom-event-quickstart.md)或[使用 Azure PowerShell 和事件网格创建和路由自定义事件](custom-event-quickstart-powershell.md)。
* 有关身份验证密钥的详细信息，请参阅[事件网格安全性和身份验证](security-authentication.md)。
* 有关创建 Azure 事件网格订阅的详细信息，请参阅[事件网格订阅架构](subscription-creation-schema.md)。
