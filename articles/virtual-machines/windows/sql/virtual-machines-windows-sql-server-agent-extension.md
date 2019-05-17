---
title: 在 SQL VM (Resource Manager) 上自动完成管理任务 | Microsoft 文档
description: 本文介绍如何管理可以自动执行特定 SQL Server 管理任务的 SQL Server 代理扩展。 这些任务包括自动备份、自动修补和 Azure 密钥保管库集成。
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-resource-manager
ms.assetid: effe4e2f-35b5-490a-b5ef-b06746083da4
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 07/12/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 2943a1501e1d81ff4884c21b5aa3861e16523bbb
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/17/2019
ms.locfileid: "65827703"
---
# <a name="automate-management-tasks-on-azure-virtual-machines-with-the-sql-server-agent-extension-resource-manager"></a>使用 SQL Server 代理扩展 (Resource Manager) 在 Azure 虚拟机上自动完成管理任务
> [!div class="op_single_selector"]
> * [资源管理器](virtual-machines-windows-sql-server-agent-extension.md)
> * [经典](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md)

SQL Server IaaS 代理扩展 (SqlIaasExtension) 在 Azure 虚拟机上运行，以自动执行管理任务。 本文概述了该扩展支持的服务以及有关安装、状态及删除的说明。

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

若要查看这篇文章的经典版，请参阅[适用于 SQL Server 经典 VM 的 SQL Server 代理扩展](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md)。

## <a name="supported-services"></a>支持的服务
SQL Server IaaS 代理扩展支持以下管理任务：

| 管理功能 | 描述 |
| --- | --- |
| **SQL 自动备份** |对 VM 中 SQL Server 的默认实例或[已正确安装的](virtual-machines-windows-sql-server-iaas-faq.md#administration)命名实例自动执行所有数据库的备份计划。 有关详细信息，请参阅 [Azure 虚拟机中 SQL Server 的自动备份 (Resource Manager)](virtual-machines-windows-sql-automated-backup.md)。 |
| **SQL 自动修补** |配置维护时段，可在此时段对 VM 进行重要的 Windows 更新，避开工作负荷的高峰期。 有关详细信息，请参阅 [Azure 虚拟机中 SQL Server 的自动修补 (Resource Manager)](virtual-machines-windows-sql-automated-patching.md)。 |
| **Azure 密钥保管库集成** |可在 SQL Server VM 上自动安装和配置 Azure Key Vault。 有关详细信息，请参阅 [为 Azure VM 上的 SQL Server 配置 Azure 密钥保管库集成 (Resource Manager)](virtual-machines-windows-ps-sql-keyvault.md)。 |

一旦安装和运行，SQL Server IaaS 代理扩展便可使这些管理功能在 Azure 门户中虚拟机的 SQL Server 面板上获得，也可通过 Azure PowerShell for SQL Server 市场映像和 Azure PowerShell 获得，以手动安装扩展。 

## <a name="prerequisites"></a>必备组件
在 VM 上使用 SQL Server IaaS 代理扩展的要求：

**操作系统**：

* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

**SQL Server 版本**：

* SQL Server 2008 
* SQL Server 2008 R2
* SQL Server 2012
* SQL Server 2014
* SQL Server 2016

**Azure PowerShell**：

* [下载和配置最新 Azure PowerShell 命令](/powershell/azure/overview)

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

> [!IMPORTANT]
> 目前，Azure 上的 SQL Server FCI 不支持 [SQL Server IaaS 代理扩展](virtual-machines-windows-sql-server-agent-extension.md)。 我们建议你从参与 FCI 的 VM 中卸载扩展。 卸载代理后，SQL VM 将无法使用扩展支持的功能。

## <a name="installation"></a>安装
预配某个 SQL Server 虚拟机库映像时，系统会自动安装 SQL Server IaaS 代理扩展。 SQL IaaS 扩展提供了有关 SQL Server VM 上的单个实例的可管理性。 如果默认实例，该扩展会使用默认实例，并且它不支持管理其他实例。 如果没有默认实例，但只有一个命名的实例，它将管理该命名的实例。 如果没有默认实例，并且有多个命名的实例，然后该扩展将无法安装。 



如果需要在其中一个 SQL Server VM 上重新手动安装扩展，请使用以下 PowerShell 命令：

```powershell
Set-AzVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SqlIaasExtension" -Version "2.0" -Location "East US 2"
```

> [!WARNING]
> 如果尚未安装该扩展，安装该扩展将会重新启动 SQL Server 服务。 但是，更新 SQL IaaS 扩展不会重新启动 SQL Server 服务。 

> [!NOTE]
> 尽管就可以安装 SQL Server IaaS 代理扩展到 SQL Server 的自定义映像，此功能是目前仅限于[更改许可证类型](virtual-machines-windows-sql-ahb.md)。 SQL IaaS 扩展提供其他功能仅适用于[SQL Server 虚拟机库映像](virtual-machines-windows-sql-server-iaas-overview.md#get-started-with-sql-vms)（即用即付或自带的自己的许可证）。

### <a name="use-a-single-named-instance"></a>使用单个命名实例
如果正确，卸载默认实例和 IaaS 扩展重新安装，SQL IaaS 扩展将使用命名实例适用于 SQL Server 映像中。

若要使用 SQL Server 的命名的实例，请执行以下操作：
   1. 从市场部署 SQL Server VM。 
   1. 在卸载 IaaS 扩展[Azure 门户](https://portal.azure.com)。
   1. 卸载 SQL Server 完全包含在 SQL Server 虚拟机。
   1. SQL Server VM 内的命名实例安装 SQL Server。 
   1. 安装从 Azure 门户中的 IaaS 扩展。  

## <a name="status"></a>状态
验证是否已安装扩展的方法之一是在 Azure 门户中查看代理状态。 在虚拟机窗口中选择“所有设置”，并单击“扩展”。 应看到列出“SqlIaasExtension”扩展。

![Azure 门户中的 SQL Server IaaS 代理扩展](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-portal.png)

也可以使用 **Get-AzVMSqlServerExtension** Azure PowerShell cmdlet。

    Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"

上一个命令确认已安装代理并提供常规状态信息。 还可使用以下命令获取有关自动备份和修补的特定状态信息。

    $sqlext = Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
    $sqlext.AutoPatchingSettings
    $sqlext.AutoBackupSettings

## <a name="removal"></a>删除
在 Azure 门户中，可以卸载扩展，通过单击省略号**扩展**窗口中的虚拟机属性。 然后单击“删除”。

![在 Azure 门户中卸载 SQL Server IaaS 代理扩展](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-uninstall.png)

也可以使用 **Remove-AzVMSqlServerExtension** PowerShell cmdlet。

    Remove-AzVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SqlIaasExtension"

## <a name="next-steps"></a>后续步骤
开始使用扩展支持的服务之一。 有关详细信息，请参阅本文的[支持的服务](#supported-services)部分中提到的文章。

有关在 Azure 虚拟机中运行 SQL Server 的详细信息，请参阅 [Azure 虚拟机中的 SQL Server 概述](virtual-machines-windows-sql-server-iaas-overview.md)。

