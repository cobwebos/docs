---
title: "使用 Azure Resource Manager 模板创建服务总线资源 | Microsoft Docs"
description: "使用 Azure Resource Manager 模板自动创建服务总线资源"
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 24f6a207-0fa4-49cf-8a58-963f9e2fd655
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 10/14/2016
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 182b4378cfd876d3700a1f0028a681ce936be03c
ms.openlocfilehash: 1425628c6e0c544b5b84a4f828bfaef3073fddee


---
# <a name="create-service-bus-resources-using-azure-resource-manager-templates"></a>使用 Azure Resource Manager 模板创建服务总线资源
本文介绍如何使用 Azure Resource Manager 模板、PowerShell 和服务总线资源提供程序创建和部署服务总线资源。

Azure Resource Manager 模板可帮助你定义要为解决方案部署的资源，以及指定可用于为不同环境输入值的参数和变量。 模板中包含可用于为部署构造值的 JSON 和表达式。 有关编写 Azure Resource Manager 模板的详细信息，以及模板格式的讨论，请参阅[创作 Azure Resource Manager 模板](../resource-group-authoring-templates.md)。

> [!NOTE]
> 本文中的示例演示如何使用 Azure Resource Manager 来创建服务总线命名空间和消息实体（队列）。 有关其他模板示例，请访问 [Azure 快速启动模板库][Azure 快速启动模板库]并搜索“服务总线”。
> 
> 

## <a name="service-bus-and-event-hubs-resource-manager-templates"></a>服务总线和事件中心 Resource Manager 模板
这些服务总线 Azure Resource Manager 模板可供下载和部署。 单击以下链接可获得有关每个链接的详细信息，其中包含指向 GitHub 上的模板的链接：

* [创建服务总线命名空间](service-bus-resource-manager-namespace.md)
* [创建包含队列的服务总线命名空间](service-bus-resource-manager-namespace-queue.md)
* [创建包含主题和订阅的服务总线命名空间](service-bus-resource-manager-namespace-topic.md)
* [创建包含队列和授权规则的服务总线命名空间](service-bus-resource-manager-namespace-auth-rule.md)
* [创建包含主题、订阅和规则的服务总线命名空间](service-bus-resource-manager-namespace-topic-with-rule.md)

