---
title: 利用 IaaS 代理扩展自动执行管理任务
description: 本文介绍如何管理可以自动执行特定 SQL Server 管理任务的 SQL Server IaaS 代理扩展。 这些任务包括自动备份、自动修补和 Azure 密钥保管库集成。
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
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
ms.custom: seo-lt-2019
ms.openlocfilehash: 67df782cf8044593859e7761278d93187d258d26
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2020
ms.locfileid: "86231924"
---
# <a name="automate-management-tasks-on-azure-virtual-machines-by-using-the-sql-server-iaas-agent-extension"></a>使用 SQL Server IaaS 代理扩展在 Azure 虚拟机上自动完成管理任务
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]


SQL Server IaaS 代理扩展 (SqlIaasExtension) 在 Azure 虚拟机上运行，以自动执行管理任务。 本文概述了该扩展支持的服务概述。 本文还提供了有关安装、状态和删除扩展的说明。

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

若要查看这篇文章的经典版，请参阅[适用于 SQL Server VM（经典）的 SQL Server 代理扩展](../../../virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md)。


## <a name="supported-services"></a>支持的服务
SQL Server IaaS 代理扩展支持以下管理任务：

| 管理功能 | 说明 |
| --- | --- |
| **SQL Server 自动备份** |对 VM 中的 SQL Server 默认实例或已[正确安装](frequently-asked-questions-faq.md#administration)的命名实例自动执行所有数据库的备份计划。 有关详细信息，请参阅 [Azure 虚拟机中 SQL Server 的自动备份 (Resource Manager)](automated-backup-sql-2014.md)。 |
| **SQL Server 自动修补** |配置维护时段，可在此时段对 VM 进行重要的 Windows 更新，避开工作负荷的高峰期。 有关详细信息，请参阅 [Azure 虚拟机中 SQL Server 的自动修补 (Resource Manager)](automated-patching.md)。 |
| **Azure 密钥保管库集成** |可让你在 SQL Server VM 上自动安装和配置 Azure 密钥保管库。 有关详细信息，请参阅 [为 Azure 虚拟机上的 SQL Server 配置 Azure 密钥保管库集成 (Resource Manager)](azure-key-vault-integration-configure.md)。 |

安装并运行 SQL Server Iaas 代理扩展后，它将使管理功能可用：

* 在 Azure 门户的虚拟机的 SQL Server 面板中，通过 Azure 市场上 SQL Server 映像的 Azure PowerShell。
* 通过 Azure PowerShell 手动安装扩展。 

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
* SQL Server 2019

**Azure PowerShell**：

* [下载和配置最新 Azure PowerShell 命令](/powershell/azure/overview)

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]


##  <a name="installation"></a>安装
将 SQL Server VM 注册到[SQL Server VM 资源提供程序](sql-vm-resource-provider-register.md)时，会安装 SQL Server IaaS 扩展。 如有必要，可以使用以下 PowerShell 命令手动安装 SQL Server IaaS 代理： 

  ```powershell-interactive
    Set-AzVMSqlServerExtension -VMName "sql2017" `
    -ResourceGroupName "LabsqlIAASagent" -Name "SQLIaasExtension" `
    -Version "2.0" -Location "Central US";  
  ```

> [!NOTE]
> 安装扩展会重新启动 SQL Server 服务。 


### <a name="install-on-a-vm-with-a-single-named-sql-server-instance"></a>在具有单个命名 SQL Server 实例的 VM 上安装
如果卸载了默认实例并且重新安装了 IaaS 扩展，则 SQL Server IaaS 扩展将与 SQL Server 上的命名实例一起使用。

若要使用 SQL Server 的命名实例，请执行以下步骤：
   1. 从 Azure 市场部署 SQL Server VM。 
   1. 从 [Azure 门户](https://portal.azure.com)中卸载 IaaS 扩展。
   1. 完全卸载 SQL Server VM 中的 SQL Server。
   1. 使用 SQL Server VM 中的命名实例安装 SQL Server。 
   1. 从 Azure 门户中安装 IaaS 扩展。  


## <a name="get-the-status-of-the-sql-server-iaas-extension"></a>获取 SQL Server IaaS 扩展的状态
验证是否已安装扩展的方法之一是在 Azure 门户中查看代理状态。 在虚拟机窗口中选择“所有设置”，然后选择“扩展”。  应看到列出“SqlIaasExtension”扩展。

![Azure 门户中 SQL Server IaaS 代理扩展的状态](./media/sql-server-iaas-agent-extension-automate-management/azure-rm-sql-server-iaas-agent-portal.png)

也可以使用 **Get-AzVMSqlServerExtension** Azure PowerShell cmdlet：

   ```powershell-interactive
   Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
   ```

上一个命令确认已安装代理并提供常规状态信息。 还可使用以下命令获取有关自动备份和修补的特定状态信息：

   ```powershell-interactive
    $sqlext = Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
    $sqlext.AutoPatchingSettings
    $sqlext.AutoBackupSettings
   ```

## <a name="removal"></a>删除
在 Azure 门户中，可以通过选择虚拟机属性的“扩展”窗口中的省略号来卸载扩展。 然后选择“删除”。

![在 Azure 门户中卸载 SQL Server IaaS 代理扩展](./media/sql-server-iaas-agent-extension-automate-management/azure-rm-sql-server-iaas-agent-uninstall.png)

也可以使用 **Remove-AzVMSqlServerExtension** PowerShell cmdlet：

   ```powershell-interactive
    Remove-AzVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SqlIaasExtension"
   ```

## <a name="next-steps"></a>后续步骤
开始使用扩展支持的服务之一。 有关信息，请参阅本文的[支持的服务](#supported-services)部分中提到的文章。

有关在 Azure 虚拟机中运行 SQL Server 的详细信息，请参阅[什么是 Azure 虚拟机中的 SQL Server？](sql-server-on-azure-vm-iaas-what-is-overview.md)。
