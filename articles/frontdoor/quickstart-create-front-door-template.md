---
title: 快速入门：使用 Azure 资源管理器模板（ARM 模板）创建 Azure Front Door 服务
description: 本快速入门介绍如何使用 Azure 资源管理器模板（ARM 模板）来创建 Azure Front Door 服务。
services: front-door
documentationcenter: ''
author: duongau
editor: ''
ms.assetid: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/14/2020
ms.author: duau
ms.openlocfilehash: 41ad240b7232895a83839777f9942011edfaf5f4
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/15/2020
ms.locfileid: "90529377"
---
# <a name="quickstart-create-a-front-door-using-an-arm-template"></a>快速入门：使用 ARM 模板创建 Front Door

本快速入门介绍如何使用 Azure 资源管理器模板（ARM 模板）创建 Front Door，以便设置 Web 终结点的高可用性。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

如果你的环境满足先决条件，并且你熟悉如何使用 ARM 模板，请选择“部署到 Azure”按钮****。 Azure 门户中会打开模板。

[![部署到 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-front-door-create-basic%2Fazuredeploy.json)

## <a name="prerequisites"></a>先决条件

* 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 网站或 Web 应用程序的 IP 或 FQDN。

## <a name="review-the-template"></a>查看模板

本快速入门中使用的模板来自 [Azure 快速启动模板](https://azure.microsoft.com/resources/templates/101-front-door-create-basic)。

在本快速入门中，你将创建一个 Front Door 配置，其中包含一个后端和一个与“/*”匹配的默认路径。 

:::code language="json" source="~/quickstart-templates/101-front-door-create-basic/azuredeploy.json":::

模板中定义了一个 Azure 资源：

* [Microsoft.Network/frontDoors](/azure/templates/microsoft.network/frontDoors)

## <a name="deploy-the-template"></a>部署模板

1. 从以下代码块中选择“试用”，以打开 Azure Cloud Shell，然后按照相关说明登录到 Azure****。 

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-front-door-create-basic/azuredeploy.json"

    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

    Read-Host -Prompt "Press [ENTER] to continue ..."
    ```

    等到控制台中显示提示。

1. 从上一个代码块中选择“复制”****，以复制 PowerShell 脚本。

1. 右键单击 shell 控制台窗格，然后选择“粘贴”****。

1. 输入相应的值。

    模板部署将创建具有单个后端的 Front Door。 在此示例中，<span>microsoft.</span>com 用作 backendAddress。

    资源组名称是追加了 **rg** 的项目名称。

    > [!NOTE]
    > “frontDoorName”需要是全局唯一名称，才能成功部署模板。 如果部署失败，请从步骤 1 重新开始。

    部署模板需要几分钟时间。 完成后，输出类似于：

    :::image type="content" source="./media/quickstart-create-front-door-template/front-door-template-deployment-powershell-output.png" alt-text="Front Door 资源管理器模板 PowerShell 部署输出":::

使用 Azure PowerShell 部署模板。 除了 Azure PowerShell，还可以使用 Azure 门户、Azure CLI 和 REST API。 若要了解其他部署方法，请参阅[部署模板](../azure-resource-manager/templates/deploy-portal.md)。

## <a name="validate-the-deployment"></a>验证部署

1. 登录 [Azure 门户](https://portal.azure.com)。

1. 从左侧窗格中选择“资源组”****。

1. 选择你在上一部分中创建的资源组。 默认资源组名称是追加了 **rg** 的项目名称。

1. 选择之前创建的 Front Door，并单击“前端主机”链接。 该链接将打开一个 Web 浏览器，将你重定向到创建期间定义的后端 FQDN。

    :::image type="content" source="./media/quickstart-create-front-door-template/front-door-overview.png" alt-text="Front Door 资源管理器模板 PowerShell 部署输出":::

## <a name="clean-up-resources"></a>清理资源

如果不再需要 Front Door 服务，请删除资源组。 这会删除 Front Door 和所有相关资源。

若要删除资源组，请调用 `Remove-AzResourceGroup` cmdlet：

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>后续步骤

在本快速入门中，我们创建了：
* Front Door

若要了解如何将自定义域添加到 Front Door，请继续学习 Front Door 教程。

> [!div class="nextstepaction"]
> [Front Door 教程](front-door-custom-domain.md)
