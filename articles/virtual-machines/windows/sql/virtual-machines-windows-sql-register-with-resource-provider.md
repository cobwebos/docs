---
title: 向 SQL VM 资源提供程序注册
description: 向 SQL VM 资源提供程序注册 Azure SQL Server 虚拟机，以便为在 Azure Marketplace 外部署的 SQL Server Vm 启用功能，并提供相容性和改进的可管理性。
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
ms.date: 11/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: b59470a187fe060bd5e9a2c1bd84e63f598770df
ms.sourcegitcommit: 2f8ff235b1456ccfd527e07d55149e0c0f0647cc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/07/2020
ms.locfileid: "75690781"
---
# <a name="register-a-sql-server-virtual-machine-in-azure-with-the-sql-vm-resource-provider"></a>使用 SQL VM 资源提供程序在 Azure 中注册 SQL Server 虚拟机

本文介绍如何通过 SQL VM 资源提供程序在 Azure 中注册 SQL Server 虚拟机（VM）。 向资源提供程序注册会在你的订阅中创建**SQL 虚拟机**_资源_，该资源与虚拟机资源是单独的资源。 从资源提供程序中注销 SQL Server VM 将删除**SQL 虚拟机**_资源_，但不会删除实际的虚拟机。 

通过 Azure 门户部署 SQL Server VM Azure Marketplace 映像会自动将 SQL Server VM 注册到资源提供程序。 但是，如果选择在 Azure 虚拟机上自行安装 SQL Server，或通过自定义 VHD 来预配 Azure 虚拟机，则应向的资源提供程序注册 SQL Server VM：

- **功能优势**：向资源提供程序注册 SQL Server VM 可解锁[自动修补](virtual-machines-windows-sql-automated-patching.md)、[自动备份](virtual-machines-windows-sql-automated-backup-v2.md)以及监视和可管理性功能。 它还解锁了[授权](virtual-machines-windows-sql-ahb.md)和[版本](virtual-machines-windows-sql-change-edition.md)灵活性。 以前，这些功能仅可用于从 Azure Marketplace 部署的 SQL Server VM 映像。 

- **符合性**：注册到 SQL VM 资源提供程序提供了一种简化的方法，使您能够在产品条款中指定 Azure 混合权益已启用。 此过程无需管理每个资源的授权注册窗体。  

- **免费管理**：在所有三个可管理性模式下注册 SQL VM 资源提供程序完全免费。 资源提供程序或更改管理模式不会产生额外的费用。 

- **简化的许可证管理**：注册到 SQL VM 资源提供程序可简化 SQL Server 许可证管理，并使你能够使用[AZURE 门户](virtual-machines-windows-sql-manage-portal.md)、Az CLI 或 PowerShell 快速识别 Azure 混合权益启用 SQL Server vm： 

   # <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

   ```azurecli-interactive
   $vms = az sql vm list | ConvertFrom-Json
   $vms | Where-Object {$_.sqlServerLicenseType -eq "AHUB"}
   ```

   # <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

   ```powershell-interactive
   Get-AzSqlVM | Where-Object {$_.LicenseType -eq 'AHUB'}
   ```

   ---

