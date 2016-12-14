---
title: "使用 Resource Manager 模板创建服务总线命名空间 | Microsoft 文档"
description: "使用 Azure Resource Manager 模板创建服务总线命名空间"
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: dc0d6482-6344-4cef-8644-d4573639f5e4
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 10/04/2016
ms.author: sethm;shvija
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 39907193d006d6060152c9ac737a082d655c2256


---
# <a name="create-a-service-bus-namespace-using-an-azure-resource-manager-template"></a>使用 Azure Resource Manager 模板创建服务总线命名空间
本文介绍如何使用 Azure Resource Manager 模板，该模板用于创建包含标准/基本 SKU 的类型为 **Messaging** 的服务总线命名空间。 本文还定义了为执行部署指定的参数。 可将此模板用于自己的部署，或自定义此模板以满足要求。

有关创建模板的详细信息，请参阅[创作 Azure Resource Manager 模板][创作 Azure Resource Manager 模板]。

有关完整的模板，请参阅 GitHub 上的[服务总线命名空间模板][服务总线命名空间模板]。

> [!NOTE]
> 以下 Azure Resource Manager 模板可供下载和部署。 
> 
> * [创建包含队列的服务总线命名空间](service-bus-resource-manager-namespace-queue.md)
> * [创建包含主题和订阅的服务总线命名空间](service-bus-resource-manager-namespace-topic.md)
> * [创建包含队列和授权规则的服务总线命名空间](service-bus-resource-manager-namespace-auth-rule.md)
> * [创建包含主题、订阅和规则的服务总线命名空间](service-bus-resource-manager-namespace-topic-with-rule.md)
> 
> 若要检查最新模板，请访问 [Azure 快速入门模板][Azure 快速入门模板]库并搜索“服务总线”。
> 
> 

## <a name="what-will-you-deploy"></a>你将部署什么内容？
使用此模板，可以部署包含[基本、标准或高级](https://azure.microsoft.com/pricing/details/service-bus/) SKU 的服务总线命名空间。

若要自动运行部署，请单击以下按钮：

[![部署到 Azure](./media/service-bus-resource-manager-namespace/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-servicebus-create-namespace%2Fazuredeploy.json)

## <a name="parameters"></a>parameters
使用 Azure Resource Manager，可以定义在部署模板时想要指定的值的参数。 该模板具有一个名为 `Parameters` 的部分，其中包含所有参数值。 你应该为随着要部署的项目或要部署到的环境而变化的值定义参数。 不要为永远保持不变的值定义参数。 每个参数值可在模板中用来定义所部署的资源。

此模板定义以下参数。

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

### <a name="servicebussku"></a>serviceBusSKU
要创建的服务总线 [SKU](https://azure.microsoft.com/pricing/details/service-bus/) 的名称。

```
"serviceBusSku": { 
    "type": "string", 
    "allowedValues": [ 
        "Basic", 
        "Standard",
        "Premium" 
    ], 
    "defaultValue": "Standard", 
    "metadata": { 
        "description": "The messaging tier for service Bus namespace" 
    } 

```

模板定义此参数允许的值（Basic、Standard 或 Premium），如果未指定任何值，则分配默认值 (Standard)。

有关服务总线定价的完整信息，请参阅[Service Bus pricing and billing][Service Bus pricing and billing]。

### <a name="servicebusapiversion"></a>serviceBusApiVersion
模板的服务总线 API 版本。

```
"serviceBusApiVersion": { 
       "type": "string", 
       "defaultValue": "2015-08-01", 
       "metadata": { 
           "description": "Service Bus ApiVersion used by the template" 
       } 
```

## <a name="resources-to-deploy"></a>要部署的资源
### <a name="service-bus-namespace"></a>服务总线命名空间
创建类型为 **Messaging** 的标准服务总线命名空间。

```
"resources": [
    {
        "apiVersion": "[parameters('serviceBusApiVersion')]",
        "name": "[parameters('serviceBusNamespaceName')]",
        "type": "Microsoft.ServiceBus/Namespaces",
        "location": "[variables('location')]",
        "kind": "Messaging",
        "sku": {
            "name": "StandardSku",
            "tier": "Standard"
        },
        "properties": {
        }
    }
]
```

## <a name="commands-to-run-deployment"></a>运行部署的命令
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell
```
New-AzureRmResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateFile https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-servicebus-create-namespace/azuredeploy.json
```

### <a name="azure-cli"></a>Azure CLI
```
azure config mode arm

azure group deployment create <my-resource-group> <my-deployment-name> --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-servicebus-create-namespace/azuredeploy.json
```

## <a name="next-steps"></a>后续步骤
现在，你已使用 Azure Resource Manager 创建并部署了资源，请通过阅读以下文章了解如何管理这些资源：

* [使用 PowerShell 管理服务总线](service-bus-powershell-how-to-provision.md)
* [使用服务总线 Explorer 管理服务总线资源](https://code.msdn.microsoft.com/Service-Bus-Explorer-f2abca5a)

[创作 Azure Resource Manager 模板]: ../resource-group-authoring-templates.md
[服务总线命名空间模板]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-servicebus-create-namespace/
[Azure 快速入门模板]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Service Bus pricing and billing]: https://azure.microsoft.com/documentation/articles/service-bus-pricing-billing/（服务总线定价和计费）
[将 Azure PowerShell 与 Azure Resource Manager 结合使用]: ../powershell-azure-resource-manager.md
[将适用于 Mac、Linux 和 Windows 的 Azure CLI 与 Azure 资源管理结合使用]: ../xplat-cli-azure-resource-manager.md



<!--HONumber=Nov16_HO3-->


