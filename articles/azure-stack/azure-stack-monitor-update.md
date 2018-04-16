---
title: 监视使用特权终结点的 Azure 堆栈中的更新 |Microsoft 文档
description: 了解如何使用特权终结点监视的集成的 Azure 堆栈系统更新状态。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 449ae53e-b951-401a-b2c9-17fee2f491f1
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2017
ms.author: mabrigg
ms.openlocfilehash: 96eebf340f13f2f5e9e922fee8032d04fce1d130
ms.sourcegitcommit: 0e1c4b925c778de4924c4985504a1791b8330c71
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/06/2018
---
# <a name="monitor-updates-in-azure-stack-using-the-privileged-endpoint"></a>监视使用特权终结点的 Azure 堆栈中的更新

*适用范围： Azure 堆栈集成系统*

你可以使用特权的终结点监视 Azure 堆栈更新运行的进度，恢复失败的更新运行最后一个成功的步骤应使用 Azure 堆栈变得不可用的门户。  使用 Azure 堆栈门户可管理 Azure 堆栈中的更新的推荐的方法。

为集成的 Azure 堆栈系统 1710年更新中包含以下新的 PowerShell cmdlet 用于更新管理。

| Cmdlet  | 说明  |
|---------|---------|
| `Get-AzureStackUpdateStatus` | 返回当前正在运行、 已完成，或失败更新的状态。 提供更新操作，并描述当前步骤和相应的状态的 XML 文档的高级的状态。 |
| `Get-AzureStackUpdateVerboseLog` | 返回生成的更新的详细日志。 |
| `Resume-AzureStackUpdate` | 恢复失败的位置的点处失败的更新。 在某些情况下，你可能需要完成缓解步骤，然后恢复更新。         |
| | |

## <a name="verify-the-cmdlets-are-available"></a>验证提供了 cmdlet
Cmdlet 不熟悉 Azure 堆栈 1710年更新包中，因为 1710年更新过程将需要获取到某个点，监视功能才可用。 通常情况下，提供了 cmdlet 如果管理员门户中的状态表示为 1710年更新**重新启动存储主机**步骤。 Cmdlet 更新期间出现的具体而言，**步骤： 运行步骤 2.6 的更新 PrivilegedEndpoint 允许列表**。

您还可以确定是否提供了 cmdlet 以编程方式通过查询从特权终结点的命令列表。 若要执行此操作，请运行以下命令，从硬件生命周期主机或特权访问工作站。 此外，请确保特权终结点是受信任的主机。 有关详细信息，请参阅第 1 步[访问特权终结点](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint)。 

1. 在任何 Azure 堆栈环境中 ERCS 虚拟机上创建的 PowerShell 会话 (*前缀*-ERCS01，*前缀*-ERCS02，或*前缀*-ERCS03)。 替换*前缀*与特定于你环境的虚拟机前缀字符串。

   ```powershell
   $cred = Get-Credential

   $pepSession = New-PSSession -ComputerName <Prefix>-ercs01 -Credential $cred -ConfigurationName PrivilegedEndpoint 
   ```
   出现提示时输入凭据，使用&lt; *Azure 堆栈域*&gt;\cloudadmin 帐户或 CloudAdmins 组的成员的帐户。 对于 CloudAdmin 帐户中，输入 AzureStackAdmin 域管理员帐户安装期间提供的相同密码。

2. 获取特权终结点中可用命令的完整列表。 

   ```powershell
   $commands = Invoke-Command -Session $pepSession -ScriptBlock { Get-Command } 
   ```
3. 确定是否已更新的特权的终结点。

   ```powershell
   $updateManagementModuleName = "Microsoft.Azurestack.UpdateManagement"
    if (($commands | ? Source -eq $updateManagementModuleName)) {
   Write-Host "Privileged endpoint was updated to support update monitoring tools."
    } else {
   Write-Host "Privileged endpoint has not been updated yet. Please try again later."
    } 
   ```

4. 列出特定于 Microsoft.AzureStack.UpdateManagement 模块的命令。

   ```powershell
   $commands | ? Source -eq $updateManagementModuleName 
   ```
   例如：
   ```powershell
   $commands | ? Source -eq $updateManagementModuleName
   
   CommandType     Name                                               Version    Source                                                  PSComputerName
    -----------     ----                                               -------    ------                                                  --------------
   Function        Get-AzureStackUpdateStatus                         0.0        Microsoft.Azurestack.UpdateManagement                   Contoso-ercs01
   Function        Get-AzureStackUpdateVerboseLog                     0.0        Microsoft.Azurestack.UpdateManagement                   Contoso-ercs01
   Function        Resume-AzureStackUpdate                            0.0        Microsoft.Azurestack.UpdateManagement                   Contoso-ercs01
   ``` 

## <a name="use-the-update-management-cmdlets"></a>使用更新管理 cmdlet

> [!NOTE]
> 从硬件生命周期主机或特权访问工作站，请运行以下命令。 此外，请确保特权终结点是受信任的主机。 有关详细信息，请参阅第 1 步[访问特权终结点](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint)。

