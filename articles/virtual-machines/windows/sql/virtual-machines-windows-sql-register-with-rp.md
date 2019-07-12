---
title: 在 Azure 中的 SQL Server 虚拟机注册到 SQL VM 资源提供程序 |Microsoft Docs
description: SQL Server 虚拟机注册到 SQL VM 的资源提供程序，以提高可管理性。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/24/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: c78b5d71fffbf579b15f747c048bd65259039749
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2019
ms.locfileid: "67786750"
---
# <a name="register-sql-server-virtual-machine-in-azure-with-the-sql-vm-resource-provider"></a>在 Azure 中的 SQL Server 虚拟机注册到 SQL VM 资源提供程序

本文介绍如何使用 SQL VM 资源提供程序注册 Azure SQL Server 虚拟机 (VM)。 

自动部署 SQL Server 虚拟机 marketplace 映像通过 Azure 门户注册资源提供程序的 SQL Server 虚拟机。 但是，如果您选择自安装 SQL Server Azure 虚拟机上而不是从 Azure Marketplace 中选择一个映像，然后应该注册到 SQL Server VM 的资源提供程序现在为：

-  **符合性**– 每个 Microsoft 产品条款，使用的客户[Azure 混合权益](https://azure.microsoft.com/pricing/hybrid-benefit/)必须指示到 Microsoft 时使用的 Azure 混合权益-并且，若要执行此操作，它们必须注册到 SQL VM 资源提供程序。 

-  **功能优势**-向资源提供程序注册到 SQL Server VM 解锁[自动修补](virtual-machines-windows-sql-automated-patching.md)，[自动备份](virtual-machines-windows-sql-automated-backup-v2.md)，监视和可管理性功能，以及[许可](virtual-machines-windows-sql-ahb.md)并[edition](virtual-machines-windows-sql-change-edition.md)大的灵活性。 以前，这些是仅提供到 SQL Server VM 映像从 Azure Marketplace。

自安装 Azure VM 上的 SQL Server 或预配包含 SQL Server 的自定义 VHD 从 Azure VM 符合通过 Azure 混合权益适用于 SQL Server 客户注册后 SQL VM 资源指示给 Microsoft 使用的条件提供程序。 

若要使用 SQL VM 资源提供程序，还必须与你的订阅中注册的 SQL VM 资源提供程序。 这可以使用 Azure 门户、 Azure CLI 和 PowerShell 来实现。 

## <a name="prerequisites"></a>系统必备

若要使用资源提供程序注册到 SQL Server VM，你将需要： 

- 一个 [Azure 订阅](https://azure.microsoft.com/free/)。
- 一个[SQL Server 虚拟机](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision)。 
- [Azure CLI](/cli/azure/install-azure-cli)并[PowerShell](/powershell/azure/new-azureps-module-az)。 

## <a name="register-with-sql-vm-resource-provider"></a>使用 SQL VM 资源提供程序注册
如果[SQL IaaS 扩展](virtual-machines-windows-sql-server-agent-extension.md)上已安装了 VM，然后使用 SQL VM 资源提供程序注册只需创建类型 Microsoft.SqlVirtualMachine/SqlVirtualMachines 的元数据资源。 下面是将代码段仅当在 VM 上已安装 SQL IaaS 扩展注册到 SQL VM 资源提供程序。 你需要提供的类型注册为 SQL VM 资源提供程序时所需的 SQL Server 许可证 PAYG 或 AHUB。 

注册 SQL Server 虚拟机使用以下代码片段使用 PowerShell:

  ```powershell-interactive
     // Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     // Register with SQL VM resource provider
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;sqlLicenseType='AHUB'}  
  
  ```

如果在 VM 上未安装 SQL IaaS 扩展，然后您可以向注册的 SQL VM 资源提供程序通过指定的轻型 SQL 管理模式。 在轻型 SQL 管理模式下，SQL 虚拟机资源提供程序将自动安装中的 SQL IaaS 扩展[轻量级模式下](virtual-machines-windows-sql-server-agent-extension.md#install-in-lightweight-mode)并验证 SQL Server 实例元数据; 它将不重新启动 SQL Server 服务。 你需要提供的类型注册为 SQL VM 资源提供程序时所需的 SQL Server 许可证 PAYG 或 AHUB。 

在轻型 SQL 管理模式下使用以下代码片段使用 PowerShell 注册 SQL Server 虚拟机：

  ```powershell-interactive
     // Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     // Register SQL VM with 'Lightweight' SQL IaaS agent
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;sqlLicenseType='AHUB';sqlManagement='LightWeight'}  
  
  ```
使用 SQL VM 资源提供程序中注册[轻量级模式下](virtual-machines-windows-sql-server-agent-extension.md#install-in-lightweight-mode)将确保符合性和启用灵活的许可，以及现有版本的 SQL Server 更新。 可以使用仅在轻量级模式下的 SQL VM 资源提供程序注册故障转移群集实例和多实例部署。 可以按照的说明进行操作来升级到 Azure 门户上找到[完整模式](virtual-machines-windows-sql-server-agent-extension.md#full-mode-installation)并随时启用全面的可管理性功能集与 SQL Server 重新启动。 

## <a name="register-sql-server-2008r2-on-windows-server-2008-vms"></a>注册 Windows Server 2008 虚拟机上的 SQL Server 2008/R2

SQL Server 2008 和 2008 R2 安装在 Windows Server 2008 上可注册到资源提供在 SQL VM [NoAgent](virtual-machines-windows-sql-server-agent-extension.md)模式。 此选项可确保符合性，并允许 SQL Server 虚拟机，若要在 Azure 门户中监视使用有限的功能。

下表详细说明了在注册期间提供的参数可接受的值：

| 参数 | 可接受的值                                 |
| :------------------| :--------------------------------------- |
| **sqlLicenseType** | `'AHUB'`或 `'PAYG'`                    |
| **sqlImageOffer**  | `'SQL2008-WS2008'` 或 `'SQL2008R2-WS2008`|
| &nbsp;             | &nbsp;                                   |


若要注册你的 SQL Server 2008 或 Windows Server 2008 实例上的 2008 R2，请使用以下 Powershell 代码片段：  

  ```powershell-interactive
     // Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
    New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
      -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
      -Properties @{virtualMachineResourceId=$vm.Id;sqlLicenseType='AHUB'; `
       sqlManagement='NoAgent';sqlImageSku='Standard';sqlImageOffer='SQL2008R2-WS2008'}
  ```



## <a name="verify-registration-status"></a>验证注册状态
你可以验证是否 SQL Server 已注册到 SQL VM 资源提供程序使用 Azure 门户、 Azure CLI 或 PowerShell。 

# <a name="azure-portaltabazure-portal"></a>[Azure 门户](#tab/azure-portal)
若要验证使用 Azure 门户的注册状态，请执行以下操作。

1. 登录到 [Azure 门户](https://portal.azure.com)。 
1. 导航到您[SQL 虚拟机](virtual-machines-windows-sql-manage-portal.md)。
1. 从列表中选择 SQL Server VM。 如果此处未列出你的 SQL Server VM，很可能未向 SQL 虚拟机资源提供程序注册到 SQL Server VM。 
1. 查看下的值`Status`。 如果`Status = Succeeded`，则 SQL Server 虚拟机已注册到 SQL VM 资源提供程序已成功。 

    ![验证与 SQL RP 注册状态](media/virtual-machines-windows-sql-register-with-rp/verify-registration-status.png)

# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)

验证当前的 SQL Server 虚拟机注册状态，使用以下 AZ CLI 命令。 `ProvisioningState` 将显示`Succeeded`如果注册成功。 

  ```azurecli-interactive
  az sql vm show -n <vm_name> -g <resource_group>
  ```


# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

验证当前的 SQL Server 虚拟机注册状态，使用以下 PowerShell cmdlet。 `ProvisioningState` 将显示`Succeeded`如果注册成功。 

  ```powershell-interactive
  Get-AzResource -ResourceName <vm_name> -ResourceGroupName <resource_group> -ResourceType Microsoft.SqlVirtualMachine/sqlVirtualMachines
  ```
一个错误，指示资源提供程序尚未注册 SQL Server 虚拟机。 

---

## <a name="register-sql-vm-resource-provider-with-subscription"></a>SQL VM 资源提供程序注册订阅 

若要使用 SQL VM 资源提供程序注册到 SQL Server VM，必须为你的订阅注册资源提供程序。 可以使用 Azure 门户或 Azure CLI 执行操作。

# <a name="azure-portaltabazure-portal"></a>[Azure 门户](#tab/azure-portal)


以下步骤将注册到 Azure 订阅使用 Azure 门户的 SQL VM 资源提供程序。 

1. 打开 Azure 门户，导航到“所有服务”。  
1. 导航到“订阅”  ，选择感兴趣的订阅。  
1. 上**订阅**页上，导航到**资源提供程序**。 
1. 在筛选器中键入 `sql`，以便显示与 SQL 相关的资源提供程序。 
1. 根据所需操作为    **Microsoft.SqlVirtualMachine** 提供程序选择“注册”、“重新注册”或“取消注册”。 

   ![修改提供程序](media/virtual-machines-windows-sql-ahb/select-resource-provider-sql.png)

# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)

下面的代码段将注册到 Azure 订阅的 SQL VM 资源提供程序。 

```azurecli-interactive
# Register the new SQL VM resource provider to your subscription 
az provider register --namespace Microsoft.SqlVirtualMachine 
```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

以下 PowerShell 代码片段将注册到 Azure 订阅的 SQL VM 资源提供程序。

```powershell-interactive
# Register the new SQL VM resource provider to your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```
---

## <a name="remarks"></a>备注

 - SQL VM 资源提供程序仅支持使用 Resource Manager 部署的 SQL Server Vm。 使用经典模型不支持 SQL Server 虚拟机部署。 
 - SQL VM 资源提供程序仅支持 SQL Server 虚拟机部署到公有云。 不支持部署到专用或政府云。 

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅以下文章： 

* [Windows VM 上的 SQL Server 概述](virtual-machines-windows-sql-server-iaas-overview.md)
* [Windows VM 上的 SQL Server 常见问题解答](virtual-machines-windows-sql-server-iaas-faq.md)
* [Windows VM 上的 SQL Server 定价指南](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Windows VM 上的 SQL Server 发行说明](virtual-machines-windows-sql-server-iaas-release-notes.md)


