---
title: 在 CloudEvents 架构中将 Azure 事件网格与事件配合使用
description: 说明如何针对 Azure 事件网格中的事件设置 CloudEvents 架构。
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: babanisa
ms.openlocfilehash: 6a0e24ce7fa11c6373fbaada40cd9f1b1e7f55a2
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/22/2019
ms.locfileid: "74325476"
---
# <a name="use-cloudevents-v10-schema-with-event-grid"></a>将 CloudEvents 1.0 版架构与事件网格结合使用

Azure 事件网格除了[默认的事件架构](event-schema.md)外，还支持 CloudEvents V1.0 和[HTTP 协议绑定](https://github.com/cloudevents/spec/blob/v1.0/http-protocol-binding.md)的[JSON 实现](https://github.com/cloudevents/spec/blob/v1.0/json-format.md)中的事件。 [CloudEvents](https://cloudevents.io/) 是一种用于描述事件数据的[开放规范](https://github.com/cloudevents/spec/blob/v1.0/spec.md)。

CloudEvents 提供的常用事件架构适合发布和使用基于云的事件，因此可简化互操作性。 可以通过此架构使用统一的工具、以标准方式路由和处理事件，以及以通用方式反序列化外部事件架构。 使用通用架构可以更轻松地跨平台集成工作。

CloudEvents 是由包括 Microsoft 在内的多个[协作者](https://github.com/cloudevents/spec/blob/master/community/contributors.md)通过 [Cloud Native Computing Foundation](https://www.cncf.io/) 构建的。 目前提供的版本为1.0。

本文介绍如何将 CloudEvents 架构与事件网格配合使用。

[!INCLUDE [requires-azurerm](../../includes/requires-azurerm.md)]

## <a name="install-preview-feature"></a>安装预览功能

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="cloudevent-schema"></a>CloudEvent 架构

下面以示例方式说明了 CloudEvents 格式的 Azure Blob 存储事件：

``` JSON
{
    "specversion": "1.0",
    "type": "Microsoft.Storage.BlobCreated",  
    "source": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-account}",
    "id": "9aeb0fdf-c01e-0131-0922-9eb54906e209",
    "time": "2019-11-18T15:13:39.4589254Z",
    "subject": "blobServices/default/containers/{storage-container}/blobs/{new-file}",
    "dataschema": "#",
    "data": {
        "api": "PutBlockList",
        "clientRequestId": "4c5dd7fb-2c48-4a27-bb30-5361b5de920a",
        "requestId": "9aeb0fdf-c01e-0131-0922-9eb549000000",
        "eTag": "0x8D76C39E4407333",
        "contentType": "image/png",
        "contentLength": 30699,
        "blobType": "BlockBlob",
        "url": "https://gridtesting.blob.core.windows.net/testcontainer/{new-file}",
        "sequencer": "000000000000000000000000000099240000000000c41c18",
        "storageDiagnostics": {
            "batchId": "681fe319-3006-00a8-0022-9e7cde000000"
        }
    }
}
```

[可在此处](https://github.com/cloudevents/spec/blob/v1.0/spec.md#required-attributes)查看可用字段、其类型和 CloudEvents v 0.1 中的定义的详细说明。

在 CloudEvents 架构和事件网格架构中传递的事件的标头值是相同的，但 `content-type` 除外。 对于 CloudEvents 架构，该标头值为 `"content-type":"application/cloudevents+json; charset=utf-8"`。 对于事件网格架构，该标头值为 `"content-type":"application/json; charset=utf-8"`。

## <a name="configure-event-grid-for-cloudevents"></a>为 CloudEvents 配置事件网格

可以将事件网格用于 CloudEvents 架构的事件的输入和输出。 可以将 CloudEvents 用于系统事件（例如 Blob 存储事件和 IoT 中心事件）和自定义事件。 它还可以将网络上的这些事件来回转换。


| 输入架构       | 输出架构
|--------------------|---------------------
| CloudEvents 格式 | CloudEvents 格式
| 事件网格格式  | CloudEvents 格式
| CloudEvents 格式 | 事件网格格式
| 事件网格格式  | 事件网格格式

对于所有事件架构，事件网格都要求在发布到事件网格主题时以及在创建事件订阅时进行验证。 有关详细信息，请参阅[事件网格安全性和身份验证](security-authentication.md)。

### <a name="input-schema"></a>输入架构

在创建自定义主题时为自定义主题设置输入架构。

对于 Azure CLI，请使用：

```azurecli-interactive
# If you have not already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid topic create \
  --name <topic_name> \
  -l westcentralus \
  -g gridResourceGroup \
  --input-schema cloudeventschemav1_0
```

对于 PowerShell，请使用：

```azurepowershell-interactive
# If you have not already installed the module, do it now.
# This module is required for preview features.
Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery

New-AzureRmEventGridTopic `
  -ResourceGroupName gridResourceGroup `
  -Location westcentralus `
  -Name <topic_name> `
  -InputSchema CloudEventSchemaV1_0
```

CloudEvents 的当前版本不支持对事件进行批处理。 若要使用 CloudEvent 架构将事件发布到某个主题，请单独发布每个事件。

### <a name="output-schema"></a>输出架构

在创建事件订阅时设置输出架构。

对于 Azure CLI，请使用：

```azurecli-interactive
topicID=$(az eventgrid topic show --name <topic-name> -g gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --name <event_subscription_name> \
  --source-resource-id $topicID \
  --endpoint <endpoint_URL> \
  --event-delivery-schema cloudeventschemav1_0
```

对于 PowerShell，请使用：
```azurepowershell-interactive
$topicid = (Get-AzureRmEventGridTopic -ResourceGroupName gridResourceGroup -Name <topic-name>).Id

New-AzureRmEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -DeliverySchema CloudEventSchemaV1_0
```

 目前，在以 CloudEvents 架构传递事件时，无法为 Azure Functions 应用使用事件网格触发器。 使用 HTTP 触发器。 有关实现在 CloudEvents 架构中接收事件的 HTTP 触发器的示例，请参阅[使用 HTTP 触发器作为事件网格触发器](../azure-functions/functions-bindings-event-grid.md#use-an-http-trigger-as-an-event-grid-trigger)。

 ## <a name="endpoint-validation-with-cloudevents-v10"></a>用 CloudEvents 1.0 版进行终结点验证

如果已熟悉事件网格，可能会注意到事件网格的终结点验证握手，以防止滥用。 CloudEvents 1.0 版使用 HTTP OPTIONS 方法实现自己的[滥用保护语义](security-authentication.md#webhook-event-delivery)。 可以在 [此处](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#4-abuse-protection)阅读详细内容。 使用 CloudEvents 架构进行输出时，事件网格将与 CloudEvents v1.0 滥用保护一起使用，以取代事件网格验证事件机制。

## <a name="next-steps"></a>后续步骤

* 有关监视事件传送的信息，请参阅[监视事件网格消息传送](monitor-event-delivery.md)。
* 我们鼓励你对 CloudEvents 进行测试、评论并亲自[参与](https://github.com/cloudevents/spec/blob/master/CONTRIBUTING.md)进来。
* 有关创建 Azure 事件网格订阅的详细信息，请参阅[事件网格订阅架构](subscription-creation-schema.md)。
