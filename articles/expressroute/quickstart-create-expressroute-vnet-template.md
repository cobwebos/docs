---
title: 使用 Azure 资源管理器模板（ARM 模板）创建 ExpressRoute 线路
description: 了解如何使用 Azure 资源管理器模板（ARM 模板）创建 ExpressRoute 线路。
services: expressroute
author: duongau
mnager: kumud
ms.service: expressroute
ms.topic: quickstart
ms.custom: subject-armsq
ms.date: 08/31/2020
ms.author: duau
ms.openlocfilehash: 2bc4c2a3ee7f1a1dbd85d46e49ec27e5276de159
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/01/2020
ms.locfileid: "89236895"
---
# <a name="quickstart-create-an-expressroute-circuit-with-private-peering-using-an-arm-template"></a>快速入门：使用 ARM 模板创建具有专用对等互连的 ExpressRoute 线路

本快速入门介绍如何使用 Azure 资源管理器模板（ARM 模板）来创建具有专用对等互连的 ExpressRoute 线路。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

如果你的环境满足先决条件，并且你熟悉如何使用 ARM 模板，请选择“部署到 Azure”按钮。 Azure 门户中会打开模板。

[![部署到 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-expressroute-private-peering-vnet%2Fazuredeploy.json)

## <a name="prerequisites"></a>先决条件

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="review-the-template"></a>查看模板

本快速入门中使用的模板来自 [Azure 快速启动模板](https://azure.microsoft.com/resources/templates/101-expressroute-private-peering-vnet)。

在本快速入门中，你将创建一个以 Equinix 为服务提供商的 ExpressRoute 线路。 该线路将使用高级 SKU，其带宽为 50 Mbps，对等互连位置为华盛顿特区  。 将使用主要子网 192.168.10.16/30 和辅助子网 192.168.10.20/30 来启用专用对等互连 。 还将创建虚拟网络以及 HighPerformance ExpressRoute 网关。

:::code language="json" source="~/quickstart-templates/101-expressroute-private-peering-vnet/azuredeploy.json" range="001-351" highlight="183-219":::

该模板中已定义了多个 Azure 资源：

* [**Microsoft.Network/expressRouteCircuits**](/azure/templates/microsoft.network/expressRouteCircuits)
* [**Microsoft.Network/expressRouteCircuits/peerings**](/azure/templates/microsoft.network/expressRouteCircuits/peerings)（用于线路上已启用的专用对等互连）
* [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networkSecurityGroups)（网络安全组应用于虚拟网络中的子网）
* [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualNetworks) 
* [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicIPAddresses)（公共 IP 由 ExpressRoute 网关使用）
* [**Microsoft.Network/virtualNetworkGateways**](/azure/templates/microsoft.network/virtualNetworkGateways)（ExpressRoute 网关用于将 VNet 链接到线路）

若要查找与 ExpressRoute 相关的更多模板，请参阅 [Azure 快速启动模板](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular)。

## <a name="deploy-the-template"></a>部署模板

1. 从以下代码块中选择“试用”，以打开 Azure Cloud Shell，然后按照相关说明登录到 Azure。 

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-expressroute-private-peering-vnet/azuredeploy.json"

    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

    Read-Host -Prompt "Press [ENTER] to continue ..."
    ```

    等到控制台中显示提示。

1. 从上一个代码块中选择“复制”，以复制 PowerShell 脚本。

1. 右键单击 shell 控制台窗格，然后选择“粘贴”。

1. 输入相应的值。

    资源组名称是追加了 **rg** 的项目名称。

部署模板大约需要 20 分钟。 完成后，输出类似于：

:::image type="content" source="./media/quickstart-create-expressroute-vnet/expressroute-powershell-output.png" alt-text="ExpressRoute 资源管理器模板 PowerShell 部署输出":::

使用 Azure PowerShell 部署模板。 除了 Azure PowerShell，还可以使用 Azure 门户、Azure CLI 和 REST API。 若要了解其他部署方法，请参阅[部署模板](../azure-resource-manager/templates/deploy-portal.md)。

## <a name="validate-the-deployment"></a>验证部署

1. 登录 [Azure 门户](https://portal.azure.com)。

1. 从左侧窗格中选择“资源组”。

1. 选择你在上一部分中创建的资源组。 默认资源组名称是追加了 **rg** 的项目名称。

1. 资源组应包含以下资源：

     :::image type="content" source="./media/quickstart-create-expressroute-vnet/expressroute-resource-group.png" alt-text="ExpressRoute 部署资源组":::

1. 选择 ExpressRoute 线路 er-ck01 来验证线路状态是否为“已启用”，提供程序状态是否为“未预配”，专用对等互连的状态是否为“已预配”   。

    :::image type="content" source="./media/quickstart-create-expressroute-vnet/expressroute-circuit.png" alt-text="ExpressRoute 部署线路":::

> [!NOTE]
> 你将需要调用提供程序来完成预配过程，然后才能将虚拟网络链接到线路。

## <a name="clean-up-resources"></a>清理资源

如果不再需要通过 ExpressRoute 线路创建的资源，请删除该资源组。 这会删除 ExpressRoute 线路和所有相关资源。

若要删除资源组，请调用 `Remove-AzResourceGroup` cmdlet：

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>后续步骤

在本快速入门中，我们创建了：
* ExpressRoute 线路
* 虚拟网络
* VPN 网关
* 公共 IP
* 网络安全组

若要详细了解如何将虚拟网络链接到线路，请继续学习 ExpressRoute 教程。

> [!div class="nextstepaction"]
> [ExpressRoute 教程](expressroute-howto-linkvnet-portal-resource-manager.md)

* 有关 ExpressRoute 工作流的详细信息，请参阅 [ExpressRoute 工作流](expressroute-workflows.md)。
* 有关线路对等互连的详细信息，请参阅 [ExpressRoute 线路和路由域](expressroute-circuit-peerings.md)。
* 有关使用虚拟网络的详细信息，请参阅 [虚拟网络概述](../virtual-network/virtual-networks-overview.md)。