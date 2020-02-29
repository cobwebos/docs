---
title: Azure 自动化 Runbook 错误故障排除
description: 了解如何通过 Azure 自动化 runbook 排查和解决可能遇到的问题。
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/24/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: b7d876c7f865b8368451ea1b6cc96ade89a59aa8
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/29/2020
ms.locfileid: "78190953"
---
# <a name="troubleshoot-errors-with-runbooks"></a>Runbook 错误故障排除

如果在 Azure Automation 中执行 runbook 时出现错误，则可以使用以下步骤来帮助诊断问题。

1. **确保你的 runbook 脚本在本地计算机上成功执行：** 参阅[PowerShell 文档](/powershell/scripting/overview)或[Python 文档](https://docs.python.org/3/)，了解语言参考和学习模块。

   在本地执行脚本可以发现和解决常见错误，如：

   - **缺少模块**
   - **语法错误**
   - **逻辑错误**

2. **调查**特定消息的 runbook[错误流](https://docs.microsoft.com/azure/automation/automation-runbook-output-and-messages#runbook-output)，并将其与以下错误进行比较。

3. **确保节点和自动化工作区具有所需的模块：** 如果你的 runbook 导入任何模块，请使用[导入模块](../shared-resources/modules.md#import-modules)中列出的步骤，确保它们可用于你的自动化帐户。 按照[Azure 自动化中的更新 azure 模块中](..//automation-update-azure-modules.md)的说明，将模块更新到最新版本。 有关疑难解答的详细信息，请参阅[排查模块问题](shared-resources.md#modules)。

如果 Runbook 已挂起或意外失败：

* [检查作业状态](https://docs.microsoft.com/azure/automation/automation-runbook-execution#job-statuses)定义 runbook 状态和某些可能的原因。
* [将其他输出添加](https://docs.microsoft.com/azure/automation/automation-runbook-output-and-messages#message-streams)到 runbook，以标识在挂起 runbook 之前会发生的情况。
* 处理作业引发的[任何异常](https://docs.microsoft.com/azure/automation/automation-runbook-execution#handling-exceptions)。

## <a name="login-azurerm"></a>方案：运行登录-Add-azurermaccount 以登录

### <a name="issue"></a>问题

执行 runbook 时，会收到以下错误：

```error
Run Login-AzureRMAccount to login.
```

### <a name="cause"></a>原因

如果你使用的不是运行方式帐户或运行方式帐户已过期，则会出现此错误。 请参阅[管理 Azure 自动化运行方式帐户](https://docs.microsoft.com/azure/automation/manage-runas-account)。

此错误有两个主要原因：

* 不同版本的 AzureRM 模块。
* 你正在尝试访问单独订阅中的资源。

### <a name="resolution"></a>解决方法

如果在更新一个 AzureRM 模块后收到此错误，则应将所有 AzureRM 模块更新为同一版本。

如果要尝试访问其他订阅中的资源，可以执行以下步骤来配置权限。

1. 中转到自动化运行方式帐户，并复制应用程序 ID 和指纹。
  ![复制应用程序 ID 和指纹](../media/troubleshoot-runbooks/collect-app-id.png)
1. 访问不承载自动化帐户的订阅的访问控制，并添加新的角色分配。
  ![访问控制](../media/troubleshoot-runbooks/access-control.png)
1. 添加在上一步中收集的应用程序 ID。 选择 "参与者权限"。
   ![添加角色分配](../media/troubleshoot-runbooks/add-role-assignment.png)
1. 复制订阅的名称以进行下一步。
1. 你现在可以使用以下 runbook 代码来测试从自动化帐户到其他订阅的权限。

    将 "\<CertificateThumbprint\>" 替换为在步骤 #1 中复制的值以及在步骤\>中复制的 "\<SubscriptionName #4" 值。

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

## <a name="unable-to-find-subscription"></a>场景：无法找到 Azure 订阅

### <a name="issue"></a>问题

使用 `Select-AzureSubscription` 或 `Select-AzureRmSubscription` cmdlet 时收到以下错误：

```error
The subscription named <subscription name> cannot be found.
```

### <a name="error"></a>错误

以下情况下可能发生此错误：

* 订阅名称无效

* 尝试获取订阅详细信息的 Azure Active Directory 用户未配置为订阅的管理员。

### <a name="resolution"></a>解决方法

执行以下步骤以确定是否已正确向 Azure 进行身份验证并有权访问尝试选择的订阅：

1. 在 Azure 自动化之外测试脚本，以确保它独立运行。
2. 请确保在运行 `Add-AzureAccount` cmdlet 之前运行 `Select-AzureSubscription` cmdlet。
3. 将 `Disable-AzureRmContextAutosave –Scope Process` 添加到 runbook 的开头。 此 cmdlet 可以确保任何凭据都仅适用于当前 runbook 的执行。
4. 如果仍然看到此错误消息，可通过在 **cmdlet 后添加**AzureRmContext`Add-AzureAccount` 参数来修改代码，然后执行代码。

   ```powershell
   Disable-AzureRmContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $context = Get-AzureRmContext

   Get-AzureRmVM -ResourceGroupName myResourceGroup -AzureRmContext $context
    ```

## <a name="auth-failed-mfa"></a>场景：无法向 Azure 进行身份验证，因为已启用多重身份验证

### <a name="issue"></a>问题

使用 Azure 用户名和密码向 Azure 进行身份验证时收到以下错误：

```error
Add-AzureAccount: AADSTS50079: Strong authentication enrollment (proof-up) is required
```

### <a name="cause"></a>原因

如果对 Azure 帐户设置了多重身份验证，则不能使用 Azure Active Directory 用户向 Azure 进行身份验证。 而只能使用证书或服务主体向 Azure 进行身份验证。

### <a name="resolution"></a>解决方法

若要将证书用于 Azure 经典部署模型 cmdlet，请参阅[创建和添加证书以管理 azure 服务](https://blogs.technet.com/b/orchestrator/archive/2014/04/11/managing-azure-services-with-the-microsoft-azure-automation-preview-service.aspx)。 若要将服务主体用于 Azure 资源管理器 cmdlet，请参阅[使用 Azure 门户创建服务主体](../../active-directory/develop/howto-create-service-principal-portal.md)，并使用[azure 资源管理器对服务主体进行身份验证](../../active-directory/develop/howto-authenticate-service-principal-powershell.md)。

## <a name="get-serializationsettings"></a>方案：在作业流中看到关于 get_SerializationSettings 方法的错误

### <a name="issue"></a>问题

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

### <a name="cause"></a>原因

此错误是因为在同一 runbook 中同时使用 AzureRM 和 Az cmdlet 而导致的。 在导入 `Az` 之前导入 `AzureRM` 会出现此错误。

### <a name="resolution"></a>解决方法

不能在同一 runbook 中导入和使用 Az 和 AzureRM cmdlet。 若要了解有关 Azure 自动化中 Az cmdlet 的详细信息，请参阅[Azure 自动化中的 az module 支持](../az-modules.md)。

## <a name="task-was-cancelled"></a>场景：Runbook 失败且出现错误：取消了一个任务

### <a name="issue"></a>问题

Runbook 失败，出现类似于以下示例的错误：

```error
Exception: A task was canceled.
```

### <a name="cause"></a>原因

使用过时的 Azure 模块会导致此错误。

### <a name="resolution"></a>解决方法

可以通过将 Azure 模块更新到最新版本来解决此错误。

在自动化帐户中，单击 "**模块**"，然后单击 "**更新 Azure 模块**"。 更新需要花费大约 15 分钟，完成后，重新运行失败的 Runbook。 若要了解有关更新模块的详细信息，请参阅[在 Azure 自动化中更新 Azure 模块](../automation-update-azure-modules.md)。

## <a name="runbook-auth-failure"></a>方案： Runbook 在处理多个订阅时失败

### <a name="issue"></a>问题

执行 runbook 时，runbook 无法管理 Azure 资源。

### <a name="cause"></a>原因

Runbook 在运行时没有使用正确的上下文。

### <a name="resolution"></a>解决方法

如果使用多个订阅，则调用 Runbook 时可能会丢失订阅上下文。 若要确保将订阅上下文传递给 Runbook，请将 `AzureRmContext` 参数添加到 cmdlet 并将上下文传递给它。 还建议将 `Disable-AzureRmContextAutosave` cmdlet 与 **Process** 范围配合使用来确保你使用的凭据仅用于当前 runbook。

```azurepowershell-interactive
# Ensures that any credentials apply only to the execution of this runbook
Disable-AzureRmContextAutosave –Scope Process

# Connect to Azure with Run As account
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

有关详细信息，请参阅[使用多个订阅](../automation-runbook-execution.md#working-with-multiple-subscriptions)。

## <a name="not-recognized-as-cmdlet"></a>方案：字词未被识别为 cmdlet、函数、脚本的名称

### <a name="issue"></a>问题

Runbook 失败，出现类似于以下示例的错误：

```error
The term 'Connect-AzureRmAccount' is not recognized as the name of a cmdlet, function, script file, or operable program.  Check the spelling of the name, or if the path was included verify that the path is correct and try again.
```

### <a name="cause"></a>原因

由于以下原因，可能会发生此错误：

* 包含该 cmdlet 的模块不会导入到自动化帐户中。
* 包含 cmdlet 的模块已导入，但已过时。

### <a name="resolution"></a>解决方法

可通过完成以下任务之一来解决此错误：

如果模块是 Azure 模块，请参阅[如何更新 Azure 自动化中的 Azure PowerShell 模块](../automation-update-azure-modules.md)，了解如何更新自动化帐户中的模块。

如果它是一个单独的模块，请确保在自动化帐户中导入了模块。

## <a name="job-attempted-3-times"></a>方案： runbook 作业启动已尝试三次，但每次都无法启动

### <a name="issue"></a>问题

Runbook 失败，出现以下错误。

```error
The job was tried three times but it failed
```

### <a name="cause"></a>原因

发生此错误的原因是以下问题之一。

* 内存限制。 如果作业使用超过 400 MB 的内存，作业可能会失败。 在[自动化服务限制](../../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)中可以找到已记录的分配给沙盒的内存限制。 

* 网络套接字。 Azure 沙箱限制为1000并发网络套接字。 请参阅[自动化服务限制](../../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)。

* 模块不兼容。 模块依赖项可能不正确。 在这种情况下，runbook 通常会返回 "找不到命令" 或 "无法绑定参数" 消息。

* 没有用于沙盒 Active Directory 的身份验证。 Runbook 尝试调用在 Azure 沙盒中运行的可执行文件或子进程。 不支持使用 Azure Active Directory 身份验证库（ADAL）将 runbook 配置为使用 Azure AD 进行身份验证。

* 异常数据太多。 Runbook 尝试将过多的异常数据写入输出流。

### <a name="resolution"></a>解决方法

* 内存限制，网络套接字。 在内存限制内工作的建议方法是在多个 runbook 之间拆分工作负荷，在内存中处理较少的数据，避免从 runbook 写入不必要的输出，并考虑将多少个检查点写入 PowerShell 工作流runbook. 使用 clear 方法（如 `$myVar.clear`）清除变量并使用 `[GC]::Collect` 立即运行垃圾回收。 这将减少运行时期间 runbook 的内存占用情况。

* 模块不兼容。 按照[如何更新 Azure 自动化中的 Azure PowerShell 模块](../automation-update-azure-modules.md)中的步骤更新 azure 模块。

* 没有通过 ADAL for 沙盒进行身份验证。 当使用 runbook 对 Azure AD 进行身份验证时，请确保 Azure AD 模块在你的自动化帐户中可用。 请确保授予运行方式帐户执行 runbook 自动执行的任务所需的权限。

  如果你的 runbook 无法调用在 Azure 沙盒中运行的可执行文件或子进程，请在[混合 Runbook 辅助角色](../automation-hrw-run-runbooks.md)上使用 runbook。 混合辅助角色不受 Azure 沙箱具有的内存和网络限制的限制。

* 异常数据太多。 作业输出流的限制为1MB。 确保你的 runbook 在 try/catch 块中包含对可执行文件或子进程的调用。 如果操作引发异常，请使代码将异常消息写入自动化变量中。 此方法可防止将消息写入作业输出流中。

## <a name="sign-in-failed"></a>方案：登录 Azure 帐户失败

### <a name="issue"></a>问题

使用 `Add-AzureAccount` 或 `Connect-AzureRmAccount` cmdlet 时，会收到以下错误之一：

```error
Unknown_user_type: Unknown User Type
```

```error
No certificate was found in the certificate store with thumbprint
```

### <a name="cause"></a>原因

如果凭据资产名称无效，将发生此错误。 如果用于设置自动化凭据资产的用户名和密码无效，也可能会出现此错误。

### <a name="resolution"></a>解决方法

要确定具体错误，请执行以下步骤：

1. 请确保没有包含任何特殊字符。 这些字符包括用于连接 Azure 的自动化凭据资产名称中的  **字符\@** 。
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
       #Logging in to Azure...
       $connectionResult = Connect-AzureRmAccount `
                              -ServicePrincipal `
                              -TenantId $servicePrincipalConnection.TenantId `
                              -ApplicationId $servicePrincipalConnection.ApplicationId `
                              -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }
   ```

## <a name="child-runbook-object"></a>方案：对象引用未设置为对象的实例

### <a name="issue"></a>问题

当使用 `-Wait` 开关调用子 runbook 并且输出流包含对象时，会收到以下错误：

```error
Object reference not set to an instance of an object
```

### <a name="cause"></a>原因

存在一个已知问题，即 Start-azurermautomationrunbook 在包含对象的情况下不会正确处理输出流。

### <a name="resolution"></a>解决方法

若要解决此问题，建议你实现轮询逻辑，并使用[get-azurermautomationjoboutput](/powershell/module/azurerm.automation/get-azurermautomationjoboutput) cmdlet 来检索输出。 下面的示例中定义了此逻辑的示例。

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

## <a name="fails-deserialized-object"></a>场景：Runbook 因反序列化的对象而失败

### <a name="issue"></a>问题

Runbook 失败并显示错误：

```error
Cannot bind parameter <ParameterName>.

Cannot convert the <ParameterType> value of type Deserialized <ParameterType> to type <ParameterType>.
```

### <a name="cause"></a>原因

如果 runbook 为 PowerShell 工作流，它会将复杂对象以反序列化格式进行存储，以便在工作流暂停的情况下保留 runbook 状态。

### <a name="resolution"></a>解决方法

下述三种解决方案中的任何一种都可以解决此问题：

* 如果要将复杂对象从一个 cmdlet 传送到另一个 cmdlet，则可将这两个 cmdlet 包装在 InlineScript 中。
* 传递复杂对象中你所需要的名称或值，不必传递整个对象。
* 使用 PowerShell Runbook，而不使用 PowerShell 工作流 Runbook。

## <a name="quota-exceeded"></a>方案： Runbook 作业失败，因为超过了分配的配额

### <a name="issue"></a>问题

Runbook 作业失败并显示错误：

```error
The quota for the monthly total job run time has been reached for this subscription
```

### <a name="cause"></a>原因

作业执行时间超过帐户的 500 分钟免费配额时，就会出现此错误。 此配额适用于所有类型的作业执行任务。 其中一些任务可能是测试作业、从门户启动作业、使用 Webhook 执行作业，以及通过 Azure 门户或数据中心计划要执行的作业。 若要详细了解自动化的定价，请参阅[自动化定价](https://azure.microsoft.com/pricing/details/automation/)。

### <a name="resolution"></a>解决方法

如果想要每月使用 500 分钟以上的处理时间，则需将订阅从免费层改为基本层。 可以通过下述步骤升级到基本层：

1. 登录到 Azure 订阅。
2. 选择要升级的自动化帐户。
3. 依次单击 "**设置**"、"**定价**"。
4. 单击页面底部的“启用”，以将帐户升级到“基本”层。

## <a name="cmdlet-not-recognized"></a>场景：在执行 Runbook 时无法识别 cmdlet

### <a name="issue"></a>问题

Runbook 作业失败并显示错误：

```error
<cmdlet name>: The term <cmdlet name> is not recognized as the name of a cmdlet, function, script file, or operable program.
```

### <a name="cause"></a>原因

当 PowerShell 引擎找不到要在 runbook 中使用的 cmdlet 时，则会导致此错误。 此错误可能是因为，帐户中缺少包含该 cmdlet 的模块、与 Runbook 名称冲突，或者该 cmdlet 也存在于其他模块中，而自动化无法解析该名称。

### <a name="resolution"></a>解决方法

下述解决方案中的任何一种都可以解决此问题：

* 检查输入的 cmdlet 名称是否正确。
* 确保 cmdlet 存在于自动化帐户中，且没有冲突。 要验证 cmdlet 是否存在，请在编辑模式下打开 Runbook，并搜索希望在库中找到的 cmdlet，或者运行 `Get-Command <CommandName>`。 验证该 cmdlet 可供帐户使用且与其他 cmdlet 或 runbook 不存在名称冲突以后，可将其添加到画布上，并确保使用的是 runbook 中的有效参数集。
* 如果存在名称冲突且 cmdlet 可在两个不同的模块中使用，则可使用 cmdlet 的完全限定名称来解决此问题。 例如，可以使用 **ModuleName\CmdletName**。
* 如果是在本地执行混合辅助角色组中的 runbook，则请确保模块和 cmdlet 已安装在托管混合辅助角色的计算机上。

## <a name="long-running-runbook"></a>方案：长时间运行的 runbook 无法完成

### <a name="issue"></a>问题

运行 3 小时后，runbook 将显示处于“已停止”状态。 可能还会收到错误：

```error
The job was evicted and subsequently reached a Stopped state. The job cannot continue running
```

此行为是在 Azure 沙箱中设计的，因为在 Azure 自动化中对进程进行[公平共享](../automation-runbook-execution.md#fair-share)监视。 如果执行时间超过三个小时，公平份额会自动停止 runbook。 超过公平共享时间限制的 runbook 状态因 runbook 类型而异。 PowerShell 和 Python runbook 设置为“已停止”状态。 PowerShell 工作流 runbook 设置为“失败”。

### <a name="cause"></a>原因

Runbook 在 Azure 沙盒中的公平共享允许的3小时限制内运行。

### <a name="resolution"></a>解决方法

建议的解决方案是在[混合 Runbook 辅助角色](../automation-hrw-run-runbooks.md)上运行 runbook。

混合辅助角色不受 Azure 沙箱使用的3小时公平共享 runbook 限制的限制。 应开发在混合 Runbook 辅助角色上运行的 runbook，以便在出现意外的本地基础结构问题时支持重启行为。

另一种选择是通过创建[子 runbook](../automation-child-runbooks.md) 来优化 runbook。 如果 runbook 在多个资源上遍历同一函数，例如在多个数据库上执行某个数据库操作，则可将该函数移到子 runbook。 各个子 runbook 是在单独的进程中并行执行的。 此行为降低了完成父 runbook 所需的时间总量。

启用子 runbook 方案的 PowerShell cmdlet 是：

[Start-AzureRMAutomationRunbook](/powershell/module/AzureRM.Automation/Start-AzureRmAutomationRunbook) - 此 cmdlet 用于启动 runbook 并向其传递参数

[Get-AzureRmAutomationJob](/powershell/module/azurerm.automation/get-azurermautomationjob) - 在子 runbook 完成后，如果需要执行操作，可使用此 cmdlet 检查每个子 runbook 的作业状态。

## <a name="expired webhook"></a>情况：状态：调用 webhook 时出现错误的请求（400）

### <a name="issue"></a>问题

在尝试调用 Azure 自动化 Runbook 的 Webhook 时，收到以下错误：

```error
400 Bad Request : This webhook has expired or is disabled
```

### <a name="cause"></a>原因

尝试调用的 Webhook 已禁用，或者已过期。

### <a name="resolution"></a>解决方法

如果 Webhook 处于禁用状态，可以通过 Azure 门户重新启用 Webhook。 如果 Webhook 已过期，需要删除并重新创建 Webhook。 如果尚未过期，只能[续订 Webhook](../automation-webhooks.md#renew-webhook)。

## <a name="429"></a>方案：429：请求速率目前太大 。

### <a name="issue"></a>问题

在运行 `Get-AzureRmAutomationJobOutput` cmdlet 时收到以下错误消息：

```error
429: The request rate is currently too large. Please try again
```

### <a name="cause"></a>原因

从具有多个[详细流](../automation-runbook-output-and-messages.md#verbose-stream)的 Runbook 中检索作业输出时，可能发生此错误。

### <a name="resolution"></a>解决方法

可通过两种方法来解决此错误：

* 编辑 Runbook，并减少它发出的作业流数量。
* 减少运行 cmdlet 时要检索的流数量。 若要遵循此行为，可以为 `-Stream Output` cmdlet 指定 `Get-AzureRmAutomationJobOutput` 参数以仅检索输出流。 

## <a name="cannot-invoke-method"></a>方案： PowerShell 作业失败并出现错误：无法调用方法

### <a name="issue"></a>问题

在 Azure 中运行的 runbook 中启动 PowerShell 作业时，会收到以下错误消息：

```error
Exception was thrown - Cannot invoke method. Method invocation is supported only on core types in this language mode.
```

### <a name="cause"></a>原因

当你在 Azure 中运行的 runbook 中启动 PowerShell 作业时，可能会发生此错误。 发生此行为的原因是，在 Azure 沙盒中运行的 runbook 可能无法以[完整语言模式](/powershell/module/microsoft.powershell.core/about/about_language_modes)运行。

### <a name="resolution"></a>解决方法

可通过两种方法来解决此错误：

* 使用 `Start-AzureRmAutomationRunbook` 启动 runbook，而不使用 `Start-Job`。
* 如果 runbook 有此错误消息，请在混合 Runbook 辅助角色上运行它

若要了解有关此行为以及 Azure 自动化 Runbook 的其他行为的详细信息，请参阅[Runbook 行为](../automation-runbook-execution.md#runbook-behavior)。

## <a name="scenario-linux-hybrid-runbook-worker-receives-a-prompt-for-a-password-when-signing-a-runbook"></a>方案： Linux 混合 Runbook 辅助角色在对 Runbook 进行签名时接收密码提示

### <a name="issue"></a>问题

为 Linux 混合 Runbook 辅助角色运行**sudo**命令将检索意外的密码提示。

### <a name="cause"></a>原因

未在 sudoers 文件中正确配置适用于 Linux 的 Log Analytics 代理的 nxautomationuser 帐户。 混合 Runbook 辅助角色需要对帐户权限和其他数据进行适当的配置，以便能够对 Linux Runbook 辅助角色上的 runbook 进行签名。

### <a name="resolution"></a>解决方法

* 确保混合 Runbook 辅助角色在计算机上具有 GnuPG （GPG）可执行文件。

* 验证 nxautomationuser 帐户在 sudoers 文件中的配置。 请参阅[在混合 Runbook 辅助角色上运行 runbook](../automation-hrw-run-runbooks.md)

## <a name="scenario-cmdlet-failing-in-pnp-powershell-runbook-on-azure-automation"></a>方案： Cmdlet 在 Azure 自动化中的 PnP PowerShell runbook 中失败

### <a name="issue"></a>问题

当 runbook 直接将 PnP PowerShell 生成的对象写入 Azure 自动化输出时，cmdlet 输出无法流式传输到自动化。

### <a name="cause"></a>原因

此问题最常见的原因是 Azure 自动化处理调用 PnP PowerShell cmdlet 的 runbook，例如**pnplistitem**，而无需捕获返回对象。

### <a name="resolution"></a>解决方法

编辑脚本以将任何返回值分配给变量，使 cmdlet 不会尝试将整个对象写入标准输出。 脚本可以将输出流重定向到 cmdlet，如下所示。

```azurecli
  $null = add-pnplistitem
```
如果你的脚本分析了 cmdlet 输出，则该脚本必须将输出存储在一个变量中，然后操作变量，而不是只是对输出进行流式处理。

```azurecli
$SomeVariable = add-pnplistitem ....
if ($SomeVariable.someproperty -eq ....
```

## <a name="other"></a>我的问题未在上面列出

以下部分列出了其他常见错误以及支持文档，以帮助你解决问题。

### <a name="hybrid-runbook-worker-doesnt-run-jobs-or-isnt-responding"></a>混合 runbook 辅助角色不运行作业，或者没有响应

如果使用混合辅助角色而不是在 Azure 自动化中运行作业，可能需要对[混合辅助角色本身进行故障排除](https://docs.microsoft.com/azure/automation/troubleshoot/hybrid-runbook-worker)。

### <a name="runbook-fails-with-no-permission-or-some-variation"></a>Runbook 由于“无权限”或某个变动而失败

运行方式帐户可能没有与当前帐户相同的权限。 确保运行方式帐户有权访问脚本中使用的[任何资源](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)。

### <a name="runbooks-were-working-but-suddenly-stopped"></a>Runbook 在工作时突然停止

* 如果 runbook 以前执行但停止，请确保[运行方式帐户](https://docs.microsoft.com/azure/automation/manage-runas-account#cert-renewal)未过期。
* 如果使用 webhook 启动 runbook，请确保[webhook](https://docs.microsoft.com/azure/automation/automation-webhooks#renew-webhook)未过期。

### <a name="issues-passing-parameters-into-webhooks"></a>将参数传递到 webhook 的问题

有关将参数传递到 webhook 的帮助，请参阅[从 Webhook 启动 runbook](https://docs.microsoft.com/azure/automation/automation-webhooks#parameters)。

### <a name="issues-using-az-modules"></a>使用 Az 模块时出现问题

不支持在同一 Automation 帐户中使用 Az 模块和 AzureRM 模块。 有关详细信息，请参阅[runbook 中的 Az 模块](https://docs.microsoft.com/azure/automation/az-modules)了解更多详细信息。

### <a name="inconsistent-behavior-in-runbooks"></a>Runbook 中的行为不一致

按照[runbook 执行](https://docs.microsoft.com/azure/automation/automation-runbook-execution#runbook-behavior)中的指导进行操作，以避免出现并发作业问题、创建多个资源或在 runbook 中进行其他计时敏感逻辑。

### <a name="runbook-fails-with-the-error-no-permission-forbidden-403-or-some-variation"></a>Runbook 失败，并出现错误 "无权限"、"已禁止" （403）或 "某些变体"

运行方式帐户可能没有与当前帐户相同的权限。 确保运行方式帐户有权访问脚本中使用的[任何资源](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)。

### <a name="runbooks-were-working-but-suddenly-stopped"></a>Runbook 在工作时突然停止

* 如果 runbook 以前执行但停止，请确保运行方式帐户未过期。 请参阅[认证续订](https://docs.microsoft.com/azure/automation/manage-runas-account#cert-renewal)。
* 如果使用 webhook 启动 runbook，请确保 webhook[未过期](https://docs.microsoft.com/azure/automation/automation-webhooks#renew-webhook)。

### <a name="passing-parameters-into-webhooks"></a>将参数传递到 webhook

有关将参数传递到 webhook 的帮助，请参阅[从 Webhook 启动 runbook](https://docs.microsoft.com/azure/automation/automation-webhooks#parameters)。

### <a name="using-az-modules"></a>使用 Az 模块

不支持在同一 Automation 帐户中使用 Az 模块和 AzureRM 模块。 请参阅[runbook 中的 Az 模块](https://docs.microsoft.com/azure/automation/az-modules)。

### <a name="using-self-signed-certificates"></a>使用自签名证书

若要使用自签名证书，请参阅[创建新证书](https://docs.microsoft.com/azure/automation/shared-resources/certificates#creating-a-new-certificate)。

## <a name="recommended-documents"></a>建议的文档

* [在 Azure 自动化中启动 Runbook](https://docs.microsoft.com/azure/automation/automation-starting-a-runbook)
* [Azure Automation 中的 Runbook 执行](https://docs.microsoft.com/azure/automation/automation-runbook-execution)

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过 [Azure 论坛](https://azure.microsoft.com/support/forums/)获取 Azure 专家的解答
* 与 [@AzureSupport](https://twitter.com/azuresupport)（Microsoft Azure 官方帐户）联系，它可以将 Azure 社区引导至适当的资源来改进客户体验：提供解答、支持和专业化服务。
* 如需更多帮助，可以提交 Azure 支持事件。 请转到 [Azure 支持站点](https://azure.microsoft.com/support/options/)并选择 **获取支持**。
