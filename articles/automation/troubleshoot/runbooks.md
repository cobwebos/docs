---
title: Azure 自动化 Runbook 错误故障排除
description: 了解如何解决 Azure 自动化 Runbook 的问题
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 12/04/2018
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 41eb31ecabb20ec9eec3db13d5eda9f9cfbe6c69
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/07/2018
ms.locfileid: "53015460"
---
# <a name="troubleshoot-errors-with-runbooks"></a>Runbook 错误故障排除

## <a name="authentication-errors-when-working-with-azure-automation-runbooks"></a>使用 Azure 自动化 Runbook 时遇到的身份验证错误

### <a name="sign-in-failed"></a>场景：登录 Azure 帐户失败

#### <a name="issue"></a>问题

使用 `Add-AzureAccount` 或 `Connect-AzureRmAccount` cmdlet 时收到以下错误
:

```
Unknown_user_type: Unknown User Type
```

#### <a name="cause"></a>原因

如果凭据资产名称无效或者用于设置自动化凭据资产的用户名和密码无效，则会出现此错误。

#### <a name="resolution"></a>解决方法

要确定具体错误，请执行以下步骤：  

1. 确保在用于连接到 Azure 的自动化凭据资产名称中没有任何特殊字符，包括 @ 字符。  
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

```
The subscription named <subscription name> cannot be found.
```

#### <a name="error"></a>错误

如果订阅名称无效，或者尝试获取订阅详细信息的 Azure Active Directory 用户未配置为订阅的管理员，则会出现此错误。

#### <a name="resolution"></a>解决方法

要确定是否已正确向 Azure 进行身份验证并有权访问尝试选择的订阅，请执行以下步骤：  

1. 在 Azure 自动化之外测试脚本，以确保它独立运行。
2. 确保先运行 **Add-AzureAccount** cmdlet，再运行 **Select-AzureSubscription** cmdlet。  
3. 如果仍显示此错误消息，可通过在 **Add-AzureAccount** cmdlet 后添加 **-AzureRmContext** 参数来修改代码，并执行代码。

   ```powershell
   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID `
-ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $context = Get-AzureRmContext

   Get-AzureRmVM -ResourceGroupName myResourceGroup -AzureRmContext $context
    ```

### <a name="auth-failed-mfa"></a>场景：无法向 Azure 进行身份验证，因为已启用多重身份验证

#### <a name="issue"></a>问题

使用 Azure 用户名和密码向 Azure 进行身份验证时收到以下错误：

```
Add-AzureAccount: AADSTS50079: Strong authentication enrollment (proof-up) is required
```

#### <a name="cause"></a>原因

如果对 Azure 帐户设置了多重身份验证，则不能使用 Azure Active Directory 用户向 Azure 进行身份验证。 而只能使用证书或服务主体向 Azure 进行身份验证。

#### <a name="resolution"></a>解决方法

要将证书用于 Azure 经典部署模型 cmdlet，请参阅[创建并添加管理 Azure 服务所需的证书](https://blogs.technet.com/b/orchestrator/archive/2014/04/11/managing-azure-services-with-the-microsoft-azure-automation-preview-service.aspx)。 要将服务主体用于 Azure 资源管理器 cmdlet，请参阅[使用 Azure 门户创建服务主体](../../active-directory/develop/howto-create-service-principal-portal.md)和[通过 Azure 资源管理器对服务主体进行身份验证](../../active-directory/develop/howto-authenticate-service-principal-powershell.md)。

## <a name="common-errors-when-working-with-runbooks"></a>使用 Runbook 时的常见错误

### <a name="task-was-cancelled"></a>场景：Runbook 失败并显示错误：任务已取消

#### <a name="issue"></a>问题

Runbook 失败，出现类似于以下示例的错误：

```
Exception: A task was canceled.
```

#### <a name="cause"></a>原因

使用过时的 Azure 模块会导致此错误。

#### <a name="resolution"></a>解决方法

可以通过将 Azure 模块更新到最新版本来解决此错误。

在自动化帐户中，单击“模块”，然后单击“更新 Azure 模块”。 更新需要花费大约 15 分钟，完成后，重新运行失败的 runbook。 若要了解有关更新模块的详细信息，请参阅[在 Azure 自动化中更新 Azure 模块](../automation-update-azure-modules.md)。

### <a name="child-runbook-auth-failure"></a>场景：处理多个订阅时，子 Runbook 失败

#### <a name="issue"></a>问题

使用 `Start-AzureRmRunbook` 执行子 Runbook 时，子 Runbook 无法管理 Azure 资源。

#### <a name="cause"></a>原因

子 Runbook 在运行时没有使用正确的上下文。

#### <a name="resolution"></a>解决方法

如果使用多个订阅，则在调用子 Runbook 时可能会丢失订阅上下文。 若要确保将订阅上下文传递给子 Runbook，请将 `AzureRmContext` 参数添加到 cmdlet 并将上下文传递给它。

```azurepowershell-interactive
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

