---
title: 快速入门：将自定义事件发送到事件中心 - 事件网格、Azure CLI
description: 快速入门：使用 Azure 事件网格和 Azure CLI 发布一个主题，然后订阅该事件。 事件中心用于终结点。
services: event-grid
keywords: ''
author: spelluru
ms.author: spelluru
ms.date: 11/05/2019
ms.topic: quickstart
ms.service: event-grid
ms.custom: seodec18
ms.openlocfilehash: 832c4e453befc9e54051c968e0c364d22afd2bc2
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "73721421"
---
# <a name="quickstart-route-custom-events-to-azure-event-hubs-with-azure-cli-and-event-grid"></a>快速入门：使用 Azure CLI 和事件网格将自定义事件路由到 Azure 事件中心

Azure 事件网格是针对云的事件处理服务。 Azure 事件中心是受支持的事件处理程序之一。 在本文中，将使用 Azure CLI 创建一个自定义主题，然后订阅该自定义主题，再触发可查看结果的事件。 将事件发送到事件中心。

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-resource-group"></a>创建资源组

事件网格主题是 Azure 资源，必须放置在 Azure 资源组中。 该资源组是在其中部署和管理 Azure 资源的逻辑集合。

使用“[az group create](/cli/azure/group#az-group-create)”命令创建资源组。 

以下示例在“westus2”  位置创建名为“gridResourceGroup”  的资源组。

```azurecli-interactive
az group create --name gridResourceGroup --location westus2
```

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="create-a-custom-topic"></a>创建自定义主题

事件网格主题提供用户定义的终结点，可向其发布事件。 以下示例在资源组中创建自定义主题。 将 `<your-topic-name>` 替换为自定义主题的唯一名称。 自定义主题名称必须唯一，因为它由 DNS 条目表示。

```azurecli-interactive
topicname=<your-topic-name>
az eventgrid topic create --name $topicname -l westus2 -g gridResourceGroup
```

## <a name="create-event-hub"></a>创建事件中心

在订阅自定义主题之前，让我们创建事件消息的终结点。 创建用于收集事件的事件中心。

```azurecli-interactive
namespace=<unique-namespace-name>
hubname=demohub

az eventhubs namespace create --name $namespace --resource-group gridResourceGroup
az eventhubs eventhub create --name $hubname --namespace-name $namespace --resource-group gridResourceGroup
```

## <a name="subscribe-to-a-custom-topic"></a>订阅自定义主题

订阅事件网格主题是为了告知事件网格要跟踪哪些事件。以下示例订阅所创建的自定义主题，并传递终结点事件中心的资源 ID。 终结点的格式如下：

`/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.EventHub/namespaces/<namespace-name>/eventhubs/<hub-name>`

以下脚本获取事件中心的资源 ID，并订阅事件网格主题。 它将终结点类型设置为 `eventhub` 并使用终结点的事件中心 ID。

```azurecli-interactive
hubid=$(az eventhubs eventhub show --name $hubname --namespace-name $namespace --resource-group gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --topic-name $topicname \
  -g gridResourceGroup \
  --name subtoeventhub \
  --endpoint-type eventhub \
  --endpoint $hubid
```

创建事件订阅的帐户必须对事件中心具有写访问权限。

## <a name="send-an-event-to-your-custom-topic"></a>向自定义主题发送事件

让我们触发一个事件，看看事件网格如何将消息分发到终结点。 首先，让我们获取自定义主题的 URL 和密钥。

```azurecli-interactive
endpoint=$(az eventgrid topic show --name $topicname -g gridResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name $topicname -g gridResourceGroup --query "key1" --output tsv)
```

为简化本文，将使用要发送到自定义主题的示例事件数据。 通常情况下，应用程序或 Azure 服务会发送事件数据。 CURL 是发送 HTTP 请求的实用工具。 本文使用 CURL 向自定义主题发送事件。  以下示例将三个事件发送到事件网格主题：

```azurecli-interactive
for i in 1 2 3
do
   event='[ {"id": "'"$RANDOM"'", "eventType": "recordInserted", "subject": "myapp/vehicles/motorcycles", "eventTime": "'`date +%Y-%m-%dT%H:%M:%S%z`'", "data":{ "make": "Ducati", "model": "Monster"},"dataVersion": "1.0"} ]'
   curl -X POST -H "aeg-sas-key: $key" -d "$event" $endpoint
done
```

导航到门户中的事件中心，请注意，事件网格会将这三个事件发送到事件中心。

![显示消息](./media/custom-event-to-eventhub/show-result.png)

通常，你将创建一个从事件中心检索事件的应用程序。 若要创建从事件中心获取消息的应用程序，请参阅：

* [使用 .NET Standard 中的事件处理程序主机接收消息入门](../event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph.md)
* [使用 Java 从 Azure 事件中心接收事件](../event-hubs/event-hubs-java-get-started-receive-eph.md)
* [使用 Apache Storm 从事件中心接收事件](../event-hubs/event-hubs-storm-getstarted-receive.md)

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
