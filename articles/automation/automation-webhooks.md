---
title: "Azure 自动化 Webhook | Microsoft Docs"
description: "一个可供客户端通过 HTTP 调用在 Azure 自动化中启动 Runbook 的 Webhook。  本文介绍了如何创建 Webhook，以及如何通过调用 Webhook 来启动 Runbook。"
services: automation
documentationcenter: 
author: mgoedtel
manager: jwhit
editor: tysonn
ms.assetid: 9b20237c-a593-4299-bbdc-35c47ee9e55d
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/12/2016
ms.author: magoedte;bwren;sngun
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 155d89ed6aed0afd2780a017c221bd807ca61ba2


---
# <a name="azure-automation-webhooks"></a>Azure 自动化 Webhook
Webhook 可以用来在 Azure 自动化中通过单个 HTTP 请求来启动特定的 Runbook。 这样，外部服务（例如 Visual Studio Team Services、GitHub 或自定义应用程序）就可以在不通过 Azure 自动化 API 实现完整解决方案的情况下启动 Runbook。  
![WebhooksOverview](media/automation-webhooks/webhook-overview-image.png)

你可以将 Webhook 与[在 Azure 自动化中启动 Runbook](automation-starting-a-runbook.md) 中其他启动 Runbook 的方法进行比较

## <a name="details-of-a-webhook"></a>Webhook 详细信息
下表介绍了你必须为 Webhook 配置的属性。

| 属性 | 说明 |
|:--- |:--- |
| Name |你可以提供要用于 Webhook 的任何名称，因为该名称不会公开给客户端。  它只供你用来标识 Azure 自动化中的 Runbook。 <br>  最好是为 Webhook 提供一个与名称，该名称需要与将要使用的客户端相关。 |
| 代码 |Webhook 的 URL 是客户端通过 HTTP POST 来调用的唯一地址，用于启动链接到 Webhook 的 Runbook。  它是在你创建 Webhook 时自动生成的。  不能指定自定义 URL。 <br> <br>  URL 包含一个允许第三方系统调用 Runbook 的安全令牌，不需要进一步进行身份验证。 因此，应将其视为密码。  出于安全原因，你只能在创建 Webhook 时通过 Azure 门户查看该 URL。 你应该将保存在安全位置的 URL 记下来，供将来使用。 |
| 到期日期 |与证书一样，每个 Webhook 都有一个过期日期，到了过期日期 Webhook 将不再可用。  此到期日期在 Webhook 创建后将无法更改，而在到达到期日期以后，Webhook 也将无法再次启用。  在这种情况下，你必须创建另一个 Webhook 来替换当前的 Webhook，并对客户端进行更新，使之能够使用新的 Webhook。 |
| Enabled |Webhook 在创建后已按默认启用。  如果将 Runbook 设置为“Disabled”，将没有客户端能够使用它。  可以在创建 Webhook 时设置 **Enabled** 属性，也可以在创建后随时设置它。 |

### <a name="parameters"></a>parameters
Webhook 可以定义 Runbook 参数的值，当该 Webhook 启动 Runbook 时会用到这些值。 Webhook 必须包含 Runbook 的任何必需参数的值，可以包含可选参数的值。 即使在创建 Webhoook 后，也可以修改配置给 Webhook 的参数值。 链接到单个 Runbook 的多个 Webhook 可以使用不同的参数值。

客户端在使用 Webhook 启动 Runbook 时，不能重写在 Webhook 中定义的参数值。  为了从客户端接收数据，Runbook 可能会接受名为 **$WebhookData** 且类型为 [object] 的单个参数，该参数会包含客户端包括在 POST 请求中的数据。

![Webhookdata 属性](media/automation-webhooks/webhook-data-properties.png)

**$WebhookData** 对象将具有以下属性：

| 属性 | 说明 |
|:--- |:--- |
| WebhookName |Webhook 的名称。 |
| RequestHeader |包含传入 POST 请求标头的哈希表。 |
| RequestBody |传入 POST 请求的正文。  用于保留任何格式，如字符串、JSON、XML，或窗体编码的数据。 编写的 Runbook 必须能够与预期的数据格式配合工作。 |

不需配置 Webhook 即可支持 **$WebhookData** 参数，也不需要 Runbook 来接受它。  如果 Runbook 没有定义该参数，则会忽略从客户端发送的请求的任何详细信息。

