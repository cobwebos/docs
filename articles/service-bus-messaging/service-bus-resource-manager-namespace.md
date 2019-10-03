---
title: 使用 Azure 资源管理器模板创建服务总线消息命名空间 | Microsoft Docs
description: 使用 Azure 资源管理器模板创建服务总线消息命名空间
services: service-bus-messaging
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: dc0d6482-6344-4cef-8644-d4573639f5e4
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 06/21/2019
ms.author: spelluru
ms.openlocfilehash: 4162775153a48dc8ea28e06f7c99f9927b9c602a
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444768"
---
# <a name="create-a-service-bus-namespace-by-using-an-azure-resource-manager-template"></a>使用 Azure 资源管理器模板创建服务总线命名空间

了解如何部署 Azure 资源管理器模板创建服务总线命名空间。 可将此模板用于自己的部署，或自定义此模板以满足要求。 有关创建模板的详细信息，请参阅[Azure 资源管理器文档](/azure/azure-resource-manager/)。

以下模板仍可用于创建服务总线命名空间：

* [创建包含队列的服务总线命名空间](./service-bus-resource-manager-namespace-queue.md)
* [创建包含主题和订阅的服务总线命名空间](./service-bus-resource-manager-namespace-topic.md)
* [创建包含队列和授权规则的服务总线命名空间](./service-bus-resource-manager-namespace-auth-rule.md)
* [创建包含主题、订阅和规则的服务总线命名空间](./service-bus-resource-manager-namespace-topic-with-rule.md)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="create-a-service-bus-namespace"></a>创建服务总线命名空间

在本快速入门中，使用[现有的资源管理器模板](https://github.com/Azure/azure-quickstart-templates/blob/master/101-servicebus-create-namespace/azuredeploy.json)从[Azure 快速入门模板](https://azure.microsoft.com/resources/templates/):

[!code-json[create-azure-service-bus-namespace](~/quickstart-templates/101-servicebus-create-namespace/azuredeploy.json)]

若要查找更多模板示例，请参阅[Azure 快速入门模板](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Servicebus&pageNumber=1&sort=Popular)。

若要通过部署一个模板创建服务总线命名空间：

1. 选择**试试**从以下代码块，，然后按照说明登录到 Azure Cloud shell。

    ```azurepowershell-interactive
    $serviceBusNamespaceName = Read-Host -Prompt "Enter a name for the service bus namespace to be created"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $resourceGroupName = "${serviceBusNamespaceName}rg"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-servicebus-create-namespace/azuredeploy.json"

    New-AzResourceGroup -Name $resourceGroupName -Location $location
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -serviceBusNamespaceName $serviceBusNamespaceName

    Write-Host "Press [ENTER] to continue ..."
    ```

    资源组名称是 service bus 命名空间名称加**rg**追加。

2. 选择“复制”以复制 PowerShell 脚本。 
3. 命令行程序控制台中，右键单击，然后选择**粘贴**。

它需要一些时间来创建事件中心。

## <a name="verify-the-deployment"></a>验证部署

若要查看已部署的服务总线命名空间，您可以从 Azure 门户中，打开资源组或使用以下 Azure PowerShell 脚本。 如果仍打开 Cloud shell 中，您不需要复制/运行以下脚本的第一个和第二个行。

```azurepowershell-interactive
$serviceBusNamespaceName = Read-Host -Prompt "Enter the same service bus namespace name used earlier"
$resourceGroupName = "${serviceBusNamespaceName}rg"

Get-AzServiceBusNamespace -ResourceGroupName $resourceGroupName -Name $serviceBusNamespaceName

Write-Host "Press [ENTER] to continue ..."
```

Azure PowerShell 用于本教程中部署该模板。 有关其他模板的部署方法，请参阅：

* [使用 Azure 门户](../azure-resource-manager/resource-group-template-deploy-portal.md)。
* [使用 Azure CLI](../azure-resource-manager/resource-group-template-deploy-cli.md)。
* [使用 REST API](../azure-resource-manager/resource-group-template-deploy-rest.md)。

## <a name="clean-up-resources"></a>清理资源

不再需要 Azure 资源时，请通过删除资源组来清理部署的资源。 如果仍打开 Cloud shell 中，您不需要复制/运行以下脚本的第一个和第二个行。

```azurepowershell-interactive
$serviceBusNamespaceName = Read-Host -Prompt "Enter the same service bus namespace name used earlier"
$resourceGroupName = "${serviceBusNamespaceName}rg"

Remove-AzResourceGroup -ResourceGroupName $resourceGroupName

Write-Host "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>后续步骤

在本文中，我们已创建一个服务总线命名空间。 请参阅其他快速入门，了解如何创建和使用队列、主题/订阅：

* [服务总线队列入门](service-bus-dotnet-get-started-with-queues.md)
* [服务总线主题入门](service-bus-dotnet-how-to-use-topics-subscriptions.md)
