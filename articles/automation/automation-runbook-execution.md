---
title: 在 Azure 自动化中执行 Runbook
description: 详细介绍如何处理 Azure 自动化中的 Runbook。
services: automation
ms.subservice: process-automation
ms.date: 04/14/2020
ms.topic: conceptual
ms.openlocfilehash: 1933688459cd02ee4da448d2e83b0a7a92a1d2c8
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/09/2020
ms.locfileid: "82994739"
---
# <a name="runbook-execution-in-azure-automation"></a>在 Azure 自动化中执行 Runbook

通过 Azure Automation 中的流程自动化，你可以创建和管理 PowerShell、PowerShell 工作流和图形 runbook。 有关详细信息，请参阅[Azure 自动化 runbook](automation-runbook-types.md)。 

自动基于在其中定义的逻辑执行 runbook。 如果 runbook 中断，它将在开始时重新启动。 此行为需要你编写支持在发生暂时性问题时重新启动的 runbook。

在 Azure 自动化中启动 runbook 会创建一个作业，该作业是 runbook 的单个执行实例。 每个作业通过与 Azure 订阅建立连接来访问 Azure 资源。 如果资源是从公有云访问的，则该作业只能访问你的数据中心内的资源。

Azure Automation 分配一个辅助角色，以便在执行 runbook 的过程中运行每个作业。 尽管辅助角色由多个 Azure 帐户共享，但不同自动化帐户中的作业是相互独立的。 无法控制作业请求的工作线程。

查看 Azure 门户中的 runbook 列表时，它会显示已为每个 runbook 启动的每个作业的状态。 Azure Automation 最多存储30天的作业日志。

