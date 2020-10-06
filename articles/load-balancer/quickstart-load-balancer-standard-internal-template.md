---
title: 使用 Azure 资源管理器模板（ARM 模板）创建内部负载均衡器
description: 了解如何使用 Azure 资源管理器模板（ARM 模板）创建内部 Azure 负载均衡器。
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: allensu
ms.date: 09/14/2020
ms.openlocfilehash: 287afc51aa15ed4cadba7e2d6cd389e4869d7d61
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/15/2020
ms.locfileid: "90532821"
---
# <a name="quickstart-create-an-internal-load-balancer-to-load-balance-vms-by-using-an-arm-template"></a>快速入门：使用 ARM 模板创建内部负载均衡器，以对 VM 进行负载均衡

本快速入门介绍如何使用 Azure 资源管理器模板（ARM 模板）来创建内部 Azure 负载均衡器。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>先决条件

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="review-the-template"></a>查看模板

本快速入门中使用的模板来自 [Azure 快速入门模板](https://azure.microsoft.com/resources/templates/201-2-vms-internal-load-balancer)。

:::code language="json" source="~/quickstart-templates/201-2-vms-internal-load-balancer/azuredeploy.json":::

该模板中已定义了多个 Azure 资源：

- [**Microsoft.Storage/storageAccounts**](/azure/templates/microsoft.storage/storageaccounts)：用于启动诊断的虚拟机存储帐户。
- [**Microsoft.Compute/availabilitySets**](/azure/templates/microsoft.compute/availabilitySets)：虚拟机的可用性集。
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualNetworks)：负载均衡器和虚拟机的虚拟网络。
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkInterfaces)：虚拟机的网络接口。
- [**Microsoft.Network/loadBalancers**](/azure/templates/microsoft.network/loadBalancers)：内部负载均衡器。
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualMachines)：虚拟机。

若要查找与 Azure 负载均衡器相关的更多模板，请参阅 [Azure 快速入门模板](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular)。

## <a name="deploy-the-template"></a>部署模板

**Azure CLI**

```azurecli-interactive
read -p "Enter the location (i.e. westcentralus): " location
resourceGroupName="myResourceGroupLB"
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-internal-load-balancer/azuredeploy.json" 

az group create \
--name $resourceGroupName \
--location $location

az deployment group create \
--resource-group $resourceGroupName \
--template-uri  $templateUri
```

## <a name="review-deployed-resources"></a>查看已部署的资源

1. 登录 [Azure 门户](https://portal.azure.com)。

2. 从左侧窗格中选择“资源组”****。

3. 选择你在上一部分中创建的资源组。 默认资源组名称是“myResourceGroupLB”

4. 验证是否在资源组中创建了以下资源：

:::image type="content" source="media/quickstart-load-balancer-standard-internal-template/verify-creation.png" alt-text="用于验证资源创建的用户 Azure 门户。" border="true":::

## <a name="clean-up-resources"></a>清理资源

如果不再需要上述资源组及其包含的所有资源，可以使用 [az group delete](/cli/azure/group#az-group-delete) 命令将其删除。

```azurecli-interactive 
  az group delete \
    --name myResourceGroupLB
```

## <a name="next-steps"></a>后续步骤

有关引导你完成模板创建过程的分步教程，请参阅：

> [!div class="nextstepaction"]
> 创建和部署你的第一个 ARM 模板[