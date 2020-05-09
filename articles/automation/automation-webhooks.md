---
title: 使用 webhook 启动 Azure 自动化 runbook
description: 一个可供客户端通过 HTTP 调用在 Azure 自动化中启动 Runbook 的 Webhook。  本文介绍了如何创建 Webhook，以及如何通过调用 Webhook 来启动 Runbook。
services: automation
ms.subservice: process-automation
ms.date: 01/16/2020
ms.topic: conceptual
ms.openlocfilehash: cbe43b298c57d266f0b031b5192f25fe3df07c05
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82582434"
---
# <a name="starting-an-azure-automation-runbook-with-a-webhook"></a>使用 webhook 启动 Azure 自动化 runbook

外部服务可以使用 Webhook 在 Azure 自动化中通过单个 HTTP 请求启动特定的 Runbook。 外部服务包括 Azure DevOps Services、GitHub、Azure Monitor 日志和自定义应用程序。 此类服务可以使用 Webhook 来启动 Runbook，而无需使用 Azure 自动化 API 实施整个解决方案。 可将 Webhook 与[在 Azure 自动化中启动 Runbook](automation-starting-a-runbook.md) 中所述的其他 Runbook 启动方法进行比较。

> [!NOTE]
> 不支持使用 Webhook 启动 Python runbook。

![WebhooksOverview](media/automation-webhooks/webhook-overview-image.png)

