---
title: Azure 自动化 runbook 错误疑难解答
description: 了解如何排查和解决在使用 Azure 自动化 Runbook 时可能遇到的问题。
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/24/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.custom: has-adal-ref
ms.openlocfilehash: 70f3c52adc10556c358ed75a75fd023ffb21a813
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82855089"
---
# <a name="troubleshoot-runbook-errors"></a>Runbook 错误疑难解答

 本文介绍了可能发生的各种 runbook 错误以及如何解决这些错误。

>[!NOTE]
>本文进行了更新，以便使用新的 Azure PowerShell Az 模块。 你仍然可以使用 AzureRM 模块，至少在 2020 年 12 月之前，它将继续接收 bug 修补程序。 若要详细了解新的 Az 模块和 AzureRM 兼容性，请参阅[新 Azure Powershell Az 模块简介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 有关混合 Runbook 辅助角色上的 Az module 安装说明，请参阅[安装 Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 对于你的 Azure 自动化帐户，你可以通过使用[如何在 Azure 自动化中更新 Azure PowerShell 模块](../automation-update-azure-modules.md)，将模块更新到最新版本。

## <a name="diagnose-runbook-issues"></a>诊断 runbook 问题

当你在 Azure 自动化中的 runbook 执行过程中收到错误时，你可以使用以下步骤来帮助诊断问题：

1. 确保你的 runbook 脚本在本地计算机上成功执行。

    有关语言参考和学习模块，请参阅[PowerShell 文档](/powershell/scripting/overview)或[Python 文档](https://docs.python.org/3/)。在本地运行脚本可以发现和解决常见错误，如：

      * 缺少模块
      * 语法错误
      * 逻辑错误

1. 调查 runbook[错误流](../automation-runbook-output-and-messages.md#runbook-output)。

    查看这些流中的特定消息，并将其与本文中所述的错误进行比较。

1. 确保节点和自动化工作区具有所需的模块。

    如果你的 runbook 导入任何模块，请使用[导入模块](../shared-resources/modules.md#import-modules)中的步骤验证这些模块是否可用于你的自动化帐户。 按照[Azure 自动化中的更新 Azure PowerShell 模块](../automation-update-azure-modules.md)中的说明，将 PowerShell 模块更新到最新版本。 有关疑难解答的详细信息，请参阅[排查模块问题](shared-resources.md#modules)。

1. 如果 runbook 已挂起或意外失败：

    * 如果运行方式帐户已过期，请[续订证书](../manage-runas-account.md#cert-renewal)。
    * 如果尝试使用过期的 webhook 启动 runbook，请[续订 webhook](../automation-webhooks.md#renew-a-webhook) 。
    * [检查作业状态](../automation-runbook-execution.md#job-statuses)，以确定当前的 runbook 状态和问题的一些可能原因。
    * [将其他输出添加](../automation-runbook-output-and-messages.md#message-streams)到 runbook，以标识在挂起 runbook 之前会发生的情况。
    * 处理作业引发的[任何异常](../automation-runbook-execution.md#exceptions)。

1. 如果在混合 Runbook 辅助角色上的 runbook 作业或环境未响应，请执行此步骤。

    如果要在混合 Runbook 辅助角色上运行 runbook，而不是在 Azure 自动化中运行，则可能需要[排除混合辅助角色本身的故障](https://docs.microsoft.com/azure/automation/troubleshoot/hybrid-runbook-worker)。

## <a name="scenario-runbook-fails-with-a-no-permission-or-forbidden-403-error"></a><a name="runbook-fails-no-permission"></a>方案： Runbook 因 "无" 权限或 "禁止" 403 错误而失败

### <a name="issue"></a>问题

Runbook 将失败，并出现 "无" 权限或 "禁用" 403 错误或等效项。

### <a name="cause"></a>原因

运行方式帐户可能对 Azure 资源没有与当前自动化帐户相同的权限。 

### <a name="resolution"></a>解决方法

确保运行方式帐户有权访问脚本中使用的[任何资源](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)。

## <a name="scenario-sign-in-to-azure-account-failed"></a><a name="sign-in-failed"></a>方案：登录 Azure 帐户失败

### <a name="issue"></a>问题

使用`Connect-AzAccount` cmdlet 时，会收到以下错误之一：

```error
Unknown_user_type: Unknown User Type
```

```error
No certificate was found in the certificate store with thumbprint
```

### <a name="cause"></a>原因

如果凭据资产名称无效，则会出现这些错误。 如果用于设置自动化凭据资产的用户名和密码无效，则也可能出现这种情况。

### <a name="resolution"></a>解决方法

若要确定错误的内容，请执行以下步骤：

1. 请确保没有包含任何特殊字符。 这些字符包括用于连接 Azure 的自动化凭据资产名称中的  字符`\@`。
1. 查看是否可以在本地 PowerShell ISE 编辑器中使用存储在 Azure 自动化凭据中的用户名和密码。 在 PowerShell ISE 中运行以下 cmdlet。

   ```powershell
   $Cred = Get-Credential
   #Using Azure Service Management
   Add-AzureAccount –Credential $Cred
   #Using Azure Resource Manager
   Connect-AzAccount –Credential $Cred
   ```

1. 如果身份验证在本地失败，则尚未正确设置 Azure Active Directory （Azure AD）凭据。 若要正确设置 Azure AD 帐户，请参阅博客文章[使用 Azure Active Directory 对 Azure 进行身份验证](https://azure.microsoft.com/blog/azure-automation-authenticating-to-azure-using-azure-active-directory/)。

1. 如果错误看起来是暂时性的，请尝试将重试逻辑添加到身份验证例程，使身份验证更可靠。

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

## <a name="scenario-run-login-azurermaccount-to-log-in"></a><a name="login-azurerm"></a>方案：运行登录-Add-azurermaccount 以登录

### <a name="issue"></a>问题

运行 runbook 时，会收到以下错误：

```error
Run Login-AzureRMAccount to login.
```

### <a name="cause"></a>原因

如果你未使用运行方式帐户或运行方式帐户已过期，则会出现此错误。 有关详细信息，请参阅[管理 Azure 自动化运行方式帐户](https://docs.microsoft.com/azure/automation/manage-runas-account)。

此错误有两个主要原因：

* AzureRM 或 Az 模块有不同的版本。
* 你正在尝试访问单独订阅中的资源。

### <a name="resolution"></a>解决方法

如果在更新一个 AzureRM 或 Az 模块后收到此错误，请将所有模块更新为同一版本。

如果要尝试访问其他订阅中的资源，请按照以下步骤配置权限：

1. 中转到自动化运行方式帐户，然后复制**应用程序 ID**和**指纹**。

    ![复制应用程序 ID 和指纹](../media/troubleshoot-runbooks/collect-app-id.png)

1. 访问*不*承载自动化帐户的订阅的**访问控制**，并添加新的角色分配。

    ![访问控制](../media/troubleshoot-runbooks/access-control.png)

1. 添加先前收集的**应用程序 ID** 。 选择 "**参与者**权限"。

    ![添加角色分配](../media/troubleshoot-runbooks/add-role-assignment.png)

1. 复制订阅的名称。

1. 你现在可以使用以下 runbook 代码来测试从自动化帐户到其他订阅的权限。 替换`"\<CertificateThumbprint\>"`为步骤1中复制的值。 替换`"\<SubscriptionName\>"`为在步骤4中复制的值。

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

当使用`Select-AzureSubscription`、 `Select-AzureRMSubscription`或`Select-AzSubscription` cmdlet 时，会收到以下错误：

```error
The subscription named <subscription name> cannot be found.
```

### <a name="error"></a>错误

以下情况下可能发生此错误：

* 订阅名称无效。
* 尝试获取订阅详细信息的 Azure AD 用户未配置为订阅的管理员。
* Cmdlet 不可用。

### <a name="resolution"></a>解决方法

按照以下步骤确定是否已通过 Azure 身份验证，并有权访问要尝试选择的订阅：

1. 若要确保你的脚本独立工作，请在 Azure 自动化之外进行测试。
1. 在运行`Select-*` cmdlet 之前，请确保脚本运行[AzAccount](https://docs.microsoft.com/powershell/module/Az.Accounts/Connect-AzAccount?view=azps-3.7.0) cmdlet。
1. 将 `Disable-AzContextAutosave –Scope Process` 添加到 runbook 的开头。 此 cmdlet 可以确保任何凭据都仅适用于当前 runbook 的执行。
1. 如果仍然看到错误消息，请通过为添加`AzContext`参数来`Connect-AzAccount`修改代码，然后执行代码。

   ```powershell
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $context = Get-AzContext

   Get-AzVM -ResourceGroupName myResourceGroup -AzContext $context
    ```

## <a name="scenario-runbooks-fail-when-dealing-with-multiple-subscriptions"></a><a name="runbook-auth-failure"></a>方案： Runbook 在处理多个订阅时失败

### <a name="issue"></a>问题

执行 runbook 时，runbook 无法管理 Azure 资源。

### <a name="cause"></a>原因

Runbook 在运行时没有使用正确的上下文。

### <a name="resolution"></a>解决方法

当 runbook 调用多个 runbook 时，订阅上下文可能会丢失。 若要确保将订阅上下文传递给 runbook，请将客户端 runbook 的上下文传递给`Start-AzureRmAutomationRunbook` `AzureRmContext`参数中的 cmdlet。 使用`Disable-AzureRmContextAutosave` cmdlet，并将`Scope`参数设置为`Process` ，以确保指定的凭据仅用于当前 runbook。 有关详细信息，请参阅[订阅](../automation-runbook-execution.md#subscriptions)。

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

## <a name="scenario-authentication-to-azure-fails-because-multifactor-authentication-is-enabled"></a><a name="auth-failed-mfa"></a>方案：对 Azure 进行身份验证失败，因为启用了多重身份验证

### <a name="issue"></a>问题

用 Azure 用户名和密码对 Azure 进行身份验证时，会收到以下错误：

```error
Add-AzureAccount: AADSTS50079: Strong authentication enrollment (proof-up) is required
```

### <a name="cause"></a>原因

如果在 Azure 帐户上进行多重身份验证，则无法使用 Azure Active Directory 用户向 Azure 进行身份验证。 相反，你需要使用证书或服务主体进行身份验证。

### <a name="resolution"></a>解决方法

若要将证书用于 Azure 经典部署模型 cmdlet，请参阅[创建和添加用于管理 azure 服务的证书](https://blogs.technet.com/b/orchestrator/archive/2014/04/11/managing-azure-services-with-the-microsoft-azure-automation-preview-service.aspx)。 若要将服务主体用于 Azure 资源管理器 cmdlet，请参阅[使用 Azure 门户创建服务主体](../../active-directory/develop/howto-create-service-principal-portal.md)和使用[azure 资源管理器对服务主体进行身份验证](../../active-directory/develop/howto-authenticate-service-principal-powershell.md)。

## <a name="scenario-runbook-fails-with-a-task-was-canceled-error-message"></a><a name="task-was-cancelled"></a>方案： Runbook 失败，出现 "已取消任务" 错误消息

### <a name="issue"></a>问题

Runbook 失败，出现类似于以下示例的错误：

```error
Exception: A task was canceled.
```

### <a name="cause"></a>原因

使用过时的 Azure 模块会导致此错误。

### <a name="resolution"></a>解决方法

可以通过将 Azure 模块更新到最新版本来解决此错误：

1. 在自动化帐户中，选择 "**模块**"，然后选择 "**更新 Azure 模块**"。
1. 此更新大约需15分钟。 完成后，重新运行失败的 runbook。

若要了解有关更新模块的详细信息，请参阅[在 Azure 自动化中更新 azure 模块](../automation-update-azure-modules.md)。

## <a name="scenario-term-not-recognized-as-the-name-of-a-cmdlet-function-or-script"></a><a name="not-recognized-as-cmdlet"></a>方案：字词未被识别为 cmdlet、函数或脚本的名称

### <a name="issue"></a>问题

Runbook 失败，出现类似于以下示例的错误：

```error
The term 'Connect-AzAccount' is not recognized as the name of a cmdlet, function, script file, or operable program. Check the spelling of the name, or if the path was included verify that the path is correct and try again.
```

### <a name="cause"></a>原因

发生此错误的原因如下：

* 包含 cmdlet 的模块不会导入到自动化帐户中。
* 包含 cmdlet 的模块已导入，但已过时。

### <a name="resolution"></a>解决方法

若要解决此错误，请执行以下任务之一：

* 有关 Azure 模块，请参阅[如何更新 Azure 自动化中的 Azure PowerShell 模块](../automation-update-azure-modules.md)，了解如何更新自动化帐户中的模块。
* 对于非 Azure 模块，请确保将模块导入到自动化帐户中。

## <a name="scenario-cmdlet-fails-in-pnp-powershell-runbook-on-azure-automation"></a>方案： Azure Automation 上的 PnP PowerShell runbook 中的 Cmdlet 失败

### <a name="issue"></a>问题

当 runbook 直接将 PnP PowerShell 生成的对象写入 Azure 自动化输出时，cmdlet 输出无法流式传输到自动化。

### <a name="cause"></a>原因

此问题最常见的`add-pnplistitem`原因是 Azure 自动化处理调用 PnP PowerShell cmdlet 的 runbook，而无需捕获返回对象。

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

## <a name="scenario-cmdlet-not-recognized-when-executing-a-runbook"></a><a name="cmdlet-not-recognized"></a>场景：在执行 Runbook 时无法识别 Cmdlet

### <a name="issue"></a>问题

Runbook 作业失败并显示错误：

```error
<cmdlet name>: The term <cmdlet name> is not recognized as the name of a cmdlet, function, script file, or operable program.
```

### <a name="cause"></a>原因

当 PowerShell 引擎找不到要在 runbook 中使用的 cmdlet 时，则会导致此错误。 帐户中可能缺少包含该 cmdlet 的模块、与 runbook 名称存在名称冲突，或者该 cmdlet 也存在于其他模块中，而自动化无法解析该名称。

### <a name="resolution"></a>解决方法

使用以下任一解决方案来解决该问题：

* 请确保已正确输入 cmdlet 名称。
* 确保该 cmdlet 存在于自动化帐户中，并且不存在冲突。 若要验证 cmdlet 是否存在，请在编辑模式下打开 runbook，然后搜索要在库中查找的 cmdlet，或者运行`Get-Command <CommandName>`。 验证该 cmdlet 可供帐户使用且与其他 cmdlet 或 runbook 不存在名称冲突时，请将该 cmdlet 添加到画布中。 请确保使用的是 runbook 中的有效参数集。
* 如果确实存在名称冲突且 cmdlet 可在两个不同的模块中使用，请使用 cmdlet 的完全限定名称来解决此问题。 例如，你可以使用 `ModuleName\CmdletName`。
* 如果要在混合辅助角色组中本地执行 runbook，请确保在承载混合辅助角色的计算机上安装了该模块和 cmdlet。

## <a name="scenario-incorrect-object-reference-on-call-to-add-azaccount"></a><a name="object-reference-not-set"></a>方案：对 AzAccount 的调用上的对象引用不正确

### <a name="issue"></a>问题

使用`Add-AzAccount`时，会收到此错误，这是`Connect-AzAccount` cmdlet 的别名：

```error
Add-AzAccount : Object reference not set to an instance of an object
```

### <a name="cause"></a>原因

如果 runbook 在调用`Add-AzAccount`以添加自动化帐户之前未执行正确的步骤，则会发生此错误。 使用运行方式帐户登录的一个必需步骤示例。 若要在 runbook 中使用正确的操作，请参阅[在 Azure 自动化中执行 runbook](https://docs.microsoft.com/azure/automation/automation-runbook-execution)。

## <a name="scenario-object-reference-not-set-to-an-instance-of-an-object"></a><a name="child-runbook-object"></a>方案：对象引用未设置为对象的实例

### <a name="issue"></a>问题

当使用`Wait`参数调用子 Runbook 并且输出流包含对象时，会收到以下错误：

```error
Object reference not set to an instance of an object
```

### <a name="cause"></a>原因

如果流包含对象， `Start-AzAutomationRunbook`则不会正确处理输出流。

### <a name="resolution"></a>解决方法

实现轮询逻辑，并使用[AzAutomationJobOutput](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJobOutput?view=azps-3.7.0) cmdlet 来检索输出。 此逻辑的示例定义如下：

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

使用以下任一解决方案来解决此问题：

* 如果要将复杂对象从一个 cmdlet 传送到另一个 cmdlet，请将`InlineScript`这些 cmdlet 包装在活动中。
* 传递复杂对象中你所需要的名称或值，不必传递整个对象。
* 使用 PowerShell Runbook，而不使用 PowerShell 工作流 Runbook。



## <a name="scenario-400-bad-request-status-when-calling-a-webhook"></a><a name="expired webhook"></a>方案：调用 webhook 时出现400请求状态错误

### <a name="issue"></a>问题

尝试调用 Azure 自动化 runbook 的 webhook 时，会收到以下错误：

```error
400 Bad Request : This webhook has expired or is disabled
```

### <a name="cause"></a>原因

尝试调用的 Webhook 已禁用，或者已过期。 

### <a name="resolution"></a>解决方法

如果 webhook 处于禁用状态，则可以通过 Azure 门户重新启用它。 如果 webhook 过期，则必须删除然后重新创建它。 如果尚未过期，只能[续订 Webhook](../automation-webhooks.md#renew-a-webhook)。 

## <a name="scenario-429-the-request-rate-is-currently-too-large"></a><a name="429"></a>方案：429：请求速率目前太大

### <a name="issue"></a>问题

在运行 `Get-AzAutomationJobOutput` cmdlet 时收到以下错误消息：

```error
429: The request rate is currently too large. Please try again
```

### <a name="cause"></a>原因

从具有多个[详细流](../automation-runbook-output-and-messages.md#verbose-stream)的 runbook 检索作业输出时可能会发生此错误。

### <a name="resolution"></a>解决方法

若要解决此错误，请执行以下操作之一：

* 编辑 Runbook，并减少它发出的作业流数量。
* 减少运行 cmdlet 时要检索的流数量。 为此，可以将`Stream` [AzAutomationJobOutput](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJobOutput?view=azps-3.7.0) cmdlet 的参数值设置为仅检索输出流。 

## <a name="scenario-runbook-job-fails-because-allocated-quota-was-exceeded"></a><a name="quota-exceeded"></a>方案： Runbook 作业失败，因为超过了分配的配额

### <a name="issue"></a>问题

Runbook 作业失败并显示错误：

```error
The quota for the monthly total job run time has been reached for this subscription
```

### <a name="cause"></a>原因

作业执行时间超过帐户的 500 分钟免费配额时，就会出现此错误。 此配额适用于所有类型的作业执行任务。 其中一些任务是测试作业、从门户启动作业、使用 webhook 执行作业，或使用 Azure 门户或数据中心计划要执行的作业。 若要详细了解自动化的定价，请参阅[自动化定价](https://azure.microsoft.com/pricing/details/automation/)。

### <a name="resolution"></a>解决方法

如果希望每月使用超过500分钟的处理，请将免费级别的订阅更改为 "基本" 层：

1. 登录到 Azure 订阅。
1. 选择要升级的自动化帐户。
1. 选择 "**设置**"，然后选择 "**定价**"。
1. 选择页面底部的 "**启用**"，将你的帐户升级到 "基本" 层。

## <a name="scenario-runbook-job-start-attempted-three-times-but-fails-to-start-each-time"></a><a name="job-attempted-3-times"></a>方案： Runbook 作业开始尝试三次，但每次都无法启动

### <a name="issue"></a>问题

Runbook 失败，并出现以下错误：

```error
The job was tried three times but it failed
```

### <a name="cause"></a>原因

发生此错误的原因是以下问题之一：

* **内存限制。** 如果作业使用超过 400 MB 的内存，作业可能会失败。 在[自动化服务限制](../../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)中可以找到已记录的分配给沙盒的内存限制。 
* **网络套接字。** Azure 沙箱限制为1000并发网络套接字。 有关详细信息，请参阅[自动化服务限制](../../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)。
* **模块不兼容。** 模块依赖项可能不正确。 在这种情况下，runbook 通常会`Command not found`返回`Cannot bind parameter`或消息。
* **没有用于沙盒 Active Directory 的身份验证。** Runbook 尝试调用在 Azure 沙盒中运行的可执行文件或子进程。 不支持使用 Azure Active Directory 身份验证库（ADAL）将 runbook 配置为使用 Azure AD 进行身份验证。
* **异常数据太多。** runbook 尝试向输出流写入太多异常数据。

### <a name="resolution"></a>解决方法

* **内存限制，网络套接字。** 在内存限制内工作的建议方法是在多个 runbook 之间拆分工作负荷，在内存中处理较少的数据，避免从 runbook 写入不必要的输出，并考虑将多少个检查点写入 PowerShell 工作流 runbook。 使用 clear 方法（如`$myVar.clear`）清除变量并使用`[GC]::Collect`立即运行垃圾回收。 这将减少运行时期间 runbook 的内存占用情况。
* **模块不兼容。** 按照[如何更新 Azure 自动化中的 Azure PowerShell 模块](../automation-update-azure-modules.md)中的步骤更新 azure 模块。
* **没有用于沙盒 Active Directory 的身份验证。** 当你使用 runbook Azure AD 进行身份验证时，请确保 Azure AD 模块在你的自动化帐户中可用。 请确保授予运行方式帐户执行 runbook 自动执行的任务所需的权限。

  如果你的 runbook 无法调用在 Azure 沙盒中运行的可执行文件或子进程，请在[混合 Runbook 辅助角色](../automation-hrw-run-runbooks.md)上使用 runbook。 混合辅助角色不受 Azure 沙箱具有的内存和网络限制的限制。

* **异常数据太多。** 作业输出流的限制为 1 MB。 确保 runbook 使用`try`和`catch`阻止对可执行文件或子进程的调用。 如果操作引发异常，请使代码将异常消息写入自动化变量中。 此方法可防止将消息写入作业输出流中。

## <a name="scenario-powershell-job-fails-with-cannot-invoke-method-error-message"></a><a name="cannot-invoke-method"></a>方案： PowerShell 作业失败，出现 "无法调用方法" 错误消息

### <a name="issue"></a>问题

当你在 Azure 中运行的 runbook 中启动 PowerShell 作业时，会收到以下错误消息：

```error
Exception was thrown - Cannot invoke method. Method invocation is supported only on core types in this language mode.
```

### <a name="cause"></a>原因

此错误可能表示在 Azure 沙盒中运行的 runbook 无法在[完整语言模式下](/powershell/module/microsoft.powershell.core/about/about_language_modes)运行。

### <a name="resolution"></a>解决方法

可通过两种方法来解决此错误：

* 使用[AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.7.0)启动 runbook，而不是使用[启动作业](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/start-job?view=powershell-7)。
* 尝试在混合 Runbook 辅助角色上运行 runbook。

若要了解有关此行为以及 Azure 自动化 runbook 的其他行为的详细信息，请参阅[在 Azure 自动化中执行 Runbook](../automation-runbook-execution.md)。

## <a name="scenario-a-long-running-runbook-fails-to-complete"></a><a name="long-running-runbook"></a>方案：长时间运行的 runbook 无法完成

### <a name="issue"></a>问题

Runbook 在运行3小时后显示为停止状态。 还可能会收到此错误：

```error
The job was evicted and subsequently reached a Stopped state. The job cannot continue running.
```

此行为是在 Azure 沙箱中设计的，因为在 Azure 自动化中对进程进行[公平共享](../automation-runbook-execution.md#fair-share)监视。 如果某个进程的执行时间超过3小时，则公平共享会自动停止 runbook。 超过公平共享时间限制的 runbook 状态因 runbook 类型而异。 PowerShell 和 Python runbook 设置为“已停止”状态。 PowerShell 工作流 runbook 设置为“失败”。

### <a name="cause"></a>原因

Runbook 在 Azure 沙盒中的公平共享允许的三小时限制内运行。

### <a name="resolution"></a>解决方法

建议的解决方案是在[混合 Runbook 辅助角色](../automation-hrw-run-runbooks.md)上运行 runbook。 混合辅助角色不受 Azure 沙盒使用的三小时公平共享 runbook 限制的限制。 应开发在混合 Runbook 辅助角色上运行的 runbook，以便在出现意外的本地基础结构问题时支持重启行为。

另一种解决方案是通过创建[子 runbook](../automation-child-runbooks.md)来优化 runbook。 如果 runbook 在多个资源上循环遍历相同的函数（例如，在多个数据库的数据库操作中），则可以将函数移动到子 runbook。 每个子 runbook 并行在单独的进程中执行。 此行为降低了完成父 runbook 所需的时间总量。

启用子 runbook 方案的 PowerShell cmdlet 是：

* [AzAutomationRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Start-AzAutomationRunbook?view=azps-3.7.0)。 此 cmdlet 用于启动 Runbook 并将参数传递给该 Runbook。
* [AzAutomationJob](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJob?view=azps-3.7.0)。 如果在子 runbook 完成后需要执行某些操作，则此 cmdlet 允许您检查每个子 runbook 的作业状态。

## <a name="scenario-error-in-job-streams-about-the-get_serializationsettings-method"></a><a name="get-serializationsettings"></a>方案：有关 get_SerializationSettings 方法的作业流中的错误

### <a name="issue"></a>问题

Runbook 的作业流中会出现以下错误：

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

此错误的原因可能是在 runbook 中使用 AzureRM 到 Az 模块的不完整迁移。 这种情况可能会导致 Azure 自动化仅使用 AzureRM 模块启动 runbook 作业，然后仅使用 Az 模块启动另一作业，从而导致沙盒崩溃。

### <a name="resolution"></a>解决方法

不建议在同一 runbook 中使用 Az 和 AzureRM cmdlet。 若要了解有关正确使用这些模块的详细信息，请参阅[迁移到 Az 模块](../shared-resources/modules.md#migrating-to-az-modules)。

## <a name="scenario-access-denied-when-using-azure-sandbox-for-runbook-or-application"></a><a name="access-denied-azure-sandbox"></a>方案：使用 Azure 沙箱 for runbook 或应用程序时访问被拒绝

### <a name="issue"></a>问题

当 runbook 或应用程序尝试在 Azure 沙盒中运行时，环境将拒绝访问。

### <a name="cause"></a>原因

之所以会出现此问题，是因为 Azure 沙箱禁止访问所有进程外 COM 服务器。 例如，沙盒应用程序或 runbook 无法调入 Windows Management Instrumentation （WMI）或 Windows Installer 服务（msiserver）。 

### <a name="resolution"></a>解决方法

有关使用 Azure 沙盒的详细信息，请参阅[Runbook 执行环境](../automation-runbook-execution.md#runbook-execution-environment)。

## <a name="scenario-invalid-forbidden-status-code-when-using-key-vault-inside-a-runbook"></a>方案：使用 runbook 中的 Key Vault 时禁止的状态代码无效

### <a name="issue"></a>问题

尝试通过 Azure 自动化 runbook 访问 Azure Key Vault 时，会收到以下错误：

```error
Operation returned an invalid status code 'Forbidden'
```

### <a name="cause"></a>原因

此问题的可能原因包括：

* 不使用运行方式帐户。
* 权限不足。

### <a name="resolution"></a>解决方法

#### <a name="not-using-a-run-as-account"></a>不使用运行方式帐户

按照[步骤 5-添加身份验证来管理 Azure 资源](https://docs.microsoft.com/azure/automation/automation-first-runbook-textual-powershell#add-authentication-to-manage-azure-resources)，以确保使用运行方式帐户访问 Key Vault。

#### <a name="insufficient-permissions"></a>权限不足

[将权限添加到 Key Vault](https://docs.microsoft.com/azure/automation/manage-runas-account#add-permissions-to-key-vault) ，以确保运行方式帐户有足够的权限访问 Key Vault。

## <a name="recommended-documents"></a>建议的文档

* [在 Azure 自动化中执行 Runbook](../automation-runbook-execution.md)
* [在 Azure 自动化中启动 Runbook](https://docs.microsoft.com/azure/automation/automation-starting-a-runbook)

## <a name="next-steps"></a>后续步骤

如果在此处看不到你的问题，或者无法解决你的问题，请尝试以下通道之一以获得更多支持：

* 通过[Azure 论坛](https://azure.microsoft.com/support/forums/)获取 azure 专家的解答。
* 与[@AzureSupport](https://twitter.com/azuresupport)官方 Microsoft Azure 帐户联系，以改善客户体验。 Azure 支持人员会将你连接到 Azure 社区，以获得答案、支持和专家。
* 如需更多帮助，可以提交 Azure 支持事件。 转到[Azure 支持站点](https://azure.microsoft.com/support/options/)并选择 "**获取支持**"。
