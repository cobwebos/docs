<properties
    pageTitle="创建包含队列的服务总线命名空间 | Azure"
    description="使用 ARM 模板创建服务总线命名空间和队列"
    services="service-bus"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.date="04/15/2016"
    wacn.date=""/>

# 使用 ARM 模板创建服务总线命名空间和队列

本文介绍如何使用创建服务总线命名空间和队列的 Azure Resource Manager (ARM) 模板。你将了解如何定义要部署的资源以及如何定义执行部署时指定的参数。可将此模板用于自己的部署，或自定义此模板以满足要求

有关创建模板的详细信息，请参阅[创作 Azure Resource Manager 模板][]。

有关完整的模板，请参阅 GitHub 上的[服务总线命名空间和队列模板][]。

>[AZURE.NOTE] 下列 ARM 模板将可供下载和部署。
>
>-    [创建包含队列和授权规则的服务总线命名空间](/documentation/articles/service-bus-arm-namespace-auth-rule)
>-    [创建包含事件中心和使用者组的服务总线命名空间](/documentation/articles/service-bus-arm-namespace-event-hub)
>-    [创建包含主题和订阅的服务总线命名空间](/documentation/articles/service-bus-arm-namespace-topic)
>-    [创建服务总线命名空间](/documentation/articles/service-bus-arm-namespace)
>
>若要检查最新模板，请参阅 [Azure 快速入门模板][]并搜索服务总线。

## 你将部署什么内容？

使用此模板，你将部署包含队列的服务总线命名空间。

队列为一个或多个竞争使用方提供“先入先出 (FIFO)”消息传递方式。

[了解有关服务总线队列的详细信息](/documentation/articles/service-bus-queues-topics-subscriptions)。

若要自动运行部署，请单击以下按钮：

[![部署到 Azure](./media/service-bus-arm-namespace-queue/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-queue%2Fazuredeploy.json)

## Parameters

使用 Azure 资源管理器，可以定义在部署模板时想要指定的值的参数。该模板具有一个名为 `Parameters` 的部分，其中包含所有参数值。你应该为随着要部署的项目或要部署到的环境而变化的值定义参数。不要为永远保持不变的值定义参数。每个参数值可在模板中用来定义所部署的资源。

下面介绍模板中的每个参数。

### serviceBusNamespaceName

要创建的服务总线命名空间的名称。

```
"serviceBusNamespaceName": {
"type": "string"
}
```

### serviceBusQueueName

在服务总线命名空间中创建的队列的名称。

```
"serviceBusQueueName": {
"type": "string"
}
```

### serviceBusApiVersion

模板的服务总线 API 版本。

```
"serviceBusApiVersion": {
"type": "string"
}
```

## 要部署的资源

创建包含队列的标准服务总线命名空间。

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

## 运行部署的命令

[AZURE.INCLUDE [app-service-deploy-commands](../includes/app-service-deploy-commands.md)]

## PowerShell

```
New-AzureRmResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateFile <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-queue/azuredeploy.json>
```

## Azure CLI

```
azure config mode arm

azure group deployment create <my-resource-group> <my-deployment-name> --template-uri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-queue/azuredeploy.json>
```

## 后续步骤

现在，你已使用 ARM 创建并部署了资源，请通过查看以下文章了解如何管理这些资源：

- [使用 Azure 自动化管理 Azure Service Bus](/documentation/articles/service-bus-automation-manage)
- [使用 PowerShell 管理服务总线](/documentation/articles/service-bus-powershell-how-to-provision)
- [使用服务总线资源管理器管理服务总线资源](https://code.msdn.microsoft.com/Service-Bus-Explorer-f2abca5a)


  [创作 Azure Resource Manager 模板]: /documentation/articles/resource-group-authoring-templates
  [服务总线命名空间和队列模板]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/
  [Azure 快速入门模板]: https://azure.microsoft.com/documentation/templates/
  [Learn more about Service Bus queues]: /documentation/articles/service-bus-queues-topics-subscriptions
  [Using Azure PowerShell with Azure Resource Manager]: /documentation/articles/powershell-azure-resource-manager
  [Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: /documentation/articles/xplat-cli-azure-resource-manager

<!---HONumber=Mooncake_0516_2016-->