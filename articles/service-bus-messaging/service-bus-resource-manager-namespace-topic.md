---
title: "使用 Azure Resource Manager 模板创建包含主题和订阅的服务总线命名空间 | Microsoft 文档"
description: "使用 Azure Resource Manager 模板创建包含主题和订阅的服务总线命名空间"
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: d3d55200-5c60-4b5f-822d-59974cafff0e
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 10/14/2016
ms.author: sethm;shvija
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: d4c3da81e4f809bba252738c783f66a62f116eaa


---
# <a name="create-a-service-bus-namespace-with-topic-and-subscription-using-an-azure-resource-manager-template"></a>使用 Azure Resource Manager 模板创建包含主题和订阅的服务总线命名空间
本文介绍如何使用创建包含主题和订阅的服务总线命名空间的 Azure Resource Manager 模板。 你将了解如何定义要部署的资源以及如何定义执行部署时指定的参数。 可将此模板用于自己的部署，或自定义此模板以满足要求

有关创建模板的详细信息，请参阅[创作 Azure Resource Manager 模板][创作 Azure Resource Manager 模板]。

有关完整模板，请参阅[包含主题和订阅的服务总线命名空间][包含主题和订阅的服务总线命名空间]模板。

> [!NOTE]
> 以下 Azure Resource Manager 模板可供下载和部署。
> 
> * [创建服务总线命名空间](service-bus-resource-manager-namespace.md)
> * [创建包含队列的服务总线命名空间](service-bus-resource-manager-namespace-queue.md)
> * [创建包含队列和授权规则的服务总线命名空间](service-bus-resource-manager-namespace-auth-rule.md)
> * [创建包含主题、订阅和规则的服务总线命名空间](service-bus-resource-manager-namespace-topic-with-rule.md)
> 
> 若要检查最新模板，请访问 [Azure 快速入门模板][Azure 快速入门模板]库并搜索“服务总线”。
> 
> 

## <a name="what-will-you-deploy"></a>你将部署什么内容？
使用此模板，你将部署包含主题和订阅的服务总线命名空间。

[服务总线主题和订阅](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions)以“发布/订阅”模式提供一对多的通信形式。

若要自动运行部署，请单击以下按钮：

[![部署到 Azure](./media/service-bus-resource-manager-namespace-topic/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-topic-and-subscription%2Fazuredeploy.json)

## <a name="parameters"></a>parameters
使用 Azure Resource Manager，可以定义在部署模板时想要指定的值的参数。 该模板具有一个名为 `Parameters` 的部分，其中包含所有参数值。 你应该为随着要部署的项目或要部署到的环境而变化的值定义参数。 不要为永远保持不变的值定义参数。 每个参数值可在模板中用来定义所部署的资源。

模板定义以下参数。

### <a name="servicebusnamespacename"></a>serviceBusNamespaceName
要创建的服务总线命名空间的名称。

```
"serviceBusNamespaceName": {
"type": "string"
}
```

### <a name="servicebustopicname"></a>serviceBusTopicName
在服务总线命名空间中创建的主题的名称。

```
"serviceBusTopicName": {
"type": "string"
}
```

### <a name="servicebussubscriptionname"></a>serviceBusSubscriptionName
在服务总线命名空间中创建的订阅的名称。

```
"serviceBusSubscriptionName": {
"type": "string"
}
```

### <a name="servicebusapiversion"></a>serviceBusApiVersion
模板的服务总线 API 版本。

```
"serviceBusApiVersion": {
"type": "string"
}
```
## <a name="resources-to-deploy"></a>要部署的资源
创建类型为 **Messaging** 的包含主题和订阅的标准服务总线命名空间。

```
"resources ": [{
        "apiVersion": "[variables('sbVersion')]",
        "name": "[parameters('serviceBusNamespaceName')]",
        "type": "Microsoft.ServiceBus/Namespaces",
        "location": "[variables('location')]",
        "kind": "Messaging",
        "sku": {
            "name": "StandardSku",
            "tier": "Standard"
        },
        "resources": [{
            "apiVersion": "[variables('sbVersion')]",
            "name": "[parameters('serviceBusTopicName')]",
            "type": "Topics",
            "dependsOn": [
                "[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"
            ],
            "properties": {
                "path": "[parameters('serviceBusTopicName')]",
            },
            "resources": [{
                "apiVersion": "[variables('sbVersion')]",
                "name": "[parameters('serviceBusSubscriptionName')]",
                "type": "Subscriptions",
                "dependsOn": [
                    "[parameters('serviceBusTopicName')]"
                ],
                "properties": {}
            }]
        }]
    }]
```

## <a name="commands-to-run-deployment"></a>运行部署的命令
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell
```
New-AzureResourceGroupDeployment -Name \<deployment-name\> -ResourceGroupName \<resource-group-name\> -TemplateUri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-topic-and-subscription/azuredeploy.json>
```

## <a name="azure-cli"></a>Azure CLI
```
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-topic-and-subscription/azuredeploy.json>
```

## <a name="next-steps"></a>后续步骤
现在，你已使用 Azure Resource Manager 创建并部署了资源，请通过查看以下文章了解如何管理这些资源：

* [使用 PowerShell 管理服务总线](service-bus-powershell-how-to-provision.md)
* [使用服务总线 Explorer 管理服务总线资源](https://code.msdn.microsoft.com/Service-Bus-Explorer-f2abca5a)

[创作 Azure Resource Manager 模板]: ../resource-group-authoring-templates.md
[Azure 快速入门模板]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[了解有关服务总线主题和订阅的详细信息]: service-bus-queues-topics-subscriptions.md
[将 Azure PowerShell 与 Azure Resource Manager 结合使用]: ../powershell-azure-resource-manager.md
[将适用于 Mac、Linux 和 Windows 的 Azure CLI 与 Azure 资源管理结合使用]: ../xplat-cli-azure-resource-manager.md
[包含主题和订阅的服务总线命名空间]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-topic-and-subscription/



<!--HONumber=Nov16_HO3-->


