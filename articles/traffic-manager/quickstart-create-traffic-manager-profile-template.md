---
title: 快速入门：使用 Azure 资源管理器模板（ARM 模板）创建流量管理器
description: 本快速入门文章介绍如何使用 Azure 资源管理器模板（ARM 模板）创建 Azure 流量管理器。
services: traffic-manager
author: duongau
ms.service: traffic-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: duau
ms.date: 09/01/2020
ms.openlocfilehash: dbdb6a255fdf0214103a0011f25b0a6d25014e69
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/02/2020
ms.locfileid: "89299144"
---
# <a name="quickstart-create-a-traffic-manager-profile-using-an-arm-template"></a>快速入门：使用 ARM 模板创建流量管理器配置文件

本快速入门介绍如何使用 Azure 资源管理器模板（ARM 模板）创建具有使用性能路由方法的外部终结点的流量管理器配置文件。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

如果你的环境满足先决条件，并且你熟悉如何使用 ARM 模板，请选择“部署到 Azure”按钮。 Azure 门户中会打开模板。

[![部署到 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-traffic-manager-external-endpoint%2Fazuredeploy.json)

## <a name="prerequisites"></a>先决条件

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="review-the-template"></a>查看模板

本快速入门中使用的模板来自 [Azure 快速启动模板](https://azure.microsoft.com/resources/templates/101-traffic-manager-external-endpoint)。

:::code language="json" source="~/quickstart-templates/101-traffic-manager-external-endpoint/azuredeploy.json":::

模板中定义了一个 Azure 资源：

* [Microsoft.Network/trafficManagerProfiles](/azure/templates/microsoft.network/trafficmanagerprofiles)

若要查找与 Azure 流量管理器相关的更多模板，请参阅 [Azure 快速入门模板](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular)。

## <a name="deploy-the-template"></a>部署模板

1. 从以下代码块中选择“试用”，以打开 Azure Cloud Shell，然后按照相关说明登录到 Azure。 

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-traffic-manager-external-endpoint/azuredeploy.json"

    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

    Read-Host -Prompt "Press [ENTER] to continue ..."
    ```

    等到控制台中显示提示。

1. 从上一个代码块中选择“复制”，以复制 PowerShell 脚本。

1. 右键单击 shell 控制台窗格，然后选择“粘贴”。

1. 输入相应的值。

    模板部署将创建包含两个外部终结点的配置文件。 “Endpoint1”使用位于“欧洲北部”的 w<span>ww.microsoft</span>.com 目标终结点。 “Endpoint2”使用位于“美国中南部”的 d<span>ocs.microsoft</span>.com 目标终结点。 

    资源组名称是追加了 **rg** 的项目名称。

    > [!NOTE]
    > “uniqueDNSname”需要是全局唯一名称，才能成功部署模板。 如果部署失败，请从步骤 1 重新开始。

    部署模板需要几分钟时间。 完成后，输出类似于：

    :::image type="content" source="./media/quickstart-create-traffic-manager-profile/traffic-manager-arm-powershell-output.png" alt-text="Azure 流量管理器资源管理器模板 PowerShell 部署输出":::

使用 Azure PowerShell 部署模板。 除了 Azure PowerShell，还可以使用 Azure 门户、Azure CLI 和 REST API。 若要了解其他部署方法，请参阅[部署模板](../azure-resource-manager/templates/deploy-portal.md)。

## <a name="validate-the-deployment"></a>验证部署

1. 使用 [Get-AzTrafficManagerProfile](/powershell/module/az.trafficmanager/get-aztrafficmanagerprofile) 确定流量管理器配置文件的 DNS 名称。

    ```azurepowershell-interactive
    Get-AzTrafficManagerProfile -Name ExternalEndpointExample -ResourceGroupName $resourceGroupName | Select RelativeDnsName
    ```

    复制 **RelativeDnsName** 值。 流量管理器配置文件的 DNS 名称为 *<* relativednsname *>.trafficmanager.net*。 

1. 在本地 PowerShell 中，通过将 {relativeDNSname} 变量替换为 <relativednsname>.trafficmanager.net 运行以下命令 。

    ```powershell
    Resolve-DnsName -Name {relativeDNSname} | Select-Object NameHost | Select -First 1
    ```
    你应该获得的 NameHost 为 w<span>ww.microsoft</span>.com 或 d<span>ocs.microsoft</span>.com，具体取决于哪个区域离你更近 。

1. 若要检查是否可以解析为其他终结点，请禁用在上一步中获得的目标的终结点。 将“{endpointName}”替换为“endpoint1”或“endpoint2”，以分别禁用 w<span>ww.microsoft</span>.com 或 d<span>ocs.microsoft</span>.com 的目标   。

    ```azurepowershell-interactive
    Disable-AzTrafficManagerEndpoint -Name {endpointName} -Type ExternalEndpoints -ProfileName ExternalEndpointExample -ResourceGroupName $resourceGroupName -Force
    ```
1. 在本地 PowerShell 中再次运行步骤 2 中的命令。 这一次你应该获得其他终结点的其他 NameHost。 

## <a name="clean-up-resources"></a>清理资源

如果不再需要流量管理器配置文件，请删除资源组。 这会删除流量管理器配置文件和所有相关资源。

若要删除资源组，请调用 `Remove-AzResourceGroup` cmdlet：

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>后续步骤

在本快速入门中，我们创建了：
* 流量管理器配置文件

若要详细了解如何路由流量，请继续学习流量管理器教程。

> [!div class="nextstepaction"]
> [流量管理器教程](tutorial-traffic-manager-improve-website-response.md)