```
The term 'Connect-AzureRmAccount' is not recognized as the name of a cmdlet, function, script file, or operable program.  Check the spelling of the name, or if the path was included verify that the path is correct and try again.
```

#### <a name="cause"></a>原因

此错误可能由以下原因引起：

1. 包含该 cmdlet 的模块未导入到自动化帐户中
2. 包含该 cmdlet 的模块已导入但已过期

#### <a name="resolution"></a>解决方法

可通过完成以下任务之一来解决此错误：

如果该模块是 Azure 模块，请参阅[如何在 Azure 自动化中更新 Azure PowerShell 模块](../automation-update-azure-modules.md)，以了解如何更新自动化帐户中的模块。

如果它是一个单独的模块，请确保该模块已导入到自动化帐户中。

### <a name="job-attempted-3-times"></a>场景：Runbook 作业已尝试启动三次，但每次均启动失败

#### <a name="issue"></a>问题

Runbook 失败并显示错误：

```
The job was tried three times but it failed
```

#### <a name="cause"></a>原因

此错误可能由以下原因引起：

1. 内存限制。 [自动化服务限制](../../azure-subscription-service-limits.md#automation-limits)中规定了对可以分配给沙盒的内存量的限制，因此，如果使用超过 400 MB 的内存，作业可能会失败。

1. 网络套接字。 如[自动化服务限制](../../azure-subscription-service-limits.md#automation-limits)中所述，Azure 沙盒限制为 1000 个并发网络套接字。

1. 模块不兼容。 如果模块依赖关系不正确，则可能会发生此错误，并且如果它们不正确，则 runbook 通常会返回“找不到命令”或“无法绑定参数”消息。

#### <a name="resolution"></a>解决方法

下述解决方案中的任何一种都可以解决此问题：

* 在内存限制内工作的建议方法是拆分多个 runbook 之间的工作负载，不作为内存中的诸多数据进行处理，不写入不必要的 runbook 输出，或考虑在 PowerShell 工作流 runbook 中写入多少个检查点。 可以使用 clear 方法（例如 `$myVar.clear()`）清除变量并使用 `[GC]::Collect()` 立即运行垃圾收集，这将减少运行时期间 Runbook 的内存占用量。

* 按照[如何更新 Azure 自动化中的 Azure PowerShell 模块](../automation-update-azure-modules.md)中的步骤更新 Azure 模块。  

* 另一个解决方案是在[混合 Runbook 辅助角色](../automation-hrw-run-runbooks.md)上运行 runbook。 混合辅助角色不受内存和网络限制，而 Azure 沙盒则受限于此限制。

### <a name="fails-deserialized-object"></a>场景：Runbook 因反序列化的对象而失败

#### <a name="issue"></a>问题

Runbook 失败并显示错误：

```
Cannot bind parameter <ParameterName>.

Cannot convert the <ParameterType> value of type Deserialized <ParameterType> to type <ParameterType>.
```

#### <a name="cause"></a>原因

如果 runbook 为 PowerShell 工作流，它会将复杂对象以反序列化格式进行存储，以便在工作流暂停的情况下保留 runbook 状态。

#### <a name="resolution"></a>解决方法

下述三种解决方案中的任何一种都可以解决此问题：

1. 如果要将复杂对象从一个 cmdlet 传送到另一个 cmdlet，则可将这两个 cmdlet 包装在 InlineScript 中。
2. 传递复杂对象中你所需要的名称或值，不必传递整个对象。
3. 使用 PowerShell Runbook，而不使用 PowerShell 工作流 Runbook。

### <a name="quota-exceeded"></a>场景：Runbook 作业失败，因为超过了分配的配额

#### <a name="issue"></a>问题

Runbook 作业失败并显示错误：

```
The quota for the monthly total job run time has been reached for this subscription
```

#### <a name="cause"></a>原因

作业执行时间超过帐户的 500 分钟免费配额时，就会出现此错误。 此配额适用于所有类型的作业执行任务，例如测试作业、从门户启动作业、使用 Webhook 执行作业，以及通过 Azure 门户或数据中心计划要执行的作业。 若要详细了解自动化的定价，请参阅[自动化定价](https://azure.microsoft.com/pricing/details/automation/)。

#### <a name="resolution"></a>解决方法

如果想要每月使用 500 分钟以上的处理时间，则需将订阅从免费层改为基本层。 可以通过下述步骤升级到基本层：  

1. 登录到 Azure 订阅  
2. 选择要升级的自动化帐户  
3. 单击“设置” > “定价”。
4. 单击页面底部的“启用”，以将帐户升级到“基本”层。

### <a name="cmdlet-not-recognized"></a>场景：在执行 Runbook 时无法识别 cmdlet

#### <a name="issue"></a>问题

Runbook 作业失败并显示错误：

```
<cmdlet name>: The term <cmdlet name> is not recognized as the name of a cmdlet, function, script file, or operable program.
```

#### <a name="cause"></a>原因

当 PowerShell 引擎找不到要在 runbook 中使用的 cmdlet 时，则会导致此错误。 这可能是因为，帐户中缺少包含该 cmdlet 的模块、与 runbook 名称存在名称冲突，或者该 cmdlet 也存在于其他模块中，而自动化无法解析该名称。

#### <a name="resolution"></a>解决方法

下述解决方案中的任何一种都可以解决此问题：  

* 检查输入的 cmdlet 名称是否正确。  
* 确保 cmdlet 存在于自动化帐户中，且没有冲突。 要验证 cmdlet 是否存在，请在编辑模式下打开 Runbook，并搜索希望在库中找到的 cmdlet，或者运行 `Get-Command <CommandName>`。 验证该 cmdlet 可供帐户使用且与其他 cmdlet 或 runbook 不存在名称冲突以后，可将其添加到画布上，并确保使用的是 runbook 中的有效参数集。  
* 如果存在名称冲突且 cmdlet 可在两个不同的模块中使用，则可使用 cmdlet 的完全限定名称来解决此问题。 例如，可以使用 **ModuleName\CmdletName**。  
* 如果是在本地执行混合辅助角色组中的 runbook，则请确保模块和 cmdlet 已安装在托管混合辅助角色的计算机上。

### <a name="long-running-runbook"></a>场景：长时间运行的 Runbook 无法完成

#### <a name="issue"></a>问题

运行 3 小时后，runbook 将显示处于“已停止”状态。 可能还会收到错误：

```
The job was evicted and subsequently reached a Stopped state. The job cannot continue running
```

此行为是 Azure 沙盒的设计使然，因为 Azure 自动化中对进程的“公平份额”监视会自动停止执行时间超过 3 小时的 runbook。 超过公平份额时间限制的 runbook 的状态因 runbook 类型而异。 PowerShell 和 Python runbook 设置为“已停止”状态。 PowerShell 工作流 runbook 设置为“失败”。

#### <a name="cause"></a>原因

Runbook 超出了 Azure 沙盒中公平份额允许的 3 小时限制。

#### <a name="resolution"></a>解决方法

建议的解决方案是在[混合 Runbook 辅助角色](../automation-hrw-run-runbooks.md)上运行 runbook。

混合辅助角色不受[公平份额](../automation-runbook-execution.md#fair-share) 3 小时 runbook 限制，而 Azure 沙盒受限于此限制。 虽然混合 Runbook 辅助角色不受 3 小时公平份额限制的限制，但仍应开发在混合 Runbook 辅助角色上运行的 runbook，以便在出现意外的本地基础结构问题时支持重启行为。

另一种选择是通过创建[子 runbook](../automation-child-runbooks.md) 来优化 runbook。 如果 runbook 在多个资源上遍历同一函数，例如在多个数据库上执行某个数据库操作，可将该函数移到子 runbook。 每个这样的子 Runbook 都会在单独的进程中并行执行，缩短了父 Runbook 完成操作所需的总时间。

启用子 runbook 方案的 PowerShell cmdlet 是：

[Start-AzureRMAutomationRunbook](/powershell/module/AzureRM.Automation/Start-AzureRmAutomationRunbook) - 此 cmdlet 用于启动 runbook 并向其传递参数

[Get-AzureRmAutomationJob](/powershell/module/azurerm.automation/get-azurermautomationjob) - 在子 runbook 完成后，如果需要执行操作，可使用此 cmdlet 检查每个子 runbook 的作业状态。

### <a name="expired webhook"></a>场景：状态：请求时调用 Webhook 时显示 400 错误

#### <a name="issue"></a>问题

在尝试调用 Azure 自动化 Runbook 的 Webhook 时，收到以下错误。

```error
400 Bad Request : This webhook has expired or is disabled
```

#### <a name="cause"></a>原因

尝试调用的 Webhook 已禁用，或者已过期。

#### <a name="resolution"></a>解决方法

如果 Webhook 处于禁用状态，可以通过 Azure 门户重新启用 Webhook。 如果 Webhook 已过期，需要删除并重新创建 Webhook。 如果尚未过期，只能[续订 Webhook](../automation-webhooks.md#renew-webhook)。

### <a name="429"></a>场景：429：当前的请求速率过大。 请重试

#### <a name="issue"></a>问题

在运行 `Get-AzureRmAutomationJobOutput` cmdlet 时收到以下错误消息：

```
429: The request rate is currently too large. Please try again
```

#### <a name="cause"></a>原因

从具有多个[详细流](../automation-runbook-output-and-messages.md#verbose-stream)的 Runbook 中检索作业输出时，可能发生此错误。

#### <a name="resolution"></a>解决方法

可通过两种方法来解决此错误：

* 编辑 Runbook，并减少它发出的作业流数量。
* 减少运行 cmdlet 时要检索的流数量。 若要执行此操作，可以向 `Get-AzureRmAutomationJobOutput` cmdlet 指定 `-Stream Output` 参数以仅检索输出流。 

## <a name="common-errors-when-importing-modules"></a>导入模块时的常见错误

### <a name="module-fails-to-import"></a>场景：模块无法导入，或者 cmdlet 在导入后无法执行

#### <a name="issue"></a>问题

模块无法导入，或者虽然导入成功，但无法提取 cmdlet。

#### <a name="cause"></a>原因

模块无法成功导入到 Azure 自动化中的一些常见原因是：

* 结构与自动化所需的模块结构不符。
* 该模块依赖于其他模块，而后者尚未部署到自动化帐户。
* 该模块的文件夹中缺少依赖项。
* 使用了 `New-AzureRmAutomationModule` cmdlet 来上传该模块，但你尚未提供完整的存储路径，或者尚未使用可公开访问的 URL 来加载该模块。

#### <a name="resolution"></a>解决方法

下述解决方案中的任何一种都可以解决此问题：

* 请确保该模块遵循以下格式：ModuleName.Zip **->** 模块名称或版本号 **->** (ModuleName.psm1, ModuleName.psd1)
* 打开 .psd1 文件，看模块是否有任何依赖项。 如果有，则将这些模块上传到自动化帐户。
* 确保任何引用的 .dll 都存在于模块文件夹中。

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过 [Azure 论坛](https://azure.microsoft.com/support/forums/)获取 Azure 专家的解答
* 与 [@AzureSupport](https://twitter.com/azuresupport)（Microsoft Azure 官方帐户）联系，它可以将 Azure 社区引导至适当的资源来改进客户体验：提供解答、支持和专业化服务。
* 如需更多帮助，可以提交 Azure 支持事件。 请转到 [Azure 支持站点](https://azure.microsoft.com/support/options/)并选择 **获取支持**。