### <a name="connect-to-the-privileged-endpoint-and-assign-session-variable"></a>连接到特权终结点并分配会话变量

运行以下命令以在任何 Azure 堆栈环境中 ERCS 虚拟机上创建的 PowerShell 会话 (*前缀*-ERCS01，*前缀*-ERCS02，或*前缀*-ERCS03)，并将会话变量分配。

```powershell
$cred = Get-Credential

$pepSession = New-PSSession -ComputerName <Prefix>-ercs01 -Credential $cred -ConfigurationName PrivilegedEndpoint 
```
 出现提示时输入凭据，使用&lt; *Azure 堆栈域*&gt;\cloudadmin 帐户或 CloudAdmins 组的成员的帐户。 对于 CloudAdmin 帐户中，输入 AzureStackAdmin 域管理员帐户安装期间提供的相同密码。

### <a name="get-high-level-status-of-the-current-update-run"></a>获取当前的更新运行的高级状态 

若要获取当前的更新运行的高级状态，请运行以下命令： 

```powershell
$statusString = Invoke-Command -Session $pepSession -ScriptBlock { Get-AzureStackUpdateStatus -StatusOnly }

$statusString.Value 
```

可能的值包括：

- 正在运行
- 已完成
- 已失败 
- 已取消

你可以运行这些命令重复以查看最新状态。 你无需重新建立连接再次检查。

### <a name="get-the-full-update-run-status-with-details"></a>获取完整的更新运行状态的详细信息 

你可以获取完整的更新运行摘要以 XML 字符串。 你可以将字符串写入文件，以检查，或将其转换为 XML 文档和 PowerShell 用于分析它。 下面的命令分析 XML 后，若要获取当前正在运行的步骤的分层列表。

```powershell
[xml]$updateStatus = Invoke-Command -Session $pepSession -ScriptBlock { Get-AzureStackUpdateStatus }

$updateStatus.SelectNodes("//Step[@Status='InProgress']")
```

在下面的示例中，顶层的步骤 （云更新） 具有子计划，以更新并重新启动存储主机。 它显示重新启动存储主机计划正在更新主机之一上的 Blob 存储服务。

```powershell
[xml]$updateStatus = Invoke-Command -Session $pepSession -ScriptBlock { Get-AzureStackUpdateStatus }

$updateStatus.SelectNodes("//Step[@Status='InProgress']") 

    FullStepIndex : 2
    Index         : 2
    Name          : Cloud Update
    Description   : Perform cloud update.
    StartTimeUtc  : 2017-10-13T12:50:39.9020351Z
    Status        : InProgress
    Task          : Task
    
    FullStepIndex  : 2.9
    Index          : 9
    Name           : Restart Storage Hosts
    Description    : Restart Storage Hosts.
    EceErrorAction : Stop
    StartTimeUtc   : 2017-10-13T15:44:06.7431447Z
    Status         : InProgress
    Task           : Task
    
    FullStepIndex : 2.9.2
    Index         : 2
    Name          : PreUpdate ACS Blob Service
    Description   : Check function level, update deployment artifacts, configure Blob service settings
    StartTimeUtc  : 2017-10-13T15:44:26.0708525Z
    Status        : InProgress
    Task          : Task
```

### <a name="get-the-verbose-progress-log"></a>获取详细的进度日志

你可以将日志写入文件，以检查。 这可以帮助你诊断更新失败。

```powershell
$log = Invoke-Command -Session $pepSession -ScriptBlock { Get-AzureStackUpdateVerboseLog }

$log > ".\UpdateVerboseLog.txt" 
```

### <a name="actively-view-the-verbose-logging"></a>主动查看详细日志记录

主动到视图的更新过程的详细日志运行，并跳转到的最新的条目，运行以下命令以在交互模式下，会话以及如何显示日志：

```powershell
Enter-PSSession -Session $pepSession 

Get-AzureStackUpdateVerboseLog -Wait 
```
日志更新每隔 60 秒，并向控制台写入新的内容 （如果可用）。 

在长时间运行的后台进程，控制台输出可能不会向控制台写入了一段时间。 若要取消的交互式输出，请按 Ctrl + C。 

### <a name="resume-a-failed-update-operation"></a>恢复失败的更新操作

如果更新失败，则可以恢复更新运行中断的位置。

```powershell
Invoke-Command -Session $pepSession -ScriptBlock { Resume-AzureStackUpdate } 
```

## <a name="troubleshoot"></a>故障排除

特权的终结点是在 Azure 堆栈环境中的所有 ERCS 虚拟机上可用。 因为没有连接到高度可用的终结点，你可能会遇到偶尔中断、 警告或错误消息。 这些消息可能表示会话已断开连接或者没有与 ECE 服务通信时出错。 此行为被预期行为。 你可以重试该操作在几分钟或其中一个其他 ERCS 虚拟机上创建新的特权的终结点会话。 

## <a name="next-steps"></a>后续步骤

- [管理 Azure 堆栈中的更新](azure-stack-updates.md) 


