---
title: 创建托管实例（ARM 模板和 PowerShell）
titleSuffix: Azure SQL Managed Instance
description: 使用此 Azure PowerShell 示例脚本创建托管实例。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: seo-dt-2019, devx-track-azurepowershell
ms.devlang: PowerShell
ms.topic: sample
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 03/12/2019
ms.openlocfilehash: 6758aed67949032bb93086527623b60af33e716e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89079540"
---
# <a name="use-powershell-with-an-azure-resource-manager-template-to-create-a-managed-instance"></a>将 PowerShell 与 Azure 资源管理器模板配合使用，以创建托管实例

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqlmi.md)]

可以使用 Azure PowerShell 库和 Azure 资源管理器模板创建托管实例。

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 PowerShell，则本教程需要 Azure PowerShell 1.4.0 或更高版本。 如果需要进行升级，请参阅 [Install Azure PowerShell module](/powershell/azure/install-az-ps)（安装 Azure PowerShell 模块）。 如果在本地运行 PowerShell，则运行 `Connect-AzAccount` 以创建与 Azure 的连接。

Azure PowerShell 命令可以使用预定义的 Azure 资源管理器模板启动部署。 可以在模板中指定以下属性：

- 托管实例名称
- SQL 管理员用户名和密码。
- 实例的大小（内核数和最大存储大小）。
- 要将实例放置在其中的 VNet 和子网。
- 实例的服务器级排序规则（预览版）。

实例名称、SQL 管理员用户名、VNet/子网和排序规则以后无法更改。 其他实例属性可以更改。

## <a name="prerequisites"></a>先决条件

此示例假定已为托管实例[创建了有效的网络环境](../virtual-network-subnet-create-arm-template.md)或[修改了现有的 VNet](../vnet-existing-add-subnet.md)。 如有必要，可以使用单独的[ Azure 资源管理器模板](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-managed-instance-azure-environment)准备网络环境。 


此示例使用 cmdlet [New-AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment) 和 [Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork)，因此请确保已安装以下 PowerShell 模块：

```powershell
Install-Module Az.Network
Install-Module Az.Resources
```

## <a name="azure-resource-manager-template"></a>Azure Resource Manager 模板


将以下脚本保存到 .json 文件中，并记下文件位置： 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.1",
    "parameters": {
        "instance": {
            "type": "string"
        },
        "user": {
            "type": "string"
        },
        "pwd": {
            "type": "securestring"
        },
        "subnetId": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[parameters('instance')]",
            "location": "West Central US",
            "tags": {
                "Description":"GP Instance with custom instance collation - Serbian_Cyrillic_100_CS_AS"
            },
            "sku": {
                "name": "GP_Gen5",
                "tier": "GeneralPurpose"
            },
            "properties": {
                "administratorLogin": "[parameters('user')]",
                "administratorLoginPassword": "[parameters('pwd')]",
                "subnetId": "[parameters('subnetId')]",
                "storageSizeInGB": 256,
                "vCores": 8,
                "licenseType": "LicenseIncluded",
                "hardwareFamily": "Gen5",
                "collation": "Serbian_Cyrillic_100_CS_AS"
            },
            "type": "Microsoft.Sql/managedInstances",
            "identity": {
                "type": "SystemAssigned"
            },
            "apiVersion": "2015-05-01-preview"
        }
    ]
}
```

使用以前保存的 .json 文件的正确文件路径更新以下 PowerShell 脚本，并更改脚本中对象的名称：

```powershell
$subscriptionId = "ed827499-xxxx-xxxx-xxxx-xxxxxxxxxx"
Select-AzSubscription -SubscriptionId $subscriptionId

# Managed instance properties
$resourceGroup = "rg_mi"
$location = "West Central US"
$name = "managed-instance-name"
$user = "miSqlAdmin"
$secpasswd = ConvertTo-SecureString "<Put some strong password here>" -AsPlainText -Force

# Network configuration
$vNetName = "my_vnet"
$vNetResourceGroup = "rg_mi_vnet"
$subnetName = "ManagedInstances"
$vNet = Get-AzVirtualNetwork -Name $vNetName -ResourceGroupName $vNetResourceGroup
$subnet = Get-AzVirtualNetworkSubnetConfig -Name $SubnetName -VirtualNetwork $vNet
$subnetId = $subnet.Id

# Deploy instance using Azure Resource Manager template:
New-AzResourceGroupDeployment  -Name MyDeployment -ResourceGroupName $resourceGroup  `
                                    -TemplateFile 'C:\...\create-managed-instance.json' `
                                    -instance $name -user $user -pwd $secpasswd -subnetId $subnetId
```

该脚本完成后，可以通过所有 Azure 服务和配置的 IP 地址访问托管实例。

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 的详细信息，请参阅 [Azure PowerShell 文档](/powershell/azure/)。

Azure SQL 托管实例的其他 PowerShell 脚本示例可在 [Azure SQL 托管实例 PowerShell 脚本](../../database/powershell-script-content-guide.md)中找到。
