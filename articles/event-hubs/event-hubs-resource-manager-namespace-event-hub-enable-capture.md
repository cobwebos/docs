---
title: "使用模板创建 Azure 事件中心命名空间并启用捕获 | Microsoft Docs"
description: "使用 Azure Resource Manager 模板创建包含一个事件中心的 Azure 事件中心命名空间并启用捕获"
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
editor: 
ms.assetid: 8bdda6a2-5ff1-45e3-b696-c553768f1090
ms.service: event-hubs
ms.devlang: tbd
ms.topic: get-started-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 06/28/2017
ms.author: shvija;sethm
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: f19a3d9b323d75ae23480d0699d55b79bb7d2e84
ms.contentlocale: zh-cn
ms.lasthandoff: 06/28/2017


---
# 使用 Azure Resource Manager 模板创建包含事件中心的事件中心命名空间并启用捕获
<a id="create-an-event-hubs-namespace-with-an-event-hub-and-enable-capture-using-an-azure-resource-manager-template" class="xliff"></a>
本文介绍如何使用 Azure Resource Manager 模板创建包含一个事件中心实例的事件中心命名空间，并在事件中心启用捕获功能。 本文介绍如何定义要部署的资源以及如何定义执行部署时指定的参数。 可将此模板用于自己的部署，或自定义此模板以满足要求

有关创建模板的详细信息，请参阅[创作 Azure Resource Manager 模板][Authoring Azure Resource Manager templates]。

有关 Azure 资源命名约定的实践和模式的详细信息，请参阅 [Azure 资源命名约定][Azure Resources Naming Conventions]。

有关完整的模板，请参阅 GitHub 上的[事件中心和启用捕获模板][Event Hub and enable Capture template]。

> [!NOTE]
> 若要查看最新模板，请访问 [Azure 快速入门模板][Azure Quickstart Templates]库并搜索事件中心。
> 
> 

## 你将部署什么内容？
<a id="what-will-you-deploy" class="xliff"></a>
借助此模板，可以部署包含事件中心的事件中心命名空间并启用[事件中心捕获](event-hubs-capture-overview.md)。

[事件中心](event-hubs-what-is-event-hubs.md)是一种事件处理服务，用于向 Azure 提供大规模的事件与遥测数据入口，并且具有较低的延迟和较高的可靠性。 通过事件中心捕获，可以在所选的指定时间或大小间隔内，将事件中心的流数据自动传送到所选的 Azure Blob 存储。

若要自动运行部署，请单击以下按钮：