若要利用 SQL VM 资源提供程序，必须先向[资源提供程序注册订阅](#register-subscription-with-rp)，这使资源提供程序能够在特定订阅中创建资源。

有关使用 SQL VM 资源提供程序的好处的详细信息，请观看此[channel9](https://channel9.msdn.com/Shows/Data-Exposed/Benefit-from-SQL-VM-Resource-Provider-when-self-installing-SQL-Server-on-Azure?WT.mc_id=dataexposed-c9-niner)视频： 

<iframe src="https://channel9.msdn.com/Shows/Data-Exposed/Benefit-from-SQL-VM-Resource-Provider-when-self-installing-SQL-Server-on-Azure/player" width="960" height="540" allowFullScreen frameBorder="0" title="在 Azure 上自行安装 SQL Server 时，从 SQL VM 资源提供程序受益-Microsoft 第9频道视频"></iframe>


## <a name="prerequisites"></a>必备组件

若要向资源提供程序注册你的 SQL Server VM，你将需要： 

- 一个 [Azure 订阅](https://azure.microsoft.com/free/)。
- 部署到公共或 Azure 政府云的 Azure 资源模型[SQL Server VM](virtual-machines-windows-portal-sql-server-provision.md) 。 
- [Azure CLI](/cli/azure/install-azure-cli)或[PowerShell](/powershell/azure/new-azureps-module-az)的最新版本。 

## <a name="management-modes"></a>管理模式

如果尚未安装[Sql IaaS 扩展](virtual-machines-windows-sql-server-agent-extension.md)，则向 sql VM 资源提供程序注册会在注册过程中指定的三个管理模式之一自动安装 SQL Server IaaS 扩展。 如果未指定管理模式，将在完全管理模式下安装 SQL IaaS 扩展。  

如果已手动安装 SQL IaaS 扩展，则该扩展已处于完全管理模式，并且以完整模式向资源提供程序注册不会重新启动 SQL Server 服务。

这三种管理模式如下：

- **轻型**模式不需要重新启动 SQL Server，但仅支持更改 SQL Server 的许可证类型和版本。 对于具有多个实例或参与故障转移群集实例（FCI）的 SQL Server Vm，请使用此选项。 使用轻型模式时，不会对内存或 CPU 产生任何影响，并且不会产生关联的开销。 建议先在轻型模式下注册 SQL Server VM，然后在计划的维护时段内升级到完整模式。  

- **完整**模式提供所有功能，但需要重新启动 SQL Server 和系统管理员权限。 这是手动安装 SQL IaaS 扩展时默认安装的选项。 使用它来管理包含单个实例的 SQL Server VM。 完整模式安装两个 windows 服务，这些服务对内存和 CPU 的影响最小-可通过任务管理器进行监视。 使用完全可管理性模式不会产生任何费用。 

- **NoAgent**模式专用于在 Windows Server 2008 上安装 SQL Server 2008 和 SQL Server 2008 R2。 使用 NoAgent 模式时，不会对内存或 CPU 产生任何影响。 使用 NoAgent 可管理性模式不会产生费用。 

你可以使用 PowerShell 查看 SQL Server IaaS 代理的当前模式： 

  ```powershell-interactive
  # Get the SqlVirtualMachine
  $sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
  $sqlvm.SqlManagementType
  ```

## <a name="register-subscription-with-rp"></a>向 RP 注册订阅

若要向 SQL VM 资源提供程序注册你的 SQL Server VM，必须先向资源提供程序注册订阅。 这使 SQL VM 资源提供程序能够在订阅中创建资源。  你可以使用 Azure 门户、Azure CLI 或 PowerShell 来执行此操作。

### <a name="azure-portal"></a>Azure 门户

1. 打开 Azure 门户并中转到 "**所有服务**"。 
1. 单击 "**订阅**" 并选择感兴趣的订阅。  
1. 在 "**订阅**" 页上，中转到 "**资源提供程序**"。 
1. 在筛选器中输入**sql**以打开与 sql 相关的资源提供程序。 
1. 根据所需的操作，为**SqlVirtualMachine**提供程序选择 "**注册**"、"**重新注册**" 或 "**注销**"。 

![修改提供程序](media/virtual-machines-windows-sql-ahb/select-resource-provider-sql.png)


### <a name="command-line"></a>命令行

使用 Az CLI 或 PowerShell 将 SQL VM 资源提供程序注册到你的 Azure 订阅。 

# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)

```azurecli-interactive
# Register the SQL VM resource provider to your subscription 
az provider register --namespace Microsoft.SqlVirtualMachine 
```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

```powershell-interactive
# Register the SQL VM resource provider to your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```

---

## <a name="register-sql-vm-with-rp"></a>向 RP 注册 SQL VM 

### <a name="lightweight-management-mode"></a>轻型管理模式

如果未在 VM 上安装[SQL Server IaaS 代理扩展](virtual-machines-windows-sql-server-agent-extension.md)，则建议在轻型模式下向 SQL VM 资源提供程序注册。 这会在[轻型模式下](#management-modes)安装 SQL IaaS 扩展，并阻止 SQL Server 服务重新启动。 然后，你可以随时升级到完整模式，但这样做将重新启动 SQL Server 服务，因此建议等待计划的维护时段。 需要将 SQL Server 许可证的类型作为即用即付（`PAYG`）提供，以按使用情况付费，或 Azure 混合权益（`AHUB`）来使用自己的许可证。

故障转移群集实例和多实例部署只能在轻型模式下的 SQL VM 资源提供程序中注册。 

# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)

用 Az CLI 在轻型模式下注册 SQL Server VM： 

  ```azurecli-interactive
  # Register Enterprise or Standard self-installed VM in Lightweight mode
  az sql vm create --name <vm_name> --resource-group <resource_group_name> --location <vm_location> --license-type PAYG 
  ```


# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

通过 PowerShell 在轻型模式下注册 SQL Server VM：  


  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  # Register SQL VM with 'Lightweight' SQL IaaS agent
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType PAYG -SqlManagementType LightWeight  
  ```

---

### <a name="full-management-mode"></a>完全管理模式


如果已手动将 SQL IaaS 扩展安装到 VM，则可以在完整模式下注册 SQL Server VM，而无需重新启动 SQL Server 服务。 **但是，如果尚未安装 SQL IaaS 扩展，则以完全模式注册将在完整模式下安装 SQL IaaS 扩展，并重新启动 SQL Server 服务。请谨慎操作。**

下面是以完整模式向 SQL VM 资源提供程序注册的代码片段。 若要以完全管理模式注册，请使用以下 PowerShell 命令：

  ```powershell-interactive
  # Get the existing  Compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
  # Register with SQL VM resource provider in full mode
  Update-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
  ```


### <a name="noagent-management-mode"></a>NoAgent 管理模式 

Windows Server 2008 上安装的 SQL Server 2008 和 2008 R2 可以在[NoAgent 模式下](#management-modes)注册到 SQL VM 资源提供程序。 此选项可确保符合性，并允许在 Azure 门户中监视 SQL Server VM 功能有限。

指定 `AHUB` 或 `PAYG` 作为**sqlLicenseType**，并 `SQL2008-WS2008` 或 `SQL2008R2-WS2008` 为**sqlImageOffer**。 

若要在 Windows Server 2008 实例上注册 SQL Server 2008 或 2008 R2 实例，请使用以下 Az CLI 或 PowerShell 代码片段： 


# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)

用 Az CLI 在 NoAgent 模式下注册 SQL Server VM： 

  ```azurecli-interactive
   az sql vm create -n sqlvm -g myresourcegroup -l eastus |
   --license-type PAYG --sql-mgmt-type NoAgent 
   --image-sku Enterprise --image-offer SQL2008-WS2008R2
 ```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

在 NoAgent 模式下，通过 PowerShell 注册 SQL Server VM： 


  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType PAYG -SqlManagementType NoAgent -Sku Standard -Offer SQL2008R2-WS2008
  ```

---

## <a name="upgrade-to-full-management-mode"></a>升级到完整管理模式 

如果 SQL Server 安装了*轻型*IaaS 扩展的 vm，则可以使用 Azure 门户、Az CLI 或 PowerShell 将模式升级为_完整_模式。 将操作系统升级到 Windows 2008 R2 及更高版本后，在_NoAgent_模式下 SQL Server vm 可以升级到_完整_版本。 不可能进行降级-若要执行此操作，你将需要从 SQL VM 资源提供程序中[注销](#unregister-vm-from-rp)SQL Server VM。 这样做会删除**SQL 虚拟机**_资源_，但不会删除实际的虚拟机。 

你可以使用 PowerShell 查看 SQL Server IaaS 代理的当前模式： 

  ```powershell-interactive
  # Get the SqlVirtualMachine
  $sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
  $sqlvm.SqlManagementType
  ```

若要将代理模式升级到完整模式： 


### <a name="azure-portal"></a>Azure 门户

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 中转到[SQL 虚拟机](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource)资源。 
1. 选择 SQL Server 虚拟机，然后选择 "**概述**"。 
1. 对于 NoAgent 或轻型 IaaS 模式下的 SQL Server Vm，请选择 " **SQL IaaS 扩展消息仅提供许可证类型" 和 "版本更新**"。

   ![用于从门户更改模式的选择](media/virtual-machines-windows-sql-server-agent-extension/change-sql-iaas-mode-portal.png)

1. 选中 "**我同意在虚拟机上重新启动 SQL Server 服务**" 复选框，然后选择 "**确认**" 将 IaaS 模式升级到 full。 

    ![确认在虚拟机上重新启动 SQL Server 服务的复选框](media/virtual-machines-windows-sql-server-agent-extension/enable-full-mode-iaas.png)

### <a name="command-line"></a>命令行

# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)

运行以下 Az CLI 代码片段：

  ```azurecli-interactive
  # Update to full mode
  az sql vm update --name <vm_name> --resource-group <resource_group_name> --sql-mgmt-type full  
  ```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

运行以下 PowerShell 代码片段：

  ```powershell-interactive
  # Get the existing  Compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>

  # Update to full mode
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
     -LicenseType PAYG -SqlManagementType Full
  ```

---

## <a name="verify-registration-status"></a>验证注册状态
可以通过使用 Azure 门户、Azure CLI 或 PowerShell 来验证是否已向 SQL VM 资源提供程序注册了 SQL Server VM。 

### <a name="azure-portal"></a>Azure 门户 

1. 登录 [Azure 门户](https://portal.azure.com)。 
1. 中转到[SQL Server 虚拟机](virtual-machines-windows-sql-manage-portal.md)。
1. 从列表中选择 SQL Server VM。 如果 SQL Server VM 未在此处列出，则可能尚未向 SQL VM 资源提供程序注册它。 
1. 查看 "**状态**" 下的值。 如果**状态**为 "**成功**"，则已成功向 SQL VM 资源提供程序注册了 SQL Server VM。 

![通过 SQL RP 注册验证状态](media/virtual-machines-windows-sql-register-with-rp/verify-registration-status.png)

### <a name="command-line"></a>命令行

使用 Az CLI 或 PowerShell 验证当前 SQL Server VM 注册状态。 如果注册成功，`ProvisioningState` 将显示 `Succeeded`。 

# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)


  ```azurecli-interactive
  az sql vm show -n <vm_name> -g <resource_group>
 ```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

  ```powershell-interactive
  Get-AzSqlVM -Name <vm_name> -ResourceGroupName <resource_group>
  ```

---

错误表明 SQL Server VM 尚未注册到资源提供程序。 


## <a name="unregister-vm-from-rp"></a>从 RP 注销 VM

若要使用 SQL VM 资源提供程序取消注册 SQL Server VM，请使用 Azure 门户或 Azure CLI 删除 SQL 虚拟机*资源*。 删除 SQL 虚拟机*资源*不会删除 SQL Server VM。 但是，请谨慎使用并严格执行步骤，因为在尝试删除*资源*时，可能会意外删除虚拟机。 

若要将管理模式从完全降级，必须在 SQL VM 资源提供程序中注销 SQL VM。 

### <a name="azure-portal"></a>Azure 门户

若要使用 Azure 门户向资源提供程序注销 SQL Server VM，请执行以下步骤：

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 导航到 "SQL Server VM" 资源。 
  
   ![SQL 虚拟机资源](media/virtual-machines-windows-sql-manage-portal/sql-vm-manage.png)

1. 选择“删除”。 

   ![删除 SQL VM 资源提供程序](media/virtual-machines-windows-sql-register-with-rp/delete-sql-vm-resource-provider.png)

1. 键入 SQL 虚拟机的名称，并**清除虚拟机旁边的复选框**。

   ![删除 SQL VM 资源提供程序](media/virtual-machines-windows-sql-register-with-rp/confirm-delete-of-resource-uncheck-box.png)

   >[!WARNING]
   > 如果未清除虚拟机名称旁的复选框，则将完全*删除*虚拟机。 清除该复选框可从资源提供程序中注销 SQL Server VM，但*不会删除实际的虚拟机*。 

1. 选择 "**删除**" 以确认删除 SQL 虚拟机*资源*，而不是 SQL Server 虚拟机。 

### <a name="command-line"></a>命令行

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)
若要使用 Azure CLI 从资源提供程序中注销 SQL Server 虚拟机，请使用[az SQL vm delete](/cli/azure/sql/vm?view=azure-cli-latest#az-sql-vm-delete)命令。 这会删除 SQL Server 虚拟机*资源*，但不会删除虚拟机。 


```azurecli-interactive
az sql vm delete 
  --name <SQL VM resource name> |
  --resource-group <Resource group name> |
  --yes 
```

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
若要使用 Azure CLI 从资源提供程序中注销 SQL Server 虚拟机，请使用[AzSqlVM](/powershell/module/az.sqlvirtualmachine/new-azsqlvm)命令。 这会删除 SQL Server 虚拟机*资源*，但不会删除虚拟机。 

```powershell-interactive
Remove-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name>
```

---

## <a name="limitations"></a>限制

SQL VM 资源提供程序仅支持：
- SQL Server 通过 Azure 资源管理器部署的 Vm。 不支持 SQL Server 通过经典模型部署的 Vm。 
- 将 Vm 部署到公共或 Azure 政府云 SQL Server。 不支持部署到其他私有云或政府云。 


## <a name="frequently-asked-questions"></a>常见问题 

**是否应在 Azure Marketplace 中注册从 SQL Server 映像预配的 SQL Server VM？**

不。 Microsoft 自动注册从 Azure Marketplace 中的 SQL Server 映像预配的 Vm。 仅当*未*从 Azure Marketplace 中的 SQL Server 映像预配 VM，并且 SQL Server 自安装时，才需要向 SQL VM 资源提供程序注册。

**SQL VM 资源提供程序是否适用于所有客户？** 

可以。 如果客户使用的是 Azure Marketplace 中的 SQL Server 映像，而不是自安装的 SQL Server，或者如果他们提供自定义 VHD，则应该向 SQL VM 资源提供程序注册其 SQL Server Vm。 所有类型的订阅（直接、企业协议和云解决方案提供商）拥有的 Vm 均可使用 SQL VM 资源提供程序进行注册。

**如果 SQL Server VM 已安装了 SQL Server IaaS 扩展，我应该向 SQL VM 资源提供程序注册吗？**

如果 SQL Server VM 是自行安装的，并且未从 Azure Marketplace 中的 SQL Server 映像进行预配，则应该向 SQL VM 资源提供程序注册，即使安装了 SQL Server IaaS 扩展。 使用 SQL VM 资源提供程序注册会创建 SqlVirtualMachines 类型的新资源。 安装 SQL Server IaaS 扩展不会创建该资源。

**向 SQL VM 资源提供程序注册时，默认的管理模式是什么？**

向 SQL VM 资源提供程序注册时的默认管理模式为*full*。 如果在向 SQL VM 资源提供程序注册时未设置 SQL Server 管理属性，则该模式将设置为完全可管理性，并且你的 SQL Server 服务将重新启动。 建议先向轻型模式下的 SQL VM 资源提供程序注册，然后在维护时段内升级到完整。 

**向 SQL VM 资源提供程序注册的先决条件是什么？**

在轻型模式或无代理模式下，不需要向 SQL VM 资源提供程序注册。 以完全模式向 SQL VM 资源提供程序注册的先决条件是在 VM 上安装了 SQL Server IaaS 扩展，否则 SQL Server 服务将重新启动。 

**如果未在 VM 上安装 SQL Server IaaS 扩展，则可以使用 SQL VM 资源提供程序注册吗？**

是的，如果未在 VM 上安装 SQL Server IaaS 扩展，则可以在轻型管理模式下向 SQL VM 资源提供程序注册。 在轻型模式下，SQL VM 资源提供程序将使用控制台应用程序来验证 SQL Server 实例的版本。 

在 SQL VM 资源提供程序中注册时，默认 SQL 管理模式已_满_。 如果在向 SQL VM 资源提供程序注册时未设置 SQL 管理属性，则该模式将设置为完全可管理性。 建议先向轻型模式下的 SQL VM 资源提供程序注册，然后在维护时段内升级到完整。 

**向 SQL VM 资源提供程序注册将在我的 VM 上安装代理？**

不。 使用 SQL VM 资源提供程序注册将仅创建新的元数据资源。 它不会在 VM 上安装代理。

仅在启用完全可管理性时，才需要 SQL Server IaaS 扩展。 将可管理性模式从轻型升级到 full 会安装 SQL Server IaaS 扩展，并将 SQL Server 重新启动。

**将向 VM 注册 SQL Server VM 资源提供程序重启 SQL Server？**

这取决于注册期间指定的模式。 如果指定了轻型模式或 NoAgent 模式，则 SQL Server 服务将不会重新启动。 但是，将管理模式指定为 "完全"，或将管理模式留空会在完全管理模式下安装 SQL IaaS 扩展，这将导致 SQL Server 服务重新启动。 

**向 SQL VM 资源提供程序注册时，轻量和无代理管理模式之间的区别是什么？** 

无-代理管理模式仅适用于 SQL Server 2008，Windows Server 2008 上 SQL Server 2008 R2。 对于这些版本，它是唯一可用的管理模式。 对于所有其他版本的 SQL Server，两种可用的可管理性模式为轻型和完整。 

无代理模式要求客户设置 SQL Server 版本和版本属性。 轻型模式将查询 VM 以查找 SQL Server 实例的版本。

**是否可以向 SQL VM 资源提供程序注册而不指定 SQL Server 许可证类型？**

不。 向 SQL VM 资源提供程序注册时，SQL Server 许可证类型不是可选的属性。 在所有可管理性模式（无代理、轻型和完整）中向 SQL VM 资源提供程序注册时，必须将 SQL Server 许可证类型设置为即用即付或 Azure 混合权益。

**是否可以将 SQL Server IaaS 扩展从无代理模式升级到完整模式？**

不。 在无代理模式下，将可管理性模式升级为 "完整" 或 "轻型" 不可用。 这是 Windows Server 2008 的技术限制。 需要首先将操作系统升级到 Windows Server 2008 R2 或更高版本，然后才能升级到完整管理模式。 

**是否可以将 SQL Server IaaS 扩展从轻型模式升级到完整模式？**

可以。 支持通过 PowerShell 或 Azure 门户将可管理性模式从轻型升级到完整。 它需要重新启动 SQL Server 服务。

**是否可以将 SQL Server IaaS 扩展从完整模式降级到无代理或轻型管理模式？**

不。 不支持降级 SQL Server IaaS 扩展可管理性模式。 可管理性模式无法从完整模式降级为轻型模式或无代理模式，并且无法从轻型模式降级到无代理模式。 

若要从完全可管理性更改可管理性模式，通过删除 SQL Server*资源*从 SQL Server 资源提供程序[取消](#unregister-vm-from-rp)注册 SQL Server 虚拟机，然后在不同的管理模式下再次向 SQL VM 资源提供程序重新注册该 SQL Server VM。

**能否从 Azure 门户注册 SQL VM 资源提供程序？**

不。 Azure 门户中不提供注册到 SQL VM 资源提供程序的功能。 仅 Azure CLI 或 PowerShell 支持在 SQL VM 资源提供程序中注册。 

**是否可以在安装 SQL Server 之前向 SQL VM 资源提供程序注册 VM？**

不。 VM 应该至少有一个 SQL Server 实例，才能成功注册到 SQL VM 资源提供程序。 如果 VM 上没有 SQL Server 实例，则新的 SqlVirtualMachine 资源将处于 "失败" 状态。

**如果有多个 SQL Server 实例，能否使用 SQL VM 资源提供程序注册 VM？**

可以。 SQL VM 资源提供程序将只注册一个 SQL Server 实例。 SQL VM 资源提供程序将在多个实例的情况下注册默认 SQL Server 实例。 如果没有默认实例，则仅支持在轻型模式下进行注册。 若要从轻型升级到完全可管理性模式，应存在默认 SQL Server 实例，或者 VM 应只有一个命名 SQL Server 实例。

**是否可以向 SQL VM 资源提供程序注册 SQL Server 故障转移群集实例？**

可以。 在 Azure VM 上 SQL Server 故障转移群集实例可在轻型模式下通过 SQL VM 资源提供程序进行注册。 但 SQL Server 故障转移群集实例不能升级到完全可管理性模式。

**如果配置了 Always On 可用性组，是否可以向 SQL VM 资源提供程序注册 VM？**

可以。 如果正在加入 Always On 的可用性组配置，则使用 SQL VM 资源提供程序在 Azure VM 上注册 SQL Server 实例没有任何限制。

**向 SQL VM 资源提供程序注册或升级到完全可管理性模式的成本是多少？**
无。 向 SQL VM 资源提供程序注册或使用三个可管理性模式中的任何一种，都不会产生任何费用。 用资源提供程序管理 SQL Server VM 完全免费。 

**使用不同的可管理性模式会对性能造成哪些影响？**
使用*NoAgent*和*轻量级*可管理性模式时，不会产生任何影响。 从安装到操作系统的两个服务使用*完全*可管理性模式时，影响最小。 可以通过任务管理器监视这些服务，并可以在内置 Windows 服务控制台中查看。 

这两个服务名称为：
- `SqlIaaSExtensionQuery` （显示名称-`Microsoft SQL Server IaaS Query Service`）
- `SQLIaaSExtension` （显示名称-`Microsoft SQL Server IaaS Agent`）


## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅以下文章： 

* [Windows VM 上的 SQL Server 概述](virtual-machines-windows-sql-server-iaas-overview.md)
* [Windows VM 上的 SQL Server 常见问题](virtual-machines-windows-sql-server-iaas-faq.md)
* [Windows VM 上 SQL Server 的定价指南](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Windows VM 上 SQL Server 的发行说明](virtual-machines-windows-sql-server-iaas-release-notes.md)
