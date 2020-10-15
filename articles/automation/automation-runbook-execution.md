---
title: 在 Azure 自动化中执行 Runbook
description: 本文概述了 Azure 自动化中 runbook 的处理过程。
services: automation
ms.subservice: process-automation
ms.date: 10/06/2020
ms.topic: conceptual
ms.openlocfilehash: 6ac7d99f4a47711f9974d30d877a3237eec15443
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2020
ms.locfileid: "92078827"
---
# <a name="runbook-execution-in-azure-automation"></a>在 Azure 自动化中执行 Runbook

借助 Azure 自动化中的流程自动化，你可以创建并管理 PowerShell、PowerShell 工作流和图形 runbook。 有关详细信息，请参阅 [Azure 自动化 runbook](automation-runbook-types.md)。 

自动化根据 runbook 内部定义的逻辑来执行 runbook。 如果 runbook 中断，则会从开始处重启。 此行为要求编写在发生暂时性问题时支持重启的 runbook。

在 Azure 自动化中启动 runbook 会创建一个作业，该作业是 runbook 的单个执行实例。 每个作业都通过连接到 Azure 订阅来访问 Azure 资源。 仅当数据中心内的资源可从公有云访问时，作业才能访问这些资源。

Azure 自动化分配辅助角色用于在 runbook 执行期间运行每个作业。 尽管辅助角色由多个 Azure 帐户共享，但不同自动化帐户中的作业是相互独立的。 你无法控制作业请求的辅助角色服务。

在 Azure 门户中查看 runbook 列表时，列表会显示已为每个 runbook 启动的每个作业的状态。 Azure 自动化最多将作业日志存储 30 天。

