---
title: 创建 SQL Server VM（Azure 资源管理器模板）
description: 了解如何使用 Azure 资源管理器模板在 Azure 虚拟机 (VM) 上创建 SQL Server。
author: MashaMSFT
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: mathoma
ms.date: 06/29/2020
ms.service: virtual-machines-sql
ms.openlocfilehash: 8b165f640548f28e5d94e5a791c0fe8545df4d78
ms.sourcegitcommit: cec9676ec235ff798d2a5cad6ee45f98a421837b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85852460"
---
# <a name="create-sql-server-vm-azure-resource-manager-template"></a>创建 SQL Server VM（Azure 资源管理器模板）

使用此 Azure 资源管理器模板可在 Azure 虚拟机 (VM) 上部署 SQL Server。 

[!INCLUDE [About Azure Resource Manager](../../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>先决条件

SQL Server VM ARM 模板需要以下内容：

- 最新版本的 [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) 和/或 [PowerShell](/powershell/scripting/install/installing-powershell?view=powershell-7)。 
- 预先配置的[资源组](../../../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups)以及准备就绪的[虚拟网络](../../../virtual-network/quick-create-portal.md)和[子网](../../../virtual-network/virtual-network-manage-subnet.md#add-a-subnet)。
- Azure 订阅。 如果还没有该订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。


## <a name="review-the-template"></a>查看模板

本快速入门中使用的模板来自 [Azure 快速启动模板](https://azure.microsoft.com/resources/templates/101-sql-vm-new-storage/)。

:::code language="json" source="~/quickstart-templates/101-sql-vm-new-storage/azuredeploy.json" highlight="169-310":::

该模板中定义了五个 Azure 资源： 

- [Microsoft.Network/publicIpAddresses](/azure/templates/microsoft.network/publicipaddresses)：创建公共 IP 地址。 
- [Microsoft.Network/networkSecurityGroups](/azure/templates/microsoft.network/networksecuritygroups)：创建网络安全组。 
- [Microsoft.Network/networkInterfaces](/azure/templates/microsoft.network/networkinterfaces)：配置网络接口。 
- [Microsoft.Compute/virtualMachines](/azure/templates/microsoft.compute/virtualmachines)：在 Azure 中创建虚拟机。 
- [Microsoft.SqlVirtualMachine/SqlVirtualMachines](/azure/templates/microsoft.sqlvirtualmachine/sqlvirtualmachines)：向 SQL VM 资源提供程序注册虚拟机。 

可以在[快速入门模板库](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Sqlvirtualmachine)中找到更多有关 Azure VM 模板的 SQL Server。


## <a name="deploy-the-template"></a>部署模板

1. 选择下图登录到 Azure 并打开一个模板。 使用该模板创建一个虚拟机，并在该虚拟机上安装目标 SQL Server 版本，然后向 SQL VM 资源提供程序注册。 

   [![部署到 Azure](../../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-sql-vm-new-storage%2fazuredeploy.json)

2. 选择或输入以下值。

    * 订阅：选择 Azure 订阅。
    * 资源组：SQL Server VM 的已准备就绪的资源组。 
    * **区域**：选择区域。  例如“美国中部”。
    * **虚拟机名称**：输入 SQL Server 虚拟机的名称。 
    * **虚拟机大小**：从下拉列表中为虚拟机选择适当的大小。
    * **现有虚拟网络名称**：输入 SQL Server VM 的已准备就绪的虚拟网络的名称。 
    * **现有 Vnet 资源组**：输入虚拟网络已准备就绪的资源组。 
    * **现有子网名称**：已准备就绪的子网的名称。 
    * **映像产品/服务**：选择最适合你的业务需求的 SQL Server 和 Windows Server 映像。 
    * **SQL SKU**：选择最适合你的业务需求的 SQL Server SKU 版本。 
    * **管理员用户名**：虚拟机管理员的用户名。 
    * **管理员密码**：VM 管理员帐户使用的密码。 
    * **存储工作负载类型**：最适合你的业务的工作负载存储类型。 
    * **SQL 数据磁盘计数**：SQL Server 用于数据文件的磁盘数。  
    * **数据路径**：SQL Server 数据文件的路径。 
    * **SQL 日志磁盘计数**：SQL Server 用于日志文件的磁盘数。 
    * **日志路径**：SQL Server 日志文件的路径。 
    * 位置：所有资源的位置，此值应保留默认值 `[resourceGroup().location]`。 

3. 选择“查看 + 创建”。 成功部署 SQL Server VM 后，你会收到通知。

使用 Azure 门户部署模板。 除了 Azure 门户，还可以使用 Azure PowerShell、Azure CLI 和 REST API。 若要了解其他部署方法，请参阅[部署模板](../../../azure-resource-manager/templates/deploy-powershell.md)。

## <a name="review-deployed-resources"></a>查看已部署的资源

可以使用 Azure CLI 查看已部署的资源。 


```azurecli-interactive
echo "Enter the resource group where your SQL Server VM exists:" &&
read resourcegroupName &&
az resource list --resource-group $resourcegroupName 
```

## <a name="clean-up-resources"></a>清理资源

不再需要时，可使用 Azure CLI 或 Azure PowerShell 删除资源组：

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>后续步骤

有关引导你完成模板创建过程的分步教程，请参阅：

> [!div class="nextstepaction"]
> [教程：创建和部署你的第一个 Azure 资源管理器模板](/azure/azure-resource-manager/templates/template-tutorial-create-first-template)

有关部署 SQL Server VM 的其他方法，请参阅： 
- [Azure 门户](create-sql-vm-portal.md)
- [PowerShell](create-sql-vm-powershell.md)

若要了解详细信息，请参阅 [Azure VM 上的 SQL Server 概述](sql-server-on-azure-vm-iaas-what-is-overview.md)。
