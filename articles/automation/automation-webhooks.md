---
title: 通过 Webhook 启动 Azure 自动化 Runbook
description: 一个可供客户端通过 HTTP 调用在 Azure 自动化中启动 Runbook 的 Webhook。  本文介绍了如何创建 Webhook，以及如何通过调用 Webhook 来启动 Runbook。
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 06/04/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: e047dffa86915b0cd6e8829ea27e0335e7f88cb2
ms.sourcegitcommit: 4f9fa86166b50e86cf089f31d85e16155b60559f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2018
ms.locfileid: "34757150"
---
# <a name="starting-an-azure-automation-runbook-with-a-webhook"></a>通过 Webhook 启动 Azure 自动化 Runbook

Webhook 可以用来在 Azure 自动化中通过单个 HTTP 请求来启动特定的 Runbook。 这样，外部服务（例如 Visual Studio Team Services、GitHub、Azure Log Analytics 或自定义应用程序）就可以在不通过 Azure 自动化 API 实现完整解决方案的情况下启动 Runbook。  
![WebhooksOverview](media/automation-webhooks/webhook-overview-image.png)

可以将 Webhook 与[在 Azure 自动化中启动 Runbook](automation-starting-a-runbook.md) 中其他启动 Runbook 的方法进行比较

## <a name="details-of-a-webhook"></a>Webhook 详细信息

下表介绍了必须为 webhook 配置的属性。

| 属性 | 说明 |
|:--- |:--- |
| 名称 |可以提供用于 Webhook 的任何名称，因为该名称不会公开给客户端。 它只用来标识 Azure 自动化中的 Runbook。 <br> 最好是为 Webhook 提供一个名称，该名称需要与使用它的客户端相关。 |
| 代码 |Webhook 的 URL 是客户端通过 HTTP POST 来调用的唯一地址，用于启动链接到 Webhook 的 Runbook。 它是在创建 Webhook 时自动生成的。 不能指定自定义 URL。 <br> <br> URL 包含一个允许第三方系统调用 Runbook 的安全令牌，不需要进一步进行身份验证。 因此，应将其视为密码。 出于安全原因，只能在创建 Webhook 时通过 Azure 门户查看该 URL。 请将保存在安全位置的 URL 记下来，供将来使用。 |
| 到期日期 |与证书一样，每个 Webhook 都有一个过期日期，到了过期日期 Webhook 不再可用。 创建 Webhook 后，可以修改此到期日期。 |
| 已启用 |Webhook 在创建后已按默认启用。 如果将它设置为“禁用”，则没有客户端能够使用它。 可以在创建 Webhook 时设置 **Enabled** 属性，也可以在创建后随时设置它。 |

### <a name="parameters"></a>parameters

Webhook 可以定义 Runbook 参数的值，当该 Webhook 启动 Runbook 时会用到这些值。 Webhook 必须包含 Runbook 的任何必需参数的值，可以包含可选参数的值。 即使在创建 Webhook 后，也可以修改配置给 Webhook 的参数值。 链接到单个 Runbook 的多个 Webhook 可以使用不同的参数值。

客户端在使用 Webhook 启动 Runbook 时，不能重写在 Webhook 中定义的参数值。 为了从客户端接收数据，Runbook 可能会接受名为“$WebhookData”且类型为 [object] 的单个参数，该参数包含客户端包括在 POST 请求中的数据。

![Webhookdata 属性](media/automation-webhooks/webhook-data-properties.png)

$WebhookData 对象具有以下属性：

| 属性 | 说明 |
|:--- |:--- |
| WebhookName |Webhook 的名称。 |
| RequestHeader |包含传入 POST 请求标头的哈希表。 |
| RequestBody |传入 POST 请求的正文。 用于保留任何格式，如字符串、JSON、XML，或窗体编码的数据。 编写的 Runbook 必须能够与预期的数据格式配合工作。 |

不需配置 Webhook 即可支持 **$WebhookData** 参数，也不需要 Runbook 来接受它。 如果 Runbook 没有定义该参数，则会忽略从客户端发送的请求的任何详细信息。