下图显示了[powershell runbook](automation-runbook-types.md#powershell-runbooks)、 [powershell 工作流 runbook](automation-runbook-types.md#powershell-workflow-runbooks)和[图形 runbook](automation-runbook-types.md#graphical-runbooks)的 runbook 作业生命周期。

![作业状态 - PowerShell 工作流](./media/automation-runbook-execution/job-statuses.png)

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

>[!NOTE]
>本文进行了更新，以便使用新的 Azure PowerShell Az 模块。 你仍然可以使用 AzureRM 模块，至少在 2020 年 12 月之前，它将继续接收 bug 修补程序。 若要详细了解新的 Az 模块和 AzureRM 兼容性，请参阅[新 Azure Powershell Az 模块简介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 有关混合 Runbook 辅助角色上的 Az 模块安装说明，请参阅[安装 Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 对于自动化帐户，可参阅[如何更新 Azure 自动化中的 Azure PowerShell 模块](automation-update-azure-modules.md)，将模块更新到最新版本。

## <a name="runbook-execution-environment"></a>Runbook 执行环境

Azure Automation 中的 runbook 可以在 Azure 沙盒或[混合 Runbook 辅助角色](automation-hybrid-runbook-worker.md)上运行。 

当 runbook 旨在针对 Azure 中的资源进行身份验证和运行时，它们将在 Azure 沙箱中运行，这是多个作业可以使用的共享环境。 使用同一沙盒的作业受沙盒的资源限制约束。 Azure 沙箱环境不支持交互式操作。 它阻止访问所有进程外 COM 服务器。 它还需要对进行 Win32 调用的 runbook 使用本地 MOF 文件。

你还可以使用[混合 Runbook 辅助](automation-hybrid-runbook-worker.md)角色直接在托管角色的计算机上或在环境中的本地资源上运行 runbook。 Azure Automation 存储和管理 runbook，然后将其传递给一个或多个已分配的计算机。

>[!NOTE]
>若要在 Linux 混合 Runbook 辅助角色上运行，必须对脚本进行签名，并相应地配置辅助角色。 或者，[必须关闭签名验证](automation-linux-hrw-install.md#turn-off-signature-validation)。 

下表列出了一些 runbook 执行任务，其中每个任务都列出了建议的执行环境。

|任务|建议|注释|
|---|---|---|
|与 Azure 资源集成|Azure 沙盒|在 Azure 中托管，身份验证更简单。 如果在 Azure VM 上使用混合 Runbook 辅助角色，则可以[将 Runbook 身份验证用于托管标识](automation-hrw-run-runbooks.md#runbook-auth-managed-identities)。|
|获得最佳性能来管理 Azure 资源|Azure 沙盒|脚本运行在相同的环境中，延迟较低。|
|最大程度减少运营成本|Azure 沙盒|不需要计算开销，也无需 VM。|
|执行长时间运行的脚本|混合 Runbook 辅助角色|Azure 沙箱具有[资源限制](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)。|
|与本地服务交互|混合 Runbook 辅助角色|直接访问主机或其他云环境中的资源或本地环境中的资源。 |
|需要第三方软件和可执行文件|混合 Runbook 辅助角色|你可以管理操作系统并安装软件。|
|使用 Runbook 监视文件或文件夹|混合 Runbook 辅助角色|在混合 Runbook 辅助角色上使用[观察程序任务](automation-watchers-tutorial.md)。|
|运行资源密集型脚本|混合 Runbook 辅助角色| Azure 沙箱具有[资源限制](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)。|
|使用具有特定要求的模块| 混合 Runbook 辅助角色|一些示例如下：</br> WinSCP - winscp.exe 上的依赖项 </br> IIS 管理-依赖于启用或管理 IIS|
|使用安装程序安装模块|混合 Runbook 辅助角色|沙盒的模块必须支持复制。|
|使用需要不同于4.7.2 的 .NET Framework 版本的 runbook 或模块|混合 Runbook 辅助角色|Azure 沙盒支持 .NET Framework 4.7.2，不支持升级到不同版本。|
|运行需要提升的脚本|混合 Runbook 辅助角色|沙盒不允许提升。 使用混合 Runbook 辅助角色，你可以在运行需要提升的命令时关闭 UAC 并使用[Invoke 命令](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/invoke-command?view=powershell-7)。|
|运行需要访问 Windows Management Instrumentation （WMI）的脚本|混合 Runbook 辅助角色|在云中沙盒中运行的作业不能访问 WMI 提供程序。 |

## <a name="resources"></a>资源

Runbook 必须包含用于处理[资源](https://docs.microsoft.com/rest/api/resources/resources)（例如，虚拟机、网络和网络上的资源）的逻辑。 资源与 Azure 订阅相关联，并且 runbook 需要适当的凭据才能访问任何资源。 有关在 runbook 中处理资源的示例，请参阅[处理资源](manage-runbooks.md#handle-resources)。 

## <a name="security"></a>安全性

Azure Automation 使用[Azure 安全中心（ASC）](../security-center/security-center-intro.md)为你的资源提供安全性，并在 Linux 系统中检测到危害。 不管资源是否在 Azure 中，均跨工作负荷提供安全。 请参阅[Azure 自动化中的身份验证简介](automation-security-overview.md)。

ASC 对可在 VM 上运行任何脚本（有符号或无符号）的用户施加限制。 如果你是具有 VM 的根访问权限的用户，则必须使用数字签名显式配置计算机，或将其关闭。 否则，只有在创建自动化帐户并启用适当的功能之后，才能运行脚本来应用操作系统更新。

## <a name="subscriptions"></a>订阅

Azure[订阅](https://docs.microsoft.com/office365/enterprise/subscriptions-licenses-accounts-and-tenants-for-microsoft-cloud-offerings)是与 Microsoft 签订的协议，可使用一个或多个基于云的服务进行收费。 对于 Azure 自动化，每个订阅都链接到 Azure 自动化帐户，你可以在帐户中[创建多个订阅](manage-runbooks.md#work-with-multiple-subscriptions)。

## <a name="credentials"></a>凭据

无论是针对 Azure 还是第三方系统，runbook 都需要适当的[凭据](shared-resources/credentials.md)才能访问任何资源。 这些凭据存储在 Azure 自动化中，Key Vault 等。  

## <a name="azure-monitor"></a>Azure Monitor

Azure Automation 利用[Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview)来监视其计算机操作。 操作需要 Log Analytics 工作区和[Log Analytics 代理](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent)。

### <a name="log-analytics-agent-for-windows"></a>适用于 Windows 的 Log Analytics 代理

适用于[windows 的 Log Analytics 代理](https://docs.microsoft.com/azure/azure-monitor/platform/agent-windows)使用 Azure Monitor 管理 windows vm 和物理计算机。 计算机可以在 Azure 中运行，也可以在非 Azure 环境（例如本地数据中心）中运行。 您必须将代理配置为向一个或多个 Log Analytics 工作区报告。 

>[!NOTE]
>Windows 的 Log Analytics 代理之前称为 Microsoft Monitoring Agent （MMA）。

### <a name="log-analytics-agent-for-linux"></a>适用于 Linux 的 Log Analytics 代理

适用于[linux 的 Log Analytics 代理](https://docs.microsoft.com/azure/azure-monitor/platform/agent-linux)的工作方式类似于 Windows 代理，但会将 Linux 计算机连接到 Azure Monitor。 使用**nxautomation**用户帐户安装代理，该用户帐户允许执行需要根权限的命令，例如，在混合 Runbook 辅助角色上执行。 **Nxautomation**帐户是无需密码的系统帐户。 

在[安装 Linux 混合 Runbook 辅助角色](automation-linux-hrw-install.md)的过程中，必须存在具有相应 sudo 权限的**nxautomation**帐户。 如果尝试安装该辅助角色，但该帐户不存在或没有适当的权限，则安装将失败。

Log Analytics 代理和**nxautomation**帐户的可用日志如下：

* /var/opt/microsoft/omsagent/log/omsagent.log-Log Analytics 代理日志 
* /var/opt/microsoft/omsagent/run/automationworker/worker.log-自动化 worker 日志

>[!NOTE]
>作为更新管理的一部分的**nxautomation**用户载入仅执行签名的 runbook。

## <a name="runbook-permissions"></a>Runbook 权限

Runbook 需要通过凭据对 Azure 进行身份验证的权限。 请参阅[管理 Azure 自动化运行方式帐户](manage-runas-account.md)。 

## <a name="modules"></a>模块

Azure 自动化支持多个默认模块，包括一些 AzureRM 模块（AzureRM）和包含多个内部 cmdlet 的模块。 还支持使用可安装的模块，包括 Az 模块（Az），当前正用于 AzureRM 模块。 有关适用于 runbook 和 DSC 配置的模块的详细信息，请参阅[在 Azure 自动化中管理模块](shared-resources/modules.md)。

## <a name="certificates"></a>证书

Azure Automation 使用[证书](shared-resources/certificates.md)进行 azure 身份验证，或者将证书添加到 azure 或第三方资源。 安全地存储证书以供 runbook 和 DSC 配置访问。 

Runbook 可以使用未由证书颁发机构（CA）签名的自签名证书。 请参阅[创建新证书](shared-resources/certificates.md#create-a-new-certificate)。

## <a name="jobs"></a>作业

Azure 自动化支持从同一自动化帐户运行作业的环境。 一个 runbook 可以同时运行多个作业。 同时运行的作业越多，就越可能将其分派到同一个沙盒中。 

在同一沙盒过程中运行的作业可能会相互影响。 一个示例是运行[AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/disconnect-azaccount?view=azps-3.7.0) cmdlet。 此 cmdlet 的执行会断开共享沙盒进程中的每个 runbook 作业。 有关使用此方案的示例，请参阅[阻止并发作业](manage-runbooks.md#prevent-concurrent-jobs)。

>[!NOTE]
>从在 Azure 沙盒中运行的 runbook 启动的 PowerShell 作业可能无法在完整[PowerShell 语言模式下](/powershell/module/microsoft.powershell.core/about/about_language_modes)运行。 

### <a name="job-statuses"></a>作业状态

下表描述了作业可能的状态。 你可以查看所有 runbook 作业的状态摘要，或在 Azure 门户中深入了解特定 runbook 作业的详细信息。 此外，还可配置与 Log Analytics 工作区的集成，以转发 runbook 作业状态和作业流。 有关与 Azure Monitor 日志集成的详细信息，请参阅[将作业状态和作业流从自动化转发到 Azure Monitor 日志](automation-manage-send-joblogs-log-analytics.md)。 有关使用 runbook 中的状态的示例，另请参阅[获取作业状态](manage-runbooks.md#obtain-job-statuses)。

| 状态 | 说明 |
|:--- |:--- |
| 已完成 |作业已成功完成。 |
| Failed |未能编译图形或 PowerShell 工作流 runbook。 PowerShell runbook 无法启动或作业有异常。 请参阅[Azure 自动化 runbook 类型](automation-runbook-types.md)。|
| 失败，正在等待资源 |作业失败，因为它已达到[公平份额](#fair-share)限制三次，并且每次都从同一个检查点或 Runbook 开始处启动。 |
| 已排队 |作业正在等待自动化工作线程上的资源变为可用，以便能够启动。 |
| 正在恢复 |系统正在恢复已暂停的作业。 |
| 正在运行 |作业正在运行。 |
| 正在运行，正在等待资源 |作业已卸载，因为它已达到公平份额限制。 片刻之后，它将从其上一个检查点恢复。 |
| 正在启动 |作业已分配给辅助角色，并且系统正在将它启动。 |
| 已停止 |作业在完成之前已被用户停止。 |
| 正在停止 |系统正在停止作业。 |
| 已挂起 |仅适用于[图形和 PowerShell 工作流 runbook](automation-runbook-types.md) 。 作业已被用户、系统或 Runbook 中的命令暂停。 如果 runbook 没有检查点，则会从开始处开始。 如果它有检查点，它将重新启动并从其上一个检查点继续。 系统仅在发生异常时挂起 runbook。 默认情况下， `ErrorActionPreference`该变量设置为 Continue，表示该作业在出现错误时继续运行。 如果首选项变量设置为 "停止"，则作业将在出现错误时挂起。  |
| 正在暂停 |仅适用于[图形和 PowerShell 工作流 runbook](automation-runbook-types.md) 。 系统正在尝试按用户请求暂停作业。 Runbook 只有在达到其下一个检查点后才能挂起。 如果它已通过其最后一个检查点，则它将完成，然后才能挂起。 |

## <a name="activity-logging"></a>活动日志记录

在 Azure 自动化中执行 runbook 会在自动化帐户的活动日志中写入详细信息。 有关使用日志的详细信息，请参阅[从活动日志中检索详细信息](manage-runbooks.md#retrieve-details-from-activity-log)。 

## <a name="exceptions"></a>例外

本部分介绍在 runbook 中处理异常或间歇问题的一些方法。 例如，WebSocket 异常。 更正异常处理可防止暂时性的网络故障导致 runbook 失败。 

### <a name="erroractionpreference"></a>ErrorActionPreference

[ErrorActionPreference](/powershell/module/microsoft.powershell.core/about/about_preference_variables#erroractionpreference)变量确定 PowerShell 如何响应非终止错误。 终止错误始终终止，不受影响`ErrorActionPreference`。

当 runbook 使用`ErrorActionPreference`时，通常非终止错误（如`PathNotFound` [get-childitem](https://docs.microsoft.com/powershell/module/microsoft.powershell.management/get-childitem?view=powershell-7) cmdlet）将停止 runbook 的完成。 以下示例显示了 `ErrorActionPreference` 的用法。 当脚本停止时，最终的[写入输出](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/write-output?view=powershell-7)命令永远不会执行。

```powershell-interactive
$ErrorActionPreference = 'Stop'
Get-ChildItem -path nofile.txt
Write-Output "This message will not show"
```

### <a name="try-catch-finally"></a>Try Catch Finally

[尝试 Catch Finally](/powershell/module/microsoft.powershell.core/about/about_try_catch_finally)用于处理终止错误。 该脚本可以使用此机制来捕获特定的异常或一般异常。 `catch`语句应用于跟踪或尝试处理错误。 以下示例尝试下载一个不存在的文件。 它将捕获`System.Net.WebException`异常并返回任何其他异常的最后一个值。

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

可以使用 [Throw](/powershell/module/microsoft.powershell.core/about/about_throw) 来生成终止性错误。 在 runbook 中定义自己的逻辑时，此机制可能会很有用。 如果脚本满足应停止的条件，则可以使用`throw`语句停止。 下面的示例使用此语句来显示所需的函数参数。

```powershell-interactive
function Get-ContosoFiles
{
  param ($path = $(throw "The Path parameter is required."))
  Get-ChildItem -Path $path\*.txt -recurse
}
```

## <a name="errors"></a>错误

Runbook 必须处理错误。 Azure 自动化支持两种类型的 PowerShell 错误，即终止和非终止。 

终止错误会在 runbook 执行发生时停止。 Runbook 停止并且作业状态为 "失败"。

非终止错误允许脚本即使在发生之后也能继续。 非终止错误的一个示例是 runbook 将`Get-ChildItem` cmdlet 与不存在的路径一起使用时出现的错误。 PowerShell 发现路径不存在，然后引发错误，并继续转到下一文件夹。 此情况下的错误不会将 runbook 作业状态设置为 "失败"，甚至可能会完成该作业。 若要强制 runbook 在发生非终止性错误时停止，可以使用 `ErrorAction Stop` cmdlet。

## <a name="calling-processes"></a>调用进程

在 Azure 沙盒中运行的 runbook 不支持调用进程（如可执行文件（**.exe**文件）或子进程）。 出现这种情况的原因是，Azure 沙箱是一个共享进程，该进程在可能无法访问所有底层 Api 的容器中运行。 对于需要第三方软件或对子进程的调用的方案，应在[混合 Runbook 辅助角色](automation-hybrid-runbook-worker.md)上执行 runbook。

## <a name="device-and-application-characteristics"></a>设备和应用程序特征

Azure 沙盒中的 Runbook 作业无法访问任何设备或应用程序特征。 用于在 Windows 上查询性能指标的最常见 API 是 WMI，其中一些常用指标为内存和 CPU 使用率。 但是，因为在云中运行的作业不能访问基于 Web 的企业管理（WBEM）的 Microsoft 实现，所以这并不重要。 此平台基于通用信息模型（CIM）构建，为定义设备和应用程序特征提供行业标准。

## <a name="webhooks"></a>Webhook

例如，Azure DevOps Services 和 GitHub 的外部服务可以在 Azure 自动化中启动 runbook。 若要执行这种类型的启动，服务会通过单个 HTTP 请求使用[webhook](automation-webhooks.md) 。 使用 webhook 可在未实现完整 Azure 自动化解决方案的情况下启动 runbook。 

## <a name="shared-resources"></a><a name="fair-share"></a>共享资源

为了在云中的所有 runbook 之间共享资源，Azure 使用名为 "公平共享" 的概念。 使用公平共享时，Azure 会临时卸载或停止运行超过三个小时的任何作业。 [PowerShell runbook](automation-runbook-types.md#powershell-runbooks)和[Python runbook](automation-runbook-types.md#python-runbooks)的作业将停止且不会重新启动，作业状态将变为 "已停止"。

对于长时间运行的 Azure 自动化任务，建议使用混合 Runbook 辅助角色。 混合 Runbook 辅助角色不受公平份额限制，并且不会限制 runbook 的执行时间。 其他作业[限制](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)适用于 Azure 沙盒和混合 Runbook 辅助角色。 虽然混合 Runbook 辅助角色不受3小时公平份额限制的限制，但你应开发 runbook，使其在支持从意外的本地基础结构问题中重新启动的辅助角色上运行。

另一种方法是使用子 runbook 优化 runbook。 例如，你的 runbook 可能在多个资源上循环使用同一个函数，例如，对多个数据库执行数据库操作。 可以将此函数移动到[子 runbook](automation-child-runbooks.md) ，并让 Runbook 使用[AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.7.0)调用它。 子 runbook 并行在单独的进程中执行。

使用子 runbook 会缩短父 runbook 完成的总时间。 Runbook 可以使用[AzAutomationJob](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationjob?view=azps-3.7.0) cmdlet 来检查子 runbook 的作业状态（如果子 runbook 在子项完成后仍有更多操作）。

## <a name="next-steps"></a>后续步骤

* 若要开始使用 runbook，请参阅[在 Azure 自动化中管理 runbook](manage-runbooks.md)。
* 有关 PowerShell 的详细信息（包括语言参考和学习模块），请参阅 [PowerShell 文档](https://docs.microsoft.com/powershell/scripting/overview)。
* 有关 PowerShell cmdlet 参考，请参阅 [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
)。
