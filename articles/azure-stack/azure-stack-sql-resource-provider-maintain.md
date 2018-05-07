---
title: 在 Azure Stack 中使用 SQL 数据库 | Microsoft Docs
description: 了解如何在 Azure Stack 中部署 SQL 数据库即服务，并通过便捷的步骤部署 SQL Server 资源提供程序适配器。
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
ms.date: 05/01/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: 53436d131672622ae1a72a1bb84d5aa83fdbdc0c
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2018
---
# <a name="maintenance-operations"></a>维护操作 
SQL 资源提供程序是锁定的虚拟机。 可以通过 PowerShell Just Enough Administration (JEA) 终结点 _DBAdapterMaintenance_ 更新资源提供程序虚拟机的安全性。 RP 的安装包随附了一个方便执行这些操作的脚本。

## <a name="patching-and-updating"></a>修补和更新
作为 Azure 堆栈的一部分不得到响应 SQL 资源提供程序，因为它是一个外接程序组件。 根据需要 SQL 资源提供程序，Microsoft 将提供更新。 SQL 资源提供程序实例化上_用户_默认提供程序订阅下的虚拟机。 因此，需要提供 Windows 修补程序、防病毒签名等信息。使用修补和更新周期提供的 Windows 更新包可将更新应用到 Windows VM。 发布更新的适配器后，会提供一个脚本来应用更新。 此脚本将创建新的 RP VM，并迁移现有的任何状态。

 ## <a name="backuprestoredisaster-recovery"></a>备份/还原/灾难恢复
 SQL 资源提供程序不会备份 Azure 堆栈业务连续性灾难恢复过程，因为它是一个外接程序组件。 我们会提供脚本来简化以下操作：
- 备份所需的状态信息（存储在 Azure Stack 存储帐户中）
- 在有必要恢复整个堆栈时还原 RP。
（如果有必要） 必须首先恢复数据库服务器，在资源之前提供程序将会还原。

## <a name="updating-sql-credentials"></a>更新 SQL 凭据
你需要负责在 SQL 服务器上创建和维护管理员帐户。 资源提供程序需要具有这些权限来管理代表用户的数据库的帐户-它不需要对这些数据库中的数据的访问。 如果你需要更新你的 SQL server 上的 sa 密码，你可以使用资源提供程序的管理器界面的更新功能更改资源提供程序所使用的存储的密码。 这些密码存储在 Azure Stack 实例上的 Key Vault 中。

若要修改设置，请单击“浏览”&gt;“管理资源”&gt;“SQL 宿主服务器”&gt;“SQL 登录到”并选择登录名。 必须先在 SQL 实例上（必要时还需要在任何副本上）进行更改。 在“设置”面板中单击“密码”。

![更新管理密码](./media/azure-stack-sql-rp-deploy/sqlrp-update-password.PNG)

## <a name="update-the-virtual-machine-operating-system"></a>更新虚拟机操作系统
可以通过多种方式更新 Windows Server VM：
* 使用当前进行了修补的 Windows Server 2016 Core 映像安装最新的资源提供程序包
* 在安装或更新 RP 期间安装 Windows 更新包

## <a name="update-the-virtual-machine-windows-defender-definitions"></a>更新虚拟机 Windows Defender 定义
请按以下步骤更新 Defender 定义：

1. 从 [Windows Defender 定义](https://www.microsoft.com/en-us/wdsi/definitions)下载 Windows Defender 定义更新

    在该页的“Manually download and install the definitions”（手动下载和安装定义）下，下载“适用于 Windows 10 和 Windows 8.1 的 Windows Defender 防病毒”64 位文件。 
    
    直接链接：https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64

2. 创建连接到 SQL RP 适配器虚拟机的维护终结点的 PowerShell 会话
3. 使用维护终结点会话将定义更新文件复制到 DB 适配器虚拟机
4. 在维护 PowerShell 会话中，调用 _Update-DBAdapterWindowsDefenderDefinitions_ 命令
5. 安装以后，建议删除使用过的定义更新文件。 可以在维护会话中使用 _Remove-ItemOnUserDrive)_ 命令将其删除。


下面是一个用于更新 Defender 定义的示例脚本（请将虚拟机的地址或名称替换为实际值）：

