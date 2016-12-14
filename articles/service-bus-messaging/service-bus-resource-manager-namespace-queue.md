---
title: "使用 Azure Resource Manager 模板创建服务总线命名空间和队列 | Microsoft 文档"
description: "使用 Azure Resource Manager 模板创建服务总线命名空间和队列"
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: a6bfb5fd-7b98-4588-8aa1-9d5f91b599b6
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 10/14/2016
ms.author: sethm;shvija
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 71e11a2279350236e0f65ac0b310034fb464d71a


---
# <a name="create-a-service-bus-namespace-and-a-queue-using-an-azure-resource-manager-template"></a>使用 Azure Resource Manager 模板创建服务总线命名空间和队列
本文介绍如何使用创建服务总线命名空间和队列的 Azure Resource Manager 模板。 你将了解如何定义要部署的资源以及如何定义执行部署时指定的参数。 可将此模板用于自己的部署，或自定义此模板以满足要求。

有关创建模板的详细信息，请参阅[创作 Azure Resource Manager 模板][创作 Azure Resource Manager 模板]。

有关完整的模板，请参阅 GitHub 上的[Service Bus namespace and queue template][Service Bus namespace and queue template]。

> [!NOTE]
> 以下 Azure Resource Manager 模板可供下载和部署。
> 
> * [创建包含队列和授权规则的服务总线命名空间](service-bus-resource-manager-namespace-auth-rule.md)
> * [创建包含主题和订阅的服务总线命名空间](service-bus-resource-manager-namespace-topic.md)
> * [创建服务总线命名空间](service-bus-resource-manager-namespace.md)
> * [创建包含主题、订阅和规则的服务总线命名空间](service-bus-resource-manager-namespace-topic-with-rule.md)
> 
> 若要检查最新模板，请访问 [Azure 快速入门模板][Azure 快速入门模板]库并搜索“服务总线”。
> 
> 

## <a name="what-will-you-deploy"></a>你将部署什么内容？
使用此模板，你将部署包含队列的服务总线命名空间。

[服务总线队列](service-bus-queues-topics-subscriptions.md#queues)为一个或多个竞争使用方提供先入先出 (FIFO) 消息传送方式。

若要自动运行部署，请单击以下按钮：

[![部署到 Azure](./media/service-bus-resource-manager-namespace-queue/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-queue%2Fazuredeploy.json)

## <a name="parameters"></a>parameters
使用 Azure Resource Manager，可以定义在部署模板时想要指定的值的参数。 该模板具有一个名为 `Parameters` 的部分，其中包含所有参数值。 你应该为随着要部署的项目或要部署到的环境而变化的值定义参数。 不要为永远保持不变的值定义参数。 每个参数值可在模板中用来定义所部署的资源。

模板定义以下参数。

### <a name="servicebusnamespacename"></a>serviceBusNamespaceName
要创建的服务总线命名空间的名称。

```
"serviceBusNamespaceName": {
"type": "string",
"metadata": { 
    "description": "Name of the Service Bus namespace" 
    }
}
```

### <a name="servicebusqueuename"></a>serviceBusQueueName
在服务总线命名空间中创建的队列的名称。

```
"serviceBusQueueName": {
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
创建“Messaging”类型的标准服务总线命名空间和队列。

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
            "name": "[parameters('serviceBusQueueName')]",
            "type": "Queues",
            "dependsOn": [
                "[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"
            ],
            "properties": {
                "path": "[parameters('serviceBusQueueName')]",
            }
        }]
    }]
```

## <a name="commands-to-run-deployment"></a>运行部署的命令
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell
```
New-AzureRmResourceGroupDeployment -ResourceGroupName \<resource-group-name\> -TemplateFile <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-queue/azuredeploy.json>
```

## <a name="azure-cli"></a>Azure CLI
```
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-queue/azuredeploy.json>
```

## <a name="next-steps"></a>后续步骤
现在，你已使用 Azure Resource Manager 创建并部署了资源，请通过查看以下文章了解如何管理这些资源：

* [使用 PowerShell 管理服务总线](service-bus-powershell-how-to-provision.md)
* [使用服务总线 Explorer 管理服务总线资源](https://code.msdn.microsoft.com/Service-Bus-Explorer-f2abca5a)

[创作 Azure Resource Manager 模板]: ../resource-group-authoring-templates.md
[Service Bus namespace and queue template]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/（服务总线命名空间和队列模板）
[Azure 快速入门模板]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[了解有关服务总线队列的详细信息]: service-bus-queues-topics-subscriptions.md
[将 Azure PowerShell 与 Azure Resource Manager 结合使用]: ../powershell-azure-resource-manager.md
[将适用于 Mac、Linux 和 Windows 的 Azure CLI 与 Azure 资源管理结合使用]: ../xplat-cli-azure-resource-manager.md



<!--HONumber=Nov16_HO3-->


