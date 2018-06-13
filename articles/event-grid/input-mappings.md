---
title: 将自定义字段映射到 Azure 事件网格架构
description: 介绍如何将自定义架构转换为 Azure 事件网格架构。
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: tomfitz
ms.openlocfilehash: 32f93f383ec4044afb0696fcef1705c9ed65d673
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/18/2018
ms.locfileid: "34301961"
---
# <a name="map-custom-fields-to-event-grid-schema"></a>将自定义字段映射到事件网格架构

即使事件数据与预期的[事件网格架构](event-schema.md)不匹配，也仍然可以使用事件网格将事件路由到订阅服务器。 本文介绍如何将你的架构映射到事件网格架构。

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="original-event-schema"></a>原始事件架构

假设你有一个以下述格式发送事件的应用程序：

```json
[
  {
    "myEventTypeField":"Created",
    "resource":"Users/example/Messages/1000",
    "resourceData":{"someDataField1":"SomeDataFieldValue"}
  }
]
```

虽然该格式与所需的架构不符，但你仍可使用事件网格将字段映射到架构。 也可以原始架构接收值。

## <a name="create-custom-topic-with-mapped-fields"></a>使用映射的字段创建自定义主题

在创建自定义主题时，请指定如何将字段从原始事件映射到事件网格架构。 可以使用三种属性来自定义映射：

* `--input-schema` 参数指定架构类型。 可用选项为 *cloudeventv01schema*、*customeventschema* 和 *eventgridschema*。 默认值为 eventgridschema。 在你的架构和事件网格架构之间创建自定义映射时，请使用 customeventschema。 当事件采用 CloudEvents 架构时，请使用 cloudeventv01schema。

* `--input-mapping-default-values` 参数为事件网格架构中的字段指定默认值。 可以设置 *subject*、*eventtype* 和 *dataversion* 的默认值。 通常情况下，如果自定义架构不包括这三个字段中的一个的对应字段，请使用此参数。 例如，可以指定将 dataversion 始终设置为 **1.0**。

* `--input-mapping-fields` 参数将字段从你的架构映射到事件网格架构。 请以空格分隔的键/值对形式指定值。 对于键名称，请使用事件网格字段的名称。 对于值，请使用字段的名称。 可以使用 *id*、*topic*、*eventtime*、*subject*、*eventtype* 和 *dataversion* 的键名称。

以下示例创建包含某些映射字段和默认字段的自定义主题：

```azurecli-interactive
# if you have not already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid topic create \
  -n demotopic \
  -l eastus2 \
  -g myResourceGroup \
  --input-schema customeventschema
  --input-mapping-fields eventType=myEventTypeField \
  --input-mapping-default-values subject=DefaultSubject dataVersion=1.0
```

## <a name="subscribe-to-event-grid-topic"></a>订阅事件网格主题

订阅自定义主题时，请指定要用于接收事件的架构。 请使用 `--event-delivery-schema` 参数并将其设置为 *cloudeventv01schema*、*eventgridschema* 或 *inputeventschema*。 默认值为 eventgridschema。

此部分的示例使用适用于事件处理程序的队列存储。 有关详细信息，请参阅[将自定义事件路由到 Azure 队列存储](custom-event-to-queue-storage.md)。

以下示例订阅一个事件网格主题并使用默认的事件网格架构：

```azurecli-interactive
az eventgrid event-subscription create \
  --topic-name demotopic \
  -g myResourceGroup \
  --name eventsub1 \
  --endpoint-type storagequeue \
  --endpoint <storage-queue-url>
```

下一示例使用事件的输入架构：

```azurecli-interactive
az eventgrid event-subscription create \
  --topic-name demotopic \
  -g myResourceGroup \
  --name eventsub2 \
  --event-delivery-schema inputeventschema \
  --endpoint-type storagequeue \
  --endpoint <storage-queue-url>
```

## <a name="publish-event-to-topic"></a>将事件发布到主题

现在可以将事件发送到自定义主题并查看映射结果了。 以下脚本采用[示例架构](#original-event-schema)发布事件：

```azurecli-interactive
endpoint=$(az eventgrid topic show --name demotopic -g myResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name demotopic -g myResourceGroup --query "key1" --output tsv)

body=$(eval echo "'$(curl https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/mapeventfields.json)'")

curl -X POST -H "aeg-sas-key: $key" -d "$body" $endpoint
```

现在，请查看队列存储。 这两个订阅以不同架构分发了事件。

第一个订阅使用了事件网格架构。 已分发事件的格式为：

```json
{
  "Id": "016b3d68-881f-4ea3-8a9c-ed9246582abe",
  "EventTime": "2018-05-01T20:00:25.2606434Z",
  "EventType": "Created",
  "DataVersion": "1.0",
  "MetadataVersion": "1",
  "Topic": "/subscriptions/<subscription-id>/resourceGroups/myResourceGroup/providers/Microsoft.EventGrid/topics/demotopic",
  "Subject": "DefaultSubject",
  "Data": {
    "myEventTypeField": "Created",
    "resource": "Users/example/Messages/1000",
    "resourceData": { "someDataField1": "SomeDataFieldValue" } 
  }
}
```

这些字段包含来自自定义主题的映射。 **myEventTypeField** 映射到 **EventType**。 使用 **DataVersion** 和“主题”的默认值。 “数据”对象包含原始的事件架构字段。

第二个订阅使用了输入事件架构。 已分发事件的格式为：

```json
{
  "myEventTypeField": "Created",
  "resource": "Users/example/Messages/1000",
  "resourceData": { "someDataField1": "SomeDataFieldValue" }
}
```

请注意，分发的是原始字段。

## <a name="next-steps"></a>后续步骤

* 有关事件传送和重试的信息，请参阅[事件网格消息传送和重试](delivery-and-retry.md)。
* 有关事件网格的介绍，请参阅[关于事件网格](overview.md)。
* 若要快速开始使用事件网格，请参阅[使用 Azure 事件网格创建和路由自定义事件](custom-event-quickstart.md)。
