---
title: 如何使用事件域管理 Azure 事件网格中的大型主题集并向其发布事件
description: 介绍如何使用事件域管理 Azure 事件网格中的主题并向其发布事件。
services: event-grid
author: banisadr
ms.service: event-grid
ms.author: babanisa
ms.topic: conceptual
ms.date: 10/30/2018
ms.openlocfilehash: d6da1ee603c85556693b145ba17d1e0cd0dfabd7
ms.sourcegitcommit: f0c2758fb8ccfaba76ce0b17833ca019a8a09d46
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2018
ms.locfileid: "51034534"
---
# <a name="manage-topics-and-publish-events-using-event-domains"></a>使用事件域管理主题和发布事件

本文介绍以下操作：

* 创建事件网格域
* 订阅主题
* 列出密钥
* 将事件发布到域

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="create-an-event-domain"></a>创建事件域

可以通过 [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 的 `eventgrid` 扩展创建事件域。 创建域后，可以使用它来管理大型主题集。

```azurecli-interactive
# if you haven't already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid domain create \
  -g <my-resource-group> \
  --name <my-domain-name>
  -l <location>
```

创建成功后会返回以下结果：

```json
{
  "endpoint": "https://<my-domain-name>.westus2-1.eventgrid.azure.net/api/events",
  "id": "/subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>",
  "inputSchema": "EventGridSchema",
  "inputSchemaMapping": null,
  "location": "westus2",
  "name": "<my-domain-name>",
  "provisioningState": "Succeeded",
  "resourceGroup": "<my-resource-group>",
  "tags": null,
  "type": "Microsoft.EventGrid/domains"
}
```

请记下 `endpoint` 和 `id`，因为需要使用它们来管理域和发布事件。

## <a name="create-topics-and-subscriptions"></a>创建主题和订阅

事件网格服务根据创建域主题事件订阅的调用，在域中自动创建和管理相应的主题。 没有单独的步骤可在域中创建主题。 同样，删除某个主题的最后一个事件订阅时，也会删除该主题。

订阅域中主题的过程与订阅其他任何 Azure 资源相同：

```azurecli-interactive
az eventgrid event-subscription create \
  --name <event-subscription> \
  --resource-id "/subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/<my-topic>" \
  --endpoint https://contoso.azurewebsites.net/api/f1?code=code
```

给定的资源 ID 与前面在创建域时返回的 ID 相同。 若要指定想要订阅的主题，请将 `/topics/<my-topic>` 添加到资源 ID 的末尾。

若要在域中创建接收所有事件的域范围事件订阅，请将域指定为 `resource-id` 且不要指定任何主题，例如 `/subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>`。

如果需要将事件订阅到某个测试终结点，始终可以部署能够显示传入事件的[预生成 Web 应用](https://github.com/Azure-Samples/azure-event-grid-viewer)。 可将事件发送到测试网站 (`https://<your-site-name>.azurewebsites.net/api/updates`)。

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>

为主题设置的权限存储在 Azure Active Directory 中，必须显式删除。 如果用户对主题拥有写访问权限，则删除事件订阅不会撤消用户创建事件订阅的权限。

## <a name="manage-access-to-topics"></a>管理对主题的访问

可通过[角色分配](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli)来管理对主题的访问。 角色分配使用“基于角色的访问权限检查” Azure 资源的操作权限限制为特定范围的已授权用户。

事件网格包含两个内置角色，可以使用这些角色为特定用户分配对域中不同主题的访问权限。 这些角色为 `EventGrid EventSubscription Contributor (Preview)` 和 `EventGrid EventSubscription Reader (Preview)`，分别用于创建/删除订阅，以及只允许列出事件订阅。

以下命令将 `alice@contoso.com` 限制为只能对主题 `foo` 创建或删除事件订阅：

```azurecli-interactive
az role assignment create --assignee alice@contoso.com --role "EventGrid EventSubscription Contributor (Preview)" --scope /subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/foo
```

请参阅[事件网格安全性和身份验证](./security-authentication.md)，以详细了解以下相关信息：

* 管理访问控制
* 操作类型
* 创建自定义角色定义

## <a name="publish-events-to-an-event-grid-domain"></a>将事件发布到事件网格域

将事件发布到域的过程与[发布到自定义主题](./post-to-custom-topic.md)相同。 唯一的区别是，需要指定每个事件要传送到的主题。 例如，以下事件数组会导致将包含 `"id": "1111"` 的事件发送到主题 `foo`，将包含 `"id": "2222"` 的事件发送到主题 `bar`：

```json
[{
  "topic": "foo",
  "id": "1111",
  "eventType": "maintenanceRequested",
  "subject": "myapp/vehicles/diggers",
  "eventTime": "2018-10-30T21:03:07+00:00",
  "data": {
    "make": "Contoso",
    "model": "Small Digger"
  },
  "dataVersion": "1.0"
},
{
  "topic": "bar",
  "id": "2222",
  "eventType": "maintenanceCompleted",
  "subject": "myapp/vehicles/tractors",
  "eventTime": "2018-10-30T21:04:12+00:00",
  "data": {
    "make": "Contoso",
    "model": "Big Tractor"
  },
  "dataVersion": "1.0"
}]
```

若要获取域的密钥，请使用：

```azurecli-interactive
az eventgrid domain key list \
  -g <my-resource-group> \
  -n <my-domain>
```

然后，使用偏好的方法发出 HTTP POST，将事件发布到事件网格域。

## <a name="next-steps"></a>后续步骤

* 有关事件域中的高级概念及其作用的详细信息，请参阅[事件域的概念概述](./event-domains.md)。