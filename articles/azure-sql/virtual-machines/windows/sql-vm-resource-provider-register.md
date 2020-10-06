---
title: 注册到 SQL VM 资源提供程序
description: 向 SQL VM 资源提供程序注册 Azure SQL Server 虚拟机，以便为在 Azure Marketplace 外部部署的 SQL Server 虚拟机启用功能，并提供相容性和改进的可管理性。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 09/21/2020
ms.author: mathoma
ms.reviewer: jroth
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: b48f0429525822d09f08965128df0ceb1e32898a
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2020
ms.locfileid: "91761305"
---
# <a name="register-a-sql-server-vm-in-azure-with-the-sql-vm-resource-provider-rp"></a>使用 SQL VM 资源提供程序在 Azure 中注册 SQL Server VM (RP) 
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

本文介绍如何在 Azure 中通过 SQL VM 资源提供程序 (RP) 注册 SQL Server 虚拟机 (VM) 。 

本文介绍如何使用 SQL VM 资源提供程序注册单个 SQL Server VM。 或者，你可以 [自动](sql-vm-resource-provider-automatic-registration.md) 注册所有 SQL Server vm 或 [批量](sql-vm-resource-provider-bulk-register.md)注册。

## <a name="overview"></a>概述

注册到资源提供程序会在订阅中创建 SQL 虚拟机资源，这是与虚拟机资源不同的资源。 从资源提供程序中取消注册 SQL Server VM 会删除 SQL 虚拟机资源，但不会删除实际虚拟机。

通过 Azure 门户部署 SQL Server VM Azure 市场映像会自动将 SQL Server VM 注册到资源提供程序。 但是，如果选择在 Azure 虚拟机上自行安装 SQL Server，或通过自定义 VHD 预配 Azure 虚拟机，则应将 SQL Server VM 注册到资源提供程序以实现以下目的：

- 功能优势：将 SQL Server VM 注册到资源提供程序会解锁[自动修补](automated-patching.md)、[自动备份](automated-backup.md)以及监视和可管理性功能。 它还会解锁[许可](licensing-model-azure-hybrid-benefit-ahb-change.md)和[编辑](change-sql-server-edition.md)灵活性。 以前，这些功能只适用于从 Azure 市场部署的 SQL Server VM 映像。 

- **符合性**：注册到 SQL VM 资源提供程序提供了一种简化方法，用于满足在产品条款中指定的向 Microsoft 通知 Azure 混合权益已启用这一要求。 此过程无需为每个资源管理许可注册表单。  

- **免费管理**：在所有三个可管理性模式下注册 SQL VM 资源提供程序完全免费。 资源提供程序或更改管理模式不会产生相关联的额外成本。 

