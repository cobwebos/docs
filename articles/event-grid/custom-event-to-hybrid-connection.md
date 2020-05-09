---
title: 教程：将自定义事件发送到混合连接 - 事件网格
description: 教程：使用 Azure 事件网格和 Azure CLI 发布一个主题，然后订阅该事件。 混合连接用于终结点。
services: event-grid
keywords: ''
author: spelluru
ms.author: spelluru
ms.date: 11/05/2019
ms.topic: tutorial
ms.service: event-grid
ms.custom: seodec18
ms.openlocfilehash: 2773d06fdfc606faaab88340fc2ef9685bba21f9
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "73720700"
---
# <a name="tutorial-route-custom-events-to-azure-relay-hybrid-connections-with-azure-cli-and-event-grid"></a>教程：使用 Azure CLI 和事件网格将自定义事件路由到 Azure 中继混合连接

Azure 事件网格是针对云的事件处理服务。 Azure 中继混合连接是支持的事件处理程序之一。 需要处理没有公共终结点的应用程序的事件时，可将混合连接用作事件处理程序。 这些应用程序可能位于企业网络中。 在本文中，将使用 Azure CLI 创建一个自定义主题，然后订阅该自定义主题，再触发可查看结果的事件。 将事件发送到混合连接。

## <a name="prerequisites"></a>先决条件

本文假定已有混合连接和侦听器应用程序。 若要开始使用混合连接，请参阅[中继混合连接入门 - .NET](../service-bus-relay/relay-hybrid-connections-dotnet-get-started.md) 或[中继混合连接入门 - Node](../service-bus-relay/relay-hybrid-connections-node-get-started.md)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!NOTE]
> 如果是在本地计算机上使用 Azure CLI，请使用 Azure CLI 2.0.56 或更高版本。 有关安装 Azure CLI 的最新版本的说明，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="create-a-resource-group"></a>创建资源组

事件网格主题是 Azure 资源，必须放置在 Azure 资源组中。 该资源组是在其中部署和管理 Azure 资源的逻辑集合。

使用“[az group create](/cli/azure/group#az-group-create)”命令创建资源组。 

以下示例在“westus2”  位置创建名为“gridResourceGroup”  的资源组。

```azurecli-interactive
az group create --name gridResourceGroup --location westus2
```

## <a name="create-a-custom-topic"></a>创建自定义主题

事件网格主题提供用户定义的终结点，可向其发布事件。 以下示例在资源组中创建自定义主题。 将 `<topic_name>` 替换为自定义主题的唯一名称。 事件网格主题名称必须唯一，因为它由 DNS 条目表示。

```azurecli-interactive
az eventgrid topic create --name <topic_name> -l westus2 -g gridResourceGroup
```

## <a name="subscribe-to-a-custom-topic"></a>订阅自定义主题

订阅事件网格主题是为了告知事件网格要跟踪哪些事件。以下示例订阅所创建的自定义主题，并传递终结点混合连接的资源 ID。 混合连接 ID 采用以下格式：

`/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Relay/namespaces/<relay-namespace>/hybridConnections/<hybrid-connection-name>`

以下脚本获取中继命名空间的资源 ID。 它构造混合连接的 ID 并订阅事件网格主题。 该脚本将终结点类型设置为 `hybridconnection` 并使用终结点的混合连接 ID。

```azurecli-interactive
relayname=<namespace-name>
relayrg=<resource-group-for-relay>
hybridname=<hybrid-name>

relayid=$(az resource show --name $relayname --resource-group $relayrg --resource-type Microsoft.Relay/namespaces --query id --output tsv)
hybridid="$relayid/hybridConnections/$hybridname"
topicid=$(az eventgrid topic show --name <topic_name> -g gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --source-resource-id $topicid \
  --name <event_subscription_name> \
  --endpoint-type hybridconnection \
  --endpoint $hybridid \
  --expiration-date "<yyyy-mm-dd>"
```

请注意为订阅设置[到期日期](concepts.md#event-subscription-expiration)。

## <a name="create-application-to-process-events"></a>创建应用程序以处理事件

需要一个可以从混合连接检索事件的应用程序。 [适用于 C# 的 Microsoft Azure 事件网格混合连接使用者示例](https://github.com/Azure-Samples/event-grid-dotnet-hybridconnection-destination)可执行该操作。 已完成先决条件步骤。

1. 确保具有 Visual Studio 2019 或更高版本。

1. 将存储库克隆到本地计算机。

1. 在 Visual Studio 中加载 HybridConnectionConsumer 项目。

1. 在 Program.cs 中，将 `<relayConnectionString>` 和 `<hybridConnectionName>` 替换为创建的中继连接字符串和混合连接名称。

1. 在 Visual Studio 中编译并运行应用程序。

## <a name="send-an-event-to-your-topic"></a>向主题发送事件

让我们触发一个事件，看看事件网格如何将消息分发到终结点。 本文介绍如何使用 Azure CLI 来触发事件。 或者，可以使用[事件网格发布者应用程序](https://github.com/Azure-Samples/event-grid-dotnet-publish-consume-events/tree/master/EventGridPublisher)。

首先，让我们获取自定义主题的 URL 和密钥。 同样，请将自定义主题名称用于 `<topic_name>`。

```azurecli-interactive
endpoint=$(az eventgrid topic show --name <topic_name> -g gridResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name <topic_name> -g gridResourceGroup --query "key1" --output tsv)
```

为简化本文，将使用要发送到自定义主题的示例事件数据。 通常情况下，应用程序或 Azure 服务会发送事件数据。 CURL 是发送 HTTP 请求的实用工具。 本文使用 CURL 向自定义主题发送事件。

```azurecli-interactive
event='[ {"id": "'"$RANDOM"'", "eventType": "recordInserted", "subject": "myapp/vehicles/motorcycles", "eventTime": "'`date +%Y-%m-%dT%H:%M:%S%z`'", "data":{ "make": "Ducati", "model": "Monster"},"dataVersion": "1.0"} ]'
curl -X POST -H "aeg-sas-key: $key" -d "$event" $endpoint
```

侦听器应用程序应接收事件消息。

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
