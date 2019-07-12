---
title: 如何更改 SQL Server VM 在 Azure 中的授权模型
description: 了解如何针对 SQL 的虚拟机在 Azure 中从即用即付许可切换到自带的自己的许可证使用 Azure 混合权益。
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
ms.date: 02/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 78ad784a45d2b0063932791daedc9b1ec1aafd72
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2019
ms.locfileid: "67786764"
---
# <a name="how-to-change-licensing-model-for-a-sql-server-virtual-machine-in-azure"></a>如何更改 SQL Server 虚拟机在 Azure 中的授权模型
本文介绍如何在 Azure 中使用新的 SQL VM 资源提供程序 **Microsoft.SqlVirtualMachine** 来更改 SQL Server 虚拟机的许可模型。

有两种授权模型托管 SQL Server 的虚拟机 (VM) 的即用即付，以及 Azure 混合权益 (AHB)。 和现在，使用 Azure 门户、 Azure CLI 或 PowerShell，您可以修改 SQL Server VM 的许可模式。 

**即用即付**(PAYG) 模型的推出意味着 Azure VM 运行的每秒费用包括 SQL Server 许可证的费用。
[Azure 混合权益 (AHB)](https://azure.microsoft.com/pricing/hybrid-benefit/) ，可使用运行 SQL Server 的虚拟机使用 SQL Server 许可证。 

Microsoft SQL Server 的 Azure 混合权益允许具有软件保障 （"合格许可证"） 在 Azure 虚拟机上使用 SQL Server 许可证。 SQL Server 的 Azure 混合权益，客户不会收取在 VM 上的 SQL Server 许可证的使用情况，但它们仍必须支付的基础云计算 （即，基准费率）、 存储和备份，以及其 u 与关联的 I/O 成本se （如适用） 的服务。

根据 Microsoft 产品条款"的客户必须指示使用的 Azure SQL 数据库 （托管实例、 弹性池和单一数据库），Azure 数据工厂、 SQL Server Integration Services 或在 Azure 的混合的 SQL Server 虚拟机SQL Server 时在 Azure 上配置工作负荷的权益。"

若要指示 Azure 混合权益用于 Azure VM 上的 SQL Server 并符合要求，有三个选项：

1. 预配虚拟机从 Azure marketplace，仅适用于具有企业协议的客户使用 BYOL SQL Server 映像。
1. 预配使用从 Azure marketplace PAYG SQL Server 映像的虚拟机并激活 AHB。
1. 自手动安装为 Azure VM 上的 SQL Server[注册其 SQL Server 虚拟机](virtual-machines-windows-sql-register-with-resource-provider.md)并激活 AHB。

当 VM 预配，以后随时可更改设置的 SQL Server 的许可证类型。 许可模式模式之间切换会产生**无需停机**，不会重启 VM，添加**无需额外付费**(事实上，激活 AHB*减少*成本) 并为**即日起**。 

## <a name="prerequisites"></a>先决条件

使用 SQL VM 资源提供程序需要安装 SQL IaaS 扩展。 因此，若要继续利用 SQL VM 提供程序，需要：
- 一个 [Azure 订阅](https://azure.microsoft.com/free/)。
- [软件保障](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default)。 
- 一个[SQL Server 虚拟机](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision)注册[SQL 虚拟机资源提供程序](virtual-machines-windows-sql-register-with-resource-provider.md)安装。 


## <a name="change-license-for-vms-already-registered-with-resource-provider"></a>更改 Vm 的许可证已注册到资源提供程序 

# <a name="azure-portaltabazure-portal"></a>[Azure 门户](#tab/azure-portal)

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

您可以修改直接从门户的授权模型。 

1. 打开[Azure 门户](https://portal.azure.com)，然后启动[SQL 虚拟机资源](virtual-machines-windows-sql-manage-portal.md#access-sql-virtual-machine-resource)为 SQL Server VM。 
1. 选择**配置**下**设置**。 
1. 选择**Azure 混合权益**选项，然后选择该复选框以确认你拥有具有软件保障的 SQL Server 许可证。 
1. 选择**Apply**底部**配置**页。 

![在门户中的 AHB](media/virtual-machines-windows-sql-ahb/ahb-in-portal.png)


# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)

可以通过 Azure CLI 更改许可模型。  

下面的代码段将切换到 BYOL （或使用 Azure 混合权益） 即用即付许可证模型：

```azurecli-interactive
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
# example: az sql vm update -n AHBTest -g AHBTest --license-type AHUB

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type AHUB
```

下面的代码段将切换到即用即付自带的自己的许可证模型： 

```azurecli-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
# example: az sql vm update -n AHBTest -g AHBTest --license-type PAYG

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type PAYG
```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
可以使用 PowerShell 更改许可模型。

下面的代码段将切换到 BYOL （或使用 Azure 混合权益） 即用即付许可证模型：

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

下面的代码段将切换到即用即付 BYOL 模型：

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

## <a name="change-license-for-vms-not-registered-with-resource-provider"></a>更改 Vm 使用资源提供程序未注册的许可证

如果 SQL Server VM 从 PAYG Azure Marketplace 映像预配 SQL 许可证类型将是 PAYG。 如果你在预配 SQL Server VM 使用 BYOL 映像从 Azure Marketplace 许可证类型将 AHUB。 所有 SQL Server Vm 预都配从默认值 (PAYG) 或 BYOL Azure Marketplace 映像都会自动注册到的 SQL VM 资源提供程序，以便用户可以更改[许可证类型](#change-license-for-vms-already-registered-with-resource-provider)

只有符合条件自安装通过 Azure 混合权益的 Azure VM 上的 SQL Server 版本，您应[使用 SQL VM 资源提供程序注册这些 Vm](virtual-machines-windows-sql-register-with-resource-provider.md)通过将 SQL Server 许可证设置为 AHB 以指示根据 AHB 使用情况Microsoft 产品条款。

如果 SQL VM 注册到 SQL VM 资源提供程序; 只能更改为 PAYG 或 AHB SQL Server VM 的许可证类型并为许可证符合性，应使用 SQL VM RP 注册所有 SQL Vm。

## <a name="remarks"></a>备注

 - Azure 云解决方案合作伙伴 (CSP) 客户可以通过首先将部署的即用即付的 VM，然后将其转换为自带的自己的许可证，如果它们具有可用 SA 利用 Azure 混合权益。
 - 如果您删除 SQL Server VM 资源，将返回到该映像的硬编码的许可证设置。 
  - 若要更改的许可模式，您是 SQL 虚拟机资源提供程序的功能。 自动部署通过 Azure 门户的 marketplace 映像注册资源提供程序的 SQL Server VM。 但是，在自安装 SQL Server 的客户将需要手动[注册其 SQL Server 虚拟机](virtual-machines-windows-sql-register-with-resource-provider.md)。 
- 添加到可用性集的 SQL Server 虚拟机需要重新创建 VM。 作为此类，则所有 Vm 添加到可用性集将返回到默认即用即付许可证类型和 AHB 将需要重新启用它。 


## <a name="limitations"></a>限制

 - 更改授权模型是仅适用于具有软件保障的客户。
 - SQL Server 的 standard 和 enterprise 版本仅支持更改授权模型。 Express、 Web 和开发人员许可更改处于不受支持。 
 - 使用资源管理器模型部署的虚拟机仅支持更改授权模型。 不支持使用经典模型部署的 Vm。 
 - 公共云安装仅启用更改授权模型。
 - 仅在具有单个 NIC （网络接口） 的虚拟机上支持更改授权模型。 在具有多个 NIC 的虚拟机，你应首先删除其中一个 Nic （通过使用 Azure 门户） 在尝试此过程之前。 否则，将遇到如下错误：`The virtual machine '\<vmname\>' has more than one NIC associated.` 尽管您可以将 NIC 添加回 VM 后更改的授权模式，通过在 Azure 门户中，如自动修补和备份，SQL 配置页上执行的操作将不再被视为受支持。


## <a name="known-errors"></a>已知错误

### <a name="the-resource-microsoftsqlvirtualmachinesqlvirtualmachinesresource-group-under-resource-group-resource-group-was-not-found-the-property-sqlserverlicensetype-cannot-be-found-on-this-object-verify-that-the-property-exists-and-can-be-set"></a>资源 Microsoft.SqlVirtualMachine/SqlVirtualMachines/\<资源组 > 资源组下\<资源组 > 找不到。 此对象上找不到 sqlServerLicenseType 的属性。 验证属性存在并且可以设置。
尝试更改到 SQL VM 资源提供程序尚未注册 SQL Server VM 上的许可模式时，将发生此错误。 将需要注册资源提供程序为你[订阅](virtual-machines-windows-sql-register-with-resource-provider.md#register-sql-vm-resource-provider-with-subscription)，并将 SQL 注册 SQL Server 虚拟机[资源提供程序](virtual-machines-windows-sql-register-with-resource-provider.md)。 

### <a name="cannot-validate-argument-on-parameter-sku"></a>无法验证参数“Sku”中的自变量
试图更改您的 SQL Server VM 的许可模型，在使用 Azure PowerShell 时，可能会遇到此错误 > 4.0: `Set-AzResource: Cannot validate argument on parameter 'Sku'. The argument is null or empty. Provide an argument that is not null or empty, and then try the command again.`

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


