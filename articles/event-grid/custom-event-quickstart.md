---
title: 使用 CLI 自定义 Azure 事件网格的事件 | Microsoft Docs
description: 使用 Azure 事件网格和 Azure CLI 发布一个主题，然后订阅该事件。
services: event-grid
keywords: ''
author: tfitzmac
ms.author: tomfitz
ms.date: 03/20/2018
ms.topic: hero-article
ms.service: event-grid
ms.openlocfilehash: be98fa88727a867e0fca0ca3587db276f8a2d7f2
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2018
---
# <a name="create-and-route-custom-events-with-azure-cli-and-event-grid"></a>使用 Azure CLI 和事件网格创建和路由自定义事件

Azure 事件网格是针对云的事件处理服务。 在本文中，请使用 Azure CLI 创建一个自定义主题，然后订阅该主题，再触发可查看结果的事件。 通常将事件发送到与该事件对应的终结点，例如 webhook 或 Azure Function。 但在本文中，为简便起见，请将事件发送到仅收集消息的 URL。 可以使用 [Hookbin](https://hookbin.com/) 提供的第三方工具创建此 URL。

>[!NOTE]
>**Hookbin** 并不适合在高吞吐量方案中使用。 使用此工具纯粹是为了演示。 如果一次推送多个事件，可能不会在工具中看到所有事件。

完成后即可看到事件数据已发送到某个终结点。

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，本文需要运行最新版的 Azure CLI（2.0.24 或更高版本）。 若要查找版本，请运行 `az --version`。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0](/cli/azure/install-azure-cli)。

如果不使用 Cloud Shell，则必须先使用 `az login` 登录。

## <a name="create-a-resource-group"></a>创建资源组

事件网格主题是 Azure 资源，必须放置在 Azure 资源组中。 该资源组是在其中部署和管理 Azure 资源的逻辑集合。

使用 [az group create](/cli/azure/group#az_group_create) 命令创建资源组。 

以下示例在“westus2”位置创建名为“gridResourceGroup”的资源组。

```azurecli-interactive
az group create --name gridResourceGroup --location westus2
```

## <a name="create-a-custom-topic"></a>创建自定义主题

事件网格主题提供用户定义的终结点，可向其发布事件。 以下示例在资源组中创建自定义主题。 用主题的唯一名称替换 `<topic_name>`。 主题名称必须唯一，因为它由 DNS 条目表示。

```azurecli-interactive
az eventgrid topic create --name <topic_name> -l westus2 -g gridResourceGroup
```

## <a name="create-a-message-endpoint"></a>创建消息终结点

在订阅主题之前, 让我们创建事件消息的终结点。 与其编写代码来响应事件，不如创建一个终结点来收集消息，方便你查看。 Hookbin 是第三方工具，用于创建终结点和查看发送到其中的请求。 转到 [Hookbin](https://hookbin.com/) 并单击“创建新终结点”。  复制 bin URL，因为在订阅自定义主题时需要它。

## <a name="subscribe-to-a-topic"></a>订阅主题

订阅主题是为了告知事件网格要跟踪哪些事件。以下示例订阅你所创建的主题，并将 Hookbin 中的 URL 作为事件通知的终结点传递。 将 `<event_subscription_name>` 替换为订阅的唯一名称，将 `<endpoint_URL>` 替换为上一部分的值。 在订阅时指定终结点，然后事件网格就会负责将事件路由到该终结点。 对于 `<topic_name>`，请使用此前创建的值。 

```azurecli-interactive
az eventgrid event-subscription create \
  -g gridResourceGroup \
  --topic-name <topic_name> \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL>
```

## <a name="send-an-event-to-your-topic"></a>向主题发送事件

让我们触发一个事件，看看事件网格如何将消息分发到终结点。 首先，让我们获取自定义主题的 URL 和密钥。 同样，请将主题名称用于 `<topic_name>`。

```azurecli-interactive
endpoint=$(az eventgrid topic show --name <topic_name> -g gridResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name <topic_name> -g gridResourceGroup --query "key1" --output tsv)
```

在本文中，为简便起见，请使用要发送到主题的示例事件数据。 通常情况下，应用程序或 Azure 服务会发送事件数据。 以下示例获取事件数据：

```azurecli-interactive
body=$(eval echo "'$(curl https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/customevent.json)'")
```

若要查看完整事件，请使用 `echo "$body"`。 JSON 的 `data` 元素是事件的有效负载。 可以将任何格式正确的 JSON 置于此字段中。 也可将主题字段用于高级路由和筛选。

CURL 是发送 HTTP 请求的实用工具。 本文使用 CURL 向主题发送事件。 

```azurecli-interactive
curl -X POST -H "aeg-sas-key: $key" -d "$body" $endpoint
```

现已触发事件，并且事件网格已将消息发送到订阅时配置的终结点。 浏览到此前创建的终结点 URL。 或者，在打开的浏览器中单击“刷新”。 此时会看到刚发送的事件。 

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
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/topics/{topic}"
}]
```

## <a name="clean-up-resources"></a>清理资源
如果打算继续处理此事件，请不要清除本文中创建的资源。 否则，请使用以下命令删除本文中创建的资源。

```azurecli-interactive
az group delete --name gridResourceGroup
```

## <a name="next-steps"></a>后续步骤

了解如何创建主题和事件订阅以后，即可进一步学习事件网格的功能：

- [关于事件网格](overview.md)
- [将 Blob 存储事件路由到自定义 Web 终结点](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)
- [通过 Azure 事件网格和逻辑应用监视虚拟机的更改](monitor-virtual-machine-changes-event-grid-logic-app.md)
- [将大数据流式传输到数据仓库](event-grid-event-hubs-integration.md)
