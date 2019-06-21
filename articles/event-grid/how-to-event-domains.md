---
title: 使用事件域管理 Azure 事件网格中的大型主题集
description: 展示了如何使用事件域管理 Azure 事件网格中的大型主题集并向其发布事件。
services: event-grid
author: banisadr
ms.service: event-grid
ms.author: babanisa
ms.topic: conceptual
ms.date: 01/17/2019
ms.openlocfilehash: 0042b0bd8c6ed9e9d253c44151dcf0588c742b48
ms.sourcegitcommit: e5dcf12763af358f24e73b9f89ff4088ac63c6cb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/14/2019
ms.locfileid: "67137839"
---
# <a name="manage-topics-and-publish-events-using-event-domains"></a>使用事件域管理主题和发布事件

本文介绍如何：

* 创建事件网格域
* 订阅事件网格主题
* 列出密钥
* 将事件发布到域

若要了解事件域，请参阅[了解用于管理事件网格主题的事件域](event-domains.md)。

[!INCLUDE [requires-azurerm](../../includes/requires-azurerm.md)]

## <a name="create-an-event-domain"></a>创建事件域

若要管理大型主题集，请创建一个事件域。

对于 Azure CLI，请使用：

```azurecli-interactive
az eventgrid domain create \
  -g <my-resource-group> \
  --name <my-domain-name> \
  -l <location>
```

对于 PowerShell，请使用：

```azurepowershell-interactive
New-AzureRmEventGridDomain `
  -ResourceGroupName <my-resource-group> `
  -Name <my-domain-name> `
  -Location <location>
```

创建成功后会返回以下值：

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

## <a name="manage-access-to-topics"></a>管理对主题的访问

可通过[角色分配](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli)来管理对主题的访问。 角色分配使用基于角色的访问控制来限制对 Azure 资源的操作，仅允许经授权用户在特定范围内执行操作。

事件网格包含两个内置角色，可以使用这些角色为特定用户分配对域中不同主题的访问权限。 这些角色为 `EventGrid EventSubscription Contributor (Preview)` 和 `EventGrid EventSubscription Reader (Preview)`，分别用于创建/删除订阅，以及只允许列出事件订阅。

以下 Azure CLI 命令将 `alice@contoso.com` 限制为只能在主题 `demotopic1` 上创建或删除事件订阅：

```azurecli-interactive
az role assignment create \
  --assignee alice@contoso.com \
  --role "EventGrid EventSubscription Contributor (Preview)" \
  --scope /subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/demotopic1
```

以下 PowerShell 命令将 `alice@contoso.com` 限制为只能在主题 `demotopic1` 上创建或删除事件订阅：

```azurepowershell-interactive
New-AzureRmRoleAssignment `
  -SignInName alice@contoso.com `
  -RoleDefinitionName "EventGrid EventSubscription Contributor (Preview)" `
  -Scope /subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/demotopic1
```

有关管理对事件网格操作的访问权限的详细信息，请参阅[事件网格安全性和身份验证](./security-authentication.md)。

## <a name="create-topics-and-subscriptions"></a>创建主题和订阅

事件网格服务根据创建域主题事件订阅的调用，在域中自动创建和管理相应的主题。 没有单独的步骤可在域中创建主题。 同样，删除某个主题的最后一个事件订阅时，也会删除该主题。

订阅域中主题的过程与订阅其他任何 Azure 资源相同。 对于源资源 ID，指定之前在创建域时返回的事件域 ID。 若要指定想要订阅的主题，请将 `/topics/<my-topic>` 添加到源资源 ID 的末尾。 若要创建接收域中的所有事件的域范围事件订阅，请指定事件域 ID 且不要指定任何主题。

通常情况下，将由你在前面的部分中向其授予了访问权限的用户创建订阅。 为了简化本文，将由你创建订阅。 

对于 Azure CLI，请使用：

```azurecli-interactive
az eventgrid event-subscription create \
  --name <event-subscription> \
  --source-resource-id "/subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/demotopic1" \
  --endpoint https://contoso.azurewebsites.net/api/updates
```

对于 PowerShell，请使用：

```azurepowershell-interactive
New-AzureRmEventGridSubscription `
  -ResourceId "/subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/demotopic1" `
  -EventSubscriptionName <event-subscription> `
  -Endpoint https://contoso.azurewebsites.net/api/updates
```

如果需要将事件订阅到某个测试终结点，始终可以部署能够显示传入事件的[预生成 Web 应用](https://github.com/Azure-Samples/azure-event-grid-viewer)。 可将事件发送到测试网站 (`https://<your-site-name>.azurewebsites.net/api/updates`)。

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

为主题设置的权限存储在 Azure Active Directory 中，必须显式删除。 如果用户对主题拥有写访问权限，则删除事件订阅不会撤消用户创建事件订阅的权限。


## <a name="publish-events-to-an-event-grid-domain"></a>将事件发布到事件网格域

将事件发布到域的过程与[发布到自定义主题](./post-to-custom-topic.md)相同。 但是，不是发布到自定义主题，而是将所有事件发布到域终结点。 在 JSON 事件数据中，可以指定要将事件发送到的主题。 例如，以下事件数组会导致将包含 `"id": "1111"` 的事件发送到主题 `demotopic1`，将包含 `"id": "2222"` 的事件发送到主题 `demotopic2`：

```json
[{
  "topic": "demotopic1",
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
  "topic": "demotopic2",
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

若要使用 Azure CLI 获取域终结点，请使用：

```azurecli-interactive
az eventgrid domain show \
  -g <my-resource-group> \
  -n <my-domain>
```

若要获取域的密钥，请使用：

```azurecli-interactive
az eventgrid domain key list \
  -g <my-resource-group> \
  -n <my-domain>
```

若要使用 PowerShell 获取域终结点，请使用：

```azurepowershell-interactive
Get-AzureRmEventGridDomain `
  -ResourceGroupName <my-resource-group> `
  -Name <my-domain>
```

若要获取域的密钥，请使用：

```azurepowershell-interactive
Get-AzureRmEventGridDomainKey `
  -ResourceGroupName <my-resource-group> `
  -Name <my-domain>
```

然后，使用偏好的方法发出 HTTP POST，将事件发布到事件网格域。

## <a name="search-lists-of-topics-or-subscriptions"></a>搜索的主题或订阅的列表

为了使搜索和管理的大量消息的主题或订阅，事件网格的 Api 支持列表和分页。

### <a name="using-cli"></a>使用 CLI

若要使用它请确保你在使用 Azure CLI 事件网格扩展版本 0.4.1 或更高版本。

```azurecli-interactive
# If you haven't already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid topic list \
    --odata-query "contains(name, 'my-test-filter')"
```

## <a name="next-steps"></a>后续步骤

* 有关事件域中的高级概念及其作用的详细信息，请参阅[事件域的概念概述](event-domains.md)。
