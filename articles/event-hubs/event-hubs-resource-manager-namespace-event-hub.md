---
title: "使用 Azure Resource Manager 模板创建包含事件中心和使用者组的事件中心命名空间 | Microsoft Docs"
description: "使用 Azure Resource Manager 模板创建包含事件中心和使用者组的事件中心命名空间"
services: event-hubs
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 28bb4591-1fd7-444f-a327-4e67e8878798
ms.service: event-hubs
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 11/21/2016
ms.author: sethm;shvija
translationtype: Human Translation
ms.sourcegitcommit: a9e31954983fa673258917785a5bd828f1970fa1
ms.openlocfilehash: 92ec109c6cf9e3a2792ed68dfd96f86f5f9ae339


---
# <a name="create-an-event-hubs-namespace-with-event-hub-and-consumer-group-using-an-azure-resource-manager-template"></a>使用 Azure Resource Manager 模板创建包含事件中心和使用者组的事件中心命名空间
本文介绍如何使用 Azure Resource Manager 模板创建包含事件中心和使用者组的事件中心命名空间。 你将了解如何定义要部署的资源以及如何定义执行部署时指定的参数。 可将此模板用于自己的部署，或自定义此模板以满足要求

有关创建模板的详细信息，请参阅[创作 Azure Resource Manager 模板][创作 Azure Resource Manager 模板]。

有关完整的模板，请参阅 GitHub 上的[事件中心和使用者组模板][事件中心和使用者组模板]。

> [!NOTE]
> 若要检查最新模板，请访问 [Azure 快速入门模板][Azure 快速入门模板]库并搜索“事件中心”。
> 
> 

## <a name="what-will-you-deploy"></a>你将部署什么内容？
使用此模板，你将部署包含事件中心和使用者组的事件中心命名空间。

[事件中心](event-hubs-what-is-event-hubs.md)是一种事件处理服务，用于向 Azure 提供大规模的事件与遥测数据入口，并且具有较低的延迟和较高的可靠性。

若要自动运行部署，请单击以下按钮：

[![部署到 Azure](./media/event-hubs-resource-manager-namespace-event-hub/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-event-hubs-create-event-hub-and-consumer-group%2Fazuredeploy.json)

## <a name="parameters"></a>parameters
使用 Azure 资源管理器，可以定义在部署模板时想要指定的值的参数。 该模板具有一个名为 `Parameters` 的部分，其中包含所有参数值。 你应该为随着要部署的项目或要部署到的环境而变化的值定义参数。 不要为永远保持不变的值定义参数。 模板中的每个参数值定义所部署的资源。

模板定义以下参数。

### <a name="eventhubnamespacename"></a>eventHubNamespaceName
要创建的事件中心命名空间的名称。

```json
"eventHubNamespaceName": {
"type": "string"
}
```

### <a name="eventhubname"></a>eventHubName
在事件中心命名空间中创建的事件中心的名称。

```json
"eventHubName": {
"type": "string"
}
```

### <a name="eventhubconsumergroupname"></a>eventHubConsumerGroupName
为事件中心创建的使用者组的名称。

```json
"eventHubConsumerGroupName": {
"type": "string"
}
```

### <a name="apiversion"></a>apiVersion
模板的 API 版本。

```json
"apiVersion": {
"type": "string"
}
```

## <a name="resources-to-deploy"></a>要部署的资源
创建包含事件中心和使用者组的类型 **EventHubs** 的命名空间。

```json
"resources":[  
      {  
         "apiVersion":"[variables('ehVersion')]",
         "name":"[parameters('namespaceName')]",
         "type":"Microsoft.EventHub/namespaces",
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
                  "[concat('Microsoft.EventHub/namespaces/', parameters('namespaceName'))]"
               ],
               "properties":{  
                  "path":"[parameters('eventHubName')]"
               },
               "resources":[  
                  {  
                     "apiVersion":"[variables('ehVersion')]",
                     "name":"[parameters('consumerGroupName')]",
                     "type":"ConsumerGroups",
                     "dependsOn":[  
                        "[parameters('eventHubName')]"
                     ],
                     "properties":{  

                     }
                  }
               ]
            }
         ]
      }
   ],
```

## <a name="commands-to-run-deployment"></a>运行部署的命令
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell
```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName \<resource-group-name\> -TemplateFile https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-event-hubs-create-event-hub-and-consumer-group/azuredeploy.json
```

## <a name="azure-cli"></a>Azure CLI
```
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri [https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-event-hubs-create-event-hub-and-consumer-group/azuredeploy.json][]
```

[创作 Azure Resource Manager 模板]: ../resource-group-authoring-templates.md
[Azure 快速入门模板]:  https://azure.microsoft.com/documentation/templates/?term=event+hubs
[将 Azure PowerShell 与 Azure Resource Manager 结合使用]: ../powershell-azure-resource-manager.md
[将适用于 Mac、Linux 和 Windows 的 Azure CLI 与 Azure 资源管理结合使用]: ../xplat-cli-azure-resource-manager.md
[事件中心和使用者组模板]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-event-hubs-create-event-hub-and-consumer-group/



<!--HONumber=Nov16_HO5-->


