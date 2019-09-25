---
title: 使用 SQL VM 资源提供程序在 Azure 中注册 SQL Server 虚拟机 |Microsoft Docs
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
ms.openlocfilehash: 2bf7118d1f4be065969312d1fb9b0cf77e820d48
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2019
ms.locfileid: "71262881"
---
# <a name="register-a-sql-server-virtual-machine-in-azure-with-the-sql-vm-resource-provider"></a>使用 SQL VM 资源提供程序在 Azure 中注册 SQL Server 虚拟机

本文介绍如何通过 SQL VM 资源提供程序在 Azure 中注册 SQL Server 虚拟机（VM）。 

通过 Azure 门户部署 SQL Server VM Azure Marketplace 映像会自动将 SQL Server VM 注册到资源提供程序。 如果选择在 Azure 虚拟机上自行安装 SQL Server 而不是从 Azure Marketplace 中选择映像，或者如果使用 SQL Server 从自定义 VHD 预配 Azure VM，则应将 SQL Server VM 注册到的资源提供程序:

- **简化许可证管理**：根据 Microsoft 产品条款，客户在使用[Azure 混合权益](https://azure.microsoft.com/pricing/hybrid-benefit/)时必须告知 microsoft。 使用 SQL VM 资源提供程序注册可简化 SQL Server 许可证管理，并可让你使用[门户](virtual-machines-windows-sql-manage-portal.md)或 Az CLI 中的 Azure 混合权益快速识别 SQL Server 的 vm： 

   ```azurecli-interactive
   $vms = az sql vm list | ConvertFrom-Json
   $vms | Where-Object {$_.sqlServerLicenseType -eq "AHUB"}
   ```

- **功能优势**：向资源提供程序注册您的 SQL Server VM 可解锁[自动修补](virtual-machines-windows-sql-automated-patching.md)、[自动备份](virtual-machines-windows-sql-automated-backup-v2.md)和监控和可管理性功能。 它还解锁了[授权](virtual-machines-windows-sql-ahb.md)和[版本](virtual-machines-windows-sql-change-edition.md)灵活性。 以前，这些功能仅可用于从 Azure Marketplace SQL Server VM 映像。

- **免费管理**：向 SQL VM 资源提供程序和所有可管理性模式注册都是完全免费的。 资源提供程序或更改管理模式不会产生额外的费用。 

若要利用 SQL VM 资源提供程序, 还必须将 SQL VM 资源提供程序注册到订阅。 可以使用 Azure 门户、Azure CLI 或 PowerShell 实现此目的。 

  > [!NOTE]
  > 没有与向资源提供程序注册相关的其他许可要求。 向 SQL VM 资源提供程序注册会提供一种简化的方法，使你能够在为每个资源管理许可注册表单的位置上通知 Microsoft Azure 混合权益。 

## <a name="prerequisites"></a>先决条件

若要向资源提供程序注册你的 SQL Server VM，你将需要以下各项： 

- 一个 [Azure 订阅](https://azure.microsoft.com/free/)。
- 一个[SQL Server VM](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision)。 
- [Azure CLI](/cli/azure/install-azure-cli)或[PowerShell](/powershell/azure/new-azureps-module-az)的最新版本。 


## <a name="register-with-sql-vm-resource-provider"></a>向 SQL VM 资源提供程序注册
如果 VM 上未安装[SQL Server IaaS 代理扩展](virtual-machines-windows-sql-server-agent-extension.md)，则可以通过指定轻型 SQL 管理模式向 SQL VM 资源提供程序注册。 

如果在注册过程中指定了轻型，SQL VM 资源提供程序将在[轻型模式下](#change-management-modes)自动安装 Sql IaaS 扩展，并验证 SQL Server 实例元数据;这不会重新启动 SQL Server 服务。 将 SQL VM 资源提供程序注册为 "PAYG" 或 "AHUB" 时, 需要提供所需的 SQL Server 许可证类型。

在轻型模式下向 SQL VM 资源提供程序注册将确保合规性并启用灵活的许可以及就地 SQL Server 版更新。 故障转移群集实例和多实例部署只能在轻型模式下在 SQL VM 资源提供程序中注册。 你可以随时[升级](#change-management-modes)到完整管理模式，但这样做将重新启动 SQL Server 服务。 


# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
如果已在 VM 上安装了 SQL Server IaaS 扩展，请使用以下代码片段注册到 SQL VM 资源提供程序。 向 SQL VM 资源提供程序注册时，需要提供所需的 SQL Server 许可证类型：即用即付（`PAYG`）或 Azure 混合权益（`AHUB`）。 

使用以下 PowerShell 代码片段注册 SQL Server VM：

  ```powershell-interactive
     # Get the existing compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     # Register SQL VM with 'Lightweight' SQL IaaS agent
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;SqlServerLicenseType='PAYG';sqlManagement='LightWeight'}  
  
  ```

# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)

对于付费版（企业版或标准版）：

  ```azurecli-interactive
  # Register Enterprise or Standard self-installed VM in Lightweight mode

  az sql vm create --name <vm_name> --resource-group <resource_group_name> --location <vm_location> --license-type PAYG 

  ```

对于免费版（开发人员版、Web 版或 Express 版）：

  ```azurecli-interactive
  # Register Developer, Web, or Express self-installed VM in Lightweight mode

  az sql vm create --name <vm_name> --resource-group <resource_group_name> --location <vm_location> --license-type PAYG 
  ```
---

如果已手动将 SQL IaaS 扩展安装到 VM，则可以通过创建 SqlVirtualMachine/SqlVirtualMachines 类型的元数据资源，以完全模式向 SQL VM 资源提供程序注册。 下面是在 VM 上已安装 SQL IaaS 扩展时要向 SQL VM 资源提供程序注册的代码片段。 需要提供所需的 SQL Server 许可证类型作为 "PAYG" 或 "AHUB"。 若要以完全管理模式注册，请使用以下 PowerShell 命令：

  ```powershell-interactive
  # Get the existing  Compute VM
   $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
   # Register with SQL VM resource provider
   New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
      -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
      -Properties @{virtualMachineResourceId=$vm.Id;SqlServerLicenseType='PAYG'}
  ```


## <a name="register-sql-server-2008-or-2008-r2-on-windows-server-2008-vms"></a>在 Windows Server 2008 Vm 上注册 SQL Server 2008 或 2008 R2

Windows Server 2008 上安装的 SQL Server 2008 和 2008 R2 可以在[无代理模式下](#change-management-modes)注册到 SQL VM 资源提供程序。 此选项可确保符合性, 并允许在 Azure 门户中监视 SQL Server VM 功能有限。

下表详细说明了在注册过程中提供的参数的可接受值:

| 参数 | 可接受值                                 |
| :------------------| :--------------------------------------- |
| **sqlLicenseType** | `AHUB` 或 `PAYG`                    |
| **sqlImageOffer**  | `SQL2008-WS2008` 或 `SQL2008R2-WS2008`|
| &nbsp;             | &nbsp;                                   |


若要在 Windows Server 2008 实例上注册 SQL Server 2008 或 2008 R2 实例，请使用以下 Powershell 或 Az CLI 代码片段：  

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
  ```powershell-interactive
     # Get the existing compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
    New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
      -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
      -Properties @{virtualMachineResourceId=$vm.Id;SqlServerLicenseType='PAYG'; `
       sqlManagement='NoAgent';sqlImageSku='Standard';sqlImageOffer='SQL2008R2-WS2008'}
  ```

# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)

  ```azurecli-interactive
   az sql vm create -n sqlvm -g myresourcegroup -l eastus |
   --license-type PAYG --sql-mgmt-type NoAgent 
   --image-sku Enterprise --image-offer SQL2008-WS2008R2
 ```

---

## <a name="verify-registration-status"></a>验证注册状态
可以通过使用 Azure 门户、Azure CLI 或 PowerShell 来验证是否已向 SQL VM 资源提供程序注册了 SQL Server VM。 

### <a name="azure-portal"></a>Azure 门户 

1. 登录到 [Azure 门户](https://portal.azure.com)。 
1. 中转到[SQL Server 虚拟机](virtual-machines-windows-sql-manage-portal.md)。
1. 从列表中选择 SQL Server VM。 如果 SQL Server VM 未在此处列出，则可能尚未向 SQL VM 资源提供程序注册它。 
1. 查看 "**状态**" 下的值。 如果**状态**为 "**成功**"，则已成功向 SQL VM 资源提供程序注册了 SQL Server VM。 

![通过 SQL RP 注册验证状态](media/virtual-machines-windows-sql-register-with-rp/verify-registration-status.png)

### <a name="command-line"></a>命令行

使用 Az CLI 或 PowerShell 验证当前 SQL Server VM 注册状态。 `ProvisioningState`将显示`Succeeded`注册是否成功。 

# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)


  ```azurecli-interactive
  az sql vm show -n <vm_name> -g <resource_group>
 ```


# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

  ```powershell-interactive
  Get-AzResource -ResourceName <vm_name> -ResourceGroupName <resource_group> `
  -ResourceType Microsoft.SqlVirtualMachine/sqlVirtualMachines
  ```

---

错误表明 SQL Server VM 尚未注册到资源提供程序。 

## <a name="change-management-modes"></a>更改管理模式

对于 SQL Server IaaS 扩展，有三种免费的可管理性模式： 

- **完整**模式提供所有功能，但需要重新启动 SQL Server 和系统管理员权限。 这是默认情况下安装的选项。 使用它来管理包含单个实例的 SQL Server VM。 完整模式安装两个 windows 服务，这些服务对内存和 CPU 的影响最小-可通过任务管理器进行监视。 使用完全可管理性模式不会产生任何费用。 

- **轻型**不需要重新启动 SQL Server，但它仅支持更改 SQL Server 的许可证类型和版本。 SQL Server 对于具有多个实例的 Vm 或参与故障转移群集实例（FCI）的 Vm，请使用此选项。 使用轻型模式时，不会对内存或 CPU 产生任何影响。 使用轻型可管理性模式不会产生任何费用。 

- **NoAgent**专用于在 Windows Server 2008 上安装 SQL Server 2008 和 SQL Server 2008 R2。 使用 NoAgent 模式时，不会对内存或 CPU 产生任何影响。 使用 NoAgent 可管理性模式不会产生费用。 

你可以使用 PowerShell 查看 SQL Server IaaS 代理的当前模式： 

  ```powershell-interactive
     #Get the SqlVirtualMachine
     $sqlvm = Get-AzResource -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName  -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines
     $sqlvm.Properties.sqlManagement
  ```

如果 SQL Server 安装了*轻型*IaaS 扩展的 vm，则可以使用 Azure 门户将模式升级到_完整_模式。 将操作系统升级到 Windows 2008 R2 及更高版本后，_无代理_模式下的 SQL Server vm 可以升级到_完整_版本。 不可能进行降级-若要执行此操作，你将需要完全卸载 SQL IaaS 扩展并重新安装它。 

若要将代理模式升级到完整模式： 


### <a name="azure-portal"></a>Azure 门户

1. 登录到 [Azure 门户](https://portal.azure.com)。
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
  # Update to full mode

  $SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
  $SqlVm.Properties.sqlManagement="Full"
  $SqlVm | Set-AzResource -Force
  ```
---

## <a name="register-the-sql-vm-resource-provider-with-a-subscription"></a>向订阅注册 SQL VM 资源提供程序 

若要向 SQL VM 资源提供程序注册你的 SQL Server VM，你必须将资源提供程序注册到你的订阅。 你可以使用 Azure 门户、Azure CLI 或 PowerShell 来执行此操作。

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
以下代码片段会将 SQL VM 资源提供程序注册到你的 Azure 订阅。 

```azurecli-interactive
# Register the new SQL VM resource provider to your subscription 
az provider register --namespace Microsoft.SqlVirtualMachine 
```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

```powershell-interactive
# Register the new SQL VM resource provider to your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```
---

## <a name="remarks"></a>备注

- SQL VM 资源提供程序仅支持通过 Azure 资源管理器部署 SQL Server Vm。 不支持 SQL Server 通过经典模型部署的 Vm。 
- SQL VM 资源提供程序仅支持部署到公有云 SQL Server Vm。 不支持部署到私有或政府云。 
 

## <a name="frequently-asked-questions"></a>常见问题 

**是否应在 Azure Marketplace 中注册从 SQL Server 映像预配的 SQL Server VM？**

否。 Microsoft 自动注册从 Azure Marketplace 中的 SQL Server 映像预配的 Vm。 仅当*未*从 Azure Marketplace 中的 SQL Server 映像预配 VM，并且 SQL Server 自安装时，才需要向 SQL VM 资源提供程序注册。

**SQL VM 资源提供程序是否适用于所有客户？** 

是。 如果客户使用的是 Azure Marketplace 中的 SQL Server 映像，而不是自安装的 SQL Server，或者如果他们提供自定义 VHD，则应该向 SQL VM 资源提供程序注册其 SQL Server Vm。 所有类型的订阅（直接、企业协议和云解决方案提供商）拥有的 Vm 均可使用 SQL VM 资源提供程序进行注册。

**如果 SQL Server VM 已安装了 SQL Server IaaS 扩展，我应该向 SQL VM 资源提供程序注册吗？**

如果 SQL Server VM 是自行安装的，并且未从 Azure Marketplace 中的 SQL Server 映像进行预配，则应该向 SQL VM 资源提供程序注册，即使安装了 SQL Server IaaS 扩展。 使用 SQL VM 资源提供程序注册会创建 SqlVirtualMachines 类型的新资源。 安装 SQL Server IaaS 扩展不会创建该资源。

**向 SQL VM 资源提供程序注册时，默认的管理模式是什么？**

向 SQL VM 资源提供程序注册时的默认管理模式为*full*。 如果在向 SQL VM 资源提供程序注册时未设置 SQL Server 管理属性，则该模式将设置为完全可管理性。 在虚拟机上安装 SQL Server IaaS 扩展是在完全可管理性模式下向 SQL VM 资源提供程序注册的先决条件。

**向 SQL VM 资源提供程序注册的先决条件是什么？**

在轻型模式或无代理模式下, 不需要向 SQL VM 资源提供程序注册。 在完整模式下以 SQL VM 资源提供程序注册的先决条件是在 VM 上安装了 SQL Server IaaS 扩展。

**如果未在 VM 上安装 SQL Server IaaS 扩展，则可以使用 SQL VM 资源提供程序注册吗？**

是的，如果未在 VM 上安装 SQL Server IaaS 扩展，则可以在轻型管理模式下向 SQL VM 资源提供程序注册。 在轻型模式下，SQL VM 资源提供程序将使用控制台应用程序来验证 SQL Server 实例的版本。 

在 SQL VM 资源提供程序中注册时，默认 SQL 管理模式已_满_。 如果在向 SQL VM 资源提供程序注册时未设置 SQL 管理属性, 则该模式将设置为完全可管理性。 在虚拟机上安装 SQL IaaS 扩展是在完全可管理性模式下向 SQL VM 资源提供程序注册的先决条件。

**向 SQL VM 资源提供程序注册将在我的 VM 上安装代理？**

否。 使用 SQL VM 资源提供程序注册将仅创建新的元数据资源。 它不会在 VM 上安装代理。

仅在启用完全可管理性时，才需要 SQL Server IaaS 扩展。 将可管理性模式从轻型升级到 full 会安装 SQL Server IaaS 扩展，并将 SQL Server 重新启动。

**将向 VM 注册 SQL Server VM 资源提供程序重启 SQL Server？**

否。 使用 SQL VM 资源提供程序注册将仅创建新的元数据资源。 它不会在 VM 上重新启动 SQL Server。 

只有在安装 SQL Server IaaS 扩展时，才需要重新启动 SQL Server。 若要实现完全可管理性，需要和 SQL Server IaaS 扩展。 将可管理性模式从轻型升级到 full 会安装 SQL Server IaaS 扩展，并将 SQL Server 重新启动。

**向 SQL VM 资源提供程序注册时，轻量和无代理管理模式之间的区别是什么？** 

无-代理管理模式仅适用于 SQL Server 2008，Windows Server 2008 上 SQL Server 2008 R2。 对于这些版本，它是唯一可用的管理模式。 对于所有其他版本的 SQL Server，两种可用的可管理性模式为轻型和完整。 

无代理模式要求客户设置 SQL Server 版本和版本属性。 轻型模式将查询 VM 以查找 SQL Server 实例的版本。

**是否可以使用 Azure CLI 在轻型或无代理模式下向 SQL VM 资源提供程序注册？**

否。 仅当使用 Azure PowerShell 向 SQL VM 资源提供程序注册时，管理模式属性才可用。 Azure CLI 不支持设置 SQL Server 可管理性属性。 它始终以默认模式向 SQL VM 资源提供程序注册，并具有完全可管理性。

**是否可以向 SQL VM 资源提供程序注册而不指定 SQL Server 许可证类型？**

否。 向 SQL VM 资源提供程序注册时，SQL Server 许可证类型不是可选的属性。 使用 Azure CLI 和 PowerShell 在所有可管理性模式（无代理、轻型和完全）中注册 SQL VM 资源提供程序时，必须将 SQL Server 许可证类型设置为即用即付或 Azure 混合权益。

**是否可以将 SQL Server IaaS 扩展从无代理模式升级到完整模式？**

否。 在无代理模式下，将可管理性模式升级为 "完整" 或 "轻型" 不可用。 这是 Windows Server 2008 的技术限制。

**是否可以将 SQL Server IaaS 扩展从轻型模式升级到完整模式？**

是。 支持通过 PowerShell 或 Azure 门户将可管理性模式从轻型升级到完整。 它需要重新启动 SQL Server。

**是否可以将 SQL Server IaaS 扩展从完整模式降级到无代理或轻型管理模式？**

否。 不支持降级 SQL Server IaaS 扩展可管理性模式。 可管理性模式无法从完整模式降级为轻型模式或无代理模式，并且无法从轻型模式降级到无代理模式。 

若要从完全可管理性更改可管理性模式，请删除 SQL Server IaaS 扩展。 然后，删除 SqlVirtualMachine 资源，并向 SQL VM 资源提供程序重新注册 SQL Server VM。

**能否从 Azure 门户注册 SQL VM 资源提供程序？**

否。 Azure 门户中不提供注册到 SQL VM 资源提供程序的功能。 Azure CLI 或 PowerShell 支持在完全可管理性模式下向 SQL VM 资源提供程序注册。 仅 Azure PowerShell Api 支持在轻型或无代理可管理性模式下注册 SQL VM 资源提供程序。

**是否可以在安装 SQL Server 之前向 SQL VM 资源提供程序注册 VM？**

否。 VM 应该至少有一个 SQL Server 实例，才能成功注册到 SQL VM 资源提供程序。 如果 VM 上没有 SQL Server 实例，则新的 SqlVirtualMachine 资源将处于 "失败" 状态。

**如果有多个 SQL Server 实例，能否使用 SQL VM 资源提供程序注册 VM？**

是。 SQL VM 资源提供程序将只注册一个 SQL Server 实例。 SQL VM 资源提供程序将在多个实例的情况下注册默认 SQL Server 实例。 如果没有默认实例, 则仅支持在轻型模式下进行注册。 若要从轻型升级到完全可管理性模式，应存在默认 SQL Server 实例，或者 VM 应只有一个命名 SQL Server 实例。

**是否可以向 SQL VM 资源提供程序注册 SQL Server 故障转移群集实例？**

是。 在 Azure VM 上 SQL Server 故障转移群集实例可在轻型模式下通过 SQL VM 资源提供程序进行注册。 但 SQL Server 故障转移群集实例不能升级到完全可管理性模式。

**如果配置了 Always On 可用性组，是否可以向 SQL VM 资源提供程序注册 VM？**

是。 如果正在加入 Always On 的可用性组配置，则使用 SQL VM 资源提供程序在 Azure VM 上注册 SQL Server 实例没有任何限制。

**向 SQL VM 资源提供程序注册或升级到完全可管理性模式的成本是多少？**
无。 向 SQL VM 资源提供程序注册或使用三个可管理性模式中的任何一种，都不会产生任何费用。 用资源提供程序管理 SQL Server VM 完全免费。 

**使用不同的可管理性模式会对性能造成哪些影响？**
使用*NoAgent*和*轻量级*可管理性模式时，不会产生任何影响。 从安装到操作系统的两个服务使用*完全*可管理性模式时，影响最小。 可以通过任务管理器监视这些。 

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅以下文章： 

* [Windows VM 上的 SQL Server 概述](virtual-machines-windows-sql-server-iaas-overview.md)
* [Windows VM 上的 SQL Server 常见问题](virtual-machines-windows-sql-server-iaas-faq.md)
* [Windows VM 上 SQL Server 的定价指南](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Windows VM 上 SQL Server 的发行说明](virtual-machines-windows-sql-server-iaas-release-notes.md)
