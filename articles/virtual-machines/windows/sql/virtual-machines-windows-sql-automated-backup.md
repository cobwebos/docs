---
title: "SQL Server 2014 Azure 虚拟机的自动备份 |Microsoft 文档"
description: "说明在 Azure 中运行的 SQL Server 2014 Vm 的自动备份功能。 本文是特定于使用资源管理器虚拟机。"
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: jhubbard
editor: 
tags: azure-resource-manager
ms.assetid: bdc63fd1-db49-4e76-87d5-b5c6a890e53c
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 07/05/2017
ms.author: jroth
ms.openlocfilehash: 91aab896dd5f06c950ee0ed8f36cc6a953d91611
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="automated-backup-for-sql-server-2014-virtual-machines-resource-manager"></a>自动的备份的 SQL Server 2014 虚拟机 （资源管理器）

> [!div class="op_single_selector"]
> * [SQL Server 2014](virtual-machines-windows-sql-automated-backup.md)
> * [SQL Server 2016](virtual-machines-windows-sql-automated-backup-v2.md)

自动的备份将自动配置[Managed Backup to Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx)运行 SQL Server 2014 Standard 或 Enterprise 的 Azure VM 上的所有现有和新数据库。 这使你可以配置使用持久 Azure blob 存储的定期数据库备份。 自动的备份依赖于[SQL Server IaaS 代理扩展](virtual-machines-windows-sql-server-agent-extension.md)。

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="prerequisites"></a>必备条件
若要使用自动备份，请考虑以下先决条件：

**操作系统**:

- Windows Server 2012
- Windows Server 2012 R2
- Windows Server 2016

**SQL Server 版本/版本**:

- SQL Server 2014 标准版
- SQL Server 2014 Enterprise

> [!IMPORTANT]
> 自动备份适用于 SQL Server 2014。 如果你使用的 SQL Server 2016，你可以使用自动备份 v2 以备份数据库。 有关详细信息，请参阅[自动备份 v2 的 SQL Server 2016 Azure 虚拟机](virtual-machines-windows-sql-automated-backup-v2.md)。

**数据库配置**:

