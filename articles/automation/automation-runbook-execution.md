---
title: Azure Automation에서 Runbook 실행
description: Azure Automation의 Runbook이 처리되는 방법에 대한 자세한 내용을 설명합니다.
services: automation
ms.subservice: process-automation
ms.date: 04/04/2019
ms.topic: conceptual
ms.openlocfilehash: c97e10d2785b7dc1a438c95dca9be94fcef82f94
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76714840"
---
# <a name="runbook-execution-in-azure-automation"></a>Azure Automation에서 Runbook 실행

Azure Automation에서 Runbook을 시작하면 작업이 생성됩니다. 작업은 Runbook의 단일 실행 인스턴스입니다. 각 작업을 실행하기 위해 Azure Automation 작업자가 할당됩니다. 작업자가 많은 Azure 계정에서 공유되지만 여러 Automation 계정의 작업은 서로 격리됩니다. 사용자는 작업에 대한 요청을 처리할 작업자를 제어할 수 없습니다. 단일 Runbook에서 동시에 많은 작업을 실행할 수 있습니다. 동일한 Automation 계정의 작업 실행 환경은 다시 사용할 수 있습니다. 더 많은 작업을 동시에 실행할 수록 동일한 샌드박스에 더 자주 디스패치될 수 있습니다. 동일한 샌드박스에서 실행하는 작업은 서로 영향을 줄 수 있습니다. 한 가지 예는 `Disconnect-AzureRMAccount` cmdlet을 실행하는 것입니다. 이 cmdlet을 실행하면 공유 샌드박스 프로세스의 각 Runbook 작업의 연결을 끊습니다. Azure Portal에서 Runbook 목록을 확인하면 각 Runbook에 대해 시작된 모든 작업의 상태가 나열됩니다. 각 Runbook에 대한 작업 목록을 확인하여 각 작업의 상태를 추적할 수 있습니다. 작업 로그는 최대 30일 동안 저장됩니다. 다양한 작업 상태에 대한 설명은 [작업 상태](#job-statuses)를 참조하세요.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

下图显示了[PowerShell runbook](automation-runbook-types.md#powershell-runbooks)、[图形 Runbook](automation-runbook-types.md#graphical-runbooks)和[powershell 工作流 runbook](automation-runbook-types.md#powershell-workflow-runbooks)的 runbook 作业生命周期。

![작업 상태 - PowerShell 워크플로](./media/automation-runbook-execution/job-statuses.png)

Azure 구독에 연결하면 작업에서 Azure 리소스에 액세스할 수 있습니다. 단, 퍼블릭 클라우드에서 액세스할 수 있는 데이터 센터의 리소스에만 액세스할 수 있습니다.

## <a name="where-to-run-your-runbooks"></a>Runbook을 실행하는 위치

Azure Automation의 Runbook은 Azure의 샌드박스 또는 [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md)에서 실행할 수 있습니다. 샌드박스는 여러 작업에서 사용할 수 있는 Azure의 공유 환경입니다. 동일한 샌드박스를 사용하는 작업에는 샌드박스의 리소스 제한이 적용됩니다. 混合 Runbook 辅助角色可以直接在托管角色的计算机上运行 runbook，也可以针对环境中的资源运行 runbook，从而管理这些本地资源。 Runbook은 Azure Automation에 저장 및 관리된 후 하나 이상의 할당된 컴퓨터에 전달됩니다. 大多数 runbook 可以轻松地在 Azure 沙盒中运行。 Azure 샌드박스를 통해 하이브리드 Runbook을 선택하여 Runbook을 실행하도록 추천되는 특정 시나리오가 있습니다. 몇 가지 예제 시나리오의 목록은 다음 표를 참조하세요.

|Task|최선의 선택|메모|
|---|---|---|
|Azure 리소스와 통합|Azure 샌드박스|Azure에서 호스팅되며, 인증이 더 간단합니다. Azure VM에서 Hybrid Runbook Worker를 사용하는 경우 [Azure 리소스에 대한 관리 ID](automation-hrw-run-runbooks.md#managed-identities-for-azure-resources)를 사용할 수 있습니다.|
|Azure 리소스를 관리하는 최적의 성능|Azure 샌드박스|脚本在同一环境中运行，而这种情况下，延迟时间较短|
|운영 비용 최소화|Azure 샌드박스|컴퓨팅 오버헤드가 없고 VM이 필요하지 않습니다.|
|장기 실행 스크립트|Hybrid Runbook Worker|Azure 샌드박스는 [리소스에 제한](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)이 있습니다.|
|로컬 서비스와 상호 작용|Hybrid Runbook Worker|호스트 머신에 직접 액세스할 수 있습니다.|
|타사 소프트웨어 및 실행 파일 필요|Hybrid Runbook Worker|OS를 관리하고 소프트웨어를 설치할 수 있습니다.|
|Runbook으로 파일 또는 폴더 모니터링|Hybrid Runbook Worker|Hybrid Runbook Worker에서 [감시자 작업](automation-watchers-tutorial.md)을 사용합니다.|
|리소스 사용량이 많은 스크립트|Hybrid Runbook Worker| Azure 샌드박스는 [리소스에 제한](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)이 있습니다.|
|특정 요구 사항이 있는 모듈 사용| Hybrid Runbook Worker|예는 다음과 같습니다.</br> **WinSCP** - winscp.exe에 대한 종속성 </br> **IISAdministration** - IIS를 사용하도록 설정해야 함|
|설치 관리자가 필요한 모듈 설치|Hybrid Runbook Worker|沙盒的模块必须是可复制|
|4\.7.2와 다른 .NET 프레임워크가 필요한 Runbook 또는 모듈 사용|Hybrid Runbook Worker|Automation 샌드박스에는 NET Framework 4.7.2가 있으며, 업그레이드할 수 있는 방법이 없습니다.|
|需要提升的脚本|Hybrid Runbook Worker|沙盒不允许提升。 若要解决此情况，请使用混合 Runbook 辅助角色，并可以在运行需要提升的命令时关闭 UAC 并使用 `Invoke-Command`|
|需要访问 WMI 的脚本|Hybrid Runbook Worker|在云中沙盒中运行的作业无权[访问 WMI](#device-and-application-characteristics)|

## <a name="runbook-behavior"></a>Runbook 동작

Runbook은 내부에 정의된 논리에 따라 실행됩니다. Runbook이 중단되면 해당 Runbook이 처음부터 다시 시작됩니다. 일시적인 문제가 있는 경우 이 동작을 수행하려면 다시 시작을 지원하는 방식으로 Runbook을 작성해야 합니다.

从在 Azure 沙盒中运行的 Runbook 启动的 PowerShell 作业可能无法在完整语言模式下运行。 若要了解有关 PowerShell 语言模式的详细信息，请参阅[powershell 语言模式](/powershell/module/microsoft.powershell.core/about/about_language_modes)。 有关如何在 Azure 自动化中与作业交互的其他详细信息，请参阅[通过 PowerShell 检索作业状态](#retrieving-job-status-using-powershell)

### <a name="creating-resources"></a>리소스 만들기

스크립트에서 리소스를 만드는 경우 리소스를 다시 만들기 전에 해당 리소스가 이미 있는지 확인해야 합니다. 기본 예제는 다음 예제와 같습니다.

```powershell
$vmName = "WindowsVM1"
$resourceGroupName = "myResourceGroup"
$myCred = Get-AutomationPSCredential "MyCredential"
$vmExists = Get-AzureRmResource -Name $vmName -ResourceGroupName $resourceGroupName

if(!$vmExists)
    {
    Write-Output "VM $vmName does not exists, creating"
    New-AzureRmVM -Name $vmName -ResourceGroupName $resourceGroupName -Credential $myCred
    }
else
    {
    Write-Output "VM $vmName already exists, skipping"
    }
```

### <a name="time-dependent-scripts"></a>依赖于时间的脚本

Runbook을 작성할 때는 신중하게 고려해야 합니다. 앞에서 설명한 대로 Runbook은 강력하고 Runbook이 다시 시작되거나 실패할 수 있는 일시적인 오류를 처리할 수 있는 방식으로 작성되어야 합니다. 如果 runbook 失败，则会重试。 如果 runbook 通常在时间约束内运行，则应在 runbook 中实现用于检查执行时间的逻辑，以确保启动、关机或横向扩展等操作仅在特定时间运行。

> [!NOTE]
> Azure 沙盒进程的本地时间设置为 UTC 时间。 Runbook 中的日期和时间计算需要考虑到这一点。

### <a name="tracking-progress"></a>진행률 추적

Runbook을 모듈식으로 작성하는 것이 좋습니다. 즉, Runbook의 논리를 쉽게 다시 사용하고 다시 시작할 수 있도록 구조화합니다. 如果出现问题，可以通过在 runbook 中跟踪进度来确保 runbook 中的逻辑正确执行。 Runbook에서 진행률을 추적할 수 있는 몇 가지 방법은 스토리지 계정, 데이터베이스 또는 공유 파일과 같은 외부 원본을 사용하는 것입니다. 通过在外部跟踪状态，可以在 runbook 中创建逻辑，以首先检查 runbook 采取的最后一个操作的状态。 然后，根据结果，跳过或继续 runbook 中的特定任务。

### <a name="prevent-concurrent-jobs"></a>동시 작업 방지

여러 작업을 동시에 실행하는 경우 일부 Runbook에서는 이상하게 작동할 수 있습니다. 이 경우 이미 실행 중인 작업이 Runbook에 있는지 확인하는 논리를 구현해야 합니다. 이 동작을 수행하는 방법에 대한 기본 예제는 다음 예제와 같습니다.

```powershell
# Authenticate to Azure
$connection = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzureRmAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint

$AzureContext = Select-AzureRmSubscription -SubscriptionId $connection.SubscriptionID

# Check for already running or new runbooks
$runbookName = "<RunbookName>"
$rgName = "<ResourceGroupName>"
$aaName = "<AutomationAccountName>"
$jobs = Get-AzureRmAutomationJob -ResourceGroupName $rgName -AutomationAccountName $aaName -RunbookName $runbookName -AzureRmContext $AzureContext

# If then check to see if it is already running
$runningCount = ($jobs | ? {$_.Status -eq "Running"}).count

If (($jobs.status -contains "Running" -And $runningCount -gt 1 ) -Or ($jobs.Status -eq "New")) {
    # Exit code
    Write-Output "Runbook is already running"
    Exit 1
} else {
    # Insert Your code here
}
```

### <a name="working-with-multiple-subscriptions"></a>여러 구독 작업

在创作处理多个订阅的 runbook 时，runbook 需要使用[disable-azurermcontextautosave](/powershell/module/azurerm.profile/disable-azurermcontextautosave) cmdlet，以确保不会从可能在同一沙盒中运行的另一个 runbook 中检索到身份验证上下文。 然后，需要使用 `AzureRM` cmdlet 上的 `-AzureRmContext` 参数并向其传递适当的上下文。

```powershell
# Ensures you do not inherit an AzureRMContext in your runbook
Disable-AzureRmContextAutosave –Scope Process

$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzureRmAccount -ServicePrincipal `
-Tenant $Conn.TenantID `
-ApplicationID $Conn.ApplicationID `
-CertificateThumbprint $Conn.CertificateThumbprint

$context = Get-AzureRmContext

$ChildRunbookName = 'ChildRunbookDemo'
$AutomationAccountName = 'myAutomationAccount'
$ResourceGroupName = 'myResourceGroup'

Start-AzureRmAutomationRunbook `
    -ResourceGroupName $ResourceGroupName `
    -AutomationAccountName $AutomationAccountName `
    -Name $ChildRunbookName `
    -DefaultProfile $context
```

### <a name="handling-exceptions"></a>예외 처리

创作脚本时，必须能够处理异常和潜在的间歇性故障。 下面是处理 runbook 的异常或间歇问题的几种不同方法：

#### <a name="erroractionpreference"></a>$ErrorActionPreference

[$ErrorActionPreference](/powershell/module/microsoft.powershell.core/about/about_preference_variables#erroractionpreference)首选项变量确定 PowerShell 如何响应非终止错误。 终止错误不受 `$ErrorActionPreference`影响，它们始终会终止。 通过使用 `$ErrorActionPreference`，如 `Get-ChildItem` cmdlet 中 `PathNotFound` 的正常非终止错误将阻止 runbook 完成。 다음 예에서는 `$ErrorActionPreference`를 사용하는 방법을 보여 줍니다. 当脚本停止运行时，最终 `Write-Output` 行将永远不会执行。

```powershell-interactive
$ErrorActionPreference = 'Stop'
Get-Childitem -path nofile.txt
Write-Output "This message will not show"
```

#### <a name="try-catch-finally"></a>先尝试 Catch

[请尝试](/powershell/module/microsoft.powershell.core/about/about_try_catch_finally)在 PowerShell 脚本中使用 Catch，以帮助处理终止错误。 使用 Try Catch 可以捕获特定的异常或一般异常。 Catch 语句应用于跟踪错误或用于尝试处理错误。 下面的示例尝试下载不存在的文件。 它会捕获 `System.Net.WebException` 异常，如果存在其他异常，则返回最后一个值。

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

#### <a name="throw"></a>放弃

[Throw](/powershell/module/microsoft.powershell.core/about/about_throw)可用于生成终止错误。 在 runbook 中定义自己的逻辑时，这可能很有用。 如果满足了应停止脚本的特定条件，则可以使用 `throw` 来停止脚本。 下面的示例演示如何使用 `throw`所需的函数参数。

```powershell-interactive
function Get-ContosoFiles
{
  param ($path = $(throw "The Path parameter is required."))
  Get-ChildItem -Path $path\*.txt -recurse
}
```

### <a name="using-executables-or-calling-processes"></a>실행 파일 또는 호출 프로세스 사용

在 Azure 沙盒中运行的 runbook 不支持调用进程（如 .exe 或子进程）。 这是因为 Azure 沙箱是在容器中运行的共享进程，可能无法访问所有底层 Api。 타사 소프트웨어 또는 하위 프로세스의 호출이 필요한 시나리오의 경우 [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md)에서 Runbook을 실행하는 것이 좋습니다.

### <a name="device-and-application-characteristics"></a>设备和应用程序特征

在 Azure 沙盒中运行的 Runbook 作业无法访问任何设备或应用程序特征。 用于在 Windows 上查询性能指标的最常见 API 是 WMI。 其中一些常用指标是内存和 CPU 使用率。 但是，使用什么 API 并不重要。 在云中运行的作业无权访问基于 Web 的企业管理（WBEM）的 Microsoft 实现，这是基于通用信息模型（CIM）构建的，这是用于定义设备和应用程序特征的行业标准。

## <a name="job-statuses"></a>작업 상태

다음 표에서는 작업의 가능한 여러 상태를 설명합니다. PowerShell에는 두 가지 유형의 오류, 즉 종료되는 오류와 종료되지 않는 오류가 있습니다. 종료되는 오류는 발생하는 경우 Runbook 상태를 **실패**로 설정합니다. 종료되지 않는 오류를 사용하면 오류 발생 후에도 스크립트가 계속 진행될 수 있습니다. 종료되지 않는 오류의 예는 존재하지 않는 경로로 `Get-ChildItem` cmdlet을 사용하는 것입니다. PowerShell은 경로가 없다는 것을 확인하고 오류가 throw한 후 다음 폴더로 계속 진행됩니다. 이 오류는 Runbook 상태를 **실패**로 설정하지 않으며 **완료**로 표시될 수 있습니다. 종료되지 않는 오류에서 강제로 Runbook을 중지하려면 cmdlet에서 `-ErrorAction Stop`을 사용할 수 있습니다.

| 상태 | Description |
|:--- |:--- |
| Completed |작업이 완료되었습니다. |
| 실패 |[그래픽 및 PowerShell 워크플로 Runbook](automation-runbook-types.md)의 경우 Runbook을 컴파일하지 못했습니다. [PowerShell 스크립트 Runbook](automation-runbook-types.md)의 경우 Runbook을 시작하지 못했거나 작업에서 예외가 발생했습니다. |
| Failed, waiting for resources |작업이 [공평 분배](#fair-share) 한도에 세 번 도달했기 때문에 실패했고 매번 동일한 검사점 또는 Runbook의 처음부터 시작되었습니다. |
| Queued |작업이 시작될 수 있도록 Automation 작업자의 리소스가 사용 가능한 상태가 되기를 기다리고 있습니다. |
| 시작 중 |작업이 작업자에게 지정되었으며 시스템이 작업을 시작하고 있습니다. |
| Resuming |시스템이 일시 중단된 후 작업을 재개하는 중입니다. |
| 실행 중 |작업이 실행 중입니다. |
| Running, waiting for resources |작업이 [공평 분배](#fair-share) 한도에 도달했기 때문에 언로드되었습니다. 잠시 후 마지막 검사점에서 작업이 다시 시작됩니다. |
| 중지됨 |작업이 완료되기 전에 사용자에 의해 중지되었습니다. |
| 중지 중 |시스템이 작업을 중지하는 중입니다. |
| 일시 중단 |작업이 Runbook의 사용자, 시스템 또는 명령에 의해 일시 중단되었습니다. Runbook에 검사점이 없으면 Runbook의 처음부터 시작됩니다. 검사점이 있으면 다시 시작되고 마지막 검사점에서 재개될 수 있습니다. 예외가 발생하는 경우에만 시스템에서 Runbook을 일시 중단합니다. 기본적으로 ErrorActionPreference는 **Continue**로 설정되며, 이는 오류 발생 시 작업이 계속 실행된다는 의미입니다. 이 기본 설정 변수가 **Stop** 으로 설정된 경우 오류 발생 시 작업이 일시 중단됩니다. [그래픽 및 PowerShell 워크플로 Runbook](automation-runbook-types.md) 에만 적용됩니다. |
| Suspending |시스템이 사용자의 요청에 따라 작업을 일시 중단하려고 합니다. Runbook의 다음 검사점에 도달해야만 Runbook을 일시 중단할 수 있습니다. 이미 마지막 검사점을 지난 경우 완료되어야만 일시 중단할 수 있습니다. [그래픽 및 PowerShell 워크플로 Runbook](automation-runbook-types.md) 에만 적용됩니다. |

## <a name="viewing-job-status-from-the-azure-portal"></a>Azure Portal에서 작업 상태 보기

모든 Runbook 작업의 요약 상태를 보거나 Azure Portal에서 특정 Runbook 작업의 세부 정보로 드릴할 수 있습니다. 또한 Runbook의 작업 상태와 작업 스트림을 전달하기 위해 Log Analytics 작업 영역과 통합하도록 구성할 수도 있습니다. 有关与 Azure Monitor 日志集成的详细信息，请参阅[将作业状态和作业流从自动化转发到 Azure Monitor 日志](automation-manage-send-joblogs-log-analytics.md)。

### <a name="automation-runbook-jobs-summary"></a>Automation runbook 작업 요약

선택한 Automation 계정 오른쪽의 **작업 통계** 타일 아래에서 모든 Runbook 작업에 대한 요약을 확인할 수 있습니다.

![작업 통계 타일](./media/automation-runbook-execution/automation-account-job-status-summary.png)

이 타일은 실행된 모든 작업의 개수 및 작업 상태를 그래픽으로 표시합니다.

타일을 클릭하면 실행된 모든 작업의 요약 목록이 포함된 **작업** 페이지가 표시됩니다. 이 페이지는 상태, 시작 시간 및 완료 시간을 표시합니다.

![Automation 계정 작업 페이지](./media/automation-runbook-execution/automation-account-jobs-status-blade.png)

**작업 필터링**을 선택하여 작업 목록을 필터링할 수 있습니다. 특정 Runbook이나 작업 상태를 필터링하거나 드롭다운 목록에서 검색할 시간 범위를 필터링할 수 있습니다.

![작업 상태 필터링](./media/automation-runbook-execution/automation-account-jobs-filter.png)

또는 Automation 계정의 **Runbook** 페이지에서 Runbook을 선택하여 특정 Runbook에 대한 작업 요약 세부 정보를 확인한 다음, **작업** 타일을 선택할 수 있습니다. 이 작업을 수행하면 **작업** 페이지가 표시되며, 여기서 작업 레코드를 클릭하여 세부 정보와 출력을 확인할 수 있습니다.

![Automation 계정 작업 페이지](./media/automation-runbook-execution/automation-runbook-job-summary-blade.png)

### <a name="job-summary"></a>작업 요약

특정 Runbook에 대해 만들어진 모든 작업 및 해당 작업의 최신 상태가 나와 있는 목록을 볼 수 있습니다. 이 목록에서 작업 상태 및 날짜 범위를 필터링하면 작업의 마지막 변경 사항을 볼 수 있습니다. 세부 정보와 출력을 표시하려면 작업의 이름을 클릭합니다. 작업의 세부 보기에는 해당 작업에 제공된 Runbook 매개 변수의 값이 포함됩니다.

다음 단계를 수행하여 Runbook의 작업을 볼 수 있습니다.

1. Azure Portal에서 **Automation**을 선택한 다음 Automation 계정의 이름을 선택합니다.
2. 허브에서 **Runbook**을 선택하고 **Runbook** 페이지의 목록에서 Runbook을 선택합니다.
3. 선택한 Runbook에 대한 페이지에서 **작업** 타일을 클릭합니다.
4. 목록에 있는 작업 중 하나를 클릭하면 Runbook 작업 세부 정보 페이지에서 세부 정보와 출력을 확인할 수 있습니다.

## <a name="retrieving-job-status-using-powershell"></a>使用 PowerShell 检索作业状态

[Get-AzureRmAutomationJob](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationjob)을 사용하여 Runbook에 대해 생성된 작업 및 특정 작업을 검색할 수 있습니다. 如果使用[start-azurermautomationrunbook](https://docs.microsoft.com/powershell/module/azurerm.automation/start-azurermautomationrunbook)启动带有 PowerShell 的 runbook，则将返回生成的作业。 작업의 출력을 얻으려면 [Get-AzureRmAutomationJob](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationjoboutput)을 사용합니다.

다음 명령 예제는 샘플 Runbook에 대한 마지막 작업을 검색하고 작업의 상태, Runbook 매개 변수에 제공된 값, 작업의 출력을 표시합니다.

```azurepowershell-interactive
$job = (Get-AzureRmAutomationJob –AutomationAccountName "MyAutomationAccount" `
–RunbookName "Test-Runbook" -ResourceGroupName "ResourceGroup01" | sort LastModifiedDate –desc)[0]
$job.Status
$job.JobParameters
Get-AzureRmAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAcct" -Id $job.JobId –Stream Output
```

다음 예제는 특정 작업에 대한 출력을 검색하고, 각 레코드를 반환합니다. 레코드 중 하나에 대한 예외가 발생한 경우 값 대신 예외가 쓰여집니다. 이 동작은 출력하는 동안 예외가 일반적으로 기록되지 않을 수 있는 추가 정보를 제공하므로 유용합니다.

```azurepowershell-interactive
$output = Get-AzureRmAutomationJobOutput -AutomationAccountName <AutomationAccountName> -Id <jobID> -ResourceGroupName <ResourceGroupName> -Stream "Any"
foreach($item in $output)
{
    $fullRecord = Get-AzureRmAutomationJobOutputRecord -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -JobId <jobID> -Id $item.StreamRecordId
    if ($fullRecord.Type -eq "Error")
    {
        $fullRecord.Value.Exception
    }
    else
    {
    $fullRecord.Value
    }
}
```

## <a name="get-details-from-activity-log"></a>활동 로그에서 세부 정보 가져오기

Runbook을 시작한 사용자 또는 계정과 같은 기타 세부 정보는 Automation 계정의 활동 로그에서 검색할 수 있습니다. 다음 PowerShell 예에서는 문제의 Runbook을 실행하는 마지막 사용자를 제공합니다.

```powershell-interactive
$SubID = "00000000-0000-0000-0000-000000000000"
$AutomationResourceGroupName = "MyResourceGroup"
$AutomationAccountName = "MyAutomationAccount"
$RunbookName = "MyRunbook"
$StartTime = (Get-Date).AddDays(-1)
$JobActivityLogs = Get-AzureRmLog -ResourceGroupName $AutomationResourceGroupName -StartTime $StartTime `
                                | Where-Object {$_.Authorization.Action -eq "Microsoft.Automation/automationAccounts/jobs/write"}

$JobInfo = @{}
foreach ($log in $JobActivityLogs)
{
    # Get job resource
    $JobResource = Get-AzureRmResource -ResourceId $log.ResourceId

    if ($JobInfo[$log.SubmissionTimestamp] -eq $null -and $JobResource.Properties.runbook.name -eq $RunbookName)
    {
        # Get runbook
        $Runbook = Get-AzureRmAutomationJob -ResourceGroupName $AutomationResourceGroupName -AutomationAccountName $AutomationAccountName `
                                            -Id $JobResource.Properties.jobId | ? {$_.RunbookName -eq $RunbookName}

        # Add job information to hash table
        $JobInfo.Add($log.SubmissionTimestamp, @($Runbook.RunbookName,$Log.Caller, $JobResource.Properties.jobId))
    }
}
$JobInfo.GetEnumerator() | sort key -Descending | Select-Object -First 1
```

## <a name="fair-share"></a>공평 분배

为了在云中的所有 runbook 之间共享资源，Azure 自动化会临时卸载或停止运行超过三个小时的任何作业。 [PowerShell 기반 Runbook](automation-runbook-types.md#powershell-runbooks) 및 [Python Runbook](automation-runbook-types.md#python-runbooks)의 작업은 중지된 후 다시 시작되지 않으며 작업 상태는 중지됨으로 표시됩니다.

장기 실행 작업의 경우 [Hybrid Runbook Worker](automation-hrw-run-runbooks.md#job-behavior)를 사용하는 것이 좋습니다. Hybrid Runbook Worker는 공평 분배로 제한되지 않으며, Runbook을 실행할 수 있는 기간에 대한 제한이 없습니다. 다른 작업 [제한](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)은 Azure 샌드박스 및 Hybrid Runbook Worker에 모두 적용됩니다. 虽然混合 Runbook 辅助角色不受3小时公平份额限制的限制，但应开发运行 runbook 以支持来自意外的本地基础结构问题的重启行为。

또 다른 옵션은 자식 Runbook을 사용하여 Runbook을 최적화하는 것입니다. Runbook이 여러 데이터베이스의 데이터베이스 작업과 같이 여러 리소스에서 동일한 함수를 반복하는 경우, 해당 함수를 [자식 Runbook](automation-child-runbooks.md)으로 이동하고 [Start-AzureRMAutomationRunbook](/powershell/module/azurerm.automation/start-azurermautomationrunbook) cmdlet을 사용하여 이를 호출할 수 있습니다. 이러한 자식 Runbook은 각각 개별 프로세스에서 병렬로 실행됩니다. 이 동작은 부모 Runbook이 완료되는 총 기간을 감소시킵니다. 如果子 runbook 完成后执行了某些操作，则可以在 runbook 中使用[get-azurermautomationjob](/powershell/module/azurerm.automation/Get-AzureRmAutomationJob) cmdlet 来检查每个子项的作业状态。

## <a name="next-steps"></a>다음 단계

* Azure Automation에서 runbook을 시작하는 데 사용할 수 있는 여러 가지 방법에 대해 자세히 알아보려면 [Azure Automation에서 Runbook 시작](automation-starting-a-runbook.md)을 참조하세요.
* 有关 PowerShell 的详细信息，包括语言参考和学习模块，请参阅[Powershell 文档](https://docs.microsoft.com/powershell/scripting/overview)。
