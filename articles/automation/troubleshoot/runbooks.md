---
title: 排查 Azure 自动化 Runbook 问题
description: 本文介绍如何排查和解决 Azure 自动化 Runbook 的问题。
services: automation
ms.date: 07/28/2020
ms.topic: conceptual
ms.service: automation
ms.custom: has-adal-ref
ms.openlocfilehash: 1cbb5be8c1a4045b218c0e6bf5ac7ed0b901aa80
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2020
ms.locfileid: "87904796"
---
# <a name="troubleshoot-runbook-issues"></a>排查 Runbook 问题

 本文描述可能会发生的各种 Runbook 问题及其解决方法。 有关常规信息，请参阅[在 Azure 自动化中执行 Runbook](../automation-runbook-execution.md)。

## <a name="diagnose-runbook-issues"></a>诊断 Runbook 问题

如果在 Azure 自动化中执行 Runbook 期间遇到错误，可使用以下步骤来帮助诊断问题：

1. 确保 Runbook 脚本在本地计算机上执行成功。

    有关语言参考和学习模块，请参阅 [PowerShell 文档](/powershell/scripting/overview)或 [Python 文档](https://docs.python.org/3/)。在本地运行脚本即可发现并解决常见错误，例如：

      * 缺少模块
      * 语法错误
      * 逻辑错误

1. 调查 Runbook [错误流](../automation-runbook-output-and-messages.md#runbook-output)。

    查看这些流中的特定消息，并将其与本文中所述的错误进行比较。

1. 确保节点和自动化工作区具有所需的模块。

    如果 Runbook 导入任何模块，请使用[导入模块](../shared-resources/modules.md#import-modules)中的步骤来验证这些模块是否可供自动化帐户使用。 按照[更新 Azure 自动化中的 Azure PowerShell 模块](../automation-update-azure-modules.md)中的说明将 PowerShell 模块更新到最新版本。 有关更多的故障排除信息，请参阅[排查模块问题](shared-resources.md#modules)。

1. 如果 Runbook 暂停或意外失败：

    * 如果运行方式帐户已过期，请[续订证书](../manage-runas-account.md#cert-renewal)。
    * 如果尝试用来启动 Runbook 的 Webhook 已过期，请[续订 Webhook](../automation-webhooks.md#renew-a-webhook)。
    * [检查作业状态](../automation-runbook-execution.md#job-statuses)，确定当前 Runbook 状态以及导致问题的一些可能原因。
    * [将更多输出添加到](../automation-runbook-output-and-messages.md#monitor-message-streams) Runbook，以确定 Runbook 在暂停之前发生了什么情况。
    * [处理由作业引发的任何异常](../automation-runbook-execution.md#exceptions)。

1. 如果混合 Runbook 辅助角色中的 Runbook 作业或环境无响应，请执行此步骤。

    如果在混合 Runbook 辅助角色而不是 Azure 自动化中运行 Runbook 作业，可能需要[排查混合辅助角色本身的问题](hybrid-runbook-worker.md)。

## <a name="scenario-runbook-fails-with-a-no-permission-or-forbidden-403-error"></a><a name="runbook-fails-no-permission"></a>场景：Runbook 失败并出现“无权限”或“禁止 403”错误

### <a name="issue"></a>问题

Runbook 失败并出现“无权限”或“禁止 403”错误或者类似的错误。

### <a name="cause"></a>原因

运行方式帐户对 Azure 资源的权限可能与当前自动化帐户的权限不同。 

### <a name="resolution"></a>解决方法

确保运行方式帐户[有权访问脚本中使用的任何资源](../../role-based-access-control/role-assignments-portal.md)。

## <a name="scenario-sign-in-to-azure-account-failed"></a><a name="sign-in-failed"></a>场景：登录到 Azure 帐户失败

### <a name="issue"></a>问题

使用 `Connect-AzAccount` cmdlet 时遇到以下错误之一：

```error
Unknown_user_type: Unknown User Type
```

```error
No certificate was found in the certificate store with thumbprint
```

### <a name="cause"></a>原因

如果凭据资产名称无效，将发生这些错误。 如果用于设置自动化凭据资产的用户名和密码无效，也可能会发生这些错误。

### <a name="resolution"></a>解决方法

若要确定问题所在，请执行以下步骤：

1. 确保没有包含任何特殊字符。 这些字符包括用于连接 Azure 的自动化凭据资产名称中的 `\@` 字符。
1. 查看是否能够在本地 PowerShell ISE 编辑器中使用存储在 Azure 自动化凭据中的用户名和密码。 在 PowerShell ISE 中运行以下 cmdlet。

   ```powershell
   $Cred = Get-Credential
   #Using Azure Service Management
   Add-AzureAccount –Credential $Cred
   #Using Azure Resource Manager
   Connect-AzAccount –Credential $Cred
   ```

1. 如果无法在本地进行身份验证，则表示尚未正确设置 Azure Active Directory (Azure AD) 凭据。 若要正确设置 Azure AD 帐户，请参阅文章[使用 Azure Active Directory 对 Azure 进行身份验证](../automation-use-azure-ad.md)。

1. 如果该错误看上去是暂时性的，请尝试向身份验证例程添加重试逻辑，使身份验证更加可靠。

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

## <a name="scenario-run-login-azurermaccount-to-log-in"></a><a name="login-azurerm"></a>场景：运行 Login-AzureRMAccount 进行登录

### <a name="issue"></a>问题

运行 Runbook 时遇到以下错误：

```error
Run Login-AzureRMAccount to login.
```

### <a name="cause"></a>原因

如果使用的不是运行方式帐户或者运行方式帐户已过期，则可能会发生此错误。 有关详细信息，请参阅[管理 Azure 自动化运行方式帐户](../manage-runas-account.md)。

此错误有两个主要原因：

* 使用了不同版本的 AzureRM 或 Az 模块。
* 正在尝试访问单独订阅中的资源。

### <a name="resolution"></a>解决方法

如果在更新一个 AzureRM 或 Az 模块后遇到此错误，请将所有模块更新为同一版本。

如果你正在尝试访问其他订阅中的资源，请执行以下步骤来配置权限：

1. 转到自动化运行方式帐户，并复制“应用程序 ID”和“指纹”。 

    ![复制“应用程序 ID”和“指纹”](../media/troubleshoot-runbooks/collect-app-id.png)

1. 访问未托管自动化帐户的订阅的“访问控制”，并添加新的角色分配。

    ![访问控制](../media/troubleshoot-runbooks/access-control.png)

1. 添加前面收集的“应用程序 ID”。 选择“参与者”权限。

    ![添加角色分配](../media/troubleshoot-runbooks/add-role-assignment.png)

1. 复制该订阅的名称。

1. 现在，可以使用以下 Runbook 代码来测试从自动化帐户访问其他订阅的权限。 请将 `"\<CertificateThumbprint\>"` 替换为在步骤 1 中复制的值。 请将 `"\<SubscriptionName\>"` 替换为在步骤 4 中复制的值。

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

使用 `Select-AzureSubscription`、`Select-AzureRMSubscription` 或 `Select-AzSubscription` cmdlet 时遇到以下错误：

```error
The subscription named <subscription name> cannot be found.
```

### <a name="error"></a>错误

如果存在以下情况，则可能会发生此错误：

* 订阅名称无效。
* 尝试获取订阅详细信息的 Azure AD 用户未配置为订阅的管理员。
* cmdlet 不可用。

### <a name="resolution"></a>解决方法

执行以下步骤来确定是否已在 Azure 中完成身份验证并有权访问你尝试选择的订阅：

1. 为确保脚本可单独正常运行，请在 Azure 自动化外部对其进行测试。
1. 确保脚本先运行 [Connect-AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount?view=azps-3.7.0) cmdlet，再运行 `Select-*` cmdlet。
1. 将 `Disable-AzContextAutosave –Scope Process` 添加到 runbook 的开头。 此 cmdlet 可以确保任何凭据都仅适用于当前 runbook 的执行。
1. 如果仍看到该错误消息，请通过为 `Connect-AzAccount` 添加 `AzContext` 参数来修改代码，然后执行代码。

   ```powershell
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $context = Get-AzContext

   Get-AzVM -ResourceGroupName myResourceGroup -AzContext $context
    ```

## <a name="scenario-runbooks-fail-when-dealing-with-multiple-subscriptions"></a><a name="runbook-auth-failure"></a>场景：处理多个订阅时，Runbook 失败

### <a name="issue"></a>问题

执行 Runbook 时，Runbook 无法管理 Azure 资源。

### <a name="cause"></a>原因

Runbook 在运行时没有使用正确的上下文。

### <a name="resolution"></a>解决方法

当某个 Runbook 调用多个 Runbook 时，订阅上下文可能会丢失。 为确保将订阅上下文传递给 Runbook，请让客户端 Runbook 在 `AzureRmContext` 参数中向 `Start-AzureRmAutomationRunbook` cmdlet 传递上下文。 在将 `Scope` 参数设置为 `Process` 的情况下使用 `Disable-AzureRmContextAutosave` cmdlet 可确保指定的凭据仅用于当前 Runbook。 有关详细信息，请参阅[订阅](../automation-runbook-execution.md#subscriptions)。

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

## <a name="scenario-authentication-to-azure-fails-because-multifactor-authentication-is-enabled"></a><a name="auth-failed-mfa"></a>场景：无法在 Azure 中进行身份验证，因为已启用多重身份验证

### <a name="issue"></a>问题

使用 Azure 用户名和密码在 Azure 中进行身份验证时遇到以下错误：

```error
Add-AzureAccount: AADSTS50079: Strong authentication enrollment (proof-up) is required
```

### <a name="cause"></a>原因

如果对 Azure 帐户设置了多重身份验证，则不能使用 Azure Active Directory 用户在 Azure 中进行身份验证， 而必须使用证书或服务主体进行身份验证。

### <a name="resolution"></a>解决方法

若要将经典运行方式帐户用于 Azure 经典部署模型 cmdlet，请参阅[创建经典运行方式帐户以管理 azure 服务](../automation-create-standalone-account.md#create-a-classic-run-as-account)。 若要在 Azure 资源管理器 cmdlet 中使用服务主体，请参阅[使用 Azure 门户创建服务主体](../../active-directory/develop/howto-create-service-principal-portal.md)和[使用 Azure 资源管理器对服务主体进行身份验证](../../active-directory/develop/howto-authenticate-service-principal-powershell.md)。

## <a name="scenario-runbook-fails-with-a-task-was-canceled-error-message"></a><a name="task-was-cancelled"></a>场景：Runbook 失败并出现“任务已取消”错误消息

### <a name="issue"></a>问题

Runbook 失败，出现类似于以下示例的错误：

```error
Exception: A task was canceled.
```

### <a name="cause"></a>原因

使用过时的 Azure 模块会导致此错误。

### <a name="resolution"></a>解决方法

可以通过将 Azure 模块更新到最新版本来解决此错误：

1. 在自动化帐户中选择“模块”，然后选择“更新 Azure 模块”。 
1. 更新过程大约需要 15 分钟。 完成后，请重新运行失败的 Runbook。

若要了解有关更新模块的详细信息，请参阅[在 Azure 自动化中更新 Azure 模块](../automation-update-azure-modules.md)。

## <a name="scenario-term-not-recognized-as-the-name-of-a-cmdlet-function-or-script"></a><a name="not-recognized-as-cmdlet"></a>场景：字词未识别为 cmdlet、函数或脚本的名称

### <a name="issue"></a>问题

Runbook 失败，出现类似于以下示例的错误：

```error
The term 'Connect-AzAccount' is not recognized as the name of a cmdlet, function, script file, or operable program. Check the spelling of the name, or if the path was included verify that the path is correct and try again.
```

### <a name="cause"></a>原因

此错误可能是由以下原因造成的：

* 包含该 cmdlet 的模块未导入到自动化帐户中。
* 包含该 cmdlet 的模块已导入但已过时。

### <a name="resolution"></a>解决方法

执行以下任务之一来解决此错误：

* 对于 Azure 模块，请参阅[如何更新 Azure 自动化中的 Azure PowerShell 模块](../automation-update-azure-modules.md)，了解如何更新自动化帐户中的模块。
* 对于非 Azure 模块，请确保将模块导入到自动化帐户中。

## <a name="scenario-cmdlet-fails-in-pnp-powershell-runbook-on-azure-automation"></a>方案：对 Azure 自动化中的 PnP PowerShell Runbook 运行 Cmdlet 失败

### <a name="issue"></a>问题

当 Runbook 直接将 PnP PowerShell 生成的对象写入 Azure 自动化输出时，cmdlet 输出无法流式传输回自动化。

### <a name="cause"></a>原因

此问题的最常见原因是 Azure 自动化在未捕获返回对象的情况下处理调用 PnP PowerShell cmdlet（例如 `add-pnplistitem`）的 Runbook。

### <a name="resolution"></a>解决方法

编辑脚本以将任何返回值分配到变量，使 cmdlet 不会尝试将整个对象写入标准输出。 脚本可将输出流重定向到 cmdlet，如下所示。

```azurecli
  $null = add-pnplistitem
```

如果脚本分析 cmdlet 输出，该脚本必须将输出存储在某个变量中，然后操控变量，而不是仅流式传输输出。

```azurecli
$SomeVariable = add-pnplistitem ....
if ($SomeVariable.someproperty -eq ....
```

## <a name="scenario-cmdlet-not-recognized-when-executing-a-runbook"></a><a name="cmdlet-not-recognized"></a>场景：在执行 Runbook 时无法识别 cmdlet

### <a name="issue"></a>问题

Runbook 作业失败并显示错误：

```error
<cmdlet name>: The term <cmdlet name> is not recognized as the name of a cmdlet, function, script file, or operable program.
```

### <a name="cause"></a>原因

当 PowerShell 引擎找不到要在 runbook 中使用的 cmdlet 时，则会导致此错误。 可能的原因是帐户中缺少包含该 cmdlet 的模块、与 Runbook 名称存在名称冲突，或者该 cmdlet 也存在于其他模块中，而自动化无法解析该名称。

### <a name="resolution"></a>解决方法

使用以下任一解决方法来解决问题。

* 确保输入的 cmdlet 名称正确。
* 确保 cmdlet 存在于自动化帐户中，且没有冲突。 若要验证该 cmdlet 是否存在，请在编辑模式下打开一个 Runbook，并搜索希望在库中找到的 cmdlet，或者运行 `Get-Command <CommandName>`。 验证该 cmdlet 可供帐户使用且与其他 cmdlet 或 Runbook 不存在名称冲突以后，可将该 cmdlet 添加到画布。 确保使用的是 Runbook 中的有效参数集。
* 如果存在名称冲突且 cmdlet 可在两个不同的模块中使用，请使用 cmdlet 的完全限定名称来解决问题。 例如，可以使用 `ModuleName\CmdletName`。
* 如果在本地执行混合辅助角色组中的 Runbook，请确保模块和 cmdlet 已安装在托管混合辅助角色的计算机上。

## <a name="scenario-incorrect-object-reference-on-call-to-add-azaccount"></a><a name="object-reference-not-set"></a>场景：调用 Add-AzAccount 时对象引用不正确

### <a name="issue"></a>问题

使用 `Add-AzAccount`（`Connect-AzAccount` cmdlet 的别名）时遇到此错误：

```error
Add-AzAccount : Object reference not set to an instance of an object
```

### <a name="cause"></a>原因

如果在调用 `Add-AzAccount` 来添加自动化帐户之前 Runbook 未执行正确的步骤，则可能会发生此错误。 例如，必要的步骤之一是使用运行方式帐户登录。 有关要在 Runbook 中使用的正确操作，请参阅[在 Azure 自动化中执行 Runbook](../automation-runbook-execution.md)。

## <a name="scenario-object-reference-not-set-to-an-instance-of-an-object"></a><a name="child-runbook-object"></a>场景：对象引用未设置为某个对象的实例

### <a name="issue"></a>问题

结合 `Wait` 参数调用子 Runbook 并且输出流包含对象时遇到以下错误：

```error
Object reference not set to an instance of an object
```

### <a name="cause"></a>原因

如果流包含对象，`Start-AzAutomationRunbook` 不会正常处理输出流。

### <a name="resolution"></a>解决方法

实现轮询逻辑，并使用 [Get-AzAutomationJobOutput](/powershell/module/Az.Automation/Get-AzAutomationJobOutput?view=azps-3.7.0) cmdlet 检索输出。 下面定义了此逻辑的示例：

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
while($false -eq (IsJobTerminalState $job.Status) -and $waitTime -lt $maxTimeout) {
   Start-Sleep -Seconds $pollingSeconds
   $waitTime += $pollingSeconds
   $job = $job | Get-AzAutomationJob
}

$job | Get-AzAutomationJobOutput | Get-AzAutomationJobOutputRecord | Select-Object -ExpandProperty Value
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

使用以下任一解决方案来解决此问题：

* 如果将复杂对象从一个 cmdlet 传送到另一个 cmdlet，请将这些 cmdlet 包装在 `InlineScript` 活动中。
* 传递复杂对象中你所需要的名称或值，不必传递整个对象。
* 使用 PowerShell Runbook，而不使用 PowerShell 工作流 Runbook。

## <a name="scenario-400-bad-request-status-when-calling-a-webhook"></a><a name="expired webhook"></a>场景：调用 Webhook 时显示“400 错误的请求”状态

### <a name="issue"></a>问题

在尝试调用 Azure 自动化 Runbook 的 Webhook 时遇到以下错误：

```error
400 Bad Request : This webhook has expired or is disabled
```

### <a name="cause"></a>原因

尝试调用的 Webhook 已禁用，或者已过期。 

### <a name="resolution"></a>解决方法

如果 Webhook 处于禁用状态，可以通过 Azure 门户重新启用它。 如果 Webhook 已过期，必须将其删除，然后重新创建。 如果尚未过期，只能[续订 Webhook](../automation-webhooks.md#renew-a-webhook)。 

## <a name="scenario-429-the-request-rate-is-currently-too-large"></a><a name="429"></a>场景：429：当前的请求速率过大

### <a name="issue"></a>问题

在运行 `Get-AzAutomationJobOutput` cmdlet 时收到以下错误消息：

```error
429: The request rate is currently too large. Please try again
```

### <a name="cause"></a>原因

从包含多个[详细流](../automation-runbook-output-and-messages.md#monitor-verbose-stream)的 Runbook 中检索作业输出时，可能会发生此错误。

### <a name="resolution"></a>解决方法

执行以下操作之一来解决此错误：

* 编辑 Runbook，并减少它发出的作业流数量。
* 减少运行 cmdlet 时要检索的流数量。 为此，可以设置 [Get-AzAutomationJobOutput](/powershell/module/Az.Automation/Get-AzAutomationJobOutput?view=azps-3.7.0) cmdlet 的 `Stream` 参数值，以仅检索输出流。 

## <a name="scenario-runbook-job-fails-because-allocated-quota-was-exceeded"></a><a name="quota-exceeded"></a>场景：Runbook 作业因超过了分配的配额而失败

### <a name="issue"></a>问题

Runbook 作业失败并显示错误：

```error
The quota for the monthly total job run time has been reached for this subscription
```

### <a name="cause"></a>原因

作业执行时间超过帐户的 500 分钟免费配额时，就会出现此错误。 此配额适用于所有类型的作业执行任务。 其中一些任务是测试作业、从门户启动作业、使用 Webhook 执行作业，以及通过 Azure 门户或数据中心计划要执行的作业。 若要详细了解自动化的定价，请参阅[自动化定价](https://azure.microsoft.com/pricing/details/automation/)。

### <a name="resolution"></a>解决方法

如果想要每月使用 500 分钟以上的处理时间，请将订阅从“免费”层改为“基本”层：

1. 登录到 Azure 订阅。
1. 选择要升级的自动化帐户。
1. 选择“设置”，然后选择“定价”。 
1. 选择页面底部的“启用”，以将帐户升级到“基本”层。

## <a name="scenario-runbook-output-stream-greater-than-1-mb"></a><a name="output-stream-greater-1mb"></a>方案： Runbook 输出流大于 1 MB

### <a name="issue"></a>问题

在 Azure 沙盒中运行的 runbook 失败，并出现以下错误：

```error
The runbook job failed due to a job stream being larger than 1MB, this is the limit supported by an Azure Automation sandbox.
```

### <a name="cause"></a>原因

之所以发生此错误，是因为 runbook 试图将过多的异常数据写入输出流。

### <a name="resolution"></a>解决方法

作业输出流的限制为 1 MB。 确保 Runbook 使用 `try` 和 `catch` 块包含对可执行文件或子进程的调用。 如果操作引发异常，请让代码将该异常中的消息写入自动化变量中。 此方法可防止将消息写入作业输出流中。 对于执行的混合 Runbook 辅助角色作业，将显示截断为 1 MB 的输出流，且不显示错误消息。

## <a name="scenario-runbook-job-start-attempted-three-times-but-fails-to-start-each-time"></a><a name="job-attempted-3-times"></a>场景：已尝试启动 Runbook 作业三次，但每次都失败

### <a name="issue"></a>问题

Runbook 失败并出现以下错误：

```error
The job was tried three times but it failed
```

### <a name="cause"></a>原因

此错误是由以下问题之一造成的：

* **内存限制。** 如果作业使用的内存超过 400 MB，则它可能会失败。 [自动化服务限制](../../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)中阐述了分配给沙盒的内存限制。 

* **网络套接字。** Azure 沙盒限制为 1000 个并发网络套接字。 有关详细信息，请参阅[自动化服务限制](../../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)。

* **模块不兼容。** 模块依赖关系可能不正确。 在这种情况下，Runbook 通常会返回 `Command not found` 或 `Cannot bind parameter` 消息。

* **未为沙盒设置 Active Directory 身份验证。** Runbook 尝试调用在 Azure 沙盒中运行的可执行文件或子进程。 不支持将 Runbook 配置为使用 Azure Active Directory 身份验证库 (ADAL) 对 Azure AD 进行身份验证。

### <a name="resolution"></a>解决方法

* **内存限制，网络套接字。** 若要在内存限制内工作，建议的方法是在多个 Runbook 之间拆分工作负荷，在内存中处理更少的数据，避免从 Runbook 写入不必要的输出，并考虑将多少个检查点写入 PowerShell 工作流 Runbook。 使用 clear 方法（例如 `$myVar.clear`）清除变量并使用 `[GC]::Collect` 立即运行垃圾回收。 这将减少运行时期间 runbook 的内存占用情况。

* **模块不兼容。** 遵循[如何更新 Azure 自动化中的 Azure PowerShell 模块](../automation-update-azure-modules.md)中的步骤更新 Azure 模块。

* **未为沙盒设置 Active Directory 身份验证。** 使用 Runbook 向 Azure AD 进行身份验证时，请确保 Azure AD 模块在自动化帐户中可用。 确保为运行方式帐户授予所需的权限，使其能够执行 Runbook 自动执行的任务。

  如果 Runbook 无法调用 Azure 沙盒中运行的可执行文件或子进程，请在[混合 Runbook 辅助角色](../automation-hrw-run-runbooks.md)中使用 Runbook。 混合辅助角色不受内存和网络限制，而 Azure 沙盒则受限于此限制。

## <a name="scenario-powershell-job-fails-with-cannot-invoke-method-error-message"></a><a name="cannot-invoke-method"></a>场景：PowerShell 作业失败并出现“无法调用方法”错误消息

### <a name="issue"></a>问题

某个 Runbook 在 Azure 中运行，而你在该 Runbook 中启动 PowerShell 作业时收到以下错误消息：

```error
Exception was thrown - Cannot invoke method. Method invocation is supported only on core types in this language mode.
```

### <a name="cause"></a>原因

此错误可能表示在 Azure 沙盒中运行的 Runbook 无法在[“完整语言”模式](/powershell/module/microsoft.powershell.core/about/about_language_modes)下运行。

### <a name="resolution"></a>解决方法

可通过两种方法来解决此错误：

* 若要启动 Runbook，请不要使用 [Start-Job](/powershell/module/microsoft.powershell.core/start-job?view=powershell-7)，而要使用 [Start-AzAutomationRunbook](/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.7.0)。
* 尝试在混合 Runbook 辅助角色中运行 Runbook。

若要详细了解 Azure 自动化 Runbook 的此行为和其他行为，请参阅[在 Azure 自动化中执行 Runbook](../automation-runbook-execution.md)。

## <a name="scenario-a-long-running-runbook-fails-to-complete"></a><a name="long-running-runbook"></a>场景：长时间运行的 Runbook 无法完成

### <a name="issue"></a>问题

运行三小时后，Runbook 显示处于“已停止”状态。 此外，可能会遇到以下错误：

```error
The job was evicted and subsequently reached a Stopped state. The job cannot continue running.
```

此行为是 Azure 沙盒的设计使然，因为 Azure 自动化中会对进程进行[公平份额](../automation-runbook-execution.md#fair-share)监视。 如果进程的执行时间超过三小时，公平份额会自动停止 Runbook。 超过公平份额时间限制的 Runbook 的状态因 Runbook 类型而异。 PowerShell 和 Python Runbook 设置为“已停止”状态。 PowerShell 工作流 Runbook 设置为“失败”。

### <a name="cause"></a>原因

Runbook 运行时间超出了 Azure 沙盒中公平份额允许的三小时限制。

### <a name="resolution"></a>解决方法

建议的解决方案是在[混合 Runbook 辅助角色](../automation-hrw-run-runbooks.md)上运行 runbook。 混合辅助角色不受 Azure 沙盒的三小时公平份额 Runbook 限制。 应开发在混合 Runbook 辅助角色上运行的 Runbook，以便在出现意外的本地基础结构问题时支持重启行为。

另一种解决方案是通过创建[子 Runbook](../automation-child-runbooks.md) 来优化 Runbook。 如果 Runbook 在多个资源上循环访问同一函数，例如在多个数据库上执行某个数据库操作，可将该函数移到子 Runbook。 每个子 Runbook 在单独的进程中并行执行。 此行为降低了完成父 runbook 所需的时间总量。

启用子 runbook 方案的 PowerShell cmdlet 是：

* [Start-AzAutomationRunbook](/powershell/module/Az.Automation/Start-AzAutomationRunbook?view=azps-3.7.0)。 此 cmdlet 用于启动 Runbook 并将参数传递给该 Runbook。
* [Get-AzAutomationJob](/powershell/module/Az.Automation/Get-AzAutomationJob?view=azps-3.7.0)。 如果在子 Runbook 完成后需要执行操作，可使用此 cmdlet 检查每个子 Runbook 的作业状态。

## <a name="scenario-error-in-job-streams-about-the-get_serializationsettings-method"></a><a name="get-serializationsettings"></a>场景：作业流中出现有关 get_SerializationSettings 方法的错误

### <a name="issue"></a>问题

Runbook 的作业流中出现以下错误：

```error
Connect-AzAccount : Method 'get_SerializationSettings' in type
'Microsoft.Azure.Management.Internal.Resources.ResourceManagementClient' from assembly
'Microsoft.Azure.Commands.ResourceManager.Common, Version=4.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35'
does not have an implementation.
At line:16 char:1
+ Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -Appl ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : NotSpecified: (:) [Connect-AzAccount], TypeLoadException
    + FullyQualifiedErrorId : System.TypeLoadException,Microsoft.Azure.Commands.Profile.ConnectAzAccountCommand
```

### <a name="cause"></a>原因

此错误的可能原因是在 Runbook 中使用了从 AzureRM 到 Az 模块的不完整迁移。 这种情况可能导致 Azure 自动化仅使用 AzureRM 模块启动 Runbook 作业，然后仅使用 Az 模块启动另一个作业，从而导致沙盒崩溃。

### <a name="resolution"></a>解决方法

不建议在同一 Runbook 中使用 Az 和 AzureRM cmdlet。 若要详细了解模块的正确用法，请参阅[迁移到 Az 模块](../shared-resources/modules.md#migrate-to-az-modules)。

## <a name="scenario-access-denied-when-using-azure-sandbox-for-runbook-or-application"></a><a name="access-denied-azure-sandbox"></a>场景：对 Runbook 或应用程序使用 Azure 沙盒时出现“拒绝访问”

### <a name="issue"></a>问题

当 Runbook 或应用程序尝试在 Azure 沙盒中运行时，环境将拒绝访问。

### <a name="cause"></a>原因

之所以会出现此问题，是因为 Azure 沙盒会阻止访问所有的进程外 COM 服务器。 例如，沙盒应用程序或 Runbook 无法调用 Windows Management Instrumentation (WMI) 或 Windows Installer 服务 (msiserver.exe)。 

### <a name="resolution"></a>解决方法

若要详细了解如何使用 Azure 沙盒，请参阅 [Runbook 执行环境](../automation-runbook-execution.md#runbook-execution-environment)。

## <a name="scenario-invalid-forbidden-status-code-when-using-key-vault-inside-a-runbook"></a>方案：在 Runbook 中使用 Key Vault 时出现“无效且禁止”状态代码

### <a name="issue"></a>问题

尝试通过 Azure 自动化 Runbook 访问 Azure Key Vault 时遇到以下错误：

```error
Operation returned an invalid status code 'Forbidden'
```

### <a name="cause"></a>原因

此问题的可能原因包括：

* 未使用运行方式帐户。
* 权限不足。

### <a name="resolution"></a>解决方法

#### <a name="not-using-a-run-as-account"></a>未使用运行方式帐户

执行[步骤 5 - 添加身份验证来管理 Azure 资源](../learn/automation-tutorial-runbook-textual-powershell.md#step-5---add-authentication-to-manage-azure-resources)，以确保使用运行方式帐户访问 Key Vault。

#### <a name="insufficient-permissions"></a>权限不足

[将权限添加到 Key Vault](../manage-runas-account.md#add-permissions-to-key-vault)，以确保运行方式帐户拥有足够的权限，可以访问 Key Vault。

## <a name="recommended-documents"></a>建议的文档

* [在 Azure 自动化中执行 Runbook](../automation-runbook-execution.md)
* [在 Azure 自动化中启动 Runbook](../start-runbooks.md)

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者你无法解决自己的问题，请尝试通过以下渠道之一获取更多支持：

* 通过 [Azure 论坛](https://azure.microsoft.com/support/forums/)获取 Azure 专家的解答。
* 联系 [@AzureSupport](https://twitter.com/azuresupport)，这是用于改进客户体验的 Microsoft Azure 官方帐户。 Azure 支持人员会将你连接到 Azure 社区，从中可以获得解答、支持和专家建议。
* 如需更多帮助，可以提交 Azure 支持事件。 请转到 [Azure 支持站点](https://azure.microsoft.com/support/options/)并选择“获取支持”。