- 目标数据库必须使用完整恢复模式。 有关详细信息的影响的完整恢复模式对备份，请参阅[备份完整恢复模式下的](https://technet.microsoft.com/library/ms190217.aspx)。
- 目标数据库必须位于默认 SQL Server 实例上。 SQL Server IaaS 扩展插件不支持命名的实例。

**Azure 部署模型**:

- 资源管理器

**Azure PowerShell**:

- [安装最新的 Azure PowerShell 命令](/powershell/azure/overview)如果你打算使用 PowerShell 配置自动备份。

> [!NOTE]
> 自动的备份依赖 SQL Server IaaS 代理扩展。 默认情况下，当前的 SQL 虚拟机库映像添加此扩展。 有关详细信息，请参阅[SQL Server IaaS 代理扩展](virtual-machines-windows-sql-server-agent-extension.md)。

## <a name="settings"></a>设置

下表介绍可以为自动备份配置的选项。 实际配置步骤根据你使用 Azure 门户或 Windows PowerShell 的 Azure 命令而有所不同。

| Setting | 范围 （默认值） | 描述 |
| --- | --- | --- |
| **自动的备份** | 启用/禁用 （已禁用） | 启用或禁用自动备份运行 SQL Server 2014 Standard 或 Enterprise 的 Azure VM。 |
| **保持期** | 1-30 天 （30 天） | 天数保留备份数。 |
| **存储帐户** | Azure 存储帐户 | 要在 blob 存储中存储自动备份文件的使用的 Azure 存储帐户。 在此位置以存储所有备份文件创建一个容器。 备份文件命名约定包括日期、 时间和计算机名称。 |
| **加密** | 启用/禁用 （已禁用） | 启用或禁用加密。 启用加密后，用于还原备份的证书位于指定的存储帐户在同一个`automaticbackup`容器使用的同一命名约定。 如果密码发生更改，使用该密码生成新证书，但旧证书仍会还原以前的备份。 |
| **密码** | 密码文本 | 加密密钥的密码。 这只是所需当启用了加密。 若要还原加密的备份，你必须具有正确的密码和相关的证书在备份时使用。 |

## <a name="configuration-in-the-portal"></a>在门户中的配置

可以使用 Azure 门户设置过程或现有的 SQL Server 2014 虚拟机配置自动备份。

### <a name="new-vms"></a>新的虚拟机

使用 Azure 门户配置自动备份，当在资源管理器部署模型中创建新的 SQL Server 2014 虚拟机。

在**SQL Server 设置**边栏选项卡，选择**自动备份**。 下面的 Azure 门户屏幕截图演示**SQL 自动备份**边栏选项卡。

![在 Azure 门户中的 SQL 自动备份配置](./media/virtual-machines-windows-sql-automated-backup/azure-sql-arm-autobackup.png)

上下文，请参阅完整主题上[设置 Azure 中的 SQL Server 虚拟机](virtual-machines-windows-portal-sql-server-provision.md)。

### <a name="existing-vms"></a>现有 Vm

对于现有的 SQL Server 虚拟机，选择你的 SQL Server 虚拟机。 然后选择**SQL Server 配置**部分**设置**边栏选项卡。

![针对现有的 Vm 的 SQL 自动备份](./media/virtual-machines-windows-sql-automated-backup/azure-sql-rm-autobackup-existing-vms.png)

在**SQL Server 配置**边栏选项卡，单击**编辑**自动备份部分中的按钮。

![为现有 Vm 中配置 SQL 自动备份](./media/virtual-machines-windows-sql-automated-backup/azure-sql-rm-autobackup-configuration.png)

完成后，单击**确定**底部的按钮**SQL Server 配置**边栏选项卡以保存所做的更改。

如果你首次启用自动备份，Azure 将在后台配置 SQL Server IaaS 代理。 在此期间，Azure 门户可能不会显示自动备份已配置。 等待几分钟为要安装、 配置的代理。 之后，Azure 门户将反映新设置。

> [!NOTE]
> 你还可以配置使用模板的自动备份。 有关详细信息，请参阅[自动备份的 Azure 快速入门模板](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-autobackup-update)。

## <a name="configuration-with-powershell"></a>使用 PowerShell 配置

可以使用 PowerShell 配置自动备份。 在开始之前，你必须：

- [下载并安装最新的 Azure PowerShell](http://aka.ms/webpi-azps)。
- 打开 Windows PowerShell 并将其与你的帐户关联。 你可以执行此操作中的步骤[配置你的订阅](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-ps-sql-create#configure-your-subscription)设置主题的部分。

### <a name="install-the-sql-iaas-extension"></a>安装 SQL IaaS 扩展
如果你在设置 SQL Server 虚拟机在 Azure 门户中，应已安装 SQL Server IaaS 扩展。 你可以确定它是否安装为你的 VM 通过调用**Get AzureRmVM**命令并检查**扩展**属性。

```powershell
$vmname = "vmname"
$resourcegroupname = "resourcegroupname"

(Get-AzureRmVM -Name $vmname -ResourceGroupName $resourcegroupname).Extensions
```

如果安装了 SQL Server IaaS 代理扩展，你应看到它列为"SqlIaaSAgent"或"SQLIaaSExtension"。 **ProvisioningState**扩展还应显示"已成功"。

如果它未安装或未能设置，则可以使用以下命令来安装它。 除了 VM 名称和资源组中，你还必须指定的区域 (**$region**)，你的 VM 位于中。

```powershell
$region = “EASTUS2”
Set-AzureRmVMSqlServerExtension -VMName $vmname `
    -ResourceGroupName $resourcegroupname -Name "SQLIaasExtension" `
    -Version "1.2" -Location $region
```

### <a id="verifysettings"></a>验证当前设置

如果在设置期间启用自动的备份，你可以使用 PowerShell 来检查你的当前配置。 运行**Get AzureRmVMSqlServerExtension**命令并检查**AutoBackupSettings**属性：

```powershell
(Get-AzureRmVMSqlServerExtension -VMName $vmname -ResourceGroupName $resourcegroupname).AutoBackupSettings
```

你会获得类似于以下内容的输出：

```
Enable                      : False
EnableEncryption            : False
RetentionPeriod             : -1
StorageUrl                  : NOTSET
StorageAccessKey            : 
Password                    : 
BackupSystemDbs             : False
BackupScheduleType          : 
FullBackupFrequency         : 
FullBackupStartTime         : 
FullBackupWindowHours       : 
LogBackupFrequency          : 
```

如果你的输出显示**启用**设置为**False**，则需要启用自动的备份。 好消息是你启用并配置自动备份的方式相同。 请参阅下的节，了解此信息。

> [!NOTE] 
> 如果在进行更改后立即检查设置，则可能，你会收到的旧配置值。 等待几分钟，并检查以确保所做的更改已应用的设置。

### <a name="configure-automated-backup"></a>配置自动的备份
可以使用 PowerShell 来启用自动备份也可以随时修改其配置和行为。

首先，选择或创建的备份文件的存储帐户。 以下脚本选择一个存储帐户，或创建它，如果不存在。

```powershell
$storage_accountname = “yourstorageaccount”
$storage_resourcegroupname = $resourcegroupname

$storage = Get-AzureRmStorageAccount -ResourceGroupName $resourcegroupname `
    -Name $storage_accountname -ErrorAction SilentlyContinue
If (-Not $storage)
    { $storage = New-AzureRmStorageAccount -ResourceGroupName $storage_resourcegroupname `
    -Name $storage_accountname -SkuName Standard_GRS -Location $region }
```

> [!NOTE]
> 自动的备份不支持高级存储中存储的备份，但它可以从 VM 磁盘使用高级存储的备份。

然后使用**新建 AzureRmVMSqlServerAutoBackupConfig**命令来启用和配置 Azure 存储帐户中存储备份的自动备份设置。 在此示例中，将备份设置保留 10 天。 第二个命令，**集 AzureRmVMSqlServerExtension**，使用这些设置更新指定的 Azure VM。

```powershell
$autobackupconfig = New-AzureRmVMSqlServerAutoBackupConfig -Enable `
    -RetentionPeriodInDays 10 -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname

Set-AzureRmVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

可能需要几分钟来安装和配置 SQL Server IaaS 代理。

> [!NOTE]
> 对于其他设置**新建 AzureRmVMSqlServerAutoBackupConfig** ，仅适用于 SQL Server 2016 和自动备份 v2。 SQL Server 2014 不支持以下设置： **BackupSystemDbs**， **BackupScheduleType**， **FullBackupFrequency**， **FullBackupStartHour**， **FullBackupWindowInHours**，和**LogBackupFrequencyInMinutes**。 如果你尝试在 SQL Server 2014 虚拟机上配置这些设置，不存在错误，但不要不获取应用的设置。 如果你想要在 SQL Server 2016 的虚拟机上使用这些设置，请参阅[自动备份 v2 的 SQL Server 2016 Azure 虚拟机](virtual-machines-windows-sql-automated-backup-v2.md)。

若要启用加密，请修改前面的脚本将传递**EnableEncryption**参数连同密码 （安全字符串） 一起**CertificatePassword**参数。 以下脚本启用上一示例中的自动备份设置，并添加加密。

```powershell
$password = "P@ssw0rd"
$encryptionpassword = $password | ConvertTo-SecureString -AsPlainText -Force

$autobackupconfig = New-AzureRmVMSqlServerAutoBackupConfig -Enable `
    -EnableEncryption -CertificatePassword $encryptionpassword `
    -RetentionPeriodInDays 10 -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname

Set-AzureRmVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

若要确认应用你的设置，[验证自动备份配置](#verifysettings)。

### <a name="disable-automated-backup"></a>禁用自动的备份

若要禁用自动备份，运行同一个脚本，而无需**-启用**参数**新建 AzureRmVMSqlServerAutoBackupConfig**命令。 缺少**-启用**参数发出信号，以禁用该功能的命令。 与安装一样，它可能需要几分钟，来禁用自动备份。

```powershell
$autobackupconfig = New-AzureRmVMSqlServerAutoBackupConfig -ResourceGroupName $storage_resourcegroupname

Set-AzureRmVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

### <a name="example-script"></a>示例脚本

以下脚本提供一的组可以自定义来启用和配置自动备份你的 vm 的变量。 在你的情况下，你可能需要自定义脚本基于你的要求。 例如，你将必须进行更改，如果你想要禁用的系统数据库备份或启用加密。

```powershell
$vmname = "yourvmname"
$resourcegroupname = "vmresourcegroupname"
$region = “Azure region name such as EASTUS2”
$storage_accountname = “storageaccountname”
$storage_resourcegroupname = $resourcegroupname
$retentionperiod = 10

# ResourceGroupName is the resource group which is hosting the VM where you are deploying the SQL IaaS Extension

Set-AzureRmVMSqlServerExtension -VMName $vmname `
    -ResourceGroupName $resourcegroupname -Name "SQLIaasExtension" `
    -Version "1.2" -Location $region

# Creates/use a storage account to store the backups

$storage = Get-AzureRmStorageAccount -ResourceGroupName $resourcegroupname `
    -Name $storage_accountname -ErrorAction SilentlyContinue
If (-Not $storage)
    { $storage = New-AzureRmStorageAccount -ResourceGroupName $storage_resourcegroupname `
    -Name $storage_accountname -SkuName Standard_GRS -Location $region }

# Configure Automated Backup settings

$autobackupconfig = New-AzureRmVMSqlServerAutoBackupConfig -Enable `
    -RetentionPeriodInDays $retentionperiod -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname

# Apply the Automated Backup settings to the VM

Set-AzureRmVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

## <a name="next-steps"></a>后续步骤

自动的备份 Azure 虚拟机上配置托管备份。 因此，请务必到[查看有关托管备份文档](https://msdn.microsoft.com/library/dn449496.aspx)以了解其行为和影响。

您可以找不到其他备份和还原 Azure Vm 上的 SQL Server 的以下主题中的指南：[备份和还原为 Azure 虚拟机中 SQL Server](virtual-machines-windows-sql-backup-recovery.md)。

有关其他可用的自动化任务的信息，请参阅[SQL Server IaaS 代理扩展](virtual-machines-windows-sql-server-agent-extension.md)。

有关在 Azure Vm 上运行 SQL Server 的详细信息，请参阅[SQL Server Azure 虚拟机概述](virtual-machines-windows-sql-server-iaas-overview.md)。

