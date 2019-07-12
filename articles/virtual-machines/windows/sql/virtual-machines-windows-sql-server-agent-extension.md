---
title: 自动执行管理任务 Azure 虚拟机上使用 SQL Server IaaS 代理扩展 |Microsoft Docs
description: 本文介绍如何管理可以自动执行特定 SQL Server 管理任务的 SQL Server 代理扩展。 这些任务包括自动备份、自动修补和 Azure 密钥保管库集成。
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: jroth
editor: ''
tags: azure-resource-manager
ms.assetid: effe4e2f-35b5-490a-b5ef-b06746083da4
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/24/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 41023103dc30d16f599e847f9d324bc7bb4be11c
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798053"
---
# <a name="automate-management-tasks-on-azure-virtual-machines-with-the-sql-server-iaas-agent-extension"></a>自动执行管理任务 Azure 虚拟机上使用 SQL Server IaaS 代理扩展
> [!div class="op_single_selector"]
> * [资源管理器](virtual-machines-windows-sql-server-agent-extension.md)
> * [经典](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md)

SQL Server IaaS 代理扩展 (SqlIaasExtension) 在 Azure 虚拟机上运行，以自动执行管理任务。 本文提供的概述和支持的扩展插件以及有关安装、 状态和删除说明的服务。

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

若要查看这篇文章的经典版，请参阅[适用于 SQL Server 经典 VM 的 SQL Server 代理扩展](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md)。

有三个 SQL 可管理性模式的 SQL IaaS 扩展：**完整**，**轻型**，和**NoAgent**。 

- **完整**模式提供了所有功能，但需要重新启动 SQL Server 和 SA 权限。 这是默认情况下安装，应使用用于管理 SQL Server VM 的单个实例的选项。 

- **轻型**不需要重新启动 SQL Server，但仅支持更改许可证类型和版本的 SQL Server。 此选项应为用于 SQL Server Vm 的多个实例，或参与故障转移群集实例 (FCI) 中。 

