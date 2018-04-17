---
title: 在 Azure 自动化混合 Runbook 辅助角色上运行 runbook
description: 本文介绍如何使用混合 Runbook 辅助角色在本地数据中心或云提供商的计算机上运行 runbook。
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 04/05/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 1db1bf6f147ae4635b0d23e84faa67dbd8f786bc
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2018
---
# <a name="running-runbooks-on-a-hybrid-runbook-worker"></a>在混合 Runbook 辅助角色上运行 runbook

运行在 Azure 自动化中的 Runbook 和运行在混合 Runbook 辅助角色上的 Runbook 没有结构上的区别。 上述两种 Runbook 使用起来可能会有很大差异，因为用于混合 Runbook 辅助角色的 runbook 通常会管理本地计算机本身的资源或其部署的本地环境中的资源，而 Azure 自动化中的 runbook 通常会管理 Azure 云中的资源。

创建 runbook 以在混合 Runbook 辅助角色上运行时，应当在承载着混合辅助角色的计算机内编辑并测试 runbook。 宿主计算机具有管理和访问本地资源时所需的所有 PowerShell 模块和网络访问权限。 在混合辅助角色计算机上编辑和测试 runbook 后，可以将它上传到 Azure 自动化环境，然后它可以在此环境中在混合辅助角色中运行。 请务必知道，作业在本地系统帐户下运行，这可能会带来细微的差别，在为混合 Runbook 辅助角色创建 runbook 时应当考虑此事项。

## <a name="starting-a-runbook-on-hybrid-runbook-worker"></a>在混合 Runbook 辅助角色中启动 runbook

[在 Azure 自动化中启动 Runbook](automation-starting-a-runbook.md) 介绍了用于启动 Runbook 的不同方法。 混合 Runbook 辅助角色增加了一个 **RunOn** 选项，可以在其中指定混合 Runbook 辅助角色组的名称。 如果指定了组，则会由该组中的辅助角色检索和运行 Runbook。 如果未指定此选项，则会在 Azure 自动化中正常运行 Runbook。

在 Azure 门户中启动 Runbook 时，会看到一个“运行位置”选项，可以在其中选择“Azure”或“混合辅助角色”。 如果选择“混合辅助角色”，则可以从下拉列表中选择该组。

使用 **RunOn** 参数。 可以使用以下命令，通过 Windows PowerShell 在名为 MyHybridGroup 的混合 Runbook 辅助角色组中启动一个名为 Test-Runbook 的 Runbook。

```powershell-interactive
Start-AzureRmAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -RunOn "MyHybridGroup"
```