如果你在创建 Webhook 时为 $WebhookData 指定了值，则会在 Webhook 使用客户端 POST 请求中的数据启动 Runbook 时重写该值，即使该客户端的请求正文中不包含任何数据。  如果你使用 Webhook 以外的方法启动包含 $WebhookData 的 Runbook，则可为能够被 Runbook 识别的 $WebhookData 提供值。  此值应该是与 $Webhookdata 具有相同[属性](#details-of-a-webhook)的对象，这样 Runbook 就可以正常地使用它，如同使用 Webhook 所传递的实际 WebhookData 一样。

例如，如果你要从 Azure 门户启动以下 Runbook，并想要传递一些 WebhookData 示例进行测试。由于 WebhookData 为对象，因此应以 UI 中的 JSON 进行传递。

![UI 中的 WebhookData 参数](media/automation-webhooks/WebhookData-parameter-from-UI.png)

针对上述 Runbook，如果 WebhookData 参数具有以下属性：

1. WebhookName：MyWebhook
2. RequestHeader：rom=Test User
3. RequestBody：*[“VM1”, “VM2”]*

那么，应为 WebhookData 参数传递以下 UI 中的 JSON 值：  

* {"WebhookName":"MyWebhook", "RequestHeader":{"From":"Test User"}, "RequestBody":"[\"VM1\",\"VM2\"]"}

![UI 中的启动 WebhookData 参数](media/automation-webhooks/Start-WebhookData-parameter-from-UI.png)

> [!NOTE]
> 所有输入参数的值都会通过 Runbook 作业进行记录。  这意味着，客户端在 Webhook 请求中提供的任何输入都将记录下来，并可供有权访问自动化作业的任何人使用。  因此，在 Webhook 调用中包括敏感信息时，应该特别小心。
> 
> 

## <a name="security"></a>“安全”
Webhook 的安全性取决于其 URL 的私密性，可以通过 URL 中包含的安全令牌来调用它。 如果请求是向正确的 URL 发出的，Azure 自动化不对请求进行任何身份验证。 因此，不应将 Webhook 用于执行敏感性很高的功能却不使用替代方法来验证请求的 Runbook。

可以将逻辑包括在 Runbook 中，以便确定它是否是通过 Webhook 调用的，只需检查 $WebhookData 参数的 **WebhookName** 属性即可。 该 Runbook 还可以执行进一步的验证，只需在 **RequestHeader** 或 **RequestBody** 属性中查找特定信息即可。

另一种策略是让 Runbook 在收到 Webhook 请求时对外部条件执行某些验证。  例如，当有新的内容提交到 GitHub 存储库时，可通过 GitHub 调用 Runbook。  Runbook 在继续之前，可能会连接到 GitHub 来验证是否确实有新的提交内容。

## <a name="creating-a-webhook"></a>创建 Webhook
在 Azure 门户中使用以下过程来创建新的链接到 Runbook 的 Webhook。

1. 在 Azure 门户的“Runbook”边栏选项卡中，单击需要通过 Webhook 来启动以查看其详细信息边栏选项卡的 Runbook。
2. 单击边栏选项卡顶部的 **Webhook** 以打开“添加 Webhook”边栏选项卡。 <br>
   ![Webhooks 按钮](media/automation-webhooks/webhooks-button.png)
3. 单击“新建 Webhook”以打开“创建 Webhook”边栏选项卡。
4. 指定 Webhook 的**名称**、**到期日期**，以及是否应启用它。 有关这些属性的详细信息，请参阅 [Webhook 详细信息](#details-of-a-webhook)。
5. 单击复制图标，然后按 Ctrl+C 以复制 Webhook 的 URL。  然后，将其记录在某个安全的位置。  **一旦创建 Webhook，你就不能再次检索该 URL。** <br>
   ![Webhook URL](media/automation-webhooks/copy-webhook-url.png)
6. 单击“参数”为 Runbook 参数提供值。  如果 Runbook 包含必需的参数，除非提供了相应的值，否则你无法创建 Webhook。
7. 单击“创建”以创建 Webhook。

## <a name="using-a-webhook"></a>使用 Webhook
若要在创建 Webhook 后使用该 Webhook，客户端应用程序必须发出带 Webhook URL 的 HTTP POST。  Webhook 的语法将按以下格式。

    http://<Webhook Server>/token?=<Token Value>

客户端将从 POST 请求中接收以下返回代码之一。  

| 代码 | 文本 | 说明 |
|:--- |:--- |:--- |
| 202 |已接受 |已接受该请求，并已成功将 Runbook 排队。 |
| 400 |错误的请求 |出于以下原因之一，未接受该请求。 <ul> <li>Webhook 已过期。</li> <li>Webhook 已禁用。</li> <li>URL 中的令牌无效。</li>  </ul> |
| 404 |未找到 |出于以下原因之一，未接受该请求。 <ul> <li>找不到 Webhook。</li> <li>找不到 Runbook。</li> <li>找不到帐户。</li>  </ul> |
| 500 |内部服务器错误 |URL 有效，但出现了错误。  请重新提交请求。 |

假设请求成功，Webhook 响应包含 JSON 格式的作业 ID，如下所示。 它包含单个作业 ID，但 JSON 格式允许将来可能的增强功能。

    {"JobIds":["<JobId>"]}  

客户端无法从 Webhook 确定 Runbook 的作业何时完成或其完成状态。  可以使用作业 ID 并配合其他方法（例如 [Windows PowerShell](http://msdn.microsoft.com/library/azure/dn690263.aspx) 或 [Azure 自动化 API](https://msdn.microsoft.com/library/azure/mt163826.aspx)）来确定此信息。

### <a name="example"></a>示例
以下示例使用 Windows PowerShell 并配合 Webhook 来启动 Runbook。  请注意，任何可以发出 HTTP 请求的语言都可以使用 Webhook；Windows PowerShell 在这里仅用作示例。

Runbook 预期请求的正文中包含 JSON 格式的虚拟机列表。 我们还要在请求标头中包含有关启动 Runbook 的人员、启动日期和时间的信息。      

    $uri = "https://s1events.azure-automation.net/webhooks?token=8ud0dSrSo%2fvHWpYbklW%3c8s0GrOKJZ9Nr7zqcS%2bIQr4c%3d"
    $headers = @{"From"="user@contoso.com";"Date"="05/28/2015 15:47:00"}

    $vms  = @(
                @{ Name="vm01";ServiceName="vm01"},
                @{ Name="vm02";ServiceName="vm02"}
            )
    $body = ConvertTo-Json -InputObject $vms

    $response = Invoke-RestMethod -Method Post -Uri $uri -Headers $headers -Body $body
    $jobid = ConvertFrom-Json $response


下图显示此请求中的标头信息（使用 [Fiddler](http://www.telerik.com/fiddler) 跟踪）。 这包括 HTTP 请求的标准标头，除此之外，还有我们添加的自定义“日期”和“时间”标头。  其中每个值都可以在 **WebhookData** 的 **RequestHeaders** 属性中提供给 Runbook 使用。

![Webhook 按钮](media/automation-webhooks/webhook-request-headers.png)

下图显示请求的正文（使用 [Fiddler](http://www.telerik.com/fiddler) 跟踪）可在 **WebhookData** 的 **RequestBody** 属性中提供给 Runbook 使用。 格式化为 JSON 是因为其格式是请求正文中包含的格式。     

![Webhook 按钮](media/automation-webhooks/webhook-request-body.png)

下图显示了从 Windows PowerShell 发送的请求以及生成的响应。  作业 ID 从响应中提取，并转换为字符串。

![Webhook 按钮](media/automation-webhooks/webhook-request-response.png)

以下示例 Runbook 将接受前面的示例请求，并启动请求正文中指定的虚拟机。

    workflow Test-StartVirtualMachinesFromWebhook
    {
        param (
            [object]$WebhookData
        )

        # If runbook was called from Webhook, WebhookData will not be null.
        if ($WebhookData -ne $null) {

            # Collect properties of WebhookData
            $WebhookName     =     $WebhookData.WebhookName
            $WebhookHeaders =     $WebhookData.RequestHeader
            $WebhookBody     =     $WebhookData.RequestBody

            # Collect individual headers. VMList converted from JSON.
            $From = $WebhookHeaders.From
            $VMList = ConvertFrom-Json -InputObject $WebhookBody
            Write-Output "Runbook started from webhook $WebhookName by $From."

            # Authenticate to Azure resources
            $Cred = Get-AutomationPSCredential -Name 'MyAzureCredential'
            Add-AzureAccount -Credential $Cred

            # Start each virtual machine
            foreach ($VM in $VMList)
            {
                $VMName = $VM.Name
                Write-Output "Starting $VMName"
                Start-AzureVM -Name $VM.Name -ServiceName $VM.ServiceName
            }
        }
        else {
            Write-Error "Runbook mean to be started only from webhook."
        }
    }


## <a name="starting-runbooks-in-response-to-azure-alerts"></a>启动 Runbook 以响应 Azure 警报
启用 Webhook 的 Runbook 可用以响应 [Azure 警报](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)。 Azure 中的资源可以借助 Azure 警报通过收集性能、可用性和使用情况等统计信息进行监视。 可以收到根据监视 Azure 资源的计量或事件的警报，目前自动化帐户仅支持度量值。 当指定的度量值超过分配的阈值时，或者如果触发了配置的事件，就会向服务管理员或共同管理员发送通知以解决该警报。有关度量值和事件的详细信息，请参阅 [Azure 警报](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)。

除了使用 Azure 警报作为通知系统以外，还可以使用 Runbook 响应警报。 Azure 自动化可让你使用 Azure 警报运行启用 Webhook 的 Runbook。 当某个度量值超过配置的阈值时，警报规则将会激活，并触发自动化 Webhook，后者将执行 Runbook。

![Webhook](media/automation-webhooks/webhook-alert.jpg)

### <a name="alert-context"></a>警报上下文
考虑虚拟机等 Azure 资源，这台计算机的 CPU 使用率是其中一个关键性能度量值。 如果 CPU 使用率是 100% 或者在一段长时间超过某个量，你可能想要重新启动虚拟机以解决问题。 这可以通过配置虚拟机的警报规则来解决，而此规则可以使用 CPU 百分比作为度量值。 此处的 CPU 百分比只是一个示例，但是还有许多可以对 Azure 资源配置的其他度量值，重新启动虚拟机是解决此问题采取的一种措施，你可以将 Runbook 配置为采取其他措施。

当此警报规则活动并触发启用 Webhook 的 Runbook 时，将向 Runbook 发送警报上下文。 [警报上下文](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)中的详细信息包括 **SubscriptionID**、**ResourceGroupName**、**ResourceName**、**ResourceType**、**ResourceId** 和 **Timestamp**，这些都是 Runbook 识别在其上执行操作的资源所需的项。 警报上下文嵌入在发送到 Runbook 的 **WebhookData** 对象的正文部分，可以使用 **Webhook.RequestBody** 属性访问

### <a name="example"></a>示例
在订阅中创建 Azure 虚拟机，并关联[用于监视 CPU 百分比度量值的警报](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)。 创建警报时，请确保使用创建 Webhook 时生成的 Webhook URL 填充 Webhook 字段。

以下示例 Runbook 在警报规则激活时触发，并收集 Runbook 识别在上面采取措施的资源所需的警报上下文参数。

    workflow Invoke-RunbookUsingAlerts
    {
        param (      
            [object]$WebhookData
        )

        # If runbook was called from Webhook, WebhookData will not be null.
        if ($WebhookData -ne $null) {   
            # Collect properties of WebhookData.
            $WebhookName    =   $WebhookData.WebhookName
            $WebhookBody    =   $WebhookData.RequestBody
            $WebhookHeaders =   $WebhookData.RequestHeader

            # Outputs information on the webhook name that called This
            Write-Output "This runbook was started from webhook $WebhookName."


            # Obtain the WebhookBody containing the AlertContext
            $WebhookBody = (ConvertFrom-Json -InputObject $WebhookBody)
            Write-Output "`nWEBHOOK BODY"
            Write-Output "============="
            Write-Output $WebhookBody

            # Obtain the AlertContext     
            $AlertContext = [object]$WebhookBody.context

            # Some selected AlertContext information
            Write-Output "`nALERT CONTEXT DATA"
            Write-Output "==================="
            Write-Output $AlertContext.name
            Write-Output $AlertContext.subscriptionId
            Write-Output $AlertContext.resourceGroupName
            Write-Output $AlertContext.resourceName
            Write-Output $AlertContext.resourceType
            Write-Output $AlertContext.resourceId
            Write-Output $AlertContext.timestamp

            # Act on the AlertContext data, in our case restarting the VM.
            # Authenticate to your Azure subscription using Organization ID to be able to restart that Virtual Machine.
            $cred = Get-AutomationPSCredential -Name "MyAzureCredential"
            Add-AzureAccount -Credential $cred
            Select-AzureSubscription -subscriptionName "Visual Studio Ultimate with MSDN"

            #Check the status property of the VM
            Write-Output "Status of VM before taking action"
            Get-AzureVM -Name $AlertContext.resourceName -ServiceName $AlertContext.resourceName
            Write-Output "Restarting VM"

            # Restart the VM by passing VM name and Service name which are same in this case
            Restart-AzureVM -ServiceName $AlertContext.resourceName -Name $AlertContext.resourceName
            Write-Output "Status of VM after alert is active and takes action"
            Get-AzureVM -Name $AlertContext.resourceName -ServiceName $AlertContext.resourceName
        }
        else  
        {
            Write-Error "This runbook is meant to only be started from a webhook."  
        }  
    }



## <a name="next-steps"></a>后续步骤
* 有关以不同方式启动 Runbook 的详细信息，请参阅[启动 Runbook](automation-starting-a-runbook.md)
* 有关查看 Runbook 作业状态的信息，请参阅[在 Azure 自动化中执行 Runbook](automation-runbook-execution.md)
* 若要了解如何使用 Azure 自动化对 Azure 警报执行操作，请参阅[使用自动化 Runbook 解决 Azure VM 警报](automation-azure-vm-alert-integration.md)




<!--HONumber=Nov16_HO3-->


