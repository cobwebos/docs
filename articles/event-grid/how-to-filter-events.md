---
title: 如何筛选 Azure 事件网格的事件
description: 显示如何创建筛选事件的 Azure 事件网格订阅。
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: tomfitz
ms.openlocfilehash: 8bf7ac9daf928c35a3d6efcac528d3372fa87c8a
ms.sourcegitcommit: 1d3353b95e0de04d4aec2d0d6f84ec45deaaf6ae
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2018
ms.locfileid: "50252039"
---
# <a name="filter-events-for-event-grid"></a>筛选事件网格的事件

本文介绍如何在创建事件网格订阅时筛选事件。 若要了解事件筛选的选项，请参阅[了解事件网格订阅的事件筛选](event-filtering.md)。

## <a name="filter-by-event-type"></a>按事件类型筛选

创建事件网格订阅时，可以指定要发送到终结点的[事件类型](event-schema.md)。 本部分中的示例创建资源组的事件订阅，但会限制发送到 `Microsoft.Resources.ResourceWriteFailure` 和 `Microsoft.Resources.ResourceWriteSuccess` 的事件。 如果在按事件类型筛选事件时需要更多灵活性，请参阅[按高级运算符和数据字段筛选](#filter-by-advanced-operators-and-data-fields)。

对于 PowerShell，请在创建订阅时使用 `-IncludedEventType` 参数。

```powershell
$includedEventTypes = "Microsoft.Resources.ResourceWriteFailure", "Microsoft.Resources.ResourceWriteSuccess"

New-AzureRmEventGridSubscription `
  -EventSubscriptionName demoSubToResourceGroup `
  -ResourceGroupName myResourceGroup `
  -Endpoint <endpoint-URL> `
  -IncludedEventType $includedEventTypes
```

对于 Azure CLI，请使用 `--included-event-types` 参数。 以下示例在 Bash shell 中使用 Azure CLI：

```azurecli
includedEventTypes="Microsoft.Resources.ResourceWriteFailure Microsoft.Resources.ResourceWriteSuccess"

az eventgrid event-subscription create \
  --name demoSubToResourceGroup \
  --resource-group myResourceGroup \
  --endpoint <endpoint-URL> \
  --included-event-types $includedEventTypes
```

对于资源管理器模板，请使用 `includedEventTypes` 属性。

```json
"resources": [
  {
    "type": "Microsoft.EventGrid/eventSubscriptions",
    "name": "[parameters('eventSubName')]",
    "apiVersion": "2018-09-15-preview",
    "properties": {
      "destination": {
        "endpointType": "WebHook",
        "properties": {
          "endpointUrl": "[parameters('endpoint')]"
        }
      },
      "filter": {
        "subjectBeginsWith": "",
        "subjectEndsWith": "",
        "isSubjectCaseSensitive": false,
        "includedEventTypes": [
          "Microsoft.Resources.ResourceWriteFailure",
          "Microsoft.Resources.ResourceWriteSuccess"
        ]
      }
    }
  }
]
```

## <a name="filter-by-subject"></a>按主题筛选

可以按事件数据中的主题筛选事件。 可以指定一个值来匹配主题的开头或结尾。 如果在按主题筛选事件时需要更多灵活性，请参阅[按高级运算符和数据字段筛选 ](#filter-by-advanced-operators-and-data-fields)。

在以下 PowerShell 示例中，会创建一个按主题开头筛选的事件订阅。 使用 `-SubjectBeginsWith` 参数将事件限制为特定资源的事件。 然后传递网络安全组的资源 ID。

```powershell
$resourceId = (Get-AzureRmResource -ResourceName demoSecurityGroup -ResourceGroupName myResourceGroup).ResourceId

New-AzureRmEventGridSubscription `
  -Endpoint <endpoint-URL> `
  -EventSubscriptionName demoSubscriptionToResourceGroup `
  -ResourceGroupName myResourceGroup `
  -SubjectBeginsWith $resourceId
```

下一个 PowerShell 示例创建 blob 存储的订阅。 其中将事件限制为主题以 `.jpg` 结尾的事件。

```powershell
$storageId = (Get-AzureRmStorageAccount -ResourceGroupName myResourceGroup -AccountName $storageName).Id

New-AzureRmEventGridSubscription `
  -EventSubscriptionName demoSubToStorage `
  -Endpoint <endpoint-URL> `
  -ResourceId $storageId `
  -SubjectEndsWith ".jpg"
```

在以下 Azure CLI 示例中，会创建一个按主题开头筛选的事件订阅。 使用 `--subject-begins-with` 参数将事件限制为特定资源的事件。 然后传递网络安全组的资源 ID。

```azurecli
resourceId=$(az resource show --name demoSecurityGroup --resource-group myResourceGroup --resource-type Microsoft.Network/networkSecurityGroups --query id --output tsv)

az eventgrid event-subscription create \
  --name demoSubscriptionToResourceGroup \
  --resource-group myResourceGroup \
  --endpoint <endpoint-URL> \
  --subject-begins-with $resourceId
```

下一个 Azure CLI 示例创建 blob 存储的订阅。 其中将事件限制为主题以 `.jpg` 结尾的事件。

```azurecli
storageid=$(az storage account show --name $storageName --resource-group myResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --resource-id $storageid \
  --name demoSubToStorage \
  --endpoint <endpoint-URL> \
  --subject-ends-with ".jpg"
```

在以下资源管理器模板示例中，会创建一个按主题开头筛选的事件订阅。 使用 `subjectBeginsWith` 属性将事件限制为特定资源的事件。 然后传递网络安全组的资源 ID。

```json
"resources": [
  {
    "type": "Microsoft.EventGrid/eventSubscriptions",
    "name": "[parameters('eventSubName')]",
    "apiVersion": "2018-09-15-preview",
    "properties": {
      "destination": {
        "endpointType": "WebHook",
        "properties": {
          "endpointUrl": "[parameters('endpoint')]"
        }
      },
      "filter": {
        "subjectBeginsWith": "[resourceId('Microsoft.Network/networkSecurityGroups','demoSecurityGroup')]",
        "subjectEndsWith": "",
        "isSubjectCaseSensitive": false,
        "includedEventTypes": [ "All" ]
      }
    }
  }
]
```

下一个资源管理模板示例创建 blob 存储的订阅。 其中将事件限制为主题以 `.jpg` 结尾的事件。

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts/providers/eventSubscriptions",
    "name": "[concat(parameters('storageName'), '/Microsoft.EventGrid/', parameters('eventSubName'))]",
    "apiVersion": "2018-09-15-preview",
    "properties": {
      "destination": {
        "endpointType": "WebHook",
        "properties": {
          "endpointUrl": "[parameters('endpoint')]"
        }
      },
      "filter": {
        "subjectEndsWith": ".jpg",
        "subjectBeginsWith": "",
        "isSubjectCaseSensitive": false,
        "includedEventTypes": [ "All" ]
      }
    }
  }
]
```

## <a name="filter-by-operators-and-data"></a>按运算符和数据进行筛选

若要使用高级筛选，必须安装 Azure CLI 的预览扩展。 可以使用 [CloudShell ](/azure/cloud-shell/quickstart) 或在本地安装 Azure CLI。

### <a name="install-extension"></a>安装扩展

在 CloudShell 中：

* 如果之前已安装此扩展，请使用 `az extension update -n eventgrid` 进行更新
* 如果之前尚未安装此扩展，请使用 `az extension add -n eventgrid` 安装

对于本地安装：

1. 在本地卸载 Azure CLI。
1. 安装[最新版本](/cli/azure/install-azure-cli)的 Azure CLI。
1. 启动命令窗口。
1. 卸载早期版本的扩展 `az extension remove -n eventgrid`
1. 安装扩展 `az extension add -n eventgrid`

现在便可以使用高级筛选。

### <a name="subscribe-with-advanced-filters"></a>订阅高级筛选器

若要了解可用于高级筛选的运算符和密钥，请参阅[高级筛选](event-filtering.md#advanced-filtering)。

以下示例创建自定义主题。 该示例订阅自定义主题，并按数据对象中的值进行筛选。 将颜色属性设置为“蓝色”、“红色”或“绿色”的事件会发送到订阅。

```azurecli-interactive
topicName=<your-topic-name>
endpointURL=<endpoint-URL>