- 简化的许可证管理：注册到 SQL VM 资源提供程序可简化 SQL Server 许可证管理，并使你能够使用 [Azure 门户](manage-sql-vm-portal.md)、Azure CLI 或 PowerShell 快速识别启用了 Azure 混合权益的 SQL Server VM： 

   # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

   ```azurecli-interactive
   $vms = az sql vm list | ConvertFrom-Json
   $vms | Where-Object {$_.sqlServerLicenseType -eq "AHUB"}
   ```

   # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```powershell-interactive
   Get-AzSqlVM | Where-Object {$_.LicenseType -eq 'AHUB'}
   ```

   ---

若要利用 SQL VM 资源提供程序，必须先向 [资源提供程序注册订阅](#register-subscription-with-rp)，这使资源提供程序能够在特定订阅中创建资源。

## <a name="prerequisites"></a>先决条件

若要将 SQL Server VM 注册到资源提供程序，需要： 

- 一个 [Azure 订阅](https://azure.microsoft.com/free/)。
- 将[SQL Server](https://www.microsoft.com/sql-server/sql-server-downloads)部署到公共或 Azure 政府云的 Azure 资源模型[Windows 虚拟机](../../../virtual-machines/windows/quick-create-portal.md)。 
- 最新版本的 [Azure CLI](/cli/azure/install-azure-cli) 或 [PowerShell](/powershell/azure/new-azureps-module-az)。 

## <a name="management-modes"></a>管理模式

如果尚未安装 [SQL IaaS 扩展](sql-server-iaas-agent-extension-automate-management.md)，则注册到 SQL VM 资源提供程序会采用在注册过程中指定的三个管理模式之一自动安装 SQL Server IaaS 扩展。 如果未指定管理模式，则会在完整管理模式下安装 SQL IaaS 扩展。  

如果已手动安装 SQL IaaS 扩展，则该扩展已处于完整管理模式，在完整模式下注册到资源提供程序不会重启 SQL Server 服务。

这三种管理模式为：

- “轻型”模式不需要重启 SQL Server，但仅支持更改 SQL Server 的许可证类型和版本。 对于具有多个实例或参与故障转移群集实例 (FCI) 的 SQL Server VM，请使用此选项。 使用轻型模式时，不会对内存或 CPU 产生任何影响，并且没有关联成本。 建议先在轻型模式下注册 SQL Server VM，然后在计划性维护时段内升级到完整模式。  

- 完整模式提供所有功能，但需要 SQL Server 重启和系统管理员权限。 在手动安装 SQL IaaS 扩展时，这是默认安装的选项。 使用它管理具有单个实例的 SQL Server VM。 完整模式安装两个 Windows 服务，它们对内存和 CPU 的影响最小 - 可通过任务管理器进行监视。 使用完整可管理性模式时没有关联成本。 

- 无代理模式专用于在 Windows Server 2008 上安装的 SQL Server 2008 和 SQL Server 2008 R2。 使用无代理模式时，不会对内存或 CPU 产生任何影响。 使用无代理可管理性模式时没有关联成本。 

可以使用 PowerShell 查看 SQL Server IaaS 代理的当前模式： 

  ```powershell-interactive
  # Get the SqlVirtualMachine
  $sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
  $sqlvm.SqlManagementType
  ```

## <a name="register-subscription-with-rp"></a>将订阅注册到 RP

若要将 SQL Server VM 注册到 SQL VM 资源提供程序，必须首先将订阅注册到资源提供程序。 这使 SQL VM 资源提供程序能够在订阅中创建资源。  可以使用 Azure 门户、Azure CLI 或 PowerShell 执行此操作。

### <a name="azure-portal"></a>Azure 门户

1. 打开 Azure 门户，转到“所有服务”。 
1. 转到“订阅”，选择感兴趣的订阅。  
1. 在“订阅”页中，转到“资源提供程序” 。 
1. 在筛选器中输入“sql”，以便显示与 SQL 相关的资源提供程序。 
1. 根据所需操作为“Microsoft.SqlVirtualMachine”提供程序选择“注册”、“重新注册”或“取消注册”   。 

   ![修改提供程序](./media/sql-vm-resource-provider-register/select-resource-provider-sql.png)


### <a name="command-line"></a>命令行

使用 Azure CLI 或 PowerShell 将 SQL VM 资源提供程序注册到 Azure 订阅。 

# <a name="azure-cli"></a>[Azure CLI](#tab/bash)

```azurecli-interactive
# Register the SQL VM resource provider to your subscription 
az provider register --namespace Microsoft.SqlVirtualMachine 
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell-interactive
# Register the SQL VM resource provider to your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```

---

## <a name="register-with-rp"></a>向 RP 注册

### <a name="lightweight-management-mode"></a>轻型管理模式