>[!NOTE]
>本文进行了更新，以便使用新的 Azure PowerShell Az 模块。 你仍然可以使用 AzureRM 模块，至少在 2020 年 12 月之前，它将继续接收 bug 修补程序。 若要详细了解新的 Az 模块和 AzureRM 兼容性，请参阅[新 Azure Powershell Az 模块简介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 有关混合 Runbook 辅助角色上的 Az 模块安装说明，请参阅[安装 Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 对于自动化帐户，可参阅[如何更新 Azure 自动化中的 Azure PowerShell 模块](automation-update-azure-modules.md)，将模块更新到最新版本。

## <a name="webhook-properties"></a>Webhook 属性

下表介绍了必须为 webhook 配置的属性。

| 属性 | 说明 |
|:--- |:--- |
| 名称 |Webhook 的名称。 您可以提供所需的任何名称，因为它不会公开给客户端。 它只用来标识 Azure 自动化中的 Runbook。 最好是为 Webhook 提供一个名称，该名称需要与使用它的客户端相关。 |
| URL |Webhook 的 URL。 这是客户端使用 HTTP POST 调用的唯一地址，用于启动链接到 webhook 的 runbook。 它是在创建 Webhook 时自动生成的。 无法指定自定义 URL。 <br> <br> URL 包含一个安全令牌，该令牌允许第三方系统调用 runbook，而无需进行进一步的身份验证。 出于此原因，应将 URL 视为密码。 出于安全考虑，只能在创建 webhook 时查看 Azure 门户中的 URL。 请将保存在安全位置的 URL 记下来，供将来使用。 |
| 到期日期 | Webhook 的到期日期，超过此时间后，将无法再使用它。 如果 webhook 尚未过期，则可以修改创建 webhook 后的到期日期。 |
| 已启用 | 指示是否在创建时默认启用 webhook 的设置。 如果将此属性设置为 "禁用"，则客户端不能使用 webhook。 您可以在创建 webhook 时设置此属性，也可以在创建后的任何其他时间设置此属性。 |

## <a name="parameters-used-when-the-webhook-starts-a-runbook"></a>Webhook 启动 runbook 时使用的参数

Webhook 可以定义 runbook 参数的值，这些值在 runbook 启动时使用。 Webhook 必须包含任何必需的 runbook 参数的值，并且可以包含可选参数的值。 即使在创建 webhook 后，也可以修改配置为 webhook 的参数值。 链接到单个 runbook 的多个 webhook 可以使用不同的 runbook 参数值。 客户端在使用 Webhook 启动 Runbook 时，无法重写在 Webhook 中定义的参数值。

若要从客户端接收数据，runbook 支持一个名`WebhookData`为的参数。 此参数定义包含客户端在 POST 请求中包含的数据的对象。

![WebhookData 属性](media/automation-webhooks/webhook-data-properties.png)

`WebhookData` 参数具有以下属性：

| 属性 | 说明 |
|:--- |:--- |
| `WebhookName` | Webhook 的名称。 |
| `RequestHeader` | 包含传入 POST 请求标头的哈希表。 |
| `RequestBody` | 传入 POST 请求的正文。 此正文保留任何数据格式，如字符串、JSON、XML 或窗体编码。 编写的 Runbook 必须能够与预期的数据格式配合工作。 |

不需要配置支持`WebhookData`参数所需的 webhook，也不需要 runbook 来接受它。 如果 runbook 没有定义该参数，则会忽略从客户端发送的请求的任何详细信息。

> [!NOTE]
> 调用 webhook 时，如果调用失败，客户端应始终存储所有参数值。 如果发生网络中断或连接问题，应用程序将无法检索 webhook 调用失败。

如果在 webhook 创建时指定`WebhookData`的值，则当 webhook 使用客户端 POST 请求中的数据启动 runbook 时，将重写它。 即使应用程序未在请求正文中包含任何数据，也会发生这种情况。 

如果启动使用 webhook 之外的机制`WebhookData`定义的 runbook，则可以为`WebhookData`该 runbook 可识别的值提供值。 此值应该是一个对象，该对象[properties](#webhook-properties)具有与`WebhookData`参数相同的属性，因此，runbook 可以使用它，就像它处理`WebhookData` webhook 传递的实际对象一样。

例如，如果从 Azure 门户启动以下 runbook，并想要传递一些用于测试的示例 webhook 数据，则必须在用户界面中以 JSON 格式传递数据。

![UI 中的 WebhookData 参数](media/automation-webhooks/WebhookData-parameter-from-UI.png)

对于下一个 runbook 示例，让我们为定义以下属性`WebhookData`：

* **WebhookName**： MyWebhook
* **RequestBody**：`*[{'ResourceGroup': 'myResourceGroup','Name': 'vm01'},{'ResourceGroup': 'myResourceGroup','Name': 'vm02'}]*`

现在，我们在 UI 中为`WebhookData`参数传递以下 JSON 对象。 在此示例中，使用回车符和换行符，可匹配从 webhook 传入的格式。

```json
{"WebhookName":"mywebhook","RequestBody":"[\r\n {\r\n \"ResourceGroup\": \"vm01\",\r\n \"Name\": \"vm01\"\r\n },\r\n {\r\n \"ResourceGroup\": \"vm02\",\r\n \"Name\": \"vm02\"\r\n }\r\n]"}
```

![UI 中的启动 WebhookData 参数](media/automation-webhooks/Start-WebhookData-parameter-from-UI.png)

> [!NOTE]
> Azure 自动化将所有输入参数的值记录在 runbook 作业中。 因此，对该客户端在 webhook 请求中提供的任何输入都将记录下来，并可供有权访问自动化作业的任何人使用。 因此，在 Webhook 调用中包括敏感信息时，应该特别小心。

## <a name="webhook-security"></a>Webhook 安全性

Webhook 的安全性取决于其 URL 的隐私，其中包含的安全令牌允许调用 webhook。 只要对正确的 URL 发出请求，Azure Automation 就不会对请求执行任何身份验证。 出于此原因，客户端不应将 webhook 用于执行高敏感度操作的 runbook，而不使用用于验证请求的替代方法。

可以将逻辑包括在 runbook 中，以确定它是否被 webhook 调用。 让 runbook 检查`WebhookData`参数的`WebhookName`属性。 通过在`RequestHeader`和`RequestBody`属性中查找特定信息，runbook 可以执行进一步的验证。

另一种策略是让 runbook 在收到 webhook 请求时对外部条件执行某些验证。 例如，假设有一个在 GitHub 存储库有新的提交时由 GitHub 调用的 runbook。 Runbook 在继续之前，可能会连接到 GitHub 来验证是否已发生新的提交。

## <a name="create-a-webhook"></a>创建 Webhook

在 Azure 门户中使用以下过程来创建新的链接到 Runbook 的 Webhook。

1. 在 Azure 门户的 "Runbook" 页中，单击 webhook 开始查看 runbook 详细信息的 runbook。 确保 "runbook**状态**" 字段设置为 "**已发布**"。
2. 单击页顶部的“Webhook”以打开“添加 Webhook”页****。
3. 单击 "**新建 webhook** " 以打开 "创建 webhook" 页。
4. 填写 webhook 的 "**名称**" 和 "**到期日期**" 字段，并指定是否应启用此字段。 有关这些属性的详细信息，请参阅[Webhook 属性](#webhook-properties)。
5. 单击复制图标，并按 Ctrl+C 以复制 Webhook 的 URL。 然后，将其记录在某个安全的位置。 

    > [!NOTE]
    > 一旦创建 Webhook，你就不能再次检索该 URL。

   ![Webhook URL](media/automation-webhooks/copy-webhook-url.png)

1. 单击“参数”**** 为 Runbook 参数提供值。 如果 runbook 包含必需的参数，除非提供值，否则无法创建 webhook。
1. 单击“创建”**** 以创建 Webhook。

## <a name="use-a-webhook"></a>使用 webhook

若要在创建 webhook 后使用该 webhook，客户端必须使用 webhook 的`POST` URL 发出 HTTP 请求。 语法为：

```http
http://<Webhook Server>/token?=<Token Value>
```

客户端从`POST`请求中接收以下返回代码之一。

| 代码 | 文本 | 说明 |
|:--- |:--- |:--- |
| 202 |已接受 |已接受该请求，并已成功将 Runbook 排队。 |
| 400 |错误的请求 |出于以下原因之一，未接受该请求： <ul> <li>Webhook 已过期。</li> <li>Webhook 已禁用。</li> <li>URL 中的令牌无效。</li>  </ul> |
| 404 |未找到 |出于以下原因之一，未接受该请求： <ul> <li>找不到 Webhook。</li> <li>找不到 Runbook。</li> <li>找不到帐户。</li>  </ul> |
| 500 |内部服务器错误 |URL 有效，但出现了错误。 请重新提交请求。 |

假设请求成功，webhook 响应包含 JSON 格式的作业 ID，如下所示。 它包含单个作业 ID，但 JSON 格式允许将来可能的增强功能。

```json
{"JobIds":["<JobId>"]}
```

客户端无法从 Webhook 确定 Runbook 的作业何时完成或其完成状态。 它可以通过其他机制（例如[Windows PowerShell](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureautomationjob)或[Azure 自动化 API](/rest/api/automation/job)）使用作业 ID 来了解此信息。

## <a name="renew-a-webhook"></a>续订 Webhook

创建 webhook 后，其有效期为十年，超过该时间段后，它将自动过期。 Webhook 过期后，将无法重新激活它。 只能删除然后重新创建它。 

可以扩展尚未过期的 webhook。 扩展 webhook：

1. 导航到包含 webhook 的 runbook。 
2. 选择“资源”****下的“Webhook”****。 
3. 单击要扩展的 webhook。 
4. 在 Webhook 页面中，选择新的过期日期和时间，然后单击 "**保存**"。

## <a name="sample-runbook"></a>示例 Runbook

以下示例 Runbook 将接受 Webhook 数据，并启动请求正文中指定的虚拟机。 若要测试此 runbook，请在**runbook**下的自动化帐户中，单击 "**创建 runbook**"。 如果不知道如何创建 Runbook，请参阅[创建 Runbook](automation-quickstart-create-runbook.md)。

> [!NOTE]
> 对于非图形 PowerShell runbook， `Add-AzAccount`和`Add-AzureRMAccount`是[AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0)的别名。 可以使用这些 cmdlet，也可以在自动化帐户中[将模块更新](automation-update-azure-modules.md)为最新版本。 即使刚刚创建了一个新的自动化帐户，也可能需要更新你的模块。

```powershell
param
(
    [Parameter (Mandatory = $false)]
    [object] $WebhookData
)

# If runbook was called from Webhook, WebhookData will not be null.
if ($WebhookData) {

    # Check header for message to validate request
    if ($WebhookData.RequestHeader.message -eq 'StartedbyContoso')
    {
        Write-Output "Header has required information"}
    else
    {
        Write-Output "Header missing required information";
        exit;
    }

    # Retrieve VMs from Webhook request body
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
            Add-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Write-Output

        # Start each virtual machine
        foreach ($vm in $vms)
        {
            $vmName = $vm.Name
            Write-Output "Starting $vmName"
            Start-AzVM -Name $vm.Name -ResourceGroup $vm.ResourceGroup
        }
}
else {
    # Error
    write-Error "This runbook is meant to be started from an Azure alert webhook only."
}
```

## <a name="test-the-sample"></a>测试示例

以下示例使用 Windows PowerShell 并配合 Webhook 来启动 Runbook。 任何可以发出 HTTP 请求的语言都可以使用 webhook。 此处使用 Windows PowerShell 作为示例。

Runbook 预期请求的正文中包含 JSON 格式的虚拟机列表。 Runbook 还将验证标头是否包含已定义的消息，以验证 webhook 调用方是否有效。

```azurepowershell-interactive
$uri = "<webHook Uri>"

$vms  = @(
            @{ Name="vm01";ResourceGroup="vm01"},
            @{ Name="vm02";ResourceGroup="vm02"}
        )
$body = ConvertTo-Json -InputObject $vms
$header = @{ message="StartedbyContoso"}
$response = Invoke-WebRequest -Method Post -Uri $uri -Body $body -Headers $header
$jobid = (ConvertFrom-Json ($response.Content)).jobids[0]
```

下面的示例演示了在的`RequestBody`属性中可用于 runbook 的请求的正文。 `WebhookData` 此值在 JSON 格式设置为与请求正文中包含的格式兼容。

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

* 若要了解如何使用 Azure 自动化对 Azure 警报执行操作，请参阅[使用警报触发 Azure 自动化 runbook](automation-create-alert-triggered-runbook.md)。
