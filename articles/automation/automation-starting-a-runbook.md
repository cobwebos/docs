---
title: 在 Azure 自动化中启动 Runbook
description: 汇总了可用于在 Azure 自动化中启动 Runbook 的不同方法，并提供有关使用 Azure 门户和 Windows PowerShell 的详细信息。
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 45ac19ad5011ae67e95281d1c9928c1db4bc7043
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/11/2018
---
# <a name="starting-a-runbook-in-azure-automation"></a>在 Azure 自动化中启动 Runbook
下表将帮助你确定如何在 Azure 自动化中以最适合你方案的方法启动 Runbook。 本文包含有关使用 Azure 门户和 Windows PowerShell 启动 Runbook 的详细信息。 有关其他方法的详细信息会在其他文档中提供，可以通过以下链接来访问。

| **方法** | **特征** |
| --- | --- |
| [Azure 门户](#starting-a-runbook-with-the-azure-portal) |<li>使用交互式用户界面的最简单方法。<br> <li>用于提供简单参数值的窗体。<br> <li>轻松跟踪作业状态。<br> <li>使用 Azure 登录对访问进行身份验证。 |
| [Windows PowerShell](https://msdn.microsoft.com/library/dn690259.aspx) |<li>使用 Windows PowerShell cmdlet 从命令行调用。<br> <li>可以使用多个步骤包含在自动化解决方案中。<br> <li>使用证书或 OAuth 用户主体/服务主体对请求进行身份验证。<br> <li>提供简单和复杂的参数值。<br> <li>跟踪作业状态。<br> <li>支持 PowerShell cmdlet 所需的客户端。 |
| [Azure 自动化 API](https://msdn.microsoft.com/library/azure/mt662285.aspx) |<li>最有弹性的方法，但也最复杂。<br> <li>从任何可发出 HTTP 请求的自定义代码调用。<br> <li>使用证书或 OAuth 用户主体/服务主体对请求进行身份验证。<br> <li>提供简单和复杂的参数值。 *如果你正在调用 Python runbook 使用 API 时，必须序列化 JSON 有效负载。*<br> <li>跟踪作业状态。 |
| [Webhook](automation-webhooks.md) |<li>从单个 HTTP 请求启动 Runbook。<br> <li>使用 URL 中的安全令牌进行身份验证。<br> <li>客户端无法覆盖创建 Webhook 时指定的参数值。 Runbook 可以定义填入了 HTTP 请求详细信息的单个参数。<br> <li>无法通过 Webhook URL 跟踪作业状态。 |
| [响应 Azure 警报](../log-analytics/log-analytics-alerts.md) |<li>启动 Runbook 以响应 Azure 警报。<br> <li>为 Runbook 配置 webhook 并链接到警报。<br> <li>使用 URL 中的安全令牌进行身份验证。 |
| [计划](automation-schedules.md) |<li>按每小时、每天、每周或每月计划自动启动 Runbook。<br> <li>通过 Azure 门户、PowerShell cmdlet 或 Azure API 来操作计划。<br> <li>提供与计划配置使用的参数值。 |
| [从另一个 Runbook](automation-child-runbooks.md) |<li>使用一个 Runbook 作为另一个 Runbook 中的活动。<br> <li>对多个 Runbook 使用的功能很有用。<br> <li>为子 Runbook 提供参数值，并使用父 Runbook 中的输出。 |

下图演示了 Runbook 生命周期的详细分步过程。 它包括在 Azure 自动化中启动 Runbook 的不同方式、混合 Runbook 辅助角色执行 Azure 自动化 Runbook 所需的组件以及不同组件之间的交互方式。 若要了解如何在数据中心执行自动化 Runbook，请参阅[混合 Runbook 辅助角色](automation-hybrid-runbook-worker.md)

![Runbook 体系结构](media/automation-starting-runbook/runbooks-architecture.png)

## <a name="starting-a-runbook-with-the-azure-portal"></a>使用 Azure 门户启动 Runbook
1. 在 Azure 门户中，选择“自动化”，并单击自动化帐户的名称。
2. 在“中心”菜单中选择“Runbook”。
3. 在“Runbook”页上，选择 Runbook 并单击“启动”。
4. 如果 Runbook 包含参数，则系统会提示在文本框中提供每个参数的值。 请参阅下面的 [Runbook 参数](#Runbook-parameters)，获取有关参数的更多详细信息。
5. 在“作业”页上，可以查看 runbook 作业的状态。

## <a name="starting-a-runbook-with-windows-powershell"></a>使用 Windows PowerShell 启动 Runbook
可以在 Windows PowerShell 中使用 [Start-AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt603661.aspx) 启动 Runbook。 以下示例代码将启动名为 Test-Runbook 的 Runbook。

```
Start-AzureRmAutomationRunbook -AutomationAccountName "MyAutomationAccount" -Name "Test-Runbook" -ResourceGroupName "ResourceGroup01"
```

Start-AzureRmAutomationRunbook 将返回一个作业对象，启动 Runbook 后，可以使用该对象来跟踪 Runbook 的状态。 然后可结合使用此作业对象和 [Get-AzureRmAutomationJob](https://msdn.microsoft.com/library/mt619440.aspx)，以确定作业的状态，也可以结合使用此作业对象和 [Get-AzureRmAutomationJobOutput](https://msdn.microsoft.com/library/mt603476.aspx)，以获取作业的输出。 以下示例代码将启动名为 Test-Runbook 的 Runbook，等待它完成，并显示其输出。

```
$runbookName = "Test-Runbook"
$ResourceGroup = "ResourceGroup01"
$AutomationAcct = "MyAutomationAccount"

$job = Start-AzureRmAutomationRunbook –AutomationAccountName $AutomationAcct -Name $runbookName -ResourceGroupName $ResourceGroup

$doLoop = $true
While ($doLoop) {
   $job = Get-AzureRmAutomationJob –AutomationAccountName $AutomationAcct -Id $job.JobId -ResourceGroupName $ResourceGroup
   $status = $job.Status
   $doLoop = (($status -ne "Completed") -and ($status -ne "Failed") -and ($status -ne "Suspended") -and ($status -ne "Stopped"))
}

Get-AzureRmAutomationJobOutput –AutomationAccountName $AutomationAcct -Id $job.JobId -ResourceGroupName $ResourceGroup –Stream Output
```

如果 Runbook 需要参数，则必须以[哈希表](http://technet.microsoft.com/library/hh847780.aspx)的形式提供参数，其中，哈希表的密钥与参数名称匹配，值为参数值。 以下示例演示如何启动包含两个名称分别为 FirstName 和 LastName 的字符串参数、一个名为 RepeatCount 的整数和一个名为 Show 的布尔参数的 Runbook。 有关参数的其他信息，请参阅下面的 [Runbook 参数](#Runbook-parameters)。

```
$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
Start-AzureRmAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -ResourceGroupName "ResourceGroup01" –Parameters $params
```

## <a name="runbook-parameters"></a>Runbook 参数
使用 Azure 门户或 Windows PowerShell 启动 Runbook 时，系统将通过 Azure 自动化 Web 服务发送指令。 此服务不支持复杂数据类型的参数。 如果需要提供复杂参数的值，则必须根据 [Azure 自动化中的子 Runbook](automation-child-runbooks.md) 中所述，以内联方式从另一个 Runbook 调用该参数值。

Azure 自动化 Web 服务将为使用特定数据类型的参数提供特殊功能，如以下部分中所述。

### <a name="named-values"></a>命名值
如果参数的数据类型为 [object]，则可以使用以下 JSON 格式向它发送命名值列表：*{Name1:'Value1', Name2:'Value2', Name3:'Value3'}*。 这些值必须使用简单类型。 Runbook 以 [PSCustomObject](https://msdn.microsoft.com/library/system.management.automation.pscustomobject%28v=vs.85%29.aspx) 的形式接收参数，该对象的属性对应于每个命名值。

请考虑以下接受名为 user 的参数的测试 Runbook。

```
Workflow Test-Parameters
{
   param (
      [Parameter(Mandatory=$true)][object]$user
   )
    $userObject = $user | ConvertFrom-JSON
    if ($userObject.Show) {
        foreach ($i in 1..$userObject.RepeatCount) {
            $userObject.FirstName
            $userObject.LastName
        }
    }
}
```

可为 user 参数使用以下文本。

```
{FirstName:'Joe',LastName:'Smith',RepeatCount:'2',Show:'True'}
```

这会导致生成以下输出：

```
Joe
Smith
Joe
Smith
```

### <a name="arrays"></a>数组
如果参数是数组（如 [array] 或 [string[]]），则可以使用以下 JSON 格式向它发送值列表：*[Value1,Value2,Value3]*。 这些值必须使用简单类型。

请考虑以下接受名为 *user* 的参数的测试 Runbook。

```
Workflow Test-Parameters
{
   param (
      [Parameter(Mandatory=$true)][array]$user
   )
    if ($user[3]) {
        foreach ($i in 1..$user[2]) {
            $ user[0]
            $ user[1]
        }
    }
}
```

可为 user 参数使用以下文本。

```
["Joe","Smith",2,true]
```

这会导致生成以下输出：

```
Joe
Smith
Joe
Smith
```

### <a name="credentials"></a>凭据
如果参数的数据类型为 **PSCredential**，则可以提供 Azure 自动化[凭据资产](automation-credentials.md)的名称。 Runbook 将检索具有指定名称的凭据。

请考虑以下接受名为 credential 的参数的测试 Runbook。

```
Workflow Test-Parameters
{
   param (
      [Parameter(Mandatory=$true)][PSCredential]$credential
   )
   $credential.UserName
}
```

假设存在名为 *My Credential* 的凭据资产，则可为 user 参数使用以下文本。

```
My Credential
```

假设凭据中的用户名为 *jsmith*，则会导致生成以下输出。

```
jsmith
```

## <a name="next-steps"></a>后续步骤
* 本文中的 Runbook 体系结构提供了在本地和 Azure 中使用混合 Runbook 辅助角色管理资源的 Runbook 的高级概述。 若要了解如何在数据中心执行自动化 Runbook，请参阅[混合 Runbook 辅助角色](automation-hybrid-runbook-worker.md)。
* 若要详细了解如何创建模块化 Runbook，以供其他 Runbook 用于特定或常用函数，请参阅[子 Runbook](automation-child-runbooks.md)。