下图显示 [PowerShell runbook](automation-runbook-types.md#powershell-runbooks)、[PowerShell 工作流 runbook](automation-runbook-types.md#powershell-workflow-runbooks) 和[图形 runbook](automation-runbook-types.md#graphical-runbooks) 的 runbook 作业的生命周期。

![作业状态 - PowerShell 工作流](./media/automation-runbook-execution/job-statuses.png)

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="runbook-execution-environment"></a>Runbook 执行环境

Azure 自动化中的 runbook 可以在 Azure 沙盒上运行，也可以在[混合 Runbook 辅助角色](automation-hybrid-runbook-worker.md)上运行。 

如果 runbook 设计为针对 Azure 中的资源进行身份验证和运行，则它们会在 Azure 沙盒中运行，这是多个作业可以使用的共享环境。 使用同一沙盒的作业受沙盒的资源限制约束。 Azure 沙盒环境不支持交互式操作。 它阻止访问所有进程外 COM 服务器。 它还要求将本地 MOF 文件用于进行 Win32 调用的 runbook。

你也可以使用[混合 Runbook 辅助角色](automation-hybrid-runbook-worker.md)直接在托管角色的计算机上运行 runbook，以及针对环境中的资源运行 runbook。 Azure 自动化存储并管理 runbook，然后将其发送到一个或多个分配的计算机。

>[!NOTE]
>若要在 Linux 混合 Runbook 辅助角色上运行，必须对脚本进行签名并相应配置辅助角色。 或者，[必须关闭签名验证](automation-linux-hrw-install.md#turn-off-signature-validation)。

下表列出一些 runbook 执行任务，并为每个任务列出了建议的执行环境。

|任务|建议|说明|
|---|---|---|
|与 Azure 资源集成|Azure 沙盒|在 Azure 中托管，身份验证更为简单。 如果使用 Azure VM 上的混合 Runbook 辅助角色，则可[将 runbook 身份验证与托管标识配合使用](automation-hrw-run-runbooks.md#runbook-auth-managed-identities)。|
|获取管理 Azure 资源的最佳性能|Azure 沙盒|脚本在同一环境中运行，延迟更低。|
|最大程度减少运营成本|Azure 沙盒|没有计算开销，且不需要 VM。|
|执行长时间运行的脚本|混合 Runbook 辅助角色|Azure 沙盒具有[资源限制](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)。|
|与本地服务进行交互|混合 Runbook 辅助角色|直接访问主机，或其他云环境或本地环境中的资源。 |
|需要第三方软件和可执行文件|混合 Runbook 辅助角色|管理操作系统并且可以安装软件。|
|使用 Runbook 监视文件或文件夹|混合 Runbook 辅助角色|在混合 Runbook 辅助角色上，使用[观察程序任务](automation-watchers-tutorial.md)。|
|运行资源密集型脚本|混合 Runbook 辅助角色| Azure 沙盒具有[资源限制](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)。|
|使用具有特定要求的模块| 混合 Runbook 辅助角色|一些示例如下：</br> WinSCP - winscp.exe 上的依赖项 </br> IIS 管理 - 用于启用或管理 IIS 的依赖项|
|使用安装程序安装模块|混合 Runbook 辅助角色|沙盒模块必须支持复制。|
|使用需要 4.7.2 以外版本的 .NET Framework 的 runbook 或模块|混合 Runbook 辅助角色|Azure 沙盒支持 .NET Framework 4.7.2，并且不支持升级到其他版本。|
|运行需要提升的脚本|混合 Runbook 辅助角色|沙盒不允许提升。 借助混合 Runbook 辅助角色，可以在运行需要提升的命令时关闭 UAC 并使用 [Invoke-Command](/powershell/module/microsoft.powershell.core/invoke-command)。|
|运行需要访问 Windows Management Instrumentation (WMI) 的脚本|混合 Runbook 辅助角色|在云中的沙盒中运行的作业无法访问 WMI 提供程序。 |

## <a name="temporary-storage-in-a-sandbox"></a>沙盒中的临时存储

如果需要在 runbook 逻辑中创建临时文件，可以使用 Temp 文件夹 (即， `$env:TEMP` 在 azure 沙盒中) 在 azure 中运行的 runbook。 唯一的限制是不能使用超过 1 GB 的磁盘空间，这是每个沙箱的配额。 使用 PowerShell 工作流时，这种情况可能会导致出现问题，因为 PowerShell 工作流使用检查点，并且可能会在不同的沙箱中重试该脚本。

使用混合沙盒，可以 `C:\temp` 基于混合 Runbook 辅助角色上的存储可用性使用。 但是，根据 Azure VM 的建议，不应在 Windows 或 Linux 上使用 [临时磁盘](../virtual-machines/managed-disks-overview.md#temporary-disk) 来查找需要保存的数据。

## <a name="resources"></a>资源

Runbook 必须包含用于处理[资源](/rest/api/resources/resources)（例如，VM、网络和网络上的资源）的逻辑。 资源绑定到 Azure 订阅，且 runbook 需要适当的凭据才能访问任何资源。 有关在 runbook 中处理资源的示例，请参阅[处理资源](manage-runbooks.md#handle-resources)。

## <a name="security"></a>安全性

Azure 自动化使用 [Azure 安全中 (ASC)](../security-center/security-center-introduction.md) 保护你的资源以及检测 Linux 系统中的漏洞。 无论资源是否在 Azure 中，均可跨工作负荷提供安全性。 请参阅 [Azure 自动化中的身份验证简介](automation-security-overview.md)。

ASC 对可以在 VM 上运行任何签名或未签名脚本的用户施加限制。 如果你是具有 VM 根访问权限的用户，则必须使用数字签名显式配置计算机或将其关闭。 否则，只有在创建自动化帐户并启用相应功能后才能通过运行脚本应用操作系统更新。

## <a name="subscriptions"></a>订阅

Azure [订阅](/office365/enterprise/subscriptions-licenses-accounts-and-tenants-for-microsoft-cloud-offerings)是与 Microsoft 达成的使用一项或多项基于云的服务的协议，并会为此向你收费。 对于 Azure 自动化，每个订阅都链接到一个 Azure 自动化帐户，你可以在帐户中[创建多个订阅](manage-runbooks.md#work-with-multiple-subscriptions)。

## <a name="credentials"></a>凭据

Runbook 需要适当[凭据](shared-resources/credentials.md)才能访问任何资源，无论是用于 Azure 还是第三方系统。 这些凭据存储在 Azure 自动化、密钥保管库等中。  

## <a name="azure-monitor"></a>Azure Monitor

Azure 自动化利用 [Azure Monitor](../azure-monitor/overview.md) 来监视其计算机操作。 操作需要 Log Analytics 工作区和 [Log Analytics 代理](../azure-monitor/platform/log-analytics-agent.md)。

### <a name="log-analytics-agent-for-windows"></a>适用于 Windows 的 Log Analytics 代理

[适用于 Windows 的 Log Analytics 代理](../azure-monitor/platform/agent-windows.md)可与 Azure Monitor 配合使用，用于管理 Windows VM 和物理计算机。 这些计算机可以在 Azure 或非 Azure 环境（例如本地数据中心）中运行。

>[!NOTE]
>适用于 Windows 的 Log Analytics 代理之前称为 Microsoft Monitoring Agent (MMA)。

### <a name="log-analytics-agent-for-linux"></a>适用于 Linux 的 Log Analytics 代理

[适用于 Linux 的 Log Analytics 代理](../azure-monitor/platform/agent-linux.md)与适用于 Windows 的代理工作原理类似，但它将 Linux 计算机连接到 Azure Monitor。 使用 **nxautomation** 用户帐户安装代理，该用户帐户允许执行需要根权限的命令，例如，在混合 Runbook 辅助角色上执行。 nxautomation 帐户是不需要密码的系统帐户。

在[安装 Linux 混合 Runbook 辅助角色](automation-linux-hrw-install.md)期间，必须存在具有相应 sudo 权限的 nxautomation 帐户。 如果尝试安装辅助角色，但该帐户不存在或没有相应权限，则安装会失败。

不应更改 `sudoers.d` 文件夹或其所有权的权限。 Sudo 权限对于 **nxautomation** 帐户是必需的，不应删除这些权限。 将此限制为某些文件夹或命令可能会导致重大更改。

Log Analytics 代理和 nxautomation 帐户的可用日志如下：

* /var/opt/microsoft/omsagent/log/omsagent.log - Log Analytics 代理日志
* /var/opt/microsoft/omsagent/run/automationworker/worker.log - 自动化辅助角色日志

>[!NOTE]
>作为更新管理的一部分启用的 nxautomation 用户仅执行签名的 runbook。

## <a name="runbook-permissions"></a>Runbook 权限

Runbook 需要通过凭据向 Azure 进行身份验证的权限。 请参阅[管理 Azure 自动化运行方式帐户](manage-runas-account.md)。

## <a name="modules"></a>模块

Azure 自动化支持多个默认模块，包括一些 AzureRM 模块 (AzureRM.Automation) 和一个包含多个内部 cmdlet 的模块。 它还支持可安装的模块，其中包括 Az 模块 (Az.Automation)，当前优先使用该模块，而不是 AzureRM 模块。 有关可用于你的 runbook 和 DSC 配置的模块的详细信息，请参阅[在 Azure 自动化中管理模块](shared-resources/modules.md)。

## <a name="certificates"></a>证书

Azure 自动化使用[证书](shared-resources/certificates.md)向 Azure 进行身份验证，或将其添加到 Azure 或第三方资源。 证书通过安全方式存储，以供 runbook 和 DSC 配置访问。

Runbook 可以使用未经证书颁发机构 (CA) 签名的自签名证书。 请参阅[创建新证书](shared-resources/certificates.md#create-a-new-certificate)。

## <a name="jobs"></a>作业

Azure 自动化支持从同一自动化帐户运行作业的环境。 一个 runbook 可以同时运行多个作业。 同时运行的作业越多，就越可能将其分派到同一个沙盒中。 

在同一沙盒进程中运行的作业可能相互影响。 一个示例就是运行 [Disconnect-AzAccount](/powershell/module/az.accounts/disconnect-azaccount) cmdlet。 执行此 cmdlet 会断开共享沙盒进程中每个 runbook 作业的连接。 有关使用此方案的示例，请参阅[阻止并发作业](manage-runbooks.md#prevent-concurrent-jobs)。

>[!NOTE]
>从 Azure 中运行的 runbook 启动的 PowerShell 作业可能无法在完整 [PowerShell 语言模式](/powershell/module/microsoft.powershell.core/about/about_language_modes)下运行。

### <a name="job-statuses"></a>作业状态

下表介绍作业的可能状态。 可以查看所有 runbook 作业的状态摘要或在 Azure 门户中深入了解特定 runbook 作业的详细信息。 此外，还可配置与 Log Analytics 工作区的集成，以转发 runbook 作业状态和作业流。 有关与 Azure Monitor 日志集成的详细信息，请参阅[将作业状态和作业流从自动化转发到 Azure Monitor 日志](automation-manage-send-joblogs-log-analytics.md)。 另请参阅[获取作业状态](manage-runbooks.md#obtain-job-statuses)，以获取使用 runbook 中的状态的示例。

| 状态 | 说明 |
|:--- |:--- |
| 已完成 |作业已成功完成。 |
| 已失败 |图形或 PowerShell 工作流 runbook 未能编译。 PowerShell runbook 未能启动或作业遇到异常。 请参阅 [Azure 自动化 runbook 类型](automation-runbook-types.md)。|
| 失败，正在等待资源 |作业失败，因为它已达到[公平份额](#fair-share)限制三次，并且每次都从同一个检查点或 Runbook 开始处启动。 |
| 已排队 |作业正在等待自动化辅助角色上的资源变得可用，以便其能够启动。 |
| 正在恢复 |系统正在恢复已暂停的作业。 |
| 正在运行 |作业正在运行。 |
| 正在运行，正在等待资源 |作业已卸载，因为它已达到公平份额限制。 片刻之后，它将从其上一个检查点恢复。 |
| 正在启动 |作业已分配给辅助角色，并且系统正在将它启动。 |
| 已停止 |作业在完成之前已被用户停止。 |
| 正在停止 |系统正在停止作业。 |
| 已挂起 |仅适用于[图形 runbook 和 PowerShell 工作流 runbook](automation-runbook-types.md)。 作业已被用户、系统或 Runbook 中的命令暂停。 如果 runbook 没有检查点，则会从开始处启动。 如果它有检查点，它将重新启动并从其上一个检查点继续。 系统仅在发生异常时暂停 runbook。 默认情况下，`ErrorActionPreference` 变量设置为“继续”，表示出错时作业将保持运行。 如果该首选项变量设置为“停止”，则出错时作业会暂停。  |
| 正在暂停 |仅适用于[图形 runbook 和 PowerShell 工作流 runbook](automation-runbook-types.md)。 系统正在尝试按用户请求暂停作业。 Runbook 只有在达到其下一个检查点后才能挂起。 如果 runbook 越过了最后一个检查点，则只有在完成后才能暂停。 |

## <a name="activity-logging"></a>活动日志记录

在 Azure 自动化中执行 runbook 会在自动化帐户的活动日志中写入详细信息。 有关如何使用日志的详细信息，请参阅[从活动日志中检索详细信息](manage-runbooks.md#retrieve-details-from-activity-log)。

## <a name="exceptions"></a>例外

本部分介绍在 runbook 中处理异常或间歇性问题的一些方法。 一个示例是 WebSocket 异常。 正确的异常处理可防止暂时性网络故障导致 runbook 失败。

### <a name="erroractionpreference"></a>ErrorActionPreference

[ErrorActionPreference](/powershell/module/microsoft.powershell.core/about/about_preference_variables#erroractionpreference) 变量确定 PowerShell 如何响应非终止错误。 终止错误始终会终止，并且不受 `ErrorActionPreference` 影响。

当 runbook 使用 `ErrorActionPreference` 时，通常发生的非终止错误（例如 [Get-ChildItem](/powershell/module/microsoft.powershell.management/get-childitem) cmdlet 中的 `PathNotFound`）会阻止 runbook 完成。 以下示例演示如何使用 `ErrorActionPreference`。 由于脚本停止，最后的 [Write-Output](/powershell/module/microsoft.powershell.utility/write-output) 命令从不执行。

```powershell-interactive
$ErrorActionPreference = 'Stop'
Get-ChildItem -path nofile.txt
Write-Output "This message will not show"
```

### <a name="try-catch-finally"></a>Try Catch Finally

[Try Catch Finally](/powershell/module/microsoft.powershell.core/about/about_try_catch_finally) 在 PowerShell 脚本中用于处理终止错误。 脚本可以使用此机制来捕获特定异常或一般异常。 `catch` 语句应用于跟踪或尝试处理错误。 以下示例尝试下载不存在的文件。 它捕获 `System.Net.WebException` 异常，并返回任何其他异常的最后一个值。

```powershell-interactive
try
{
   $wc = new-object System.Net.WebClient
   $wc.DownloadFile("http://www.contoso.com/MyDoc.doc")
}
catch [System.Net.WebException]
{
    "Unable to download MyDoc.doc from http://www.contoso.com."
}
catch
{
    "An error occurred that could not be resolved."
}
```

### <a name="throw"></a>Throw

[Throw](/powershell/module/microsoft.powershell.core/about/about_throw) 可用于生成终止错误。 在 runbook 中定义自己的逻辑时，此机制很有用。 如果脚本满足应停止脚本的条件，则可以使用 `throw` 语句停止。 以下示例使用此语句显示必需的函数参数。

```powershell-interactive
function Get-ContosoFiles
{
  param ($path = $(throw "The Path parameter is required."))
  Get-ChildItem -Path $path\*.txt -recurse
}
```

## <a name="errors"></a>错误

Runbook 必须处理错误。 Azure 自动化支持两种类型的 PowerShell 错误，即终止错误和非终止错误。 

发生终止错误时，终止错误会停止执行 runbook。 Runbook 停止且作业状态为“失败”。

非终止错误允许脚本在发生非终止错误后继续运行。 非终止错误的示例为：runbook 对不存在的路径使用 `Get-ChildItem` cmdlet。 PowerShell 发现路径不存在，然后引发错误，并继续转到下一文件夹。 此例中的错误不会将 runbook 作业状态设置为“失败”，并且作业甚至可能已完成。 若要强制 runbook 在发生非终止性错误时停止，可以使用 `ErrorAction Stop` cmdlet。

## <a name="calling-processes"></a>调用进程

在 Azure 沙盒中运行的 runbook 不支持调用进程，例如可执行文件（.exe 文件）或子进程。 出现这种情况的原因是，Azure 沙盒是在容器中运行的共享进程，该容器可能无法访问所有基础 API。 对于需要第三方软件或需要调用子进程的方案，应在[混合 Runbook 辅助角色](automation-hybrid-runbook-worker.md)上执行 runbook。

## <a name="device-and-application-characteristics"></a>设备和应用程序特征

Azure 沙盒中的 runbook 作业无法访问任何设备或应用程序特征。 用于在 Windows 上查询性能指标的最常见 API 为 WMI，其中一些常用指标包括内存和 CPU 使用率。 但是，由于在云中运行的作业无法访问基于 Web 的企业管理 (WBEM) 的 Microsoft 实现，所使用的 API 并不重要。 此平台基于通用信息模型 (CIM) 生成，提供用于定义设备和应用程序特性的行业标准。

## <a name="webhooks"></a>Webhook

外部服务（例如，Azure DevOps Services 和 GitHub）可以在 Azure 自动化中启动 runbook。 为了执行这种类型的启动，服务通过单个 HTTP 请求使用 [Webhook](automation-webhooks.md)。 借助 Webhook，无需实现完整的 Azure 自动化功能即可启动 runbook。

## <a name="shared-resources"></a><a name="fair-share"></a>共享资源

为了在云中的所有 runbook 之间共享资源，Azure 使用称为公平份额的概念。 使用公平份额时，Azure 会暂时卸载或停止已运行三小时以上的所有作业。 [PowerShell runbook](automation-runbook-types.md#powershell-runbooks) 和 [Python runbook](automation-runbook-types.md#python-runbooks) 的作业会停止且不会重启，作业状态变为“已停止”。

对于长时间运行的 Azure 自动化任务，建议使用混合 Runbook 辅助角色。 混合 Runbook 辅助角色不受公平份额限制，并且不会限制 runbook 的执行时间。 其他作业[限制](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)适用于 Azure 沙盒和混合 Runbook 辅助角色。 虽然混合 Runbook 辅助角色不受三小时公平份额限制的限制，但你应开发 runbook，使其在支持从意外的本地基础结构问题中重新启动的辅助角色上运行。

另一种选择是通过使用子 runbook 来优化 runbook。 例如，runbook 可能会在多个资源上循环访问同一函数（例如，对多个数据库执行某个数据库操作）。 可将此函数移至[子 runbook](automation-child-runbooks.md)，并让 runbook 使用 [Start-AzAutomationRunbook](/powershell/module/az.automation/start-azautomationrunbook) 对其进行调用。 子 runbook 在单独的进程中并行执行。

使用子 runbook 可减少完成父 runbook 所需的时间总量。 Runbook 可以使用 [Get-AzAutomationJob](/powershell/module/az.automation/get-azautomationjob) cmdlet 检查子 runbook 的作业状态（如果其在子 runbook 完成后仍有更多操作需要执行）。

## <a name="next-steps"></a>后续步骤

* 若要开始使用 PowerShell Runbook，请参阅[教程：创建 PowerShell Runbook](learn/automation-tutorial-runbook-textual-powershell.md)。
* 若要使用 Runbook，请参阅[在 Azure 自动化中管理 Runbook](manage-runbooks.md)。
* 有关 PowerShell 的详细信息，请参阅 [PowerShell 文档](/powershell/scripting/overview)。
* 有关 PowerShell cmdlet 参考，请参阅 [Az.Automation](/powershell/module/az.automation#automation)。