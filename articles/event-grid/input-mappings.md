---
title: 将自定义字段映射到 Azure 事件网格架构
description: 本文介绍当事件数据与事件网格架构不匹配时，如何将自定义架构转换为 Azure 事件网格架构。
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: e8077068a265d659cf6009eb7762188637c373d6
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721653"
---
# <a name="map-custom-fields-to-event-grid-schema"></a>将自定义字段映射到事件网格架构

即使事件数据与预期的[事件网格架构](event-schema.md)不匹配，也仍然可以使用事件网格将事件路由到订阅服务器。 本文介绍如何将你的架构映射到事件网格架构。

[!INCLUDE [requires-azurerm](../../includes/requires-azurerm.md)]

## <a name="install-preview-feature"></a>安装预览功能

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

在创建自定义主题时，请指定如何将字段从原始事件映射到事件网格架构。 可以使用三个值来自定义映射：

* **输入架构**值指定架构的类型。 可用选项包括 CloudEvents 架构、自定义事件架构或事件网格架构。 默认值为事件网格架构。 在你的架构和事件网格架构之间创建自定义映射时，请使用自定义事件架构。 当事件采用 CloudEvents 架构时，请使用 Cloudevents 架构。

* “映射默认值”属性指定事件网格架构中字段的默认值。 可以为 `subject`、`eventtype` 和 `dataversion` 设置默认值。 通常情况下，如果自定义架构不包括这三个字段中的一个的对应字段，请使用此参数。 例如，可以指定将数据版本始终设置为 **1.0**。

* “映射字段”值将字段从你的架构映射到事件网格架构。 请以空格分隔的键/值对形式指定值。 对于键名称，请使用事件网格字段的名称。 对于值，请使用字段的名称。 可以对 `id`、`topic`、`eventtime`、`subject`、`eventtype` 和 `dataversion` 使用键名称。

要使用 Azure CLI 创建自定义主题，请使用：

```azurecli-interactive
# If you have not already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid topic create \
  -n demotopic \
  -l eastus2 \
  -g myResourceGroup \
  --input-schema customeventschema \
  --input-mapping-fields eventType=myEventTypeField \
  --input-mapping-default-values subject=DefaultSubject dataVersion=1.0
```

对于 PowerShell，请使用：

```azurepowershell-interactive
# If you have not already installed the module, do it now.
# This module is required for preview features.
Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery

New-AzureRmEventGridTopic `
  -ResourceGroupName myResourceGroup `
  -Name demotopic `
  -Location eastus2 `
  -InputSchema CustomEventSchema `
  -InputMappingField @{eventType="myEventTypeField"} `
  -InputMappingDefaultValue @{subject="DefaultSubject"; dataVersion="1.0" }
```

## <a name="subscribe-to-event-grid-topic"></a>订阅事件网格主题

订阅自定义主题时，请指定要用于接收事件的架构。 可以指定 CloudEvents 架构、自定义事件架构或事件网格架构。 默认值为事件网格架构。

以下示例订阅一个事件网格主题并使用事件网格架构。 对于 Azure CLI，请使用：

```azurecli-interactive
topicid=$(az eventgrid topic show --name demoTopic -g myResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --source-resource-id $topicid \
  --name eventsub1 \
  --event-delivery-schema eventgridschema \
  --endpoint <endpoint_URL>
```

下一示例使用事件的输入架构：

```azurecli-interactive
az eventgrid event-subscription create \
  --source-resource-id $topicid \
  --name eventsub2 \
  --event-delivery-schema custominputschema \
  --endpoint <endpoint_URL>
```

以下示例订阅一个事件网格主题并使用事件网格架构。 对于 PowerShell，请使用：

```azurepowershell-interactive
$topicid = (Get-AzureRmEventGridTopic -ResourceGroupName myResourceGroup -Name demoTopic).Id

New-AzureRmEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName eventsub1 `
  -EndpointType webhook `
  -Endpoint <endpoint-url> `
  -DeliverySchema EventGridSchema
```

下一示例使用事件的输入架构：

```azurepowershell-interactive
New-AzureRmEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName eventsub2 `
  -EndpointType webhook `
  -Endpoint <endpoint-url> `
  -DeliverySchema CustomInputSchema
```

## <a name="publish-event-to-topic"></a>将事件发布到主题

现在可以将事件发送到自定义主题并查看映射结果了。 以下脚本采用[示例架构](#original-event-schema)发布事件：

对于 Azure CLI，请使用：

```azurecli-interactive
endpoint=$(az eventgrid topic show --name demotopic -g myResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name demotopic -g myResourceGroup --query "key1" --output tsv)

event='[ { "myEventTypeField":"Created", "resource":"Users/example/Messages/1000", "resourceData":{"someDataField1":"SomeDataFieldValue"} } ]'

curl -X POST -H "aeg-sas-key: $key" -d "$event" $endpoint
```

对于 PowerShell，请使用：

```azurepowershell-interactive
$endpoint = (Get-AzureRmEventGridTopic -ResourceGroupName myResourceGroup -Name demotopic).Endpoint
$keys = Get-AzureRmEventGridTopicKey -ResourceGroupName myResourceGroup -Name demotopic

$htbody = @{
    myEventTypeField="Created"
    resource="Users/example/Messages/1000"
    resourceData= @{
        someDataField1="SomeDataFieldValue"
    }
}

$body = "["+(ConvertTo-Json $htbody)+"]"
Invoke-WebRequest -Uri $endpoint -Method POST -Body $body -Headers @{"aeg-sas-key" = $keys.Key1}
```

现在，请查看 WebHook 终结点。 这两个订阅以不同架构分发了事件。

第一个订阅使用了事件网格架构。 已分发事件的格式为：

```json
{
  "id": "aa5b8e2a-1235-4032-be8f-5223395b9eae",
  "eventTime": "2018-11-07T23:59:14.7997564Z",
  "eventType": "Created",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "topic": "/subscriptions/<subscription-id>/resourceGroups/myResourceGroup/providers/Microsoft.EventGrid/topics/demotopic",
  "subject": "DefaultSubject",
  "data": {
    "myEventTypeField": "Created",
    "resource": "Users/example/Messages/1000",
    "resourceData": {
      "someDataField1": "SomeDataFieldValue"
    }
  }
}
```

这些字段包含来自自定义主题的映射。 **myEventTypeField** 映射到 **EventType**。 使用 **DataVersion** 和“主题”的默认值。 “数据”对象包含原始的事件架构字段。

第二个订阅使用了输入事件架构。 已分发事件的格式为：

```json
{
  "myEventTypeField": "Created",
  "resource": "Users/example/Messages/1000",
  "resourceData": {
    "someDataField1": "SomeDataFieldValue"
  }
}
```

请注意，分发的是原始字段。

## <a name="next-steps"></a>后续步骤

* 有关事件传送和重试的信息，请参阅[事件网格消息传送和重试](delivery-and-retry.md)。
* 有关事件网格的介绍，请参阅[关于事件网格](overview.md)。
* 若要快速开始使用事件网格，请参阅[使用 Azure 事件网格创建和路由自定义事件](custom-event-quickstart.md)。
