---
title: 使用 PowerShell 将 Windows Server 备份到 Azure
description: 本文介绍如何使用 PowerShell 在 Windows Server 或 Windows 客户端上设置 Azure 备份，以及管理备份和恢复。
ms.topic: conceptual
ms.date: 12/2/2019
ms.openlocfilehash: fde81aba5a2b74ce25c8f3cd70dc24df6f566420
ms.sourcegitcommit: acc558d79d665c8d6a5f9e1689211da623ded90a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82597971"
---
# <a name="deploy-and-manage-backup-to-azure-for-windows-serverwindows-client-using-powershell"></a>使用 PowerShell 部署和管理 Windows Server/Windows 客户端的 Azure 备份

本文说明如何使用 PowerShell 在 Windows Server 或 Windows 客户端上设置 Azure 备份，以及管理备份和恢复。

## <a name="install-azure-powershell"></a>安装 Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

若要开始操作，请[安装最新的 PowerShell 版本](/powershell/azure/install-az-ps)。

## <a name="create-a-recovery-services-vault"></a>创建恢复服务保管库

以下步骤引导用户创建恢复服务保管库。 恢复服务保管库不同于备份保管库。

1. 首次使用 Azure 备份时，必须使用 **Register-AzResourceProvider** cmdlet 将 Azure 恢复服务提供程序注册到订阅。

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

2. 恢复服务保管库是一种 ARM 资源，因此需要将它放在资源组中。 可以使用现有资源组，也可以创建新组。 创建新的资源组时，请指定资源组的名称和位置。  

    ```powershell
    New-AzResourceGroup –Name "test-rg" –Location "WestUS"
    ```

3. 使用 **New-AzRecoveryServicesVault** cmdlet 创建新的保管库。 确保为保管库指定的位置与用于资源组的位置是相同的。

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName " test-rg" -Location "WestUS"
    ```

4. 指定要使用的存储冗余类型；可以使用[本地冗余存储 (LRS)](../storage/common/storage-redundancy-lrs.md) 或[异地冗余存储 (GRS)](../storage/common/storage-redundancy-grs.md)。 以下示例显示，testVault 的 -BackupStorageRedundancy 选项设置为 GeoRedundant。

   > [!TIP]
   > 许多 Azure 备份 cmdlet 要求使用恢复服务保管库对象作为输入。 因此，在变量中存储备份恢复服务保管库对象可提供方便。
   >
   >

    ```powershell
    $Vault1 = Get-AzRecoveryServicesVault –Name "testVault"
    Set-AzRecoveryServicesBackupProperties -Vault $Vault1 -BackupStorageRedundancy GeoRedundant
    ```

## <a name="view-the-vaults-in-a-subscription"></a>在订阅中查看保管库

使用 **Get-AzRecoveryServicesVault** 查看当前订阅中所有保管库的列表。 可以使用此命令来查看是否创建了新的保管库，或者查看订阅中的可用保管库。

运行 **Get-AzRecoveryServicesVault** 命令即可列出订阅中的所有保管库。

```powershell
Get-AzRecoveryServicesVault
```

```Output
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="installing-the-azure-backup-agent"></a>安装 Azure 备份代理

