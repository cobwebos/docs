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
ms.openlocfilehash: 8fd2f808169a0615e1ad5ca338823e3a8e3212ff
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79297843"
---
# <a name="troubleshoot-errors-with-runbooks"></a>Runbook 错误故障排除

如果在 Azure Automation 中执行 runbook 时出现错误，则可以使用以下步骤来帮助诊断问题。

1. **确保你的 runbook 脚本在本地计算机上成功执行。** 

    参阅[PowerShell 文档](/powershell/scripting/overview)或[Python 文档](https://docs.python.org/3/)，了解语言参考和学习模块。 在本地执行脚本可以发现和解决常见错误，如：

      * 缺少模块
      * 语法错误
      * 逻辑错误

2. **调查 runbook[错误流](https://docs.microsoft.com/azure/automation/automation-runbook-output-and-messages#runbook-output)。**

    查看这些流中的特定消息，并将其与本文中所述的错误进行比较。

3. **确保节点和自动化工作区具有所需的模块。** 

    如果你的 runbook 导入任何模块，请使用[导入模块](../shared-resources/modules.md#import-modules)中列出的步骤验证这些模块是否可用于你的自动化帐户。 按照[Azure 自动化中的更新 azure 模块中](..//automation-update-azure-modules.md)的说明，将模块更新到最新版本。 有关疑难解答的详细信息，请参阅[排查模块问题](shared-resources.md#modules)。

4. **如果 runbook 已挂起或发生意外失败，则执行此操作。**

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

* AzureRM 或 Az 模块有不同的版本。
* 你正在尝试访问单独订阅中的资源。

### <a name="resolution"></a>解决方法

如果在更新一个 AzureRM 或 Az 模块后收到此错误，则应将所有模块更新为同一版本。

如果要尝试访问其他订阅中的资源，可以执行以下步骤来配置权限。

1. 中转到自动化运行方式帐户，并复制应用程序 ID 和指纹。
  ![复制应用程序 ID 和指纹](../media/troubleshoot-runbooks/collect-app-id.png)
1. 访问不承载自动化帐户的订阅的访问控制，并添加新的角色分配。
  ![访问控制](../media/troubleshoot-runbooks/access-control.png)
1. 添加先前收集的应用程序 ID。 选择 "参与者权限"。
   ![添加角色分配](../media/troubleshoot-runbooks/add-role-assignment.png)
1. 复制订阅的名称。
1. 你现在可以使用以下 runbook 代码来测试从自动化帐户到其他订阅的权限。 将 `"\<CertificateThumbprint\>"` 替换为在步骤1中复制的值。 将 `"\<SubscriptionName\>"` 替换为在步骤4中复制的值。

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

使用 `Select-AzureSubscription` 或 `Select-AzureRmSubscription` cmdlet 时，会收到以下错误：

```error
The subscription named <subscription name> cannot be found.
```

### <a name="error"></a>错误

如果出现以下情况，则可能发生此错误：

* 订阅名称无效。
* 尝试获取订阅详细信息的 Azure Active Directory 用户未配置为订阅的管理员。

### <a name="resolution"></a>解决方法

按照以下步骤进行操作，确定你是否已对 Azure 进行了身份验证，以及是否有权访问你要尝试选择的订阅。

1. 若要确保你的脚本独立工作，请在 Azure 自动化之外进行测试。
2. 在运行 `Select-AzureSubscription` cmdlet 之前，请确保脚本运行 `Add-AzureAccount` cmdlet。
3. 将 `Disable-AzureRmContextAutosave –Scope Process` 添加到 runbook 的开头。 此 cmdlet 调用可确保任何凭据仅适用于当前 runbook 的执行。
4. 如果仍看到此错误消息，请通过添加 `Add-AzureAccount` cmdlet 的 `AzureRmContext` 参数来修改代码，然后执行代码。

   ```powershell
   Disable-AzureRmContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

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

如果对 Azure 帐户设置了多重身份验证，则不能使用 Azure Active Directory 用户向 Azure 进行身份验证。 相反，你需要使用证书或服务主体进行身份验证。

### <a name="resolution"></a>解决方法

若要将证书用于 Azure 经典部署模型 cmdlet，请参阅[创建和添加证书以管理 azure 服务](https://blogs.technet.com/b/orchestrator/archive/2014/04/11/managing-azure-services-with-the-microsoft-azure-automation-preview-service.aspx)。 若要将服务主体用于 Azure 资源管理器 cmdlet，请参阅[使用 Azure 门户创建服务主体](../../active-directory/develop/howto-create-service-principal-portal.md)和使用[azure 资源管理器对服务主体进行身份验证](../../active-directory/develop/howto-authenticate-service-principal-powershell.md)。

## <a name="get-serializationsettings"></a>方案：在作业流中看到关于 get_SerializationSettings 方法的错误

### <a name="issue"></a>问题

Runbook 的作业流中会出现以下错误：

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

此错误是使用 runbook 中的 AzureRM 和 Az module cmdlet 导致的。 如果在导入 AzureRM 模块之前导入 Az 模块，则会发生这种情况。

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

1. 在自动化帐户中，单击 "**模块**"，然后单击 "**更新 Azure 模块**"。 
2. 此更新大约需15分钟。 完成后，重新运行失败的 runbook。

若要了解有关更新模块的详细信息，请参阅[在 Azure 自动化中更新 Azure 模块](../automation-update-azure-modules.md)。

## <a name="runbook-auth-failure"></a>方案： Runbook 在处理多个订阅时失败

### <a name="issue"></a>问题

执行 runbook 时，runbook 无法管理 Azure 资源。

### <a name="cause"></a>原因

Runbook 在运行时没有使用正确的上下文。

### <a name="resolution"></a>解决方法

当 runbook 调用多个 runbook 时，订阅上下文可能会丢失。 若要确保将订阅上下文传递到 runbook，请将客户端 runbook 向 `AzureRmContext` 参数中的 `Start-AzureRmAutomationRunbook` cmdlet 传递上下文。 使用 `Disable-AzureRmContextAutosave` cmdlet，并将 `Scope` 参数设置为 `Process`，以确保指定的凭据仅用于当前 runbook。 有关详细信息，请参阅[使用多个订阅](../automation-runbook-execution.md#working-with-multiple-subscriptions)。

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

## <a name="not-recognized-as-cmdlet"></a>方案：字词未被识别为 cmdlet、函数、脚本的名称

### <a name="issue"></a>问题

Runbook 失败，出现类似于以下示例的错误：

```error
The term 'Connect-AzureRmAccount' is not recognized as the name of a cmdlet, function, script file, or operable program. Check the spelling of the name, or if the path was included verify that the path is correct and try again.
```

### <a name="cause"></a>原因

发生此错误的原因如下：

* 包含该 cmdlet 的模块不会导入到自动化帐户中。
* 包含 cmdlet 的模块已导入，但已过时。

### <a name="resolution"></a>解决方法

若要解决此错误，请执行以下任务之一。 

* 有关 Azure 模块，请参阅[如何更新 Azure 自动化中的 Azure PowerShell 模块](../automation-update-azure-modules.md)，了解如何更新自动化帐户中的模块。

* 对于非 Azure 模块，请确保导入到自动化帐户中的模块。

## <a name="job-attempted-3-times"></a>方案： runbook 作业启动已尝试三次，但每次都无法启动

### <a name="issue"></a>问题

Runbook 失败，并出现以下错误：

```error
The job was tried three times but it failed
```

### <a name="cause"></a>原因

发生此错误的原因是以下问题之一：

* 内存限制。 如果作业使用超过 400 MB 的内存，作业可能会失败。 在[自动化服务限制](../../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)中可以找到已记录的分配给沙盒的内存限制。 

* 网络套接字。 Azure 沙箱限制为1000并发网络套接字。 请参阅[自动化服务限制](../../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)。

* 模块不兼容。 模块依赖项可能不正确。 在这种情况下，runbook 通常会返回 `Command not found` 或 `Cannot bind parameter` 消息。

* 没有用于沙盒 Active Directory 的身份验证。 Runbook 尝试调用在 Azure 沙盒中运行的可执行文件或子进程。 不支持使用 Azure Active Directory 身份验证库（ADAL）将 runbook 配置为使用 Azure AD 进行身份验证。

* 异常数据太多。 Runbook 尝试将过多的异常数据写入输出流。

### <a name="resolution"></a>解决方法

* 内存限制，网络套接字。 在内存限制内工作的建议方法是在多个 runbook 之间拆分工作负荷，在内存中处理较少的数据，避免从 runbook 写入不必要的输出，并考虑将多少个检查点写入 PowerShell 工作流runbook. 使用 clear 方法（如 `$myVar.clear`）清除变量并使用 `[GC]::Collect` 立即运行垃圾回收。 这将减少运行时期间 runbook 的内存占用情况。

* 模块不兼容。 按照[如何更新 Azure 自动化中的 Azure PowerShell 模块](../automation-update-azure-modules.md)中的步骤更新 azure 模块。

* 没有用于沙盒 Active Directory 的身份验证。 当使用 runbook 对 Azure AD 进行身份验证时，请确保 Azure AD 模块在你的自动化帐户中可用。 请确保授予运行方式帐户执行 runbook 自动执行的任务所需的权限。

  如果你的 runbook 无法调用在 Azure 沙盒中运行的可执行文件或子进程，请在[混合 Runbook 辅助角色](../automation-hrw-run-runbooks.md)上使用 runbook。 混合辅助角色不受 Azure 沙箱具有的内存和网络限制的限制。

* 异常数据太多。 作业输出流的限制为1MB。 确保 runbook 使用 `try` 和 `catch` 块将调用封装到可执行文件或子进程。 如果操作引发异常，请使代码将异常消息写入自动化变量中。 此方法可防止将消息写入作业输出流中。

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

如果凭据资产名称无效，则会出现这些错误。 如果用于设置自动化凭据资产的用户名和密码无效，则也可能出现这种情况。

### <a name="resolution"></a>解决方法

要确定具体错误，请执行以下步骤：

1. 请确保没有任何特殊字符。 这些字符包括用于连接到 Azure 的自动化凭据资产名称中的 `\@` 字符。
2. 查看是否可以在本地 PowerShell ISE 编辑器中使用存储在 Azure 自动化凭据中的用户名和密码。 在 PowerShell ISE 中运行以下 cmdlet。

   ```powershell
   $Cred = Get-Credential
   #Using Azure Service Management
   Add-AzureAccount –Credential $Cred
   #Using Azure Resource Manager
   Connect-AzureRmAccount –Credential $Cred
   ```

3. 如果身份验证在本地失败，则尚未正确设置 Azure Active Directory 凭据。 请参阅使用 Azure Active Directory 博客文章对[Azure 进行身份验证](https://azure.microsoft.com/blog/azure-automation-authenticating-to-azure-using-azure-active-directory/)，以便正确设置 Azure Active Directory 帐户。

4. 如果错误看起来是暂时性的，请尝试将重试逻辑添加到身份验证例程，使身份验证更可靠。

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

使用 `Wait` 参数调用子 runbook 时，会收到以下错误，并且输出流包含一个对象：

```error
Object reference not set to an instance of an object
```

### <a name="cause"></a>原因

如果流包含对象，`Start-AzureRmAutomationRunbook` 不会正确处理输出流。

### <a name="resolution"></a>解决方法

建议实现轮询逻辑，并使用[get-azurermautomationjoboutput](/powershell/module/azurerm.automation/get-azurermautomationjoboutput) cmdlet 来检索输出。 下面定义了此逻辑的示例。

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

使用以下任一解决方案来解决此问题。

* 如果要将复杂对象从一个 cmdlet 传送到另一个 cmdlet，请在 `InlineScript` 活动中包装这些 cmdlet。
* 传递复杂对象中你所需要的名称或值，不必传递整个对象。
* 使用 PowerShell Runbook，而不使用 PowerShell 工作流 Runbook。

## <a name="quota-exceeded"></a>方案： Runbook 作业失败，因为超过了分配的配额

### <a name="issue"></a>问题

Runbook 作业失败并显示错误：

```error
The quota for the monthly total job run time has been reached for this subscription
```

### <a name="cause"></a>原因

作业执行时间超过帐户的 500 分钟免费配额时，就会出现此错误。 此配额适用于所有类型的作业执行任务。 其中一些任务是测试作业、从门户启动作业、使用 webhook 执行作业，或使用 Azure 门户或数据中心计划要执行的作业。 若要详细了解自动化的定价，请参阅[自动化定价](https://azure.microsoft.com/pricing/details/automation/)。

### <a name="resolution"></a>解决方法

如果希望每月使用超过500分钟的处理，请将免费级别的订阅更改为 "基本" 层。

1. 登录到 Azure 订阅。
2. 选择要升级的自动化帐户。
3. 依次单击 "**设置**"、"**定价**"。
4. 单击页面底部的 "**启用**"，将你的帐户升级到 "基本" 层。

## <a name="cmdlet-not-recognized"></a>场景：在执行 Runbook 时无法识别 cmdlet

### <a name="issue"></a>问题

Runbook 作业失败并显示错误：

```error
<cmdlet name>: The term <cmdlet name> is not recognized as the name of a cmdlet, function, script file, or operable program.
```

### <a name="cause"></a>原因

当 PowerShell 引擎找不到要在 runbook 中使用的 cmdlet 时，则会导致此错误。 帐户中可能缺少包含该 cmdlet 的模块、与 runbook 名称存在名称冲突，或者该 cmdlet 也存在于其他模块中，而自动化无法解析该名称。

### <a name="resolution"></a>解决方法

使用以下任一解决方案来解决该问题。

* 请确保已正确输入 cmdlet 名称。
* 确保该 cmdlet 存在于自动化帐户中，并且不存在冲突。 若要验证 cmdlet 是否存在，请在编辑模式下打开 runbook，搜索要在库中查找的 cmdlet，或者运行 `Get-Command <CommandName>`。 验证该 cmdlet 可供帐户使用且与其他 cmdlet 或 runbook 不存在名称冲突时，请将该 cmdlet 添加到画布中，并确保在 runbook 中使用有效的参数集。
* 如果确实存在名称冲突且 cmdlet 可在两个不同的模块中使用，请使用 cmdlet 的完全限定名称来解决此问题。 例如，可以使用 `ModuleName\CmdletName`。
* 如果要在混合辅助角色组中本地执行 runbook，请确保在承载混合辅助角色的计算机上安装了该模块和 cmdlet。

## <a name="long-running-runbook"></a>方案：长时间运行的 runbook 无法完成

### <a name="issue"></a>问题

Runbook 在运行3小时后显示为停止状态。 还可能会收到此错误：

```error
The job was evicted and subsequently reached a Stopped state. The job cannot continue running.
```

此行为是在 Azure 沙箱中设计的，因为在 Azure 自动化中对进程进行[公平共享](../automation-runbook-execution.md#fair-share)监视。 如果某个进程的执行时间超过3小时，则公平共享会自动停止 runbook。 超过公平共享时间限制的 runbook 状态因 runbook 类型而异。 PowerShell 和 Python runbook 设置为 "已停止" 状态。 PowerShell 工作流 runbook 设置为 "失败"。

### <a name="cause"></a>原因

Runbook 在 Azure 沙盒中的公平共享允许的3小时限制内运行。

### <a name="resolution"></a>解决方法

建议的解决方案是在[混合 Runbook 辅助角色](../automation-hrw-run-runbooks.md)上运行 runbook。 混合辅助角色不受 Azure 沙箱使用的3小时公平共享 runbook 限制的限制。 应开发在混合 Runbook 辅助角色上运行的 runbook，以便在出现意外的本地基础结构问题时支持重启行为。

另一种解决方案是通过创建[子 runbook](../automation-child-runbooks.md)来优化 runbook。 如果 runbook 在多个资源上循环遍历相同的函数（例如，在多个数据库的数据库操作中），则可以将函数移动到子 runbook。 每个子 runbook 并行在单独的进程中执行。 此行为降低了完成父 runbook 所需的时间总量。

启用子 runbook 方案的 PowerShell cmdlet 是：

* [Start-azurermautomationrunbook](/powershell/module/AzureRM.Automation/Start-AzureRmAutomationRunbook)。 此 cmdlet 用于启动 Runbook 并将参数传递给该 Runbook。

* [Get-azurermautomationjob](/powershell/module/azurerm.automation/get-azurermautomationjob)。 如果在子 runbook 完成后需要执行某些操作，则此 cmdlet 允许您检查每个子 runbook 的作业状态。

## <a name="expired webhook"></a>情况：状态：调用 webhook 时出现错误的请求（400）

### <a name="issue"></a>问题

尝试调用 Azure 自动化 runbook 的 webhook 时，会收到以下错误：

```error
400 Bad Request : This webhook has expired or is disabled
```

### <a name="cause"></a>原因

尝试调用的 Webhook 已禁用，或者已过期。

### <a name="resolution"></a>解决方法

如果 Webhook 处于禁用状态，可以通过 Azure 门户重新启用 Webhook。 如果 webhook 过期，则必须删除然后重新创建它。 如果尚未过期，只能[续订 Webhook](../automation-webhooks.md#renew-webhook)。

## <a name="429"></a>方案：429：请求速率目前太大 。

### <a name="issue"></a>问题

在运行 `Get-AzureRmAutomationJobOutput` cmdlet 时收到以下错误消息：

```error
429: The request rate is currently too large. Please try again
```

### <a name="cause"></a>原因

从具有多个[详细流](../automation-runbook-output-and-messages.md#verbose-stream)的 runbook 检索作业输出时可能会发生此错误。

### <a name="resolution"></a>解决方法

若要解决此错误，请执行以下操作之一。

* 编辑 Runbook，并减少它发出的作业流数量。

* 减少运行 cmdlet 时要检索的流数量。 为此，可以将 `Get-AzureRmAutomationJobOutput` cmdlet 的 `Stream` 参数的值设置为仅检索输出流。 

## <a name="cannot-invoke-method"></a>方案： PowerShell 作业失败并出现错误：无法调用方法

### <a name="issue"></a>问题

在 Azure 中运行的 runbook 中启动 PowerShell 作业时，会收到以下错误消息：

```error
Exception was thrown - Cannot invoke method. Method invocation is supported only on core types in this language mode.
```

### <a name="cause"></a>原因

此错误可能表示在 Azure 沙箱中执行的 runbook 无法在[完整语言模式下](/powershell/module/microsoft.powershell.core/about/about_language_modes)运行。

### <a name="resolution"></a>解决方法

可以通过两种方法来解决此错误。

* 使用 `Start-AzureRmAutomationRunbook` 启动 runbook，而不使用 `Start-Job`。
* 尝试在混合 Runbook 辅助角色上运行 runbook。

若要了解有关此行为以及 Azure 自动化 runbook 的其他行为的详细信息，请参阅[Runbook 行为](../automation-runbook-execution.md#runbook-behavior)。

## <a name="scenario-linux-hybrid-runbook-worker-receives-a-prompt-for-a-password-when-signing-a-runbook"></a>方案： Linux 混合 Runbook 辅助角色在对 Runbook 进行签名时接收密码提示

### <a name="issue"></a>问题

为 Linux 混合 Runbook 辅助角色运行 `sudo` 命令将检索意外的密码提示。

### <a name="cause"></a>原因

未在**sudoers**文件中正确配置适用于 Linux 的 Log Analytics 代理的**nxautomationuser**帐户。 混合 Runbook 辅助角色需要对帐户权限和其他数据进行适当的配置，以便能够对 Linux Runbook 辅助角色上的 runbook 进行签名。

### <a name="resolution"></a>解决方法

* 确保混合 Runbook 辅助角色在计算机上具有 GnuPG （GPG）可执行文件。

* 验证**nxautomationuser**帐户在**sudoers**文件中的配置。 请参阅[在混合 Runbook 辅助角色上运行 runbook](../automation-hrw-run-runbooks.md)

## <a name="scenario-cmdlet-failing-in-pnp-powershell-runbook-on-azure-automation"></a>方案： Cmdlet 在 Azure 自动化中的 PnP PowerShell runbook 中失败

### <a name="issue"></a>问题

当 runbook 直接将 PnP PowerShell 生成的对象写入 Azure 自动化输出时，cmdlet 输出无法流式传输到自动化。

### <a name="cause"></a>原因

此问题最常见的原因是 Azure 自动化处理调用 PnP PowerShell cmdlet 的 runbook，例如 `add-pnplistitem`，而不会捕获返回对象。

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

以下各部分列出了其他常见错误，并提供了可帮助您解决问题的支持文档。

### <a name="hybrid-runbook-worker-doesnt-run-jobs-or-isnt-responding"></a>混合 runbook 辅助角色不运行作业，或者没有响应

如果要在混合 Runbook 辅助角色（而不是 Azure 自动化）中运行作业，可能需要对[混合辅助角色本身进行故障排除](https://docs.microsoft.com/azure/automation/troubleshoot/hybrid-runbook-worker)。

### <a name="runbook-fails-with-no-permission-or-some-variation"></a>Runbook 失败，无权限或某些变体

运行方式帐户可能没有与当前帐户相同的权限。 确保运行方式帐户有权访问脚本中使用的[任何资源](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)。

### <a name="issues-passing-parameters-into-webhooks"></a>将参数传递到 webhook 的问题

有关将参数传递到 webhook 的帮助，请参阅[从 Webhook 启动 runbook](../automation-webhooks.md#parameters-used-when-the-webhook-starts-a-runbook)。

### <a name="issues-using-az-modules"></a>使用 Az 模块时出现问题

不支持在同一 Automation 帐户中使用 Az 模块和 AzureRM 模块。 有关更多详细信息，请参阅[runbook 中的 Az 模块](https://docs.microsoft.com/azure/automation/az-modules)。

### <a name="inconsistent-behavior-in-runbooks"></a>Runbook 中的行为不一致

遵循[runbook 执行](https://docs.microsoft.com/azure/automation/automation-runbook-execution#runbook-behavior)中的指导，以避免出现并发作业、多次创建的资源或 runbook 中其他计时敏感逻辑的问题。

### <a name="runbook-fails-with-the-error-no-permission-forbidden-403-or-some-variation"></a>Runbook 失败，并出现错误 "无权限"、"已禁止" （403）或 "某些变体"

运行方式帐户可能没有与当前帐户相同的权限。 确保运行方式帐户有权访问脚本中使用的[任何资源](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)。

### <a name="runbooks-were-working-but-suddenly-stopped"></a>Runbook 在工作时突然停止

* 确保运行方式帐户未过期。 请参阅[认证续订](https://docs.microsoft.com/azure/automation/manage-runas-account#cert-renewal)。
* 如果你使用[webhook](https://docs.microsoft.com/azure/automation/automation-webhooks#renew-webhook)来启动 runbook，请确保 webhook 未过期。

### <a name="passing-parameters-into-webhooks"></a>将参数传递到 webhook

有关将参数传递到 webhook 的帮助，请参阅[从 Webhook 启动 runbook](https://docs.microsoft.com/azure/automation/automation-webhooks#parameters-used-when-the-webhook-starts-a-runbook)。

### <a name="using-az-modules"></a>使用 Az 模块

不支持在同一 Automation 帐户中使用 Az 模块和 AzureRM 模块。 请参阅[runbook 中的 Az 模块](https://docs.microsoft.com/azure/automation/az-modules)。

### <a name="using-self-signed-certificates"></a>使用自签名证书

若要使用自签名证书，请参阅[创建新证书](https://docs.microsoft.com/azure/automation/shared-resources/certificates#creating-a-new-certificate)。

### <a name="access-denied-when-using-azure-sandbox-for-a-runbook"></a>使用 Azure 沙盒作为 runbook 时拒绝访问

Azure 沙箱会阻止访问所有进程外 COM 服务器。 例如，沙盒应用程序或 runbook 无法调入 Windows Management Instrumentation （WMI）或 Windows Installer 服务（msiserver）中。 有关如何使用沙盒的详细信息，请参阅[在 Azure 自动化中执行 Runbook](https://docs.microsoft.com/azure/automation/automation-runbook-execution.md)。

## <a name="recommended-documents"></a>建议的文档

* [在 Azure 自动化中启动 Runbook](https://docs.microsoft.com/azure/automation/automation-starting-a-runbook)
* [Azure Automation 中的 Runbook 执行](https://docs.microsoft.com/azure/automation/automation-runbook-execution)

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过[Azure 论坛](https://azure.microsoft.com/support/forums/)获取 azure 专家的解答。
* 与 [@AzureSupport](https://twitter.com/azuresupport)（Microsoft Azure 官方帐户）联系，它可以将 Azure 社区引导至适当的资源来改进客户体验：提供解答、支持和专业化服务。
* 如需更多帮助，可以提交 Azure 支持事件。 请转到 [Azure 支持站点](https://azure.microsoft.com/support/options/)并选择 **获取支持**。
