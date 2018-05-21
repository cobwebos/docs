---
title: 通过 PowerShell 自定义 Azure 事件网格的事件 | Microsoft Docs
description: 使用 Azure 事件网格和 PowerShell 发布一个主题，然后订阅该事件。
services: event-grid
keywords: ''
author: tfitzmac
ms.author: tomfitz
ms.date: 03/20/2018
ms.topic: quickstart
ms.service: event-grid
ms.openlocfilehash: 695aa5c567882ef7742666146877e1fbc660492b
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/18/2018
---
# <a name="create-and-route-custom-events-with-azure-powershell-and-event-grid"></a>使用 Azure PowerShell 和事件网格创建和路由自定义事件

Azure 事件网格是针对云的事件处理服务。 在本文中，请使用 Azure PowerShell 创建一个自定义主题，然后订阅该主题，再触发可查看结果的事件。 通常将事件发送到与该事件对应的终结点，例如 webhook 或 Azure Function。 但在本文中，为简便起见，请将事件发送到仅收集消息的 URL。 可以使用 [Hookbin](https://hookbin.com/) 提供的第三方工具创建此 URL。

>[!NOTE]
>**Hookbin** 并不适合在高吞吐量方案中使用。 使用此工具纯粹是为了演示。 如果一次推送多个事件，可能不会在工具中看到所有事件。

完成后即可看到事件数据已发送到某个终结点。

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

本文要求运行最新版本的 Azure PowerShell。 如需进行安装或升级，请参阅[安装和配置 Azure PowerShell](/powershell/azure/install-azurerm-ps)。

## <a name="create-a-resource-group"></a>创建资源组

事件网格主题是 Azure 资源，必须放置在 Azure 资源组中。 该资源组是在其中部署和管理 Azure 资源的逻辑集合。

使用 [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) 命令创建资源组。

以下示例在“westus2”位置创建名为“gridResourceGroup”的资源组。

```powershell
New-AzureRmResourceGroup -Name gridResourceGroup -Location westus2
```

## <a name="create-a-custom-topic"></a>创建自定义主题

事件网格主题提供用户定义的终结点，可向其发布事件。 以下示例在资源组中创建自定义主题。 用主题的唯一名称替换 `<topic_name>`。 主题名称必须唯一，因为它由 DNS 条目表示。

```powershell
New-AzureRmEventGridTopic -ResourceGroupName gridResourceGroup -Location westus2 -Name <topic_name>
```

## <a name="create-a-message-endpoint"></a>创建消息终结点

在订阅自定义主题之前，让我们创建事件消息的终结点。 与其编写代码来响应事件，不如创建一个终结点来收集消息，方便你查看。 Hookbin 是第三方工具，用于创建终结点和查看发送到其中的请求。 转到 [Hookbin](https://hookbin.com/) 并单击“创建新终结点”。  复制 bin URL，因为在订阅主题时需要它。

## <a name="subscribe-to-a-topic"></a>订阅主题

订阅主题是为了告知事件网格要跟踪哪些事件。以下示例订阅所创建的自定义主题，并将 Hookbin 中的 URL 作为事件通知的终结点传递。 将 `<event_subscription_name>` 替换为订阅的唯一名称，将 `<endpoint_URL>` 替换为上一部分的值。 在订阅时指定终结点，然后事件网格就会负责将事件路由到该终结点。 对于 `<topic_name>`，请使用此前创建的值。

```powershell
New-AzureRmEventGridSubscription -EventSubscriptionName <event_subscription_name> -Endpoint <endpoint_URL> -ResourceGroupName gridResourceGroup -TopicName <topic_name>
```

## <a name="send-an-event-to-your-topic"></a>向主题发送事件

让我们触发一个事件，看看事件网格如何将消息分发到终结点。 首先，让我们获取主题的 URL 和密钥。 同样，请将主题名称用于 `<topic_name>`。

```powershell
$endpoint = (Get-AzureRmEventGridTopic -ResourceGroupName gridResourceGroup -Name <topic-name>).Endpoint
$keys = Get-AzureRmEventGridTopicKey -ResourceGroupName gridResourceGroup -Name <topic-name>
```

在本文中，为简便起见，请将示例事件数据设置为发送到自定义主题。 通常情况下，应用程序或 Azure 服务会发送事件数据。 以下示例使用 Hashtable 来构造事件的数据 `htbody`，然后将其转换为正确格式的 JSON 有效负载对象 `$body`：

```powershell
$eventID = Get-Random 99999

#Date format should be SortableDateTimePattern (ISO 8601)
$eventDate = Get-Date -Format s

#Construct body using Hashtable
$htbody = @{
    id= $eventID
    eventType="recordInserted"
    subject="myapp/vehicles/motorcycles"
    eventTime= $eventDate   
    data= @{
        make="Ducati"
        model="Monster"
    }
    dataVersion="1.0"
}

#Use ConvertTo-Json to convert event body from Hashtable to JSON Object
#Append square brackets to the converted JSON payload since they are expected in the event's JSON payload syntax
$body = "["+(ConvertTo-Json $htbody)+"]"
```

如果查看 `$body`，则可看到完整的事件。 JSON 的 `data` 元素是事件的有效负载。 可以将任何格式正确的 JSON 置于此字段中。 也可将主题字段用于高级路由和筛选。

现在，请向主题发送事件。

```powershell
Invoke-WebRequest -Uri $endpoint -Method POST -Body $body -Headers @{"aeg-sas-key" = $keys.Key1}
```

现已触发事件，并且事件网格已将消息发送到订阅时配置的终结点。 浏览到此前创建的终结点 URL。 或者，在打开的浏览器中单击“刷新”。 此时会看到刚发送的事件。

```json
[{
  "id": "1807",
  "eventType": "recordInserted",
  "subject": "myapp/vehicles/motorcycles",
  "eventTime": "2018-01-25T15:58:13",
  "data": {
    "make": "Ducati",
    "model": "Monster"
  },
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/topics/{topic}"
}]
```

## <a name="clean-up-resources"></a>清理资源

如果打算继续处理此事件，请不要清除本文中创建的资源。 否则，请使用以下命令删除本文中创建的资源。

```powershell
Remove-AzureRmResourceGroup -Name gridResourceGroup
```

## <a name="next-steps"></a>后续步骤

了解如何创建主题和事件订阅以后，即可进一步学习事件网格的功能：

- [关于事件网格](overview.md)
- [将 Blob 存储事件路由到自定义 Web 终结点](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)
- [通过 Azure 事件网格和逻辑应用监视虚拟机的更改](monitor-virtual-machine-changes-event-grid-logic-app.md)
- [将大数据流式传输到数据仓库](event-grid-event-hubs-integration.md)
