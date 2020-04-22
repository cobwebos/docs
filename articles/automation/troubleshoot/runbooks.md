---
title: 排除 Azure 自动化运行簿错误
description: 了解如何排查和解决在使用 Azure 自动化 Runbook 时可能遇到的问题。
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/24/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 5ed25821f606b98bacf2acf3c2c389a8437406fa
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770907"
---
# <a name="troubleshoot-runbook-errors"></a>排除运行簿错误

 本文介绍了可能发生的各种 Runbook 错误以及如何解决这些问题。

>[!NOTE]
>本文进行了更新，以便使用新的 Azure PowerShell Az 模块。 你仍然可以使用 AzureRM 模块，至少在 2020 年 12 月之前，它将继续接收 bug 修补程序。 若要详细了解新的 Az 模块和 AzureRM 兼容性，请参阅[新 Azure Powershell Az 模块简介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 有关混合 Runbook 辅助角色上的 Az 模块安装说明，请参阅[安装 Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 对于自动化帐户，可以使用["如何更新 Azure 自动化 中的 Azure PowerShell"模块](../automation-update-azure-modules.md)将模块更新到最新版本。

## <a name="diagnosing-runbook-issues"></a>诊断运行簿问题

在 Azure 自动化中的 Runbook 执行期间收到错误时，可以使用以下步骤来帮助诊断问题。

1. **确保 Runbook 脚本在本地计算机上成功执行。** 

    请参阅 [PowerShell 文档](/powershell/scripting/overview)或 [Python 文档](https://docs.python.org/3/)，了解语言参考和学习模块。 在本地执行脚本即可发现并解决常见错误，例如：

      * 缺少模块
      * 语法错误
      * 逻辑错误

2. **调查运行簿[错误流](https://docs.microsoft.com/azure/automation/automation-runbook-output-and-messages#runbook-output)。**

    查看这些特定消息的流，并将其与本文中记录的错误进行比较。

3. **确保您的节点和自动化工作区具有所需的模块。** 

    如果 Runbook 导入任何模块，请使用[导入模块](../shared-resources/modules.md#importing-modules)中列出的步骤验证它们是否可用于您的自动化帐户。 按照[Azure 自动化 中更新 Azure 模块](..//automation-update-azure-modules.md)中的说明将模块更新到最新版本。 有关详细信息，请参阅[故障排除模块](shared-resources.md#modules)。

4. **如果 Runbook 挂起或意外失败，请执行操作。**

    * [检查作业状态](https://docs.microsoft.com/azure/automation/automation-runbook-execution#job-statuses)定义 Runbook 状态和一些可能的原因。
    * 向 Runbook[添加其他输出](https://docs.microsoft.com/azure/automation/automation-runbook-output-and-messages#message-streams)，以标识在暂存 Runbook 之前发生的情况。
    * [处理作业引发的任何异常](https://docs.microsoft.com/azure/automation/automation-runbook-execution#handling-exceptions)。

## <a name="scenario-run-login-azurermaccount-to-log-in"></a><a name="login-azurerm"></a>方案：运行登录-AzureRM帐户以登录

### <a name="issue"></a>问题

执行 runbook 时收到以下错误：

```error
Run Login-AzureRMAccount to login.
```

### <a name="cause"></a>原因

当您不使用"运行作为"帐户或"运行为"帐户已过期时，可能会发生此错误。 请参阅[管理 Azure 自动化作为帐户运行](https://docs.microsoft.com/azure/automation/manage-runas-account)。

此错误有两个主要原因：

* AzureRM 或 Az 模块有不同的版本。
* 您正在尝试访问单独的订阅中的资源。

### <a name="resolution"></a>解决方法

如果在更新一个 AzureRM 或 Az 模块后收到此错误，则应将所有模块更新为同一版本。

如果您尝试访问其他订阅中的资源，可以按照以下步骤配置权限。

1. 转到"自动运行为帐户"并复制应用程序 ID 和指纹。

    ![复制 ID 和指纹](../media/troubleshoot-runbooks/collect-app-id.png)

1. 转到未托管自动化帐户的订阅访问控制，并添加新的角色分配。

    ![访问控制](../media/troubleshoot-runbooks/access-control.png)

1. 添加较早收集的应用程序 ID。 选择“参与者”权限。

    ![添加角色分配](../media/troubleshoot-runbooks/add-role-assignment.png)

1. 复制订阅的名称。

1. 现在，您可以使用以下 Runbook 代码来测试从自动化帐户到其他订阅的权限。 替换为`"\<CertificateThumbprint\>"`步骤 1 中复制的值。 替换为`"\<SubscriptionName\>"`步骤 4 中复制的值。

    ```powershell
    $Conn = Get-AutomationConnection -Name AzureRunAsConnection
    Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint "<CertificateThumbprint>"
    #Select the subscription you want to work with
    Select-AzSubscription -SubscriptionName '<YourSubscriptionNameGoesHere>'

    #Test and get outputs of the subscriptions you granted access.
    $subscriptions = Get-AzSubscription
    foreach($subscription in $subscriptions)
    {
        Set-AzContext $subscription
        Write-Output $subscription.Name
    }
    ```

## <a name="scenario-unable-to-find-the-azure-subscription"></a><a name="unable-to-find-subscription"></a>场景：无法找到 Azure 订阅

### <a name="issue"></a>问题

使用`Select-AzureSubscription`时收到以下错误`Select-AzureRMSubscription`， 或`Select-AzSubscription`cmdlet：

```error
The subscription named <subscription name> cannot be found.
```

### <a name="error"></a>错误

以下情况下可能发生此错误：

* 订阅名称无效。
* 尝试获取订阅详细信息的 Azure 活动目录用户未配置为订阅的管理员。
* cmdlet 不可用。

### <a name="resolution"></a>解决方法

按照以下步骤确定您是否已对 Azure 进行身份验证，以及是否有权访问尝试选择的订阅。

1. 为了确保脚本独立工作，请将其测试到 Azure 自动化之外。
2. 在运行`Select-*`cmdlet 之前，请确保脚本运行[连接-AzAccount](https://docs.microsoft.com/powershell/module/Az.Accounts/Connect-AzAccount?view=azps-3.7.0) cmdlet。
3. 将 `Disable-AzContextAutosave –Scope Process` 添加到 runbook 的开头。 此 cmdlet 调用可确保任何凭据仅适用于当前 Runbook 的执行。
4. 如果仍然看到此错误消息，请通过添加 的`AzContext``Connect-AzAccount`参数来修改代码，然后执行代码。

   ```powershell
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $context = Get-AzContext

   Get-AzVM -ResourceGroupName myResourceGroup -AzContext $context
    ```

## <a name="scenario-authentication-to-azure-failed-because-multi-factor-authentication-is-enabled"></a><a name="auth-failed-mfa"></a>场景：无法向 Azure 进行身份验证，因为已启用多重身份验证

### <a name="issue"></a>问题

使用 Azure 用户名和密码向 Azure 进行身份验证时收到以下错误：

```error
Add-AzureAccount: AADSTS50079: Strong authentication enrollment (proof-up) is required
```

### <a name="cause"></a>原因

如果对 Azure 帐户设置了多重身份验证，则不能使用 Azure Active Directory 用户向 Azure 进行身份验证。 相反，您需要使用证书或服务主体进行身份验证。

### <a name="resolution"></a>解决方法

要将证书与 Azure 经典部署模型 cmdlet 一起使用，请参阅[创建并添加证书来管理 Azure 服务](https://blogs.technet.com/b/orchestrator/archive/2014/04/11/managing-azure-services-with-the-microsoft-azure-automation-preview-service.aspx)。 要将服务主体与 Azure 资源管理器 cmdlet 一起使用，请参阅[使用 Azure 门户创建服务主体](../../active-directory/develop/howto-create-service-principal-portal.md)，并使用[Azure 资源管理器对服务主体进行身份验证](../../active-directory/develop/howto-authenticate-service-principal-powershell.md)。

## <a name="scenario-you-see-an-error-in-your-job-streams-about-the-get_serializationsettings-method"></a><a name="get-serializationsettings"></a>方案：在作业流中看到有关get_SerializationSettings方法的错误

### <a name="issue"></a>问题

在 Runbook 的作业流中可以看到以下错误：

```error
Connect-AzAccount : Method 'get_SerializationSettings' in type
'Microsoft.Azure.Management.Internal.Resources.ResourceManagementClient' from assembly
'Microsoft.Azure.Commands.ResourceManager.Common, Version=4.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35'
does not have an implementation.
At line:16 char:1
+ Connect-AZAccount -ServicePrincipal -Tenant $Conn.TenantID -Appl ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : NotSpecified: (:) [Connect-AzAccount], TypeLoadException
    + FullyQualifiedErrorId : System.TypeLoadException,Microsoft.Azure.Commands.Profile.ConnectAzAccountCommand
```

### <a name="cause"></a>原因

此错误是由在 Runbook 中使用 AzureRM 和 Az 模块 cmdlet 引起的。 在导入 AzureRM 模块之前导入 Az 模块时，将发生这种情况。

### <a name="resolution"></a>解决方法

不能在同一运行簿中导入和使用 Az 和 AzureRM cmdlet。 要了解有关 Azure 自动化中的 Az cmdlet 的更多内容，请参阅[在 Azure 自动化 中管理模块](../shared-resources/modules.md)。

## <a name="scenario-the-runbook-fails-with-the-error-a-task-was-canceled"></a><a name="task-was-cancelled"></a>场景：Runbook 失败且出现错误：取消了一个任务

### <a name="issue"></a>问题

Runbook 失败，出现类似于以下示例的错误：

```error
Exception: A task was canceled.
```

### <a name="cause"></a>原因

使用过时的 Azure 模块会导致此错误。

### <a name="resolution"></a>解决方法

您可以通过将 Azure 模块更新到最新版本来解决此错误。 

1. 在自动化帐户中，单击 **"模块**"，然后单击 **"更新 Azure 模块**"。 
2. 更新大约需要 15 分钟。 完成后，重新运行失败的 Runbook。

要了解有关更新模块的详细信息，请参阅在[Azure 自动化 中更新 Azure 模块](../automation-update-azure-modules.md)。

## <a name="scenario-runbooks-fail-when-dealing-with-multiple-subscriptions"></a><a name="runbook-auth-failure"></a>方案：处理多个订阅时 Runbook 失败

### <a name="issue"></a>问题

执行 runbook 时，runbook 无法管理 Azure 资源。

### <a name="cause"></a>原因

Runbook 在运行时没有使用正确的上下文。

### <a name="resolution"></a>解决方法

当 Runbook 调用多个 Runbook 时，订阅上下文可能会丢失。 为确保订阅上下文传递到 Runbook，请让客户端 Runbook 将上下文传递给`Start-AzureRmAutomationRunbook``AzureRmContext`参数中的 cmdlet。 将`Disable-AzureRmContextAutosave`cmdlet 与`Scope`参数集一`Process`起使用，以确保指定的凭据仅用于当前 Runbook。 有关详细信息，请参阅[使用多个订阅](../automation-runbook-execution.md#working-with-multiple-subscriptions)。

```azurepowershell-interactive
# Ensures that any credentials apply only to the execution of this runbook
Disable-AzContextAutosave –Scope Process

# Connect to Azure with Run As account
$ServicePrincipalConnection = Get-AutomationConnection -Name 'AzureRunAsConnection'

Connect-AzAccount `
    -ServicePrincipal `
    -Tenant $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint

$AzContext = Select-AzSubscription -SubscriptionId $ServicePrincipalConnection.SubscriptionID

$params = @{"VMName"="MyVM";"RepeatCount"=2;"Restart"=$true}

Start-AzAutomationRunbook `
    –AutomationAccountName 'MyAutomationAccount' `
    –Name 'Test-ChildRunbook' `
    -ResourceGroupName 'LabRG' `
    -AzContext $AzureContext `
    –Parameters $params –wait
```

## <a name="scenario-term-not-recognized-as-the-name-of-a-cmdlet-function-script"></a><a name="not-recognized-as-cmdlet"></a>方案：术语未识别为 cmdlet 的名称、函数、脚本

### <a name="issue"></a>问题

Runbook 失败，出现类似于以下示例的错误：

```error
The term 'Connect-AzAccount' is not recognized as the name of a cmdlet, function, script file, or operable program. Check the spelling of the name, or if the path was included verify that the path is correct and try again.
```

### <a name="cause"></a>原因

发生此错误的原因如下：

* 包含 cmdlet 的模块不会导入到自动化帐户中。
* 包含 cmdlet 的模块已导入，但已过期。

### <a name="resolution"></a>解决方法

执行以下任务之一来解决此错误。 

* 有关 Azure 模块，请参阅[如何在 Azure 自动化中更新 Azure PowerShell 模块](../automation-update-azure-modules.md)，了解如何在自动化帐户中更新模块。

* 对于非 Azure 模块，请确保 中的模块导入到自动化帐户中。

## <a name="scenario-the-runbook-job-start-was-attempted-three-times-but-failed-to-start-each-time"></a><a name="job-attempted-3-times"></a>方案：运行簿作业启动尝试了三次，但每次都无法启动

### <a name="issue"></a>问题

Runbook 失败，出现以下错误：

```error
The job was tried three times but it failed
```

### <a name="cause"></a>原因

出现此错误的原因是以下问题之一：

* 内存限制。 如果作业使用超过 400 MB 的内存，则可能会失败。 分配给沙盒的内存的记录限制位于[自动化服务限制](../../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)处。 

* 网络套接字。 Azure 沙盒限制为 1000 个并发网络套接字。 请参阅[自动化服务限制](../../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)。

* 模块不兼容。 模块依赖项可能不正确。 在这种情况下，runbook 通常会返回 或`Command not found``Cannot bind parameter`消息。

* 没有沙盒活动目录的身份验证。 Runbook 尝试调用在 Azure 沙盒中运行的可执行进程或子进程。 不支持使用 Azure 活动目录身份验证库 （ADAL） 配置运行簿以使用 Azure AD 进行身份验证。

* 异常数据过多。 runbook 尝试向输出流写入太多异常数据。

### <a name="resolution"></a>解决方法

* 内存限制，网络套接字。 建议在内存限制内工作的方法包括：在多个 Runbook 之间拆分工作负载，处理内存中更少的数据，避免从 Runbook 编写不必要的输出，并考虑将多少个检查点写入 PowerShell 工作流 Runbook。 使用清除方法（如`$myVar.clear`）清除变量，并用于`[GC]::Collect`立即运行垃圾回收。 这将减少运行时期间 runbook 的内存占用情况。

* 模块不兼容。 按照["如何更新 Azure 自动化 中的 Azure PowerShell"模块](../automation-update-azure-modules.md)的步骤更新 Azure 模块。

* 没有用于沙盒的活动目录的身份验证。 使用 Runbook 对 Azure AD 进行身份验证时，请确保 Azure AD 模块在自动化帐户中可用。 请务必向"运行为"帐户授予执行 Runbook 自动执行的任务所需的权限。

  如果 Runbook 无法调用在 Azure 沙盒中运行的可执行进程或子进程，请使用[混合 Runbook 辅助角色上的 Runbook。](../automation-hrw-run-runbooks.md) 混合辅助角色不受 Azure 沙盒的内存和网络限制的限制。

* 异常数据过多。 作业输出流限制为 1MB。 确保 runbook 将调用包含对可执行进程或子进程的调用，`try`使用`catch`和 块。 如果操作引发异常，则让代码将异常的消息写入自动化变量。 此技术可防止将消息写入作业输出流。

## <a name="scenario-sign-in-to-azure-account-failed"></a><a name="sign-in-failed"></a>方案：登录到 Azure 帐户失败

### <a name="issue"></a>问题

使用`Connect-AzAccount`cmdlet 时，您会收到以下错误之一：

```error
Unknown_user_type: Unknown User Type
```

```error
No certificate was found in the certificate store with thumbprint
```

### <a name="cause"></a>原因

如果凭据资产名称无效，则会发生这些错误。 如果您用于设置自动化凭据资产的用户名和密码无效，也可能发生此类操作。

### <a name="resolution"></a>解决方法

要确定具体错误，请执行以下步骤：

1. 请确保没有包含任何特殊字符。 这些字符包括用于连接 Azure 的自动化凭据资产名称中的  字符`\@`。
2. 检查是否可以使用存储在本地 PowerShell ISE 编辑器中的 Azure 自动化凭据中的用户名和密码。 在 PowerShell ISE 中运行以下 cmdlet。

   ```powershell
   $Cred = Get-Credential
   #Using Azure Service Management
   Add-AzureAccount –Credential $Cred
   #Using Azure Resource Manager
   Connect-AzAccount –Credential $Cred
   ```

3. 如果身份验证在本地失败，则未正确设置 Azure 活动目录凭据。 请参阅使用[Azure 活动目录博客文章对 Azure 进行身份验证](https://azure.microsoft.com/blog/azure-automation-authenticating-to-azure-using-azure-active-directory/)，以便正确设置 Azure 活动目录帐户。

4. 如果错误看起来是暂时性的，请尝试向身份验证例程添加重试逻辑，以使身份验证更加可靠。

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
       $connectionResult = Connect-AzAccount `
                              -ServicePrincipal `
                              -Tenant $servicePrincipalConnection.TenantId `
                              -ApplicationId $servicePrincipalConnection.ApplicationId `
                              -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }
   ```

## <a name="scenario-object-reference-not-set-to-an-instance-of-an-object"></a><a name="child-runbook-object"></a>方案：对象引用未设置为对象的实例

### <a name="issue"></a>问题

使用`Wait`参数调用子 Runbook 时，您会收到以下错误，并且输出流包含一个对象：

```error
Object reference not set to an instance of an object
```

### <a name="cause"></a>原因

`Start-AzAutomationRunbook`如果流包含对象，则无法正确处理输出流。

### <a name="resolution"></a>解决方法

建议实现轮询逻辑，并使用[Get-AzAutomationJob输出](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJobOutput?view=azps-3.7.0)cmdlet 检索输出。 下面定义了此逻辑的示例。

```powershell
$automationAccountName = "ContosoAutomationAccount"
$runbookName = "ChildRunbookExample"
$resourceGroupName = "ContosoRG"

function IsJobTerminalState([string] $status) {
    return $status -eq "Completed" -or $status -eq "Failed" -or $status -eq "Stopped" -or $status -eq "Suspended"
}

$job = Start-AzAutomationRunbook -AutomationAccountName $automationAccountName -Name $runbookName -ResourceGroupName $resourceGroupName
$pollingSeconds = 5
$maxTimeout = 10800
$waitTime = 0
while((IsJobTerminalState $job.Status) -eq $false -and $waitTime -lt $maxTimeout) {
   Start-Sleep -Seconds $pollingSeconds
   $waitTime += $pollingSeconds
   $job = $job | Get-AzAutomationJob
}

$jobResults | Get-AzAutomationJobOutput | Get-AzAutomationJobOutputRecord | Select-Object -ExpandProperty Value
```

## <a name="scenario-runbook-fails-because-of-deserialized-object"></a><a name="fails-deserialized-object"></a>场景：Runbook 因反序列化的对象而失败

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

* 如果要将复杂对象从一厘米长管道到另一个 cmdlet，请将这些 cmdlet 包装在`InlineScript`活动中。
* 传递复杂对象中你所需要的名称或值，不必传递整个对象。
* 使用 PowerShell Runbook，而不使用 PowerShell 工作流 Runbook。

## <a name="scenario-runbook-job-fails-because-allocated-quota-exceeded"></a><a name="quota-exceeded"></a>方案：Runbook 作业失败，因为已分配的配额超过

### <a name="issue"></a>问题

Runbook 作业失败并显示错误：

```error
The quota for the monthly total job run time has been reached for this subscription
```

### <a name="cause"></a>原因

作业执行时间超过帐户的 500 分钟免费配额时，就会出现此错误。 此配额适用于所有类型的作业执行任务。 其中一些任务是测试作业、从门户启动作业、使用 Webhook 执行作业，或者安排作业以使用 Azure 门户或数据中心执行。 要了解有关自动化定价的更多信息，请参阅[自动化定价](https://azure.microsoft.com/pricing/details/automation/)。

### <a name="resolution"></a>解决方法

如果要每月使用超过 500 分钟的处理时间，请将订阅从"免费"层更改为"基本"层。

1. 登录到 Azure 订阅。
2. 选择要升级的自动化帐户。
3. 单击 **"设置"，** 然后单击**定价**。
4. 单击"在页面底部**启用**"可将您的帐户升级到基本层。

## <a name="scenario-cmdlet-not-recognized-when-executing-a-runbook"></a><a name="cmdlet-not-recognized"></a>场景：在执行 Runbook 时无法识别 Cmdlet

### <a name="issue"></a>问题

Runbook 作业失败并显示错误：

```error
<cmdlet name>: The term <cmdlet name> is not recognized as the name of a cmdlet, function, script file, or operable program.
```

### <a name="cause"></a>原因

当 PowerShell 引擎找不到要在 runbook 中使用的 cmdlet 时，则会导致此错误。 帐户中可能缺少包含 cmdlet 的模块，与 Runbook 名称存在名称冲突，或者 cmdlet 也存在于另一个模块中，并且自动化无法解析该名称。

### <a name="resolution"></a>解决方法

使用以下任一解决方案来解决此问题。

* 请确保输入了 cmdlet 名称。
* 确保 cmdlet 存在于您的自动化帐户中，并且没有冲突。 要验证 cmdlet 是否存在，请在编辑模式下打开 Runbook 并搜索要在库中找到的 cmdlet 或运行`Get-Command <CommandName>`。 验证 cmdlet 对帐户可用且没有与其他 cmdlet 或 Runbook 的名称冲突后，请将 cmdlet 添加到画布，并确保在 Runbook 中使用有效的参数集。
* 如果存在名称冲突，并且 cmdlet 在两个不同的模块中可用，请使用 cmdlet 的完全限定名称来解决此问题。 例如，你可以使用 `ModuleName\CmdletName`。
* 如果要在混合辅助角色组中在本地执行 Runbook，请确保模块和 cmdlet 安装在承载混合辅助角色的计算机上。

## <a name="scenario-a-long-running-runbook-fails-to-complete"></a><a name="long-running-runbook"></a>方案：长时间运行的运行手册无法完成

### <a name="issue"></a>问题

运行 3 小时后，runbook 将显示处于“已停止”状态。 您可能还会收到此错误：

```error
The job was evicted and subsequently reached a Stopped state. The job cannot continue running.
```

此行为是在 Azure 沙盒中设计，因为 Azure 自动化中的进程[具有公平的共享](../automation-runbook-execution.md#fair-share)监视。 如果进程执行的时间超过三个小时，则公平共享将自动停止运行簿。 超过公平共享时间限制的 Runbook 的状态因 Runbook 类型而异。 PowerShell 和 Python runbook 设置为“已停止”状态。 PowerShell 工作流 runbook 设置为“失败”。

### <a name="cause"></a>原因

Runbook 超过 Azure 沙盒中公平共享允许的 3 小时限制。

### <a name="resolution"></a>解决方法

建议的解决方案是在[混合 Runbook 辅助角色](../automation-hrw-run-runbooks.md)上运行 runbook。 混合工作角色不受 Azure 沙盒的 3 小时公平共享运行簿限制的限制。 应开发在混合 Runbook 工作簿上运行的 Runbook，以支持在出现意外的本地基础结构问题时的重新启动行为。

另一个解决方案是通过创建子 Runbook 来优化[Runbook。](../automation-child-runbooks.md) 如果 Runbook 循环多个资源上的同一函数，例如，在多个数据库上的数据库操作中，可以将该函数移动到子 Runbook。 每个子运行簿在单独的进程中并行执行。 此行为降低了完成父 runbook 所需的时间总量。

启用子 runbook 方案的 PowerShell cmdlet 是：

* [启动-阿兹自动化Runbook](https://docs.microsoft.com/powershell/module/Az.Automation/Start-AzAutomationRunbook?view=azps-3.7.0)。 此 cmdlet 用于启动 Runbook 并将参数传递给该 Runbook。

* [获取-阿兹自动化作业](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJob?view=azps-3.7.0)。 如果在子 Runbook 完成后需要执行一些操作，则此 cmdlet 允许您检查每个子级的作业状态。

## <a name="scenario-status-400-bad-request-when-calling-a-webhook"></a><a name="expired webhook"></a>方案：状态：调用 Webhook 时有 400 个错误请求

### <a name="issue"></a>问题

当您尝试为 Azure 自动化运行簿调用 Webhook 时，您会收到以下错误：

```error
400 Bad Request : This webhook has expired or is disabled
```

### <a name="cause"></a>原因

尝试调用的 Webhook 已禁用，或者已过期。

### <a name="resolution"></a>解决方法

如果 Webhook 处于禁用状态，可以通过 Azure 门户重新启用 Webhook。 如果 Webhook 已过期，则必须删除并重新创建它。 如果尚未过期，只能[续订 Webhook](../automation-webhooks.md#renew-webhook)。

## <a name="scenario-429-the-request-rate-is-currently-too-large"></a><a name="429"></a>方案： 429： 请求速率当前太大...

### <a name="issue"></a>问题

在运行 `Get-AzAutomationJobOutput` cmdlet 时收到以下错误消息：

```error
429: The request rate is currently too large. Please try again
```

### <a name="cause"></a>原因

从具有许多[详细流](../automation-runbook-output-and-messages.md#verbose-stream)的 Runbook 检索作业输出时，可能会发生此错误。

### <a name="resolution"></a>解决方法

执行以下操作之一来解决此错误。

* 编辑 Runbook，并减少它发出的作业流数量。

* 减少运行 cmdlet 时要检索的流数量。 为此，您可以设置[Get-AzAutomation输出](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJobOutput?view=azps-3.7.0) `Stream` cmdlet 的参数值，以便仅检索输出流。 

## <a name="scenario-powershell-job-fails-with-error-cannot-invoke-method"></a><a name="cannot-invoke-method"></a>方案：PowerShell 作业失败，出错：无法调用方法

### <a name="issue"></a>问题

在 Azure 中运行的 Runbook 中启动 PowerShell 作业时，您将收到以下错误消息：

```error
Exception was thrown - Cannot invoke method. Method invocation is supported only on core types in this language mode.
```

### <a name="cause"></a>原因

此错误可能表示在 Azure 沙盒中执行的 Runbook 无法在["完整语言"模式下](/powershell/module/microsoft.powershell.core/about/about_language_modes)运行。

### <a name="resolution"></a>解决方法

有两种方法可以解决此问题。

* 不使用["开始作业"，](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/start-job?view=powershell-7)请使用["开始-Az自动化 Runbook"](https://docs.microsoft.com/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.7.0)来启动 Runbook。
* 尝试在混合 Runbook 工作线程上运行 Runbook。

要了解有关 Azure 自动化 Runbook 的此行为和其他行为的更多信息，请参阅[Runbook 行为](../automation-runbook-execution.md#runbook-behavior)。

## <a name="scenario-linux-hybrid-runbook-worker-receives-a-prompt-for-a-password-when-signing-a-runbook"></a>方案：Linux 混合 Runbook 工作人员在签署 Runbook 时收到密码提示

### <a name="issue"></a>问题

运行`sudo`Linux 混合 Runbook 辅助角色的命令会检索密码的意外提示。

### <a name="cause"></a>原因

Linux 日志分析代理的**nx 自动化用户帐户**在**sudoers**文件中未正确配置。 混合 Runbook 辅助角色需要适当的帐户权限和其他数据配置，以便它可以在 Linux Runbook 辅助角色上签名 Runbook。

### <a name="resolution"></a>解决方法

* 确保混合 Runbook 工作线程在计算机上具有 GnuPG （GPG） 可执行文件。

* 验证**sudoers**文件中**的 nx 自动化用户帐户**的配置。 请参阅[在混合 Runbook 工作线程上运行 Runbook](../automation-hrw-run-runbooks.md)

## <a name="scenario-cmdlet-failing-in-pnp-powershell-runbook-on-azure-automation"></a>方案：在 Azure 自动化上的 PnP PowerShell 运行簿中出现 Cmdlet 失败

### <a name="issue"></a>问题

当 Runbook 将 PnP PowerShell 生成的对象直接写入 Azure 自动化输出时，cmdlet 输出无法流式传输回自动化。

### <a name="cause"></a>原因

此问题最常发生在 Azure 自动化处理调用 PnP PowerShell cmdlet 的运行簿时，`add-pnplistitem`例如，而不捕获返回对象。

### <a name="resolution"></a>解决方法

编辑脚本以将任何返回值分配给变量，以便 cmdlet 不会尝试将整个对象写入标准输出。 脚本可以将输出流重定向到 cmdlet，如下所示。

```azurecli
  $null = add-pnplistitem
```

如果脚本分析 cmdlet 输出，脚本必须将输出存储在变量中并操作变量，而不是简单地流式传输输出。

```azurecli
$SomeVariable = add-pnplistitem ....
if ($SomeVariable.someproperty -eq ....
```

## <a name="scenario-invalid-status-code-forbidden-when-using-key-vault-inside-a-runbook"></a>方案：在 Runbook 中使用密钥保管库时，无效状态代码"禁止"

### <a name="issue"></a>问题

尝试通过 Azure 自动化运行簿访问密钥保管库时，会收到以下错误：

```error
Operation returned an invalid status code 'Forbidden' 
```

### <a name="cause"></a>原因

此问题的可能原因：

* 不使用"运行为"帐户。
* 权限不足。

### <a name="resolution"></a>解决方法

#### <a name="not-using-run-as-account"></a>不使用"作为帐户运行"

按照步骤[5 中的步骤 - 添加身份验证来管理 Azure 资源](https://docs.microsoft.com/azure/automation/automation-first-runbook-textual-powershell#add-authentication-to-manage-azure-resources)，以确保使用"运行作为"帐户访问密钥保管库。 

#### <a name="insufficient-permissions"></a>权限不足

按照"[向密钥保管库添加权限"](https://docs.microsoft.com/azure/automation/manage-runas-account#add-permissions-to-key-vault)中的步骤操作，以确保运行"作为"帐户具有访问密钥保管库的足够权限。 

## <a name="my-problem-isnt-listed-above"></a><a name="other"></a>上面未列出我的问题

以下各节列出了其他常见错误，并提供支持文档以帮助您解决问题。

### <a name="hybrid-runbook-worker-doesnt-run-jobs-or-isnt-responding"></a>混合 runbook 辅助角色不运行作业，或者没有响应

如果在混合 Runbook 工作线程上而不是 Azure 自动化中运行作业，则可能需要[对混合辅助角色本身进行故障排除](https://docs.microsoft.com/azure/automation/troubleshoot/hybrid-runbook-worker)。

### <a name="runbook-fails-with-no-permission-or-some-variation"></a>Runbook 失败，没有权限或某些变体

"运行为帐户"对 Azure 资源的权限可能与当前帐户没有相同的权限。 确保您的"运行为"帐户具有访问脚本中使用的[任何资源的权限](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)。

### <a name="issues-passing-parameters-into-webhooks"></a>将参数传递到网钩的问题

有关将参数传递到 Webhook 的帮助，请参阅[从 Webhook 启动 Runbook。](../automation-webhooks.md#parameters-used-when-the-webhook-starts-a-runbook)

### <a name="issues-using-az-modules"></a>使用 Az 模块的问题

不支持在同一自动化帐户中使用 Az 模块和 AzureRM 模块。 有关详细信息[，请参阅 Runbook 中的 Az 模块](https://docs.microsoft.com/azure/automation/az-modules)。

### <a name="inconsistent-behavior-in-runbooks"></a>Runbook 中的行为不一致

按照[Runbook 执行](https://docs.microsoft.com/azure/automation/automation-runbook-execution#runbook-behavior)中的指南操作，以避免在 Runbook 中出现并发作业、多次创建资源或其他对计时敏感的逻辑的问题。

### <a name="runbook-fails-with-the-error-no-permission-forbidden-403-or-some-variation"></a>Runbook 失败，错误 无权限、禁止 （403） 或某些变体

"运行为帐户"对 Azure 资源的权限可能与当前帐户没有相同的权限。 确保您的"运行为"帐户具有访问脚本中使用的[任何资源的权限](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)。

### <a name="runbooks-were-working-but-suddenly-stopped"></a>Runbook 在工作时突然停止

* 确保"运行为"帐户尚未过期。 请参阅[认证续订](https://docs.microsoft.com/azure/automation/manage-runas-account#cert-renewal)。
* 如果您使用[Webhook](https://docs.microsoft.com/azure/automation/automation-webhooks#renew-webhook)启动 Runbook，请确保 Webhook 尚未过期。

### <a name="passing-parameters-into-webhooks"></a>将参数传递到 webhook

有关将参数传递到 Webhook 的帮助，请参阅[从 Webhook 启动 Runbook。](https://docs.microsoft.com/azure/automation/automation-webhooks#parameters-used-when-the-webhook-starts-a-runbook)

### <a name="using-az-modules"></a>使用 Az 模块

不支持在同一自动化帐户中使用 Az 模块和 AzureRM 模块。 请参阅[Runbook 中的 Az 模块](https://docs.microsoft.com/azure/automation/az-modules)。

### <a name="using-self-signed-certificates"></a>使用自签名证书

要使用自签名证书，请参阅[创建新证书](https://docs.microsoft.com/azure/automation/shared-resources/certificates#creating-a-new-certificate)。

### <a name="access-denied-when-using-azure-sandbox-for-a-runbook"></a>使用 Runbook 的 Azure 沙盒时拒绝访问

Azure 沙盒可阻止访问所有进程外 COM 服务器。 例如，沙盒应用程序或 Runbook 无法调用 Windows 管理检测 （WMI） 或 Windows 安装程序服务 （msiserver.exe）。 有关使用沙盒的详细信息，请参阅 Azure 自动化[中的 Runbook 执行](https://docs.microsoft.com/azure/automation/automation-runbook-execution)。

## <a name="recommended-documents"></a>建议的文档

* [在 Azure 自动化中启动 Runbook](https://docs.microsoft.com/azure/automation/automation-starting-a-runbook)
* [在 Azure 自动化中执行 Runbook](https://docs.microsoft.com/azure/automation/automation-runbook-execution)

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过[Azure 论坛](https://azure.microsoft.com/support/forums/)从 Azure 专家那里获得答案。
* 与[@AzureSupport](https://twitter.com/azuresupport)— 正式的 Microsoft Azure 帐户连接，通过将 Azure 社区连接到正确的资源（答案、支持和专家），从而改善客户体验。
* 如需更多帮助，可以提交 Azure 支持事件。 转到[Azure 支持站点](https://azure.microsoft.com/support/options/)并选择 **"获取支持**"。
