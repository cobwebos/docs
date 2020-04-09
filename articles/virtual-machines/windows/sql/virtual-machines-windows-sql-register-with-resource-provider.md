---
title: 注册到 SQL VM 资源提供程序
description: 将 Azure SQL Server 虚拟机注册到 SQL VM 资源提供程序，以启用 Azure 应用商店之外部署的 SQL Server VM 的功能，以及合规性以及增强的可管理性。
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
ms.openlocfilehash: d9c1cff53d5d0f0385d3d61938c7fb6309efb7b1
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985382"
---
# <a name="register-a-sql-server-virtual-machine-in-azure-with-the-sql-vm-resource-provider"></a>在 Azure 中向 SQL VM 资源提供程序注册 SQL Server 虚拟机

本文介绍如何在 Azure 中向 SQL VM 资源提供程序注册 SQL Server 虚拟机 （VM）。 向资源提供程序注册会在订阅中创建**SQL 虚拟机**_资源_，这是与虚拟机资源分离的资源。 从资源提供程序取消注册 SQL Server VM 将删除**SQL 虚拟机**_资源_，但不会删除实际虚拟机。 

通过 Azure 门户部署 SQL Server VM Azure 应用商店映像会自动将 SQL Server VM 注册到资源提供程序。 但是，如果选择在 Azure 虚拟机上自行安装 SQL Server，或者从自定义 VHD 预配 Azure 虚拟机，则应向资源提供程序注册 SQL Server VM，以便：

- **功能优势**：向资源提供商注册 SQL Server VM 可解锁[自动修补](virtual-machines-windows-sql-automated-patching.md)、[自动备份](virtual-machines-windows-sql-automated-backup-v2.md)以及监视和可管理功能。 它还可解锁[许可](virtual-machines-windows-sql-ahb.md)和[版本](virtual-machines-windows-sql-change-edition.md)灵活性。 以前，这些功能仅适用于从 Azure 应用商店部署的 SQL Server VM 映像。 

- **合规性**：向 SQL VM 资源提供程序注册提供了一种简化的方法，用于满足通知 Microsoft Azure 混合权益已启用的要求，如产品术语中所述。 此过程无需管理每个资源的许可注册表单。  

- **免费管理**：在所有三种可管理模式下向 SQL VM 资源提供程序注册是完全免费的。 与资源提供程序或更改管理模式没有关联的额外费用。 

- **简化许可证管理**：与 SQL VM 资源提供程序注册可简化 SQL Server 许可证管理，并允许您使用[Azure 门户](virtual-machines-windows-sql-manage-portal.md)、Az CLI 或 PowerShell 快速识别启用 Azure 混合权益的 SQL Server VM： 

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