az group create -n gridResourceGroup -l eastus2
az eventgrid topic create --name $topicName -l eastus2 -g gridResourceGroup

topicid=$(az eventgrid topic show --name $topicName -g gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --source-resource-id $topicid \
  -n demoAdvancedSub \
  --advanced-filter data.color stringin blue red green \
  --endpoint $endpointURL \
  --expiration-date "2018-11-30"
```

请注意为订阅设置的过期日期。 事件订阅在该日期后自动过期。 为仅在有限时间内所需的事件订阅设置过期时间。

### <a name="test-filter"></a>测试筛选器

若要测试筛选器，请发送将颜色字段设置为“绿色”的事件。

```azurecli-interactive
topicEndpoint=$(az eventgrid topic show --name $topicName -g gridResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name $topicName -g gridResourceGroup --query "key1" --output tsv)

event='[ {"id": "'"$RANDOM"'", "eventType": "recordInserted", "subject": "myapp/vehicles/cars", "eventTime": "'`date +%Y-%m-%dT%H:%M:%S%z`'", "data":{ "model": "SUV", "color": "green"},"dataVersion": "1.0"} ]'

curl -X POST -H "aeg-sas-key: $key" -d "$event" $topicEndpoint
```

该事件会发送到终结点。

若要测试未发送事件的情形，请发送将颜色字段设置为“黄色”的事件。

```azurecli-interactive
event='[ {"id": "'"$RANDOM"'", "eventType": "recordInserted", "subject": "myapp/vehicles/cars", "eventTime": "'`date +%Y-%m-%dT%H:%M:%S%z`'", "data":{ "model": "SUV", "color": "yellow"},"dataVersion": "1.0"} ]'

curl -X POST -H "aeg-sas-key: $key" -d "$event" $topicEndpoint
```

“黄色”不是订阅中所指定的其中一个值，因此不会将事件发送到订阅。

## <a name="next-steps"></a>后续步骤

* 有关监视事件传送的信息，请参阅[监视事件网格消息传送](monitor-event-delivery.md)。
* 有关身份验证密钥的详细信息，请参阅[事件网格安全性和身份验证](security-authentication.md)。
* 有关创建 Azure 事件网格订阅的详细信息，请参阅[事件网格订阅架构](subscription-creation-schema.md)。
