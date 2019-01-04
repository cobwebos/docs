---
title: 如何在 Azure 中更改 SQL VM 的许可模型 | Microsoft Docs
description: 了解如何在 Azure 中切换 SQL 虚拟机的许可。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/14/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: cd784163047f4fe15fde719ce56aba64eed60dd2
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/13/2018
ms.locfileid: "53336979"
---
# <a name="how-to-change-the-licensing-model-for-a-sql-server-virtual-machine-in-azure"></a>如何在 Azure 中更改 SQL Server 虚拟机的许可模型
本文介绍如何在 Azure 中使用新的 SQL 资源提供程序 **Microsoft.SqlVirtualMachine** 来更改 SQL Server 虚拟机的许可模型。 对于托管 SQL Server 的虚拟机 (VM) 来说，有两个许可模型 -“按使用情况付费”和“自带许可证(BYOL)”。 现在可以通过 PowerShell 或 Azure CLI 对 SQL VM 使用哪个许可模型进行修改。 

“按使用情况付费”模型意味着 Azure VM 的每秒运行成本包括 SQL Server 许可证的费用。

“自带许可证”模型也称 [Azure 混合权益](https://azure.microsoft.com/pricing/hybrid-benefit/)，允许在运行 SQL Server 的 VM 中使用你自己的 SQL Server 许可证。 有关价格的详细信息，请参阅 [SQL VM 定价指南](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance)。

在两个许可证模型之间进行切换不会导致停机，不会重启 VM，不会增加费用（事实上，激活 AHB 会降低费用），并且会立即生效。 


## <a name="register-existing-sql-vm-with-new-resource-provider"></a>将现有 SQL VM 注册到新的资源提供程序
在许可模型之间进行切换是新的 SQL VM 资源提供程序 (Microsoft.SqlVirtualMachine) 提供的一项功能。 目前，若要切换许可模型，首先需将新提供程序注册到订阅，然后将现有 VM 注册到新的 SQL VM 资源提供程序。 若要利用 SQL VM 资源提供程序，还必须安装 SQL IaaS 扩展。 这样做将允许你注册使用 VHD 部署的 VM。 有关详细信息，请参阅 [SQL IaaS 扩展](virtual-machines-windows-sql-server-agent-extension.md)。 

  >[!IMPORTANT]
  > 如果删除 SQL VM 资源，则会回退到映像的硬编码许可证设置。 

### <a name="powershell"></a>PowerShell


下面的代码片段将你连接到 Azure 并验证你正在使用哪个订阅 ID。 
```PowerShell
# Connect to Azure
Connect-AzureRmAccount
Account: <account_name>

# Verify your subscription ID
Get-AzureRmContext

# Set the correct Azure Subscription ID
Set-AzureRmContext -SubscriptionId <Subscription_ID>
```

以下代码片段首先为你的订阅注册新的 SQL 资源提供程序，然后将现有 SQL VM 注册到新的资源提供程序。 

```powershell
# Register the new SQL resource provider for your subscription
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine

# Register your existing SQL VM with the new resource provider
# example: $vm=Get-AzureRmVm -ResourceGroupName AHBTest -Name AHBTest
$vm=Get-AzureRmVm -ResourceGroupName <ResourceGroupName> -Name <VMName>
New-AzureRmResource -ResourceName $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location -ResourceType Microsoft.SqlVirtualMachine/sqlVirtualMachines -Properties @{virtualMachineResourceId=$vm.Id}
```

### <a name="portal"></a>门户
也可通过门户注册新的 SQL VM 资源提供程序。 为此，请执行以下步骤：
1. 打开 Azure 门户，导航到“所有服务”。 
1. 导航到“订阅”，选择感兴趣的订阅。  
1. 在“订阅”边栏选项卡中，导航到“资源提供程序”。 
1. 在筛选器中键入 `sql`，以便显示与 SQL 相关的资源提供程序。 
1. 根据所需操作为 **Microsoft.SqlVirtualMachine** 提供程序选择“注册”、“重新注册”或“取消注册”。 

  ![修改提供程序](media/virtual-machines-windows-sql-ahb/select-resource-provider-sql.png)


## <a name="use-powershell"></a>使用 PowerShell 
可以使用 PowerShell 更改许可模型。  请确保在切换许可模型之前，SQL VM 已注册到新的 SQL 资源提供程序。 

以下代码片段将“按使用情况付费”许可证模型切换为“BYOL”（或者使用 Azure 混合权益）： 
```PowerShell
#example: $SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName AHBTest -ResourceName AHBTest
$SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType="AHUB"
$SqlVm | Set-AzureRmResource -Force 
``` 

以下代码片段将“BYOL”模型切换为“按使用情况付费”：
```PowerShell
#example: $SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName AHBTest -ResourceName AHBTest
$SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType="PAYG"
$SqlVm | Set-AzureRmResource -Force 
```

  >[!NOTE]
  > 若要在许可证之间进行切换，必须使用新的 SQL VM 资源提供程序。 如果你在将 SQL VM 注册到新提供程序之前尝试运行这些命令，可能会遇到以下错误：`Get-AzureRmResource : The Resource 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/AHBTest' under resource group 'AHBTest' was not found. The property 'sqlServerLicenseType' cannot be found on this object. Verify that the property exists and can be set. ` 如果你看到此错误，请[将 SQL VM 注册到新的资源提供程序](#register-existing-SQL-vm-with-new-resource-provider)。 
 

## <a name="use-azure-cli"></a>使用 Azure CLI
可以通过 Azure CLI 更改许可模型。  请确保在切换许可模型之前，SQL VM 已注册到新的 SQL 资源提供程序。 

以下代码片段将“按使用情况付费”许可证模型切换为“BYOL”（或者使用 Azure 混合权益）：
```azurecli
az resource update -g <resource_group_name> -n <sql_virtual_machine_name> --resource-type "Microsoft.SqlVirtualMachine/SqlVirtualMachines" --set properties.sqlServerLicenseType=AHUB
# example: az resource update -g AHBTest -n AHBTest --resource-type "Microsoft.SqlVirtualMachine/SqlVirtualMachines" --set properties.sqlServerLicenseType=AHUB
```

以下代码片段将“BYOL”模型切换为“按使用情况付费”： 
```azurecli
az resource update -g <resource_group_name> -n <sql_virtual_machine_name> --resource-type "Microsoft.SqlVirtualMachine/SqlVirtualMachines" --set properties.sqlServerLicenseType=PAYG
# example: az resource update -g AHBTest -n AHBTest --resource-type "Microsoft.SqlVirtualMachine/SqlVirtualMachines" --set properties.sqlServerLicenseType=PAYG
```

  >[!NOTE]
  >若要在许可证之间进行切换，必须使用新的 SQL VM 资源提供程序。 如果你在将 SQL VM 注册到新提供程序之前尝试运行这些命令，可能会遇到以下错误：`The Resource 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/AHBTest' under resource group 'AHBTest' was not found. ` 如果你看到此错误，请[将 SQL VM 注册到新的资源提供程序](#register-existing-SQL-vm-with-new-resource-provider)。 

## <a name="view-current-licensing"></a>查看当前许可 

可以使用以下代码片段查看 SQL VM 的当前许可模型。 

```PowerShell
# View current licensing model for your SQL VM
#example: $SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType
```

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅以下文章： 

* [Windows VM 上的 SQL Server 概述](virtual-machines-windows-sql-server-iaas-overview.md)
* [Windows VM 上的 SQL Server 常见问题解答](virtual-machines-windows-sql-server-iaas-faq.md)
* [Windows VM 上的 SQL Server 定价指南](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Windows VM 上的 SQL Server 发行说明](virtual-machines-windows-sql-server-iaas-release-notes.md)