## <a name="deploy-with-powershell"></a>使用 PowerShell 进行部署
以下过程描述如何使用 PowerShell 部署 Azure Resource Manager 模板以创建“标准”层服务总线命名空间和该命名空间中的一个队列。 本示例基于[创建包含队列的服务总线命名空间](https://github.com/Azure/azure-quickstart-templates/tree/master/201-servicebus-create-queue)模板。 大概的工作流如下所示：

1. 安装 PowerShell。
2. 创建模板和（可选）参数文件。
3. 在 PowerShell 中，登录到你的 Azure 帐户。
4. 创建新资源组（如果不存在）。
5. 测试部署。
6. 如果需要，设置部署模式。
7. 部署模板。

有关部署 Azure Resource Manager 模板的完整信息，请参阅[使用 Azure Resource Manager 模板部署资源][使用 Azure Resource Manager 模板部署资源]。

### <a name="install-powershell"></a>安装 PowerShell
请按[如何安装和配置 Azure PowerShell](../powershell-install-configure.md) 中的说明安装 Azure PowerShell。

### <a name="create-a-template"></a>创建模板
从 GitHub 克隆或复制 [201-servicebus-create-queue](https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/azuredeploy.json) 模板：

```
{
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "serviceBusNamespaceName": {
            "type": "string",
            "metadata": {
                "description": "Name of the Service Bus namespace"
            }
        },
        "serviceBusQueueName": {
            "type": "string",
            "metadata": {
                "description": "Name of the Queue"
            }
        },
        "serviceBusApiVersion": {
            "type": "string",
            "defaultValue": "2015-08-01",
            "metadata": {
                "description": "Service Bus ApiVersion used by the template"
            }
        }
    },
    "variables": {
        "location": "[resourceGroup().location]",
        "sbVersion": "[parameters('serviceBusApiVersion')]",
        "defaultSASKeyName": "RootManageSharedAccessKey",
        "authRuleResourceId": "[resourceId('Microsoft.ServiceBus/namespaces/authorizationRules', parameters('serviceBusNamespaceName'), variables('defaultSASKeyName'))]"
    },
    "resources": [{
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
                "path": "[parameters('serviceBusQueueName')]"
            }
        }]
    }],
    "outputs": {
        "NamespaceConnectionString": {
            "type": "string",
            "value": "[listkeys(variables('authRuleResourceId'), variables('sbVersion')).primaryConnectionString]"
        },
        "SharedAccessPolicyPrimaryKey": {
            "type": "string",
            "value": "[listkeys(variables('authRuleResourceId'), variables('sbVersion')).primaryKey]"
        }
    }
}
```

### <a name="create-a-parameters-file-optional"></a>创建参数文件（可选）
若要使用可选参数文件，请复制 [201-servicebus-create-queue](https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/azuredeploy.parameters.json) 文件。 将 `serviceBusNamespaceName` 的值替换为要在此部署中创建的服务总线命名空间的名称，并将 `serviceBusQueueName` 的值替换为要创建的队列的名称。

```
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "serviceBusNamespaceName": {
            "value": "<myNamespaceName>"
        },
        "serviceBusQueueName": {
            "value": "<myQueueName>"
        },
        "serviceBusApiVersion": {
            "value": "2015-08-01"
        }
    }
}
```

有关详细信息，请参阅[参数文件](../resource-group-template-deploy.md#parameter-precedence)主题。

### <a name="log-in-to-azure-and-set-the-azure-subscription"></a>登录到 Azure 并设置 Azure 订阅
在 PowerShell 提示符下，运行以下命令：

```
Login-AzureRmAccount
```

系统将提示你登录到 Azure 帐户。 登录后，运行以下命令以查看可用订阅。

```
Get-AzureRMSubscription
```

此命令返回可用 Azure 订阅的列表。 通过运行以下命令为当前会话选择订阅。 将 `<YourSubscriptionId>` 替换为要使用的 Azure 订阅的 GUID。

```
Set-AzureRmContext -SubscriptionID <YourSubscriptionId>
```

### <a name="set-the-resource-group"></a>设置资源组
如果目前没有资源组，请使用“New-AzureRmResourceGroup”命令创建新的资源组。 提供资源组的名称，以及要使用的位置。 例如：

```
New-AzureRmResourceGroup -Name MyDemoRG -Location "West US"
```

如果成功，则会显示新的资源组的摘要。

```
ResourceGroupName : MyDemoRG
Location          : westus
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/<GUID>/resourceGroups/MyDemoRG
```

### <a name="test-the-deployment"></a>测试部署
通过运行 `Test-AzureRmResourceGroupDeployment` cmdlet 验证你的部署。 测试部署时，请提供与执行部署时所提供的完全相同的参数。

```
Test-AzureRmResourceGroupDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

### <a name="create-the-deployment"></a>创建部署
若要创建新部署，请运行 `New-AzureRmResourceGroupDeployment` 命令，并在出现提示时提供必需的参数。 参数包括部署的名称、资源组的名称，以及模板文件的路径或 URL。 如果未指定 **Mode** 参数，将使用 **Incremental** 的默认值。 有关详细信息，请参阅[增量部署和完整部署](../resource-group-template-deploy.md#incremental-and-complete-deployments)。

以下命令会提示你在 PowerShell 窗口中输入三个参数：

```
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

若要改为指定参数文件，请使用以下命令。

```
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
```

运行部署 cmdlet 时，还可以使用内联参数。 该命令如下所示：

```
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -parameterName "parameterValue"
```

若要运行[完整](../resource-group-template-deploy.md#incremental-and-complete-deployments)部署，请将 **Mode** 参数设置为 **Complete**：

```
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -Mode Complete -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

### <a name="verify-the-deployment"></a>验证部署
如果资源已成功部署，将在 PowerShell 窗口中显示部署的摘要：

```
DeploymentName    : MyDemoDeployment
ResourceGroupName : MyDemoRG
ProvisioningState : Succeeded
Timestamp         : 4/19/2016 10:38:30 PM
Mode              : Incremental
TemplateLink      :
Parameters        :
                    Name             Type                       Value
                    ===============  =========================  ==========
                    serviceBusNamespaceName  String             <namespaceName>
                    serviceBusQueueName  String                 <queueName>
                    serviceBusApiVersion  String                2015-08-01

```

## <a name="next-steps"></a>后续步骤
你现在已了解用于部署 Azure Resource Manager 模板的基本工作流和命令。 有关更多详细信息，请访问以下链接：

* [Azure Resource Manager 概述][Azure Resource Manager 概述]
* [使用 Azure Resource Manager 模板部署资源][使用 Azure Resource Manager 模板部署资源]
* [创作模板](../resource-group-authoring-templates.md)

[Azure Resource Manager 概述]: ../azure-resource-manager/resource-group-overview.md
[使用 Azure Resource Manager 模板部署资源]: ../resource-group-template-deploy.md
[Azure 快速启动模板库]: https://azure.microsoft.com/documentation/templates/?term=service+bus



<!--HONumber=Nov16_HO3-->