```powershell
# Set credentials for the diagnostic user
$diagPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$diagCreds = New-Object System.Management.Automation.PSCredential `
    ("dbadapterdiag", $vmLocalAdminPass)$diagCreds = Get-Credential

# Public IP Address of the DB adapter machine
$databaseRPMachine  = "XX.XX.XX.XX"
$localPathToDefenderUpdate = "C:\DefenderUpdates\mpam-fe.exe"
 
# Download Windows Defender update definitions file from https://www.microsoft.com/en-us/wdsi/definitions. 
Invoke-WebRequest -Uri https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64 `
    -Outfile $localPathToDefenderUpdate 

# Create session to the maintenance endpoint
$session = New-PSSession -ComputerName $databaseRPMachine `
    -Credential $diagCreds -ConfigurationName DBAdapterMaintenance
# Copy defender update file to the db adapter machine
Copy-Item -ToSession $session -Path $localPathToDefenderUpdate `
     -Destination "User:\mpam-fe.exe"
# Install the update file
Invoke-Command -Session $session -ScriptBlock `
    {Update-AzSDBAdapterWindowsDefenderDefinitions -DefinitionsUpdatePackageFile "User:\mpam-fe.exe"}
# Cleanup the definitions package file and session
Invoke-Command -Session $session -ScriptBlock `
    {Remove-AzSItemOnUserDrive -ItemPath "User:\mpam-fe.exe"}
$session | Remove-PSSession
```


## <a name="collect-diagnostic-logs"></a>收集诊断日志
SQL 资源提供程序是锁定的虚拟机。 如果必须从虚拟机收集日志，则会相应地提供 PowerShell Just Enough Administration (JEA) 终结点 _DBAdapterDiagnostics_。 可以通过此终结点使用两个命令：

* Get-AzsDBAdapterLog - 准备包含 RP 诊断日志的 zip 包并将其置于会话用户驱动器上。 此命令可以在不使用参数的情况下调用，将会收集过去四小时的日志。
* Remove-AzsDBAdapterLog - 清理资源提供程序 VM 上现有的日志包

在 RP 部署或更新期间会创建名为 _dbadapterdiag_ 的用户帐户，用于连接到诊断终结点以提取 RP 日志。 此帐户的密码就是在部署/更新期间为本地管理员帐户提供的密码。

若要使用这些命令，需创建一个连接到资源提供程序虚拟机的远程 PowerShell 会话，然后调用命令。 可以选择提供 FromDate 和 ToDate 参数。 如果不指定这其中的一个参数，或者两个参数都不指定，则 FromDate 为当前时间之前的四小时，ToDate 为当前时间。

以下示例脚本演示如何使用这些命令：

```powershell
# Create a new diagnostics endpoint session.
$databaseRPMachineIP = '<RP VM IP>'
$diagnosticsUserName = 'dbadapterdiag'
$diagnosticsUserPassword = '<see above>'

$diagCreds = New-Object System.Management.Automation.PSCredential `
        ($diagnosticsUserName, $diagnosticsUserPassword)
$session = New-PSSession -ComputerName $databaseRPMachineIP -Credential $diagCreds `
        -ConfigurationName DBAdapterDiagnostics

# Sample captures logs from the previous one hour
$fromDate = (Get-Date).AddHours(-1)
$dateNow = Get-Date
$sb = {param($d1,$d2) Get-AzSDBAdapterLog -FromDate $d1 -ToDate $d2}
$logs = Invoke-Command -Session $session -ScriptBlock $sb -ArgumentList $fromDate,$dateNow

# Copy the logs
$sourcePath = "User:\{0}" -f $logs
$destinationPackage = Join-Path -Path (Convert-Path '.') -ChildPath $logs
Copy-Item -FromSession $session -Path $sourcePath -Destination $destinationPackage

# Cleanup logs
$cleanup = Invoke-Command -Session $session -ScriptBlock {Remove- AzsDBAdapterLog }
# Close the session
$session | Remove-PSSession
```

## <a name="next-steps"></a>后续步骤
[添加 SQL Server 宿主服务器](azure-stack-sql-resource-provider-hosting-servers.md)
