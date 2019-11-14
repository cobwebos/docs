---
title: Azure ExpressRoute 模板：创建 ExpressRoute 线路
description: 创建、预配、删除和取消预配 ExpressRoute 线路。
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 07/05/2019
ms.author: cherylmc
ms.reviewer: ganesr
ms.openlocfilehash: d6d9a3b472e2a40bfacf25a0b8fe0accb7d62cb8
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2019
ms.locfileid: "74031976"
---
# <a name="create-an-expressroute-circuit-by-using-azure-resource-manager-template"></a>使用 Azure 资源管理器模板创建 ExpressRoute 线路

> [!div class="op_single_selector"]
> * [Azure 门户](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [Azure CLI](howto-circuit-cli.md)
> * [Azure 资源管理器模板](expressroute-howto-circuit-resource-manager-template.md)
> * [视频 - Azure 门户](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell（经典）](expressroute-howto-circuit-classic.md)
>

了解如何使用 Azure PowerShell 部署 Azure 资源管理器模板，以便创建 ExpressRoute 线路。 有关开发资源管理器模板的详细信息，请参阅[资源管理器文档](/azure/azure-resource-manager/)和[模板参考](/azure/templates/microsoft.network/expressroutecircuits)。

## <a name="before-you-begin"></a>开始之前

* 在开始配置之前，请查看[先决条件](expressroute-prerequisites.md)和[工作流](expressroute-workflows.md)。
* 确保有权创建新的网络资源。 如果没有适当的权限，请与帐户管理员联系。
* 可以在开始前[观看一段视频](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)，以更好地了解步骤。

## <a name="create"></a>创建和预配 ExpressRoute 线路

[Azure 快速入门模板](https://azure.microsoft.com/resources/templates/)有许多资源管理器模板。 请使用某个[现有模板](https://azure.microsoft.com/resources/templates/101-expressroute-circuit-create/)来创建 ExpressRoute 线路。

[!code-json[create-azure-expressroute-circuit](~/quickstart-templates/101-expressroute-circuit-create/azuredeploy.json)]

若要查看更多相关的模板，请在[此处](https://azure.microsoft.com/resources/templates/?term=expressroute)进行选择。

若要通过部署模板来创建 ExpressRoute 线路，请执行以下操作：

1. 从以下代码块中选择“试用”，然后按照说明登录 Azure Cloud shell。

    ```azurepowershell-interactive
    $circuitName = Read-Host -Prompt "Enter a circuit name"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $resourceGroupName = "${circuitName}rg"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-expressroute-circuit-create/azuredeploy.json"

    $serviceProviderName = "Equinix"
    $peeringLocation = "Silicon Valley"
    $bandwidthInMbps = 500
    $sku_tier = "Premium"
    $sku_family = "MeteredData"

    New-AzResourceGroup -Name $resourceGroupName -Location $location
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -circuitName $circuitName -serviceProviderName $serviceProviderName -peeringLocation $peeringLocation -bandwidthInMbps $bandwidthInMbps -sku_tier $sku_tier -sku_family $sku_family

    Write-Host "Press [ENTER] to continue ..."
    ```

   * “层”决定是启用 ExpressRoute 标准版外接程序还是 ExpressRoute 高级版外接程序。 可以指定“Standard”以获取标准 SKU，或指定“Premium”以获取高级版外接程序。

   * “对等互连位置”是与 Microsoft 建立对等互连的实际位置。

     > [!IMPORTANT]
     > “对等互连位置”指明了与 Microsoft 建立对等互连的[实际位置](expressroute-locations.md)。 此位置与“Location”属性**没有**关系，后者指的是 Azure 网络资源提供商所在的地理位置。 尽管两者之间没有关系，但最好是选择地理上与线路对等互连位置靠近的网络资源提供商。

    资源组名称是追加了 **rg** 的服务总线命名空间名称。

2. 选择“复制”以复制 PowerShell 脚本。
3. 右键单击 shell 控制台，然后选择“粘贴”。

创建事件中心需要一些时间。

在本教程中，请使用 Azure PowerShell 来部署模板。 如需其他模板部署方法，请参阅：

* [使用 Azure 门户](../azure-resource-manager/resource-group-template-deploy-portal.md)。
* [使用 Azure CLI](../azure-resource-manager/resource-group-template-deploy-cli.md)。
* [使用 REST API](../azure-resource-manager/resource-group-template-deploy-rest.md)。

## <a name="delete"></a>取消预配和删除 ExpressRoute 线路

可以通过选择“删除”图标来删除 ExpressRoute 线路。 请注意以下信息：

* 必须取消所有虚拟网络与 ExpressRoute 线路的链接。 如果此操作失败，请检查是否有虚拟网络链接到了该线路。
* 如果 ExpressRoute 线路服务提供商预配状态为“正在预配”或“已预配”，则必须与服务提供商合作，在他们一端取消预配线路。 在服务提供商完成取消设置线路并通知我们之前，我们会继续保留资源并向你收费。
* 如果服务提供商已取消预配线路（服务提供商预配状态设置为“未预配”），则可以删除线路。 这样就会停止对线路的计费。

可以通过运行以下 PowerShell 命令删除 ExpressRoute 线路：

```azurepowershell-interactive
$circuitName = Read-Host -Prompt "Enter the same circuit name that you used earlier"
$resourceGroupName = "${circuitName}rg"

Remove-AzExpressRouteCircuit -ResourceGroupName $resourceGroupName -Name $circuitName
```

## <a name="next-steps"></a>后续步骤

创建线路后，请继续执行以下后续步骤：

* [创建和修改 ExpressRoute 线路的路由](expressroute-howto-routing-portal-resource-manager.md)
* [将虚拟网络链接到 ExpressRoute 线路](expressroute-howto-linkvnet-arm.md)