要利用 SQL VM 资源提供程序，必须首先[向资源提供程序注册订阅](#register-subscription-with-rp)，使资源提供程序能够创建该特定订阅中的资源。

有关使用 SQL VM 资源提供程序的好处的详细信息，请观看此[通道9](https://channel9.msdn.com/Shows/Data-Exposed/Benefit-from-SQL-VM-Resource-Provider-when-self-installing-SQL-Server-on-Azure?WT.mc_id=dataexposed-c9-niner)视频： 

<iframe src="https://channel9.msdn.com/Shows/Data-Exposed/Benefit-from-SQL-VM-Resource-Provider-when-self-installing-SQL-Server-on-Azure/player" width="960" height="540" allowFullScreen frameBorder="0" title="在 Azure 上自行安装 SQL 服务器时，从 SQL VM 资源提供程序中获益 - Microsoft 通道 9 视频"></iframe>


## <a name="prerequisites"></a>先决条件

要向资源提供程序注册 SQL Server VM，您需要： 

- [Azure 订阅](https://azure.microsoft.com/free/)。
- 部署到公共或 Azure 政府云的 Azure 资源模型[SQL Server VM。](virtual-machines-windows-portal-sql-server-provision.md) 
- 最新版本的[Azure CLI](/cli/azure/install-azure-cli)或[PowerShell](/powershell/azure/new-azureps-module-az)。 

## <a name="management-modes"></a>管理模式

如果[SQL IaaS 扩展](virtual-machines-windows-sql-server-agent-extension.md)尚未安装，则向 SQL VM 资源提供程序注册，在注册过程中指定的三种管理模式下自动安装 SQL Server IaaS 扩展。 未指定管理模式将在完全管理模式下安装 SQL IaaS 扩展。  

如果 SQL IaaS 扩展已手动安装，则该扩展已处于完全管理模式，并且以全模式向资源提供程序注册将不会重新启动 SQL Server 服务。

三种管理模式是：

- **轻量级**模式不需要重新启动 SQL Server，但仅支持更改 SQL Server 的许可证类型和版本。 对具有多个实例的 SQL Server VM 或参与故障转移群集实例 （FCI） 的 SQL Server VM 使用此选项。 使用轻量模式时对内存或 CPU 没有影响，并且没有关联的成本。 建议先以轻量级模式注册 SQL Server VM，然后在计划维护时段内升级到"完整"模式。  

- **完全**模式提供所有功能，但需要重新启动 SQL Server 和系统管理员权限。 这是默认情况下手动安装 SQL IaaS 扩展时安装的选项。 使用它使用单个实例管理 SQL Server VM。 完全模式安装两个窗口服务，对内存和 CPU 的影响最小 - 可通过任务管理器监视这些服务。 使用完全可管理性模式无需相关成本。 

- **NoAgent**模式专用于 Windows Server 2008 上安装的 SQL Server 2008 和 SQL Server 2008 R2。 使用 NoAgent 模式时，对内存或 CPU 没有影响。 使用 NoAgent 可管理性模式无需相关成本。 

您可以使用 PowerShell 查看 SQL Server IaaS 代理的当前模式： 

  ```powershell-interactive
  # Get the SqlVirtualMachine
  $sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
  $sqlvm.SqlManagementType
  ```

## <a name="register-subscription-with-rp"></a>使用 RP 注册订阅

要向 SQL VM 资源提供程序注册 SQL Server VM，必须首先向资源提供程序注册订阅。 这使 SQL VM 资源提供程序能够在订阅中创建资源。  可以使用 Azure 门户、Azure CLI 或 PowerShell 执行此操作。

### <a name="azure-portal"></a>Azure 门户

1. 打开 Azure 门户并转到**所有服务**。 
1. 转到 **"订阅"** 并选择感兴趣的订阅。  
1. 在 **"订阅"** 页上，转到**资源提供程序**。 
1. 在筛选器中输入**sql**以启动与 SQL 相关的资源提供程序。 
1. 选择**Microsoft.SqlVirtualMachine**提供程序的**注册**、**重新注册**或**取消注册**，具体取决于所需的操作。 

![修改提供程序](media/virtual-machines-windows-sql-ahb/select-resource-provider-sql.png)


### <a name="command-line"></a>命令行

使用 Az CLI 或 PowerShell 将 SQL VM 资源提供程序注册到 Azure 订阅。 

# <a name="az-cli"></a>[AZ CLI](#tab/bash)

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

## <a name="register-sql-vm-with-rp"></a>将 SQL VM 注册为 RP 

### <a name="lightweight-management-mode"></a>轻量级管理模式

如果[SQL Server IaaS 代理扩展](virtual-machines-windows-sql-server-agent-extension.md)尚未安装在 VM 上，则建议以轻量模式向 SQL VM 资源提供程序注册。 这将在[轻量级模式下](#management-modes)安装 SQL IaaS 扩展，并阻止 SQL Server 服务重新启动。 然后，您可以随时升级到完整模式，但这样做将重新启动 SQL Server 服务，因此建议等到计划维护窗口。 

将`PAYG`SQL Server 许可证类型作为即用即付 （） 支付每个用时费用，Azure`AHUB`混合权益 （） 使用您自己的许可证，或`DR`灾难恢复 （） 来激活[免费的 DR 副本许可证](virtual-machines-windows-sql-high-availability-dr.md#free-dr-replica-in-azure)。

故障转移群集实例和多实例部署只能在轻量级模式下注册到 SQL VM 资源提供程序。 

# <a name="az-cli"></a>[AZ CLI](#tab/bash)

使用 Az CLI 以轻量级模式注册 SQL Server VM： 

  ```azurecli-interactive
  # Register Enterprise or Standard self-installed VM in Lightweight mode
  az sql vm create --name <vm_name> --resource-group <resource_group_name> --location <vm_location> --license-type PAYG 
  ```


# <a name="powershell"></a>[PowerShell](#tab/powershell)

使用 PowerShell 以轻量级模式注册 SQL Server VM：  


  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  # Register SQL VM with 'Lightweight' SQL IaaS agent
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType PAYG -SqlManagementType LightWeight  
  ```

---

### <a name="full-management-mode"></a>完全管理模式


如果 SQL IaaS 扩展已手动安装到 VM 中，则可以以完全模式注册 SQL Server VM，而无需重新启动 SQL Server 服务。 **但是，如果 SQL IaaS 扩展尚未安装，则完全模式下注册将以完整模式安装 SQL IaaS 扩展并重新启动 SQL Server 服务。请谨慎行事。**


要直接以完整模式注册 SQL Server VM（并可能重新启动 SQL Server 服务），请使用以下 PowerShell 命令： 

  ```powershell-interactive
  # Get the existing  Compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
  # Register with SQL VM resource provider in full mode
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
  ```

### <a name="noagent-management-mode"></a>无代理管理模式 

SQL Server 2008 和 2008 R2 安装在 Windows Server 2008 （_而不是 R2）_ 上，可以在[NoAgent 模式下](#management-modes)注册到 SQL VM 资源提供程序。 此选项可确保合规性，并允许在 Azure 门户中监视 SQL Server VM，功能有限。

指定`AHUB`、`PAYG`或`DR`指定**sqlLicenseType**，或`SQL2008-WS2008``SQL2008R2-WS2008`指定为**sqlImageOffer**。 

要在 Windows Server 2008 实例上注册 SQL Server 2008 或 2008 R2，请使用以下 Az CLI 或 PowerShell 代码段： 


# <a name="az-cli"></a>[AZ CLI](#tab/bash)

在无代理模式下将 SQL Server 2008 VM 与 Az CLI 注册： 

  ```azurecli-interactive
   az sql vm create -n sqlvm -g myresourcegroup -l eastus |
   --license-type PAYG --sql-mgmt-type NoAgent 
   --image-sku Enterprise --image-offer SQL2008-WS2008
 ```
 
 
将 SQL Server 2008 R2 VM 以 NoAgent 模式与 Az CLI 注册： 

  ```azurecli-interactive
   az sql vm create -n sqlvm -g myresourcegroup -l eastus |
   --license-type PAYG --sql-mgmt-type NoAgent 
   --image-sku Enterprise --image-offer SQL2008R2-WS2008
 ```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

将 SQL Server 2008 VM 注册为无代理模式，使用 PowerShell： 


  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType PAYG -SqlManagementType NoAgent -Sku Standard -Offer SQL2008-WS2008
  ```
  
  将 SQL Server 2008 R2 VM 注册为无代理模式，使用 PowerShell： 


  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType PAYG -SqlManagementType NoAgent -Sku Standard -Offer SQL2008R2-WS2008
  ```

---

## <a name="upgrade-to-full-management-mode"></a>升级到完全管理模式 

安装了*轻量级*IaaS 扩展的 SQL Server VM 可以使用 Azure 门户、Az CLI 或 PowerShell 将模式升级到_完全_。 在操作系统升级到 Windows 2008 R2 及以上后，_处于 NoAgent_模式下的 SQL Server VM 可以升级到_完整_。 无法降级 - 为此，您需要从 SQL VM 资源提供程序[中取消注册](#unregister-vm-from-rp)SQL Server VM。 这样做将删除**SQL 虚拟机**_资源_，但不会删除实际虚拟机。 

您可以使用 PowerShell 查看 SQL Server IaaS 代理的当前模式： 

  ```powershell-interactive
  # Get the SqlVirtualMachine
  $sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
  $sqlvm.SqlManagementType
  ```

要将代理模式升级到完整： 


### <a name="azure-portal"></a>Azure 门户

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 转到[SQL 虚拟机](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource)资源。 
1. 选择 SQL Server 虚拟机，然后选择 **"概述**"。 
1. 对于具有 NoAgent 或轻量级 IaaS 模式的 SQL Server VM，选择**SQL IaaS 扩展消息提供的"仅许可证类型和版本更新**"。

   ![从门户更改模式的选择](media/virtual-machines-windows-sql-server-agent-extension/change-sql-iaas-mode-portal.png)

1. 选中"**我同意重新启动虚拟机上的 SQL Server 服务**"复选框，然后选择"**确认"** 以将 IaaS 模式升级到完整。 

    ![用于同意在虚拟机上重新启动 SQL Server 服务的复选框](media/virtual-machines-windows-sql-server-agent-extension/enable-full-mode-iaas.png)

### <a name="command-line"></a>命令行

# <a name="az-cli"></a>[AZ CLI](#tab/bash)

运行以下 Az CLI 代码段：

  ```azurecli-interactive
  # Update to full mode
  az sql vm update --name <vm_name> --resource-group <resource_group_name> --sql-mgmt-type full  
  ```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

运行以下 PowerShell 代码段：

  ```powershell-interactive
  # Get the existing  Compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
  # Register with SQL VM resource provider in full mode
  Update-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
  ```

---

## <a name="verify-registration-status"></a>验证注册状态
可以使用 Azure 门户、Azure CLI 或 PowerShell 验证 SQL Server VM 是否已注册到 SQL VM 资源提供程序。 

### <a name="azure-portal"></a>Azure 门户 

1. 登录 [Azure 门户](https://portal.azure.com)。 
1. 转到[SQL Server 虚拟机](virtual-machines-windows-sql-manage-portal.md)。
1. 从列表中选择 SQL Server VM。 如果此处未列出 SQL Server VM，则可能尚未向 SQL VM 资源提供程序注册。 
1. 查看**状态**下的值。 如果**状态****成功**，则 SQL Server VM 已成功注册到 SQL VM 资源提供程序。 

![使用 SQL RP 注册验证状态](media/virtual-machines-windows-sql-register-with-rp/verify-registration-status.png)

### <a name="command-line"></a>命令行

使用 Az CLI 或 PowerShell 验证当前 SQL Server VM 注册状态。 `ProvisioningState`将显示`Succeeded`注册是否成功。 

# <a name="az-cli"></a>[AZ CLI](#tab/bash)


  ```azurecli-interactive
  az sql vm show -n <vm_name> -g <resource_group>
 ```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

  ```powershell-interactive
  Get-AzSqlVM -Name <vm_name> -ResourceGroupName <resource_group>
  ```

---

错误表示 SQL Server VM 尚未向资源提供程序注册。 


## <a name="unregister-vm-from-rp"></a>从 RP 取消注册 VM

要将 SQL Server VM 与 SQL VM 资源提供程序取消注册，请使用 Azure 门户或 Azure CLI 删除 SQL 虚拟机*资源*。 删除 SQL 虚拟机*资源*不会删除 SQL Server VM。 但是，请谨慎操作并仔细执行这些步骤，因为在尝试删除*资源*时可能会无意中删除虚拟机。 

要将管理模式从完全降级，必须取消向 SQL VM 资源提供程序注册 SQL VM。 

### <a name="azure-portal"></a>Azure 门户

要使用 Azure 门户向资源提供程序取消注册 SQL Server VM，请按照以下步骤操作：

1. 登录到[Azure 门户](https://portal.azure.com)。
1. 导航到 SQL Server VM 资源。 
  
   ![SQL 虚拟机资源](media/virtual-machines-windows-sql-manage-portal/sql-vm-manage.png)

1. 选择 **"删除**"。 

   ![删除 SQL VM 资源提供程序](media/virtual-machines-windows-sql-register-with-rp/delete-sql-vm-resource-provider.png)

1. 键入 SQL 虚拟机的名称，并**清除虚拟机旁边的复选框**。

   ![删除 SQL VM 资源提供程序](media/virtual-machines-windows-sql-register-with-rp/confirm-delete-of-resource-uncheck-box.png)

   >[!WARNING]
   > 无法清除虚拟机名称旁边的复选框将完全*删除*虚拟机。 清除该复选框以从资源提供程序取消注册 SQL Server VM，*但不删除实际虚拟机*。 

1. 选择 **"删除**"以确认删除 SQL 虚拟机*资源*，而不是 SQL Server 虚拟机。 

### <a name="command-line"></a>命令行

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
要从 Azure CLI 的资源提供程序取消注册 SQL Server 虚拟机，请使用[az sql vm 删除](/cli/azure/sql/vm?view=azure-cli-latest#az-sql-vm-delete)命令。 这将删除 SQL Server 虚拟机*资源*，但不会删除虚拟机。 


```azurecli-interactive
az sql vm delete 
  --name <SQL VM resource name> |
  --resource-group <Resource group name> |
  --yes 
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
要从 Azure CLI 的资源提供程序取消注册 SQL Server 虚拟机，请使用[New-AzSqlVM](/powershell/module/az.sqlvirtualmachine/new-azsqlvm)命令。 这将删除 SQL Server 虚拟机*资源*，但不会删除虚拟机。 

```powershell-interactive
Remove-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name>
```

---

## <a name="limitations"></a>限制

SQL VM 资源提供程序仅支持：
- 通过 Azure 资源管理器部署的 SQL Server VM。 不支持通过经典模型部署的 SQL Server VM。 
- 部署到公共或 Azure 政府云的 SQL Server VM。 不支持部署到其他私有或政府云。 


## <a name="frequently-asked-questions"></a>常见问题 

**我应该注册从 Azure 应用商店中的 SQL Server 映像预配的 SQL Server VM 吗？**

不是。 Microsoft 自动注册从 Azure 应用商店中的 SQL Server 映像预配的 VM。 仅当 VM*未*从 Azure 应用商店中的 SQL Server 映像预配且 SQL Server 已自行安装时，才需要向 SQL VM 资源提供程序注册。

**SQL VM 资源提供程序是否适用于所有客户？** 

是的。 如果客户没有使用 Azure 应用商店中的 SQL Server 映像，而是使用自安装的 SQL Server，或者带来了自定义 VHD，则应将其 SQL Server VM VM 注册给 SQL VM 资源提供程序。 所有类型的订阅（直接、企业协议和云解决方案提供商）拥有的 VM 都可以向 SQL VM 资源提供商注册。

**如果我的 SQL Server VM 已经安装了 SQL Server IaaS 扩展，我应该向 SQL VM 资源提供程序注册吗？**

如果 SQL Server VM 是自行安装的，并且未从 Azure 应用商店中的 SQL Server 映像预配，则应向 SQL VM 资源提供程序注册，即使您安装了 SQL Server IaaS 扩展。 向 SQL VM 资源提供程序注册将创建 Microsoft.Sql虚拟计算机类型的新资源。 安装 SQL Server IaaS 扩展不会创建该资源。

**注册 SQL VM 资源提供程序时，默认管理模式是什么？**

在 SQL VM 资源提供程序注册时，默认管理模式*已满*。 如果在向 SQL VM 资源提供程序注册时未设置 SQL Server 管理属性，则该模式将设置为完全可管理性，SQL Server 服务将重新启动。 建议先以轻量级模式向 SQL VM 资源提供程序注册，然后在维护时段内升级到已满。 

**向 SQL VM 资源提供程序注册的先决条件是什么？**

在轻量级模式或无代理模式下向 SQL VM 资源提供程序注册没有先决条件。 在全模式下向 SQL VM 资源提供程序注册的先决条件是在 VM 上安装 SQL Server IaaS 扩展，否则 SQL Server 服务将重新启动。 

**如果我没有在 VM 上安装 SQL Server IaaS 扩展，是否可以向 SQL VM 资源提供程序注册？**

可以，如果您没有在 VM 上安装 SQL Server IaaS 扩展，则可以以轻量级管理模式向 SQL VM 资源提供程序注册。 在轻量级模式下，SQL VM 资源提供程序将使用控制台应用来验证 SQL Server 实例的版本和版本。 

注册 SQL VM 资源提供程序时的默认 SQL 管理模式为 _"已满_"。 如果在注册 SQL VM 资源提供程序时未设置 SQL 管理属性，则该模式将设置为"完全可管理性"。 建议先以轻量级模式向 SQL VM 资源提供程序注册，然后在维护时段内升级到已满。 

**向 SQL VM 资源提供程序注册是否会在我的 VM 上安装代理？**

不是。 向 SQL VM 资源提供程序注册只会创建新的元数据资源。 它不会在 VM 上安装代理。

SQL Server IaaS 扩展仅需要实现完全可管理性。 将可管理性模式从轻量级升级到完全将安装 SQL Server IaaS 扩展并将重新启动 SQL Server。

**向 SQL Server VM 资源提供程序注册是否会在我的 VM 上重新启动 SQL Server？**

这取决于注册期间指定的模式。 如果指定了轻量级或 NoAgent 模式，则 SQL Server 服务将不会重新启动。 但是，将管理模式指定为完整，或将管理模式留空将在完全管理模式下安装 SQL IaaS 扩展，这将导致 SQL Server 服务重新启动。 

**在 SQL VM 资源提供程序注册时，轻量级和非代理管理模式之间的区别是什么？** 

无代理管理模式仅适用于 Windows Server 2008 上的 SQL Server 2008 和 SQL Server 2008 R2。 这是这些版本的唯一可用管理模式。 对于所有其他版本的 SQL Server，两种可用的可管理模式是轻量级和完整的。 

无代理模式需要由客户设置 SQL Server 版本和版本属性。 轻量级模式查询 VM 以查找 SQL Server 实例的版本和版本。

**是否可以在不指定 SQL Server 许可证类型的情况下向 SQL VM 资源提供程序注册？**

不是。 当您向 SQL VM 资源提供程序注册时，SQL Server 许可证类型不是可选属性。 在其所有可管理模式（无代理、轻量级和完整）中注册 SQL VM 资源提供程序时，必须将 SQL Server 许可证类型设置为即用即付或 Azure 混合权益。

**是否可以将 SQL Server IaaS 扩展从无代理模式升级到完整模式？**

不是。 将可管理性模式升级到完全或轻量级不适用于无代理模式。 这是 Windows 服务器 2008 的技术限制。 您需要首先将操作系统升级到 Windows Server 2008 R2 或更高版本，然后您将能够升级到完全管理模式。 

**是否可以将 SQL Server IaaS 扩展从轻量级模式升级到完整模式？**

是的。 通过 PowerShell 或 Azure 门户支持将可管理性模式从轻量级升级到完全。 它需要重新启动 SQL Server 服务。

**是否可以将 SQL Server IaaS 扩展从完整模式降级为无代理或轻量级管理模式？**

不是。 不支持降级 SQL Server IaaS 扩展可管理性模式。 可管理性模式不能从完整模式降级为轻量级模式或无代理模式，也不能从轻量模式降级为无代理模式。 

要从完全可管理性更改可管理性模式，请从 SQL Server 资源提供程序[取消注册](#unregister-vm-from-rp)SQL Server 虚拟机，删除 SQL Server*资源*，并在不同的管理模式下再次将 SQL Server VM 重新注册到 SQL VM 资源提供程序。

**是否可以从 Azure 门户向 SQL VM 资源提供程序注册？**

不是。 在 Azure 门户中，向 SQL VM 资源提供程序注册不可用。 仅 Azure CLI 或 PowerShell 支持向 SQL VM 资源提供程序注册。 

**在安装 SQL Server 之前，是否可以向 SQL VM 资源提供程序注册 VM？**

不是。 VM 应至少有一个 SQL Server（数据库引擎）实例才能成功注册到 SQL VM 资源提供程序。 如果 VM 上没有 SQL Server 实例，则新的 Microsoft.SqlVirtualMachine 资源将处于失败状态。

**如果有多个 SQL Server 实例，是否可以向 SQL VM 资源提供程序注册 VM？**

是的。 SQL VM 资源提供程序将仅注册一个 SQL Server（数据库引擎）实例。 SQL VM 资源提供程序将在多个实例的情况下注册默认 SQL Server 实例。 如果没有默认实例，则仅支持在轻量级模式下注册。 要从轻量级模式升级到完全可管理性模式，默认 SQL Server 实例应存在，或者 VM 应只有一个命名 SQL Server 实例。

**是否可以向 SQL VM 资源提供程序注册 SQL Server 故障转移群集实例？**

是的。 Azure VM 上的 SQL Server 故障转移群集实例可以在轻量级模式下注册到 SQL VM 资源提供程序。 但是，SQL Server 故障转移群集实例无法升级到完全可管理模式。

**如果配置了"始终打开"可用性组，是否可以向 SQL VM 资源提供程序注册 VM？**

是的。 如果您正在参与"始终打开"可用性组配置，则在 Azure VM 上向 SQL VM 资源提供程序注册 SQL Server 实例没有限制。

**注册 SQL VM 资源提供程序或升级到完全可管理性模式的成本是多少？**
无。 与注册 SQL VM 资源提供程序或使用三种可管理模式中的任何一种不收取任何费用。 使用资源提供程序管理 SQL Server VM 是完全免费的。 

**使用不同的可管理性模式对性能有何影响？**
使用*NoAgent*和*轻量级*可管理性模式时，没有任何影响。 使用从安装到操作系统的两个服务*的完整*可管理性模式时，影响最小。 这些可以通过任务管理器进行监视，并在内置的 Windows 服务控制台中看到。 

两个服务名称是：
- `SqlIaaSExtensionQuery`（显示名称`Microsoft SQL Server IaaS Query Service`- ）
- `SQLIaaSExtension`（显示名称`Microsoft SQL Server IaaS Agent`- ）


## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅以下文章： 

* [Windows VM 上的 SQL 服务器概述](virtual-machines-windows-sql-server-iaas-overview.md)
* [Windows VM 上的 SQL 服务器常见问题解答](virtual-machines-windows-sql-server-iaas-faq.md)
* [Windows VM 上的 SQL 服务器定价指南](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Windows VM 上的 SQL 服务器的发行说明](virtual-machines-windows-sql-server-iaas-release-notes.md)