如果在创建 Webhook 时为 $WebhookData 指定了值，则会在 Webhook 使用客户端 POST 请求中的数据启动 Runbook 时重写该值，即使该客户端的请求正文中不包含任何数据。 如果使用 Webhook 以外的方法启动包含 $WebhookData 的 Runbook，则可为能够被 Runbook 识别的 $Webhookdata 提供值。 此值应该是与 $Webhookdata 具有相同[属性](#details-of-a-webhook)的对象，这样 Runbook 就可以正常地使用它，如同使用 Webhook 所传递的实际 WebhookData 一样。

例如，如果要从 Azure 门户启动以下 Runbook，并想要传递一些 WebhookData 示例进行测试。由于 WebhookData 是一个对象，因此应以 UI 中的 JSON 进行传递。

![UI 中的 WebhookData 参数](media/automation-webhooks/WebhookData-parameter-from-UI.png)

对于以下 Runbook，如果 WebhookData 参数具有以下属性：

* WebhookName：MyWebhook
* RequestBody: [{'ResourceGroup': 'myResourceGroup','Name': 'vm01'},{'ResourceGroup': 'myResourceGroup','Name': 'vm02'}]

那么，应为 WebhookData 参数传递以下 UI 中的 JSON 值。 下面的示例带有回车符和换行符，与从 Webhook 中传递的格式相匹配。

```json
{"WebhookName":"mywebhook","RequestBody":"[\r\n {\r\n \"ResourceGroup\": \"vm01\",\r\n \"Name\": \"vm01\"\r\n },\r\n {\r\n \"ResourceGroup\": \"vm02\",\r\n \"Name\": \"vm02\"\r\n }\r\n]"}
```

![UI 中的启动 WebhookData 参数](media/automation-webhooks/Start-WebhookData-parameter-from-UI.png)

> [!NOTE]
> 所有输入参数的值都会通过 Runbook 作业进行记录。 这意味着，客户端在 Webhook 请求中提供的任何输入都将记录下来，并可供有权访问自动化作业的任何人使用。  因此，在 Webhook 调用中包括敏感信息时，应该特别小心。

## <a name="security"></a>“安全”

Webhook 的安全性取决于其 URL 的私密性，可以通过 URL 中包含的安全令牌来调用它。 如果请求是向正确的 URL 发出的，Azure 自动化不对请求进行任何身份验证。 因此，不应将 Webhook 用于执行敏感性很高的功能却不使用替代方法来验证请求的 Runbook。

可以将逻辑包括在 Runbook 中，以便确定它是否是通过 Webhook 调用的，只需检查 $WebhookData 参数的 **WebhookName** 属性即可。 该 Runbook 还可以执行进一步的验证，只需在 **RequestHeader** 或 **RequestBody** 属性中查找特定信息即可。

另一种策略是让 Runbook 在收到 Webhook 请求时对外部条件执行某些验证。 例如，当有新的内容提交到 GitHub 存储库时，可通过 GitHub 调用 Runbook。 Runbook 在继续之前，可能会连接到 GitHub 来验证是否有新的提交内容。

## <a name="creating-a-webhook"></a>创建 Webhook

在 Azure 门户中使用以下过程来创建新的链接到 Runbook 的 Webhook。

1. 在 Azure 门户的“Runbook”页中，单击需要通过 Webhook 来启动以查看其详细信息页的 Runbook。
1. 单击页顶部的“Webhook”以打开“添加 Webhook”页。
1. 单击“新建 Webhook”以打开“创建 Webhook”页。
1. 指定 Webhook 的**名称**、**到期日期**，以及是否应启用它。 有关这些属性的详细信息，请参阅 [Webhook 详细信息](#details-of-a-webhook)。
1. 单击复制图标，并按 Ctrl+C 以复制 Webhook 的 URL。 然后，将其记录在某个安全的位置。 **一旦创建 Webhook，就不能再次检索该 URL。**

   ![Webhook URL](media/automation-webhooks/copy-webhook-url.png)

1. 单击“参数”为 Runbook 参数提供值。 如果 Runbook 包含必需的参数，除非提供了相应的值，否则无法创建 Webhook。
1. 单击“创建”以创建 Webhook。

## <a name="using-a-webhook"></a>使用 Webhook

若要在创建 Webhook 后使用该 Webhook，客户端应用程序必须发出带 Webhook URL 的 HTTP POST。 Webhook 的语法采用以下格式：

```http
http://<Webhook Server>/token?=<Token Value>
```

客户端从 POST 请求中接收以下返回代码之一。

| 代码 | 文本 | 说明 |
|:--- |:--- |:--- |
| 202 |已接受 |已接受该请求，并已成功将 Runbook 排队。 |
| 400 |错误的请求 |出于以下原因之一，未接受该请求： <ul> <li>Webhook 已过期。</li> <li>Webhook 已禁用。</li> <li>URL 中的令牌无效。</li>  </ul> |
| 404 |未找到 |出于以下原因之一，未接受该请求： <ul> <li>找不到 Webhook。</li> <li>找不到 Runbook。</li> <li>找不到帐户。</li>  </ul> |
| 500 |内部服务器错误 |URL 有效，但出现了错误。 请重新提交请求。 |

假设请求成功，Webhook 响应包含 JSON 格式的作业 ID，如下所示。 它包含单个作业 ID，但 JSON 格式允许将来可能的增强功能。

```json
{"JobIds":["<JobId>"]}
```

客户端无法从 Webhook 确定 Runbook 的作业何时完成或其完成状态。 可以使用作业 ID 并配合其他方法（例如 [Windows PowerShell](http://msdn.microsoft.com/library/azure/dn690263.aspx) 或 [Azure 自动化 API](/rest/api/automation/job)）来确定此信息。

## <a name="sample-runbook"></a>示例 Runbook

以下示例 Runbook 将接受 Webhook 数据，并启动请求正文中指定的虚拟机。 若要在“Runbook”下的自动化帐户中测试此 Runbook，请单击“+ 添加 Runbook”。 如果不知道如何创建 Runbook，请参阅[创建 Runbook](automation-quickstart-create-runbook.md)。

```powershell
param
(
    [Parameter (Mandatory = $false)]
    [object] $WebhookData
)

# If runbook was called from Webhook, WebhookData will not be null.
if ($WebhookData) {

    # Retrieve VM's from Webhook request body
    $vms = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)

    # Authenticate to Azure by using the service principal and certificate. Then, set the subscription.

    Write-Output "Authenticating to Azure with service principal and certificate"
    $ConnectionAssetName = "AzureRunAsConnection"
    Write-Output "Get connection asset: $ConnectionAssetName" 

    $Conn = Get-AutomationConnection -Name $ConnectionAssetName
            if ($Conn -eq $null)
            {
                throw "Could not retrieve connection asset: $ConnectionAssetName. Check that this asset exists in the Automation account."
            }
            Write-Output "Authenticating to Azure with service principal." 
            Add-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Write-Output

        # Start each virtual machine
        foreach ($vm in $vms)
        {
            $vmName = $vm.Name
            Write-Output "Starting $vmName"
            Start-AzureRMVM -Name $vm.Name -ResourceGroup $vm.ResourceGroup
        }
}
else {
    # Error
    write-Error "This runbook is meant to be started from an Azure alert webhook only."
}
```

## <a name="test-the-example"></a>测试示例

以下示例使用 Windows PowerShell 并配合 Webhook 来启动 Runbook。 任何可以发出 HTTP 请求的语言都可以使用 Webhook；Windows PowerShell 在这里用作示例。

Runbook 预期请求的正文中包含 JSON 格式的虚拟机列表。

```azurepowershell-interactive
$uri = "<webHook Uri>"

$vms  = @(
            @{ Name="vm01";ResourceGroup="vm01"},
            @{ Name="vm02";ResourceGroup="vm02"}
        )
$body = ConvertTo-Json -InputObject $vms

$response = Invoke-RestMethod -Method Post -Uri $uri -Body $body
$jobid = (ConvertFrom-Json ($response.Content)).jobids[0]
```

下面的示例显示请求的正文，可在“WebhookData”的“RequestBody”属性中提供给 Runbook 使用。 格式化为 JSON 是因为其格式是请求正文中包含的格式。

```json
[
    {
        "Name":  "vm01",
        "ResourceGroup":  "myResourceGroup"
    },
    {
        "Name":  "vm02",
        "ResourceGroup":  "myResourceGroup"
    }
]
```

下图显示了从 Windows PowerShell 发送的请求以及生成的响应。 作业 ID 从响应中提取，并转换为字符串。

![Webhook 按钮](media/automation-webhooks/webhook-request-response.png)

## <a name="next-steps"></a>后续步骤

* 若要了解如何使用 Azure 自动化对 Azure 警报执行操作，请参阅[使用警报触发 Azure 自动化 Runbook](automation-create-alert-triggered-runbook.md)。
