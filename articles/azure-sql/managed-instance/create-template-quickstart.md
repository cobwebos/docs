---
title: Azure 资源管理器：创建 Azure SQL 托管实例
description: 了解如何使用 Azure 资源管理器模板创建 Azure SQL 托管实例。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: subject-armqs
ms.devlang: ''
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 06/22/2020
ms.openlocfilehash: 01c6c37d31d41f88b370face372555536724adde
ms.sourcegitcommit: bf99428d2562a70f42b5a04021dde6ef26c3ec3a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2020
ms.locfileid: "85256064"
---
# <a name="quickstart-create-an-azure-sql-managed-instance-using-an-azure-resource-manager-template"></a>快速入门：使用 Azure 资源管理器模板创建 Azure SQL 托管实例

本快速入门重点介绍部署资源管理器模板以创建 Azure SQL 托管实例和 vNet 的过程。

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

如果没有 Azure 订阅，可以[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

无。

## <a name="create-an-azure-sql-managed-instance"></a>创建 Azure SQL 托管实例

[Azure SQL 托管实例](sql-managed-instance-paas-overview.md)是一个智能、完全托管且可缩放的云数据库，与 SQL Server 数据库引擎之间的功能奇偶一致性达 100%。

### <a name="review-the-template"></a>查看模板

本快速入门中使用的模板来自 [Azure 快速入门模板](https://azure.microsoft.com/resources/templates/101-sqlmi-new-vnet/)。

:::code language="json" source="~/quickstart-templates/101-sqlmi-new-vnet/azuredeploy.json":::

该模板中定义了以下资源：

- [**Microsoft.Sql/managedinstances**](/azure/templates/microsoft.sql/managedinstances)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.Network/virtualNetworks)
- [**Microsoft.Network/routeTables**](/azure/templates/microsoft.Network/routeTables)
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.Network/networkSecurityGroups)



可以在 [Azure 快速启动模板](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Sql&pageNumber=1&sort=Popular)中找到更多模板示例。

## <a name="deploy-the-template"></a>部署模板

从以下 PowerShell 代码块中选择“试用”以打开 Azure Cloud Shell。

> [!IMPORTANT]
> 部署托管实例是一项运行时间很长的操作。 通常情况下，子网中第一个实例的部署时间比子网中已经有托管实例时实例的部署时间要长得多。 若要了解平均预配时间，请参阅 [SQL 托管实例管理操作](sql-managed-instance-paas-overview.md#management-operations)。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-sqlmi-new-vnet/azuredeploy.json"

$resourceGroupName = "${projectName}rg"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

Read-Host -Prompt "Press [ENTER] to continue ..."
```

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
read -p "Enter a project name that is used for generating resource names:" projectName &&
read -p "Enter the location (i.e. centralus):" location &&
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-sqlmi-new-vnet/azuredeploy.json" &&
resourceGroupName="${projectName}rg" &&
az group create --name $resourceGroupName --location "$location" &&
az deployment group create --resource-group $resourceGroupName --template-uri  $templateUri &&
echo "Press [ENTER] to continue ..." &&
read
```

* * *

## <a name="review-deployed-resources"></a>查看已部署的资源

访问 [Azure 门户](https://portal.azure.com/#blade/HubsExtension/BrowseResourceGroups)，并验证所选资源组中是否有托管实例。 由于创建托管实例可能需要一些时间，所以可能需要在资源组的“概述”页面中查看“部署”链接 。

- 有关介绍如何从 Azure 虚拟机连接到 SQL 托管实例的快速入门，请参阅[配置 Azure 虚拟机连接](connect-vm-instance-configure.md)。
- 有关介绍如何使用点到站点连接从本地客户端计算机连接到 SQL 托管实例的快速入门，请参阅[配置点到站点连接](point-to-site-p2s-configure.md)。

## <a name="clean-up-resources"></a>清理资源

若要执行[后续步骤](#next-steps)，请保留托管实例，但请在完成任何其他教程之后删除托管实例及相关资源。 删除托管实例之后，请参阅[在删除托管实例后删除子网](virtual-cluster-delete.md)。


若要删除资源组，请执行以下操作：

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
```

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName
```

* * *

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [将 Azure VM 配置为连接到 Azure SQL 托管实例](connect-vm-instance-configure.md)