在安装 Azure 备份代理之前，必须先将安装程序下载到 Windows Server 上。 可以从 [Microsoft 下载中心](https://aka.ms/azurebackup_agent)或恢复服务保管库的“仪表板”页获取最新版本的安装程序。 将安装程序保存到方便访问的位置`C:\Downloads\*`，如。

或者，使用 PowerShell 获取下载程序：

 ```powershell
 $MarsAURL = 'https://aka.ms/Azurebackup_Agent'
 $WC = New-Object System.Net.WebClient
 $WC.DownloadFile($MarsAURL,'C:\downloads\MARSAgentInstaller.EXE')
 C:\Downloads\MARSAgentInstaller.EXE /q
 ```

若要安装代理，请在已提升权限的 PowerShell 控制台中运行以下命令：

```powershell
MARSAgentInstaller.exe /q
```

这会以所有默认选项安装代理。 安装在几分钟内在后台完成。 如果没有指定 /nu 选项，则安装结束时，会打开“Windows 更新”窗口，检查是否有任何更新   。 安装之后，代理会显示在已安装程序列表中。

若要查看已安装的程序列表，请转到“**控制面板**”“ > **程序** > ”“**程序和功能**”。

![已安装代理](./media/backup-client-automation/installed-agent-listing.png)

### <a name="installation-options"></a>安装选项

若要查看可通过命令行使用的所有选项，请使用以下命令：

```powershell
MARSAgentInstaller.exe /?
```

可用选项包括：

| 选项 | 详细信息 | 默认 |
| --- | --- | --- |
| /q |静默安装 |- |
| /p:"location" |Azure 备份代理的安装文件夹路径。 |C:\Program Files\Microsoft Azure Recovery Services Agent |
| /s:"location" |Azure 备份代理的缓存文件夹路径。 |C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch |
| /m |选择启用 Microsoft Update |- |
| /nu |安装完成后不要检查更新 |- |
| /d |卸载 Microsoft Azure 恢复服务代理 |- |
| /ph |代理主机地址 |- |
| /po |代理主机端口号 |- |
| /pu |代理主机用户名 |- |
| /pw |代理密码 |- |

## <a name="registering-windows-server-or-windows-client-machine-to-a-recovery-services-vault"></a>将 Windows Server 或 Windows 客户端计算机注册到恢复服务保管库

创建恢复服务保管库后，请下载最新的代理和保管库凭据，并将其存储在一个方便访问的位置（如 C:\Downloads）。

```powershell
$CredsPath = "C:\downloads"
$CredsFilename = Get-AzRecoveryServicesVaultSettingsFile -Backup -Vault $Vault1 -Path $CredsPath
```

### <a name="registering-using-the-ps-az-module"></a>使用 PS Az 模块注册

> [!NOTE]
> 生成保管库证书时的 bug 已在 Az 3.5.0 版中修复。 请使用 Az 3.5.0 版或更高版本下载保管库证书。

由于基本平台限制，在 PowerShell 的最新 Az 模块中，下载保管库凭据需要自签名证书。 以下示例演示如何提供自签名证书并下载保管库凭据。

```powershell
$dt = $(Get-Date).ToString("M-d-yyyy")
$cert = New-SelfSignedCertificate -CertStoreLocation Cert:\CurrentUser\My -FriendlyName 'test-vaultcredentials' -subject "Windows Azure Tools" -KeyExportPolicy Exportable -NotAfter $(Get-Date).AddHours(48) -NotBefore $(Get-Date).AddHours(-24) -KeyProtection None -KeyUsage None -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2") -Provider "Microsoft Enhanced Cryptographic Provider v1.0"
$certficate = [convert]::ToBase64String($cert.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pfx))
$CredsFilename = Get-AzRecoveryServicesVaultSettingsFile -Backup -Vault $Vault -Path $CredsPath -Certificate $certficate
```

在 Windows Server 或 Windows 客户端计算机上，运行 [Start-OBRegistration](https://docs.microsoft.com/powershell/module/msonlinebackup/start-obregistration?view=winserver2012-ps) cmdlet 以将计算机注册到保管库。
此 cmdlet 和用于备份的其他 cmdlet 都来自 Mars AgentInstaller 在安装过程中添加的 MSONLINE 模块。

代理安装程序不会更新 $Env:PSModulePath 变量。 这意味着模块自动加载失败。 若要解决此问题，可以执行以下操作：

```powershell
$Env:PSModulePath += ';C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules'
```

或者，可在脚本中手动加载模块，如下所示：

```powershell
Import-Module -Name 'C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules\MSOnlineBackup'
```

加 Online Backup cmdlet 后便可注册保管库凭据：

```powershell
Start-OBRegistration -VaultCredentials $CredsFilename.FilePath -Confirm:$false
```

```Output
CertThumbprint      : 7a2ef2caa2e74b6ed1222a5e89288ddad438df2
SubscriptionID      : ef4ab577-c2c0-43e4-af80-af49f485f3d1
ServiceResourceName : testvault
Region              : WestUS
Machine registration succeeded.
```

> [!IMPORTANT]
> 请勿使用相对路径来指定保管库凭据文件。 必须提供绝对路径作为 cmdlet 的输入。
>
>

## <a name="networking-settings"></a>网络设置

如果 Windows 计算机通过代理服务器连接到 Internet，则也可以向代理提供代理设置。 此示例未使用代理服务器，因此我们会显式清除任何与代理相关的信息。

也可以针对给定的一组星期日期，使用 `work hour bandwidth` 和 `non-work hour bandwidth` 选项控制带宽使用。

使用 [Set-OBMachineSetting](https://docs.microsoft.com/powershell/module/msonlinebackup/set-obmachinesetting?view=winserver2012-ps) cmdlet 即可设置代理和带宽详细信息：

```powershell
Set-OBMachineSetting -NoProxy
```

```Output
Server properties updated successfully.
```

```powershell
Set-OBMachineSetting -NoThrottle
```

```Output
Server properties updated successfully.
```

## <a name="encryption-settings"></a>加密设置

发送到 Azure 备份的备份数据会加密，以保护数据的机密性。 加密通行短语是在还原时用于解密数据的“密码”。

必须在 Azure 门户的“恢复服务保管库”  部分的“设置”   > “属性”   > “安全 PIN”  下选择“生成”  来生成一个安全 PIN。 

>[!NOTE]
> 只能通过 Azure 门户生成安全 PIN。

然后，将其用作命令中的 `generatedPIN`：

```powershell
$PassPhrase = ConvertTo-SecureString -String "Complex!123_STRING" -AsPlainText -Force
Set-OBMachineSetting -EncryptionPassPhrase $PassPhrase -SecurityPin "<generatedPIN>"
```

```Output
Server properties updated successfully
```

> [!IMPORTANT]
> 请妥善保管设置好的通行短语，并保证其安全。 如果没有此通行短语，将无法从 Azure 还原数据。
>
>

## <a name="back-up-files-and-folders"></a>备份文件和文件夹

从 Windows Server 和客户端到 Azure 备份的所有备份由策略控制。 策略由三个部分组成：

1. 一个**备份计划**，指定何时需要备份以及与服务同步。
2. 一个**备份计划**，指定要在 Azure 中保留恢复点多长时间。
3. 一个**文件包含/排除规范**，指示应备份哪些内容。

在本文档中，由于我们要自动备份，因此假设尚未配置任何选项。 首先，我们使用 [New-OBPolicy](https://docs.microsoft.com/powershell/module/msonlinebackup/new-obpolicy?view=winserver2012-ps) cmdlet 创建新的备份策略。

```powershell
$NewPolicy = New-OBPolicy
```

该策略暂时是空的，需要使用其他 cmdlet 来定义要包含或排除的项、运行备份的时间，以及备份的存储位置。

### <a name="configuring-the-backup-schedule"></a>配置备份计划

在策略的 3 个组成部分中，第 1 个部分是备份计划，它是使用 [New-OBSchedule](https://docs.microsoft.com/powershell/module/msonlinebackup/new-obschedule?view=winserver2012-ps) cmdlet 创建的。 备份计划将定义何时需要备份。 创建计划时，需要指定 2 个输入参数：

* 应运行备份的**星期日期**。 可以只选一天或选择一周的每天运行备份作业，或选择星期日期的任意组合。
* 应运行备份的**日期时间**。 最多可以定义一天中触发备份的三个不同时间。

例如，可以配置在每个星期六和星期日下午 4 点运行备份策略。

```powershell
$Schedule = New-OBSchedule -DaysOfWeek Saturday, Sunday -TimesOfDay 16:00
```

备份计划需要与策略相关联，这可以使用 [Set-OBSchedule](https://docs.microsoft.com/powershell/module/msonlinebackup/set-obschedule?view=winserver2012-ps) cmdlet 来实现。

```powershell
Set-OBSchedule -Policy $NewPolicy -Schedule $Schedule
```

```Output
BackupSchedule : 4:00 PM Saturday, Sunday, Every 1 week(s) DsList : PolicyName : RetentionPolicy : State : New PolicyState : Valid
```

### <a name="configuring-a-retention-policy"></a>配置保留策略

保留策略定义基于备份作业创建的恢复点的保留时间。 使用 [New-OBRetentionPolicy](https://docs.microsoft.com/powershell/module/msonlinebackup/new-obretentionpolicy?view=winserver2012-ps) cmdlet 创建新的保留策略时，可以使用 Azure 备份来指定需要保留备份恢复点的天数。 以下示例将保留策略设置为 7 天。

```powershell
$RetentionPolicy = New-OBRetentionPolicy -RetentionDays 7
```

必须使用 cmdlet [Set-OBRetentionPolicy](https://docs.microsoft.com/powershell/module/msonlinebackup/set-obretentionpolicy?view=winserver2012-ps) 将保留策略与主要策略相关联：

```powershell
Set-OBRetentionPolicy -Policy $NewPolicy -RetentionPolicy $RetentionPolicy
```

```Output
BackupSchedule  : 4:00 PM
                  Saturday, Sunday,
                  Every 1 week(s)
DsList          :
PolicyName      :
RetentionPolicy : Retention Days : 7

                  WeeklyLTRSchedule :
                  Weekly schedule is not set

                  MonthlyLTRSchedule :
                  Monthly schedule is not set

                  YearlyLTRSchedule :
                  Yearly schedule is not set

State           : New
PolicyState     : Valid
```

### <a name="including-and-excluding-files-to-be-backed-up"></a>包含和排除要备份的文件

`OBFileSpec` 对象定义要在备份中包含与排除的文件。 这组规则可划分出计算机上要保护的文件和文件夹。 可以设置任意数量的文件包含或排除规则，并将其与策略相关联。 创建新的 OBFileSpec 对象时，可以：

* 指定要包含的文件和文件夹
* 指定要排除的文件和文件夹
* 指定要递归备份文件夹中的数据，或是否仅备份指定文件夹中的顶级文件。

可以在 New-OBFileSpec 命令中使用 -NonRecursive 标志来完成后一种指定。

在以下示例中，我们要备份卷 C: 和 D:，并排除 Windows 文件夹和任何临时文件夹中的操作系统二进制文件。 为此，我们将使用[OBFileSpec](https://docs.microsoft.com/powershell/module/msonlinebackup/new-obfilespec?view=winserver2012-ps) cmdlet 创建两个文件规范-一个用于包含，一个用于排除。 创建文件规范后，使用 [Add-OBFileSpec](https://docs.microsoft.com/powershell/module/msonlinebackup/add-obfilespec?view=winserver2012-ps) cmdlet 将它们与策略相关联。

```powershell
$Inclusions = New-OBFileSpec -FileSpec @("C:\", "D:\")
$Exclusions = New-OBFileSpec -FileSpec @("C:\windows", "C:\temp") -Exclude
Add-OBFileSpec -Policy $NewPolicy -FileSpec $Inclusions
```

```Output
BackupSchedule  : 4:00 PM
                  Saturday, Sunday,
                  Every 1 week(s)
DsList          : {DataSource
                  DatasourceId:0
                  Name:C:\
                  FileSpec:FileSpec
                  FileSpec:C:\
                  IsExclude:False
                  IsRecursive:True

                  , DataSource
                  DatasourceId:0
                  Name:D:\
                  FileSpec:FileSpec
                  FileSpec:D:\
                  IsExclude:False
                  IsRecursive:True

                  }
PolicyName      :
RetentionPolicy : Retention Days : 7

                  WeeklyLTRSchedule :
                  Weekly schedule is not set

                  MonthlyLTRSchedule :
                  Monthly schedule is not set

                  YearlyLTRSchedule :
                  Yearly schedule is not set

State           : New
PolicyState     : Valid
```

```powershell
Add-OBFileSpec -Policy $NewPolicy -FileSpec $Exclusions
```

```Output
BackupSchedule  : 4:00 PM
                  Saturday, Sunday,
                  Every 1 week(s)
DsList          : {DataSource
                  DatasourceId:0
                  Name:C:\
                  FileSpec:FileSpec
                  FileSpec:C:\
                  IsExclude:False
                  IsRecursive:True
                  ,FileSpec
                  FileSpec:C:\windows
                  IsExclude:True
                  IsRecursive:True
                  ,FileSpec
                  FileSpec:C:\temp
                  IsExclude:True
                  IsRecursive:True

                  , DataSource
                  DatasourceId:0
                  Name:D:\
                  FileSpec:FileSpec
                  FileSpec:D:\
                  IsExclude:False
                  IsRecursive:True

                  }
PolicyName      :
RetentionPolicy : Retention Days : 7

                  WeeklyLTRSchedule :
                  Weekly schedule is not set

                  MonthlyLTRSchedule :
                  Monthly schedule is not set

                  YearlyLTRSchedule :
                  Yearly schedule is not set

State           : New
PolicyState     : Valid
```

### <a name="applying-the-policy"></a>应用策略

现在已完成策略对象，并且具有关联的备份计划、保留策略及文件包含/排除列表。 现在可以提交此策略以供 Azure 备份使用。 在应用新创建的策略之前，请使用[OBPolicy](https://docs.microsoft.com/powershell/module/msonlinebackup/remove-obpolicy?view=winserver2012-ps) cmdlet 确保没有任何现有备份策略与服务器相关联。 删除策略时，系统会提示确认。 若要跳过确认，请在 cmdlet 中使用 `-Confirm:$false` 标志。

```powershell
Get-OBPolicy | Remove-OBPolicy
```

```Output
Microsoft Azure Backup Are you sure you want to remove this backup policy? This will delete all the backed up data. [Y] Yes [A] Yes to All [N] No [L] No to All [S] Suspend [?] Help (default is "Y"):
```

使用 [Set-OBPolicy](https://docs.microsoft.com/powershell/module/msonlinebackup/set-obpolicy?view=winserver2012-ps) cmdlet 可以提交策略对象。 系统会提示确认。 若要跳过确认，请在 cmdlet 中使用 `-Confirm:$false` 标志。

```powershell
Set-OBPolicy -Policy $NewPolicy
```

```Output
Microsoft Azure Backup Do you want to save this backup policy ? [Y] Yes [A] Yes to All [N] No [L] No to All [S] Suspend [?] Help (default is "Y"):
BackupSchedule : 4:00 PM Saturday, Sunday, Every 1 week(s)
DsList : {DataSource
         DatasourceId:4508156004108672185
         Name:C:\
         FileSpec:FileSpec
         FileSpec:C:\
         IsExclude:False
         IsRecursive:True,

         FileSpec
         FileSpec:C:\windows
         IsExclude:True
         IsRecursive:True,

         FileSpec
         FileSpec:C:\temp
         IsExclude:True
         IsRecursive:True,

         DataSource
         DatasourceId:4508156005178868542
         Name:D:\
         FileSpec:FileSpec
         FileSpec:D:\
         IsExclude:False
         IsRecursive:True
    }
PolicyName : c2eb6568-8a06-49f4-a20e-3019ae411bac
RetentionPolicy : Retention Days : 7
              WeeklyLTRSchedule :
              Weekly schedule is not set

              MonthlyLTRSchedule :
              Monthly schedule is not set

              YearlyLTRSchedule :
              Yearly schedule is not set
State : Existing PolicyState : Valid
```

可以使用 [Get-OBPolicy](https://docs.microsoft.com/powershell/module/msonlinebackup/get-obpolicy?view=winserver2012-ps) cmdlet 来查看现有备份策略的详细信息。 可以使用 [Get-OBSchedule](https://docs.microsoft.com/powershell/module/msonlinebackup/get-obschedule?view=winserver2012-ps) cmdlet（适用于备份计划）和 [Get-OBRetentionPolicy](https://docs.microsoft.com/powershell/module/msonlinebackup/get-obretentionpolicy?view=winserver2012-ps) cmdlet（适用于保留策略）向下钻取

```powershell
Get-OBPolicy | Get-OBSchedule
```

```Output
SchedulePolicyName : 71944081-9950-4f7e-841d-32f0a0a1359a
ScheduleRunDays : {Saturday, Sunday}
ScheduleRunTimes : {16:00:00}
State : Existing
```

```powershell
Get-OBPolicy | Get-OBRetentionPolicy
```

```Output
RetentionDays : 7
RetentionPolicyName : ca3574ec-8331-46fd-a605-c01743a5265e
State : Existing
```

```powershell
Get-OBPolicy | Get-OBFileSpec
```

```Output
FileName : *
FilePath : \?\Volume{b835d359-a1dd-11e2-be72-2016d8d89f0f}\
FileSpec : D:\
IsExclude : False
IsRecursive : True

FileName : *
FilePath : \?\Volume{cdd41007-a22f-11e2-be6c-806e6f6e6963}\
FileSpec : C:\
IsExclude : False
IsRecursive : True

FileName : *
FilePath : \?\Volume{cdd41007-a22f-11e2-be6c-806e6f6e6963}\windows
FileSpec : C:\windows
IsExclude : True
IsRecursive : True

FileName : *
FilePath : \?\Volume{cdd41007-a22f-11e2-be6c-806e6f6e6963}\temp
FileSpec : C:\temp
IsExclude : True
IsRecursive : True
```

### <a name="performing-an-on-demand-backup"></a>执行按需备份

设置备份策略之后，会根据计划进行备份。 也可以使用 [Start-OBBackup](https://docs.microsoft.com/powershell/module/msonlinebackup/start-obbackup?view=winserver2012-ps) cmdlet 触发按需备份：

```powershell
Get-OBPolicy | Start-OBBackup
```

```Output
Initializing
Taking snapshot of volumes...
Preparing storage...
Generating backup metadata information and preparing the metadata VHD...
Data transfer is in progress. It might take longer since it is the first backup and all data needs to be transferred...
Data transfer completed and all backed up data is in the cloud. Verifying data integrity...
Data transfer completed
In progress...
Job completed.
The backup operation completed successfully.
```

## <a name="back-up-windows-server-system-state-in-mabs-agent"></a>在 MABS 代理中备份 Windows Server 系统状态

本部分介绍用于在 MABS 代理中设置系统状态的 PowerShell 命令

### <a name="schedule"></a>计划

```powershell
$sched = New-OBSchedule -DaysOfWeek Sunday,Monday,Tuesday,Wednesday,Thursday,Friday,Saturday -TimesOfDay 2:00
```

### <a name="retention"></a>保留

```powershell
$rtn = New-OBRetentionPolicy -RetentionDays 32 -RetentionWeeklyPolicy -RetentionWeeks 13 -WeekDaysOfWeek Sunday -WeekTimesOfDay 2:00  -RetentionMonthlyPolicy -RetentionMonths 13 -MonthDaysOfMonth 1 -MonthTimesOfDay 2:00
```

### <a name="configuring-schedule-and-retention"></a>配置计划和保留期

```powershell
New-OBPolicy | Add-OBSystemState |  Set-OBRetentionPolicy -RetentionPolicy $rtn | Set-OBSchedule -Schedule $sched | Set-OBSystemStatePolicy
 ```

### <a name="verifying-the-policy"></a>验证策略

```powershell
Get-OBSystemStatePolicy
 ```

## <a name="restore-data-from-azure-backup"></a>从 Azure 备份还原数据

本部分将引导完成自动从 Azure 备份恢复数据的步骤。 此过程涉及以下步骤：

1. 选取源卷
2. 选择要还原的备份点
3. 指定要还原的项
4. 触发还原过程

### <a name="picking-the-source-volume"></a>选取源卷

若要从 Azure 备份还原某个项，需要先识别该项的源。 由于我们要在 Windows Server 或 Windows 客户端的上下文中执行命令，因此已识别了计算机。 识别源的下一步是识别它所在的卷。 运行 [Get-OBRecoverableSource](https://docs.microsoft.com/powershell/module/msonlinebackup/get-obrecoverablesource?view=winserver2012-ps) cmdlet 可以检索正在从此计算机备份的卷或源的列表。 此命令将返回从此服务器/客户端备份的所有源的数组。

```powershell
$Source = Get-OBRecoverableSource
$Source
```

```Output
FriendlyName : C:\
RecoverySourceName : C:\
ServerName : myserver.microsoft.com

FriendlyName : D:\
RecoverySourceName : D:\
ServerName : myserver.microsoft.com
```

### <a name="choosing-a-backup-point-from-which-to-restore"></a>选择要从中还原的备份点

结合适当的参数运行 [Get-OBRecoverableItem](https://docs.microsoft.com/powershell/module/msonlinebackup/get-obrecoverableitem?view=winserver2012-ps) cmdlet 可检索备份点列表。 在本示例中，我们将选择源卷*C：* 的最新备份点，并使用它来恢复特定文件。

```powershell
$Rps = Get-OBRecoverableItem $Source[0]
$Rps
```

```Output

IsDir                : False
ItemNameFriendly     : C:\
ItemNameGuid         : \\?\Volume{297cbf7a-0000-0000-0000-401f00000000}\
LocalMountPoint      : C:\
MountPointName       : C:\
Name                 : C:\
PointInTime          : 10/17/2019 7:52:13 PM
ServerName           : myserver.microsoft.com
ItemSize             :
ItemLastModifiedTime :

IsDir                : False
ItemNameFriendly     : C:\
ItemNameGuid         : \\?\Volume{297cbf7a-0000-0000-0000-401f00000000}\
LocalMountPoint      : C:\
MountPointName       : C:\
Name                 : C:\
PointInTime          : 10/16/2019 7:00:19 PM
ServerName           : myserver.microsoft.com
ItemSize             :
ItemLastModifiedTime :
```

对象 `$Rps` 是备份点数组。 第一个元素是最新备份点，第 N 个元素是最旧的备份点。 为了选择最新的点，我们将使用 `$Rps[0]`。

### <a name="specifying-an-item-to-restore"></a>指定要还原的项

若要还原特定文件，请指定与根卷相关的文件名。 例如，若要检索 C:\Test\Cat.job，请执行以下命令。

```powershell
$Item = New-OBRecoverableItem $Rps[0] "Test\cat.jpg" $FALSE
$Item
```

```Output
IsDir                : False
ItemNameFriendly     : C:\Test\cat.jpg
ItemNameGuid         :
LocalMountPoint      : C:\
MountPointName       : C:\
Name                 : cat.jpg
PointInTime          : 10/17/2019 7:52:13 PM
ServerName           : myserver.microsoft.com
ItemSize             :
ItemLastModifiedTime : 21-Jun-14 6:43:02 AM

```

### <a name="triggering-the-restore-process"></a>触发还原过程

为了触发还原过程，首先需要指定恢复选项。 这可以使用 [New-OBRecoveryOption](https://docs.microsoft.com/powershell/module/msonlinebackup/new-obrecoveryoption?view=winserver2012-ps) cmdlet 来完成。 对于本示例，假设我们想要将文件还原到*C：\temp*。我们还假设要跳过目标文件夹*C：\temp*中已存在的文件。若要创建此类恢复选项，请使用以下命令：

```powershell
$RecoveryOption = New-OBRecoveryOption -DestinationPath "C:\temp" -OverwriteType Skip
```

现在，请对 `Get-OBRecoverableItem` cmdlet 输出中的选定 `$Item` 使用 [Start-OBRecovery](https://docs.microsoft.com/powershell/module/msonlinebackup/start-obrecovery?view=winserver2012-ps) 命令来触发还原过程：

```powershell
Start-OBRecovery -RecoverableItem $Item -RecoveryOption $RecoveryOption
```

```Output
Estimating size of backup items...
Estimating size of backup items...
Estimating size of backup items...
Estimating size of backup items...
Job completed.
The recovery operation completed successfully.
```

## <a name="uninstalling-the-azure-backup-agent"></a>卸载 Azure 备份代理

可以使用以下命令卸载 Azure 备份代理：

```powershell
.\MARSAgentInstaller.exe /d /q
```

若要从计算机中卸载代理二进制文件，请注意以下部分后果：

* 这会从计算机中删除文件筛选器，并停止跟踪更改。
* 将从计算机中删除所有策略信息，但服务中会继续存储这些策略信息。
* 将删除所有备份计划，且不会进一步创建备份。

不过，Azure 中存储的数据会根据你设置的保留策略继续保留。 较旧的恢复点会自动过时。

## <a name="remote-management"></a>远程管理

围绕 Azure 备份代理、策略和数据源的所有管理工作都可通过 Azure PowerShell 远程完成。 要远程管理的计算机需要经过适当的准备。

默认情况下，WinRM 服务已配置为手动启动。 必须将启动类型设置为“*自动*”，并且应该启动该服务。 若要确认 WinRM 服务正在运行，“状态”属性的值应该是“*正在运行*”。

```powershell
Get-Service -Name WinRM
```

```Output
Status   Name               DisplayName
------   ----               -----------
Running  winrm              Windows Remote Management (WS-Manag...
```

应该针对远程管理配置 PowerShell。

```powershell
Enable-PSRemoting -Force
```

```Output
WinRM is already set up to receive requests on this computer.
WinRM has been updated for remote management.
WinRM firewall exception enabled.
```

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force
```

现在可以远程管理计算机 - 从代理的安装开始。 例如，以下脚本会将代理复制到远程计算机并安装代理。

```powershell
$DLoc = "\\REMOTESERVER01\c$\Windows\Temp"
$Agent = "\\REMOTESERVER01\c$\Windows\Temp\MARSAgentInstaller.exe"
$Args = "/q"
Copy-Item "C:\Downloads\MARSAgentInstaller.exe" -Destination $DLoc -Force

$Session = New-PSSession -ComputerName REMOTESERVER01
Invoke-Command -Session $Session -Script { param($D, $A) Start-Process -FilePath $D $A -Wait } -ArgumentList $Agent, $Args
```

## <a name="next-steps"></a>后续步骤

有关适用于 Windows Server/客户端的 Azure 备份的详细信息，请参阅：

* [Azure 备份简介](backup-introduction-to-azure-backup.md)
* [备份 Windows Servers](backup-windows-with-mars-agent.md)
