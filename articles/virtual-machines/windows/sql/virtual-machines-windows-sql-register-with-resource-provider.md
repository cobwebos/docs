---
title: 通过 SQL VM 资源提供程序在 Azure 中注册 SQL Server 虚拟机 |Microsoft Docs
description: 向 SQL VM 资源提供程序注册你的 SQL Server VM 以提高可管理性。
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
ms.openlocfilehash: 95a167cbc8fde4488e3f46ffd850f0619cf1b651
ms.sourcegitcommit: 770b060438122f090ab90d81e3ff2f023455213b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/17/2019
ms.locfileid: "68305872"
---
# <a name="register-sql-server-virtual-machine-in-azure-with-the-sql-vm-resource-provider"></a>通过 SQL VM 资源提供程序在 Azure 中注册 SQL Server 虚拟机

本文介绍如何向 SQL VM 资源提供程序注册 Azure SQL Server 虚拟机 (VM)。 

通过 Azure 门户部署 SQL Server VM marketplace 映像会自动将 SQL Server VM 注册到资源提供程序。 但是, 如果选择在 Azure 虚拟机上自行安装 SQL Server 而不是从 Azure Marketplace 中选择映像, 则应立即将 SQL Server VM 注册到资源提供程序:

-  **符合性**–根据 Microsoft 产品条款, 使用[Azure 混合权益](https://azure.microsoft.com/pricing/hybrid-benefit/)的客户在使用 Azure 混合权益时必须向 Microsoft 指出, 他们必须向 SQL VM 资源提供程序注册。 

-  **功能优势**-向资源提供程序注册你的 SQL Server VM 会解锁[自动修补](virtual-machines-windows-sql-automated-patching.md)、[自动备份](virtual-machines-windows-sql-automated-backup-v2.md)、监视和可管理性功能, 以及[授权](virtual-machines-windows-sql-ahb.md)和[版本](virtual-machines-windows-sql-change-edition.md)灵活性。 以前, 这些仅适用于从 Azure Marketplace SQL Server VM 映像。

在 Azure VM 上自行安装 SQL Server, 或通过使用 SQL Server 在自定义 VHD 中预配 Azure VM 时, 应通过 Azure 混合权益实现 SQL Server, 条件是客户通过注册 SQL VM 资源来表明使用 Microsoft程序. 

若要利用 SQL VM 资源提供程序, 还必须将 SQL VM 资源提供程序注册到订阅。 这可以通过 Azure 门户、Azure CLI 和 PowerShell 来完成。 

## <a name="prerequisites"></a>先决条件

若要向资源提供程序注册你的 SQL Server VM, 你将需要以下各项: 

- 一个 [Azure 订阅](https://azure.microsoft.com/free/)。
- 一个[SQL Server VM](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision)。 
- [Azure CLI](/cli/azure/install-azure-cli)和[PowerShell](/powershell/azure/new-azureps-module-az)。 

## <a name="register-with-sql-vm-resource-provider"></a>向 SQL VM 资源提供程序注册
如果已在 VM 上安装[Sql IaaS 扩展](virtual-machines-windows-sql-server-agent-extension.md), 则使用 sql VM 资源提供程序注册只需创建 SqlVirtualMachine/SqlVirtualMachines 类型的元数据资源。 下面是在 VM 上已安装 SQL IaaS 扩展时要向 SQL VM 资源提供程序注册的代码片段。 将 SQL VM 资源提供程序注册为 "PAYG" 或 "AHUB" 时, 需要提供所需的 SQL Server 许可证类型。 

使用 PowerShell 注册 SQL Server VM 以下代码片段:

  ```powershell-interactive
     # Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     # Register with SQL VM resource provider
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;SqlServerLicenseType='AHUB'}  
  
  ```

如果 VM 上未安装 SQL IaaS 扩展, 则可以通过指定轻型 SQL 管理模式向 SQL VM 资源提供程序注册。 在轻型 SQL 管理模式下, SQL VM 资源提供程序将在[轻型模式下](virtual-machines-windows-sql-server-agent-extension.md#install-in-lightweight-mode)自动安装 Sql IaaS 扩展, 并验证 SQL Server 实例元数据;这不会重新启动 SQL Server 服务。 将 SQL VM 资源提供程序注册为 "PAYG" 或 "AHUB" 时, 需要提供所需的 SQL Server 许可证类型。 

使用 PowerShell 通过以下代码片段在轻型 SQL 管理模式下注册 SQL Server VM:

  ```powershell-interactive
     # Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     # Register SQL VM with 'Lightweight' SQL IaaS agent
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;SqlServerLicenseType='AHUB';sqlManagement='LightWeight'}  
  
  ```

在[轻型模式下](virtual-machines-windows-sql-server-agent-extension.md#install-in-lightweight-mode)向 SQL VM 资源提供程序注册将确保合规性并启用灵活的许可以及就地 SQL Server 版更新。 故障转移群集实例和多实例部署只能在轻型模式下在 SQL VM 资源提供程序中注册。 可以按照 Azure 门户上的说明升级到[完整模式](virtual-machines-windows-sql-server-agent-extension.md#full-mode-installation), 并通过 SQL Server 随时重新启动来启用全面的可管理性功能集。 

## <a name="register-sql-server-2008r2-on-windows-server-2008-vms"></a>在 Windows Server 2008 Vm 上注册 SQL Server 2008/R2

Windows Server 2008 上安装的 SQL Server 2008 和 2008 R2 可以在[NoAgent](virtual-machines-windows-sql-server-agent-extension.md)模式下注册到 SQL VM 资源提供。 此选项可确保符合性, 并允许在 Azure 门户中监视 SQL Server VM 功能有限。

下表详细说明了在注册过程中提供的参数的可接受值:

| 参数 | 可接受值                                 |
| :------------------| :--------------------------------------- |
| **sqlLicenseType** | `'AHUB'`, 或`'PAYG'`                    |
| **sqlImageOffer**  | `'SQL2008-WS2008'` 或 `'SQL2008R2-WS2008`|
| &nbsp;             | &nbsp;                                   |


若要在 Windows Server 2008 实例上注册 SQL Server 2008 或 2008 R2, 请使用以下 Powershell 代码片段:  

  ```powershell-interactive
     # Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
    New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
      -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
      -Properties @{virtualMachineResourceId=$vm.Id;SqlServerLicenseType='AHUB'; `
       sqlManagement='NoAgent';sqlImageSku='Standard';sqlImageOffer='SQL2008R2-WS2008'}
  ```

## <a name="verify-registration-status"></a>验证注册状态
可以使用 Azure 门户、Azure CLI 或 PowerShell 验证是否已向 SQL VM 资源提供程序注册了 SQL Server。 

### <a name="azure-portal"></a>Azure 门户 
若要使用 Azure 门户验证注册的状态, 请执行以下操作。

1. 登录到 [Azure 门户](https://portal.azure.com)。 
1. 导航到[SQL 虚拟机](virtual-machines-windows-sql-manage-portal.md)。
1. 从列表中选择 SQL Server VM。 如果此处未列出你的 SQL Server VM, 则很可能你的 SQL Server VM 尚未注册到 SQL VM 资源提供程序。 
1. 查看下`Status`的值。 如果`Status = Succeeded`为, 则已成功向 SQL VM 资源提供程序注册了 SQL Server VM。 

    ![通过 SQL RP 注册验证状态](media/virtual-machines-windows-sql-register-with-rp/verify-registration-status.png)

### <a name="az-cli"></a>Az CLI

通过以下 AZ CLI 命令验证当前 SQL Server VM 注册状态。 `ProvisioningState`将显示`Succeeded`注册是否成功。 

  ```azurecli-interactive
  az sql vm show -n <vm_name> -g <resource_group>
  ```


### <a name="powershell"></a>PowerShell

通过以下 PowerShell cmdlet 验证当前 SQL Server VM 注册状态。 `ProvisioningState`将显示`Succeeded`注册是否成功。 

  ```powershell-interactive
  Get-AzResource -ResourceName <vm_name> -ResourceGroupName <resource_group> -ResourceType Microsoft.SqlVirtualMachine/sqlVirtualMachines
  ```
错误表明 SQL Server VM 尚未注册到资源提供程序。 

---

## <a name="register-sql-vm-resource-provider-with-subscription"></a>向订阅注册 SQL VM 资源提供程序 

若要向 SQL VM 资源提供程序注册你的 SQL Server VM, 你必须将资源提供程序注册到你的订阅。 可以通过 Azure 门户或 Azure CLI 来实现此目的。

### <a name="azure-portal"></a>Azure 门户

以下步骤将使用 Azure 门户将 SQL VM 资源提供程序注册到你的 Azure 订阅。 

1. 打开 Azure 门户，导航到“所有服务”。  
1. 导航到“订阅”  ，选择感兴趣的订阅。  
1. 在 "**订阅**" 页上, 导航到 "**资源提供程序**"。 
1. 在筛选器中键入 `sql`，以便显示与 SQL 相关的资源提供程序。 
1. 根据所需操作为    **Microsoft.SqlVirtualMachine** 提供程序选择“注册”、“重新注册”或“取消注册”。 

   ![修改提供程序](media/virtual-machines-windows-sql-ahb/select-resource-provider-sql.png)

### <a name="az-cli"></a>Az CLI
以下代码片段会将 SQL VM 资源提供程序注册到你的 Azure 订阅。 

```azurecli-interactive
# Register the new SQL VM resource provider to your subscription 
az provider register --namespace Microsoft.SqlVirtualMachine 
```

### <a name="powershell"></a>PowerShell

以下 PowerShell 代码段会将 SQL VM 资源提供程序注册到你的 Azure 订阅。

```powershell-interactive
# Register the new SQL VM resource provider to your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```
---

## <a name="remarks"></a>备注

 - SQL VM 资源提供程序仅支持使用 "资源管理器" 部署 SQL Server Vm。 不支持使用 "经典模型" 部署 SQL Server Vm。 
 - SQL VM 资源提供程序仅支持部署到公有云的 SQL Server Vm。 不支持部署到私有或政府云。 
 
## <a name="frequently-asked-questions"></a>常见问题 

**我是否应该从 Azure Marketplace 上的 SQL 映像注册我的 SQL Server VM？**

否。 Microsoft autoregisters Vm 是从 Azure Marketplace 上的 SQL 映像预配的。 仅当未从 Azure marketplace 中的 SQL 映像预配 VM 并且 SQL Server 是自行安装的时, 才需要向 SQL VM 资源提供程序注册。

**SQL VM 资源提供程序是否适用于所有客户？** 

是的。 如果客户不使用来自 Azure marketplace 的 SQL Server 映像和自行安装的 SQL Server 或其自定义 VHD, 则他们应该向 SQL VM 资源提供程序注册其 SQL Server VM。 所有类型的订阅 (直接、EA 和 CSP) 拥有的 Vm 均可注册到 SQL VM 资源提供程序。

**如果 SQL Server VM 已安装 SQL IaaS 扩展, 是否应向 SQL VM 资源提供程序注册？**

如果你的 SQL Server VM 是自安装的, 则不是从 Azure marketplace 上的 SQL 映像预配的, 你应该向 SQL VM 资源提供程序注册, 即使安装了 SQL IaaS 扩展也是如此。 使用 SQL VM 资源提供程序注册会创建 SqlVirtualMachines 类型的新资源。 安装 SQL IaaS 扩展不会创建该资源。

**向 SQL VM 资源提供程序注册时, 默认的 SQL 管理模式是什么？**

向 SQL VM RP 注册时, 默认的 SQL 管理模式已_满_。 如果在向 SQL VM 资源提供程序注册时未设置 SQL 管理属性, 则该模式将设置为完全可管理性。 在虚拟机上安装 SQL IaaS 扩展是在完全可管理性模式下向 SQL VM 资源提供程序注册的先决条件。

**向 SQL VM 资源提供程序注册的先决条件是什么？**

在轻型模式或无代理模式下, 不需要向 SQL VM 资源提供程序注册。 在完整模式下向 SQL VM 资源提供程序注册的先决条件是在 VM 上安装了 SQL IaaS 扩展。

**如果未在 VM 上安装 SQL IaaS 扩展, 是否可以向 SQL VM 资源提供程序注册？**

是的, 如果未在 VM 上安装 SQL IaaS 扩展, 则可以使用轻型管理模式向 SQL VM 资源提供程序进行注册。 在轻型模式下, SQL VM 资源提供程序将使用控制台应用程序来验证 SQL 实例的版本。 验证 VM 上至少有一个 SQL 实例运行后, 控制台应用程序会关闭自身。 向轻型模式下的 SQL VM 资源提供程序注册不会 SQL Server 重新启动, 并且不会在 VM 上创建代理。

**向 SQL VM 资源提供程序注册将在我的 VM 上安装代理？**

否。 使用 SQL VM 资源提供程序注册将仅创建新的元数据资源, 并且不会在 VM 上安装代理。 仅在启用完全可管理性时需要 SQL IaaS 扩展, 因此, 将可管理性模式从轻型升级到完整版将安装 SQL IaaS 扩展, 并将 SQL Server 重新启动。

**是否向 VM 上的 SQL VM 资源提供程序重启 SQL Server 注册？**

否。 向 SQL VM 资源提供程序注册将仅创建新的元数据资源, 并且不会在 VM 上重新启动 SQL Server。 仅在安装 SQL IaaS 扩展时需要重启 SQL Server;需要和 SQL IaaS 扩展才能实现完全可管理性。 将可管理性模式从轻型升级到完整版将安装 SQL IaaS 扩展, 并 SQL Server 重启。

**向 SQL VM 资源提供程序注册时, 轻量和无代理管理模式之间的区别是什么？** 

无-代理管理模式仅适用于 Windows Server 2008 上的 SQL Server 2008/R2;对于这些版本, 它是唯一可用的管理模式。 对于所有其他版本的 SQL Server, 可用的两种可管理性模式为轻型和 full。 无代理模式要求客户设置 SQL Server 版本和版本属性;轻型模式将查询 VM 以查找 SQL 实例的版本。

**能否在轻型或无代理模式下向 SQL VM 资源提供程序注册 Azure CLI？**

否。 仅当使用 Azure PowerShell 向 SQL VM 资源提供程序注册时, SQL 管理模式属性才可用。 Azure CLI 不支持设置 SQL 可管理性属性, 并始终在默认模式下向 SQL VM 资源提供程序注册-完全可管理性。

**是否可以向 SQL VM 资源提供程序注册而无需指定 SQL 许可证类型？**

否。 向 SQL VM RP 注册时, SQL 许可证类型不是可选属性。 当使用 AZ CLI 和 PowerShell 在所有可管理性模式 (无代理、轻型和完整) 中注册 SQL VM 资源提供程序时, 必须将 SQL 许可证类型设置为 PAYG 或 AHUB。

**是否可以从无代理模式将 SQL IaaS 扩展升级到完整模式？**

否。 在无代理模式下, 不能将 SQL 可管理性模式升级到完整或轻型。 这是 Windows Server 2008 的技术限制。

**能否将 SQL IaaS 扩展从轻型模式升级到完整模式？**

是的。 通过 PowerShell 或 Azure 门户支持从轻型升级到完整的 SQL 可管理性模式。并且需要重新启动 SQL Server。

**能否将 SQL IaaS 扩展从完整模式降级到无代理或轻型管理模式？**

否。 不支持降级 SQL IaaS 扩展可管理性模式。 SQL 可管理性模式无法从完整模式降级为轻型或无代理模式;并且不能从轻型模式降级到无代理模式。 从完全可管理性更改可管理性模式;删除 SQL IaaS 扩展;删除 Micorsoft SqlVirtualMachine 资源, 并向 SQL VM 资源提供程序重新注册 SQL Server VM。

**能否从 Azure 门户注册 SQL VM 资源提供程序？**

否。 Azure 门户中不提供注册到 SQL VM 资源提供程序的功能。 Azure CLI 或 PowerShell 支持在完全可管理性模式下注册 SQL VM 资源提供程序;在轻型或无代理可管理性模式下, 通过 SQL VM 资源提供程序注册并仅支持 Azure PowerShell Api。

**是否可以在安装 SQL Server 之前向 SQL VM 资源提供程序注册 VM？**

否。 VM 应该至少有一个 SQL 实例, 才能成功向 SQL VM 资源提供程序注册。 如果 VM 上没有 SQL 实例, 则新的 Micosoft SqlVirtualMachine 资源将处于 "失败" 状态。

**如果有多个 SQL 实例, 是否可以向 SQL VM 资源注册 VM？**

是的。 SQL VM 资源提供程序将只注册一个 SQL 实例。 SQL VM 资源提供程序将在多个实例的情况下注册默认的 SQL 实例。 如果没有默认实例, 则仅支持在轻型模式下进行注册。 若要从轻型升级到完全可管理性模式, 应存在默认的 SQL 实例, 或者 VM 应只有一个命名的 SQL 实例。

**是否可以向 SQL VM 资源提供程序注册 SQL Server 故障转移群集实例？**

是的。 Azure VM 上的 SQL FCI 实例可在轻型模式下通过 SQL VM 资源提供程序进行注册。 但是, 不能将 SQL FCI 实例升级到完全可管理性模式。

**如果配置了 Alwayson 可用性组, 是否可以向 SQL VM RP 注册 VM？**

是的。 如果加入 Always ON 可用性组配置, 则不会限制使用 SQL VM 资源提供程序在 Azure VM 中注册 SQL Server 实例。

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅以下文章： 

* [Windows VM 上的 SQL Server 概述](virtual-machines-windows-sql-server-iaas-overview.md)
* [Windows VM 上的 SQL Server 常见问题解答](virtual-machines-windows-sql-server-iaas-faq.md)
* [Windows VM 上的 SQL Server 定价指南](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Windows VM 上的 SQL Server 发行说明](virtual-machines-windows-sql-server-iaas-release-notes.md)
