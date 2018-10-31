---
title: 使用模板创建 Azure 事件中心命名空间和使用者组 | Microsoft Docs
description: 使用 Azure 资源管理器模板创建包含事件中心和使用者组的事件中心命名空间
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: 28bb4591-1fd7-444f-a327-4e67e8878798
ms.service: event-hubs
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 10/16/2018
ms.author: shvija
ms.openlocfilehash: 48d41ef4df986f959dfabe04e07552e287fdfcd0
ms.sourcegitcommit: 668b486f3d07562b614de91451e50296be3c2e1f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/19/2018
ms.locfileid: "49457027"
---
# <a name="create-an-event-hubs-namespace-with-event-hub-and-consumer-group-using-an-azure-resource-manager-template"></a>使用 Azure 资源管理器模板创建包含事件中心和使用者组的事件中心命名空间
Azure 事件中心是一个大数据流式处理平台和事件引入服务，每秒能够接收和处理数百万个事件。 事件中心可以处理和存储分布式软件和设备生成的事件、数据或遥测。 可以使用任何实时分析提供程序或批处理/存储适配器转换和存储发送到数据中心的数据。 有关事件中心的详细概述，请参阅[事件中心概述](event-hubs-about.md)和[事件中心功能](event-hubs-features.md)。

在此快速入门中，使用 Azure 资源管理器模板创建事件中心。 使用 Azure 资源管理器模板创建包含一个事件中心和一个使用者组的[事件中心](event-hubs-what-is-event-hubs.md)类型命名空间。 本文介绍如何定义要部署的资源以及如何定义执行部署时指定的参数。 可将此模板用于自己的部署，或自定义此模板以满足要求。 有关创建模板的信息，请参阅[创作 Azure 资源管理器模板][Authoring Azure Resource Manager templates]。

有关完整的模板，请参阅 GitHub 上的[事件中心和使用者组模板][Event Hub and consumer group template]。

> [!NOTE]
> 若要查看最新模板，请访问 [Azure 快速入门模板][Azure Quickstart Templates]库并搜索事件中心。

## <a name="prerequisites"></a>先决条件
若要完成本快速入门，需要一个 Azure 订阅。 如果没有订阅，请在开始之前创建一个免费帐户。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果在本地使用 Azure PowerShell 部署资源管理器模板，必须运行最新版本的 PowerShell 才能完成本快速入门。 如需进行安装或升级，请参阅[安装和配置 Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-5.7.0)。

如果选择在本地安装并使用 Azure CLI 部署资源管理器模板，本教程要求运行 Azure CLI 2.0.4 或更高版本。 请运行 `az --version` 检查版本。 如需进行安装或升级，请参阅[安装 Azure CLI]( /cli/azure/install-azure-cli)。

## <a name="what-will-you-deploy"></a>将部署什么内容？

使用此模板，将部署包含事件中心和使用者组的事件中心命名空间。

若要自动运行部署，请选择以下按钮：

[![部署到 Azure](./media/event-hubs-resource-manager-namespace-event-hub/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-event-hubs-create-event-hub-and-consumer-group%2Fazuredeploy.json)

## <a name="define-parameters"></a>定义参数
使用 Azure 资源管理器，可以定义在部署模板时想要指定的值的参数。 模板包含名为 `Parameters` 的部分，其中包含所有参数值。 应为随着要部署的项目或要部署到的环境而变化的值定义参数。 不要为始终保持不变的值定义参数。 模板中的每个参数值定义所部署的资源。

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

## <a name="define-resources-to-deploy"></a>定义要部署的资源

创建包含事件中心和使用者组的类型为 **EventHubs** 的命名空间：

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

## <a name="azure-powershell"></a>Azure PowerShell
若要使用 Azure PowerShell 部署这些资源，请运行以下命令：

```azurepowershell
New-AzureRmResourceGroupDeployment -ResourceGroupName \<resource-group-name\> -TemplateFile https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-event-hubs-create-event-hub-and-consumer-group/azuredeploy.json
```

## <a name="azure-cli"></a>Azure CLI
若要使用 Azure PowerShell 部署这些资源，请运行以下命令：

```azurecli
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri [https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-event-hubs-create-event-hub-and-consumer-group/azuredeploy.json][]
```

祝贺你！ 现已使用 Azure 资源管理器模板创建了一个事件中心命名空间，并在该命名空间中创建了一个事件中心。

## <a name="next-steps"></a>后续步骤

在本文中，我们已创建事件中心命名空间，并使用示例应用程序从事件中心发送和接收事件。 有关将事件发送到事件中心（或）从事件中心接收事件的分步说明，请参阅以下教程： 

- 将事件发送到事件中心：[.NET Standard](event-hubs-dotnet-standard-getstarted-send.md)、[.NET Framework](event-hubs-dotnet-framework-getstarted-send.md)、[Java](event-hubs-java-get-started-send.md)、[Python](event-hubs-python-get-started-send.md)、[Node.js](event-hubs-node-get-started-send.md)、[Go](event-hubs-go-get-started-send.md)、[C](event-hubs-c-getstarted-send.md)
- 从事件中心接收事件：[.NET Standard](event-hubs-dotnet-standard-getstarted-receive-eph.md)、[.NET Framework](event-hubs-dotnet-framework-getstarted-receive-eph.md)、[Java](event-hubs-java-get-started-receive-eph.md)、[Python](event-hubs-python-get-started-receive.md)、[Node.js](event-hubs-node-get-started-receive.md)、[Go](event-hubs-go-get-started-receive-eph.md)、[Apache Storm](event-hubs-storm-getstarted-receive.md)

[3]: ./media/event-hubs-quickstart-powershell/sender1.png
[4]: ./media/event-hubs-quickstart-powershell/receiver1.png
[5]: ./media/event-hubs-quickstart-powershell/metrics.png

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/resource-group-authoring-templates.md
[Azure Quickstart Templates]:  https://azure.microsoft.com/documentation/templates/?term=event+hubs
[Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
[Event hub and consumer group template]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-event-hubs-create-event-hub-and-consumer-group/