> [!NOTE]
> 在 0.9.1 版的 Microsoft Azure PowerShell中，**RunOn** 参数已添加到 **Start-AzureAutomationRunbook** cmdlet。 如果安装的是旧版，则应[下载最新版本](https://azure.microsoft.com/downloads/)。 只需在要在其中通过 Windows PowerShell 启动 Runbook 的工作站上安装此版本。 不需要在辅助角色计算机上安装它，除非要从该计算机启动 Runbook。 目前还不能通过其他 Runbook 在混合 Runbook 辅助角色上启动 Runbook，因为这需要在自动化帐户中安装最新版本的 Azure Powershell。 最新版本会在 Azure 自动化中自动更新，并会快速地自动向下推送到辅助角色。

## <a name="runbook-permissions"></a>Runbook 权限

在混合 Runbook 辅助角色上运行的 Runbook 不能使用通常用于针对 Azure 资源进行 Runbook 身份验证的方法，因为它们会访问位于 Azure 之外的资源。 Runbook 可将自身的身份验证提供给本地资源，或者可以指定 RunAs 帐户以便为所有 Runbook 提供用户上下文。

### <a name="runbook-authentication"></a>Runbook 身份验证

默认情况下，在本地计算机上，Runbook 在本地系统帐户的上下文中运行，因此必须针对要访问的资源进行身份验证。

可以在包含 cmdlet 的 Runbook 中使用[凭据](automation-credentials.md)和[证书](automation-certificates.md)资产，这些 cmdlet 可以让你指定凭据，方便你向不同资源进行身份验证。 下面的示例显示了用于重新启动计算机的 Runbook 的一部分。 它从凭据资产检索凭据，从变量资产检索计算机的名称，并将这些值用于 Restart-Computer cmdlet。

```powershell-interactive
$Cred = Get-AzureRmAutomationCredential -ResourceGroupName "ResourceGroup01" -Name "MyCredential"
$Computer = Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" -Name  "ComputerName"

Restart-Computer -ComputerName $Computer -Credential $Cred
```

还可以利用 [InlineScript](automation-powershell-workflow.md#inlinescript)，以便在其他由 [PSCredential 通用参数](/powershell/module/psworkflow/about/about_workflowcommonparameters)指定凭据的计算机上运行代码块。

### <a name="runas-account"></a>RunAs 帐户

不需要让 Runbook 将自身的身份验证提供给本地资源，而可以针对混合辅助角色组指定 **RunAs** 帐户。 指定具有本地资源访问权限的[凭据资产](automation-credentials.md)，在组中的混合 Runbook 辅助角色运行时，所有 Runbook 会在这些凭据下运行。

凭据的用户名必须采用以下格式之一：

* 域\用户名
* username@domain
* 用户名（适用于本地计算机的本地帐户）

使用以下过程针对混合辅助角色组指定 RunAs 帐户：

1. 创建具有本地资源访问权限的[凭据资产](automation-credentials.md)。
2. 在 Azure 门户中打开自动化帐户。
3. 选择“混合辅助角色组”磁贴，并选择组。
4. 选择“所有设置”，并选择“混合辅助角色组设置”。
5. 将“运行身份”从“默认”更改为“自定义”。
6. 选择凭据，并单击“保存”。

### <a name="automation-run-as-account"></a>自动化运行方式帐户

在 Azure 中部署资源时，可能需要在自动生成过程中访问本地系统以支持部署过程中的某个任务或某组步骤。 需安装运行方式帐户证书，才能使用运行方式帐户针对 Azure 进行身份验证。

下面的 PowerShell Runbook（即 *Export-RunAsCertificateToHybridWorker*）将运行方式证书从 Azure 自动化帐户导出，并将其下载和导入到混合辅助角色（已连接到同一帐户）上的本地计算机证书存储。 完成该步骤后，就会验证辅助角色能否成功地使用运行方式帐户向 Azure 进行身份验证。

```powershell-interactive
<#PSScriptInfo
.VERSION 1.0
.GUID 3a796b9a-623d-499d-86c8-c249f10a6986
.AUTHOR Azure Automation Team
.COMPANYNAME Microsoft
.COPYRIGHT
.TAGS Azure Automation
.LICENSEURI
.PROJECTURI
.ICONURI
.EXTERNALMODULEDEPENDENCIES
.REQUIREDSCRIPTS
.EXTERNALSCRIPTDEPENDENCIES
.RELEASENOTES
#>

<#
.SYNOPSIS
Exports the Run As certificate from an Azure Automation account to a hybrid worker in that account.

.DESCRIPTION
This runbook exports the Run As certificate from an Azure Automation account to a hybrid worker in that account.
Run this runbook in the hybrid worker where you want the certificate installed.
This allows the use of the AzureRunAsConnection to authenticate to Azure and manage Azure resources from runbooks running in the hybrid worker.

.EXAMPLE
.\Export-RunAsCertificateToHybridWorker

.NOTES
AUTHOR: Azure Automation Team
LASTEDIT: 2016.10.13
#>

[OutputType([string])]

# Generate the password used for this certificate
Add-Type -AssemblyName System.Web -ErrorAction SilentlyContinue | Out-Null
$Password = [System.Web.Security.Membership]::GeneratePassword(25, 10)

# Stop on errors
$ErrorActionPreference = 'stop'

# Get the management certificate that will be used to make calls into Azure Service Management resources
$RunAsCert = Get-AutomationCertificate -Name "AzureRunAsCertificate"

# location to store temporary certificate in the Automation service host
$CertPath = Join-Path $env:temp  "AzureRunAsCertificate.pfx"

# Save the certificate
$Cert = $RunAsCert.Export("pfx",$Password)
Set-Content -Value $Cert -Path $CertPath -Force -Encoding Byte | Write-Verbose

Write-Output ("Importing certificate into $env:computername local machine root store from " + $CertPath)
$SecurePassword = ConvertTo-SecureString $Password -AsPlainText -Force
Import-PfxCertificate -FilePath $CertPath -CertStoreLocation Cert:\LocalMachine\My -Password $SecurePassword -Exportable | Write-Verbose

# Test that authentication to Azure Resource Manager is working
$RunAsConnection = Get-AutomationConnection -Name "AzureRunAsConnection"

Add-AzureRmAccount `
    -ServicePrincipal `
    -TenantId $RunAsConnection.TenantId `
    -ApplicationId $RunAsConnection.ApplicationId `
    -CertificateThumbprint $RunAsConnection.CertificateThumbprint | Write-Verbose

Set-AzureRmContext -SubscriptionId $RunAsConnection.SubscriptionID | Write-Verbose

# List automation accounts to confirm Azure Resource Manager calls are working
Get-AzureRmAutomationAccount | Select-Object AutomationAccountName
```

将 *Export-RunAsCertificateToHybridWorker* Runbook 保存到装有 `.ps1` 扩展的计算机。 将其导入自动化帐户中，对 Runbook 进行编辑，将变量 `$Password` 的值更改成自己的密码。 发布并运行该 Runbook，以混合辅助角色组为目标，该组使用运行方式帐户运行 Runbook 并对其进行身份验证。 作业流会报告将证书导入本地计算机存储的尝试，并且随后会附带多个行，具体取决于在订阅中定义了多少自动化帐户，以及身份验证是否成功。

## <a name="job-behavior"></a>作业行为

在混合 Runbook 辅助角色中，对作业的处理方式与作业在 Azure 沙盒中运行时略有不同。 一个主要区别是，混合 Runbook 辅助角色中对作业持续时间没有限制。 如果具有长时间运行的 runbook，则你希望确保它在可能发生的重新启动（例如，如果承载着混合辅助角色的计算机重新启动）后能够复原。 如果混合辅助角色宿主计算机重新启动，则任何正在运行的 runbook 将从头重启，或者从 PowerShell 工作流 runbook 的最后一个检查点重启。 如果某个 runbook 作业重启了 3 次以上，则它会挂起。

## <a name="troubleshooting-runbooks-on-hybrid-runbook-worker"></a>在混合 Runbook 辅助角色中排查 Runbook 问题

日志存储在每个混合辅助角色本地的 C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes 中。 混合辅助角色还会在 **Application and Services Logs\Microsoft-SMA\Operational** 下的 Windows 事件日志中记录错误和事件。 在辅助角色上执行的 runbook 相关事件会写入 Application and Services Logs\Microsoft-Automation\Operational。 Microsoft-SMA 日志包括与推送到辅助角色的 runbook 作业以及与处理 runbook 相关的更多事件。 虽然 **Microsoft 自动化**事件日志未包含许多有助于排查 runbook 执行问题的事件和详细信息，但它包含 runbook 作业的结果。

[Runbook 输出和消息](automation-runbook-output-and-messages.md)将从混合辅助角色发送到 Azure 自动化，就像云中运行的 Runbook 作业一样。 就像在其他 Runbook 中一样，还可以启用详细流和进度流。

如果 Runbook 没有成功完成且作业摘要显示的状态为“已暂停”，请参阅故障诊断文章：[混合 Runbook 辅助角色：Runbook 作业以暂停状态终止](automation-troubleshooting-hybrid-runbook-worker.md#a-runbook-job-terminates-with-a-status-of-suspended)。

## <a name="next-steps"></a>后续步骤

* 若要详细了解其他可用于启动 Runbook 的方法，请参阅[在 Azure 自动化中启动 Runbook](automation-starting-a-runbook.md)。
* 若要了解如何通过不同过程使用文本编辑器在 Azure 自动化中处理 PowerShell Runbook 和 PowerShell 工作流 Runbook，请参阅[在 Azure 自动化中编辑 Runbook](automation-edit-textual-runbook.md)
