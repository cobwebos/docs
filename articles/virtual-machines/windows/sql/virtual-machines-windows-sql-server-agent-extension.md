---
title: 使用 SQL Server IaaS 代理扩展在 Azure 虚拟机上自动完成管理任务 |Microsoft Docs
description: 本文介绍如何管理 SQL Server IaaS 代理扩展，该扩展自动执行特定 SQL Server 管理任务。 其中包括自动备份、自动修补和 Azure Key Vault 集成。
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
ms.date: 08/30/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 3240bb689447c16de8c62e9e8118b0b0df2b1ea3
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70259430"
---
# <a name="automate-management-tasks-on-azure-virtual-machines-by-using-the-sql-server-iaas-agent-extension"></a>使用 SQL Server IaaS 代理扩展在 Azure 虚拟机上自动执行管理任务
> [!div class="op_single_selector"]
> * [资源管理器](virtual-machines-windows-sql-server-agent-extension.md)
> * [经典](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md)

SQL Server IaaS 代理扩展 (SqlIaasExtension) 在 Azure 虚拟机上运行，以自动执行管理任务。 本文概述了该扩展支持的服务。 本文还提供了有关安装、状态和删除扩展的说明。

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

若要查看本文的经典版本，请参阅[SQL Server vm 的 SQL Server IaaS 代理扩展（经典）](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md)。


## <a name="supported-services"></a>支持的服务
SQL Server IaaS 代理扩展支持以下管理任务：

| 管理功能 | 描述 |
| --- | --- |
| **SQL Server 自动备份** |对 VM 中 SQL Server 的默认实例或[已正确安装的](virtual-machines-windows-sql-server-iaas-faq.md#administration)命名实例自动执行所有数据库的备份计划。 有关详细信息，请参阅[Azure 虚拟机中 SQL Server 的自动备份（资源管理器）](virtual-machines-windows-sql-automated-backup.md)。 |
| **SQL Server 自动修补** |配置维护时段，可在此时段对 VM 进行重要的 Windows 更新，避开工作负荷的高峰期。 有关详细信息，请参阅[在 Azure 虚拟机中自动修补 SQL Server （资源管理器）](virtual-machines-windows-sql-automated-patching.md)。 |
| **Azure Key Vault 集成** |可在 SQL Server VM 上自动安装和配置 Azure Key Vault。 有关详细信息，请参阅为[Azure 虚拟机上的 SQL Server 配置 Azure Key Vault 集成（资源管理器）](virtual-machines-windows-ps-sql-keyvault.md)。 |

安装并运行 SQL Server Iaas 代理扩展后，它将使管理功能可用：

* 在 Azure 门户的虚拟机的 SQL Server 面板中，通过 Azure Marketplace 上的 SQL Server 映像 Azure PowerShell。
* 通过 Azure PowerShell 扩展的手动安装。 

## <a name="prerequisites"></a>先决条件
下面是在 VM 上使用 SQL Server IaaS 代理扩展的要求：

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


##  <a name="installation"></a>安装
当你向[SQL VM 资源提供程序](virtual-machines-windows-sql-register-with-resource-provider.md)注册你的 SQL Server VM 时，会安装 SQL Server IaaS 扩展。 如有必要，可以使用以下 PowerShell 命令手动安装 SQL Server IaaS 代理： 

  ```powershell-interactive
    Set-AzVMExtension -ResourceGroupName "<ResourceGroupName>" `
    -Location "<VMLocation>" -VMName "<VMName>" `
    -Name "SqlIaasExtension" -Publisher "Microsoft.SqlServer.Management" `
    -ExtensionType "SqlIaaSAgent" -TypeHandlerVersion "2.0";  
  ```

> [!NOTE]
> 安装扩展会重新启动 SQL Server 服务。 


### <a name="install-on-a-vm-with-a-single-named-sql-server-instance"></a>在具有单个命名 SQL Server 实例的 VM 上安装
如果卸载了默认实例并且重新安装了 IaaS 扩展，则 SQL Server IaaS 扩展将与 SQL Server 上的命名实例一起使用。

若要使用 SQL Server 的命名实例，请执行以下步骤：
   1. 从 Azure Marketplace 部署 SQL Server VM。 
   1. 从[Azure 门户](https://portal.azure.com)卸载 IaaS 扩展。
   1. 在 SQL Server VM 中彻底卸载 SQL Server。
   1. 在 SQL Server VM 中将 SQL Server 与命名实例一起安装。 
   1. 从 Azure 门户安装 IaaS 扩展。  


## <a name="get-the-status-of-the-sql-server-iaas-extension"></a>获取 SQL Server IaaS 扩展的状态
验证是否已安装扩展的方法之一是在 Azure 门户中查看代理状态。 在 "虚拟机" 窗口中选择 "**所有设置**"，然后选择 "**扩展**"。 应看到列出“SqlIaasExtension”扩展。

![Azure 门户中 SQL Server IaaS 代理扩展的状态](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-portal.png)

还可以使用**AzVMSqlServerExtension** Azure PowerShell cmdlet：

   ```powershell-interactive
   Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
   ```

上一个命令确认已安装代理并提供常规状态信息。 可以使用以下命令获取有关自动备份和修补的特定状态信息：

   ```powershell-interactive
    $sqlext = Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
    $sqlext.AutoPatchingSettings
    $sqlext.AutoBackupSettings
   ```

## <a name="removal"></a>删除
在 Azure 门户中，可以通过选择虚拟机属性的 "**扩展**" 窗口中的省略号来卸载扩展。 然后选择“删除”。

![正在卸载 Azure 门户中的 SQL Server IaaS 代理扩展](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-uninstall.png)

还可以使用**AzVMSqlServerExtension** PowerShell cmdlet：

   ```powershell-interactive
    Remove-AzVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SqlIaasExtension"
   ```

## <a name="next-steps"></a>后续步骤
开始使用扩展支持的服务之一。 有关详细信息，请参阅本文的[支持的服务](#supported-services)部分中提到的文章。

有关在 Azure 虚拟机上运行 SQL Server 的详细信息，请参阅[Azure 虚拟机上 SQL Server 的内容](virtual-machines-windows-sql-server-iaas-overview.md)。
