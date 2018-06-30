---
title: 维护 Azure 堆栈上的 MySQL 资源提供程序 |Microsoft 文档
description: 了解如何维护 Azure 堆栈上的 MySQL 资源提供程序服务。
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
ms.date: 06/29/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: bc1c96d2f027d459ca20fccb70cd94ac9e5cae94
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2018
ms.locfileid: "37130132"
---
# <a name="mysql-resource-provider-maintenance-operations"></a>MySQL 资源提供程序维护操作

MySQL 资源提供程序的锁定的虚拟机上运行。 若要启用维护操作，你需要更新虚拟机的安全。 若要执行此操作使用最低特权原则，你可以使用 PowerShell Just Enough Administration (JEA) 终结点 DBAdapterMaintenance。 资源提供程序安装包包括用于此操作的脚本。

## <a name="update-the-virtual-machine-operating-system"></a>更新虚拟机操作系统

由于资源提供程序运行*用户*虚拟机，你需要在发布时应用的必需的修补程序和更新。 可以使用提供的 Windows 更新包的修补程序更新周期的一部分，将更新应用于 VM。

更新提供程序虚拟机使用以下方法之一：

- 安装最新的资源提供程序包以使用当前修补的 Windows Server 2016 Core 映像。
- 安装过程中安装 Windows 更新的包或更新资源提供程序。

## <a name="update-the-virtual-machine-windows-defender-definitions"></a>更新虚拟机 Windows Defender 定义

若要更新的 Defender 定义，请按照下列步骤：

1. Windows Defender 定义更新从下载[Windows Defender 定义](https://www.microsoft.com/en-us/wdsi/definitions)。

    在定义页上，向下滚动"手动下载和安装定义"。 下载"适用于 Windows 10 和 Windows 8.1 的 Windows Defender Antivirus"64 位文件。

    或者，使用[此直接链接](https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64)以下载/运行 fpam fe.exe 文件。

2. 打开 MySQL 资源提供程序适配器虚拟机维护终结点的 PowerShell 会话。

3. 将定义更新文件复制到资源提供程序适配器使用维护终结点会话的 VM。

4. 在维护 PowerShell 会话中上, 运行_更新 DBAdapterWindowsDefenderDefinitions_命令。

5. 安装定义后，我们建议你通过使用删除定义更新文件_删除 ItemOnUserDrive)_ 命令。

**PowerShell 脚本示例，以便更新定义。**

你可以编辑和运行以下脚本来更新 Defender 定义。 在脚本中的值替换为你环境中的值。

```powershell
# Set credentials for the local admin on the resource provider VM.
$vmLocalAdminPass = ConvertTo-SecureString "<local admin user password>" -AsPlainText -Force
$vmLocalAdminUser = "<local admin user name>"
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential `
    ($vmLocalAdminUser, $vmLocalAdminPass)

# Provide the public IP address for the adapter VM.
$databaseRPMachine  = "<RP VM IP address>"
$localPathToDefenderUpdate = "C:\DefenderUpdates\mpam-fe.exe"

# Download Windows Defender update definitions file from https://www.microsoft.com/en-us/wdsi/definitions.  
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

## <a name="secrets-rotation"></a>机密旋转

*这些说明仅适用于 Azure 堆栈集成系统版本 1804年和更高版本。请勿尝试旋转 pre 1804 版本的 Azure 堆栈的机密信息。*

当使用 SQL 和 MySQL 资源提供程序与 Azure 堆栈集成系统时，可以旋转的以下基础结构 （部署） 机密：

- 外部的 SSL 证书[在部署过程提供](azure-stack-pki-certs.md)。
- 资源提供程序 VM 本地管理员帐户提供的密码在部署过程。
- 资源提供程序诊断用户 (dbadapterdiag) 密码。

### <a name="powershell-examples-for-rotating-secrets"></a>修改机密的 PowerShell 示例

**同时更改所有密码。**

```powershell
.\SecretRotationMySQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    –DiagnosticsUserPassword $passwd `
    -DependencyFilesLocalPath $certPath `
    -DefaultSSLCertificatePassword $certPasswd `  
    -VMLocalCredential $localCreds

