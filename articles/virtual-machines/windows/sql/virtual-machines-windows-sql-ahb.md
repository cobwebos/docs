---
title: 更改 Azure 中 SQL Server VM 的许可证型号
description: 了解如何使用 Azure 混合权益在 Azure 中切换 SQL Server 虚拟机的许可, 使其从即用即付许可。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/05/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 2cce369f6a0670790ede7367609f87c18672ddd5
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70100612"
---
# <a name="change-the-license-model-for-a-sql-server-virtual-machine-in-azure"></a>更改 Azure 中 SQL Server 虚拟机的许可证型号
本文介绍如何使用新的 SQL VM 资源提供程序**SqlVirtualMachine**在 Azure 中更改 SQL Server 虚拟机 (VM) 的许可证模型。

承载 SQL Server 的 VM 有两个许可证模型: 即用即付和 Azure 混合权益。 您可以使用 Azure 门户、Azure CLI 或 PowerShell 来修改您的 SQL Server VM 的许可证模型。 

即用即付模型意味着运行 Azure VM 的每秒费用包括 SQL Server 许可证的成本。
[Azure 混合权益](https://azure.microsoft.com/pricing/hybrid-benefit/)允许你将自己的 SQL Server 许可证与运行 SQL SERVER 的 VM 一起使用。 

Azure 混合权益允许在 Azure 虚拟机上使用带有软件保障 ("合格许可证") 的 SQL Server 许可证。 使用 Azure 混合权益, 客户无需支付对 VM 使用 SQL Server 许可证的费用。 但仍必须支付基础云计算 (即, 基础费率)、存储和备份的成本。 他们还必须为与服务的使用相关的 i/o 付费 (如果适用)。

根据 Microsoft 产品条款:"客户必须在配置时, 指出他们正在使用 Azure SQL 数据库 (托管实例、弹性池和单一数据库)、Azure 数据工厂、SQL Server Integration Services 或 SQL Server Azure 混合权益 SQL Server 虚拟机Azure 上的工作负荷 "。

若要指示在 Azure VM 上使用 SQL Server 的 Azure 混合权益, 并符合以下三个选项:

- 使用 Azure Marketplace 中的自带许可证 SQL Server 映像预配虚拟机。 此选项仅适用于具有企业协议的客户。
- 使用 Azure Marketplace 中的即用即付 SQL Server 映像预配虚拟机, 并激活 Azure 混合权益。
- 在 Azure VM 上自行安装 SQL Server, 手动[注册 SQL Server VM](virtual-machines-windows-sql-register-with-resource-provider.md)并激活 Azure 混合权益。

预配 VM 时, 将设置 SQL Server 的许可证类型。 您可以在以后随时对其进行更改。 在许可证模型之间切换不会造成停机, 不会重新启动 VM, 无需额外付费, 并且立即生效。 事实上, 激活 Azure 混合权益*降低了*成本。

## <a name="prerequisites"></a>先决条件

使用 SQL VM 资源提供程序需要 SQL Server IaaS 扩展。 因此, 你需要以下各项:
- 一个 [Azure 订阅](https://azure.microsoft.com/free/)。
- [软件保障](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default)。 
- 向[SQL VM 资源提供程序](virtual-machines-windows-sql-register-with-resource-provider.md)注册的[SQL Server VM](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) 。


## <a name="change-the-license-for-vms-already-registered-with-the-resource-provider"></a>更改已注册到资源提供程序的 Vm 的许可证 

# <a name="portaltabazure-portal"></a>[门户](#tab/azure-portal)

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

可以直接从门户修改许可证模型: 

1. 打开[Azure 门户](https://portal.azure.com)并打开 SQL Server VM 的[SQL 虚拟机资源](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource)。 
1. 选择 "**设置**" 下的 "**配置**"。 
1. 选择 " **Azure 混合权益**" 选项, 然后选中该复选框以确认你具有具有软件保障的 SQL Server 许可证。 
1. 选择 "**配置**" 页面底部的 "**应用**"。 

![门户中的 Azure 混合权益](media/virtual-machines-windows-sql-ahb/ahb-in-portal.png)


# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

你可以使用 Azure CLI 来更改你的许可证模型。  

以下代码片段将切换即用即付许可证模型, 以自带许可证 (或使用 Azure 混合权益):

```azurecli-interactive
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
# example: az sql vm update -n AHBTest -g AHBTest --license-type AHUB

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type AHUB
```

以下代码片段将您自己的许可模式切换为即用即付模式: 

```azurecli-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
# example: az sql vm update -n AHBTest -g AHBTest --license-type PAYG

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type PAYG
```

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
你可以使用 PowerShell 更改你的许可证模型。

以下代码片段将切换即用即付许可证模型, 以自带许可证 (或使用 Azure 混合权益):

```powershell-interactive
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
#example: $SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName AHBTest -ResourceName AHBTest
$SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType="AHUB"
<# the following code snippet is only necessary if using Azure Powershell version > 4
$SqlVm.Kind= "LicenseChange"
$SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
$SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new() #>
$SqlVm | Set-AzResource -Force 
```

以下代码片段将您自己的许可模式切换为即用即付模式:

```powershell-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
#example: $SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName AHBTest -ResourceName AHBTest
$SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType="PAYG"
<# the following code snippet is only necessary if using Azure Powershell version > 4
$SqlVm.Kind= "LicenseChange"
$SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
$SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new() #>
$SqlVm | Set-AzResource -Force 
```
---

## <a name="change-the-license-for-vms-not-registered-with-the-resource-provider"></a>更改未注册到资源提供程序的 Vm 的许可证

如果你从即用即付 Azure Marketplace 映像预配了 SQL Server VM, 则 SQL Server 许可证类型将为 "即用即付"。 如果使用 Azure Marketplace 中的自带许可证映像预配了 SQL Server VM, 则许可证类型将为 AHUB。 默认情况下 (即用即付) 或自带许可 Azure Marketplace 映像中预配的所有 SQL Server Vm 将自动注册到 SQL VM 资源提供程序, 因此它们可以更改[许可证类型](#change-the-license-for-vms-already-registered-with-the-resource-provider)。

仅可通过 Azure 混合权益在 Azure VM 上自行安装 SQL Server。 你应将[这些 vm 注册到 SQL VM 资源提供程序](virtual-machines-windows-sql-register-with-resource-provider.md), 方法是将 SQL Server 许可证设置为 Azure 混合权益, 以根据 Microsoft 产品条款指示 Azure 混合权益的使用情况。

仅当 SQL Server VM 注册到 SQL VM 资源提供程序时, 才能将 SQL Server VM 的许可证类型更改为即用即付或 Azure 混合权益。 所有 SQL Server Vm 应向资源提供程序注册, 以获得许可证符合性。

## <a name="remarks"></a>备注

- Azure 云解决方案提供商 (CSP) 客户可以通过先部署即用即付 VM, 然后将其转换为自带许可 (如果他们具有有效的软件保障) 来使用 Azure 混合权益。
- 如果删除 SQL Server VM 资源, 则会返回到映像的硬编码许可证设置。 
- 更改许可模式的功能是 SQL VM 资源提供程序的一项功能。 通过 Azure 门户部署 Azure Marketplace 映像会自动向资源提供程序注册 SQL Server VM。 但自行安装 SQL Server 的客户需要手动[注册其 SQL Server VM](virtual-machines-windows-sql-register-with-resource-provider.md)。 
- 若要将 SQL Server VM 添加到可用性集, 则需要重新创建 VM。 因此, 添加到可用性集的任何 Vm 都将恢复为默认的即用即付许可证类型。 需要重新启用 Azure 混合权益。 


## <a name="limitations"></a>限制

- 更改许可证模型仅适用于具有软件保障的客户。
- 仅 SQL Server 的标准版和企业版才支持更改许可证模型。 不支持速成版、Web 版和开发人员版的许可证更改。 
- 只有通过 Azure 资源管理器模型部署的虚拟机才支持更改许可证模型。 不支持通过经典模型部署的 Vm。 可以将 VM 从经典部署模型迁移到资源管理器模型, 并将其注册到 SQL VM 资源提供程序。 向 SQL VM 资源提供程序注册 VM 后, 许可证模型更改将在 VM 上可用。
- 仅为公有云安装启用更改许可证模型。
- 仅在具有单个 NIC (网络接口) 的虚拟机上支持更改许可证模型。 在具有多个 NIC 的虚拟机上, 在尝试此过程之前, 你应该首先删除其中一个 Nic (通过使用 Azure 门户)。 否则, 你将收到类似于以下内容的错误: 
   
  `The virtual machine '\<vmname\>' has more than one NIC associated.` 
   
  虽然你可以在更改许可证模型后将 NIC 添加回 VM, 但将不再考虑到通过 Azure 门户中的 "SQL Server 配置" 页 (如自动修补和备份) 执行的操作。

## <a name="known-errors"></a>已知错误

### <a name="the-resource-microsoftsqlvirtualmachinesqlvirtualmachinesresource-group-under-resource-group-resource-group-was-not-found"></a>找不到资源组 "\<\<资源组 >" 下的资源 "SqlVirtualMachine/SqlVirtualMachines/资源组 >"。
当你尝试在尚未注册到 SQL VM 资源提供程序的 SQL Server VM 上更改许可证模型时, 将发生此错误:

`The Resource 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/\<resource-group>' under resource group '\<resource-group>' was not found. The property 'sqlServerLicenseType' cannot be found on this object. Verify that the property exists and can be set.`

你需要将资源提供程序注册到你的[订阅](virtual-machines-windows-sql-register-with-resource-provider.md#register-the-sql-vm-resource-provider-with-a-subscription), 然后向[资源提供程序注册你的 SQL Server VM](virtual-machines-windows-sql-register-with-resource-provider.md)。 

### <a name="cannot-validate-argument-on-parameter-sku"></a>无法验证参数“Sku”中的自变量
尝试使用低于4.0 的 Azure PowerShell 版本更改 SQL Server VM 许可证模型时, 可能会遇到此错误:

`Set-AzResource: Cannot validate argument on parameter 'Sku'. The argument is null or empty. Provide an argument that is not null or empty, and then try the command again.`

若要解决此错误, 请在切换许可证模型时取消前面提到的 PowerShell 代码段中的以下行的注释:

  ```powershell-interactive
  # the following code snippet is necessary if using Azure Powershell version > 4
  $SqlVm.Kind= "LicenseChange"
  $SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
  $SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new()
  ```
  
使用以下代码验证 Azure PowerShell 版本：
  
  ```powershell-interactive
  Get-Module -ListAvailable -Name Azure -Refresh
  ```

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅以下文章： 

* [Windows VM 上的 SQL Server 概述](virtual-machines-windows-sql-server-iaas-overview.md)
* [Windows VM 上的 SQL Server 常见问题](virtual-machines-windows-sql-server-iaas-faq.md)
* [Windows VM 上 SQL Server 的定价指南](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Windows VM 上 SQL Server 的发行说明](virtual-machines-windows-sql-server-iaas-release-notes.md)


