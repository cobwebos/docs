---
title: 快速入门：使用资源管理器模板创建虚拟网络
titleSuffix: Azure Virtual Network
description: 了解如何使用资源管理器模板创建 Azure 虚拟网络。
services: virtual-network
author: KumudD
ms.service: virtual-network
ms.topic: quickstart
ms.date: 06/23/2020
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: c9e9ea2e59537a1c0c8e372a766fba3aa9a1b9a0
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "88122148"
---
# <a name="quickstart-create-a-virtual-network---resource-manager-template"></a>快速入门：创建虚拟网络 - 资源管理器模板

在本快速入门中，你将了解如何使用 Azure 资源管理器模板创建具有两个子网的虚拟网络。 虚拟网络是 Azure 中专用网络的基本构建块。 它能让 Azure 资源（例如 VM）互相安全通信以及与 Internet 通信。


[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

还可以使用 [Azure 门户](quick-create-portal.md)、[Azure PowerShell](quick-create-powershell.md) 或 [Azure CLI](quick-create-cli.md) 完成本快速入门。

## <a name="prerequisites"></a>先决条件

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="review-the-template"></a>查看模板

本快速入门中使用的模板来自 [Azure 快速入门模板](https://github.com/Azure/azure-quickstart-templates/blob/master/101-vnet-two-subnets/azuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-vnet-two-subnets/azuredeploy.json" range="001-96" highlight="56-92":::

该模板中已定义了以下 Azure 资源：
- [Microsoft.Network/virtualNetworks](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks)：创建 Azure 虚拟网络。
-  [Microsoft.Network/virtualNetworks/subnets](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks/subnets)：创建子网。

## <a name="deploy-the-template"></a>部署模板

将资源管理器模板部署到 Azure：

1. 选择“部署到 Azure”，登录到 Azure 并打开模板。 该模板创建包含两个子网的虚拟网络。

   [![部署到 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-virtual-network-2vms-create%2Fazuredeploy.json)

2. 在门户中的“创建具有两个子网的虚拟网络”页上，键入或选择以下值：
   - 资源组：选择“新建”，键入资源组的名称，然后选择“确定”。
   - **虚拟网络名称**：键入新虚拟网络的名称。
3. 选择“查看 + 创建”，然后选择“创建”。

## <a name="review-deployed-resources"></a>查看已部署的资源

浏览使用虚拟网络创建的资源。

若要了解模板中虚拟网络的 JSON 语法和属性，请参阅 [Microsoft.Network/virtualNetworks](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks)。

## <a name="clean-up-resources"></a>清理资源

如果不再需要使用虚拟网络创建的资源，请删除资源组。 这会删除该虚拟网络和所有相关资源。

若要删除资源组，请调用 `Remove-AzResourceGroup` cmdlet：

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>后续步骤
在本快速入门中，你部署了具有两个子网的 Azure 虚拟网络。 若要详细了解 Azure 虚拟网络，请继续学习虚拟网络的教程。

> [!div class="nextstepaction"]
> [筛选网络流量](tutorial-filter-network-traffic.md)