如果未在虚拟机上安装 [SQL Server IaaS 代理扩展](sql-server-iaas-agent-extension-automate-management.md) ，则建议在轻型模式下向 SQL VM 资源提供程序注册。 这会在[轻型模式](#management-modes)下安装 SQL IaaS 扩展并防止 SQL Server 服务重启。 随后可以随时升级到完整模式，但这样做会重启 SQL Server 服务，因此建议等到计划性维护时段。 

提供 SQL Server 许可证类型，形式为即用即付（`PAYG`，用于按使用情况付费）、Azure 混合权益（`AHUB`，用于使用自己的许可证）或灾难恢复（`DR`，用于激活[免费 DR 副本许可证](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure)）。

故障转移群集实例和多实例部署只能在轻型模式下的 SQL VM 资源提供程序中注册。 

# <a name="azure-cli"></a>[Azure CLI](#tab/bash)

使用 Azure CLI 在轻型模式下注册 SQL Server VM： 

  ```azurecli-interactive
  # Register Enterprise or Standard self-installed VM in Lightweight mode
  az sql vm create --name <vm_name> --resource-group <resource_group_name> --location <vm_location> --license-type PAYG 
  ```


# <a name="powershell"></a>[PowerShell](#tab/powershell)

使用 PowerShell 在轻型模式下注册 SQL Server VM：  


  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  # Register SQL VM with 'Lightweight' SQL IaaS agent
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType PAYG -SqlManagementType LightWeight  
  ```

---

### <a name="full-management-mode"></a>完整管理模式


如果已将 SQL IaaS 扩展手动安装到虚拟机，则可以在完整模式下注册 SQL Server VM，而无需重新启动 SQL Server 服务。 但是，如果尚未安装 SQL IaaS 扩展，则在完整模式下注册将在完整模式下安装 SQL IaaS 扩展，并重启 SQL Server 服务。请谨慎继续。


若要在完整模式下直接注册 SQL Server VM（并可能重启 SQL Server 服务），请使用以下 PowerShell 命令： 

  ```powershell-interactive
  # Get the existing  Compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
  # Register with SQL VM resource provider in full mode
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
  ```

### <a name="noagent-management-mode"></a>无代理管理模式 

在 Windows Server 2008（非 R2）上安装的 SQL Server 2008 和 2008 R2 可以在[无代理模式](#management-modes)下注册到 SQL VM 资源提供程序。 此选项可确保符合性，并允许使用有限功能在 Azure 门户中监视 SQL Server VM。

将 sqlLicenseType 指定为 `AHUB`、`PAYG` 或 `DR`，并将 sqlImageOffer 指定为 `SQL2008-WS2008` 或 `SQL2008R2-WS2008` 。 

若要在 Windows Server 2008 实例上注册 SQL Server 2008 或 2008 R2，请使用以下 Azure CLI 或 PowerShell 代码片段： 


# <a name="azure-cli"></a>[Azure CLI](#tab/bash)

在 NoAgent 模式下向 Azure CLI 注册 SQL Server 2008 虚拟机： 

  ```azurecli-interactive
   az sql vm create -n sqlvm -g myresourcegroup -l eastus |
   --license-type PAYG --sql-mgmt-type NoAgent 
   --image-sku Enterprise --image-offer SQL2008-WS2008
 ```
 
 
在 NoAgent 模式下向 Azure CLI 注册 SQL Server 2008 R2 虚拟机： 

  ```azurecli-interactive
   az sql vm create -n sqlvm -g myresourcegroup -l eastus |
   --license-type PAYG --sql-mgmt-type NoAgent 
   --image-sku Enterprise --image-offer SQL2008R2-WS2008
 ```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

在 NoAgent 模式下将 SQL Server 2008 虚拟机注册到 PowerShell： 


  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType PAYG -SqlManagementType NoAgent -Sku Standard -Offer SQL2008-WS2008
  ```
  
  在 NoAgent 模式下将 SQL Server 2008 R2 虚拟机注册到 PowerShell： 


  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType PAYG -SqlManagementType NoAgent -Sku Standard -Offer SQL2008R2-WS2008
  ```

---

## <a name="upgrade-to-full"></a>升级到完整  

安装了轻型 IaaS 扩展的 SQL Server VM 可以使用 Azure 门户、Azure CLI 或 PowerShell 将模式升级为完整模式。 在 OS 升级到 Windows 2008 R2 及更高版本之后，无代理模式下的 SQL Server VM 可以升级到完整模式 。 无法进行降级 - 若要执行此操作，需要 从 SQL VM 资源提供程序[取消注册](#unregister-from-rp) SQL Server VM。 这样做会删除 SQL 虚拟机资源，但不会删除实际虚拟机。 

可以使用 PowerShell 查看 SQL Server IaaS 代理的当前模式： 

  ```powershell-interactive
  # Get the SqlVirtualMachine
  $sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
  $sqlvm.SqlManagementType
  ```

若要将代理模式升级到完整模式，请执行以下操作： 


### <a name="azure-portal"></a>Azure 门户

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 转到 [SQL 虚拟机](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource)资源。 
1. 选择 SQL Server VM，然后选择 " **概述**"。 
1. 对于具有无代理或轻型 IaaS 模式的 SQL Server VM，请选择“SQL IaaS 扩展仅提供许可证类型和版本更新”消息。

   ![用于从门户更改模式的选项](./media/sql-vm-resource-provider-register/change-sql-iaas-mode-portal.png)

1. 选中“我同意重启虚拟机上的 SQL Server 服务”复选框，然后选择“确认”以将 IaaS 模式升级到完整模式 。 

    ![用于同意重启虚拟机上的 SQL Server 服务的复选框](./media/sql-vm-resource-provider-register/enable-full-mode-iaas.png)

### <a name="command-line"></a>命令行

# <a name="azure-cli"></a>[Azure CLI](#tab/bash)

运行以下 Azure CLI 代码片段：

  ```azurecli-interactive
  # Update to full mode
  az sql vm update --name <vm_name> --resource-group <resource_group_name> --sql-mgmt-type full  
  ```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

运行以下 PowerShell 代码片段：

  ```powershell-interactive
  # Get the existing  Compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
  # Register with SQL VM resource provider in full mode
  Update-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
  ```

---

## <a name="verify-registration-status"></a>验证注册状态
可以使用 Azure 门户、Azure CLI 或 PowerShell 验证是否已将 SQL Server VM 注册到 SQL VM 资源提供程序。 

### <a name="azure-portal"></a>Azure 门户 

1. 登录到 [Azure 门户](https://portal.azure.com)。 
1. 中转到 [SQL Server vm](manage-sql-vm-portal.md)。
1. 从列表中选择 SQL Server VM。 如果 SQL Server VM 未在此处列出，则可能尚未注册到 SQL VM 资源提供程序。 
1. 查看“状态”下的值。 如果“状态”为“成功”，则 SQL Server VM 已成功注册到 SQL VM 资源提供程序 。 

   ![通过 SQL RP 注册验证状态](./media/sql-vm-resource-provider-register/verify-registration-status.png)

### <a name="command-line"></a>命令行

使用 Azure CLI 或 PowerShell 验证当前 SQL Server VM 注册状态。 如果注册成功，`ProvisioningState` 将显示 `Succeeded`。 

# <a name="azure-cli"></a>[Azure CLI](#tab/bash)


  ```azurecli-interactive
  az sql vm show -n <vm_name> -g <resource_group>
 ```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

  ```powershell-interactive
  Get-AzSqlVM -Name <vm_name> -ResourceGroupName <resource_group>
  ```

---

错误表明 SQL Server VM 尚未注册到资源提供程序。 


## <a name="unregister-from-rp"></a>从 RP 注销

若要使用 SQL VM 资源提供程序取消注册 SQL Server VM，请使用 Azure 门户或 Azure CLI 删除 SQL 虚拟机 *资源* 。 删除 SQL 虚拟机 *资源* 不会删除 SQL Server VM。 但是，请小心谨慎并仔细执行以下步骤，因为在尝试删除资源时，可能会意外删除虚拟机。 

需要在 SQL VM 资源提供程序中注销 SQL 虚拟机，以使管理模式完全降级。 

### <a name="azure-portal"></a>Azure 门户

若要使用 Azure 门户从资源提供程序取消注册 SQL Server VM，请执行以下步骤：

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 导航到 SQL VM 资源。 
  
   ![SQL 虚拟机资源](./media/sql-vm-resource-provider-register/sql-vm-manage.png)

1. 选择“删除”。 

   ![在顶部导航栏中选择 "删除"](./media/sql-vm-resource-provider-register/delete-sql-vm-resource-provider.png)

1. 键入 SQL 虚拟机的名称，并 **清除该虚拟机旁边的复选框**。

   ![取消选中 VM 以阻止删除实际虚拟机，然后选择 "删除" 以继续删除 SQL VM 资源](./media/sql-vm-resource-provider-register/confirm-delete-of-resource-uncheck-box.png)

   >[!WARNING]
   > 如果未能清除虚拟机名称的复选框，会导致彻底删除虚拟机。 清除该复选框可从资源提供程序取消注册 SQL Server VM，但不会删除实际虚拟机。 

1. 选择 " **删除** " 以确认删除 SQL 虚拟机 *资源*，而不是 SQL Server VM。 

### <a name="command-line"></a>命令行

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
若要使用 Azure CLI 从资源提供程序中注销 SQL Server VM，请使用 [az SQL VM delete](/cli/azure/sql/vm?view=azure-cli-latest&preserve-view=true#az-sql-vm-delete) 命令。 这会删除 SQL Server VM *资源* ，但不会删除虚拟机。 


```azurecli-interactive
az sql vm delete 
  --name <SQL VM resource name> |
  --resource-group <Resource group name> |
  --yes 
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
若要使用 PowerShell 从资源提供程序中注销 SQL Server VM，请使用 [AzSqlVM](/powershell/module/az.sqlvirtualmachine/remove-azsqlvm)命令。 这会删除 SQL Server VM *资源* ，但不会删除虚拟机。 

```powershell-interactive
Remove-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name>
```

---

## <a name="limitations"></a>限制

SQL VM 资源提供程序仅支持：
- 通过 Azure 资源管理器部署的 SQL Server VM。 不支持通过经典模型部署的 SQL Server VM。 
- 部署到公有云或 Azure 政府云的 SQL Server VM。 不支持部署到其他私有云或政府云。 


## <a name="frequently-asked-questions"></a>常见问题 

是否应从 Azure 市场中的 SQL Server 映像预配的 SQL Server VM？

不是。 Microsoft 会自动注册从 Azure 市场中的 SQL Server 映像预配的 VM。 仅当 VM 不是从 Azure 市场中的 SQL Server 映像预配，并且 SQL Server 是自行安装时，才需要注册到 SQL VM 资源提供程序注册。

**SQL VM 资源提供程序是否适用于所有客户？** 

是的。 如果客户不使用 Azure 市场中的 SQL Server 映像，而是自行安装的 SQL Server，或者如果他们提供自定义 VHD，则应该将其 SQL Server VM 注册到 SQL VM 资源提供程序。 所有类型的订阅（直接、企业协议和云解决方案提供商）拥有的 VM 都可以注册到 SQL VM 资源提供程序。

如果 SQL Server VM 已安装了 SQL Server IaaS 扩展，我是否应注册到 SQL VM 资源提供程序？

如果 SQL Server VM 是自行安装，并且不是从 Azure 市场中的 SQL Server 映像进行预配，则应注册到 SQL VM 资源提供程序，即使安装了 SQL Server IaaS 扩展也是如此。 使用 SQL VM 资源提供程序注册会创建 SqlVirtualMachine 类型的新资源。 安装 SQL Server IaaS 扩展不会创建该资源。

注册到 SQL VM 资源提供程序时，默认管理模式是什么？

注册到 SQL VM 资源提供程序时，默认管理模式是完整模式。 如果在注册到 SQL VM 资源提供程序时未设置 SQL Server 管理属性，则模式将设置为完整可管理性，并且 SQL Server 服务将重启。 建议先在轻型模式下注册到 SQL VM 资源提供程序，然后在维护时段内升级到完整模式。 

注册到 SQL VM 资源提供程序的先决条件是什么？

在轻型模式或无代理模式下，注册到 SQL VM 资源提供程序没有先决条件。 在完整模式下注册到 SQL VM 资源提供程序的先决条件是在 VM 上安装了 SQL Server IaaS 扩展，否则 SQL Server 服务将重启。 

如果未在 VM 上安装 SQL Server IaaS 扩展，我是否可以注册到 SQL VM 资源提供程序？

可以，如果未在 VM 上安装 SQL Server IaaS 扩展，则可以在轻型管理模式下注册到 SQL VM 资源提供程序。 在轻型模式下，SQL VM 资源提供程序将使用控制台应用来验证 SQL Server 实例的版本。 

注册到 SQL VM 资源提供程序时，默认 SQL 管理模式是完整模式。 如果在注册到 SQL VM 资源提供程序时未设置 SQL 管理属性，则该模式将设置为完整可管理性。 建议先在轻型模式下注册到 SQL VM 资源提供程序，然后在维护时段内升级到完整模式。 

注册到 SQL VM 资源提供程序是否会在我的 VM 上安装代理？

是，使用 SQL VM 资源提供程序注册将在 VM 上安装代理。

SQL Server IaaS 扩展依赖于代理来查询 SQL Server 的元数据。 仅当在 NoAgent 模式下注册 SQL VM 资源提供程序时，才会安装代理。

**是否向 VM 上的 SQL VM 资源提供程序重启 SQL Server 注册？**

这取决于在注册期间指定的模式。 如果指定了轻型模式或无代理模式，则 SQL Server 服务不会重启。 但是，将管理模式指定为完整模式，或将管理模式留空会在完整管理模式下安装 SQL IaaS 扩展，这会导致 SQL Server 服务重启。 

注册到 SQL VM 资源提供程序时，轻型与无代理管理模式之间的区别是什么？ 

无代理管理模式仅适用于 Windows Server 2008 上的 SQL Server 2008 和 SQL Server 2008 R2。 对于这些版本，它是唯一可用的管理模式。 对于所有其他版本的 SQL Server，两种可用的可管理性模式是轻型和完整模式。 

无代理模式要求客户设置 SQL Server 版本属性。 轻型模式会查询 VM 以查找 SQL Server 实例的版本。

是否可以注册到 SQL VM 资源提供程序而不指定 SQL Server 许可证类型？

不是。 注册到 SQL VM 资源提供程序注册，SQL Server 许可证类型不是可选属性。 在所有可管理性模式（无代理、轻型和完整）下注册到 SQL VM 资源提供程序时，都必须将 SQL Server 许可证类型设置为即用即付或 Azure 混合权益。

是否可以将 SQL Server IaaS 扩展从无代理模式升级到完整模式？

不是。 对于无代理模式，无法将可管理性模式升级到完整或轻型模式。 这是 Windows Server 2008 的技术限制。 需要首先将 OS 升级到 Windows Server 2008 R2 或更高版本，然后才能升级到完整管理模式。 

是否可以将 SQL Server IaaS 扩展从轻型模式升级到完整模式？

是的。 支持通过 PowerShell 或 Azure 门户将可管理性模式从轻型升级到完整。 这需要重启 SQL Server 服务。

是否可以将 SQL Server IaaS 扩展从完整模式降级到无代理或轻型管理模式？

不是。 不支持降级 SQL Server IaaS 扩展可管理性模式。 可管理性模式无法从完整模式降级到轻型模式或无代理模式，并且无法从轻型模式降级到无代理模式。 

若要从完全可管理性更改可管理性模式，请通过删除 SQL Server*资源*从 sql vm 资源提供程序中[注销](#unregister-from-rp)SQL Server VM，然后在不同的管理模式下再次向 sql vm 资源提供程序重新注册 SQL Server VM。

是否可以从 Azure 门户注册到 SQL VM 资源提供程序？

不是。 无法在 Azure 门户中注册到 SQL VM 资源提供程序能。 仅支持使用 Azure CLI 或 PowerShell 注册到 SQL VM 资源提供程序。 

是否可以在安装 SQL Server 之前将 VM 注册到 SQL VM 资源提供程序？

不是。 VM 应该至少有一个 SQL Server（数据库引擎）实例才能成功注册到 SQL VM 资源提供程序。 如果 VM 上没有 SQL Server 实例，则新的 Microsoft.SqlVirtualMachine 资源会处于失败状态。

如果有多个 SQL Server 实例，是否可以将 VM 注册到 SQL VM 资源提供程序？

是的。 SQL VM 资源提供程序将只注册一个 SQL Server（数据库引擎）实例。 在多个实例的情况下，SQL VM 资源提供程序将注册默认 SQL Server 实例。 如果没有默认实例，则仅支持在轻型模式下注册。 若要从轻型升级到完整可管理性模式，应存在默认 SQL Server 实例，或者 VM 应只有一个命名 SQL Server 实例。

是否可以将 SQL Server 故障转移群集实例注册到 SQL VM 资源提供程序？

是的。 Azure VM 上的 SQL Server 故障转移群集实例只能在轻型模式下注册到 SQL VM 资源提供程序。 但是，SQL Server 故障转移群集实例无法升级到完整可管理性模式。

如果配置了 Always On 可用性组，是否可以将 VM 注册到 SQL VM 资源提供程序？

是的。 如果在参与 Always On 可用性组配置，则将 Azure VM 上的 SQL Server 实例注册到 SQL VM 资源提供程序时没有任何限制。

注册到 SQL VM 资源提供程序或升级到完整可管理性模式的成本是多少？
无。 注册到 SQL VM 资源提供程序或使用三种可管理性模式中的任何一种都没有关联费用。 使用资源提供程序管理 SQL Server VM 完全免费。 

使用不同的可管理性模式会产生哪些性能影响？
使用无代理和轻型可管理性模式时，不会产生任何影响 。 从安装到 OS 的两个服务使用完整可管理性模式时，影响最小。 可以通过任务管理器监视这些服务，并可以在内置 Windows 服务控制台中查看。 

这两个服务名称为：
- `SqlIaaSExtensionQuery`（显示名称 - `Microsoft SQL Server IaaS Query Service`）
- `SQLIaaSExtension`（显示名称 - `Microsoft SQL Server IaaS Agent`）


## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅以下文章： 

* [Windows VM 上的 SQL Server 概述](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Windows VM 上的 SQL Server 常见问题解答](frequently-asked-questions-faq.md)  
* [Windows VM 上的 SQL Server 定价指南](pricing-guidance.md)
* [Windows VM 上的 SQL Server 发行说明](../../database/doc-changes-updates-release-notes.md)