```

**更改诊断用户密码。**

```powershell
.\SecretRotationMySQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    –DiagnosticsUserPassword  $passwd

```

**更改 VM 的本地管理员帐户密码。**

```powershell
.\SecretRotationMySQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    -VMLocalCredential $localCreds

```

**更改的 SSL 证书密码。**

```powershell
.\SecretRotationMySQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    -DependencyFilesLocalPath $certPath `
    -DefaultSSLCertificatePassword $certPasswd

```

### <a name="secretrotationmysqlproviderps1-parameters"></a>SecretRotationMySQLProvider.ps1 参数

|参数|说明|
|-----|-----|
|AzCredential|Azure 堆栈服务管理员帐户凭据。|
|CloudAdminCredential|Azure 堆栈云管理员域帐户凭据。|
|PrivilegedEndpoint|要访问 Get AzureStackStampInformation 的特权终结点。|
|DiagnosticsUserPassword|诊断用户帐户密码。|
|VMLocalCredential|MySQLAdapter VM 上的本地管理员帐户。|
|DefaultSSLCertificatePassword|默认的 SSL 证书 (* pfx) 密码。|
|DependencyFilesLocalPath|依赖项文件的本地路径。|
|     |     |

### <a name="known-issues"></a>已知问题

**问题：**<br>
如果密钥轮换脚本失败，运行时，不会自动收集机密旋转的日志。

**解决方法：**<br>
使用 Get AzsDBAdapterLogs cmdlet 来收集所有资源提供程序日志，包括 AzureStack.DatabaseAdapter.SecretRotation.ps1_*.log，保存在 C:\Logs。

## <a name="collect-diagnostic-logs"></a>收集诊断日志

若要从锁定的虚拟机中收集日志，可以使用 PowerShell Just Enough Administration (JEA) 终结点 DBAdapterDiagnostics。 此终结点提供了以下命令：

- **Get AzsDBAdapterLog**。 此命令将创建资源提供程序诊断日志的 zip 包，并将保存会话的用户驱动器上的文件。 你可以运行此命令不带任何参数，并且最后一个四个小时的日志收集。

- **删除 AzsDBAdapterLog**。 此命令删除资源提供程序 VM 上的现有日志包。

### <a name="endpoint-requirements-and-process"></a>终结点的要求和过程

当安装或更新资源提供程序时，创建 dbadapterdiag 用户帐户。 此帐户将用于收集诊断日志。

>[!NOTE]
>Dbadapterdiag 帐户密码是用于在提供程序部署或更新期间创建的虚拟机上的本地管理员的密码相同。

若要使用_DBAdapterDiagnostics_命令，创建与资源提供程序虚拟机的远程 PowerShell 会话并运行**Get AzsDBAdapterLog**命令。

通过设置日志收集的时间跨度**FromDate**和**ToDate**参数。 如果未指定一个或两个这些参数，使用以下默认值：

* FromDate 为当前时间前四个小时。
* ToDate 是当前时间。

**有关收集日志的 PowerShell 脚本示例。**

以下脚本演示如何从 VM 的资源提供程序收集诊断日志。

```powershell
# Create a new diagnostics endpoint session.
$databaseRPMachineIP = '<RP VM IP address>'
$diagnosticsUserName = 'dbadapterdiag'
$diagnosticsUserPassword = '<Enter Diagnostic password>'
$diagCreds = New-Object System.Management.Automation.PSCredential `
        ($diagnosticsUserName, (ConvertTo-SecureString -String $diagnosticsUserPassword -AsPlainText -Force))
$session = New-PSSession -ComputerName $databaseRPMachineIP -Credential $diagCreds
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
$cleanup = Invoke-Command -Session $session -ScriptBlock {Remove-AzsDBAdapterLog}
# Close the session.
$session | Remove-PSSession

```

## <a name="next-steps"></a>后续步骤

[删除 MySQL 资源提供程序](azure-stack-mysql-resource-provider-remove.md)