- **NoAgent**是专用于 SQL Server 2008 和 Windows Server 2008 上安装 SQL Server 2008 R2。 有关利用`NoAgent`模式为 Windows Server 2008 映像，请参阅[Windows Server 2008 注册](virtual-machines-windows-sql-register-with-resource-provider.md#register-sql-server-2008r2-on-windows-server-2008-vms)。 

## <a name="supported-services"></a>支持的服务
SQL Server IaaS 代理扩展支持以下管理任务：

| 管理功能 | 描述 |
| --- | --- |
| **SQL 自动备份** |对 VM 中 SQL Server 的默认实例或[已正确安装的](virtual-machines-windows-sql-server-iaas-faq.md#administration)命名实例自动执行所有数据库的备份计划。 有关详细信息，请参阅 [Azure 虚拟机中 SQL Server 的自动备份 (Resource Manager)](virtual-machines-windows-sql-automated-backup.md)。 |
| **SQL 自动修补** |配置维护时段，可在此时段对 VM 进行重要的 Windows 更新，避开工作负荷的高峰期。 有关详细信息，请参阅 [Azure 虚拟机中 SQL Server 的自动修补 (Resource Manager)](virtual-machines-windows-sql-automated-patching.md)。 |
| **Azure 密钥保管库集成** |可在 SQL Server VM 上自动安装和配置 Azure Key Vault。 有关详细信息，请参阅 [为 Azure VM 上的 SQL Server 配置 Azure 密钥保管库集成 (Resource Manager)](virtual-machines-windows-ps-sql-keyvault.md)。 |

一旦安装和运行，SQL Server IaaS 代理扩展便可使这些管理功能在 Azure 门户中虚拟机的 SQL Server 面板上获得，也可通过 Azure PowerShell for SQL Server 市场映像和 Azure PowerShell 获得，以手动安装扩展。 

## <a name="prerequisites"></a>先决条件
在 VM 上使用 SQL Server IaaS 代理扩展的要求：

**操作系统**：

* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016
* Windows Server 2019 

**SQL Server 版本**：

* SQL Server 2008 
* SQL Server 2008 R2
* SQL Server 2012
* SQL Server 2014
* SQL Server 2016
* SQL Server 2017

**Azure PowerShell**：

* [下载和配置最新 Azure PowerShell 命令](/powershell/azure/overview)

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]


## <a name="change-management-modes"></a>更改的管理模式

可以使用 PowerShell 来查看你的 SQL IaaS 代理的当前模式： 

  ```powershell-interactive
     //Get the SqlVirtualMachine
     $sqlvm = Get-AzResource -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName  -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines
     $sqlvm.Properties.sqlManagement
  ```

具有 SQL Server vm *NoAgent*或*轻型*IaaS 安装扩展，你可以升级到的模式*完整*使用 Azure 门户。 不能降级-若要执行此操作，将需要完全卸载 SQL IaaS 扩展和重新安装它。 

升级代理模式设置为对*完整*，执行以下操作： 

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 导航到您[SQL 虚拟机](virtual-machines-windows-sql-manage-portal.md#access-sql-virtual-machine-resource)资源。 
1. 选择你的 SQL Server 虚拟机，然后选择**概述**。 
1. 对于包含的 SQL Vm *NoAgent*或*轻型*IaaS 模式下，选择为消息**许可证类型和版本更新仅适用于 SQL IaaS 扩展**。

    ![启动模式更改从门户](media/virtual-machines-windows-sql-server-agent-extension/change-sql-iaas-mode-portal.png)

1. 同意**重新启动 SQL Server 服务**通过选中该复选框，并选择**确认**升级您的 IaaS 模式为 full。 

    ![启用完全管理 IaaS 扩展](media/virtual-machines-windows-sql-server-agent-extension/enable-full-mode-iaas.png)

##  <a name="installation"></a>安装
注册与 SQL Server VM 时安装了 SQL IaaS 扩展[SQL 虚拟机资源提供程序](virtual-machines-windows-sql-register-with-resource-provider.md#register-with-sql-vm-resource-provider)。 但是，如有必要，SQL IaaS 代理也可以安装使用手动*完整*或*轻型*模式安装。 

*完整*预配使用 Azure 门户的 SQL Server 虚拟机库映像之一时，会自动安装 SQL Server IaaS 代理扩展。 

### <a name="full-mode-installation"></a>完整模式安装
*完整*SQL IaaS 扩展提供了完整的 SQL Server VM 上的单个实例的可管理性。 如果有默认实例，则此扩展可以用于该默认实例，不支持对其他实例进行管理。 如果没有默认实例，而是只有一个命名实例，则会管理命名实例。 如果没有默认实例，但是有多个命名实例，则此扩展无法安装。 

安装*完整*SQL IaaS 的模式将重新启动 SQL Server 服务。 若要避免重新启动 SQL Server 服务，请安装*轻型*改为模式使用有限的可管理性。 

安装 SQL IaaS 代理*完整*模式下使用 PowerShell:

  ```powershell-interactive
     // Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     // Register SQL VM with 'Full' SQL IaaS agent
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;sqlServerLicenseType='AHUB';sqlManagement='Full'}  
  
  ```

| 参数 | 可接受的值                        |
| :------------------| :-------------------------------|
| **sqlServerLicenseType** | `'AHUB'`或 `'PAYG'`     |
| &nbsp;             | &nbsp;                          |


> [!WARNING]
> - 如果尚未安装该扩展，安装**完整**扩展将重新启动 SQL Server 服务。 使用**轻型**模式以避免重新启动 SQL Server 服务。 
> - 更新 SQL IaaS 扩展不会重启 SQL Server 服务。 

#### <a name="install-on-a-vm-with-a-single-named-sql-server-instance"></a>在具有单个命名 SQL Server 实例的 VM 上安装
如果卸载默认实例和 IaaS 扩展重新安装，SQL IaaS 扩展将使用命名实例适用于 SQL Server 中。

若要使用 SQL Server 的命名实例，请执行以下操作：
   1. 从市场部署 SQL Server VM。 
   1. 在 [Azure 门户](https://portal.azure.com)中卸载 IaaS 扩展。
   1. 在 SQL Server VM 中彻底卸载 SQL Server。
   1. 在 SQL Server VM 中将 SQL Server 与命名实例一起安装。 
   1. 在 Azure 门户中安装 IaaS 扩展。  


### <a name="install-in-lightweight-mode"></a>在轻量级模式下安装
轻量级模式下不会重启你的 SQL Server 服务，但它提供了有限的功能。 

安装 SQL IaaS 代理*轻型*模式下使用 PowerShell:


  ```powershell-interactive
     // Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     // Register SQL VM with 'Lightweight' SQL IaaS agent
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;sqlServerLicenseType='AHUB';sqlManagement='LightWeight'}  
  
  ```

| 参数 | 可接受的值                        |
| :------------------| :-------------------------------|
| **sqlServerLicenseType** | `'AHUB'`或 `'PAYG'`     |
| &nbsp;             | &nbsp;                          |


## <a name="get-status-of-sql-iaas-extension"></a>获取 SQL IaaS 扩展状态
验证是否已安装扩展的方法之一是在 Azure 门户中查看代理状态。 在虚拟机窗口中选择“所有设置”，并单击“扩展”。   应看到列出“SqlIaasExtension”扩展。 

![Azure 门户中的 SQL Server IaaS 代理扩展](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-portal.png)

也可以使用 **Get-AzVMSqlServerExtension** Azure PowerShell cmdlet。

   ```powershell-interactive
   Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
   ```

上一个命令确认已安装代理并提供常规状态信息。 还可使用以下命令获取有关自动备份和修补的特定状态信息。

   ```powershell-interactive
    $sqlext = Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
    $sqlext.AutoPatchingSettings
    $sqlext.AutoBackupSettings
   ```

## <a name="removal"></a>删除
在 Azure 门户中，可以通过单击虚拟机属性的“扩展”窗口中的省略号来卸载扩展。  然后单击“删除”  。

![在 Azure 门户中卸载 SQL Server IaaS 代理扩展](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-uninstall.png)

也可以使用 **Remove-AzVMSqlServerExtension** PowerShell cmdlet。

   ```powershell-interactive
    Remove-AzVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SqlIaasExtension"
   ```

## <a name="next-steps"></a>后续步骤
开始使用扩展支持的服务之一。 有关详细信息，请参阅文章中引用[支持的服务](#supported-services)本文的部分。

有关在 Azure 虚拟机中运行 SQL Server 的详细信息，请参阅 [Azure 虚拟机中的 SQL Server 概述](virtual-machines-windows-sql-server-iaas-overview.md)。

