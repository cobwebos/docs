---
title: Azure 事件网格订阅的死信与重试策略
description: 介绍了如何自定义事件网格的事件传递设置。 设置死信目标并指定重试传递的时间间隔。
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/06/2019
ms.author: spelluru
ms.openlocfilehash: a15797e9b181aa877b6dfa3350e69b210af5885e
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2019
ms.locfileid: "55731761"
---
# <a name="dead-letter-and-retry-policies"></a>死信与重试策略

创建事件订阅时，可以自定义每个事件传递的设置。 本文介绍如何设置死信位置和自定义重试设置。 有关这些功能的信息，请参阅[事件网格消息传送和重试](delivery-and-retry.md)。

## <a name="set-dead-letter-location"></a>设置死信位置

若要设置死信位置，需要一个存储帐户来保存无法传递到终结点的事件。 这些示例获取现有存储帐户的资源 ID。 它们创建一个事件订阅，该事件订阅使用该存储帐户中的容器作为死信终结点。

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
containername=testcontainer

topicid=$(az eventgrid topic show --name demoTopic -g gridResourceGroup --query id --output tsv)
storageid=$(az storage account show --name demoStorage --resource-group gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --source-resource-id $topicid \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL> \
  --deadletter-endpoint $storageid/blobServices/default/containers/$containername
```

若要禁用死信，请重新运行命令以创建事件订阅，但不要为 `deadletter-endpoint` 提供值。 不需要删除事件订阅。

> [!NOTE]
> 如果是在本地计算机上使用 Azure CLI，请使用 Azure CLI 2.0.56 或更高版本。 有关安装 Azure CLI 的最新版本的说明，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
$containername = "testcontainer"

$topicid = (Get-AzureRmEventGridTopic -ResourceGroupName gridResourceGroup -Name demoTopic).Id
$storageid = (Get-AzureRmStorageAccount -ResourceGroupName gridResourceGroup -Name demostorage).Id

New-AzureRmEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -DeadLetterEndpoint "$storageid/blobServices/default/containers/$containername"
```

若要禁用死信，请重新运行命令以创建事件订阅，但不要为 `DeadLetterEndpoint` 提供值。 不需要删除事件订阅。

> [!NOTE]
> 如果是在本地计算机上使用 Azure Poweshell，请使用 Azure PowerShell 1.1.0 或更高版本。 从 [Azure 下载](https://azure.microsoft.com/downloads/)来下载并安装最新的 Azure PowerShell。

## <a name="set-retry-policy"></a>设置重试策略

创建事件网格订阅时，可以设置事件网格尝试使用多长时间来传递事件。 默认情况下，事件网格会尝试使用 24 小时（1440 分钟）或 30 次。 可以为你的事件网格订阅设置这些值之一。 事件生存时间的值必须是 1 到 1440 之间的整数。 最大重试次数的值必须是 1 到 30 之间的整数。

无法配置[重试计划](delivery-and-retry.md#retry-schedule-and-duration)。

### <a name="azure-cli"></a>Azure CLI

若要将事件生存期设置为 1440 分钟之外的值，请使用以下命令：

```azurecli-interactive
az eventgrid event-subscription create \
  -g gridResourceGroup \
  --topic-name <topic_name> \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL> \
  --event-ttl 720
```

若要将最大重试次数设置为 30 之外的值，请使用以下命令：

```azurecli-interactive
az eventgrid event-subscription create \
  -g gridResourceGroup \
  --topic-name <topic_name> \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL> \
  --max-delivery-attempts 18
```

如果同时设置 `event-ttl` 和 `max-deliver-attempts`，则事件网格使用先到期的项来确定何时停止事件传递。

### <a name="powershell"></a>PowerShell

若要将事件生存期设置为 1440 分钟之外的值，请使用以下命令：

```azurepowershell-interactive
$topicid = (Get-AzureRmEventGridTopic -ResourceGroupName gridResourceGroup -Name demoTopic).Id

New-AzureRmEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -EventTtl 720
```

若要将最大重试次数设置为 30 之外的值，请使用以下命令：

```azurepowershell-interactive
$topicid = (Get-AzureRmEventGridTopic -ResourceGroupName gridResourceGroup -Name demoTopic).Id

New-AzureRmEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -MaxDeliveryAttempt 18
```

如果同时设置 `EventTtl` 和 `MaxDeliveryAttempt`，则事件网格使用先到期的项来确定何时停止事件传递。

## <a name="next-steps"></a>后续步骤

* 有关使用 Azure 函数应用处理死信事件的示例应用程序，请参阅[适用于 .NET 的 Azure 事件网格死信示例](https://azure.microsoft.com/resources/samples/event-grid-dotnet-handle-deadlettered-events/)。
* 有关事件传送和重试的信息，请参阅[事件网格消息传送和重试](delivery-and-retry.md)。
* 有关事件网格的介绍，请参阅[关于事件网格](overview.md)。
* 若要快速开始使用事件网格，请参阅[使用 Azure 事件网格创建和路由自定义事件](custom-event-quickstart.md)。
