---
title: 在 Azure Stack 上维护 SQL 资源提供程序 | Microsoft Docs
description: 了解如何在 Azure Stack 上维护 SQL 资源提供程序服务。
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: ad899739dab1dc51d64368d2136ab87f73f6f3a0
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2018
ms.locfileid: "36300904"
---
# <a name="sql-resource-provider-maintenance-operations"></a>SQL 资源提供程序维护操作

SQL 资源提供程序在锁定的虚拟机上运行。 若要启用维护操作，您需要更新虚拟机的安全性。 若要执行此操作使用的最小特权原则，可以使用[PowerShell Just Enough Administration (JEA)](https://docs.microsoft.com/en-us/powershell/jea/overview)终结点*DBAdapterMaintenance*。 资源提供程序安装包包括用于此操作的脚本。

## <a name="patching-and-updating"></a>修补和更新

SQL 资源提供程序不提供服务作为 Azure Stack 的一部分，因为它是一个外接程序组件。 Microsoft SQL 资源提供程序根据需要提供更新。 发布更新的 SQL 适配器时，被提供一个脚本来应用此更新。 此脚本创建新的资源提供程序 VM，将旧的提供程序 VM 的状态迁移到新的 VM。 有关详细信息，请参阅[更新 SQL 资源提供程序](azure-stack-sql-resource-provider-update.md)。

### <a name="provider-virtual-machine"></a>提供程序虚拟机

因为资源提供程序上运行*用户*需要时发布时应用必需的修补程序和更新虚拟机。 可以使用提供的 Windows 更新包作为修补程序更新周期的一部分，以将更新应用到 VM。

## <a name="backuprestoredisaster-recovery"></a>备份/还原/灾难恢复

 因为它是一个外接程序组件，则 SQL 资源提供程序不被备份作为 Azure Stack 业务连续性灾难恢复 (BCDR) 过程的一部分。 脚本将提供有关以下操作：

- 备份状态信息 （存储在 Azure Stack 存储帐户。）
- 如果需要完整的堆栈恢复时，正在还原资源提供程序。

>[!NOTE]
>如果您只需恢复，在还原资源提供程序之前，必须恢复数据库服务器。

## <a name="updating-sql-credentials"></a>更新 SQL 凭据

您负责创建和维护对 SQL 服务器上的 sysadmin 帐户。 资源提供程序需要具有这些权限来管理用户的数据库的帐户，但它不需要对用户的数据的访问。 如果需要更新对 SQL 服务器上的系统管理员密码，可以使用资源提供程序的管理员界面更改存储的密码。 这些密码存储在 Azure Stack 实例上的 Key Vault 中。

若要修改的设置，请选择**浏览** &gt; **管理资源** &gt; **SQL 宿主服务器** &gt; **SQL 登录名**，并选择用户名称。 SQL 实例上必须首先进行更改 （和任何副本，如有必要。）下**设置**，选择**密码**。

![更新管理密码](./media/azure-stack-sql-rp-deploy/sqlrp-update-password.PNG)

## <a name="secrets-rotation"></a>机密轮换

*这些说明仅适用于 Azure Stack 集成系统版本 1804年和更高版本。请勿尝试轮换机密 pre 1804 Azure Stack 版本上。*

将 SQL 和 MySQL 资源提供程序与 Azure Stack 集成系统配合使用时，可以轮换以下基础结构（部署）机密：

- [部署期间提供的](azure-stack-pki-certs.md)外部 SSL 证书。
- 部署期间提供的资源提供程序 VM 本地管理员帐户密码。
- 资源提供程序诊断用户 (dbadapterdiag) 密码。

### <a name="powershell-examples-for-rotating-secrets"></a>用于轮换机密的 PowerShell 示例

**同时更改所有机密。**

```powershell
.\SecretRotationSQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    –DiagnosticsUserPassword $passwd `
    -DependencyFilesLocalPath $certPath `
    -DefaultSSLCertificatePassword $certPasswd  `
    -VMLocalCredential $localCreds
```

**更改诊断用户密码。**

```powershell
.\SecretRotationSQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    –DiagnosticsUserPassword  $passwd
```

**更改 VM 本地管理员帐户密码。**

```powershell
.\SecretRotationSQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    -VMLocalCredential $localCreds
```

**更改 SSL 证书密码。**

```powershell
.\SecretRotationSQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    -DependencyFilesLocalPath $certPath `
    -DefaultSSLCertificatePassword $certPasswd
```

### <a name="secretrotationsqlproviderps1-parameters"></a>SecretRotationSQLProvider.ps1 参数

|参数|说明|
|-----|-----|
|AzCredential|Azure Stack 服务管理员帐户凭据。|
|CloudAdminCredential|Azure Stack 云管理域帐户凭据。|
|PrivilegedEndpoint|用于访问 Get-AzureStackStampInformation 的特权终结点。|
|DiagnosticsUserPassword|诊断用户帐户密码。|
|VMLocalCredential|MySQLAdapter VM 上的本地管理员帐户。|
|DefaultSSLCertificatePassword|默认的 SSL 证书 (* pfx) 密码。|
|DependencyFilesLocalPath|依赖项文件的本地路径。|
|     |     |

### <a name="known-issues"></a>已知问题

**问题**： 机密轮换日志。<br>
如果机密轮换自定义脚本失败，将在运行时，不会自动收集机密轮换的日志。

**解决方法**:<br>
使用 Get AzsDBAdapterLogs cmdlet 来收集所有资源提供程序日志，包括 AzureStack.DatabaseAdapter.SecretRotation.ps1_*.log，保存在 C:\Logs 中。

## <a name="update-the-virtual-machine-operating-system"></a>更新虚拟机操作系统

使用以下方法之一更新虚拟机操作系统。

- 安装最新的资源提供程序包以使用当前进行了修补的 Windows Server 2016 Core 映像。
- 在安装期间安装 Windows 更新的包或更新到资源提供程序。

## <a name="update-the-virtual-machine-windows-defender-definitions"></a>更新虚拟机 Windows Defender 定义

若要更新 Windows Defender 定义：

1. 从 [Windows Defender 定义](https://www.microsoft.com/en-us/wdsi/definitions)下载 Windows Defender 定义更新

   定义更新页面上，向下滚动到"手动下载并安装定义"。 下载"Windows Defender 防病毒软件适用于 Windows 10 和 Windows 8.1"64 位文件。

   或者，使用[此直接链接](https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64)以下载/运行 fpam fe.exe 文件。

2. 创建与 SQL 资源提供程序适配器虚拟机维护终结点的 PowerShell 会话。

3. 将定义更新文件复制到虚拟机使用维护终结点会话。

4. 在维护 PowerShell 会话，运行*Update-dbadapterwindowsdefenderdefinitions*命令。

5. 安装定义后，我们建议使用删除将定义更新文件*删除 ItemOnUserDrive*命令。

**更新定义的 PowerShell 脚本示例。**

您可以编辑和运行以下脚本来更新 Defender 定义。 在脚本中的值替换为你环境中的值。

```powershell
# Set credentials for local admin on the resource provider VM.
$vmLocalAdminPass = ConvertTo-SecureString "<local admin user password>" -AsPlainText -Force
$vmLocalAdminUser = "<local admin user name>"
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential `
    ($vmLocalAdminUser, $vmLocalAdminPass)

# Provide the public IP address for the adapter VM.
$databaseRPMachine  = "<RP VM IP address>"
$localPathToDefenderUpdate = "C:\DefenderUpdates\mpam-fe.exe"

# Download the Windows Defender update definitions file from https://www.microsoft.com/en-us/wdsi/definitions.
Invoke-WebRequest -Uri 'https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64' `
    -Outfile $localPathToDefenderUpdate

# Create a session to the maintenance endpoint.
$session = New-PSSession -ComputerName $databaseRPMachine `
    -Credential $vmLocalAdminCreds -ConfigurationName DBAdapterMaintenance
# Copy the defender update file to the adapter virtual machine.
Copy-Item -ToSession $session -Path $localPathToDefenderUpdate `
     -Destination "User:\"
# Install the update definitions.
Invoke-Command -Session $session -ScriptBlock `
    {Update-AzSDBAdapterWindowsDefenderDefinition -DefinitionsUpdatePackageFile "User:\mpam-fe.exe"}
# Cleanup the definitions package file and session.
Invoke-Command -Session $session -ScriptBlock `
    {Remove-AzSItemOnUserDrive -ItemPath "User:\mpam-fe.exe"}
$session | Remove-PSSession
```

## <a name="collect-diagnostic-logs"></a>收集诊断日志

若要锁定虚拟机中收集日志，可以使用 PowerShell Just Enough Administration (JEA) 终结点*DBAdapterDiagnostics*。 此终结点提供以下命令：

- **Get-AzsDBAdapterLog**。 此命令将创建资源提供程序的诊断日志的 zip 包并将该会话的用户驱动器上的文件。 您可以运行此命令不带任何参数，并收集过去四个小时的日志。
- **Remove-AzsDBAdapterLog**。 此命令删除资源提供程序 VM 上的现有日志包。

### <a name="endpoint-requirements-and-process"></a>终结点的要求和过程

安装或更新，资源提供程序后**dbadapterdiag**创建用户帐户。 此帐户将用于收集诊断日志。

>[!NOTE]
>Dbadapterdiag 帐户密码是用于在提供程序部署或更新期间会创建虚拟机上的本地管理员的密码相同。

若要使用*DBAdapterDiagnostics*命令，创建与资源提供程序虚拟机的远程 PowerShell 会话并运行**Get-azsdbadapterlog**命令。

通过设置日志收集的时间跨度**FromDate**并**ToDate**参数。 如果未指定一个或两个参数，使用以下默认值：

- FromDate 为当前时间前四个小时。
- ToDate 为当前的时间。

**收集日志的 PowerShell 脚本示例。**

以下脚本演示如何从资源提供程序 VM 中收集诊断日志。

```powershell
# Create a new diagnostics endpoint session.
$databaseRPMachineIP = '<RP VM IP address>'
$diagnosticsUserName = 'dbadapterdiag'
$diagnosticsUserPassword = '<Enter Diagnostic password>'

$diagCreds = New-Object System.Management.Automation.PSCredential `
        ($diagnosticsUserName, (ConvertTo-SecureString -String $diagnosticsUserPassword -AsPlainText -Force))
$session = New-PSSession -ComputerName $databaseRPMachineIP -Credential $diagCreds `
        -ConfigurationName DBAdapterDiagnostics

# Sample that captures logs from the previous hour.
$fromDate = (Get-Date).AddHours(-1)
$dateNow = Get-Date
$sb = {param($d1,$d2) Get-AzSDBAdapterLog -FromDate $d1 -ToDate $d2}
$logs = Invoke-Command -Session $session -ScriptBlock $sb -ArgumentList $fromDate,$dateNow

# Copy the logs to the user drive.
$sourcePath = "User:\{0}" -f $logs
$destinationPackage = Join-Path -Path (Convert-Path '.') -ChildPath $logs
Copy-Item -FromSession $session -Path $sourcePath -Destination $destinationPackage

# Cleanup the logs.
$cleanup = Invoke-Command -Session $session -ScriptBlock {Remove- AzsDBAdapterLog }
# Close the session.
$session | Remove-PSSession
```

## <a name="next-steps"></a>后续步骤

[添加 SQL Server 宿主服务器](azure-stack-sql-resource-provider-hosting-servers.md)