[![部署到 Azure](./media/event-hubs-resource-manager-namespace-event-hub/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-eventhubs-create-namespace-and-enable-capture%2Fazuredeploy.json)

## parameters
<a id="parameters" class="xliff"></a>
使用 Azure Resource Manager，可以定义在部署模板时想要指定的值的参数。 模板包含名为 `Parameters` 的部分，其中包含所有参数值。 你应该为随着要部署的项目或要部署到的环境而变化的值定义参数。 不要为始终保持不变的值定义参数。 每个参数值可在模板中用来定义所部署的资源。

模板定义以下参数。

### eventHubNamespaceName
<a id="eventhubnamespacename" class="xliff"></a>
要创建的事件中心命名空间的名称。

```json
"eventHubNamespaceName":{  
     "type":"string",
     "metadata":{  
         "description":"Name of the EventHub namespace"
      }
}
```

### eventHubName
<a id="eventhubname" class="xliff"></a>
在事件中心命名空间中创建的事件中心的名称。

```json
"eventHubName":{  
    "type":"string",
    "metadata":{  
        "description":"Name of the event hub"
    }
}
```

### messageRetentionInDays
<a id="messageretentionindays" class="xliff"></a>
事件中心内消息的保留天数。 

```json
"messageRetentionInDays":{
    "type":"int",
    "defaultValue": 1,
    "minValue":"1",
    "maxValue":"7",
    "metadata":{
       "description":"How long to retain the data in event hub"
     }
 }
```

### partitionCount
<a id="partitioncount" class="xliff"></a>
事件中心内创建的分区数。

```json
"partitionCount":{
    "type":"int",
    "defaultValue":2,
    "minValue":2,
    "maxValue":32,
    "metadata":{
        "description":"Number of partitions chosen"
    }
 }
```

### captureEnabled
<a id="captureenabled" class="xliff"></a>
在事件中心启用捕获。

```json
"captureEnabled":{
    "type":"string",
    "defaultValue":"true",
    "allowedValues": [
    "false",
    "true"],
    "metadata":{
        "description":"Enable or disable the Capture for your event hub"
    }
 }
```
### captureEncodingFormat
<a id="captureencodingformat" class="xliff"></a>
指定用于序列化事件数据的编码格式。

```json
"captureEncodingFormat":{
    "type":"string",
    "defaultValue":"Avro",
    "allowedValues":[
    "Avro"],
    "metadata":{
        "description":"The encoding format in which Capture serializes the EventData"
    }
}
```

### captureTime
<a id="capturetime" class="xliff"></a>
事件中心捕获开始将数据捕获到 Azure Blob 存储中的时间间隔。

```json
"captureTime":{
    "type":"int",
    "defaultValue":300,
    "minValue":60,
    "maxValue":900,
    "metadata":{
         "description":"the time window in seconds for the capture"
    }
}
```

### captureSize
<a id="capturesize" class="xliff"></a>
捕获开始将数据捕获到 Azure Blob 存储中的大小间隔。

```json
"captureSize":{
    "type":"int",
    "defaultValue":314572800,
    "minValue":10485760,
    "maxValue":524288000,
    "metadata":{
        "description":"The size window in bytes for capture"
    }
}
```

### destinationStorageAccountResourceId
<a id="destinationstorageaccountresourceid" class="xliff"></a>
捕获需要 Azure 存储帐户资源 ID，以启用到所需存储帐户的捕获。

```json
 "destinationStorageAccountResourceId":{
    "type":"string",
    "metadata":{
        "description":"Your existing Storage account resource id where you want the blobs be captured"
    }
 }
```

### blobContainerName
<a id="blobcontainername" class="xliff"></a>
用于捕获事件数据的 Blob 容器。

```json
 "blobContainerName":{
    "type":"string",
    "metadata":{
        "description":"Your existing storage container in which you want the blobs captured"
    }
}
```


### apiVersion
<a id="apiversion" class="xliff"></a>
模板的 API 版本。

```json
 "apiVersion":{  
    "type":"string",
    "defaultValue":"2015-08-01",
    "metadata":{  
        "description":"ApiVersion used by the template"
    }
 }
```

## 要部署的资源
<a id="resources-to-deploy" class="xliff"></a>
创建包含一个事件中心的 **EventHubs** 类型的命名空间并启用捕获。

```json
"resources":[  
      {  
         "apiVersion":"[variables('ehVersion')]",
         "name":"[parameters('eventHubNamespaceName')]",
         "type":"Microsoft.EventHub/Namespaces",
         "location":"[variables('location')]",
         "sku":{  
            "name":"Standard",
            "tier":"Standard"
         },
         "resources":[  
            {  
               "apiVersion":"[variables('ehVersion')]",
               "name":"[parameters('eventHubName')]",
               "type":"EventHubs",
               "dependsOn":[  
                  "[concat('Microsoft.EventHub/namespaces/', parameters('eventHubNamespaceName'))]"
               ],
               "properties":{  
                  "path":"[parameters('eventHubName')]",
                  "MessageRetentionInDays":"[parameters('messageRetentionInDays')]",
                  "PartitionCount":"[parameters('partitionCount')]",
                  "CaptureDescription":{
                        "enabled":"[parameters('captureEnabled')]",
                        "encoding":"[parameters('captureEncodingFormat')]",
                        "intervalInSeconds":"[parameters('captureTime')]",
                        "sizeLimitInBytes":"[parameters('captureSize')]",
                        "destination":{
                            "name":"EventHubCapture.AzureBlockBlob",
                            "properties":{
                                "StorageAccountResourceId":"[parameters('destinationStorageAccountResourceId')]",
                                "BlobContainer":"[parameters('blobContainerName')]"
                            }
                        } 
                  }

               }

            }
         ]
      }
   ]
```

## 运行部署的命令
<a id="commands-to-run-deployment" class="xliff"></a>
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## PowerShell
<a id="powershell" class="xliff"></a>
```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName \<resource-group-name\> -TemplateFile https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-eventhubs-create-namespace-and-enable-capture/azuredeploy.json
```

## Azure CLI
<a id="azure-cli" class="xliff"></a>
```cli
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri [https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-eventhubs-create-namespace-and-enable-capture/azuredeploy.json][]
```
## 后续步骤
<a id="next-steps" class="xliff"></a>

也可通过 [Azure 门户](https://portal.azure.com)配置事件中心捕获。 有关详细信息，请参阅[通过 Azure 门户启用事件中心捕获](event-hubs-capture-enable-through-portal.md)。

访问以下链接可以了解有关事件中心的详细信息：

* [事件中心概述](event-hubs-what-is-event-hubs.md)
* [创建事件中心](event-hubs-create.md)
* [事件中心常见问题](event-hubs-faq.md)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/resource-group-authoring-templates.md
[Azure Quickstart Templates]:  https://azure.microsoft.com/documentation/templates/?term=event+hubs
[Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
[Azure Resources Naming Conventions]: https://azure.microsoft.com/documentation/articles/guidance-naming-conventions/
[Event hub and enable Capture template]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-capture

