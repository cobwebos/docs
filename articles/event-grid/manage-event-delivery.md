---
title: Azure 事件网格订阅的死信与重试策略
description: 介绍了如何自定义事件网格的事件传递设置。 设置死信目标并指定重试传递的时间间隔。
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 08/03/2018
ms.author: tomfitz
ms.openlocfilehash: 5a37fadc179157ba590b31a79fcd98f223cb1869
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2018
ms.locfileid: "39501943"
---
# <a name="dead-letter-and-retry-policies"></a>死信与重试策略

创建事件订阅时，可以自定义每个事件传递的设置。 可以设置事件网格尝试使用多长时间来传递消息。 可以设置存储帐户来用于存储无法传递到终结点的事件。

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="set-dead-letter-location"></a>设置死信位置

当事件网格无法传递事件时，它会将未送达的事件发送到某个存储帐户。 此过程称为死信处理。 默认情况下，事件网格不启用死信处理。 若要启用该功能，在创建事件订阅时必须指定一个存储帐户来存放未送达的事件。 你将从此存储帐户中拉取事件来解决传递问题。

如果事件网格已尝试了它的所有重试尝试，或者收到了指明传递永远不会成功的错误消息，则事件网格会将事件发送到死信位置。 例如，如果事件网格在传递某个事件时收到了格式不正确错误，则它会将该事件发送到死信位置。 最后一次尝试发送事件与发送到死信位置之间有五分钟的延迟。 此延迟旨在减少 Blob 存储操作的数量。 如果死信位置四小时不可用，则会丢弃该事件。

在设置死信位置之前，必须有一个包含容器的存储帐户。 在创建事件订阅时，需要提供此容器的终结点。 终结点的格式如下：`/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-name>/blobServices/default/containers/<container-name>`

下面的脚本获取某个现有存储帐户的资源 ID，并创建一个事件订阅，该订阅使用该存储帐户中的某个容器作为死信终结点。

```azurecli-interactive
# if you have not already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

storagename=demostorage
containername=testcontainer

storageid=$(az storage account show --name $storagename --resource-group gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  -g gridResourceGroup \
  --topic-name <topic_name> \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL> \
  --deadletter-endpoint $storageid/blobServices/default/containers/$containername
```

若要使用事件网格来响应未送达的事件，请为死信 blob 存储[创建事件订阅](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)。 每当死信 blob 存储收到未送达的事件时，事件网格都会通知处理程序。 处理程序使用你希望采取的、用于协调未送达的事件的操作进行响应。 

若要禁用死信，请重新运行命令以创建事件订阅，但不要为 `deadletter-endpoint` 提供值。 不需要删除事件订阅。

## <a name="set-retry-policy"></a>设置重试策略

创建事件网格订阅时，可以设置事件网格尝试使用多长时间来传递事件。 默认情况下，事件网格尝试 24 小时（1440 分钟）并且最多尝试 30 次。 可以为你的事件网格订阅设置这些值之一。 事件生存时间的值必须是 1 到 1440 之间的整数。 最大传递尝试次数的值必须是 1 到 30 之间的整数。

无法配置[重试间隔](delivery-and-retry.md#retry-intervals-and-duration)。

若要将事件生存期设置为 1440 分钟之外的值，请使用以下命令：

```azurecli-interactive
# if you have not already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid event-subscription create \
  -g gridResourceGroup \
  --topic-name <topic_name> \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL> \
  --event-ttl 720
```

若要将最大重试尝试次数设置为 30 之外的值，请使用以下命令：

```azurecli-interactive
az eventgrid event-subscription create \
  -g gridResourceGroup \
  --topic-name <topic_name> \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL> \
  --max-delivery-attempts 18
```

如果同时设置了 `event-ttl` 和 `max-deliver-attempts`，则事件网格将使用首先到期的设置进行重试尝试。

## <a name="next-steps"></a>后续步骤

* 有关使用 Azure 函数应用处理死信事件的示例应用程序，请参阅[适用于 .NET 的 Azure 事件网格死信示例](https://azure.microsoft.com/resources/samples/event-grid-dotnet-handle-deadlettered-events/)。
* 有关事件传送和重试的信息，请参阅[事件网格消息传送和重试](delivery-and-retry.md)。
* 有关事件网格的介绍，请参阅[关于事件网格](overview.md)。
* 若要快速开始使用事件网格，请参阅[使用 Azure 事件网格创建和路由自定义事件](custom-event-quickstart.md)。
