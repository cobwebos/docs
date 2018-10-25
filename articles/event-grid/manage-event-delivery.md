---
title: Azure 事件网格订阅的死信与重试策略
description: 介绍了如何自定义事件网格的事件传递设置。 设置死信目标并指定重试传递的时间间隔。
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: conceptual
ms.date: 10/10/2018
ms.author: tomfitz
ms.openlocfilehash: fcf3ecaff6e8ba1421496a96d01428946cf8ab8e
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/10/2018
ms.locfileid: "49077776"
---
# <a name="dead-letter-and-retry-policies"></a>死信与重试策略

创建事件订阅时，可以自定义每个事件传递的设置。 本文介绍如何设置死信位置和自定义重试设置。 有关这些功能的信息，请参阅[事件网格消息传送和重试](delivery-and-retry.md)。

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="set-dead-letter-location"></a>设置死信位置

若要设置死信位置，需要一个存储帐户来保存无法传递到终结点的事件。 下面的脚本获取某个现有存储帐户的资源 ID，并创建一个事件订阅，该订阅使用该存储帐户中的某个容器作为死信终结点。

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

若要禁用死信，请重新运行命令以创建事件订阅，但不要为 `deadletter-endpoint` 提供值。 不需要删除事件订阅。

## <a name="set-retry-policy"></a>设置重试策略

创建事件网格订阅时，可以设置事件网格尝试使用多长时间来传递事件。 默认情况下，事件网格尝试 24 小时（1440 分钟）并且最多尝试 30 次。 可以为你的事件网格订阅设置这些值之一。 事件生存时间的值必须是 1 到 1440 之间的整数。 最大传递尝试次数的值必须是 1 到 30 之间的整数。

无法配置[重试计划](delivery-and-retry.md#retry-schedule-and-duration)。

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
