---
title: "使用模板创建 Azure 事件中心命名空间和使用者组 | Microsoft Docs"
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
ms.date: 10/09/2017
ms.author: sethm;shvija
ms.openlocfilehash: 4cc9a0b9eaabb15a5a316e094deb178ef2219692
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-event-hubs-namespace-with-event-hub-and-consumer-group-using-an-azure-resource-manager-template"></a>使用 Azure Resource Manager 模板创建包含事件中心和使用者组的事件中心命名空间

本文介绍如何使用 Azure Resource Manager 模板创建包含一个事件中心和一个使用者组的事件中心类型的命名空间。 本文介绍如何定义要部署的资源以及如何定义执行部署时指定的参数。 可将此模板用于自己的部署，或自定义此模板以满足要求

有关创建模板的详细信息，请参阅[创作 Azure Resource Manager 模板][Authoring Azure Resource Manager templates]。

有关完整的模板，请参阅 GitHub 上的[事件中心和使用者组模板][Event Hub and consumer group template]。

> [!NOTE]
> 若要查看最新模板，请访问 [Azure 快速入门模板][Azure Quickstart Templates]库并搜索事件中心。
> 
> 

## <a name="what-will-you-deploy"></a>将部署什么内容？
使用此模板，将部署包含事件中心和使用者组的事件中心命名空间。

[事件中心](event-hubs-what-is-event-hubs.md)是一种事件处理服务，用于向 Azure 提供大规模的事件与遥测数据入口，并且具有较低的延迟和较高的可靠性。

若要自动运行部署，请单击以下按钮：

[![部署到 Azure](./media/event-hubs-resource-manager-namespace-event-hub/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-event-hubs-create-event-hub-and-consumer-group%2Fazuredeploy.json)

## <a name="parameters"></a>parameters
使用 Azure Resource Manager，可以定义在部署模板时想要指定的值的参数。 模板包含名为 `Parameters` 的部分，其中包含所有参数值。 应为随着要部署的项目或要部署到的环境而变化的值定义参数。 不要为始终保持不变的值定义参数。 模板中的每个参数值定义所部署的资源。

模板定义以下参数：

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
创建包含事件中心和使用者组的类型为 **EventHubs** 的命名空间。

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
```azurecli
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri [https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-event-hubs-create-event-hub-and-consumer-group/azuredeploy.json][]
```

## <a name="next-steps"></a>后续步骤
访问以下链接可以了解有关事件中心的详细信息：

* [事件中心概述](event-hubs-what-is-event-hubs.md)
* [创建事件中心](event-hubs-create.md)
* [事件中心常见问题](event-hubs-faq.md)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/resource-group-authoring-templates.md
[Azure Quickstart Templates]:  https://azure.microsoft.com/documentation/templates/?term=event+hubs
[Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
[Event hub and consumer group template]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-event-hubs-create-event-hub-and-consumer-group/
