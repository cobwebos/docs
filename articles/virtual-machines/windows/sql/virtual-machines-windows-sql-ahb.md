---
title: 如何在 Azure 中更改 SQL Server VM 的许可模式
description: 了解如何使用 Azure 混合权益在 Azure 中将 Azure 中的 SQL 虚拟机的授权切换到 "自带即用即付"。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/05/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 37457d8ce1189f9282f4763633e944e3c2d639c9
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2019
ms.locfileid: "68816719"
---
# <a name="how-to-change-licensing-model-for-a-sql-server-virtual-machine-in-azure"></a>如何在 Azure 中更改 SQL Server 虚拟机的许可模式
本文介绍如何在 Azure 中使用新的 SQL VM 资源提供程序 **Microsoft.SqlVirtualMachine** 来更改 SQL Server 虚拟机的许可模型。

虚拟机 (VM) 托管有两种许可模式 SQL Server-即用即付和 Azure 混合权益 (AHB)。 现在, 使用 Azure 门户、Azure CLI 或 PowerShell, 可以修改 SQL Server VM 的许可模式。 

即**用即付**(PAYG) 模型意味着运行 Azure VM 的每秒费用包括 SQL Server 许可证的成本。
[Azure 混合权益 (AHB)](https://azure.microsoft.com/pricing/hybrid-benefit/)允许你将自己的 SQL Server 许可证与运行 SQL SERVER 的 VM 一起使用。 

SQL Server 的 Microsoft Azure 混合权益允许在 Azure 虚拟机上使用具有软件保障 ("合格许可证") 的 SQL Server 许可证。 使用 SQL Server 的 Azure 混合权益时, 客户将不会对 VM 上的 SQL Server 许可证的使用进行收费, 但必须仍需支付基础云计算 (也就是基本费率)、存储空间和备份的成本, 以及与 u 关联的 i/o。服务的 se (如果适用)。

根据 Microsoft 产品条款, "客户必须指出他们正在使用 Azure SQL 数据库 (托管实例、弹性池和单一数据库)、Azure 数据工厂、SQL Server Integration Services 或 SQL Server Azure 混合下的虚拟机在 Azure 上配置工作负荷时的 SQL Server 权益。

若要指示在 Azure VM 上使用 SQL Server 的 Azure 混合权益并且合规, 有三个选项:

1. 使用 Azure marketplace 中的 BYOL SQL Server 映像预配虚拟机, 仅适用于具有企业协议的客户。
1. 使用 Azure marketplace 中的 PAYG SQL Server 映像预配虚拟机, 并激活 AHB。
1. 在 Azure VM 上自行安装 SQL Server, 手动[注册其 SQL Server VM](virtual-machines-windows-sql-register-with-resource-provider.md)并激活 AHB。

在预配 VM 时, 将设置 SQL Server 的许可证类型, 并且可以在以后随时更改。 在许可证模型之间切换不会**造成停机**, 也不会重新启动 VM, 无需支付**额外的费用**(事实上, 激活 AHB 可*降低*成本), 并且**立即生效**。 

## <a name="prerequisites"></a>先决条件

使用 SQL VM 资源提供程序需要安装 SQL IaaS 扩展。 因此，若要继续利用 SQL VM 提供程序，需要：
- 一个 [Azure 订阅](https://azure.microsoft.com/free/)。
- [软件保障](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default)。 
- 向安装的[SQL VM 资源提供程序](virtual-machines-windows-sql-register-with-resource-provider.md)注册了一个[SQL Server VM](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) 。 


## <a name="change-license-for-vms-already-registered-with-resource-provider"></a>更改已注册到资源提供程序的 Vm 的许可证 

# <a name="azure-portaltabazure-portal"></a>[Azure 门户](#tab/azure-portal)

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

您可以直接从门户修改授权模型。 

1. 打开[Azure 门户](https://portal.azure.com)并启动 SQL Server VM 的[SQL 虚拟机资源](virtual-machines-windows-sql-manage-portal.md#access-sql-virtual-machine-resource)。 
1. 选择 "**设置**" 下的 "**配置**"。 
1. 选择 " **Azure 混合权益**" 选项, 然后选中复选框以确认你具有具有软件保障的 SQL Server 许可证。 
1. 选择 "**配置**" 页面底部的 "**应用**"。 

![门户中的 AHB](media/virtual-machines-windows-sql-ahb/ahb-in-portal.png)


# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)

可以通过 Azure CLI 更改许可模型。  

以下代码片段将即用即付许可证模型切换到 BYOL (或使用 Azure 混合权益):

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

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
可以使用 PowerShell 更改许可模型。

以下代码片段将即用即付许可证模型切换到 BYOL (或使用 Azure 混合权益):

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

以下代码片段将 BYOL 模型切换为即用即付模式:

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

## <a name="change-license-for-vms-not-registered-with-resource-provider"></a>更改未注册到资源提供程序的 Vm 的许可证

如果你预配了 PAYG Azure Marketplace 映像的 SQL Server VM, 则 SQL 许可证类型将为 PAYG。 如果使用 Azure Marketplace 中的 BYOL 映像预配了 SQL Server VM, 则许可证类型将为 AHUB。 默认情况下, 从默认 (PAYG) 或 BYOL Azure Marketplace 映像预配的所有 SQL Server Vm 将自动注册到 SQL VM 资源提供程序, 因此他们可以更改[许可证类型](#change-license-for-vms-already-registered-with-resource-provider)

仅可通过 Azure 混合权益在 Azure VM 上自行安装 SQL Server, 并且应通过将 SQL Server 许可证设置为 AHB, 将[这些 vm 注册到 SQL VM 资源提供程序](virtual-machines-windows-sql-register-with-resource-provider.md), 以根据 Microsoft 产品条款指示 AHB 的使用情况。

如果 SQL VM 已注册到 SQL VM 资源提供程序, 则只能将 SQL Server VM 的许可证类型更改为 PAYG 或 AHB;所有 SQL Vm 都应该注册到 SQL VM RP 以获得许可证符合性。

## <a name="remarks"></a>备注

 - Azure 云解决方案合作伙伴 (CSP) 客户可以通过先部署即用即付 VM, 然后将其转换为自带许可 (如果他们具有活动 SA) 来利用 Azure 混合权益。
 - 如果删除 SQL Server VM 资源, 则会返回到映像的硬编码许可证设置。 
  - 更改许可模式的功能是 SQL VM 资源提供程序的一项功能。 通过 Azure 门户部署 marketplace 映像会自动向资源提供程序注册 SQL Server VM。 但是, 自行安装 SQL Server 的客户需要手动[注册其 SQL Server VM](virtual-machines-windows-sql-register-with-resource-provider.md)。 
- 若要将 SQL Server VM 添加到可用性集, 则需要重新创建 VM。 因此, 添加到可用性集的所有 Vm 都将恢复为默认的即用即付许可证类型, 需要重新启用 AHB。 


## <a name="limitations"></a>限制

 - 更改许可模式仅适用于具有软件保障的客户。
 - 只有 SQL Server 的标准版和企业版才支持更改许可模式。 不支持速成版、Web 版和开发人员版的许可证更改。 
 - 仅支持使用资源管理器模型部署的虚拟机更改许可模式。 不支持使用经典模型部署的 Vm。 可以将 VM 从经典部署模型迁移到资源管理器 (ARM) 模型, 并向 SQL VM 资源提供程序注册。 将 VM 注册到 SQL VM 资源提供程序后, 会在 VM 上提供授权模型更改。 
 - 仅为公有云安装启用更改许可模式。
 - 只有具有单个 NIC (网络接口) 的虚拟机才支持更改许可模式。 在具有多个 NIC 的虚拟机上, 在尝试此过程之前, 你应该首先删除其中一个 Nic (通过使用 Azure 门户)。 否则, 会遇到类似于下面的错误:`The virtual machine '\<vmname\>' has more than one NIC associated.`虽然你可以在更改授权模式后将 NIC 添加回 VM, 但是, 在 Azure 门户中完成的操作 (如自动修补和备份) 将不再被视为受支持。


## <a name="known-errors"></a>已知错误

### <a name="the-resource-microsoftsqlvirtualmachinesqlvirtualmachinesresource-group-under-resource-group-resource-group-was-not-found-the-property-sqlserverlicensetype-cannot-be-found-on-this-object-verify-that-the-property-exists-and-can-be-set"></a>找不到资源组 "\<\<资源组 >" 下的资源 "SqlVirtualMachine/SqlVirtualMachines/资源组 >"。 在此对象上找不到属性 "sqlServerLicenseType"。 验证该属性是否存在并可设置。
如果尝试更改尚未注册到 SQL VM 资源提供程序的 SQL Server VM 上的授权模型, 则会出现此错误。 你需要将资源提供程序注册到你的[订阅](virtual-machines-windows-sql-register-with-resource-provider.md#register-sql-vm-resource-provider-with-subscription), 然后向 SQL[资源提供程序](virtual-machines-windows-sql-register-with-resource-provider.md)注册你的 SQL Server VM。 

### <a name="cannot-validate-argument-on-parameter-sku"></a>无法验证参数“Sku”中的自变量
使用 Azure PowerShell > 4.0 时, 尝试更改 SQL Server VM 许可模式时可能会遇到此错误:`Set-AzResource: Cannot validate argument on parameter 'Sku'. The argument is null or empty. Provide an argument that is not null or empty, and then try the command again.`

若要解决此错误，请在切换许可模型时，取消注释上述 PowerShell 代码片段中的以下行：

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
* [Windows VM 上的 SQL Server 常见问题解答](virtual-machines-windows-sql-server-iaas-faq.md)
* [Windows VM 上的 SQL Server 定价指南](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Windows VM 上的 SQL Server 发行说明](virtual-machines-windows-sql-server-iaas-release-notes.md)


