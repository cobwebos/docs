---
title: 适用于 SQL Server 2014 Azure 虚拟机的自动备份 | Microsoft 文档
description: 介绍适用于 Azure 中运行的 SQL Server 2014 VM 的自动备份功能。 本文仅适用于使用 Resource Manager 的 VM。
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: craigg
tags: azure-resource-manager
ms.assetid: bdc63fd1-db49-4e76-87d5-b5c6a890e53c
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/03/2018
ms.author: jroth
ms.openlocfilehash: 43ce94653197933a13830003dd07e5b21be2a585
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2018
ms.locfileid: "33894993"
---
# <a name="automated-backup-for-sql-server-2014-virtual-machines-resource-manager"></a>SQL Server 2014 虚拟机 (Resource Manager) 的自动备份

> [!div class="op_single_selector"]
> * [SQL Server 2014](virtual-machines-windows-sql-automated-backup.md)
> * [SQL Server 2016/2017](virtual-machines-windows-sql-automated-backup-v2.md)

自动备份会在运行 SQL Server 2014 Standard 或 Enterprise 的 Azure VM 上，自动为所有现有数据库和新数据库配置[向 Microsoft Azure 的托管备份](https://msdn.microsoft.com/library/dn449496.aspx)。 这样，便可以配置使用持久 Azure Blob 存储的定期数据库备份。 自动备份依赖于 [SQL Server IaaS 代理扩展](virtual-machines-windows-sql-server-agent-extension.md)。

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="prerequisites"></a>先决条件
若要使用自动备份，请考虑以下先决条件：

**操作系统**：

- Windows Server 2012
- Windows Server 2012 R2
- Windows Server 2016

**SQL Server 版本**：

- SQL Server 2014 Standard
- SQL Server 2014 Enterprise

> [!IMPORTANT]
> 自动备份适用于 SQL Server 2014。 如果使用的是 SQL Server 2016/2017，可使用自动备份 v2 来备份数据库。 有关详细信息，请参阅 [Automated Backup v2 for SQL Server 2016 Azure Virtual Machines](virtual-machines-windows-sql-automated-backup-v2.md)（适用于 SQL Server 2016 Azure 虚拟机的自动备份 v2）。

**数据库配置**：

- 目标数据库必须使用完整恢复模式。 有关对备份使用完整恢复模型产生的影响的详细信息，请参阅 [Backup Under the Full Recovery Model](https://technet.microsoft.com/library/ms190217.aspx)（使用完整恢复模型的备份）。
- 目标数据库必须位于默认 SQL Server 实例上。 SQL Server IaaS 扩展不支持命名的实例。

> [!NOTE]
> 自动备份依赖 SQL Server IaaS 代理扩展。 当前的 SQL 虚拟机库映像默认添加此扩展。 有关详细信息，请参阅 [SQL Server IaaS 代理扩展](virtual-machines-windows-sql-server-agent-extension.md)。

## <a name="settings"></a>设置

下表描述了可为自动备份配置的选项。 实际配置步骤根据你使用的是 Azure 门户还是 Azure Windows PowerShell 命令而有所不同。

| 设置 | 范围（默认值） | 说明 |
| --- | --- | --- |
| **自动备份** | 启用/禁用（已禁用） | 为运行 SQL Server 2014 Standard 或 Enterprise 的 Azure VM 启用或禁用自动备份。 |
| **保留期** | 1-30 天（30 天） | 保留备份的天数。 |
| **存储帐户** | Azure 存储帐户 | 用于在 Blob 存储中存储自动备份文件的 Azure 存储帐户。 在此位置创建容器，用于存储所有备份文件。 备份文件命名约定包括日期、时间和计算机名称。 |
| **加密** | 启用/禁用（已禁用） | 启用或禁用加密。 启用加密时，用于还原备份的证书使用相同的命名约定存放在同一 `automaticbackup` 容器中的指定存储帐户内。 如果密码发生更改，将使用该密码生成新证书，但旧证书在备份之前仍会还原。 |
| **密码** | 密码文本 | 加密密钥的密码。 仅当启用了加密时才需要此设置。 若要还原加密的备份，必须具有创建该备份时使用的正确密码和相关证书。 |

## <a name="configure-in-the-portal"></a>在门户中配置

可以在预配期间或针对现有的 SQL Server 2014 VM 使用 Azure 门户来配置自动备份。

## <a name="configure-new-vms"></a>配置新 VM

在 Resource Manager 部署模型中创建新的 SQL Server 2014 虚拟机时，可以使用 Azure 门户配置自动备份。

在“SQL Server 设置”窗格中，选择“自动备份”。 下面的 Azure 门户屏幕截图显示了“SQL 自动备份”设置。

![Azure 门户中的 SQL 自动备份配置](./media/virtual-machines-windows-sql-automated-backup/azure-sql-arm-autobackup.png)

## <a name="configure-existing-vms"></a>配置现有 VM

对于现有的 SQL Server 虚拟机，请选择 SQL Server 虚拟机。 然后选择 VM“设置”的“SQL Server 配置”部分。

![现有 VM 的 SQL 自动备份](./media/virtual-machines-windows-sql-automated-backup/azure-sql-rm-autobackup-existing-vms.png)

在“SQL Server 配置”窗格的“自动备份”部分，单击“编辑”按钮。

![为现有 VM 配置 SQL 自动备份](./media/virtual-machines-windows-sql-automated-backup/azure-sql-rm-autobackup-configuration.png)

完成后，单击“SQL Server 配置”设置底部的“确定”按钮保存更改。

首次启用自动备份时，Azure 会在后台配置 SQL Server IaaS 代理。 在此期间，Azure 门户可能不会显示自动备份已配置。 请等待几分钟，以便安装和配置代理。 之后，Azure 门户将反映新设置。

> [!NOTE]
> 也可以使用模板来配置自动备份。 有关详细信息，请参阅 [Azure quickstart template for Automated Backup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-autobackup-update)（用于自动备份的 Azure 快速入门模板）。

## <a name="configure-with-powershell"></a>使用 PowerShell 进行配置

可使用 PowerShell 配置自动备份。 开始之前，必须：

- [下载并安装最新的 Azure PowerShell](http://aka.ms/webpi-azps)。
- 打开 Windows PowerShell 并使用 Connect-AzureRmAccount 命令将其与帐户关联。

### <a name="install-the-sql-iaas-extension"></a>安装 SQL IaaS 扩展
如果通过 Azure 门户预配了 SQL Server 虚拟机，应已安装 SQL Server IaaS 扩展。 可通过调用 **Get-AzureRmVM** 命令并检查 **Extensions** 属性，来确定是否为 VM 安装了该扩展。

```powershell
$vmname = "vmname"
$resourcegroupname = "resourcegroupname"

(Get-AzureRmVM -Name $vmname -ResourceGroupName $resourcegroupname).Extensions
```

如果已安装 SQL Server IaaS 代理扩展，应会看到列出的“SqlIaaSAgent”或“SQLIaaSExtension”。 此外，该扩展的 **ProvisioningState** 应显示“Succeeded”。

如果未安装或未能预配该扩展，可使用以下命令来安装。 除了 VM 名称和资源组以外，还必须指定 VM 所在的区域 (**$region**)。

```powershell
$region = "EASTUS2"
Set-AzureRmVMSqlServerExtension -VMName $vmname `
    -ResourceGroupName $resourcegroupname -Name "SQLIaasExtension" `
    -Version "1.2" -Location $region
```

> [!IMPORTANT]
> 如果尚未安装该扩展，安装该扩展将会重新启动 SQL Server 服务。

### <a id="verifysettings"></a> 验证当前设置

如果在预配期间启用了自动备份，可以使用 PowerShell 检查当前配置。 运行 **Get-AzureRmVMSqlServerExtension** 命令并检查 **AutoBackupSettings** 属性：

```powershell
(Get-AzureRmVMSqlServerExtension -VMName $vmname -ResourceGroupName $resourcegroupname).AutoBackupSettings
```

应会看到类似于下面的输出：

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

如果输出显示 **Enable** 设置为 **False**，则必须启用自动备份。 幸运的是，可通过相同的方式启用和配置自动备份。 有关信息，请参阅下一部分。

> [!NOTE] 
> 如果在进行更改后立即检查设置，看到的可能是旧配置值。 请等待几分钟再检查设置，确保更改已应用。

### <a name="configure-automated-backup"></a>配置自动备份
随时可以使用 PowerShell 来启用自动备份以及修改其配置和行为。

首先，为备份文件选择或创建存储帐户。 以下脚本将选择一个存储帐户，或者创建一个存储帐户（如果不存在）。

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
> 自动备份不支持在高级存储中存储备份，但可以从使用高级存储的 VM 磁盘创建备份。

然后，使用 New-AzureRmVMSqlServerAutoBackupConfig 命令启用并配置自动备份设置，以便在 Azure 存储帐户中存储备份。 在本示例中，备份保留 10 天。 第二个命令 **Set-AzureRmVMSqlServerExtension** 使用这些设置更新指定的 Azure VM。

```powershell
$autobackupconfig = New-AzureRmVMSqlServerAutoBackupConfig -Enable `
    -RetentionPeriodInDays 10 -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname

Set-AzureRmVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

可能需要花费几分钟来安装和配置 SQL Server IaaS 代理。

> [!NOTE]
> 还有仅适用于 SQL Server 2016 和自动备份 v2 的其他 New-AzureRmVMSqlServerAutoBackupConfig 设置。 SQL Server 2014 不支持以下设置：BackupSystemDbs、BackupScheduleType、FullBackupFrequency、FullBackupStartHour、FullBackupWindowInHours 和 LogBackupFrequencyInMinutes。 如果尝试在 SQL Server 2014 虚拟机上配置这些设置，则不存在错误，但不会应用这些设置。 若要在 SQL Server 2016 虚拟机上使用这些设置，请参阅[适用于 SQL Server 2016 Azure 虚拟机的自动备份 v2](virtual-machines-windows-sql-automated-backup-v2.md)。

要启用加密，请修改上述脚本，使其将 **EnableEncryption** 参数连同 **CertificatePassword** 参数的密码（安全字符串）一起传递。 以下脚本启用上一示例中的自动备份设置，并添加加密。

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

若要确认是否应用了这些设置，请[检查自动备份配置](#verifysettings)。

### <a name="disable-automated-backup"></a>禁用自动备份

若要禁用自动备份，请运行同一个脚本，但不要为 **New-AzureRmVMSqlServerAutoBackupConfig** 命令指定 **-Enable** 参数。 缺少 **-Enable** 参数将向该命令发出指示以禁用此功能。 与安装一样，可能需要花费几分钟时间来禁用自动备份。

```powershell
$autobackupconfig = New-AzureRmVMSqlServerAutoBackupConfig -ResourceGroupName $storage_resourcegroupname

Set-AzureRmVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

### <a name="example-script"></a>示例脚本

以下脚本提供一组可自定义的变量，用来为 VM 启用和配置自动备份。 根据具体的情况，可能需要根据要求自定义该脚本。 例如，如果想要禁用系统数据库备份或启用加密，则必须更改该脚本。

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

## <a name="monitoring"></a>监视

可通过两种主要方式监视 SQL Server 2014 上的自动备份。 由于自动备份使用 SQL Server 托管备份功能，同样的监视方法对两者均适用。

首先，可通过调用 [msdb.smart_admin.sp_get_backup_diagnostics](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/managed-backup-sp-get-backup-diagnostics-transact-sql) 轮询状态。 或查询 [msdb.smart_admin.fn_get_health_status](https://docs.microsoft.com/sql/relational-databases/system-functions/managed-backup-fn-get-health-status-transact-sql) 表值函数。

> [!NOTE]
> 适用于 SQL Server 2014 中托管备份的架构是 msdb.smart_admin。 在 SQL Server 2016 中，架构需更改为 msdb.managed_backup，且参考主题使用此较新架构。 但对于 SQL Server 2014，必须为所有托管备份对象继续使用 smart_admin 架构。

另一种方式是利用内置的数据库邮件功能进行通知。

1. 调用 [msdb.smart_admin.sp_set_parameter](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/managed-backup-sp-set-parameter-transact-sql) 存储过程，向 SSMBackup2WANotificationEmailIds 参数分配电子邮件地址。 
1. 启用 [SendGrid](../../../sendgrid-dotnet-how-to-send-email.md)，从 Azure VM 发送电子邮件。
1. 使用 SMTP 服务器和用户名配置数据库邮件。 可在 SQL Server Management Studio 中或使用 Transact-SQL 命令配置数据库邮件。 有关详细信息，请参阅[数据库邮件](https://docs.microsoft.com/sql/relational-databases/database-mail/database-mail)。
1. [配置 SQL Server 代理以使用数据库邮件](https://docs.microsoft.com/sql/relational-databases/database-mail/configure-sql-server-agent-mail-to-use-database-mail)。
1. 验证是否通过本地 VM 防火墙和适用于 VM 的网络安全组允许该 SMTP 端口。

## <a name="next-steps"></a>后续步骤

自动备份会在 Azure VM 上配置托管备份。 因此，请务必[查看有关 SQL Server 2014 托管备份的文档](https://msdn.microsoft.com/library/dn449497(v=sql.120).aspx)。

可以在下文中找到针对 Azure VM 上的 SQL Server 的其他备份和还原指导：[Azure 虚拟机中 SQL Server 的备份和还原](virtual-machines-windows-sql-backup-recovery.md)。

有关其他可用自动化任务的信息，请参阅 [SQL Server IaaS 代理扩展](virtual-machines-windows-sql-server-agent-extension.md)。

有关在 Azure VM 中运行 SQL Server 的详细信息，请参阅 [Azure 虚拟机中的 SQL Server 概述](virtual-machines-windows-sql-server-iaas-overview.md)。
