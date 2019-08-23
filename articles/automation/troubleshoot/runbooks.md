---
title: Azure 自动化 Runbook 错误故障排除
description: 了解如何解决 Azure 自动化 Runbook 的问题
services: automation
author: bobbytreed
ms.author: robreed
ms.date: 01/24/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: c6b526cdd317e8b075d28e0fb9018501148c731c
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/22/2019
ms.locfileid: "69971300"
---
# <a name="troubleshoot-errors-with-runbooks"></a>Runbook 错误故障排除

本文详细介绍了 runbook 的常见问题以及如何解决这些问题。

## <a name="steps-to-troubleshoot-runbooks"></a>对 runbook 进行故障排除的步骤

如果在 Azure Automation 中执行 runbook 时出现错误, 则可以使用以下步骤来帮助诊断问题。

1. **确保你的 runbook 脚本在本地计算机上成功执行:** 参阅[PowerShell 文档](/powershell/scripting/overview)或[Python 文档](https://docs.python.org/3/), 了解语言参考和学习模块。

   在本地执行脚本可以发现和解决常见错误, 如:

   - **缺少模块**
   - **语法错误**
   - **逻辑错误**

2. 调查特定消息的 runbook [错误流](https://docs.microsoft.com/azure/automation/automation-runbook-output-and-messages#runbook-output)并将其与下面的错误消息进行比较。

3. **确保节点和自动化工作区具有所需的模块:** 如果你的 runbook 导入任何模块, 请使用[导入模块](../shared-resources/modules.md#import-modules)中列出的步骤确保它们可用于你的自动化帐户。 按照在[Azure 自动化中更新 azure 模块中](..//automation-update-azure-modules.md)的说明, 将模块更新到最新版本。 有关疑难解答的详细信息, 请参阅[排查模块问题](shared-resources.md#modules)。

### <a name="if-the-runbook-is-suspended-or-unexpectedly-failed"></a>如果 Runbook 已挂起或意外失败

有多个原因可能会导致 runbook 挂起或失败：

* [作业状态](https://docs.microsoft.com/azure/automation/automation-runbook-execution#job-statuses)定义 runbook 状态和某些可能的原因。
* [将其他输出添加](https://docs.microsoft.com/azure/automation/automation-runbook-output-and-messages#message-streams)到 runbook, 以标识在挂起 runbook 之前会发生的情况。
* 处理作业引发的[任何异常](https://docs.microsoft.com/azure/automation/automation-runbook-execution#handling-exceptions)。

## <a name="authentication-errors-when-working-with-azure-automation-runbooks"></a>使用 Azure 自动化 Runbook 时遇到的身份验证错误

### <a name="login-azurerm"></a>场景：运行 Add-azurermaccount 登录

如果你使用的不是运行方式帐户或者运行方式帐户已过期，则可能会发生此错误。 请参阅[管理 Azure Automation 运行方式帐户](https://docs.microsoft.com/azure/automation/manage-runas-account)。

#### <a name="issue"></a>问题

执行 runbook 时, 会收到以下错误:

```error
Run Login-AzureRMAccount to login.
```

#### <a name="cause"></a>原因

此错误有两个主要原因:

* 不同版本的 AzureRM 模块。
* 你正在尝试访问单独订阅中的资源。

#### <a name="resolution"></a>分辨率

如果在更新一个 AzureRM 模块后收到此错误, 则应将所有 AzureRM 模块更新为同一版本。

如果尝试访问其他订阅中的资源, 则可以按照以下步骤配置权限。

1. 中转到自动化帐户的运行方式帐户, 并复制应用程序 ID 和指纹。
  ![复制应用程序 ID 和指纹](../media/troubleshoot-runbooks/collect-app-id.png)
1. 访问不承载自动化帐户的订阅的访问控制, 并添加新的角色分配。
  ![访问控制](../media/troubleshoot-runbooks/access-control.png)
1. 添加在上一步中收集的应用程序 ID。 选择 "参与者权限"。
   ![添加角色分配](../media/troubleshoot-runbooks/add-role-assignment.png)
1. 复制订阅的名称以进行下一步。
1. 你现在可以使用以下 runbook 代码来测试从自动化帐户到其他订阅的权限。

    将 "\<CertificateThumbprint\>" 替换为在步骤 #1 中复制的值以及在步骤\<#4\>中复制的 "SubscriptionName" 值。

    ```powershell
    $Conn = Get-AutomationConnection -Name AzureRunAsConnection
    Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint "<CertificateThumbprint>"
    #Select the subscription you want to work with
    Select-AzureRmSubscription -SubscriptionName '<YourSubscriptionNameGoesHere>'

    #Test and get outputs of the subscriptions you granted access.
    $subscriptions = Get-AzureRmSubscription
    foreach($subscription in $subscriptions)
    {
        Set-AzureRmContext $subscription
        Write-Output $subscription.Name
    }
    ```

### <a name="sign-in-failed"></a>场景：登录 Azure 帐户失败

#### <a name="issue"></a>问题

使用 `Add-AzureAccount` 或 `Connect-AzureRmAccount` cmdlet 时收到以下错误：

```error
Unknown_user_type: Unknown User Type
```

#### <a name="cause"></a>原因

如果凭据资产名称无效，将发生此错误。 如果用于设置自动化凭据资产的用户名和密码无效，也可能会出现此错误。

#### <a name="resolution"></a>分辨率

要确定具体错误，请执行以下步骤：

1. 请确保没有包含任何特殊字符。 这些字符包括用于连接 Azure 的自动化凭据资产名称中的 \@ 字符。
2. 查看你是否能够在本地 PowerShell ISE 编辑器中使用存储在 Azure 自动化凭据中的用户名和密码。 可以通过在 PowerShell ISE 中运行以下 cmdlet 来检查用户名和密码是否正确：

   ```powershell
   $Cred = Get-Credential
   #Using Azure Service Management
   Add-AzureAccount –Credential $Cred
   #Using Azure Resource Manager
   Connect-AzureRmAccount –Credential $Cred
   ```

3. 如果无法在本地进行身份验证，则意味着你尚未设置好 Azure Active Directory 凭据。 请参阅[使用 Azure Active Directory 向 Azure 进行身份验证](https://azure.microsoft.com/blog/azure-automation-authenticating-to-azure-using-azure-active-directory/)博客文章，了解如何正确设置 Azure Active Directory 帐户。

4. 如果它看起来是暂时性错误，请尝试向身份验证例程添加重试逻辑，使身份验证更加可靠。

   ```powershell
   # Get the connection "AzureRunAsConnection"
   $connectionName = "AzureRunAsConnection"
   $servicePrincipalConnection = Get-AutomationConnection -Name $connectionName

   $logonAttempt = 0
   $logonResult = $False

   while(!($connectionResult) -And ($logonAttempt -le 10))
   {
   $LogonAttempt++
   # Logging in to Azure...
   $connectionResult = Connect-AzureRmAccount `
      -ServicePrincipal `
      -TenantId $servicePrincipalConnection.TenantId `
      -ApplicationId $servicePrincipalConnection.ApplicationId `
      -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint

   Start-Sleep -Seconds 30
   }
   ```

### <a name="unable-to-find-subscription"></a>场景：无法找到 Azure 订阅

#### <a name="issue"></a>问题

使用 `Select-AzureSubscription` 或 `Select-AzureRmSubscription` cmdlet 时收到以下错误：

```error
The subscription named <subscription name> cannot be found.
```

#### <a name="error"></a>Error

以下情况下可能发生此错误：

* 订阅名称无效

* 尝试获取订阅详细信息的 Azure Active Directory 用户未配置为订阅的管理员。

#### <a name="resolution"></a>分辨率

执行以下步骤以确定是否已正确向 Azure 进行身份验证并有权访问尝试选择的订阅：

1. 在 Azure 自动化之外测试脚本，以确保它独立运行。
2. 请确保在运行 `Select-AzureSubscription` cmdlet 之前运行 `Add-AzureAccount` cmdlet。
3. 将 `Disable-AzureRmContextAutosave –Scope Process` 添加到 runbook 的开头。 此 cmdlet 可以确保任何凭据都仅适用于当前 runbook 的执行。
4. 如果仍然看到此错误消息，可通过在 `Add-AzureAccount` cmdlet 后添加 **AzureRmContext** 参数来修改代码，然后执行代码。

   ```powershell
   Disable-AzureRmContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $context = Get-AzureRmContext

   Get-AzureRmVM -ResourceGroupName myResourceGroup -AzureRmContext $context
    ```

### <a name="auth-failed-mfa"></a>场景：无法向 Azure 进行身份验证，因为已启用多重身份验证

#### <a name="issue"></a>问题

使用 Azure 用户名和密码向 Azure 进行身份验证时收到以下错误：

```error
Add-AzureAccount: AADSTS50079: Strong authentication enrollment (proof-up) is required
```

#### <a name="cause"></a>原因

如果对 Azure 帐户设置了多重身份验证，则不能使用 Azure Active Directory 用户向 Azure 进行身份验证。 而只能使用证书或服务主体向 Azure 进行身份验证。

#### <a name="resolution"></a>分辨率

要将证书用于 Azure 经典部署模型 cmdlet，请参阅[创建并添加管理 Azure 服务所需的证书](https://blogs.technet.com/b/orchestrator/archive/2014/04/11/managing-azure-services-with-the-microsoft-azure-automation-preview-service.aspx)。 要将服务主体用于 Azure 资源管理器 cmdlet，请参阅[使用 Azure 门户创建服务主体](../../active-directory/develop/howto-create-service-principal-portal.md)和[通过 Azure 资源管理器对服务主体进行身份验证](../../active-directory/develop/howto-authenticate-service-principal-powershell.md)。

## <a name="common-errors-when-working-with-runbooks"></a>使用 Runbook 时的常见错误

### <a name="child-runbook-object"></a>输出流包含对象而不是简单数据类型时，子 runbook 返回错误

#### <a name="issue"></a>问题

当使用`-Wait`开关调用子 runbook 并且输出流包含对象时, 会收到以下错误:

```error
Object reference not set to an instance of an object
```

#### <a name="cause"></a>原因

有一个已知问题，如果 [Start-AzureRmAutomationRunbook](/powershell/module/AzureRM.Automation/Start-AzureRmAutomationRunbook) 包含对象，则它无法正确处理输出流。

#### <a name="resolution"></a>分辨率

要解决此问题，建议改为实现轮询逻辑并使用 [Get-AzureRmAutomationJobOutput](/powershell/module/azurerm.automation/get-azurermautomationjoboutput) cmdlet 来检索输出。 下面的示例中定义了此逻辑的示例。

```powershell
$automationAccountName = "ContosoAutomationAccount"
$runbookName = "ChildRunbookExample"
$resourceGroupName = "ContosoRG"

function IsJobTerminalState([string] $status) {
    return $status -eq "Completed" -or $status -eq "Failed" -or $status -eq "Stopped" -or $status -eq "Suspended"
}

$job = Start-AzureRmAutomationRunbook -AutomationAccountName $automationAccountName -Name $runbookName -ResourceGroupName $resourceGroupName
$pollingSeconds = 5
$maxTimeout = 10800
$waitTime = 0
while((IsJobTerminalState $job.Status) -eq $false -and $waitTime -lt $maxTimeout) {
   Start-Sleep -Seconds $pollingSeconds
   $waitTime += $pollingSeconds
   $job = $job | Get-AzureRmAutomationJob
}

$jobResults | Get-AzureRmAutomationJobOutput | Get-AzureRmAutomationJobOutputRecord | Select-Object -ExpandProperty Value
```

### <a name="get-serializationsettings"></a>场景：作业流中出现了关于 get_SerializationSettings 方法的错误

#### <a name="issue"></a>问题

Runbook 的作业流中出现错误并显示了以下消息：

```error
Connect-AzureRMAccount : Method 'get_SerializationSettings' in type
'Microsoft.Azure.Management.Internal.Resources.ResourceManagementClient' from assembly
'Microsoft.Azure.Commands.ResourceManager.Common, Version=4.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35'
does not have an implementation.
At line:16 char:1
+ Connect-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -Appl ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : NotSpecified: (:) [Connect-AzureRmAccount], TypeLoadException
    + FullyQualifiedErrorId : System.TypeLoadException,Microsoft.Azure.Commands.Profile.ConnectAzureRmAccountCommand
```

#### <a name="cause"></a>原因

此错误是因为在同一 runbook 中同时使用 AzureRM 和 Az cmdlet 而导致的。 在导入 `AzureRM` 之前导入 `Az` 会出现此错误。

#### <a name="resolution"></a>分辨率

不能在同一 runbook 中同时导入并使用 Az 和 AzureRM cmdlet，若要详细了解 Azure 自动化中对 Az 的支持，请参阅 [Azure 自动化中的 Az 模块支持](../az-modules.md)。

### <a name="task-was-cancelled"></a>场景：Runbook 失败并显示错误：任务已取消

#### <a name="issue"></a>问题

Runbook 失败，出现类似于以下示例的错误：

```error
Exception: A task was canceled.
```

#### <a name="cause"></a>原因

使用过时的 Azure 模块会导致此错误。

#### <a name="resolution"></a>分辨率

可以通过将 Azure 模块更新到最新版本来解决此错误。

在自动化帐户中，单击“模块”，然后单击“更新 Azure 模块”。 更新需要花费大约 15 分钟，完成后，重新运行失败的 Runbook。 若要了解有关更新模块的详细信息，请参阅[在 Azure 自动化中更新 Azure 模块](../automation-update-azure-modules.md)。

### <a name="runbook-auth-failure"></a>场景：处理多个订阅时，Runbook 失败

#### <a name="issue"></a>问题

使用 `Start-AzureRmAutomationRunbook` 执行 Runbook 时，Runbook 无法管理 Azure 资源。

#### <a name="cause"></a>原因

Runbook 在运行时没有使用正确的上下文。

#### <a name="resolution"></a>分辨率

如果使用多个订阅，则调用 Runbook 时可能会丢失订阅上下文。 若要确保将订阅上下文传递给 Runbook，请将 `AzureRmContext` 参数添加到 cmdlet 并将上下文传递给它。 还建议将 `Disable-AzureRmContextAutosave` cmdlet 与 **Process** 范围配合使用来确保你使用的凭据仅用于当前 runbook。

```azurepowershell-interactive
# Ensures that any credentials apply only to the execution of this runbook
Disable-AzureRmContextAutosave –Scope Process

# Connect to Azure with RunAs account
$ServicePrincipalConnection = Get-AutomationConnection -Name 'AzureRunAsConnection'

Add-AzureRmAccount `
    -ServicePrincipal `
    -TenantId $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint

$AzureContext = Select-AzureRmSubscription -SubscriptionId $ServicePrincipalConnection.SubscriptionID

$params = @{"VMName"="MyVM";"RepeatCount"=2;"Restart"=$true}

Start-AzureRmAutomationRunbook `
    –AutomationAccountName 'MyAutomationAccount' `
    –Name 'Test-ChildRunbook' `
    -ResourceGroupName 'LabRG' `
    -AzureRmContext $AzureContext `
    –Parameters $params –wait
```

### <a name="not-recognized-as-cmdlet"></a>场景：由于缺少 cmdlet，Runbook 失败

#### <a name="issue"></a>问题

Runbook 失败，出现类似于以下示例的错误：

```error
The term 'Connect-AzureRmAccount' is not recognized as the name of a cmdlet, function, script file, or operable program.  Check the spelling of the name, or if the path was included verify that the path is correct and try again.
```

#### <a name="cause"></a>原因

发生此错误是由于以下原因之一：

* 包含该 cmdlet 的模块未导入到自动化帐户中
* 包含该 cmdlet 的模块已导入但已过期

#### <a name="resolution"></a>分辨率

可通过完成以下任务之一来解决此错误：

如果该模块是 Azure 模块，请参阅[如何在 Azure 自动化中更新 Azure PowerShell 模块](../automation-update-azure-modules.md)，以了解如何更新自动化帐户中的模块。

如果它是一个单独的模块，请确保该模块已导入到自动化帐户中。

### <a name="job-attempted-3-times"></a>场景：Runbook 作业已尝试启动三次，但每次均启动失败

#### <a name="issue"></a>问题

Runbook 失败并显示错误：

```error
The job was tried three times but it failed
```

#### <a name="cause"></a>原因

发生此错误是由于以下问题之一：

* 内存限制。 可以在[自动化服务限制](../../azure-subscription-service-limits.md#automation-limits)中找到有关分配给沙盒的内存量的限制。 如果作业使用的内存超过 400 MB，则它可能会失败。

* 网络套接字。 如[自动化服务限制](../../azure-subscription-service-limits.md#automation-limits)中所述，Azure 沙盒限制为 1000 个并发网络套接字。

* 模块不兼容。 如果模块依赖关系不正确，则可能会发生此错误，并且如果它们不正确，则 runbook 通常会返回“找不到命令”或“无法绑定参数”消息。

* Runbook 尝试调用在 Azure 沙盒中运行的 Runbook 中的可执行文件或子过程。 Azure 沙盒不支持此方案。

* runbook 尝试向输出流写入太多异常数据。

#### <a name="resolution"></a>分辨率

下述解决方案中的任何一种都可以解决此问题：

* 在内存限制内工作的建议方法是拆分多个 runbook 之间的工作负载，不作为内存中的诸多数据进行处理，不写入不必要的 runbook 输出，或考虑在 PowerShell 工作流 runbook 中写入多少个检查点。 可以使用 clear 方法（例如 `$myVar.clear()`）清除变量并使用 `[GC]::Collect()` 立即运行垃圾回收。 这将减少运行时期间 runbook 的内存占用情况。

* 按照[如何更新 Azure 自动化中的 Azure PowerShell 模块](../automation-update-azure-modules.md)中的步骤更新 Azure 模块。

* 另一个解决方案是在[混合 Runbook 辅助角色](../automation-hrw-run-runbooks.md)上运行 runbook。 混合辅助角色不受内存和网络限制，而 Azure 沙盒则受限于此限制。

* 如果需要在 Runbook 中调用进程（如 .exe 或 subprocess.call），则需要在[混合 Runbook 辅助角色](../automation-hrw-run-runbooks.md)上运行 Runbook。

* 作业输出流限制为 1MB。 请确保在 try/catch 块中包含对可执行文件或子进程的调用。 如果这些调用引发异常，请将该异常中的消息写入自动化变量中。 这将防止将其写入作业输出流中。

### <a name="fails-deserialized-object"></a>场景：Runbook 因反序列化的对象而失败

#### <a name="issue"></a>问题

Runbook 失败并显示错误：

```error
Cannot bind parameter <ParameterName>.

Cannot convert the <ParameterType> value of type Deserialized <ParameterType> to type <ParameterType>.
```

#### <a name="cause"></a>原因

如果 runbook 为 PowerShell 工作流，它会将复杂对象以反序列化格式进行存储，以便在工作流暂停的情况下保留 runbook 状态。

#### <a name="resolution"></a>分辨率

下述三种解决方案中的任何一种都可以解决此问题：

* 如果要将复杂对象从一个 cmdlet 传送到另一个 cmdlet，则可将这两个 cmdlet 包装在 InlineScript 中。
* 传递复杂对象中你所需要的名称或值，不必传递整个对象。
* 使用 PowerShell Runbook，而不使用 PowerShell 工作流 Runbook。

### <a name="runbook-fails"></a>场景：我的 Runbook 只能在本地运行

#### <a name="issue"></a>问题

脚本作为 Runbook 运行时失败但可以在本地运行。

#### <a name="cause"></a>原因

脚本作为 Runbook 运行会失败是由于以下原因之一：

* 身份验证问题
* 所需的模块未导入或已过期。
* 脚本可能会提示输入用户交互。
* 某些模块对 Windows 计算机上存在的库进行假设。 这些库可能不存在于沙盒中。
* 某些模块依赖的 .NET 版本不同于沙盒中可用的版本。

#### <a name="resolution"></a>分辨率

下述任一解决方案可能可以解决此问题：

* 验证是否正确[对Azure 进行身份验证](../manage-runas-account.md)。
* 请确保 [Azure 模块已导入且为最新](../automation-update-azure-modules.md)。
* 确认没有任何 cmdlet 提示输入信息。 Runbook 中不支持此行为。
* 检查模块中的任何内容是否依赖于模块中未包含的内容。
* Azure 沙盒使用 .NET Framework 4.7.2，如果模块使用更高版本，则无法使用。 在这种情况下，应使用 [混合 Runbook 辅助角色](../automation-hybrid-runbook-worker.md)

如果使用这些解决方案均未解决问题，请查看[作业日志](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal)以了解 Runbook 失败的特定详细信息。

### <a name="quota-exceeded"></a>场景：Runbook 作业失败，因为超过了分配的配额

#### <a name="issue"></a>问题

Runbook 作业失败并显示错误：

```error
The quota for the monthly total job run time has been reached for this subscription
```

#### <a name="cause"></a>原因

作业执行时间超过帐户的 500 分钟免费配额时，就会出现此错误。 此配额适用于所有类型的作业执行任务。 其中一些任务可能是测试作业、从门户启动作业、使用 Webhook 执行作业，以及通过 Azure 门户或数据中心计划要执行的作业。 若要详细了解自动化的定价，请参阅[自动化定价](https://azure.microsoft.com/pricing/details/automation/)。

#### <a name="resolution"></a>分辨率

如果想要每月使用 500 分钟以上的处理时间，则需将订阅从免费层改为基本层。 可以通过下述步骤升级到基本层：

1. 登录到 Azure 订阅
2. 选择要升级的自动化帐户
3. 单击“设置” > “定价”。
4. 单击页面底部的“启用”，以将帐户升级到“基本”层。

### <a name="cmdlet-not-recognized"></a>场景：在执行 Runbook 时无法识别 cmdlet

#### <a name="issue"></a>问题

Runbook 作业失败并显示错误：

```error
<cmdlet name>: The term <cmdlet name> is not recognized as the name of a cmdlet, function, script file, or operable program.
```

#### <a name="cause"></a>原因

当 PowerShell 引擎找不到要在 runbook 中使用的 cmdlet 时，则会导致此错误。 此错误可能是因为，帐户中缺少包含该 cmdlet 的模块、与 Runbook 名称冲突，或者该 cmdlet 也存在于其他模块中，而自动化无法解析该名称。

#### <a name="resolution"></a>分辨率

下述解决方案中的任何一种都可以解决此问题：

* 检查输入的 cmdlet 名称是否正确。
* 确保 cmdlet 存在于自动化帐户中，且没有冲突。 要验证 cmdlet 是否存在，请在编辑模式下打开 Runbook，并搜索希望在库中找到的 cmdlet，或者运行 `Get-Command <CommandName>`。 验证该 cmdlet 可供帐户使用且与其他 cmdlet 或 runbook 不存在名称冲突以后，可将其添加到画布上，并确保使用的是 runbook 中的有效参数集。
* 如果存在名称冲突且 cmdlet 可在两个不同的模块中使用，则可使用 cmdlet 的完全限定名称来解决此问题。 例如，可以使用 **ModuleName\CmdletName**。
* 如果是在本地执行混合辅助角色组中的 runbook，则请确保模块和 cmdlet 已安装在托管混合辅助角色的计算机上。

### <a name="long-running-runbook"></a>场景：长时间运行的 Runbook 无法完成

#### <a name="issue"></a>问题

运行 3 小时后，runbook 将显示处于“已停止”状态。 可能还会收到错误：

```error
The job was evicted and subsequently reached a Stopped state. The job cannot continue running
```

此行为是 Azure 沙盒的设计使然，因为 Azure 自动化中会对进程进行“公平份额”监视。 “公平份额”会自动停止执行时间超过 3 小时的 Runbook。 超过公平份额时间限制的 runbook 的状态因 runbook 类型而异。 PowerShell 和 Python runbook 设置为“已停止”状态。 PowerShell 工作流 runbook 设置为“失败”。

#### <a name="cause"></a>原因

Runbook 超出了 Azure 沙盒中公平份额允许的 3 小时限制。

#### <a name="resolution"></a>分辨率

建议的解决方案是在[混合 Runbook 辅助角色](../automation-hrw-run-runbooks.md)上运行 runbook。

混合辅助角色不受[公平份额](../automation-runbook-execution.md#fair-share) 3 小时 runbook 限制，而 Azure 沙盒受限于此限制。 应开发在混合 Runbook 辅助角色上运行的 Runbook，以便在出现意外的本地基础结构问题时支持重启行为。

另一种选择是通过创建[子 runbook](../automation-child-runbooks.md) 来优化 runbook。 如果 runbook 在多个资源上遍历同一函数，例如在多个数据库上执行某个数据库操作，则可将该函数移到子 runbook。 各个子 runbook 是在单独的进程中并行执行的。 此行为降低了完成父 runbook 所需的时间总量。

启用子 runbook 方案的 PowerShell cmdlet 是：

[Start-AzureRMAutomationRunbook](/powershell/module/AzureRM.Automation/Start-AzureRmAutomationRunbook) - 此 cmdlet 用于启动 runbook 并向其传递参数

[Get-AzureRmAutomationJob](/powershell/module/azurerm.automation/get-azurermautomationjob) - 在子 runbook 完成后，如果需要执行操作，可使用此 cmdlet 检查每个子 runbook 的作业状态。

### <a name="expired webhook"></a>场景：状态：调用 Webhook 时显示 400 错误请求

#### <a name="issue"></a>问题

在尝试调用 Azure 自动化 Runbook 的 Webhook 时，收到以下错误：

```error
400 Bad Request : This webhook has expired or is disabled
```

#### <a name="cause"></a>原因

尝试调用的 Webhook 已禁用，或者已过期。

#### <a name="resolution"></a>分辨率

如果 Webhook 处于禁用状态，可以通过 Azure 门户重新启用 Webhook。 如果 Webhook 已过期，需要删除并重新创建 Webhook。 如果尚未过期，只能[续订 Webhook](../automation-webhooks.md#renew-webhook)。

### <a name="429"></a>场景：429：当前的请求速率过大。 请重试

#### <a name="issue"></a>问题

在运行 `Get-AzureRmAutomationJobOutput` cmdlet 时收到以下错误消息：

```error
429: The request rate is currently too large. Please try again
```

#### <a name="cause"></a>原因

从具有多个[详细流](../automation-runbook-output-and-messages.md#verbose-stream)的 Runbook 中检索作业输出时，可能发生此错误。

#### <a name="resolution"></a>分辨率

可通过两种方法来解决此错误：

* 编辑 Runbook，并减少它发出的作业流数量。
* 减少运行 cmdlet 时要检索的流数量。 若要遵循此行为，可以为 `Get-AzureRmAutomationJobOutput` cmdlet 指定 `-Stream Output` 参数以仅检索输出流。 

### <a name="cannot-invoke-method"></a>场景：PowerShell 作业失败并出现错误:无法调用方法

#### <a name="issue"></a>问题

在 Azure 中运行的 runbook 中启动 PowerShell 作业时, 会收到以下错误消息:

```error
Exception was thrown - Cannot invoke method. Method invocation is supported only on core types in this language mode.
```

#### <a name="cause"></a>原因

当你在 Azure 中运行的 runbook 中启动 PowerShell 作业时, 可能会发生此错误。 出现这种情况的原因可能是, 在 Azure 沙盒中运行的 runbook 可能不会在[完整语言模式下](/powershell/module/microsoft.powershell.core/about/about_language_modes)运行)。

#### <a name="resolution"></a>分辨率

可通过两种方法来解决此错误：

* `Start-Job`使用`Start-AzureRmAutomationRunbook`启动 runbook, 而不是使用
* 如果 runbook 有此错误消息, 请在混合 Runbook 辅助角色上运行它

若要了解有关此行为以及 Azure 自动化 Runbook 的其他行为的详细信息, 请参阅[Runbook 行为](../automation-runbook-execution.md#runbook-behavior)。

## <a name="other"></a>：上面未列出我的问题

以下部分列出了其他常见错误以及支持文档, 以帮助你解决问题。

### <a name="hybrid-runbook-worker-doesnt-run-jobs-or-isnt-responding"></a>混合 runbook 辅助角色不运行作业，或者没有响应

如果使用混合辅助角色而不是在 Azure 自动化中运行作业, 可能需要对[混合辅助角色本身进行故障排除](https://docs.microsoft.com/azure/automation/troubleshoot/hybrid-runbook-worker)。

### <a name="runbook-fails-with-no-permission-or-some-variation"></a>Runbook 由于“无权限”或某个变动而失败

运行方式帐户对 Azure 资源的权限可能与当前帐户的权限不同。 请确保运行方式帐户[有权访问你的脚本中使用的任何资源](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)。

### <a name="runbooks-were-working-but-suddenly-stopped"></a>Runbook 在工作时突然停止

* 如果 runbook 以前已执行但已停止, 请[确保运行方式帐户未过期](https://docs.microsoft.com/azure/automation/manage-runas-account#cert-renewal)。
* 如果你使用 webhook 来启动 runbook，请[确保 webhook 未过期](https://docs.microsoft.com/azure/automation/automation-webhooks#renew-webhook)。

### <a name="issues-passing-parameters-into-webhooks"></a>将参数传递到 webhook 的问题

有关将参数传递到 webhook 的帮助, 请参阅[从 Webhook 启动 runbook](https://docs.microsoft.com/azure/automation/automation-webhooks#parameters)。

### <a name="issues-using-az-modules"></a>使用 Az 模块时出现问题

不支持在同一自动化帐户中同时使用 Az 模块和 AzureRM 模块。 有关更多详细信息, 请参阅[runbook 中的 Az 模块](https://docs.microsoft.com/azure/automation/az-modules)。

### <a name="runbook-job-completed-but-with-unexpected-results-or-errors"></a>Runbook 作业已完成, 但出现意外结果或错误

下面列出了具体问题及其解决方案，但我们强烈建议你首先尝试这两个故障排除步骤：

* 在 Azure 自动化中运行 runbook 之前, 先尝试在[本地运行](https://docs.microsoft.com/azure/automation/troubleshoot/runbooks#runbook-fails)它。 这可以弄清楚问题是 runbook 中的 bug 还是 Azure 自动化中的问题。
* 调查特定消息的 runbook [错误流](https://docs.microsoft.com/azure/automation/automation-runbook-output-and-messages#runbook-output)并将其与下面的错误消息进行比较。
* 向 runbook 添加[其他输出](https://docs.microsoft.com/azure/automation/automation-runbook-output-and-messages#message-streams), 以确定错误发生的位置。

### <a name="inconsistent-behavior-in-runbooks"></a>Runbook 中的行为不一致

按 [Runbook 执行](https://docs.microsoft.com/azure/automation/automation-runbook-execution#runbook-behavior)中的指南操作，避免并发作业出现问题、资源被创建多次或 Runbook 中出现其他计时敏感逻辑。

### <a name="switching-between-multiple-subscriptions-in-a-runbook"></a>在 Runbook 中的多个订阅之间切换

按照[使用多个订阅](https://docs.microsoft.com/azure/automation/automation-runbook-execution#working-with-multiple-subscriptions)中的指导进行操作。

### <a name="runbook-fails-with-error-the-subscription-cannot-be-found"></a>Runbook 失败并出现错误:找不到订阅

当 runbook 未使用运行方式帐户来访问 Azure 资源时可能会发生此问题。 若要解决此问题，请按照[场景：无法找到 Azure 订阅](https://docs.microsoft.com/azure/automation/troubleshoot/runbooks#unable-to-find-subscription)中的步骤进行操作。

### <a name="error-your-azure-credentials-have-not-been-set-up-or-have-expired-please-run-connect-azurermaccount-to-set-up-your-azure-credentials"></a>错误：你的 azure 凭据尚未设置或已过期, 请运行 Add-azurermaccount 以设置你的 azure 凭据

如果你使用的不是运行方式帐户或者运行方式帐户已过期，则可能会发生此错误。 请参阅[管理 Azure Automation 运行方式帐户](https://docs.microsoft.com/azure/automation/manage-runas-account)。

### <a name="error-run-login-azurermaccount-to-login"></a>错误：运行 Add-azurermaccount 登录

如果你使用的不是运行方式帐户或者运行方式帐户已过期，则可能会发生此错误。 请参阅[管理 Azure Automation 运行方式帐户](https://docs.microsoft.com/azure/automation/manage-runas-account)。

### <a name="runbook-fails-with-error-strong-authentication-enrollment-is-required"></a>Runbook 失败并出现错误:需要强身份验证注册

由于在 Runbook 故障排除指南中[启用了多重身份验证, 请参阅对 Azure 进行身份验证失败](https://docs.microsoft.com/azure/automation/troubleshoot/runbooks#auth-failed-mfa)。

### <a name="runbook-fails-with-the-errors-no-permission-forbidden-403-or-some-variation"></a>Runbook 失败, 并出现以下错误:无权限、禁止访问、403或某些变体

运行方式帐户对 Azure 资源的权限可能与当前帐户的权限不同。 确保运行方式帐户有[权访问](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)脚本中使用的任何资源。

### <a name="runbooks-were-working-but-suddenly-stopped"></a>Runbook 在工作时突然停止

* 如果 runbook 以前已执行但已停止, 请确保运行方式帐户[未过期](https://docs.microsoft.com/azure/automation/manage-runas-account#cert-renewal)。
* 如果使用 webhook 启动 runbook, 请确保 webhook[未过期](https://docs.microsoft.com/azure/automation/automation-webhooks#renew-webhook)。

### <a name="passing-parameters-into-webhooks"></a>将参数传递到 webhook

有关将参数传递到 webhook 的帮助, 请参阅[从 Webhook 启动 runbook](https://docs.microsoft.com/azure/automation/automation-webhooks#parameters)。

### <a name="error-the-term-is-not-recognized"></a>错误：字词无法识别

按照 "runbook 疑难解答指南" 中的 " [Cmdlet 无法识别](https://docs.microsoft.com/azure/automation/troubleshoot/runbooks#cmdlet-not-recognized)" 中的步骤进行操作

### <a name="errors-about-typedata"></a>有关 Update-typedata 的错误

如果收到有关 TypeData 的错误，则表明你在运行的一个 PowerShell 工作流包含不支持工作流的模块。 需将 Runbook 类型更改为 PowerShell。 有关更多详细信息, 请参阅[Runbook 类型](https://docs.microsoft.com/azure/automation/automation-runbook-types#powershell-runbooks)。

### <a name="using-az-modules"></a>使用 Az 模块

不支持在同一自动化帐户中同时使用 Az 模块和 AzureRM 模块。 有关更多详细信息, 请参阅[runbook 中的 Az 模块](https://docs.microsoft.com/azure/automation/az-modules)。

### <a name="using-self-signed-certificates"></a>使用自签名证书

若要使用自签名证书, 必须遵循[创建新证书](https://docs.microsoft.com/azure/automation/shared-resources/certificates#creating-a-new-certificate)中的指南。

## <a name="recommended-documents"></a>建议的文档

* [在 Azure 自动化中启动 Runbook](https://docs.microsoft.com/azure/automation/automation-starting-a-runbook)
* [在 Azure 自动化中执行 Runbook](https://docs.microsoft.com/azure/automation/automation-runbook-execution)

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过 [Azure 论坛](https://azure.microsoft.com/support/forums/)获取 Azure 专家的解答
* 与 [@AzureSupport](https://twitter.com/azuresupport)（Microsoft Azure 官方帐户）联系，它可以将 Azure 社区引导至适当的资源来改进客户体验：提供解答、支持和专业化服务。
* 如需更多帮助，可以提交 Azure 支持事件。 请转到 [Azure 支持站点](https://azure.microsoft.com/support/options/)并选择 **获取支持**。
